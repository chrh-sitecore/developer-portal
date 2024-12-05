---
title: 'CH External Scripting Guidance and Scenarios'
description: 'The Sitecore External Scripting Recipe outlines best practices for script-based integration for external systems and third-party APIs to integrate with Sitecore Content Hub.In this recipe we cover the primary APIs: REST, WebClient, Fluent, and the JavaScript SDK and when to use them.'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-11-21'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Custom Logic'
author: 'Chris Howarth'
audience: ''
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) **Context**

Sitecore Content Hub supports external scripting through its REST API, WebClient SDK, Fluent SDK, and JavaScript SDK. These tools enable automation, data synchronisation, and integration with third-party systems.

*   **REST API**: Ideal for cross-platform, language-agnostic integrations.
    
*   **WebClient SDK**: Optimised for .NET applications, simplifying client-side operations.
    
*   **Fluent SDK**: Reduces repetitive tasks in .NET environments with an intuitive syntax.
    
*   **JavaScript SDK**: Best for browser-based apps and dynamic web solutions.
    

While Content Hub’s external scripting possibilities are very strong, we recommend the use of Connectors as the first port of call due to ease of management, Sitecore support, and integration with Sitecore products.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) **Execution**

### REST API

*   The Sitecore REST API provides a hypermedia-driven way to interact with Content Hub resources through standard HTTP methods like GET, POST, PUT, and DELETE, representing and transferring resource data as JSON. Developers can navigate and manipulate resources using hyperlinks rather than hardcoding URLs. Example uses include retrieving assets (GET requests), creating new entities (POST), or updating resource properties (PUT). Access is through HTTPS endpoints, and throttling must be managed in code - (i.e. via HTTP 429 error management). For full usage examples and best practices, refer to the documentation.
    
*   The Sitecore REST API can be integrated with 3rd party platforms to enable seamless data exchange with Sitecore Content Hub. Using the 3rd party’s HTTP connector, Sitecore endpoints can be accessed to retrieve or manipulate content assets, update metadata, or automate workflows. Authentication is required to secure these API calls.
    

### WebClient SDK

*   The Sitecore WebClient SDK is a .NET-based library that simplifies interactions with the Content Hub REST API, making it suitable for serverless applications like Azure Functions and Durable Functions. In these environments, the SDK can:
    
    1.  **Fetch or Push Content**: Automate retrieval or updates of digital assets and metadata.
        
    2.  **Data Processing**: Perform transformations or validation on entities.
        
    3.  **Integration**: Synchronise Content Hub data with external systems or services.
        
    
    Its robust authentication and built-in capabilities streamline cloud-based operations, reducing boilerplate code and improving reliability.
    
*   Another interesting use case is creating standalone console apps to perform bulk updates. Often when adding a new member onto an entity there will be a requirement to populate that member for historic entities.
    

### Fluent SDK

Streamlines repetitive API calls in .NET with a chainable interface.

*   The Fluent SDK offers a more intuitive, chainable syntax that streamlines complex or repetitive workflows. It is ideal for scenarios involving intricate operations or multiple sequential calls, enhancing developer productivity by reducing code verbosity.
    
*   In comparison with the **WebClient SDK** - they are both .NET libraries for interacting with the Sitecore Content Hub, but they serve slightly different purposes:
    
    *   The **WebClient SDK**: Focuses on simplifying basic API calls. It is suited for developers who need structured, out-of-the-box functionality to perform standard operations, such as CRUD (Create, Read, Update, Delete) tasks, without requiring deep customization.
        
    *   The **Fluent SDK**: Offers a more intuitive, chainable syntax that streamlines complex or repetitive workflows. It is ideal for scenarios involving intricate operations or multiple sequential calls, enhancing productivity by reducing code verbosity.
        

### JavaScript SDK

Enables browser-based applications to interact with Content Hub.

*   The **JavaScript SDK** is designed for browser-based and client-side applications to interact with Sitecore Content Hub's REST API. It simplifies asynchronous operations, such as:
    
    1.  **Dynamic UI Interactions**: Enable real-time content updates or asset retrieval in web apps.
        
    2.  **Lightweight Applications**: Ideal for front-end apps requiring Content Hub data.
        
    3.  **Custom Portals**: Build custom experiences that extend Sitecore's functionality.
        
    
    Its native JavaScript environment makes it suited for single-page applications (SPAs) or progressive web apps (PWAs), ensuring a seamless user experience without server dependencies.
    

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) **Insights**

### REST API Example using Postman (or similiar)

This approach fetches assets from the Content Hub using Postman for debugging and exploration.

1.  **Set up a GET Request**:
    
    *   URL: `https://your-content-hub-instance/api/entities/query?query=Definition.Name=='M.Asset'"`
        
    *   Method: `GET`
        
    *   Headers:
        
        *   `Authorization`: `Bearer your-access-token`
            
        *   `Content-Type`: `application/json`
            
2.  **Run the Request**:
    
    *   Open Postman, create a new request, and paste the URL.
        
    *   Add headers for authentication and content type. We recommend using an OAuth token for authentication - please see the documentation.
        
    *   Click "Send."
        
3.  **View Response**:
    
    *   You’ll see JSON data for assets returned by the API.
        

### REST API Example using JavaScript

```csharp

const apiUrl = "https://your-content-hub-instance/api/entities/query?query=Definition.Name=='M.Asset'";
const apiKey = "your-api-key";

fetch(`${apiUrl}?query=Definition.Name=='M.Asset'`, {
  method: "GET",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Bearer ${apiKey}`
  }
})
  .then(response => response.json())
  .then(data => {
    console.log("Assets:", data);
  })
  .catch(error => console.error("Error fetching assets:", error));

```

Note your authentication key should not be stored in plain text, this is simply an example for local testing.

### Webclient SDK Examples

#### Getting Started

Add the following NuGet feed to your Visual Studio package sources to pull in the required packages.

[https://nuget.sitecore.com/resources/v3/index.json](https://nuget.sitecore.com/resources/v3/index.json)

Then create a new project in Visual Studio. The project can be either a console application or an Azure Function App.

Refer to the documentation on how to authenticate your Webclient SDK program with your Content Hub instance.

#### Example Code

Note in this example we use `EntityLoadConfiguration.Full`\- in a real world scenario it is best practice to load a minimal entity configuration set. Refer to the `EntityLoadConfiguration` documentation for details.

```csharp

using Stylelabs.M.Sdk;
using Stylelabs.M.Sdk.WebClient.Querying;

public async Task FetchAssetsAsync()
{
    var client = MClientFactory.CreateMClient(new MClientOptions
    {
        ClientId = "your-client-id",
        ClientSecret = "your-client-secret",
        Endpoint = new Uri("https://your-instance-id.contenthub.azure.com/")
    });

    var query = Query.CreateQuery(entities => entities
        .Where(entity => entity.DefinitionName == "Asset"));

    var assets = await client.Querying.QueryAsync(query);

    if (assets.Items.Any())
    {
        foreach (var assetEntity in assets.Items)
        {
            var asset = await client.Entities.GetAsync(assetEntity.Id.Value, EntityLoadConfiguration.Full);
            Console.WriteLine($"Asset ID: {asset.Id}, Asset Title: {asset.GetPropertyValue<string>("Title")}");
        }
    }
}
```

### Fluent SDK Examples

#### Using the Fluent SDK to fetch assets

```csharp

using Stylelabs.M.Sdk.Fluent;

public async Task FetchAssetsUsingFluentSdk()
{
    var fluentClient = new FluentClient(MClient);

    var result = fluentClient.Entities.GetMany(new long[] { 6, 7 });

    foreach(var item in result)
    {
        if (item.Id.HasValue && item.Id.Value > default(long))
        {
            MClient.Logger.Info("Item" + item.Id.Value);
        }
    }
}

```

### JavaScript SDK Examples

TBC

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Recipes

|     |     |
| --- | --- |
|     | **Recipe** |
| 1   | [CH Scripts Guidance and Scenarios](CH-Scripts-Guidance-and-Scenarios) |
| 2   | [Webclient SDK Guidance and Scenarios](Webclient-SDK-Guidance-and-Scenarios) |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Documentation

|     |     |
| --- | --- |
|     | **Documentation Link** |
| 1   | [https://doc.sitecore.com/ch/en/developers/cloud-dev/external-integration.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/external-integration.html) |
| 2   | Sitecore REST API documentation: [https://doc.sitecore.com/ch/en/developers/cloud-dev/rest-api.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/rest-api.html) |
| 3   | OAuth tokens: [https://doc.sitecore.com/ch/en/developers/cloud-dev/oauth-tokens.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/oauth-tokens.html) |
| 4   | WebClient SDK Authentication: [https://doc.sitecore.com/ch/en/developers/cloud-dev/authentication-1286040.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/authentication-1286040.html) |
| 5   | Fluent SDK [https://doc.sitecore.com/ch/en/developers/cloud-dev/get-started-1286138.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/get-started-1286138.html) |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |