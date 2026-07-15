# Exercise 2: DEX Remediation Trigger AI Agent

> **Objective:** Verify that the DEX remediation trigger AI agent is active and review how it automatically executes remedial actions on end-user devices to resolve common device and application issues.
>
> ⏱️ **Total time:** ~5 minutes

***

### ❇️ Navigate to AI Agent Studio — Create and manage

1. In the filter navigator, type **AI Agent Studio** and select **AI Agent Studio > Overview**.

    > **Note:** AI Agent Studio is the workspace for *all* AI agents across your organization — not just the Service Desk specialist you configured in Exercise 1. Getting comfortable navigating here pays off well beyond this lab.
2. On the **Ready-made solutions** panel, select the **AI agents** tab.
3.  Select **Explore all** to view the full list of AI agents in your organization.

    <figure><img src=".gitbook/assets/2026-06-24 10.42.21.png" alt=""><figcaption></figcaption></figure>



<figure><img src=".gitbook/assets/Screenshot 2026-05-01 at 5.01.42 PM.png" alt=""><figcaption></figcaption></figure>

***

### ❇️ Locate the DEX remediation trigger AI agent

1. In the AI agents list, select the **Name** column header to sort the list alphabetically.
2.  Scroll down to locate the **DEX remediation trigger AI agent**.

    > **Tip:** There are 100+ agents in the list. Sorting by Name makes it much easier to find what you're looking for.
3. Before opening the agent, confirm the **Status** column shows **Active**.
4.  Select **DEX remediation trigger AI agent** to open it.



<figure><img src=".gitbook/assets/Screenshot 2026-05-01 at 5.04.49 PM.png" alt=""><figcaption></figcaption></figure>

***

### ❇️ Review the DEX Agent Specialty and Configuration

You are now in the **Agent-guided setup** for the DEX remediation trigger AI agent. This agent is read-only because it is part of the Now Assist for Digital End-user Experience (DEX) application.

1. On the **Define the specialty** page, review the following:
   * **AI agent name:** DEX remediation trigger AI agent
   * **AI agent description:** Read through the description to understand what the agent does. This agent receives a resolution plan, checks for supported remedial actions, and executes them on end-user devices (Windows OS and Mac OS endpoints) to resolve common device and application issues.
2. Note the **four supported remedial actions** this agent can perform:
   * Restarting Zscaler service (Windows OS / Mac OS)
   * Clearing Microsoft Teams application cache (Windows OS / Mac OS)
   * Repairing corrupt Outlook data files (Windows OS only)
   * Performing disk space cleanup (Windows OS only)
3.  Explore the left-hand navigation to familiarize yourself with the agent's full configuration:

    * **Define the specialty** — The agent's name, description, role, and required steps
    * **Add tools and information** — The tools the agent uses to execute remedial actions
    * **Define security controls** — User access and data access policies
    * **Add triggers** — What events cause this agent to activate
    * **Select channels and status** — Communication channels and activation status

    > **Note:** "Add tools and information" is where you directly restrict *which* tools and data sources an agent can reach — a separate control from the prompt/instructions you write on "Define the specialty." Restricting both together is what keeps an agent's blast radius contained.

    > The DEX remediation trigger AI agent works hand-in-hand with the L1 Service Desk AI Specialist. When the AI Specialist investigates an incident and determines that a device-level remediation is needed, it triggers this DEX agent to execute the fix directly on the end-user's device — no human intervention required. This is what makes the Zero Touch Support experience truly end-to-end.



<figure><img src=".gitbook/assets/Screenshot 2026-05-01 at 5.05.40 PM.png" alt=""><figcaption></figcaption></figure>

***

#### ✅ Checkpoint

You have successfully:

* Located the DEX remediation trigger AI agent in AI Agent Studio
* Confirmed the agent is **Active**
* Reviewed the agent's specialty and supported remedial actions
* Explored the agent's guided setup configuration

🎉 **Nice work!** The DEX component of your Zero Touch Support experience is ready to go. Combined with the L1 Service Desk AI Specialist you configured earlier, you now have a complete autonomous support pipeline -
