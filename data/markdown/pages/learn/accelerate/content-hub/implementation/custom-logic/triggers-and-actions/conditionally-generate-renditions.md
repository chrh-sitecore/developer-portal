---
title: 'Conditionally generate renditions'
description: 'This recipe describes how to conditionally generate a predefined set of renditions by attaching an additional media processing Matrix'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-12-05'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Custom Logic > Triggers and Actions'
author: 'Jeroen Feyaerts'
audience: 'Technical Implementers (Developers), Solution Architects'
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) **Context**

Processing additional renditions results in additional consumption of Media storage and increases the time to process your Asset. Sometimes these additional renditions are only needed when certain conditions are met.

As we want to save some storage space we will attach an additional Media Processing Matrix through the Trigger and Actions framework. This allows us to pre configure additional renditions and to conditionally generate these renditions for specific Assets.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) **Execution**

The following steps are needed to achieve the desired functionality. A more detailed description of each step can be found below:

1.  Add a new Media Processing Matrix.
    
2.  Create a Script/Action that Attaches your newly-created media matrix to a Target Asset.
    
3.  Create a trigger that will be executed when specific conditions are met.
    

### Add a new Media Processing Matrix

First of all we will need to create a new media processing matrix. This will allow us to pre-configure renditions that can be used for Social media.

*   Go to Manage \> Media Processing as a super user.
    
*   Click the “New set” button and create a new set with the following details:
    
    *   Name: _Social_
        
    *   Auto-run: _disabled_
        

![image-20241122-131529.png](/images/learn/accelerate/content-hub/attachments/5452759211/5452791983.png?width=760)

*   Once your new set has been created, you can go ahead and configure your desired media processing flow.
    
*   Next **Save** and **Publish** your newly created media matrix to make it available.
    

![image-20241122-131948.png](/images/learn/accelerate/content-hub/attachments/5452759211/5453349023.png?width=445)

### Create a Script and Action that attaches your newly created media matrix to a target Asset

*   Go to Manage \> Script as a superuser.
    
*   Create a new Script with the following details:
    
    *   Name: _PREFIX - Add Social Media Processing Matrix_
        
    *   Type: _Action_
        

![image-20241122-132428.png](/images/learn/accelerate/content-hub/attachments/5452759211/5452464396.png?width=664)

*   Once created, use the following script code. Then **Build**, **Publish** and E**nable** the script.
    

```csharp

using System.Linq;

//Retrieve the asset from the Context
var asset = Context.Target as IEntity;

//Define the name of the MediaMatrix entity
string mediaMatrixSetName = "Social";

//Create the query to search for the MediaMatrix
var query = Query.CreateQuery(
    entities =>
        from 
            e in entities
        where
            e.DefinitionName == "M.MediaMatrix" &&
            e.Property("MediaMatrixName") == mediaMatrixSetName
        select e);

//Search for the MediaMatrix entity
IIdQueryResult queryResult = await MClient.Querying.QueryIdsAsync(query);

//If no result, throw exception
if(!(queryResult?.Items.Count > 0))
{
    var message = $"The MediaMatrix {mediaMatrixSetName} is not found.";
    MClient.Logger.Error(message);
    throw new Exception(message);
}

//Retrieve the entity from the result
var mediaMatrixID = queryResult.Items.First();

//Search for the relation between the MediaMatrix and the Asset
var mediaMatrixRelation = await asset.GetRelationAsync<IChildToManyParentsRelation>("MediaMatrixToAsset");

//Check if the asset is already linked to the specified Media Matrix
if(!mediaMatrixRelation.Parents.Contains(mediaMatrixID))
{
    mediaMatrixRelation.Parents.Add(mediaMatrixID);
    await MClient.Entities.SaveAsync(asset);
    MClient.Logger.Info($"{mediaMatrixSetName} set relation added for asset {asset.Id.Value}.");
}
else
{
    MClient.Logger.Info($"{mediaMatrixSetName} has been already added for asset {asset.Id.Value}.");
}
```

*   Next navigate to Actions (Manage \> Actions).
    
*   Create a New Action by Hitting the “New Action” button with the following details:
    
    *   Name: _PREFIX - Add Social Media Processing Matrix_
        
    *   Type: _Action Script_
        
    *   Script: _PREFIX - Add Social Media Processing Matrix (this is your previously created Script)_
        

![image-20241122-133749.png](/images/learn/accelerate/content-hub/attachments/5452759211/5453447351.png?width=760)

### Create a trigger

*   Navigate to Manage \> Triggers.
    
*   Create a new trigger by hitting the New Trigger button.
    

#### General

Fill in the General tab with the following details:

*   Name: _PREFIX - Add Social Media Processing Matrix_
    
*   Objective: _Entity Creation, Entity Modification_
    
    *   The Objective in combinations with the Conditions allows us to define when the trigger will be executed.
        
*   Execution type: _In Background_
    
    *   Use ‘In Background’ where possible as ‘In Process’ blocks the entity save process.
        

![image-20241122-134130.png](/images/learn/accelerate/content-hub/attachments/5452759211/5452923045.png?width=760)

#### Conditions

*   Add the “M.Asset” target definition as we want to run the trigger for Assets.
    
*   We will first define to which conditions the asset need to apply in order to run the Action (the current state of the Asset)
    
    *   Add Content Repository and Final Lifecycle conditions first as a good practice:
        
        *   **Final Lifecycle**: _Created, Under Review, Approved_
            
        *   **Content Repository**: Standard
            
    *   Add an additional condition to filter on **Asset Type** “_Social_”.
        
    *   Finally we will also check if the _Social_ media processing matrix isn’t attached to our Asset Yet by adding a filter for **Media Matrix**.
        
*   The second part of the Condition is a good place to reserve for identifying what has changed on the entity (we are only interested in relevant changes):
    
    *   Add an additional filter on the **Asset Type** relation changes.
        

![image-20241122-140112.png](/images/learn/accelerate/content-hub/attachments/5452759211/5454659593.png?width=717)

\> As a good practice, try to put as many conditions as possible that fit the situation on the trigger so we avoid any unnecessary executions of our script. In the above example we split up 1) Conditions that indicate the current state of the entity (top), and 2) Conditions that checks any relevant changes made on the entity (bottom).

#### Actions

As we are using an “in Background” execution style trigger we can only configure “Post Actions”. To finish the trigger setup, point the Post action towards the “_PREFIX - Add Social Media Processing Matrix_“ script that we have created.

![image-20241122-140855.png](/images/learn/accelerate/content-hub/attachments/5452759211/5455249427.png?width=760)

### Result

When adding the Social Taxonomy to your asset, the Facebook and Instagram renditions are now automatically generated.

### Debugging

#### Actions Auditing

We can check and verify that the trigger has run from the Actions Auditing screen:

*   Go to Manage \> Actions as a superuser.
    
*   On the Auditing tab we can see any trigger execution.
    

![image-20241122-154610.png](/images/learn/accelerate/content-hub/attachments/5452759211/5455118393.png?width=760)

#### Script Logging

As there might be something wrong with your script, you can also check script logging:

*   Navigate to your script and hit the “view logs” button.
    

![image-20241122-154940.png](/images/learn/accelerate/content-hub/attachments/5452759211/5454888999.png?width=760)

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) **Insights**

Key takeaways, deeper analysis and considerations - this is the space where you can deep dive further in the conversation or topic.

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Recipes

|     |     |
| --- | --- |
|     | **Recipe** |
| 1   |     |
| 2   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Documentation

|     |     |
| --- | --- |
|     | **Documentation Link** |
| 1   | [https://doc.sitecore.com/ch/en/users/content-hub/create-processing-flows.html](https://doc.sitecore.com/ch/en/users/content-hub/create-processing-flows.html) |
| 2   | [https://doc.sitecore.com/ch/en/users/content-hub/create-media-processing-sets.html](https://doc.sitecore.com/ch/en/users/content-hub/create-media-processing-sets.html) |
| 3   | [https://doc.sitecore.com/ch/en/developers/cloud-dev/create-a-script.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/create-a-script.html) |
| 4   | [https://doc.sitecore.com/ch/en/developers/cloud-dev/create-a-trigger.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/create-a-trigger.html) |
| 5   | [https://doc.sitecore.com/ch/en/developers/cloud-dev/create-an-action.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/create-an-action.html) |
| 6   | [https://doc.sitecore.com/ch/en/users/content-hub/media-processing-use-cases.html](https://doc.sitecore.com/ch/en/users/content-hub/media-processing-use-cases.html) |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |