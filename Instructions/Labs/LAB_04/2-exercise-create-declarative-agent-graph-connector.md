---
lab:
    title: 'Exercise 1 - Create an external connection for the Graph connector'
    module: 'LAB 04: Add custom knowledge to declarative agents using Microsoft Graph connectors and Visual Studio Code'
---

# Exercise 1 - Create an external connection for the Graph connector

Extending a declarative agent with knowledge gives it access to additional information that's not a part of its Large Language Model. Using Graph connectors, you can ingest external data to Microsoft 365, where it's available to different user experiences, including Microsoft 365 Copilot. When configuring a Copilot agent's knowledge settings, you can integrate it with an external connection created by a Graph connector.

### Exercise Duration

- **Estimated Time to complete**: 10 minutes

## Task 1 - Download sample project and connect to resources

When you integrate a Copilot agent with a Graph connector, you need to specify the ID of the external connection that the connector created. Typically, you deploy Graph connectors separately from Copilot agents. To complete this exercise, deploy an existing Graph connector which you reference in later steps.

Start by downloading the Graph connector sample project.

1. In a web browser, navigate to [https://aka.ms/learn-gc-ts-policies](https://aka.ms/learn-gc-ts-policies). You get a prompt to download a ZIP file with the sample project.
1. Save the ZIP file on your computer.
    1. Create a new folder in **Documents**.
    1. Extract the contents of the downloaded ZIP file to the folder you just created.
    1. Open the folder in Visual Studio Code.

In Visual Studio Code:

1. From the File menu, choose the **Open folder...** option.
1. Open the project folder you just extracted to your **Documents folder**.
1. In the **Activity Bar** (side bar), open the **Microsoft 365 Agents Toolkit** extension.
1. In the **Accounts** pane, confirm that you're connected to your **Microsoft 365 tenant**.
1. In the **Accounts** pane, confirm that you're connected to your **Azure subscription**.

    ![Screenshot of Microsoft 365 Agents Toolkit showing signed in accounts.](../media/LAB_04/3-m365-agents-toolkit-accounts.png)

> [!NOTE]
> If you do not a full Microsoft 365 Copilot license, you may see that "Copilot Access Disabled." The exercises can still be completed, although you may not be able to fully test the agent in Microsoft 365 Copilot Chat.

## Task 2 - Run project and create connection to Microsoft 365

1. Start the project by pressing <kbd>F5</kbd>. Microsoft 365 Agents Toolkit creates a new Microsoft Entra app registration in your tenant that allows the Graph connector to communicate with your Microsoft 365 tenant. Microsoft 365 Agents Toolkit also starts the timer-triggered Azure Function which hosts the Graph connector.

> [!IMPORTANT]
> This step can take up to 10 minutes or more to complete, do not close until you have completed the exercise.

1. Before the Graph connector can run, you need to consent to the permissions that the Entra app needs. To grant consent, use the instructions from the **Terminal** pane associated with the **func: host start** task.

    ![Screenshot of Visual Studio Code showing the permissions consent message.](../media/LAB_04/3-consent-message.png)

1. Open the consent URL in a web browser. Sign in with your work account that belongs to your Microsoft 365 tenant. Grant the required permissions to the app using the **Grant admin consent** button.

    ![Screenshot of the Microsoft Entra ID portal where a user can grant consent.](../media/LAB_04/3-consent-microsoft-entra-id.png)

1. After you grant consent to the required permissions, the Graph connector continues. In the **Terminal** pane, notice the output of the Graph connector. The Graph connector creates an external connection, provisions the schema and ingests the sample content to your Microsoft 365 tenant.
1. Running the connector takes 5-10 minutes to complete. When it completes, stop debugging, by pressing the **Stop** button in the debug toolbar.

    ![Screenshot of Visual Studio Code terminal with Graph connector output.](../media/LAB_04/3-connector-done.png)
