---
description: Run this script once in Background Scripts after grabbing your instance
---

# Before getting started

### Before you run

Run this script after deploying the base lab manifest. Run it as the **System Administrator**.

{% hint style="warning" %}
Run this script only in your assigned lab instance. It modifies user preferences, DEX records, incidents, knowledge articles, and catalog items.
{% endhint %}

### Run the script

1. In the All menu, open **System Definition → Scripts - Background**.
2. Paste the complete script below into the script field.
3. Select **Run script**.
4. Review the output before continuing with the lab.

<div><figure><img src=".gitbook/assets/2026-06-24 16.20.32.png" alt="Scripts - Background navigation in the ServiceNow All menu" width="188"><figcaption><p>Open **Scripts - Background** from the All menu.</p></figcaption></figure> <figure><img src=".gitbook/assets/2026-06-24 16.18.07.png" alt="Run script button in ServiceNow Background Scripts" width="158"><figcaption><p>Paste the script, then select **Run script**.</p></figcaption></figure></div>

The script is idempotent. You can rerun it if a gate fails. Existing seed records are reused when possible.&#x20;

### Verify results

Confirm that the output includes these values:

```
GATE_ZTSD_APP_SCOPE=PASS
GATE_ZTSD_DEX_FIX=PASS
GATE_ZTSD_INCIDENT=PASS
GATE_ZTSD_CONTENT=PASS
VERDICT: PASS - ZTSD lab build finalized
```

Warnings do not always cause a failed verdict. Review every `WARN:` message. Stop and contact your lab facilitator if any gate reports `FAIL` or the verdict is not `PASS`.

### Script

{% code overflow="wrap" expandable="true" %}
```javascript
// ZTSD AUS v3 - standalone post-build repair and acceptance gate.
// Split from 23_AICT_154439_Finalize_Remaining_Issues.js for GitBook portability.
// Run once after the base lab manifest. Idempotent and safe to rerun.
(function () {
  var BLOCKED_INSTANCE = 'techsummitdemo26ams152975';
  var instanceName = String(gs.getProperty('instance_name') || '');
  var instanceUri = String(gs.getProperty('glide.servlet.uri') || '');
  var passed = 0;
  var failed = 0;
  var documented = 0;
  var changed = 0;
  var warnings = [];

  function print(message) {
    gs.print(message);
  }

  function pass(gate, message) {
    passed++;
    print('PASS [' + gate + ']: ' + message);
  }

  function fail(gate, message) {
    failed++;
    print('FAIL [' + gate + ']: ' + message);
  }

  function warn(message) {
    warnings.push(String(message));
    print('WARN: ' + message);
  }

  if (instanceName === BLOCKED_INSTANCE || instanceUri.indexOf(BLOCKED_INSTANCE) >= 0) {
    print('VERDICT: BLOCKED - refusing to modify protected instance ' + BLOCKED_INSTANCE);
    return;
  }

  print('=== 23_ZTSD_Finalize_Remaining_Issues | instance=' + instanceName + ' | uri=' + instanceUri + ' ===');

  function resetAppScopePreference() {
    var DEX_SCOPE = '9cf1cacc4787a650f957f0ca216d43c4';
    var userId = gs.getUserID();
    if (!userId) {
      warn('APP_SCOPE_PREF: could not resolve current user — skipping check');
      return true;
    }

    var pref = new GlideRecord('sys_user_preference');
    pref.addQuery('user', userId);
    pref.addQuery('name', 'apps.current_app');
    pref.setLimit(1);
    pref.query();

    if (!pref.next()) {
      print('APP_SCOPE_PREF_OK=YES (no preference set)');
      return true;
    }

    var currentValue = String(pref.getValue('value') || '');

    var globalSysId = '';
    var globalScope = new GlideRecord('sys_scope');
    globalScope.addQuery('scope', 'global');
    globalScope.setLimit(1);
    globalScope.query();
    if (globalScope.next()) globalSysId = String(globalScope.getUniqueValue());

    if (!currentValue || (globalSysId && currentValue === globalSysId)) {
      print('APP_SCOPE_PREF_OK=YES (already global)');
      return true;
    }

    var prefSid = String(pref.getUniqueValue());
    var wasDex = (currentValue === DEX_SCOPE);
    if (!pref.deleteRecord()) {
      warn('APP_SCOPE_PREF_RESET=FAILED old=' + currentValue + ' — could not delete sys_user_preference ' + prefSid);
      return false;
    }

    // Verify by re-reading — don't trust deleteRecord()'s return value alone,
    // matching the same "write reported success but didn't stick" caution
    // already applied elsewhere in this file (updateIfChanged()).
    var verify = new GlideRecord('sys_user_preference');
    verify.addQuery('user', userId);
    verify.addQuery('name', 'apps.current_app');
    verify.setLimit(1);
    verify.query();
    if (verify.next()) {
      warn('APP_SCOPE_PREF_RESET: delete reported success but preference still present — sys_id=' + verify.getUniqueValue());
      return false;
    }

    print('APP_SCOPE_PREF_RESET=YES old=' + currentValue + (wasDex ? ' (was DEX)' : ' (was non-global app)'));
    changed++;
    return true;
  }

  // Resolve users by natural key so the script remains portable across instances.
  function resolveUserSysId(userName) {
    var ugr = new GlideRecord('sys_user');
    ugr.addQuery('user_name', userName);
    ugr.setLimit(1);
    ugr.query();
    return ugr.next() ? String(ugr.getUniqueValue()) : '';
  }

  // Fix the DEX security violation that causes every ZTSD execution plan to terminate.
  // Root cause: two DEX-scope sn_aia_agent_child records (sys_policy=read) inject DEX agents
  // into the ZTSD ACL schema. earlyFallbackIfFailed=true then kills the entire EP.
  // Fix: run in DEX scope and null out child_agent on both records — severs the traversal path
  // without deletion (REST DELETE and cross-scope GlideRecord both fail due to sys_policy=read).
  function fixZtsdDexSecurityViolation() {
    var DEX_SCOPE = '9cf1cacc4787a650f957f0ca216d43c4';
    // Two DEX-scope agent_child records that pull DEX agents into the ZTSD ACL check:
    //   401e3b35... : agent=PDA (53a73371...) → child=DEX diagnosis agent
    //   e4dfc62c... : agent=Action Agent (641297672b...) → child=DEX remediation trigger agent
    var BAD_LINKS = [
      '401e3b3593a13210f1e8f837b503d6db',
      'e4dfc62c47317210233ffa1fe16d43eb'
    ];

    // gs.setCurrentApplicationId() changes the CURRENT TRANSACTION's scope for
    // everything that runs afterward, not just this loop. Capture and restore it
    // in a try/finally so this DEX repair cannot poison subsequent ZTSD writes.
    var originalScope = gs.getCurrentApplicationId();
    var fixed = 0;
    var alreadyClean = 0;
    try {
      gs.setCurrentApplicationId(DEX_SCOPE);
      for (var i = 0; i < BAD_LINKS.length; i++) {
        var r = new GlideRecord('sn_aia_agent_child');
        if (!r.get(BAD_LINKS[i])) {
          alreadyClean++;
          print('ZTSD_DEX_FIX: record ' + BAD_LINKS[i] + ' not found (already removed)');
          continue;
        }
        var currentChild = String(r.getValue('child_agent') || '');
        if (!currentChild) {
          alreadyClean++;
          print('ZTSD_DEX_FIX: record ' + BAD_LINKS[i] + ' child_agent already null — skip');
          continue;
        }
        r.setValue('child_agent', null);
        if (r.update()) {
          fixed++;
          changed++;
          print('ZTSD_DEX_FIX: nulled child_agent on ' + BAD_LINKS[i] + ' (was ' + currentChild + ')');
        } else {
          // Null update failed — try deleting the record in DEX scope
          var del = r.deleteRecord();
          if (del) {
            fixed++;
            changed++;
            print('ZTSD_DEX_FIX: deleted ' + BAD_LINKS[i] + ' (null update failed, delete succeeded)');
          } else {
            warn('ZTSD_DEX_FIX: could not null or delete ' + BAD_LINKS[i] + ' — manual intervention needed');
          }
        }
      }
    } finally {
      gs.setCurrentApplicationId(originalScope);
    }

    if (fixed > 0 || alreadyClean === BAD_LINKS.length) {
      pass('ztsd-dex-fix', 'DEX security violation fixed — ' + fixed + ' records severed, ' + alreadyClean + ' already clean');
      return true;
    } else {
      fail('ztsd-dex-fix', 'DEX agent_child records still present — ZTSD EPs will continue to terminate');
      return false;
    }
  }

  // Seed the ZScaler / MacBook Pro incident that demonstrates ZTSD DEX remediation in the lab.
  // Caller: Fred Luddy when available; otherwise resolve a stable active user.
  // CI: MacBook Pro (resolved dynamically from cmdb_ci_computer).
  // Assigned to l1.servicedesk so the ZTSD Trigger - L1 Service Desk Specialist flow fires.
  function seedZscalerIncident() {
    // Resolved dynamically, not hardcoded — sys_ids for these two records won't
    // match on a fresh CloudLabs instance even if ravi.kapoor/IT Support exist.
    var RAVI_SYS_ID = resolveUserSysId('ravi.kapoor');
    if (!RAVI_SYS_ID) {
      warn('seedZscalerIncident: user ravi.kapoor not found — skipping seed');
      return false;
    }
    var itSupportGr = new GlideRecord('sys_user_group');
    itSupportGr.addQuery('name', 'IT Support');
    itSupportGr.setLimit(1);
    itSupportGr.query();
    if (!itSupportGr.next()) {
      warn('seedZscalerIncident: group "IT Support" not found — skipping seed');
      return false;
    }
    var IT_SUPPORT_GROUP = String(itSupportGr.getUniqueValue());

    // Resolve caller by natural key. Fresh CloudLabs instances do not always carry
    // the named demo callers, so the final fallback is a deterministic active user.
    var callerSysId = '';
    var callerName  = '';
    var cu = new GlideRecord('sys_user');
    cu.addQuery('name', 'CONTAINS', 'Fred Luddy');
    cu.setLimit(1);
    cu.query();
    if (cu.next()) {
      callerSysId = cu.getUniqueValue();
      callerName  = cu.getValue('name');
    } else {
      // Prefer known demo personas when Fred Luddy is absent.
      var fallbackNames = ['Ahmed Saad', 'Luca Bianchi', 'Irene Rose'];
      for (var f = 0; f < fallbackNames.length && !callerSysId; f++) {
        var fu = new GlideRecord('sys_user');
        fu.addQuery('name', fallbackNames[f]);
        fu.setLimit(1);
        fu.query();
        if (fu.next()) {
          callerSysId = fu.getUniqueValue();
          callerName  = fu.getValue('name');
        }
      }
    }
    if (!callerSysId) {
      var anyCaller = new GlideRecord('sys_user');
      anyCaller.addQuery('active', true);
      anyCaller.addNotNullQuery('user_name');
      anyCaller.addNotNullQuery('name');
      anyCaller.addQuery('user_name', 'NOT IN', 'ravi.kapoor,admin,system,guest');
      if (anyCaller.isValidField('locked_out')) anyCaller.addQuery('locked_out', false);
      anyCaller.orderBy('user_name');
      anyCaller.setLimit(1);
      anyCaller.query();
      if (anyCaller.next()) {
        callerSysId = String(anyCaller.getUniqueValue());
        callerName = String(anyCaller.getValue('name') || anyCaller.getValue('user_name'));
        print('ZTSD_SEED: named demo caller unavailable; using active caller ' + callerName);
      }
    }
    if (!callerSysId) {
      var adminCaller = new GlideRecord('sys_user');
      adminCaller.addQuery('user_name', 'admin');
      adminCaller.setLimit(1);
      adminCaller.query();
      if (adminCaller.next()) {
        callerSysId = String(adminCaller.getUniqueValue());
        callerName = String(adminCaller.getValue('name') || 'admin');
        print('ZTSD_SEED: no demo caller available; using admin as incident caller');
      }
    }
    if (!callerSysId) {
      fail('ztsd-zscaler-incident', 'no active caller could be resolved by natural key');
      return false;
    }

    // Resolve MacBook Pro CI
    var ciSysId = '';
    var ciName  = '';
    var ci = new GlideRecord('cmdb_ci_computer');
    ci.addQuery('name', 'CONTAINS', 'MacBook Pro');
    ci.setLimit(1);
    ci.query();
    if (ci.next()) {
      ciSysId = ci.getUniqueValue();
      ciName  = ci.getValue('name');
    }

    // Check if any ZScaler incident is already correctly assigned to Ravi Kapoor
    var correct = new GlideRecord('incident');
    correct.addQuery('short_description', 'CONTAINS', 'ZScaler');
    correct.addQuery('assigned_to', RAVI_SYS_ID);
    correct.setLimit(1);
    correct.query();
    if (correct.next()) {
      pass('ztsd-zscaler-incident', 'ZScaler incident already assigned to Ravi Kapoor: ' + correct.getValue('number'));
      return true;
    }

    // Repair path: update any New-state ZScaler incidents missing the correct assignment
    var RCA_DESC = 'ZScaler tunnel dropping intermittently on this endpoint — device-specific; other users on the same network are unaffected. Network diagnostics show VPN gateway disconnects unique to this machine. No recent software changes or updates were made. Standard troubleshooting (client reinstall, reboot) has not resolved the issue. Device-level investigation required to identify root cause.';
    var repair = new GlideRecord('incident');
    repair.addQuery('short_description', 'CONTAINS', 'ZScaler');
    repair.addQuery('state', '1'); // New only — leave In Progress alone (ZTSD may own it)
    repair.query();
    var repaired = 0;
    while (repair.next()) {
      repair.setValue('assignment_group', IT_SUPPORT_GROUP);
      repair.setValue('assigned_to', RAVI_SYS_ID);
      repair.setValue('description', RCA_DESC);
      repair.setWorkflow(false);
      repair.autoSysFields(false);
      repair.update();
      repaired++;
      print('ZTSD_SEED: repaired ' + repair.getValue('number') + ' → IT Support / Ravi Kapoor + RCA description');
    }
    if (repaired > 0) {
      changed++;
      pass('ztsd-zscaler-incident', repaired + ' ZScaler incident(s) assigned to IT Support + Ravi Kapoor');
      return true;
    }

    // Ensure the MacBook Pro CI has a DEX device record so the DEX investigation can find it.
    // DSSInvestigationUtil._filterDeviceListWithActiveAgents queries sn_agent_ci_extended_info
    // where cmdb_ci IN [ciSysId] AND status=0. Without this row the deviceIdList arrives empty
    // and the DEX agent logs "No valid Device found" then errors out.
    if (ciSysId && new GlideRecord('sn_agent_ci_extended_info').isValid()) {
      var existingAgent = new GlideRecord('sn_agent_ci_extended_info');
      existingAgent.addQuery('cmdb_ci', ciSysId);
      existingAgent.setLimit(1);
      existingAgent.query();
      if (!existingAgent.next()) {
        var agentRec = new GlideRecord('sn_agent_ci_extended_info');
        agentRec.initialize();
        agentRec.setValue('cmdb_ci', ciSysId);
        agentRec.setValue('status', '0');
        agentRec.setValue('name', ciName + ' DEX Agent');
        agentRec.setValue('agent_id', 'dex-agent-' + ciSysId.substring(0, 8));
        agentRec.setValue('ip_address', '10.0.1.42');
        agentRec.setValue('agent_version', '24.1.0');
        if (agentRec.insert()) {
          changed++;
          print('ZTSD_SEED: inserted sn_agent_ci_extended_info for CI=' + ciName);
        } else {
          warn('ZTSD_SEED: could not insert sn_agent_ci_extended_info — DEX may not find device');
        }
      } else {
        print('ZTSD_SEED: sn_agent_ci_extended_info already exists for CI=' + ciName);
      }
    }

    // Create the seed incident
    var inc = new GlideRecord('incident');
    inc.initialize();
    inc.setValue('short_description', 'ZScaler tunnel dropping — cannot reach internal apps');
    inc.setValue('description', 'ZScaler tunnel dropping intermittently on this endpoint — device-specific; other users on the same network are unaffected. Network diagnostics show VPN gateway disconnects unique to this machine. No recent software changes or updates were made. Standard troubleshooting (client reinstall, reboot) has not resolved the issue. Device-level investigation required to identify root cause.');
    inc.setValue('caller_id', callerSysId);
    inc.setValue('assignment_group', IT_SUPPORT_GROUP);
    inc.setValue('assigned_to', RAVI_SYS_ID);
    inc.setValue('contact_type', 'self-service');
    inc.setValue('priority', '4');
    inc.setValue('urgency', '3');
    inc.setValue('impact', '3');
    inc.setValue('category', 'Software');
    inc.setValue('subcategory', 'Application');
    if (ciSysId) inc.setValue('cmdb_ci', ciSysId);
    var newSysId = inc.insert();
    if (newSysId) {
      changed++;
      var newNum = inc.getValue('number');
      print('ZTSD_SEED: created ZScaler incident ' + newNum +
        ' caller=' + callerName + ' ci=' + (ciName || 'none') +
        ' assignment_group=IT Support assigned_to=Ravi Kapoor');
      pass('ztsd-zscaler-incident', 'ZScaler seed incident created: ' + newNum);
      return true;
    } else {
      fail('ztsd-zscaler-incident', 'Failed to insert ZScaler seed incident');
      return false;
    }
  }

  // ---------------------------------------------------------------------------
  // ZTSD KB + Catalog seeding — 4 incidents assigned to Ravi Kapoor that ZTSD
  // should auto-resolve via Research + Action Agent path.
  // INC0010766 (ZScaler tunnel / DEX investigation) is intentionally excluded
  // and left to route back to the team via the RCA / DEX path.
  // ---------------------------------------------------------------------------
  function seedRaviKbAndCatalog() {
    var IT_KB_SYS_ID       = 'a7e8a78bff0221009b20ffffffffff17'; // "IT" knowledge base
    var SERVICE_CATALOG_ID = 'e0d08b13c3330100c8b837659bba8fb4'; // Service Catalog
    var allOk = true;

    var articles = [
      // INC0010002 — Outlook disconnecting from Exchange
      {
        label: 'outlook',
        kbTitle: 'Fixing Outlook Disconnection from Exchange Server',
        kbText:
          '<h2>Outlook Disconnection from Exchange — Resolution Steps</h2>' +
          '<p>Use these steps when a user cannot send or receive email because Outlook has lost its Exchange connection.</p>' +
          '<ol>' +
          '<li>Run Outlook in safe mode (<code>outlook.exe /safe</code>) to rule out add-in conflicts.</li>' +
          '<li>Go to <strong>File &gt; Account Settings &gt; Email &gt; Repair</strong> to re-run Autodiscover and rebuild the server connection profile.</li>' +
          '<li>Close Outlook and delete the OST file from <code>%LOCALAPPDATA%\\Microsoft\\Outlook</code>, then reopen Outlook to force a clean rebuild.</li>' +
          '<li>If the issue persists, recreate the Outlook profile: <strong>Control Panel &gt; Mail &gt; Show Profiles &gt; Add</strong> and re-add the Exchange mailbox.</li>' +
          '<li>Verify the user\'s Exchange mailbox is healthy in the Exchange Admin Center under <strong>Recipients &gt; Mailboxes</strong>.</li>' +
          '</ol>' +
          '<p><strong>Escalate to:</strong> Exchange team if mailbox health check fails or Autodiscover cannot resolve the server address.</p>',
        catName: 'Outlook Exchange Profile Repair',
        catDesc:
          'Submit this request to have your Outlook profile fully rebuilt and your Microsoft Exchange connection re-established. ' +
          'This service repairs your Autodiscover configuration, deletes and rebuilds your OST mail data file, ' +
          'recreates your Outlook profile when needed, and verifies your Exchange mailbox health. ' +
          'Resolves all disconnection issues preventing users from sending or receiving corporate email.'
      },
      // INC0010003 — SAP locked out after SSO failure (business critical — month-end close)
      {
        label: 'sap',
        kbTitle: 'Recovering SAP Access After SSO Failure and Account Lockout',
        kbText:
          '<h2>SAP Account Lockout After SSO Failure — Resolution Steps</h2>' +
          '<p>Use these steps when a user is locked out of SAP following an SSO failure, including business-critical situations such as month-end financial close.</p>' +
          '<ol>' +
          '<li>Clear the SAP SSO Kerberos ticket cache: in SAP Logon Pad, go to <strong>Extras &gt; Settings &gt; Security &gt; Reset SSO Ticket</strong>.</li>' +
          '<li>Request an SAP Basis admin to unlock the account via transaction <strong>SU01 &gt; User &gt; Lock Status = Unlocked</strong>.</li>' +
          '<li>Have the user log out of all active SSO sessions (browser and desktop) and log back in to re-issue a fresh SAML/Kerberos assertion.</li>' +
          '<li>For business-critical month-end access: SAP Basis can issue a temporary emergency password via <strong>SU01 &gt; Password &gt; Reset</strong>.</li>' +
          '<li>Once access is restored, IT Security must re-provision the user\'s SSO certificate in the identity provider (IdP) to prevent recurrence.</li>' +
          '</ol>' +
          '<p><strong>Escalate to:</strong> SAP Basis team for account unlock; IT Security for SSO certificate re-provisioning.</p>',
        catName: 'SAP Account Unlock and SSO Reset',
        catDesc:
          'Submit this request to unlock your SAP user account and reset the SSO authentication session. ' +
          'This service covers SAP account unlock via SU01, SSO session cache clearance, ' +
          'SSO certificate re-provisioning by IT Security, and emergency temporary password issuance ' +
          'for business-critical situations including month-end financial close operations.'
      },
      // INC0010005 — Password reset portal unresponsive
      {
        label: 'pwreset',
        kbTitle: 'Manual Password Reset When Self-Service Portal Is Unavailable',
        kbText:
          '<h2>Manual Password Reset — Self-Service Portal Unavailable</h2>' +
          '<p>Use this process when the self-service password reset portal is unresponsive and users cannot reset their own credentials.</p>' +
          '<ol>' +
          '<li>User contacts the IT Service Desk directly (phone, in-person, or chat) to request an admin-initiated password reset.</li>' +
          '<li>IT admin verifies user identity via secondary method: manager confirmation, employee ID + date of birth, or registered security question.</li>' +
          '<li>Admin resets the password in Active Directory Users and Computers (<strong>ADUC &gt; User &gt; Reset Password</strong>), enabling "must change at next login".</li>' +
          '<li>Temporary credentials are delivered securely to the user\'s registered secondary email address or via SMS to a verified mobile number.</li>' +
          '<li>User logs in with temporary credentials and immediately sets a new permanent password that meets complexity requirements.</li>' +
          '<li>If the outage is widespread, escalate to the Identity Platform team to restart the SSPR (Self-Service Password Reset) service.</li>' +
          '</ol>' +
          '<p><strong>Escalate to:</strong> Identity Platform team if the portal outage affects multiple users simultaneously.</p>',
        catName: 'Manual Password Reset Request',
        catDesc:
          'Submit this request for an IT administrator to manually reset your password when the self-service password reset portal is unavailable or unresponsive. ' +
          'This service includes identity verification, admin-initiated Active Directory password reset with forced change on next login, ' +
          'secure delivery of temporary credentials, and escalation to the Identity Platform team if the portal outage is widespread.'
      },
      // INC0010765 — ZScaler login issue (authentication failure — NOT the tunnel/DEX incident)
      {
        label: 'zscaler-login',
        kbTitle: 'Resolving ZScaler Authentication and Login Failures',
        kbText:
          '<h2>ZScaler Login Failures — Resolution Steps</h2>' +
          '<p>Use these steps when a user cannot log in to ZScaler or is repeatedly prompted for credentials with no successful connection. ' +
          'This article covers authentication failures. Device-specific tunnel issues are handled separately.</p>' +
          '<ol>' +
          '<li>Right-click the ZScaler system tray icon and select <strong>Reset Sign-In</strong> to clear cached authentication tokens.</li>' +
          '<li>Click <strong>Sign In</strong> and re-enter corporate credentials (email and network password).</li>' +
          '<li>If credentials are rejected, check whether the AD account is locked (failed ZScaler retries can trigger AD lockout policy) — reset via ADUC if needed.</li>' +
          '<li>Open <strong>ZScaler App &gt; More &gt; Advanced &gt; Device Certificates</strong> — if the device certificate is expired or invalid, request re-enrollment from IT.</li>' +
          '<li>Verify the user\'s ZPA policy assignment with your IT admin: the user account must be in the correct ZPA access policy group to authenticate.</li>' +
          '<li>As a last resort, use the official ZScaler uninstaller, reboot, reinstall from the corporate software portal, and re-enroll the device.</li>' +
          '</ol>' +
          '<p><strong>Escalate to:</strong> Network Security team for ZPA policy assignment issues or device certificate re-provisioning.</p>',
        catName: 'ZScaler Authentication Reset and Re-enrollment',
        catDesc:
          'Submit this request to have your ZScaler authentication credentials reset and your ZPA access policy re-validated. ' +
          'This service covers ZScaler authentication token reset, Active Directory account unlock if triggered by failed ZScaler login attempts, ' +
          'device certificate re-provisioning, ZPA policy assignment verification, and full ZScaler client re-enrollment when required.'
      }
    ];

    for (var i = 0; i < articles.length; i++) {
      var a = articles[i];

      // --- KB article ---
      var existingKb = new GlideRecord('kb_knowledge');
      existingKb.addQuery('short_description', a.kbTitle);
      existingKb.addQuery('kb_knowledge_base', IT_KB_SYS_ID);
      existingKb.query();
      if (existingKb.next()) {
        pass(a.label + '-kb', 'KB article already exists | sys_id=' + existingKb.sys_id);
      } else {
        var nkb = new GlideRecord('kb_knowledge');
        nkb.initialize();
        nkb.setValue('kb_knowledge_base', IT_KB_SYS_ID);
        nkb.setValue('short_description', a.kbTitle);
        nkb.setValue('text', a.kbText);
        nkb.setValue('workflow_state', 'published');
        var kbId = nkb.insert();
        if (!kbId) {
          fail(a.label + '-kb', 'kb_knowledge insert failed: ' + a.kbTitle);
          allOk = false;
        } else {
          changed++;
          pass(a.label + '-kb', 'KB article seeded | sys_id=' + kbId);
        }
      }

      // --- Catalog item ---
      var existingCat = new GlideRecord('sc_cat_item');
      existingCat.addQuery('name', a.catName);
      existingCat.query();
      if (existingCat.next()) {
        pass(a.label + '-cat', 'catalog item already exists | sys_id=' + existingCat.sys_id);
      } else {
        var ncat = new GlideRecord('sc_cat_item');
        ncat.initialize();
        ncat.setValue('name', a.catName);
        ncat.setValue('short_description', a.catDesc.substring(0, 250));
        ncat.setValue('description', a.catDesc);
        ncat.setValue('sc_catalogs', SERVICE_CATALOG_ID);
        ncat.setValue('active', true);
        var catId = ncat.insert();
        if (!catId) {
          fail(a.label + '-cat', 'sc_cat_item insert failed: ' + a.catName);
          allOk = false;
        } else {
          changed++;
          pass(a.label + '-cat', 'catalog item seeded | sys_id=' + catId);
        }
      }
    }

    if (allOk) {
      pass('ravi-kb-catalog', 'ZTSD seed complete — 4 KB articles + 4 catalog items ready');
    } else {
      fail('ravi-kb-catalog', 'one or more KB/catalog seeds failed');
    }
    return allOk;
  }

  // ---------------------------------------------------------------------------
  // Generic post-assessment-gap repair.
  // Handles any AI system created via workspace or catalog where:
  //   (a) risk_assessment_methodology defaulted to "Risk assessment for AI inventory"
  //       instead of "Risk classification for AI system", AND/OR
  //   (b) "Change assessment state" flow errored after impact assessment close

  var scopeOk = resetAppScopePreference();
  var dexOk = fixZtsdDexSecurityViolation();
  var incidentOk = seedZscalerIncident();
  var contentOk = seedRaviKbAndCatalog();

  print('ZTSD_CHANGES=' + changed);
  if (warnings.length) print('ZTSD_WARNINGS=' + JSON.stringify(warnings));
  print('ZTSD_SUMMARY=' + JSON.stringify({
    passed: passed,
    failed: failed,
    documented: documented,
    changed: changed
  }));
  print('--- GATE SUMMARY ---');
  print('GATE_ZTSD_APP_SCOPE=' + (scopeOk ? 'PASS' : 'FAIL'));
  print('GATE_ZTSD_DEX_FIX=' + (dexOk ? 'PASS' : 'FAIL'));
  print('GATE_ZTSD_INCIDENT=' + (incidentOk ? 'PASS' : 'FAIL'));
  print('GATE_ZTSD_CONTENT=' + (contentOk ? 'PASS' : 'FAIL'));

  if (failed === 0 && scopeOk && dexOk && incidentOk && contentOk) {
    print('VERDICT: PASS - ZTSD lab build finalized');
  } else {
    print('VERDICT: FAIL - one or more ZTSD finalizer gates failed');
  }
})();
```
{% endcode %}
