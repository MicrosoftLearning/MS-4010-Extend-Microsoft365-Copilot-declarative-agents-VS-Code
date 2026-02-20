---
lab:
  title: 'Exercise 2 - Test declarative agent in Microsoft 365 Copilot Chat'
  module: 'LAB 05: Authenticate your API plugin for declarative agents with secured APIs'
---

# Exercise 2 - Test declarative agent in Microsoft 365 Copilot Chat

In this exercise, you will test and deploy your declarative agent to Microsoft 365 and test it using Microsoft 365 Copilot Chat.

### Exercise Duration

- **Estimated Time to complete**: 10 minutes

## Task 1 - Test the declarative agent with the API plugin in Microsoft 365 Copilot Chat

The final step is to test the declarative agent with the API plugin in Microsoft 365 Copilot.

In Visual Studio Code:

1. In the Activity Bar, activate the **Microsoft 365 Agents Toolkit** extension.
1. In the **Microsoft 365 Agents Toolkit** extension panel, in the **Accounts** section, be sure you're signed in to your Microsoft 365 tenant.

  ![Screenshot of Microsoft 365 Agents Toolkit showing the status of the connection to Microsoft 365.](../media/LAB_05/3-teams-toolkit-account.png)

1. In the Activity Bar, switch to the Run and Debug view.
1. From the list of configurations, choose **Debug in Copilot (Edge)** and press the play button to start debugging.

  ![Screenshot of the debug option in Visual Studio Code.](../media/LAB_05/3-vs-code-debug.png)

  Visual Studio Code opens a new web browser with Microsoft 365 Copilot Chat. If prompted, sign in with your Microsoft 365 account.

In the web browser:

1. From the side panel, select the **da-repairs-keylocal** agent.

  ![Screenshot of the custom agent displayed in Microsoft 365 Copilot.](../media/LAB_05/3-copilot-agent-sidebar.png)

1. In the prompt text box, type `What repairs are assigned to Karin?` and submit the prompt.
1. Confirm that you want to send data to the API plugin using the **Always allow** button.

  ![Screenshot of the prompt to allow sending data to the API.](../media/LAB_05/3-allow-data.png)

1. Wait for the agent to respond.

  ![Screenshot of the response of the custom agent to the user's prompt.](../media/LAB_05/3-copilot-response.png)

Stop the debugging session in Visual Studio Code when you're done testing.