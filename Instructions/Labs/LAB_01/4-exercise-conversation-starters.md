---
lab:
    title: 'Exercise 3 - Add conversation starters to your declarative agent'
    module: 'LAB 01: Build a declarative agent for Microsoft 365 Copilot using Visual Studio Code'
---

# Exercise 3 - Add conversation starters to your declarative agent

In this exercise, you will update the declarative agent to include conversation starters that provide users with sample prompts to help them understand the types of questions they can ask.

### Exercise Duration

- **Estimated Time to complete**: 5 minutes

## Task 1 - Add conversation starters

In Visual Studio Code:

1. In the **appPackage** folder, open the **declarativeAgent.json** file.
1. Add the following code snippet to the file:

   ```json
   "conversation_starters": [
       {
           "title": "Microsoft 365",
           "text": "Tell me about Microsoft 365"
       },
       {
           "title": "Licensing",
           "text": "What licenses are available for Microsoft 365?"
       },
       {
           "title": "Product information",
           "text": "Can you provide information on a specific product?"
       },
       {
           "title": "Product troubleshooting",
           "text": "I'm having trouble with a product. Can you help me troubleshoot the issue?"
       },
       {
           "title": "Contact support",
           "text": "How can I contact support for help?"
       }
   ]
   ```

1. Save your changes.

The **declarativeAgent.json** file should look like this:

```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/copilot/declarative-agent/v1.0/schema.json",
    "version": "v1.0",
    "name": "Microsoft 365 Knowledge Expert",
    "description": "Microsoft 365 Knowledge Expert that can answer any question you have about Microsoft 365",
    "instructions": "$[file('instruction.txt')]",
    "capabilities": [
        {
            "name": "WebSearch",
            "sites": [
                {
                    "url": "https://learn.microsoft.com/microsoft-365/"
                }
            ]
        }
    ],
  "conversation_starters": [
       {
           "title": "Microsoft 365",
           "text": "Tell me about Microsoft 365"
       },
       {
           "title": "Licensing",
           "text": "What licenses are available for Microsoft 365?"
       },
       {
           "title": "Product information",
           "text": "Can you provide information on a specific product?"
       },
       {
           "title": "Product troubleshooting",
           "text": "I'm having trouble with a product. Can you help me troubleshoot the issue?"
       },
       {
           "title": "Contact support",
           "text": "How can I contact support for help?"
       }
  ]
}
```

## Task 2 - Test the declarative agent in Microsoft 365 Copilot Chat

Next, upload your changes and start a debug session.

In Visual Studio Code:

1. In the **Activity Bar**, open the **Microsoft 365 Agents Toolkit** extension.
1. In the **Lifecycle** section, select **Provision** and then **Publish**.
1. **Confirm** that you want to submit an update to the app catalog.
1. Wait for the upload to complete.

Continuing in the web browser:

1. In **Microsoft 365 Copilot**, select the icon in the top right to expand the **Copilot side panel**.
1. Find **Product support** in the list of agents and select it to enter the immersive experience to chat directly with the agent. Notice that the conversation starters you defined in the manifest display in the user interface.

![Screenshot of Microsoft Edge showing the Microsoft 365 Knowledge Expert declarative agent in the immersive experience with custom conversation starters.](../media/LAB_01/test-conversation-starters.png)

Close the browser to stop the debug session in Visual Studio Code.