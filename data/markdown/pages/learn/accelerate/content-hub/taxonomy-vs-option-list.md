---
title: 'Taxonomy vs Option List'
description: 'How to choose between a taxonomy and an option list when designing the domain model for Content Hub implementation.ORMaking the Right Metadata Choice: Taxonomy or Option List?ORBuilding a Scalable Domain Model with the Right Data Format'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-12-01'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Configuration > Schema Management'
author: 'Soumyadeep Ghosh Dastidar'
audience: ''
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f914.png) Problem

During implementation, defining the metadata structure for a field like _Region_ (populated with a list of all countries) presents a critical decision in Content Hub. Content Hub offers two options for such drop-downs: Taxonomy and Option List. Selecting the right format for _Region_ is essential, as it directly impacts the application’s performance and the overall domain model. However, the dilemma persists: should _Region_ be modeled as a taxonomy or an option list?

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f331.png) Solution

One of the main solutions is to effectively structure the Region in the domain model by analyzing it as follows:

1.  **Hierarchical Structure for Regions**  
    Organize _Region_ as a hierarchical taxonomy to represent global business areas:
    
    *   **Level 1:** Global
        
    *   **Level 2:** Super-Regions (e.g., EMEA, APAC, NA, LATAM)
        
    *   **Level 3:** Regions within Super-Regions (e.g., Europe, Middle East)
        
    *   **Level 4:** Sub-Regions or Country Groups (e.g., Western Europe, GCC)
        
    *   **Level 5:** Countries (e.g., France, Germany, UAE)
        
    *   **Level 6 (optional):** States/Provinces or Cities
        
2.  **Handling Large Value Lists**  
    With a vast number of countries, a taxonomy efficiently organizes data compared to a flat option list, improving scalability and maintainability.
    
3.  **Security Policies and User Group Management**  
    A taxonomy allows granular control over assets by defining access policies and user groups tied to specific regions. Thus, _Region_ basically defines the type of users in the system.
    
4.  **Enhancing Search Filters**  
    A taxonomy enables the _Region_ field to serve as a facet in search, enhancing asset discovery and user experience.
    
5.  **Metadata of Metadata**  
    If additional attributes like Region Code, Currency, or Time Zones are associated with _Region_, a taxonomy supports these relationships better than an option list.
    
6.  **Support for Business Needs**  
    The hierarchical structure accommodates varying levels of granularity, enabling flexibility for organizational or geographical reporting.
    

By defining _**Region**_ **as a taxonomy**, you ensure scalability, clarity, and optimal functionality in your domain model while meeting business requirements effectively.

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) Discussion

There’s no one-size-fits-all answer—it varies based on the requirements and the field's role within the domain model. When choosing between taxonomies and option lists in Sitecore's Content Hub, the decision depends on factors like the number of values, need for hierarchy, security settings, and display requirements. For managing large sets, intricate structures, and scenarios needing protection or inheritance, taxonomies are the best choice. Conversely, option lists are effective for straightforward lists with fewer than fifty entries and no extra attributes.

When deciding on the appropriate data type for your metadata, consider the following points:

*   **Number of data items possible for metadata**: For lists with fewer than 50 items, an option list is suitable. For larger lists, a taxonomy is recommended.
    
*   **User Group Definition**: If you need to define user groups based on the metadata field, configure it as a taxonomy.
    
*   **Search Facets**: To use the field as a search facet in various search pages, it is advisable to set it up as a taxonomy.
    
*   **Metadata Storage**: To store additional details about the metadata field, such as type, name, and origin, configure it as a taxonomy. Option lists are limited to serving as data sources and do not support extended metadata.
    
*   **Hierarchy Levels**: An option list can support up to five hierarchical levels, whereas a taxonomy has no restriction on the number of hierarchical levels it can contain.
    

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
| 3   | [https://doc.sitecore.com/ch/en/users/content-hub/taxonomies-and-option-lists.html](https://doc.sitecore.com/ch/en/users/content-hub/taxonomies-and-option-lists.html) |

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