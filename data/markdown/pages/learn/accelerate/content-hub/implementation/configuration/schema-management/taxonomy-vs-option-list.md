---
title: 'Taxonomy vs Option List'
description: 'When designing the domain model for Content Hub, one of the most common dilemmas that people face is the choice of the type of field. Since Taxonomy and Option List are very similar to end user as both appear to be a drop down list the choice from a domain model design perspective becomes difficult. This recipe provides guidance regarding which one to pick based on the requirements and the factors to consider before selecting one of them.'
hasSubPageNav: false
hasInPageNav: false
area: ['accelerate']
lastUpdated: '2024-11-13'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Configuration > Schema Management'
author: 'Soumyadeep Ghosh Dastidar'
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f914.png) Problem

During implementation if you have a bunch of values for a specific metadata that you would like to populate as a drop down list, Content hub presents you with two options - Taxonomy and Option List. As an example, if you have a field called _Region_ and the set of values for this field is the list of all countries. Selecting the right data format for _Region_ is essential for building a high-performing application. Since the data model would depend on this schema structure the correct choice would result in proper domain model. However the dilemma remains: should I go with an option list or a taxonomy?

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f331.png) Solution

There’s no one-size-fits-all answer—it varies based on the requirements and the field's role within the domain model. When choosing between taxonomies and option lists in Sitecore's Content Hub, the decision depends on factors like the number of values, need for hierarchy, security settings, and display requirements. Taxonomies are preferable for extensive lists, complex hierarchies, and situations requiring security or inheritance, while option lists are suitable for simpler lists under 50 items without additional properties.

The factors to consider when making the choice are as follows:

1.  If the list of values contains fewer than 50 items, use an option list. If the list exceeds 50 items, implement a taxonomy structure.
    
2.  If user groups need to be defined based on the metadata field, the field should be configured as a taxonomy rather than an option list.
    
3.  To use the field as a search facet across various search operations, it is advisable to define it as a taxonomy rather than an option list.
    
4.  To store additional information about the metadata field—such as type, name, and origin—it should be configured as a taxonomy. Option lists are limited to serving as data sources and do not support extended metadata.
    
5.  An option list can support a maximum of 5 hierarchical levels, whereas a taxonomy has no restriction on the number of hierarchy levels it can contain.
    

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) Discussion

Let us take an example to better understanding the factors applicable during the choice between taxonomy and option list.

Example 1 - _Region_

Let us assume _Region_ refers to all the countries were your product is available OR your asset would be used. For simplicity let us consider it is available in all countries across the globe. So, the region would need to be a hierarchical structure comprising of countries from the following business regions:

*   EMEA (Europe, the Middle East and Africa)
    
*   NA (North America)
    
*   LATAM (Latin America)
    
*   APAC (Asia-Pacific)
    

If you have _Region_ as a metadata field that needs to be there in the domain model, then you would proceed with each factor as follows:

1.  **List of values for the field**: Since _Region_ would have lot of values (list of countries), so it makes sense to create a taxonomy for it.
    
2.  **Define security policies and user groups based on the field:** There would be user groups who would be allowed access on the assets based on their _Region_. So, _Region_ needs to be a taxonomy so that it can be used for defining the policies and user groups.
    
3.  **Use in search filters**: Pages like Asset Search page would benefit if _Region_ is provided as a facet so that end-users can use it to filter the assets and their search experience is improved.
    
4.  **Metadata of metadata**: Some organizations keep more information associated with a _Region_ e.g, _Region_ Code, Currency, Time Zones etc. If you have this kind of requirement then you can opt for Taxonomy over an Option List.
    
5.  **Levels of hierarchy**: For a "_Region_" field, you can create a multi-level hierarchy that breaks down geographical areas from broad to more specific categories. If you have similar needs then defining _Region_ as a taxonomy would be more beneficial over Option List
    

_Level 1: Global_

*   The highest level, encompassing all regions (e.g., "Global").
    

_Level 2: Super-Region_

*   Divides the world into broader areas, often used in global organizations.
    
*   Examples: EMEA (Europe, Middle East, Africa), APAC (Asia-Pacific), NA (North America), LATAM (Latin America).
    

_Level 3: Region_

*   More specific breakdown within each Super-Region, typically sub-continents or major regional divisions.
    
*   Examples:
    
    *   EMEA: Europe, Middle East, Africa
        
    *   APAC: East Asia, Southeast Asia, South Asia, Oceania
        

_Level 4: Sub-Region / Country Group_

*   A smaller group within each region, often a group of countries with close geographic or economic ties.
    
*   Examples:
    
    *   Europe: Western Europe, Eastern Europe, Northern Europe
        
    *   Middle East: GCC (Gulf Cooperation Council), Levant
        

_Level 5: Country_

*   The specific country within each sub-region.
    
*   Examples:
    
    *   Europe/Western Europe: France, Germany, UK
        
    *   Middle East/GCC: Saudi Arabia, UAE, Qatar
        

_Level 6: State/Province or City (optional, based on granularity needs)_

*   Further breakdown within countries, useful for highly localized data or services.
    
*   Examples:
    
    *   United States: California, Texas, New York
        
    *   China: Beijing, Shanghai, Guangdong
        

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
| 1   | [https://doc.sitecore.com/ch/en/users/content-hub/create-a-taxonomy.html](https://doc.sitecore.com/ch/en/users/content-hub/create-a-taxonomy.html) |
| 2   | [https://doc.sitecore.com/ch/en/users/content-hub/create-an-option-list.html](https://doc.sitecore.com/ch/en/users/content-hub/create-an-option-list.html) |

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