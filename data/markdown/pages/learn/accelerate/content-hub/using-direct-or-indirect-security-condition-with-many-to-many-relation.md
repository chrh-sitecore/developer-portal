---
title: 'Using direct or indirect security condition with Many to Many relation'
description: 'Explain how to implement user group using direct or indirect security condition with Many to Many relation.'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-11-13'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Configuration > Schema Management'
author: 'Xavier Cobbaert'
audience: ''
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) **Context**

When a Many to Many relation is used as security conditions in user group, 2 main security use cases could be foreseen:

1.  End user gets permission if **all values** in the relation are matching the security conditions
    
2.  End user gets permission if **at least one value** in the relation is included in the security conditions
    

These 2 use cases require different configuration in the schema and in the configuration of user group.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) **Execution**

### Security concept

In Sitecore Content Hub, security conditions could be setup using direct relation or indirect relation and the permission behaviour is different depending the direct or indirect relation.

_Example of direct relation_: Asset Type is directly linked to Assets.

_Example of indirect relation_: Brand is linked to Product Family, Product Family is linked to Product and Product is linked to Assets. Brand is indirectly linked to Assets.

#### Security behaviour of a rule / policy with a condition using DIRECT relation:

End user gets the permissions configured in the rule if all values in the relation of the entity selected are matching the conditions.

The following table shows when permissions is granted depending value(s) on the entity and the conditions configure in user group rule.

|     |     |     |     |     |
| --- | --- | --- | --- | --- |
| **Conditions on Rule** | **Entity values** |     |     |     |
| Condition on Value 1 | X   |     | X   | X   |
| Condition on Value 2 |     |     | X   |     |
| No conditions on Value 3 |     | X   |     | X   |
| Permissions granted | YES | NO  | YES | **NO** |

#### Security behaviour of a rule / policy with a condition using INDIRECT relation:

End user gets the permissions configured in the rule if at least one value in the relation of the entity selected is matching one of the conditions.

The following table shows when permissions is granted depending value(s) on the entity and the conditions configure in user group rule.

|     |     |     |     |     |
| --- | --- | --- | --- | --- |
| **Conditions on Rule** | **Entity values** |     |     |     |
| Condition on Value 1 | X   |     | X   | X   |
| Condition on Value 2 |     |     | X   |     |
| No conditions on Value 3 |     | X   |     | X   |
| Permissions granted | YES | NO  | YES | **YES** |

### Use cases

For the following use cases, Market / Geography taxonomy has been created and directly linked to Asset. The relation between Market / Geography has the option “Inherits security” equal to True and it is a Many to Many relation. See [https://doc.sitecore.com/ch/en/users/content-hub/create-a-member.html#relation-member-fields](https://doc.sitecore.com/ch/en/users/content-hub/create-a-member.html#relation-member-fields) for more information

Following use cases will show how to implement with the Market / Geography taxonomy a direct or indirect security permissions depending business needs.

In our examples, the list of values of Market / Geography are Africa, Asia, Europe, Oceania, North America and South America. The technical taxonomy name of “Market / Geography” is Sitecore.Geography.

#### Use Case 1: Update permission on Assets using Market / Geography taxonomy direct linked to Assets.

End user will have update permission on Asset if all Market / Geography values on Asset are matching the conditions.

##### Schema configuration

A new taxonomy Market / Geography has been created and a new many to many relation between Market / Geography and Asset has been created with the option inherits security equal to True. See the diagram below.