---
title: 'Multi Environments Deployment'
description: 'Managing deployments with multiple Content Hub Environments'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-12-04'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Pre-Development'
author: 'Dan Vella'
audience: 'Technical Implementers (Developers), Solution Architects, System Administrators/IT Ops'
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) **Context**

Content Hub implementations are often developed independently of the production instance, allowing for a separated space to build, configure and test any changes before releasing.

It is easy to deploy configurations between these environments using the export/import functionality, but what about environment specific variables? For example, imagine you have a service that should be notified every time an asset is created. You have an instance of this service for each Content Hub environment (dev, staging, production), located at \{env\}.assetservice.com. So, you create a trigger and an action in your dev environment to call the dev service at [dev.assetservice.com](http://dev.assetservice.nickyvadera.com).

When you deploy this to staging using the Import/Export functionality, the action will be created (or updated if it already exists), but the URL will be pointing to dev. It would be cumbersome to remember to update this after every deployment, and it becomes unsustainable once you have multiple actions (or other variables) and environments. You need a better solution.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) **Execution**

There are two ways in which this problem can be solved. Out of the box, or custom.

### Out of the Box

The out of the box solution is only relevant where the environment specific value you want to update is the URL of an action. For any other value, for example, the source url of an external component, the configuration of an external component, or related entity ids only the custom solution can be used.

The out of the box solution is detailed here [Action types | Sitecore Documentation](https://doc.sitecore.com/ch/en/developers/cloud-dev/action-types.html#api-call) and allows an action’s URL to be resolved from an environment specific setting.

### Custom

The custom solution involves using a script that executes after performing a package import to update relevant entities with any environment-specific data—in this case, the API URL of an action, but it's worth noting that this can be applied to any entity, such as external components. We are therefore going to need:

*   Somewhere to store the environment specific values
    
*   A script to orchestrate the changes
    
*   A method to execute the script
    

#### 1\. Create an Environment-specific Setting

First, you need to create a place to store the environment-specific values. To do this, create an environment specific setting for each environment, ensuring that they are all created with the same identifier — I'm going to name it `environment-specific-settings`. The value of this setting on dev could look something like this:

environment-specific-settings.json

```csharp

{
    "Actions": [
        {
            "identifier": "M.Action.AssetServiceAction",
            "apiUrl": "dev.assetservice.com",
        }
    ]
}

```

And on staging:

environment-specific-settings.json

```csharp

{
    "Actions": [
        {
            "identifier": "M.Action.AssetServiceAction",
            "apiUrl": "staging.assetservice.com",
        }
    ]
}

```

And on production:

environment-specific-settings.json

```csharp

{
    "Actions": [
        {
            "identifier": "M.Action.AssetServiceAction",
            "apiUrl": "assetservice.com",
        }
    ]
}

```

"M.Action.AssetServiceAction" in this case is the identifier of the target action - you will need to replace it with the identifier of your specific action (and perhaps add some additional ones too). This now means that whenever you want to find the correct URL of an action for the current environment, you can look up the value of `Actions` in the `environment-specific-settings` setting and find the `apiUrl` of the object whose `identifier` matches your action.

#### 2\. Create A Post-Deployment Script

Now, create a script that updates the `apiUrl` value to the correct environment-specific value of any actions defined in your setting. To do this go to **Manage \> Scripts \> + New Script**. Give the script a meaningful name, I'll call mine `CH Post Deployment`, set the type to `action` and click **Create**.

The contents of the script should be:

post-deployment.csx

```csharp

using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

const string ENVIRONMENT_SPECIFIC_SETTINGS_IDENTIFIER = "SETTING_IDENTIFIER";

const string SETTING_PROPERTY_VALUE = "M.Setting.Value";
const string SETTING_PROPERTY_VALUE_ACTIONS = "Actions";

const string ACTION_PROPERTY_SETTINGS = "Settings";
const string ACTION_PROPERTY_SETTINGS_APIURL = "apiUrl";

MClient.Logger.Info("Start Post Deployment script");

var entityLoadConfiguration = new EntityLoadConfiguration()
{
    PropertyLoadOption = new PropertyLoadOption(SETTING_PROPERTY_VALUE),
    RelationLoadOption = RelationLoadOption.None,
    CultureLoadOption = CultureLoadOption.Default
};

var environmentSpecificSettings = await MClient.Entities.GetAsync(ENVIRONMENT_SPECIFIC_SETTINGS_IDENTIFIER, entityLoadConfiguration);
var environmentSpecificSettingsValue = environmentSpecificSettings.GetPropertyValue<JToken>(SETTING_PROPERTY_VALUE);

var actionConfigs = environmentSpecificSettingsValue?.Value<JArray>(SETTING_PROPERTY_VALUE_ACTIONS)?.ToObject<List<ActionConfig>>();

await UpdateActions(actionConfigs);

MClient.Logger.Info("Completed Post Deployment script");

#region Functions
async Task UpdateActions(List<ActionConfig> actionConfigs)
{
    var actionLoadConfig = new EntityLoadConfiguration
    {
        PropertyLoadOption = new PropertyLoadOption(ACTION_PROPERTY_SETTINGS),
        RelationLoadOption = RelationLoadOption.None,
        CultureLoadOption = CultureLoadOption.None
    };

    foreach (var actionConfig in actionConfigs)
    {
        var actionEntity = await MClient.Entities.GetAsync(actionConfig.Identifier, actionLoadConfig);
        if (actionEntity == null)
        {
            MClient.Logger.Warn($"Action with identifier {actionEntity} not found. Please update the environment specific settings!");
            continue;
        }

        var dirty = false;
        var settings = actionEntity.GetPropertyValue<JToken>(ACTION_PROPERTY_SETTINGS);

        if (!String.IsNullOrEmpty(actionConfig.ApiUrl) && settings[ACTION_PROPERTY_SETTINGS_APIURL].ToString() != actionConfig.ApiUrl)
        {
            settings[ACTION_PROPERTY_SETTINGS_APIURL] = actionConfig.ApiUrl;
            dirty = true;
        }

        if (dirty)
        {
            await MClient.Entities.SaveAsync(actionEntity);
            MClient.Logger.Info($"The action {actionConfig.Identifier} has been updated");
        }
        else
            MClient.Logger.Info($"The action {actionConfig.Identifier} didn't require any update");
    }
}
#endregion

#region Classes
class ActionConfig
{
    public string Identifier { get; set; }
    public string ApiUrl { get; set; }
}
#endregion

```

You should update line 7 with the identifier of the setting you created earlier which stores the environment specific values. Save the changes, build the script and then click the Publish button. Once this is done, don't forget to go back to **Manage \> Scripts** and enable the new script.

At this point the solution is functionally complete. We can execute our script by creating a HTTP POST request to it's execution URL - this can be found by clicking **... \> Share Url**. But let's take this one step further and improve the user experience.

#### 3\. Create A Post-Deployment Page

Now, create a 'post deployment' page, that contains a button that executes the post deployment script. This way, whenever you want to run the script, you can use this page, rather than having to call the api.

The first thing you need for this is an external component that will render the button and orchestrate calling the script execution URL on click.

execute-script-button.jsx

```csharp

import ReactDOM from "react-dom";
import React from "react";
import Button from '@mui/material/Button';

export default function createExternalRoot (container) {
    const clickHandler = async (url, api, client) => {
        const response = await client.raw.postAsync(url, {})
        if(response.isSuccessStatusCode)
            api.notifier.notifySuccess("Script executed successfully")
        else
            api.notifier.notifyError("Script execution failed. Please check the logs")
    }

    return {
        render(context) {
            const scriptIdentifier = 'ob4BjmOK50yEKaQXSOvy6A';
            const url = `/api/scripts/${scriptIdentifier}/execute`

            ReactDOM.render(
                <Button
                    variant="contained"
                    disableElevation
                    theme={context.theme}
                    onClick={() => clickHandler(url, context.api, context.client)}
                >
                    Execute Post Deployment Script
                </Button>,
                container
            );
        },
        unmount() {
            ReactDOM.unmountComponentAtNode(container);
        }
    }
}

```

You could of course move the script identifier the configuration to make this component more reusable, but I've not done that here for simplicity.

Line 16 will need to be updated to the identifier of the script you just created. We can now create a new page, perhaps called "Post Deployment" and add the external component to it.

#### Putting it all together

Now, after you export a deployment package from one environment and import it to another, you can simply go to the post-deployment page, click the button, and all your relevant environment-specific variables will be updated.

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) **Insights**

There is one downside to the above approach - that every time you want to add a new variable (e.g. because you've added a new action) you will need to go and update the environment specific setting on each environment manually (it can't be done via a package of course, because that's the whole purpose of the environment specific settings in the first place!).

To remedy this you could instead update your `environment-specific-settings` setting value to only store the id of your environment. For example, on dev it would be:

environment-specific-settings.json

```csharp

{
    "EnvironmentId": "dev"
}

```

You should then create a new standard setting (I.E. not environment specific), which stores all the values for all the environments by id. Something like this:

settings-by-environment.json

```csharp

{
    "dev": {
        "Actions": [
            {
                "identifier": "M.Action.AssetServiceAction",
                "apiUrl": "dev.assetservice.com",
            }
        ]
    },
    "staging": {
        "Actions": [
            {
                "identifier": "M.Action.AssetServiceAction",
                "apiUrl": "staging.assetservice.com",
            }
        ]
    },
    "production": {
        "Actions": [
            {
                "identifier": "M.Action.AssetServiceAction",
                "apiUrl": "assetservice.com",
            }
        ]
    }
}

```

The script would of course need to be updated to first read the `environmentId` value from the `environment-specific-settings` setting, and then use that value to find the relevant object in the `settings-by-environment` setting. This approach does mean that each environment contains all the settings for all the other environments, but has the advantage that they can modified and created on dev and then deployed via the standard deployment packages.

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Recipes

|     |     |
| --- | --- |
|     | **Recipe** |
| 1   | [CH External Components Guidance and Scenarios](external-components-guidance-and-scenarios) |
| 2   | Future recipe on environment specific settings |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Documentation

|     |     |
| --- | --- |
|     | **Documentation Link** |
| 1   | [Importing and exporting packages \| Sitecore Documentation](https://doc.sitecore.com/ch/en/users/content-hub/importing-and-exporting-packages.html) |
| 2   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |