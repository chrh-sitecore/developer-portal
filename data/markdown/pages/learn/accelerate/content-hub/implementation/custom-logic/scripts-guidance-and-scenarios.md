---
title: 'CH Scripts Guidance and Scenarios'
description: 'This Sitecore Content Hub Scripting Guide provides an overview of the steps and best practices for creating, managing, and implementing custom scripts within Sitecore Content Hub.'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-12-05'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Custom Logic'
author: 'Chris Howarth'
audience: 'Technical Implementers (Developers), Solution Architects, Product Owners/Business Stakeholders'
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) Context

Sitecore Content Hub offers extensive internal scripting with an internal user-friendly IDE that allows multiple processes and workflows to be automated using a combination of scripts, actions and triggers.

The use of Scripts requires a good understanding of their intended use, the triggers framework and action types. Non-performant scripts can slow down a Content Hub instance, particularly if they are triggered often on high traffic entities (e.g. on Asset modification), or if executed ‘in-process’.

The specific topics covered on this page are:

*   Understanding the Entity Model
    
*   SDK Knowledge
    
*   Error Handling and Debugging,
    
*   Performance and Scalability
    
*   Platform-Specific Constraints
    
*   Documentation and Testing
    

It is important to know and apply the above topics to maintaining Content Hub’s performance and usability.

The guide includes guidance on pre-scripting audits, data mapping for efficient automation, quality control practices, and post-implementation checks to ensure that scripts enhance content structure, searchability, and usability within Sitecore Content Hub. There are also code examples to illustrate common scripting use-cases.

The guide is aimed at Technical Implementers, but there will need to be feed-in from Solution Architects for wider integration topics, and from Product Owners/Business Stakeholders for business logic and data shaping.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) Execution

Implementation of scripts in Sitecore Content Hub requires care to ensure scripts are reliable, performant, and maintainable. Here we will discuss solutions and best practice on each of the points posed in the section above.

### 1\. Gain a deep understanding of the entity model

Sitecore Content Hub relies on a detailed entity model with interconnected asset types, metadata fields, and taxonomy structures. Understanding and correctly mapping this model is essential for effective scripting.

When building a script:

1.  Begin with an analysis of the entity model, including asset types, metadata structures, and taxonomies. Consider which of these your customisation may touch or could leverage.
    
2.  Establish a clear mapping of these relationships to guide scripting and avoid errors.
    
3.  Consider which custom schema or members are required in order to create desired script outputs.
    
4.  Document this model to provide a reliable reference that can be used across different scripts, reducing the risk of inconsistencies and errors.
    

For example, suppose you’re automating asset ingestion, and you need to assign each asset to the correct category, and to set metadata based on its type (e.g., image, video, document).

Before writing any script, document the entire asset structure and related entities — such as category taxonomies, required metadata fields, and parent-child relationships. This helps prevent misclassification and ensures the script respects Content Hub’s entity relationships, reducing the chance of improperly linking or tagging assets.

It is also important to feed back to the project stakeholders with this documentation, as often edge cases are identified. These can be more difficult to correct later, especially if a bulk data update is required.

### 2\. Learn and Leverage Sitecore’s SDKs

Sitecore Content Hub scripting requires knowledge of the Content Hub SDKs and data structures.

Developers must understand best practice to ensure scripts are efficient and performant, especially when the Content Hub instance contains large volumes of assets or metadata.

1.  Familiarise the development team with the Sitecore Content Hub Scripting SDK by utilising Sitecore’s comprehensive documentation to ensure scripts that interact with assets and metadata are both correct and optimised.
    
2.  Although this recipe is focused on Scripts, it is also important to understand the different phases of an entity update (Pre-commit, Validation, Security, Audit, Post) and the special Script types Metadata Processing and User events. Phases have different context variables available to support development.
    
3.  For advanced Content Hub developers; Shared scripts may be an option for re-using commonly used code.
    

### 3\. Implement error handling and logging

Errors may stem from various sources, including incorrect SDK calls, data mismatches, unintended changes in related content structures, or even changes to external services. It is important to implement error handling and logging processes within Scripts so any issues can be easily diagnosed and resolved.

1.  Incorporate robust error handling and detailed logging mechanisms within all scripts to improve troubleshooting exercises.
    
2.  By capturing specific errors and tracking data flow, teams can identify and address issues quickly. Implementing a structured approach to logging helps developers diagnose the root cause of issues, whether they stem from SDK errors, data mismatches, or platform constraints.
    
3.  Leverage the different log levels (debug, info, warning, error) to ensure you have full information when debugging is required or minimal resource use when in production.
    
4.  Scripts can be debugged using the ch-cli - see the documentation for full details.
    
5.  Use consistent naming, content and structure conventions, across your Script logs.
    
6.  It is important to be concise and to combine information into each log entry as only the last 50 messages can be viewed in the script logs.
    

### 4\. Optimise for performance and scalability

Scripts that manage extensive datasets or complex workflows can impact Content Hub’s performance. Ensuring scripts are optimised to handle complex processing tasks without slowing down the system is critical, particularly for high-traffic environments or content-heavy implementations.

In most cases, Scripts are not the correct approach for doing bulk updates, rather they are intended for atomic operations. See the Platform Constraints section for more details.

1.  Ensure you use the async versions of method calls where possible to reduce blocking calls on the system.
    
2.  Review Script Telemetry reports to see which scripts are using the most processing time or are called most often. These are candidates for optimisation.
    
3.  Testing script performance in a development or staging environment with sample datasets can identify potential performance issues before deployment, reducing the risk of system slowdowns when managing large content volumes.
    
4.  If a Script is performing bulk operations it may be more appropriate to use an External Implementation as Scripts are not intended to run for long periods.
    
5.  Use the correct Script Execution Type: There are two script execution types:
    
    1.  _In Process_ - executing a script in process interrupts the ongoing process until the script has finished. This type of script runs immediately and should only be used when immediate feedback is required from the user interface.
        
    2.  _In Background_ - this type of script runs behind the scenes, without causing interruption. For performance reasons, always favour this script execution type.
        

### 5\. Work within platform constraints

Sitecore Content Hub enforces limitations on internal Scripts, such as execution limits, permissions and excluded libraries. External API calls are not possible.

1.  Avoid doing bulk or long running operations with scripts. Sitecore recommend keeping the total script run time below 10 minutes. After 12 minutes, the script worker considers the script stuck and starts a new worker to relaunch it. This can cause resource starvation and data inconsistency.
    
2.  It is not possible to make REST API calls from a script. If this is required then another approach than Scripting should be used, e.g. via an via a connector or an external integration.
    
3.  Understand which libraries and attributes are restricted when building your solution. Certain .NET libraries are not allowed to be used in a script. See the documentation for full details.
    

### 6\. Documentation and version control

Scripting documentation must be thorough and accessible, as each script may need to be updated or repurposed over time.

1.  Define a naming convention for Scripts. Try and include which schema are source and targets in the name (e.g. \<Company Name\> - Asset copy Campaign data to related Campaign Entity). Benefits include:
    
    1.  Easier searching and finding of Scripts within the Manage → Scripts page.
        
    2.  Easier trail on which Trigger Events on which Entities are calling which Scripts.
        
2.  Document each script’s purpose, affected entities, and dependencies to simplify future maintenance and updates. Document details of the script’s triggers as appropriate.
    
3.  Using a version control system allows tracking of script changes over time, making it easier to roll-back if needed and to manage script updates in line with platform changes or new requirements.
    
4.  This version control could be extended into an automated deployment process using the Content Hub CLI (see documentation below) or other API hooks.
    

### 7\. Testing and Monitoring

Comprehensive testing is essential to ensure scripts perform consistently and adapt to Content Hub updates or changes in organisational requirements.

1.  Implement a rigorous testing process that simulates real-world conditions, ensuring scripts work as expected under various scenarios.
    
2.  Regular monitoring and audits of script performance post-deployment allow teams to detect issues proactively and optimise scripts as the data model and system demands evolve.
    
3.  Build a suite of ideally automated (UI and / or API driven) tests to cover as many scenarios as possible. We recommend running these on a daily basis on a development environment to detect problems early.
    
4.  Regular testing is particularly important when developing new scripts - as these may conflict or have unintended effects. E.g. if the script triggers a save on a related entity, more scripts may be triggered that run on modification of that entity.
    
5.  Consider the impacts from scripts being triggered when entities or members are created, updated or deleted. It may make sense to consolidate multiple scripts into a single script (for performance reasons as well as avoiding race conditions or unexpected results)
    
6.  Due to Content Hub’s powerful extensibility, the platform likely be having new metadata or schema / schema relations added in order to meet new custom business requirements. An automated suite of tests will be a necessity in this case.
    

By following these solutions, teams can build and maintain scripts that enhance Sitecore Content Hub’s functionality, streamline asset and metadata management, and ensure content integrity, usability, and efficiency in the long term.

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) Insights

This section contains code examples to demonstrate some common use-cases for Content Hub scripting. Note that most scripts depend on custom schema in order to do anything meaningful, so they may require some changes to your Content Hub schema.

The examples are designed to show the classes, methods, parameters and general approaches taken to solve a development challenge, rather than to be end-to-end solutions. Please refer to the Content Hub Scripting documentation for full details of the Scripting APIs and further examples.

When experimenting with these, always do so in a sandbox or development environment.

The provided code is intended as a guideline and must be tailored to suit your specific implementation requirements. Please ensure thorough end-to-end testing is conducted to validate its functionality and performance in your environment.

### Add user to default UserGroup on first login

| Script Type | Trigger Event |
| --- | --- |
| User post-registration | User post-registration |

```csharp

using System.Linq;

if (Context.ExternalUserInfo?.Provider != "SAML") 
{
    MClient.Logger.Info("Provider is not SAML");
    return;
}

var query = Query.CreateQuery(entities =>
    from e in entities
    where e.DefinitionName == "Usergroup" && e.Property("GroupName") == "FS.UserType.SSODefault"
    select e);

var defaultGroupId = await MClient.Querying.SingleIdAsync(query);
if (!defaultGroupId.HasValue) throw new InvalidOperationException("Default usergroup for SSO login not found.");

var relation = await Context.User.GetRelationAsync<IChildToManyParentsRelation>("UserGroupToUser");
relation.Parents.Add(defaultGroupId.Value);
MClient.Logger.Info("User added to UserGroup");
await MClient.Entities.SaveAsync(Context.User);
```

### Create Public Links for X Assets

| Script Type | Trigger Event |
| --- | --- |
| Action Script | Asset Change |

```csharp

using System.Linq;
using System.Threading.Tasks;

const string Constants_Rendition_Resource = "web_png_1_1";

var assetId = Context.TargetId;

// Check if public links don't exist yet
var query = Query.CreateQuery(entities => from e in entities
                                            where e.DefinitionName == "M.PublicLink"
                                            && e.Parent("AssetToPublicLink") == assetId.Value
                                            && e.Property("Resource") == Constants_Rendition_Resource
                                            && e.Property("IsDisabled") == false
                                            select e);
query.Take = 0;

var result = await MClient.Querying.QueryIdsAsync(query);
if (result.TotalNumberOfResults > 0)
{
    MClient.Logger.Info($"Public links already exist for asset with id '{assetId}' of resource '{Constants_Rendition_Resource}'");
    return;
}

// Create public links
await CreateForRendition(Constants_Rendition_Resource, assetId.Value);
MClient.Logger.Info($"Created public link for asset with id '{assetId}' of resource '{Constants_Rendition_Resource}'");

async Task CreateForRendition(string rendition, long assetId)
{
    var publicLink = await MClient.EntityFactory.CreateAsync("M.PublicLink");

    if (publicLink.CanDoLazyLoading())
    {
        await publicLink.LoadMembersAsync(new PropertyLoadOption("Resource"), new RelationLoadOption("AssetToPublicLink"));
    }

    publicLink.SetPropertyValue("Resource", rendition);

    var relation = publicLink.GetRelation<IChildToManyParentsRelation>("AssetToPublicLink");
    if (relation == null)
    {
        MClient.Logger.Error("Unable to create public link: no AssetToPublicLink relation found.");
        return;
    }

    relation.Parents.Add(assetId);

    await MClient.Entities.SaveAsync(publicLink);
    return;
}
```

### Impersonate another user

```csharp

long userId = 123;

var userEntity = await MClient.Entities.GetAsync(userId).ConfigureAwait(false);
if (userEntity == null)
{
    MClient.Logger.Info($"{executionIdentifier}: user not found");
    return;
}

var userName = userEntity.GetPropertyValue<string>("UserName");
userMClient = await MClient.ImpersonateAsync(userName).ConfigureAwait(false);

var approvedEntityIds = await GetApprovedEntityIds().ConfigureAwait(false);

foreach (var approvedEntityId in approvedEntityIds)
{
    await userMClient.Entities.DeleteAsync(approvedEntityId).ConfigureAwait(false);
}
```

#### Notes

1.  You use the user's username in the call for impersonation.
    
2.  This returns a new MClient which you can then use to perform operations on that user's behalf.
    
3.  You cannot impersonate the administator user.
    

You can find more on Impersonation in the documentation.

### SSO role mapping

| Script Type | Trigger Event |
| --- | --- |
| User sign-in | User sign-in |

See the Functional Security SSO recipe for an in-depth walkthrough.

### Metadata processing - capture photo meta data

This script captures the photo taken date and saves it to a custom property

```csharp

using System.Linq;
using System.Net;
using Newtonsoft.Json.Linq;
using System.Globalization;

MClient.Logger.Debug($"Script - Metadata Processing - TMR_DateTaken");

var enUsCulture = CultureInfo.GetCultureInfo("en-US");

var hasChanged = false;

var entityLoadConfig = new EntityLoadConfiguration(new CultureLoadOption(enUsCulture), new PropertyLoadOption("TMR_DateTaken"), RelationLoadOption.None);
var asset = await MClient.Entities.GetAsync(Context.Asset.Id.Value,entityLoadConfig).ConfigureAwait(false);

if(Context.MetadataProperties.ContainsKey("EXIF:CreateDate"))
{
    MClient.Logger.Debug($"EXIF:CreateDate={Context.MetadataProperties["EXIF:CreateDate"]}");

    if(Context.MetadataProperties["EXIF:CreateDate"] == null)
    {
        MClient.Logger.Debug($"Context.MetadataProperties[\"EXIF:CreateDate\"] is null, skip");
        return;
    }

    DateTime dateTaken;

    // System.Globalization.DateTimeStyles is prohibited in CH scripting. Have to split the data time string to construct a parsable datetime string with specifing the DateTimeStyles.

    var rawDateTake = Context.MetadataProperties["EXIF:CreateDate"].ToString();

    // Converting string format from "yyyy:MM:dd" to yyyy-MM-dd
    if(rawDateTake.Length < "yyyy:MM:dd".Length)
    {
        MClient.Logger.Error($"Failed to tranform the datetime string format rawDateTake={rawDateTake}");
        return;
    }

    rawDateTake = rawDateTake.Remove(4, 1).Insert(4, "-").Remove(7, 1).Insert(7, "-");

    //if (!DateTime.TryParseExact(Context.MetadataProperties["EXIF:CreateDate"].ToString(), "yyyy:MM:dd HH:mm:ss", enUsCulture, DateTimeStyles.None, out dateTaken))
    if (!DateTime.TryParse(rawDateTake, out dateTaken))
    {
        MClient.Logger.Error($"Failed to parse rawDateTake={rawDateTake}");
        return;
    }

    MClient.Logger.Debug($"dateTaken={dateTaken}");

    asset.SetPropertyValue("TMR_DateTaken", dateTaken);

    hasChanged = true;
}

if(hasChanged)
{
    await MClient.Entities.SaveAsync(asset);
}
```

### Keep a tally of related entity updates (Asset to Product)

This script shows how to capture audit information, look up related entities and store values. The ‘Product’ entity used here is a custom entity definition.

| Script Type | Trigger Event |
| --- | --- |
| Action Script | When Product.ProductToAsset or Asset.ProductToAsset has changed, or PreCommit on Asset.ProductToAsset |

```csharp

using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;

const string PropertyBagNameAffectedArticleIDs = "affectedArticleIDs";
const string PropertyBagNameAffectedProductIDs = "affectedProductIDs";

var entityId = Context.TargetId;
MClient.Logger.Info($"Script - Update Product Assets Count - Started - {Context.ExecutionEvent}");
MClient.Logger.Info($"Context - {Context}");

var entity = await MClient.Entities.GetAsync(entityId.Value).ConfigureAwait(false);

if(entity == null)
{
    MClient.Logger.Warn($"Cannot get entity by id: {entityId}. The entity may have been deleted.");
    return;
}

if(Context.ExecutionEvent == ExecutionEvent.OnSave)
{
    if(entity.DefinitionName == "M.Asset")
    {
        var productRelationChange = Context.ChangeTracker.GetChangeSet().RelationChanges.FirstOrDefault(i => i.Name == "FS.ProductToAsset");

        if(productRelationChange != null)
        {
            var removedProductIDs = productRelationChange.RemovedValues;
            MClient.Logger.Info($"removed Product IDs:{string.Join("|", removedProductIDs)}");
            Context.PropertyBag.Add(PropertyBagNameAffectedProductIDs, string.Join("|", removedProductIDs));
        }
    }

    return;
}

if(entity.DefinitionName == "M.Asset")
{
    MClient.Logger.Info($"Triggered by Asset change");

    if (entity.CanDoLazyLoading())
    {
        await entity.LoadMembersAsync(null, new RelationLoadOption("FS.ProductToAsset"));
    }

    var productRelation = entity.GetRelation<IChildToManyParentsRelation>("FS.ProductToAsset");

    MClient.Logger.Info($"Products Count:{productRelation.Parents.Count}");

    var affectedProductIDs = "";
    Context.PropertyBag.TryGetValue(PropertyBagNameAffectedProductIDs, out affectedProductIDs);

    var productLoadConfiguration = new EntityLoadConfiguration(
        CultureLoadOption.None,
        new PropertyLoadOption("FS.AssetsCount"), 
        new RelationLoadOption("FS.ProductToAsset"));

    if(!string.IsNullOrEmpty(affectedProductIDs))
    {
        foreach(var productIdRaw in affectedProductIDs.Split("|"))
        {
            long productId;
            if(!long.TryParse(productIdRaw, out productId))
            {
                MClient.Logger.Error($"Can not parse product id: {productIdRaw}");
            }

            IEntity productEntity = await MClient.Entities.GetAsync(productId, productLoadConfiguration);

            var assetRelation = productEntity.GetRelation<IParentToManyChildrenRelation>("FS.ProductToAsset");

            var assetsCount = assetRelation.Children.Count;

            MClient.Logger.Info($"assetsCount:{assetsCount}");

            productEntity.SetPropertyValue("FS.AssetsCount", assetsCount);

            await MClient.Entities.SaveAsync(productEntity);
        }
    }
    {
        foreach(var productId in productRelation.Parents)
        {
            IEntity productEntity = await MClient.Entities.GetAsync(productId, productLoadConfiguration);

            var assetRelation = productEntity.GetRelation<IParentToManyChildrenRelation>("FS.ProductToAsset");

            var assetsCount = assetRelation.Children.Count;

            MClient.Logger.Info($"assetsCount:{assetsCount}");

            productEntity.SetPropertyValue("FS.AssetsCount", assetsCount);

            await MClient.Entities.SaveAsync(productEntity);
        }
    }


    MClient.Logger.Info($"Affected Product IDs:{affectedProductIDs}");
}
else if(entity.DefinitionName == "FS.Product")
{
    MClient.Logger.Info($"Triggered by FS.Product change");

    var productEntity = entity;

    if (entity.CanDoLazyLoading())
    {
        await productEntity.LoadMembersAsync(new PropertyLoadOption("FS.AssetsCount"), new RelationLoadOption("FS.ProductToAsset"));
    }

    var assetRelation = productEntity.GetRelation<IParentToManyChildrenRelation>("FS.ProductToAsset");

    var assetsCount = assetRelation.Children.Count;

    MClient.Logger.Info($"assetsCount:{assetsCount}");

    productEntity.SetPropertyValue("FS.AssetsCount", assetsCount);

    await MClient.Entities.SaveAsync(productEntity);
}
else
{
    MClient.Logger.Info($"Triggered by {entity.DefinitionName} chnage, ignore");
}
```

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Recipes

|     |     |
| --- | --- |
|     | **Recipe** |
| 1   | [Triggers and Actions](Triggers-and-Actions) |
| 2   | [CH Discovery](discovery) |
| 3   | [CH External Integrations](external-integrations) |
| 4   | [SSO and Auto-Assignment](SSO-and-Auto-Assignment) |
| 5   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Documentation

|     |     |
| --- | --- |
|     | **Documentation Link** |
| 1   | Learn and Leverage [https://doc.sitecore.com/ch/en/developers/cloud-dev/scripts.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/scripts.html) [https://doc.sitecore.com/ch/en/developers/cloud-dev/script-types.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/script-types.html) |
| 2   | Work Within Restraints[https://doc.sitecore.com/ch/en/developers/cloud-dev/script-restriction.html#permitted-libraries](https://doc.sitecore.com/ch/en/developers/cloud-dev/script-restriction.html#permitted-libraries) |
| 3   | Debugging scripts in Visual Studio Code [https://doc.sitecore.com/ch/en/developers/cloud-dev/debug-a-script-using-visual-studio-code.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/debug-a-script-using-visual-studio-code.html) |
| 4   | Documentation and version control[https://doc.sitecore.com/ch/en/developers/cloud-dev/content-hub-command-line-interface--cli-.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/content-hub-command-line-interface--cli-.html) |
| 5   | Impersonating another user [https://doc.sitecore.com/ch/en/developers/cloud-dev/impersonation.html](https://doc.sitecore.com/ch/en/developers/cloud-dev/impersonation.html) |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |