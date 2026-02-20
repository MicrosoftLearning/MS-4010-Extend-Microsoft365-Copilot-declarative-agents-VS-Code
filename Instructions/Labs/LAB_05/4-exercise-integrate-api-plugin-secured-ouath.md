---
lab:
  title: 'Exercise 3 - Integrate an API plugin with an API secured with OAuth'
  module: 'LAB 05: Authenticate your API plugin for declarative agents with secured APIs'
---

# Exercise 3 - Integrate an API plugin with an API secured with OAuth

API plugins for Microsoft 365 Copilot allow you to integrate with APIs secured with OAuth. You keep the client ID and secret of the app that protects your API secure by registering them in the Teams vault. At runtime, Microsoft 365 Copilot executes your plugin, retrieves the information from the vault, and uses it to get an access token and call the API. By following this process, the client ID and secret stay secure and are never exposed to the client.

### Exercise Duration

- **Estimated Time to complete**: 10 minutes

## Task 1 - Open the sample project and examine authentication configuration

Start by downloading the sample project:

1. In a web browser, navigate to [https://aka.ms/learn-da-api-ts-repairs](https://aka.ms/learn-da-api-ts-repairs). You get a prompt to download a ZIP file with the sample project.
1. Save the ZIP file on your computer.
1. Extract the ZIP file contents.
1. Open the folder in Visual Studio Code.

The sample project is a Microsoft 365 Agents Toolkit project that includes a declarative agent, an API plugin, and an API secured with Microsoft Entra ID. The API is running on Azure Functions and implements security using Azure Functions' built-in authentication and authorization capabilities, sometimes referred to as Easy Auth.

## Task 2 - Examine the OAuth2 authorization configuration

Before you continue, examine the OAuth2 authorization configuration in the sample project.

### Examine the API definition

First, have a look at the security configuration of the API definition included in the project.

In Visual Studio Code:

1. Open the **appPackage/apiSpecificationFile/repair.yml** file.
1. In the **components.securitySchemes** section, notice the **oAuth2AuthCode** property:

  ```yml
  components:
    securitySchemes:
    oAuth2AuthCode:
      type: oauth2
      description: OAuth configuration for the repair service
      flows:
      authorizationCode:
        authorizationUrl: https://login.microsoftonline.com/${{AAD_APP_TENANT_ID}}/oauth2/v2.0/authorize
        tokenUrl: https://login.microsoftonline.com/${{AAD_APP_TENANT_ID}}/oauth2/v2.0/token
        scopes:
        api://${{AAD_APP_CLIENT_ID}}/repairs_read: Read repair records 
  ```

  The property defines an OAuth2 security scheme and includes information about the URLs to call to get an access token and which scopes the API uses.

  > **IMPORTANT**
  > Notice, that the scope is fully-qualified with the application ID URI (**api://...**). When working with Microsoft Entra you need to fully-qualify custom scopes. When Microsoft Entra sees an unqualified scope, it assumes that it belongs to Microsoft Graph, which leads to authorization flow errors.

1. Locate the **paths./repairs.get.security** property. Notice that it references the **oAuth2AuthCode** security scheme and scope that the client needs to perform the operation.

  ```yml
  [...]
  paths:
    /repairs:
    get:
      operationId: listRepairs
      [...]
      security:
      - oAuth2AuthCode:
        - api://${{AAD_APP_CLIENT_ID}}/repairs_read
  [...]
  ```

  > **IMPORTANT**
  > Listing the necessary scopes in the API spec is purely informational. When implementing the API, you're responsible for validating the token and checking that it contains the necessary scopes.

### Examine the API implementation

Next, have a look at the API implementation.

In Visual Studio Code:

1. Open the **src/functions/repairs.ts** file.
1. In the **repairs** handler function, locate the following line which checks if the request contains an access token with the necessary scopes:

  ```typescript
  if (!hasRequiredScopes(req, 'repairs_read')) {
    return {
    status: 403,
    body: "Insufficient permissions",
    };
  }
  ```

1. The **hasRequiredScopes** function is implemented further in the **repairs.ts** file:

  ```typescript
  function hasRequiredScopes(req: HttpRequest, requiredScopes: string[] | string): boolean {
    if (typeof requiredScopes === 'string') {
    requiredScopes = [requiredScopes];
    }
  
    const token = req.headers.get("Authorization")?.split(" ");
    if (!token || token[0] !== "Bearer") {
    return false;
    }
  
    try {
    const decodedToken = jwtDecode<JwtPayload & { scp?: string }>(token[1]);
    const scopes = decodedToken.scp?.split(" ") ?? [];
    return requiredScopes.every(scope => scopes.includes(scope));
    }
    catch (error) {
    return false;
    }
  }
  ```

  The function starts by extracting the bearer token from the authorization request header. Next, it uses the **jwt-decode** package to decode the token and get the list of scopes from the **scp** claim. Finally, it checks if the **scp** claim contains all required scopes.

  Notice that the function isn't validating the access token. Instead, it only checks if the access token contains the required scopes. In this template, the API is running on Azure Functions and implements security using Easy Auth, which is responsible for validating the access token. If the request doesn't contain a valid access token, the Azure Functions runtime rejects it before it reaches your code. While Easy Auth validates the token, it doesn't check the necessary scopes, which you need to do yourself.

### Examine the vault task configuration

In this project, you use Microsoft 365 Agents Toolkit to add the OAuth information to the vault. Microsoft 365 Agents Toolkit registers the OAuth information in the vault using a special task in the project's configuration.

In Visual Studio Code:

1. Open the **./teampsapp.local.yml** file.
1. In the **provision** section, locate the **oauth/register** task.

  ```yml
  - uses: oauth/register
    with:
    name: oAuth2AuthCode
    flow: authorizationCode
    appId: ${{TEAMS_APP_ID}}
    clientId: ${{AAD_APP_CLIENT_ID}}
    clientSecret: ${{SECRET_AAD_APP_CLIENT_SECRET}}
    isPKCEEnabled: true
    # Path to OpenAPI description document
    apiSpecPath: ./appPackage/apiSpecificationFile/repair.yml
    writeToEnvironmentFile:
    configurationId: OAUTH2AUTHCODE_CONFIGURATION_ID
  ```

  The task takes the values of the **TEAMS_APP_ID**, **AAD_APP_CLIENT_ID**, and **SECRET_AAD_APP_CLIENT_SECRET** project variables, stored in the **env/.env.local** and **env/.env.local.user** files and registers them in the vault. It also enables Proof Key for Code Exchange (PKCE) as an extra security measure. Then, it takes the vault entry ID and writes it to the environment file **env/.env.local**. The outcome of this task is an environment variable named **OAUTH2AUTHCODE_CONFIGURATION_ID**. Microsoft 365 Agents Toolkit writes the value of this variable to the **appPackages/ai-plugin.json** file that contains the plugin definition. At runtime, the declarative agent that loads the API plugin, uses this ID to retrieve the OAuth information from the vault, and start and auth flow to get an access token.

  > **IMPORTANT**
  > The **oauth/register** task is only responsible for registering the OAuth information in the vault if it doesn't exist yet. If the information already exists, Microsoft 365 Agents Toolkit will skip running this task.

1. Next, locate the **oauth/update** task.

  ```yml
  - uses: oauth/update
    with:
    name: oAuth2AuthCode
    appId: ${{TEAMS_APP_ID}}
    apiSpecPath: ./appPackage/apiSpecificationFile/repair.yml
    configurationId: ${{OAUTH2AUTHCODE_CONFIGURATION_ID}}
    isPKCEEnabled: true
  ```

  The task keeps the OAuth information in the vault synchronized with your project. It's necessary for your project to work properly. One of the key properties is the URL on which your API plugin is available. Each time you start your project, Microsoft 365 Agents Toolkit opens a dev tunnel on a new URL. The OAuth information in the vault needs to reference this URL for Copilot to be able to reach your API.

### Examine the authentication and authorization configuration

The next part to explore is the Azure Functions' authentication and authorization settings. The API in this exercise uses Azure Functions' built-in authentication and authorization capabilities. Microsoft 365 Agents Toolkit configures these capabilities while provisioning Azure Functions to Azure.

In Visual Studio Code:

1. Open the **infra/azure.bicep** file.
1. Locate the **authSettings** resource:

  ```bicep
  resource authSettings 'Microsoft.Web/sites/config@2021-02-01' = {
    parent: functionApp
    name: 'authsettingsV2'
    properties: {
    globalValidation: {
      requireAuthentication: true
      unauthenticatedClientAction: 'Return401'
    }
    identityProviders: {
      azureActiveDirectory: {
      enabled: true
      registration: {
        openIdIssuer: oauthAuthority
        clientId: aadAppClientId
      }
      validation: {
        allowedAudiences: [
        aadAppClientId
        aadApplicationIdUri
        ]
      }
      }
    }
    }
  }
  ```

  This resource enables the built-in authentication and authorization capabilities on the Azure Functions app. First, in the **globalValidation** section, it defines that the app only allows authenticated requests. If the app receives an unauthenticated request, it rejects it with a 401 HTTP error. Then, in the **identityProviders** section, the configuration defines that it uses Microsoft Entra ID (previously known as Azure Active Directory) to authorize requests. It specifies which Microsoft Entra app registration it uses to secure the API, and which audiences are allowed to call the API.

### Examine the Microsoft Entra application registration

The final part to examine is the Microsoft Entra application registration that the project uses to secure the API with. When using OAuth, you secure access to resources using an application. The application typically defines credentials necessary to obtain an access token, such as a client secret or a certificate. It also specifies the different permissions (also known as scopes) that the client can request when calling the API. Microsoft Entra application registration represents an application in the Microsoft cloud and defines an application for use with OAuth authorization flows.

In Visual Studio Code:

1. Open the **./aad.manifest.json** file.
1. Locate the **oauth2Permissions** property.

  ```json
  "oauth2Permissions": [
    {
    "adminConsentDescription": "Allows Copilot to read repair records on your behalf.",
    "adminConsentDisplayName": "Read repairs",
    "id": "${{AAD_APP_ACCESS_AS_USER_PERMISSION_ID}}",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allows Copilot to read repair records.",
    "userConsentDisplayName": "Read repairs",
    "value": "repairs_read"
    }
  ],
  ```

  The property defines a custom scope, named **repairs_read** that grants the client the permission to read repairs from the repairs API.

1. Locate the **identifierUris** property.

  ```json
  "identifierUris": [
    "api://${{AAD_APP_CLIENT_ID}}"
  ]
  ```

  The **identifierUris** property defines an identifier that's used to fully qualify the scope.
