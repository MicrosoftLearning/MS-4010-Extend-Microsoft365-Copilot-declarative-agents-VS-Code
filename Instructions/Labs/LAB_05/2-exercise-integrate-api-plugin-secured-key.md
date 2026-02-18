---
lab:
  title: 'Exercise 1 - Integrate an API plugin with an API secured with a key'
  module: 'LAB 05: Authenticate your API plugin for declarative agents with secured APIs'
---

# Exercise 1 - Integrate an API plugin with an API secured with a key

API plugins for Microsoft 365 Copilot allow you to integrate with APIs secured with a key. You keep the API key secure by registering it in the Teams vault. At runtime, Microsoft 365 Copilot executes your plugin, retrieves the API key from the vault, and uses it to call the API. By following this process, the API key stays secure and is never exposed to the client.

In this exercise, you create a new declarative agent with an API plugin that authenticates with an API key that you generate.

### Exercise Duration

- **Estimated Time to complete**: 10 minutes

## Task 1 - Create a new project

Start by creating a new API plugin for Microsoft 365 Copilot. Open Visual Studio Code.

In Visual Studio Code:

1. In the **Activity Bar** (side bar), activate the Teams Toolkit extension.
1. In the **Teams Toolkit** extension panel, choose **Create a New App**.
1. From the list of project templates, choose **Copilot Agent**.
1. From the list of app features, choose **Declarative Agent**.
1. Choose the **Add plugin** option.
1. Choose the **Start with a new API** option.
1. From the list of authentication types, choose **API Key (Bearer Token Auth)**.
1. As the programming language, choose **TypeScript**.
1. Choose a folder to store your project.
1. Name your project **da-repairs-key**.

Teams Toolkit creates a new project that includes a declarative agent, an API plugin, and an API secured with a key.

## Task 2 - Examine the API key authentication configuration

Before you continue, examine the API key authentication configuration in the generated project.

### Examine the API definition

First, have a look at how API key authentication is defined in the API definition.

In Visual Studio Code:

1. Open the **appPackage/apiSpecificationFile/repair.yml** file. This file contains the OpenAPI definition for the API.
1. In the **components.securitySchemes** section, notice the **apiKey** property:

  ```yml
  components:
    securitySchemes:
    apiKey:
      type: http
      scheme: bearer
  ```

  The property defines a security scheme that uses the API key as a bearer token in the authorization request header.

1. Locate the **paths./repairs.get.security** property. Notice that it references the **apiKey** security scheme.

  ```yml
  [...]
  paths:
    /repairs:
    get:
      operationId: listRepairs
      [...]
      security:
      - apiKey: []
  [...] 
  ```

### Examine the API implementation

Next, see how the API validates the API key on each request.

In Visual Studio Code:

1. Open the **src/functions/repairs.ts** file.
1. In the **repairs** handler function, locate the following line which rejects all unauthorized requests:

  ```typescript
  if (!isApiKeyValid(req)) {
    // Return 401 Unauthorized response.
    return {
    status: 401,
    };
  } 
  ```

1. The **isApiKeyValid** function is implemented further in the repairs.ts file:

  ```typescript
  function isApiKeyValid(req: HttpRequest): boolean {
    const apiKey = req.headers.get("Authorization")?.replace("Bearer ", "").trim();
    return apiKey === process.env.API_KEY;
  }
  ```

  The function checks if the authorization header contains a bearer token and compares it against the API key defined in the **API_KEY** environment variable.

This code shows a simplistic implementation of API key security, but it illustrates how API key security works in practice.

### Examine the vault task configuration

In this project, you use Teams Toolkit to add the API key to the vault. Teams Toolkit registers the API key in the vault using a special task in the project's configuration.

In Visual Studio Code:

1. Open the **./m365agents.local.yml** file.
1. In the **provision** section, locate the **apiKey/register** task.

  ```yml
  # Register API KEY
  - uses: apiKey/register
    with:
    # Name of the API Key
    name: apiKey
    # Value of the API Key
    primaryClientSecret: ${{SECRET_API_KEY}}
    # Teams app ID
    appId: ${{TEAMS_APP_ID}}
    # Path to OpenAPI description document
    apiSpecPath: ./appPackage/apiSpecificationFile/repair.yml
    # Write the registration information of API Key into environment file for
    # the specified environment variable(s).
    writeToEnvironmentFile:
    registrationId: APIKEY_REGISTRATION_ID
  ```

  The task takes the value of the **SECRET_API_KEY** project variable, stored in the **env/.env.local.user** file and registers it in the vault. Then, it takes the vault entry ID and writes it to the environment file **env/.env.local**. The outcome of this task is an environment variable named **APIKEY_REGISTRATION_ID**. Teams Toolkit writes the value of this variable to the **appPackages/ai-plugin.json** file that contains the plugin definition. At runtime, the declarative agent that loads the API plugin, uses this ID to retrieve the API key from the vault, and call the API securely.

## Task 3 - Configure API key for local development

Before you can test the project, you need to define an API key for your API. Then, store the API key in the vault and record the vault entry ID in your API plugin. For local development, store the API key in your project and use Teams Toolkit to register it in the vault for you.

In Visual Studio Code:

1. Open the **Terminal** pane (Ctrl + `).
1. In a command line:
  1. Restore project's dependencies, by running `npm install`.
  1. Generate a new API key by running: `npm run keygen`.
  1. Copy the generated key to clipboard.
1. Open the **env/.env.local.user** file.
1. Update the **SECRET_API_KEY** property to the newly generated API key. The updated property looks as follows:

  ```text
  SECRET_API_KEY='your_key'
  ```

1. Save your changes.

Each time you build the project, Teams Toolkit automatically updates the API key in the vault and updates your project with vault entry ID.