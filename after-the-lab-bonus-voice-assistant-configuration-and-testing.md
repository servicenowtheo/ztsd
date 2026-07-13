# After the lab, Bonus: Voice Assistant Configuration and Testing



#### ❇️ Use the guided setup experience within Assistant Designer to explore your Voice Assistant <a href="#use-the-guided-setup-experience-within-assistant-designer-to-configure-your-first-voice-assistant" id="use-the-guided-setup-experience-within-assistant-designer-to-configure-your-first-voice-assistant"></a>

1. In the navigation menu, go to: **All > Conversational Interfaces**> **Assistant Designer**
2. From the _Assistants_ pane, select **Now Assist Voice Deployment.**
3. From the _AI Agents_ pane, you will see a list of agents already assigned to the Assistant.  There should be one agent **Create incident with voice AI agent** active already.  We will activate an additional agent.

<figure><img src=".gitbook/assets/Screenshot 2026-05-05 at 10.29.17 PM.png" alt=""><figcaption></figcaption></figure>

4. Select the **Manage incident with voice AI agent** from the list.&#x20;
5. Navigate to the **Select channels and status** section and toggle the agent active and click **Done.** &#x20;
6. Now navigate back to the Assistant Designer and you should see the agent is active.
7.  Navigate to the **Settings** tab.  In the _Basic Details_ pane, fell free to edit with sample details:

    1. _**Name:**_ IT Service Desk Assistant
    2. **Description:** AI Voice Assistant that will handle Tier 0 calls across the technology organization - creating incidents, processing requests, providing INC/CHG status, troubleshooting Apps, and more.
    3. Tags:
       1. Choose the tag icon on the right. Then type 'Technology', followed by Enter, to apply the new tag
    4. Select **Save and Continue**
    5. Add Tags to track analytics for the voice assistant. For example: Technology

    <figure><img src=".gitbook/assets/Screenshot 2026-05-05 at 10.43.37 PM (1).png" alt=""><figcaption></figcaption></figure>


8. In the _Voice Personality_ pane, fill in the following details:
   1. **Language:** English
   2. **Welcome Message:** Hello, and thank you for calling Otto Enterprises. How may I assist you today?
   3. Persona, explore a few of the voice personas, using the play button to sample the voices.
   4. Select the **Help Desk Man** persona and then tap **Save and Continue**
9. In the _Communication Channels_ pane, we will be using a dummy Twilio setup for testing purposes:
   1. **Provider**: Twilio
   2. **Provider application**: AI Voice Agent Provider Application
   3. **Phone number to live agent**: _any number can work here_
   4. **Authentication token**: _any number sequence can work here_
   5. Select **Save and Continue**

In your instance, you will either configure for a CCaaS Integration (Telephony Provider) or a Mobile App integration.

For today's lab session, we will be not be changing the authentication methods, but you can explore the preconfigured settings.

9. In the _Authentication_ pane, review the following:
   1. Caller Identification: Primary - Phone number
   2. Authentication: Primary - SoftPIN

<figure><img src=".gitbook/assets/Screenshot 2026-05-05 at 10.51.50 PM.png" alt=""><figcaption></figcaption></figure>



10. In the _Safeguards_ pane:
    1. Select **Connect to a live agent**
    2. Examine the values listed for the Max Call Duration and Inactivity Timeout call constraints
    3. Select **Save and Continue**

Safeguards are a great way to ensure the Voice Agent delivers a premier caller experience. This includes the voice agent knowing when it's time to pass caller to a human agent or open a record for the issue.

1. Under the _Review_ pane:
   1. Review the selections that you made match the intended configuration in the exercise above.
   2. Select **Save and Activate**

\
We will use the Voice Agents Test Experience, located within the Assistant Designer, to test the AI Voice Agents you have built.

**With a AI Voice Agent Test Experience, you can now launch on-demand tests directly from your Voice Assistant.**

<figure><img src=".gitbook/assets/Screenshot 2026-05-03 at 4.34.28 PM.png" alt=""><figcaption></figcaption></figure>

This allows you to test the full stack. This includes the Voice Assistant/Voice Agent Orchestrator, as well as the assigned Voice Agents and their underlying Tools.​

1. To use the new Voice Agent Test Experience, we start by navigating to Assistant Designer and locating our Voice Assistant
2. We then select '**Test**' and the Test Experience UI will launch in a separate window.
3. Toggle the dropdown in the upper left corner to Chat (instead of Voice)
4. Click the **Start Call** button.  You will need to accept the microphone permissions in your browser to allow audio to process.  You may need to click **Restart** if you see the timer count up but not the initial greeting message.

We will conduct a voice-based test in a moment, as a group.​

**NOTE:** For those with noise cancelling headphones, you can also opt to conduct your own voice based test (now or once you have completed all other exercises), just please be mindful of your volume.

1. The Voice Agent test will then begin with a message from the Voice Agent of "Hi, I am your voice assistant. How may I help you today?"
2. Proceed in the chat based conversation with a goal of testing both the OOB 'Incident Creation', as well as the Manage ticket agent.
3. Key Phrases during the conversation are likely to include:
   * "Can you help me create a ticket?"
   * "Do I have any open tickets?"
   * "What can you help me with?"
4. Hit "End Call" to conclude a test run

AI Voice Agents use an LLM to reason through each interaction, which means the path to resolution may vary from call to call — that's by design. Rather than following a fixed script, the agent adapts based on what the caller says.  To test your Voice Agent effectively, focus on the key issues and themes above and explore how the agent handles them across different conversation flows.  If needed, use the **Restart** button to re-initiate the Voice Agent test.  Once you have concluded your tests, exit the Test Experience window and proceed to the next exercise on setting up the L1 Service Desk AI Specialist.
