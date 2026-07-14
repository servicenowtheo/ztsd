---
description: Blast from the past
---

# Excercise 4: Give your specialist Knowledge

> **Objective:** Author a brand new knowledge article in the IT Knowledge Base, publish it, then create an incident that the AI L1 Service Desk Specialist will deflect using your article. This demonstrates how the AI Specialist leverages your organization's knowledge content to resolve incidents autonomously.
>
> 🧠 **This is where Zero Touch Support gets personal** — the AI Specialist is only as good as the knowledge you give it. Better articles mean faster, more accurate resolutions.

> **Who to be:** Stay impersonating **Ravi Kapoor** from Exercise 3 for this exercise — his role set includes `knowledge`, which is enough to author and edit KB articles. If you ended impersonation, logging back in as the **System Administrator** works too.

***

### ❇️ Step 1 — Navigate to Knowledge Center

1. In the filter navigator, type `Knowledge Center` and select **Knowledge > Knowledge Center**.
2.  You'll land on the Knowledge Center home page showing your knowledge bases, most viewed articles, and configuration options.



<figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.50.02 PM (1).png" alt=""><figcaption></figcaption></figure>



***

### ❇️ Step 2 — Create a New Article

1. Under **Actions** on the right side of the page, select **Create an article**.
2. On the **Create new article** page:
   1. **Select knowledge base:** Choose **IT**
   2. **Select article template:** Choose **Standard**
   3. The **Article template preview** will show the Standard template on the right.
3.  Select **Next** in the top-right corner.



<div><figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.51.02 PM (1).png" alt=""><figcaption></figcaption></figure> <figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.51.28 PM (1).png" alt=""><figcaption></figcaption></figure> <figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.52.10 PM (1).png" alt=""><figcaption></figcaption></figure></div>



***

### ❇️ Step 3 — Write Your Knowledge Article

1. In the **Short description** field, enter the title for your article. Choose one of the scenarios below (or write your own!).
2.  In the article body canvas:

    1. From the **Blocks** panel on the right, drag the **1 Column** component onto the canvas.
    2. Then drag the **Text** block into the column.
    3. Select the Text block and paste or type your article content.

    > **Pick a scenario below and copy the sample content into your article:**



<div><figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.54.25 PM (2).png" alt=""><figcaption></figcaption></figure> <figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 9.57.11 PM.png" alt=""><figcaption></figcaption></figure> <figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 10.04.58 PM (1).png" alt=""><figcaption></figcaption></figure></div>

***

#### 📄 Option A: Password Reset Self-Service Guide

**Short description:** `How to Reset Your Password Using Self-Service`

**Article content:**

> If you're locked out of your account or need to change your password, follow these steps to reset it without contacting the service desk.
>
> **Reset via the Self-Service Portal:**
>
> 1. Navigate to the ServiceNow Employee Center at https://your-instance.service-now.com/esc.
> 2. On the login screen, select "Forgot Password."
> 3. Enter your corporate email address and select Submit.
> 4. Check your email for a password reset link. The link expires after 15 minutes.
> 5. Select the link and enter a new password that meets the following requirements: at least 12 characters, one uppercase letter, one lowercase letter, one number, and one special character.
> 6. Confirm your new password and select Save.
> 7. Return to the login page and sign in with your new password.
>
> **Reset via Mobile Device:**
>
> If you have the ServiceNow mobile app installed, open the app, tap "Forgot Password" on the login screen, and follow the same steps above.
>
> **Still locked out?**
>
> If you do not receive the reset email within 5 minutes, check your spam or junk folder. If the issue persists, contact the IT Service Desk and request a manual password reset. Please have your employee ID ready for verification.

***

#### 📄 Option B: Troubleshooting Common Outlook Issues

**Short description:** `Outlook FAQs — Fixing Sync, Crash, and Performance Issues`

**Article content:**

> This article covers the most common Microsoft Outlook issues reported by employees and how to resolve them.
>
> **Outlook is not syncing emails:**
>
> 1. Check your internet connection by opening a web browser and navigating to any website.
> 2. In Outlook, go to File > Account Settings > Account Settings, select your email account, and choose Repair.
> 3. Follow the prompts to complete the repair process and restart Outlook.
> 4. If the issue persists, try removing and re-adding your email account.
>
> **Outlook keeps crashing or freezing:**
>
> 1. Close Outlook completely.
> 2. Open Outlook in Safe Mode by holding the Ctrl key while launching Outlook, then select Yes when prompted.
> 3. If Outlook works in Safe Mode, the issue is likely caused by an add-in. Go to File > Options > Add-ins > Manage COM Add-ins > Go, and disable all add-ins. Re-enable them one at a time to identify the culprit.
> 4. If Outlook crashes in Safe Mode as well, try repairing the Outlook data file by running the Inbox Repair Tool (ScanPST.exe) located at C:\Program Files\Microsoft Office\root\Office16.
>
> **Outlook is running slowly:**
>
> 1. Check your mailbox size by going to File > Mailbox Settings. If your mailbox is over 90% capacity, archive or delete old emails.
> 2. Disable unnecessary add-ins (see steps above).
> 3. Compact your Outlook data file by going to File > Account Settings > Data Files, selecting your data file, and choosing Compact Now.
> 4. Ensure Outlook and Windows are fully updated.
>
> **Cannot send or receive attachments:**
>
> Attachments are limited to 25 MB per message. For larger files, upload the file to OneDrive or SharePoint and share a link instead. If you are unable to receive attachments, contact your IT administrator to verify that attachment policies are not blocking the file type.

***

#### 📄 Option C: Connecting to the Corporate VPN

**Short description:** `How to Connect to the Corporate VPN from Home or a Remote Location`

**Article content:**

> This article explains how to connect to the corporate VPN (Zscaler Private Access) to securely access company resources when working remotely.
>
> **Before you begin:**
>
> Ensure that the Zscaler Client Connector application is installed on your device. It is pre-installed on all company-managed laptops. If you do not see the Zscaler icon in your system tray (Windows) or menu bar (Mac), contact the IT Service Desk to have it installed.
>
> **Connecting to the VPN:**
>
> 1. Locate the Zscaler Client Connector icon in your system tray (Windows, bottom-right) or menu bar (Mac, top-right).
> 2. Select the icon and choose "Open Zscaler."
> 3. If you are not signed in, select Sign In and authenticate using your corporate email and password. Complete multi-factor authentication if prompted.
> 4. Once signed in, the Zscaler Client Connector will automatically establish a secure tunnel. The icon will turn green when connected.
> 5. Verify connectivity by accessing an internal resource such as the company intranet or SharePoint.
>
> **Troubleshooting VPN connectivity:**
>
> If the Zscaler icon shows a red or yellow status:
>
> 1. Right-click the Zscaler icon and select Restart Service.
> 2. If the issue persists, disconnect from any personal VPNs or proxy services that may conflict with Zscaler.
> 3. Restart your device and try connecting again.
> 4. Ensure your operating system and Zscaler Client Connector are up to date.
> 5. If you are on a restricted network (hotel, airport, public Wi-Fi), try switching to a mobile hotspot to rule out network-level blocking.
>
> **Still unable to connect?**
>
> If none of the above steps resolve the issue, open a new incident with the IT Service Desk. Include the following details: your device name, operating system version, the network you are connected to, and any error messages displayed by Zscaler.

***

### ❇️ Step 4 — Save and Publish

1. Review your article content for completeness.
2. Select **Save** in the top-right corner.
3. Update the **Workflow** field from `Draft` to `Published`.
4.  Select **Save** again.

    > On this lab's Knowledge Base, articles publish immediately — no separate approval step required. If your instance is configured differently, the article may instead move to **Review** and show the banner _"This knowledge item is in review."_ If that happens, complete Step 5 below before continuing; otherwise skip straight to Step 6.



<figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 10.09.54 PM.png" alt=""><figcaption></figcaption></figure>



***

### ❇️ Step 5 — Approve the Knowledge Article (only if your article is stuck in Review)

Skip this step if your article's **Workflow** field already shows **Published** — most students won't need it. If your article is stuck in **Review**, it needs approval before it becomes available to the AI Specialist. You'll need to impersonate the approver to complete this step.

1. On the knowledge article record, select the **Approvals** tab.
2. Note the **Approver** name listed (e.g., `Bernard Laboy`).

<figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 10.09.54 PM (1).png" alt=""><figcaption></figcaption></figure>

1. Select your **user avatar** in the top-right corner of the ServiceNow header.
2. Select **Impersonate another user**.
3. In the **Impersonate user** dialog, type the approver's name (e.g., `Bernard Laboy`) and select them from the list.
4. Select **Impersonate user**.

<figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 10.11.39 PM.png" alt=""><figcaption></figcaption></figure>

1. Once impersonating the approver, navigate to **My Approvals**:
   1. In the filter navigator, type `My Approvals` and select **Service Desk > My Approvals**.
2. Sort the list by **Created** (click the Created column header) to find the most recent approval request.
3. Select the approval record for your knowledge article (e.g., `Knowledge: KB0010001 v0.02`).
4. Review the **Summary of item being approved** at the bottom to confirm it's your article.
5. Select **Approve**.

<figure><img src=".gitbook/assets/Screenshot 2026-05-04 at 10.13.25 PM.png" alt=""><figcaption></figcaption></figure>

**End impersonation:** Select your user avatar in the top-right corner and select **End impersonation** to return to your admin account.

> Once approved, the article's workflow state changes to **Published** and the content becomes available to the AI Specialist's knowledge sources. The ZTSD Search Profile you reviewed in Module 2 will index this content.

***

### ❇️ Step 6 — Create a Matching Incident

Now create an incident that the AI Specialist can resolve using your new article.

1. Navigate to **Service Operations Workspace** (Workspaces > Service Operations Workspace).
2.  Create a new incident and fill in the following fields:

    * **Caller:** `Able Tuter`
    * **Channel:** `Email`
    * **Short description:** Enter a description that matches your article. For example:

    | If you wrote...                   | Create an incident with...                                   |
    | --------------------------------- | ------------------------------------------------------------ |
    | Password Reset Self-Service Guide | `I'm locked out of my account and need to reset my password` |
    | Outlook FAQs                      | `Outlook keeps crashing every time I open it`                |
    | Corporate VPN Guide               | `I can't connect to the VPN from my home network`            |
3. In the **Assigned to** field, type `ai` and select **AI L1 Service Desk Specialist**.
4. Review the **Assign** dialog — note the Now Assist-generated work notes summarizing the issue.
5. Select **Save**.

***

### ❇️ Step 7 — Watch the AI Specialist Deflect the Incident

1. On the right sidebar, open the **Agentic Processes** menu item.
2. Select **Show steps** to watch the AI Specialist work through the incident in real time.
3. Observe as the AI Specialist:
   * Fetches task details
   * Researches knowledge sources
   * **Finds your newly published article**
   * Proposes a resolution using the content from your article
4.  Review the **Activity** feed for the AI Specialist's resolution notes — you should see content sourced from the article you just wrote!

    > 🎉 **You did it!** You've just closed the loop on Zero Touch Support. The knowledge you create directly powers the AI Specialist's ability to resolve incidents. Better knowledge articles = higher deflection rates = fewer tickets for your human agents.





***

#### ✅ Final Checkpoint

You have successfully:

* Created a new knowledge article in the IT Knowledge Base
* Published the article (approving it first if your instance required that step)
* Confirmed the article is available to the AI Specialist
* Created a matching incident and assigned it to the AI Specialist
* Watched the AI Specialist use your article to autonomously deflect the incident

**This is the power of Zero Touch Support — the more knowledge you feed the system, the smarter it gets.** 🚀
