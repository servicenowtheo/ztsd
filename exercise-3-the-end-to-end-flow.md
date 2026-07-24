# Exercise 3: The end-to-end flow

> **Objective:** Put everything together! Watch the L1 Service Desk AI Specialist work a real queue of incidents — some it resolves entirely on its own inside ServiceNow, one it correctly declines to force a fit on and escalates instead, and one (Zscaler/DEX) where it takes autonomous action all the way through requesting your consent, stopping at the point where this lab's environment can't go further.
>
> ⏱️ **Total time:** ~35 minutes

This exercise is split into four parts. **Parts A–C are the primary, reliable proof of Zero Touch Support** — the AI Specialist resolving and triaging real incidents entirely within ServiceNow, no additional infrastructure required. **Part D is an advanced, optional walkthrough** of the DEX (Digital End-user Experience) handoff — it demonstrates real autonomous diagnosis, matching, and consent, but stops short of claiming a completed device-level fix, for reasons explained in that section.

***

### ❇️ Step 1 — Impersonate Ravi Kapoor

1. From the icon on top right, pull down and choose impersonate
2.  Choose **Ravi Kapoor**
3.  Select **Impersonate user** to confirm.

    <figure><img src=".gitbook/assets/2026-06-24 10.21.51.png" alt=""><figcaption></figcaption></figure>

***

## Part A — Primary Autonomous Action: Monitor Request

This is the most reliable, fully-reproduced proof point in this lab: a low-priority request the AI Specialist matches to a real catalog item and resolves end-to-end, with no human involved.

### ❇️ Step 2 — Assign the Monitor request incident

1. As Ravi, on the Service Operations Workspace, select the **List** button on the far left, then under **Incidents** select **Assigned to you**. Find the incident by its short description — **"Request for additional monitor for remote work setup"** (the exact `INC00XXXXX` number varies per instance).
2. On the incident record, navigate to the **Details** tab, scroll to **Assignment**, and in **Assigned to** type `ai` and select your L1 Service Desk AI Specialist (e.g., **Athena Service Desk AI Specialist**).
3. An **Assign** dialog will appear with Now Assist-generated work notes summarizing the issue. Select the **Now Assist sparkle icon** if the summary hasn't populated yet.
4. Select **Save** to confirm the assignment.

### ❇️ Step 3 — Watch the AI Specialist resolve it

1. On the right sidebar, open **Agentic Processes** and select **Show steps**.
2. Watch the AI Specialist identify your device (e.g., an Apple iMac 27"), match it against the service catalog, and recommend a specific monitor model.

    > **Be patient:** this takes about 5-8 minutes.
3. In the **Activity** feed, review the resolution notes — you should see a specific catalog item recommendation (e.g., a "Standard 27\" Monitor") with clear next steps for the caller.
4. Confirm the incident **State** has moved to **Resolved** with a close code of **Solution provided**.

    > ✅ **This is the expected, proven outcome.** The AI Specialist identified the right catalog item and closed the loop with zero human involvement — the cleanest example of autonomous resolution in this lab.

***

## Part B — Escalation Contrast: Zoom License Request

Not every incident should be auto-resolved — an AI Specialist that forces a fit on every ticket is worse than one that knows when to hand off. This part shows the correct opposite behavior.

### ❇️ Step 4 — Assign the Zoom license incident

1. Find the incident **"Request for Zoom license upgrade to allow meeting recordings"** in Ravi's queue and assign it to your AI Specialist the same way as Step 2.

### ❇️ Step 5 — Watch it correctly escalate

1. Open **Agentic Processes** and **Show steps** as before.
2. This time, the AI Specialist's research will conclude that no existing catalog item explicitly supports "enabling Zoom meeting recordings" — the closest matches ("Request zoom webinar," a non-standard software request) don't actually cover the ask.
3. Review the **Activity** feed — the AI Specialist should reassign the incident to a human Support Engineer, with an explanation of what it checked and why it couldn't confidently resolve the request itself.

    > ✅ **This is also the expected, correct outcome.** The AI Specialist isn't failing here — it's demonstrating the escalation behavior you configured in Exercise 1 (**Escalate and reroute**). An honest "I don't have a good match" is a better outcome than a wrong resolution.

***

## Part C — Optional / Caveated: Password Reset

> ⚠️ **Known reliability caveat, read before running this one.** Semantically, this is the best-fit request in the whole lab — there's a real, matching knowledge article and a real, matching "Password Reset" catalog item, and the AI Specialist's research step reliably finds both with a very high confidence score. But the **next** step — fetching full catalog item details to build the resolution — has shown intermittent failures independent of this specific incident (the same failure has also been seen elsewhere in this lab's underlying tooling). Depending on the day, this incident may resolve cleanly via the catalog item, or it may fall back to generic guidance and escalate to a human even though the AI Specialist found the right resources. Either outcome is expected — this section exists to show you both, not to guarantee one.

### ❇️ Step 6 — Assign the Password Reset incident

1. Find the incident **"Need help resetting my Active Directory password"** in Ravi's queue and assign it to your AI Specialist.

### ❇️ Step 7 — Watch and interpret the result

1. Open **Agentic Processes** and **Show steps**.
2. Review the **Activity** feed. You should see the AI Specialist correctly cite a real knowledge article (e.g., "Resetting Your Active Directory (AD) Password") and the "Password Reset" catalog item.
3. From there, one of two things happens:
   * **Clean resolution:** the incident closes with a specific, catalog-grounded reset procedure. If you see this, great — it's the ideal outcome for this scenario.
   * **Escalation despite finding the right resources:** the AI Specialist falls back to generic guidance and reassigns to a human, even though it clearly identified the correct KB article and catalog item earlier in the same run.

    > This is a known, disclosed gap in this lab's current tooling reliability — not something you configured incorrectly, and not a reason to re-run the test repeatedly hoping for a different outcome (a resolved incident here won't reprocess if you reassign it again).

***

## Part D — Advanced (Optional): DEX Handoff for Device-Level Issues

> This section is intentionally scoped to **diagnosis, matching, and consent** — not completed device remediation. Real device-level execution requires a physical endpoint running a real Agent Client Collector (ACC) that reports back through a MID Server or ITOM Cloud Services gateway. This lab environment doesn't have that infrastructure, so this walkthrough stops at the point where a real deployment would hand off to it. That's a correctly-designed platform boundary, not a bug in this lab.

### ❇️ Step 8 — Assign the Zscaler tunnel incident

1. Find the incident pre-seeded for this lab by its short description — **"ZScaler tunnel dropping — cannot reach internal apps"** (the exact number may differ on your instance).

    > **Note:** If this incident isn't in **New** state, update its state to **New** before continuing so the assignment step below triggers correctly.
2. On the incident record, navigate to the **Details** tab, scroll to **Assignment**, and in **Assigned to** type `ai` and select your L1 Service Desk AI Specialist. Confirm the **Assign** dialog's Now Assist-generated summary, then select **Save**.

    > **Note:** The screenshots below may not exactly match what you see on your instance — if they don't, trust the written steps over the picture.

<figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.15.56 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.16.39 PM.png" alt=""><figcaption></figcaption></figure>

### ❇️ Step 9 — Watch diagnosis, matching, and consent

1. On the right sidebar, open **Agentic Processes** and select **Show steps**.
2.  Observe the AI Specialist working through each stage:

    * ✅ Started AI Agent "Zero Touch Service..."
    * ✅ Fetching details of the given task
    * ✅ Task details fetched
    * ✅ Solution research complete
    * ✅ Data sources fetched
    * ✅ Finding potential solutions
    * ✅ Solutions fetched — this is where a real device diagnosis (not just KB research) runs, when the incident's linked device is DEX-tracked
    * ✅ Non-customer actions filtered
    * 🔵 A **consent request** is generated, proposing a specific remedial action (e.g., "Restart Zscaler service") and asking the caller to approve before anything executes on their device

    > **Be patient:** this can take 5-10 minutes, and it's normal to see "Checked on remaining steps" appear several times.

    > **What you may also see, and why it's fine:** the device-diagnosis step doesn't run for every incident on every attempt — this is a known, still-open behavior in the underlying platform, not something wrong with your setup. If it's skipped, the AI Specialist will still correctly identify a matching remedial action from the resolution catalog, but won't request consent for an action it can't tie to a real device.
3. Review the incident's **Activity** feed and the consent request record generated for the caller. This is the proof point for this section: the AI Specialist correctly diagnosed a device-level issue, matched it to a real supported remedial action, resolved the correct device identifier, and asked for permission before acting — all autonomously.

### ❇️ Step 10 — Where this lab stops, and why

Once consent is granted, the platform hands off to `Trigger remedial actions`, which waits for a real device-side execution callback — the signal that a physical endpoint actually ran the fix and reported back. In a production deployment with real Agent Client Collectors, MID Servers, and ITOM Cloud Services, this closes the loop automatically. **In this lab, there is no physical device to report that callback, so the flow will remain in a waiting state indefinitely rather than complete.**

> Do **not** interpret a "Waiting" execution state past this point as a failure of your configuration — it's the expected boundary of a lab without physical endpoints. The value of this exercise is everything that happened *before* this point: real diagnosis, real matching against the supported remedial-action catalog, a real, correctly-resolved device identifier, and a real consent request — the parts of Zero Touch Support that are genuinely proven end-to-end in this environment.

***

### Now go through the rest of Ravi's queue

You can continue through any remaining incidents assigned to Ravi and reassign them to your L1 Specialist the same way. Expect a similar mix of outcomes to what you just saw in Parts A–C: most resolve autonomously, some correctly escalate. That's Zero Touch Support working as designed — autonomous where it can be confident, and handing off cleanly where it can't.

#### ✅ Final Checkpoint

You have successfully:

* Watched the AI Specialist **autonomously resolve** a real incident end-to-end (Monitor request)
* Watched the AI Specialist **correctly escalate** an incident it couldn't confidently match (Zoom license)
* Seen a **known reliability caveat** in action, and understood why it's disclosed rather than hidden (Password Reset)
* Watched the AI Specialist perform **real device diagnosis, matching, and consent** for a Zscaler/DEX issue, and understood exactly where full autonomous device remediation requires infrastructure beyond this lab

**You've just experienced Zero Touch Support — where it's fully proven today, and where the platform's own boundaries are.** 🎉
