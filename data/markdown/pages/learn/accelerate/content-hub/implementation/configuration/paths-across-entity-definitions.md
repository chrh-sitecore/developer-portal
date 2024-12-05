---
title: 'Paths across entity definitions'
description: 'Setting up a path across entity definitions to show hierarchy'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-11-28'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Configuration > Schema Management'
author: 'Xavier Cobbaert'
audience: 'Solution Architects, Technical Implementers'
---
|     |     |
| --- | --- |
| **Recipe name** | Paths across entity definitions |
| **Description** | Setting up a path across entity definitions to show hierarchy |
| **Reference Audience** | Solution Architects, Technical Implementers |
| **Jira #** | RCPS-1 - Path, Path relation and hiearchy Ready for deployment |
| **Project Step** | Implementation |
| **Chapter** | Configuration |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) **Context**

Path between entities is an important concept in Content Hub who allow to get additional information between entities in the same path. It is commonly misunderstood and demands a thorough and deliberate approach.

When an entity has many related paths, it requires loading a lot of entities, which might impact performance. Only configure Path Enabled Definition and Related Path when you have a real use case.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) **Execution**

A path is a trail showing a hierarchical structure between entities. The path contains the name and the ID of the different entities linked together. Paths are used to visualise the hierarchy of entities linked and to show on entity details page information from parent entities included in the path.

Examples of paths include -

1.  Asset Type: Taxonomies with different levels are shown on Asset details page.
    

![Screenshot 2024-11-12 at 07.51.04.png](/images/learn/accelerate/content-hub/attachments/5413011570/5414092907.png?width=540)

2.  Product path on Asset: The path includes Brand, Product Family and Product and is shown Assets Search page
    

![Screenshot 2024-11-12 at 07.55.39.png](/images/learn/accelerate/content-hub/attachments/5413011570/5414027420.png?width=760)

Path configuration in Content Hub can be configured by using the following options:

### **1\. On the Entity definition:**

**Path enable definition** toggled on Indicates that entities of this definition can be part of entity paths. This option can be activate when you edit a definition.

![Screenshot 2024-11-12 at 08.00.21.png](/images/learn/accelerate/content-hub/attachments/5413011570/5414551553.png?width=547)

### **2\. On the Relation**

*   **Path relation:** Creates a related path on the child side of the relation. This allows for path expansion of hierarchical entities on a related entity.
    
*   **Is path hierarchy relation:** Connects path-enabled definitions and hierarchical definitions, creating a path hierarchy. This is usually set on self-relations.
    
*   **Path hierarchy score**: If an entity has multiple parent relations, the paths of that entity are sorted in ascending order according to this score. Unless marked otherwise, the first path is used as a sole entity path when only one is required.
    

![Screenshot 2024-11-12 at 08.01.33.png](/images/learn/accelerate/content-hub/attachments/5413011570/5414125659.png?width=760)

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) **Insights**

When an entity has many related paths, it requires loading a lot of entities, which might slightly impact performance. Related paths are computed on the fly, thus their impact on the storage is minimal. Paths however, are computed and stored in the main meta–data storage for all the path–enabled entities. The computation of the paths happens during the search indexing process of the entities.

Paths themselves have a non trivial, though small, memory footprint. This does not normally cause issues. However, paths (not related paths) should not be enabled and are not intended for entity definitions with significant amounts of entities (for example, _M.Asset_ or _M.File_). You can consider the paths as a form of data classification which sorts large groups of data rather than classifying every single piece of data.

Computation of the paths for large pools of entities has an [impact on system performance](https://doc.sitecore.com/ch/en/developers/cloud-dev/related-paths-properties.html#performance-and-storage-impact) and uses a large portion of the main metadata storage.

Only configure Path Enabled Definition and Related Path when you have a real use case need. For example, by default when a taxonomy relation is created, Path relation is set to True and it is not always needed, in particular for taxonomy with only one level of data. (non hierarchical taxonomy).

### Use Cases Approach

#### Multi-level taxonomy example Geography

This use case shows how to build a Path between the Geography hierarchy and assets. In our example, Geography hierarchy is a taxonomy with a self relation to link Geography to Geography.

The configuration on the schema should include -

*   Geography has Path Enabled Definition equal to True to be part of the entity path.
    
*   Relation Geography - Asset has Path relation equal to True as we want to create a related path on the child side of the relation and Is Path Hierarchy relation equal to False as there is no need to have Asset entities in the path.
    

By default,

*   when a taxonomy is created Path enabled definition is set to True
    
*   when a taxonomy relation is created Path relation is set to True and Path hierarchy is set to False
    

How to use it:

*   Show Path information in search component, entity details component.
    

![Screenshot 2024-11-12 at 20.35.16.png](/images/learn/accelerate/content-hub/attachments/5413011570/5417336961.png?width=534)

#### Path Across multiple entity definition example Product Hierarchy

This use case shows how to build a Path between the product hierarchy and assets. In our example, product hierarchy is composed of Brand, Product Family and Product.

As shown in the following diagram, Brand is parent of Product Family, Product Family is parent of Product and Product is parent of Asset.

This objective is to be able to show this path on the asset level and to show information from Brand, Product Family and Product on the Asset Details page.

The configuration on the schema should include -

*   Brand, Product Family and Product have Path Enabled Definition equal to True to be part of the entity path.
    
*   Relations Brand - Product Family and Product Family - Product have Path relation equal to False as there is no need to create a related path on the child side of the relation and Is Path Hierarchy relation equal to True to connect Path enabled entity definition.
    
*   Relation Product - Asset has Path relation equal to True as we want to create a related path on the child side of the relation and Is Path Hierarchy relation equal to False as there is no need to have Asset entities in the path.
    

The following diagram shows the schema configuration required to have a Product Hierarchy path on the assets.

How to use it:

In the examples below, the Brand is “_Fruitful_”, Product Family is “_Fruitful Lemonade_” and Product is “_Fruitful Lemonade_ \- _Lime_”

Path can be used for:

1.  Show Path information in search component, entity details component.
    

![](attachments/5413011570/5415993498.png?width=760)

2.  Show detailed information from Parent entities (Brand, Product Family and Product) on Asset details page by using an entity details component. Screenshots below show the result and the configuration to have Product Family information available on Asset Details page.
    

![Screenshot 2024-11-12 at 19.57.05.png](/images/learn/accelerate/content-hub/attachments/5413011570/5416255686.png?width=760)![Screenshot 2024-11-12 at 19.56.03.png](/images/learn/accelerate/content-hub/attachments/5413011570/5416190132.png?width=656)

3.  Use the Path in integration to retrieve in one call parents entities included in the path.
    

See an example of API call on an Asset entity showing the path from Brand to Product linked to the Asset.

![Screenshot 2024-11-12 at 18.56.27.png](/images/learn/accelerate/content-hub/attachments/5413011570/5417140380.png?width=760)

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
| 1   | [https://doc.sitecore.com/ch/en/users/content-hub/edit-an-entity-definition.html](https://doc.sitecore.com/ch/en/users/content-hub/edit-an-entity-definition.html) |
| 2   | [https://doc.sitecore.com/ch/en/users/content-hub/create-a-member.html](https://doc.sitecore.com/ch/en/users/content-hub/create-a-member.html) |
| 3   | [https://doc.sitecore.com/ch/en/developers/cloud-dev/related-paths-properties.html#performance-and-storage-impact](https://doc.sitecore.com/ch/en/developers/cloud-dev/related-paths-properties.html#performance-and-storage-impact) |
| 4   | [https://doc.sitecore.com/ch/en/users/content-hub/entity-detail-settings.html](https://doc.sitecore.com/ch/en/users/content-hub/entity-detail-settings.html) |
| 5   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |