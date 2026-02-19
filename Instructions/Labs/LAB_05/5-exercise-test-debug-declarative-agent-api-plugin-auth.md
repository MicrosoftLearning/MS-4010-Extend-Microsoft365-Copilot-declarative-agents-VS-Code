---
lab:
    title: 'Exercise 4 - Test declarative agent in Microsoft 365 Copilot Chat'
    module: 'LAB 05: Authenticate your API plugin for declarative agents with secured APIs'
---

# Exercise 4 - Test declarative agent in Microsoft 365 Copilot

In this exercise, you will test and deploy your declarative agent to Microsoft 365 and test it using Microsoft 365 Copilot Chat.

### Exercise Duration

- **Estimated Time to complete**: 5 minutes

## Task 1 - Test the declarative agent with the API plugin in Microsoft 365 Copilot

The final step is to test the declarative agent with the API plugin in Microsoft 365 Copilot.

In Visual Studio Code:

1. In the Activity Bar, activate the **Teams Toolkit** extension.
1. In the **Teams Toolkit** extension panel, in the **Accounts** section, be sure you're signed in to your Microsoft 365 tenant.

    ![Screenshot of Teams Toolkit showing the status of the connection to Microsoft 365.](../media/LAB_05/3-teams-toolkit-account.png)

1. In the Activity Bar, switch to the **Run and Debug** view.
1. From the list of configurations, choose **Debug in Copilot (Edge)** and press the play button to start debugging.

    ![Screenshot of the debug option in Visual Studio Code.](../media/LAB_05/3-vs-code-debug.png)

    Visual Studio Code opens a new web browser with Microsoft 365 Copilot. If prompted, sign in with your Microsoft 365 account.

In the web browser:

1. From the side panel, select the **da-repairs-oauthlocal** agent.

    ![Screenshot of the custom agent displayed in Microsoft 365 Copilot.](../media/LAB_05/5-copilot-agent-sidebar.png)

1. In the prompt text box, type `Show repair records assigned to Karin Blair` and submit the prompt.

    > [!TIP]
    > Instead of typing the prompt, you can select it from the conversation starters.

    ![Screenshot of a conversation started in the custom declarative agent.](../media/LAB_05/5-conversation-starter.png)

1. Confirm that you want to send data to the API plugin using the **Always allow** button.

    ![Screenshot of the prompt to allow sending data to the API.](../media/LAB_05/5-allow-data.png)

1. When prompted, sign in to the API to continue using the same account that you use to sign in to your Microsoft 365 tenant, by selecting **Sign in to da-repairs-oauthlocal**.

    ![Screenshot of the prompt to sign in to the app that secures the API.](../media/LAB_05/5-sign-in.png)

1. Wait for the agent to respond.

    ![Screenshot of the response of the declarative agent to the user's prompt.](../media/LAB_05/5-agent-response.png)

Even though your API is accessible anonymously because it's running on your local machine, Microsoft 365 Copilot is calling your API authenticated as specified in the API spec. You can verify that the request contains an access token, by setting a breakpoint in the **repairs** function and submitting another prompt in the declarative agent. When the code reaches your breakpoint, expand the req.headers collection and look for the authorization header which contains a JSON Web Token (JWT).

![Screenshot of Visual Studio Code with a breakpoint and the debug panel showing the authorization header on the incoming request.](../media/LAB_05/5-vs-code-breakpoint-jwt.png)

Stop the debugging session in Visual Studio Code when you're done testing.
