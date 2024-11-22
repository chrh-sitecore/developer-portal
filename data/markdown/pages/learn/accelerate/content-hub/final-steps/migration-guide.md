---
title: 'Migration Guide'
description: 'This Content Migration guide outlines the steps needed to move content assets, metadata, and related data from legacy systems into Sitecore Content Hub. The process includes pre-migration auditing, data mapping, quality control measures, and post-migration verification to ensure all content retains its structure, searchability, and usability in the new environment.'
hasSubPageNav: false
hasInPageNav: false
area: ['accelerate']
lastUpdated: '2024-11-17'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Final Steps'
author: 'Korina Huizar'
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) Problem

Migrating content into Sitecore Content Hub is a multi-step process that involves moving vast amounts of data, assets, metadata, and associated workflows from legacy systems. This migration must be handled meticulously to avoid data loss, ensure data integrity, and maintain the intended structure and functionality of digital assets. Inadequate planning or testing can lead to significant challenges, including data corruption, metadata mismatches, broken links, and user access issues. This is intended to provide a structured approach to ensure a seamless migration process, minimize risk, and align the migrated data and assets with the new Sitecore Content Hub environment. The objective is to ensure that all assets are successfully transferred, retain their original structure and metadata, and function as expected within Sitecore Content Hub, facilitating a smooth transition for users and supporting organizational goals.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) Solution

This guide offers a step-by-step framework for planning, executing, and validating content migration to ensure that assets, metadata, permissions, and workflows are transferred accurately and are fully operational within Content Hub. This recipe provides guidelines on pre-migration preparation, data mapping, migration execution, and post-migration testing. By following this structured approach, the guide aims to reduce errors, streamline migration tasks, and maintain the quality and organization of content as it moves into the Content Hub environment. This migration guide also provides best practices to address any unforeseen issues during migration and establish procedures for ongoing data integrity and optimization post-migration.

|     |     |     |
| --- | --- | --- |
|     | **Topic** | **Checklist** |
| 1   | Pre-Migration Assessment and Planning | 1.  Conduct an inventory of assets, metadata, and associated workflows in the legacy system. Outline existing data sources, file types, how many files and versions, how much data in total?\<br\>    \<br\>2.  Determine which data will be brought to which environment. For examples many customer’s have a DEV and QA environment with a smaller subset of data in comparison to their production environments.\<br\>    \<br\>3.  Define migration scope, including data to be migrated and any non-essential items to be archived.\<br\>    \<br\>4.  Assess data quality and cleanliness in the legacy system to avoid migrating unnecessary or corrupted data.\<br\>    \<br\>5.  Identify source file location that includes individual file direct links. All files to be migrated need to be accessible via a direct link. Example: Azure Blob Storage\<br\>    \<br\>6.  Clean up assets that don’t need to be migrated.\<br\>    \<br\>7.  Collect all file metadata. This should be collected in a spreadsheet that includes all filenames, direct links and any other metadata that should be migrated to Content Hub​\<br\>    \<br\>8.  Establish a migration timeline, including key milestones and roles for the migration team. |
| 2   | Data Mapping and Metadata Structure | 1.  Develop a data mapping plan to align legacy metadata fields with Sitecore Content Hub metadata fields.\<br\>    \<br\>2.  Define data transformation requirements (e.g., format conversions, taxonomy updates).\<br\>    \<br\>3.  Validate that metadata and tags are mapped accurately to support search functionality and asset discoverability.\<br\>    \<br\>4.  Separators for multi-valued attributes​ i.e. EU \| USA \| APAK\<br\>    \<br\>5.  List of values mapping to correct value​\<br\>    \<br\>6.  Hierarchies including full path​ |
| 3   | Content Migration Execution | 1.  Select the appropriate migration tools or scripts, considering the complexity and volume of content. Many use an excel file as the source of legacy data and leverage Content Hub import/export functionality. Red more below [https://sitecore.atlassian.net/wiki/spaces/FM1/pages/edit-v2/5381128233?draftShareId=fa0d6578-10be-46ff-ae7a-9b566d6c6a3d#Using-Excel](https://sitecore.atlassian.net/wiki/spaces/FM1/pages/edit-v2/5381128233?draftShareId=fa0d6578-10be-46ff-ae7a-9b566d6c6a3d#Using-Excel)\<br\>    \<br\>2.  Set up a migration environment to test data transfer in a controlled setting.\<br\>    \<br\>3.  Ensure that permissions and role-based access control (RBAC) settings are properly configured in Sitecore Content Hub.\<br\>    \<br\>4.  Conduct batch migration tests to identify and address potential issues before the full migration.\<br\>    \<br\>5.  Monitor timing in QA to be able to provide estimated duration for final migration on Prod​ |
| 4   | Migration Testing and Quality Assurance | 1.  Implement quality assurance checks to validate data accuracy, completeness, and integrity post-migration.\<br\>    \<br\>2.  Test content accessibility, metadata accuracy, and file functionality (e.g., preview, download).\<br\>    \<br\>3.  Verify user roles and permissions to ensure only authorized personnel have access to relevant content. |
| 5   | Workflow Validation and Asset Usability | 1.  Test workflows and automation in Sitecore Content Hub to ensure they function correctly with migrated content.\<br\>    \<br\>2.  Validate that asset relationships (e.g., dependencies, collections) are maintained post-migration.\<br\>    \<br\>3.  Ensure that all assets and their associated workflows meet project goals and usability expectations. |
| 6   | **Post-Migration Monitoring and Optimization** | 1.  Set up post-migration monitoring to track system performance, identify potential issues, and resolve them promptly.\<br\>    \<br\>2.  Implement a feedback loop to gather user input on usability and functionality of migrated content.\<br\>    \<br\>3.  Develop an optimization plan to address any ongoing content organization, tagging, or workflow issues that emerge after migration. |
| 7   |     |     |

### Using Excel

### **Data Migration**

Importing and exporting in Sitecore’s Content Hub enables asset and asset metadata migration from a legacy system. An Excel file is used as the source of legacy data. In order to successfully migrate data, specific rules must be followed to avoid technical errors that would stop the import. We will now examine those rules and how they affect the structure of the Excel file used to import legacy data into your Content Hub.

### Create an Excel Import File

There are ground rules to follow when importing the Excel document into Sitecore Content Hub.

Ground rules for importing:

1.  **One entity definition per worksheet:** Each entity definition being imported into a Sitecore Content Hub requires its own worksheet. A worksheet must contain data for only one entity definition.
    
2.  **The worksheet name is the name of the entity definition being imported:** If M.Asset entities are being imported, then the worksheet will be named M.Asset. The import process uses the name of the worksheet to determine which types of entities to create and populate with data.
    
3.  **Identifiers to ID entities:** Use identifiers to reference existing entities in the system.
    
4.  **Worksheets are imported in the order they appear in the Excel file:** This is important when importing relations, as the referenced related item should already be imported and extant in the Content Hub before attempting to import and relate a new entity to it.
    
5.  **Use the Metadata member’s name, not its label, as the Excel column header:** The metadata field names, not the labels, are used for the Excel column headers. This is to avoid any doubt on which property or relation is being used, as the name is unique per entity definition. When working with a self-relation, you can add colon- Parent or colon-Child.
    
6.  **Multi values separate by “|”**: When multiple values are possible, as with option lists, taxonomies, or relations, you can separate the values by using the pipe character.
    
7.  **Multilingual = multiple columns with “\{propertyname\}#\{culture\}.”:** Multilingual properties have a column-per-portal language that is enabled. The property name is extended with the culture.
    
8.  **M.Asset definition requires the “File” column to import your media files:** The File column must be present as the first column in the worksheet for the **M.Asset** definition. It must contain the URL of either a public or authorized link. The import process will use the URL to run a fetch job that will copy the media file into the Sitecore Content Hub cloud-based blob storage. When this fetch job is complete, a corresponding processing job will begin; its purpose is to assign the metadata stored in the Excel row into the newly created asset and persist it.
    

![Screenshot 2024-11-04 at 14.15.09.png](/images/learn/accelerate/content-hub/attachments/5381128233/5382701058.png?width=760)

*   Assign a meaningful, user-determined identifier. By default, if no identifier value is set, the system will create a GUID value and assign it as default identifier.
    
*   In Column D, the multilingual description is given in English.
    
*   The AssetTypeToAsset relation is set by giving an asset type identifier. The asset types are loaded in the previous worksheet to make sure they are present in Sitecore Content Hub and can be linked to the new entity being created.
    
*   The File column will be used to run a fetch job with the given URL, with either a public or authorized link.
    

**Setup Data Export in Content Hub**

In this part, you will examine how to extract information out of Content Hub. You will create different export profiles to retrieve the information you are interested in, then enable the export for users directly on the search. When exporting data out of Content Hub with the Excel export feature, you can make use of the export profiles to define which metadata you want to export (e.g., **Name, Definition, IsDefault**, and **Setting**).

![Screenshot 2024-11-04 at 13.55.27.png](/images/learn/accelerate/content-hub/attachments/5381128233/5381160993.png?width=509)

For the properties, only the name needs to be included. The property type will be taken into account and exported in the appropriate format. The “relations” are a combination of the relation names and properties for export-related entities and profiles.

For assets, you can add an additional setting to control the export of public links as a URL. You can define whether you want to receive all public links from the asset or from the master file.

`"publicLinks": \{`

      `"asset": true,`

      `"masterfile": false`

`\}`

When exporting, you have some additional options to select. The Filename is the name of the file that you will be able to download. The **User-friendly column headers** switch determines whether the property and relation names or labels are displayed in the exported file. User-friendly values mean that the field’s label (rather than its name) is used as a column header. User-friendly cell values means that the label value in the field will display without its full identifier showing up.

Example:

`\{`  
`"properties": [`  
`"Title",`  
`"Description",`  
`"FileName"`  
`],`  
`"relations": \{`  
`"AssetTypeToAsset": \{`  
`"exportRelatedEntities": false`  
`\},`  
`"FinalLifeCycleStatusToAsset": \{`  
`"exportRelatedEntities": false`  
`\},`  
`"ContentRepositoryToAsset": \{`  
`"exportRelatedEntities": false`  
`\}`  
`\},`  
`"includeSystemProperties": true,`  
`"publicLinks": \{`  
`"asset": true,`  
`"masterfile": false`  
`\},`  
`"version": "1.1"`  
`\}`

### Additional Tips:

Nicky Vadera to verify.

Prior to go live you can update the following to assist migration: Active scripts moved to background: Validation script moved to flag

*   Zip files: Triggers extract archives: disabled during migration. To be enabled after and job to be run
    
*   Monitor graph during migration
    
*   Create a BatchID property to give your import batches numbers to assist with troubleshooting.
    
*   Create an additional property to store the ID of the previous DAM.
    

remember to enable after migration is complete.

  
  
**API Queries for monitoring**

*   Assets in system  
    https:// \[ Environment i.e. PROD \].\[client url\] .com/api/entities/query?query=definition.name==%27M.Asset%27%20(%27AssetMediaToAsset%27)&&take=0
    
*   Assets that have been processed by media processing (default renditions)
    

https:// \[Environment i.e. PROD \].\[client url\] .com/api/entities/query?query=definition.name==%27M.Asset%27%20AND%20Exists(%27AssetMediaToAsset%27)&&take=0

*   Assets still needing to be processed
    

https:// \[ Environment i.e. PROD \].\[client url\] .com/api/entities/query?query=definition.name==%27M.Asset%27%20AND%20Missing(%27AssetMediaToAsset%27)&&take=0

*   Metadata Complete
    

https:// \[Environment i.e. PROD \].\[client url\] .com/api/entities/query?query=definition.name==%27M.Asset%27%20AND%20Exists(%27BrandToAsset%27)&&take=0

Migration Templates: \{Insert link to excel files\} perhaps on Asset Library with public link?

M.Asset

M.PCM

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) Discussion

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f329.png) Product Gaps

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
| 1   | [https://doc.sitecore.com/ch/en/users/40/content-hub/best-practices--data-migration.html](https://doc.sitecore.com/ch/en/users/40/content-hub/best-practices--data-migration.html) |
| 2   | [https://doc.sitecore.com/ch/en/users/content-hub/importing-and-exporting-data.html](https://doc.sitecore.com/ch/en/users/content-hub/importing-and-exporting-data.html) |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related 360 Activities

|     |     |
| --- | --- |
|     | **Activity Link** |
| 1   |     |
| 2   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Value Realization Framework Activities

|     |     |
| --- | --- |
|     | **VRF Card Link** |
| 1   |     |
| 2   |     |