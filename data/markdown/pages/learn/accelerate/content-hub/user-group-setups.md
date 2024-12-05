---
title: 'User Group Setups'
description: 'Content hub allows creation of user groups to control the permissions of users within the platform. Each user would be linked with one or more user group. So, the creation of right user groups is critical during the implementation phase. The groups should not be that large in number that it over burdens the system and complicates assignment of user to user groups, while at the same time not that little that end-users don’t have enough privileges to perform their work inside the platform.'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-11-20'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Configuration > Functional Security (Users and User Groups)'
author: 'Soumyadeep Ghosh Dastidar'
audience: ''
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) **Context**

Defining user groups for Content Hub involves categorizing the users of the system based on their roles, goals, and interaction with the application. These groups should be distinct, reflecting different use cases and expertise levels. But the critical question lies on how many groups should be defined and how the permissions should vary across the user group.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) **Execution**

Ideally the structure of permissions in user roles should focus on roles & permissions that need to be made available for the users to perform their jobs efficiently within Content Hub. Although every organizations have their own user access structure, some of the common patterns observed across projects are summarized into the following table. Please note that this is not an exhaustive list and more user group setup might be needed to handle client specific logic.

|     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- |
| **User Group in Organization** | **Description** | **Primary Responsibilities** | **Access Level** | **Common Tasks** | **Suggested User group in Content Hub** |
| **Administrators** | Full control over the application, including global settings, permissions, and user management. | *   Configure system architecture and global settings. - Define roles and permissions. - Monitor security and performance. | *   Full system access.- Override permissions and manage restricted areas. | *   Creating custom roles.- Setting data governance policies.- Reviewing audit logs. | **Superusers (OOTB)** |
| **Content Managers** | Oversee content/asset creation, approval, and publishing processes. | *   Approve and manage content.- Coordinate delivery timelines.- Maintain content standards. | *   Access to approval workflows and editing tools.- Limited admin permissions for templates. | *   Reviewing drafts.- Managing taxonomy.- Scheduling publications. | **M.Builtin.ContentAdministrators (OOTB)**\<br\>\<br\>**M.Builtin.Approvers (OOTB)**\<br\>\<br\>**Custom user groups can also be create if specific logic is needed not covered by OOTB groups** |
| **Asset Creators/Contributors/Uploaders** | Create and submit content/asset for approval or publication. | *   Create and edit asset/content - Collaborate with team members.- Submit content for approval. | *   Creation and editing rights.- No access to administrative settings. | *   Creating content- Assigning metadata.- Submitting drafts. | **M.Builtin.Creators (OOTB)**\<br\>\<br\>**Custom user groups can also be create if specific logic is needed not covered by OOTB groups** |
| **Reviewers/Approvers** | Review and approve content before publication. | *   Assess content for accuracy and standards.- Approve or reject submissions.- Provide feedback. | *   Review and comment rights.- No editing or admin rights. | *   Approving content.- Providing feedback.- Collaborating on revisions. | **M.Builtin.Editors (OOTB)**\<br\>\<br\>**Custom user groups can also be create if specific logic is needed not covered by OOTB groups** |
| **View-Only Users** | View published content and analytics without editing permissions. | *   Access published content.- Consume reports and data. | *   Read-only permissions.- No editing, approving, or admin rights. | *   Viewing dashboards.- Consuming content.- Exporting data (if permitted). | **M.Builtin.Guests (OOTB)**\<br\>\<br\>**M.Builtin.Readers (OOTB)**\<br\>\<br\>**Custom user groups can also be create if specific logic is needed not covered by OOTB groups** |
| **Developers** | Integrate, customize, or extend the platform using APIs or SDKs. | *   Build custom workflows.- Set up integrations.- Troubleshoot technical issues. | *   Access to APIs, SDKs, and logs.- No content or user management rights. | *   Setting up APIs.- Customizing templates.- Debugging performance issues. | **Custom user groups should be created with access to APIs so that the users can perform their development work** |

During definition of user groups along with the Rules defined on each entity in Content Hub, one should also focus on two main topics:

1.  **Member Security:** Using schema to define entity definitions and user group member security, one can enable specific security settings within the member or member group of an entity definiton. The secured entity definition member group or members would become accessible to specific user groups through this setting. e.g. On M.Asset entity you can select a specific relation member and turn the secured flag on in the advanced options. Now through member security you can grant the specific user group to Read or Write that member.
    
2.  **Privileges:** Privileges constitute the highest tier of security rules within the system, providing authorized user groups with comprehensive access to critical administrative functions. These include the ability to view and modify system settings, manage the domain model, and configure the security model. This level of access ensures that privileged users can oversee and control the foundational aspects of the platform's operation and security framework. e.g. Using privileges one can grant users of a user group to impersonate other users in the system, clear cache of the system, reset other user’s password, publish collections so that people from outside Content Hub can also access them.
    

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) **Insights**

When designing an access model, structure user groups and their policies carefully. Use policies to grant access rights to pages, definitions, and settings, ensuring these are distinct from policies granting access to specific markets or taxonomies. Avoid mixing roles from different markets, divisions, or departments, and refrain from creating group-level permissions for individual users. Instead, configure group permissions based on shared responsibilities and assign users to groups aligned with their roles.

e.g. Consider the following organization structure: L1 marketing managers can create, edit, approve delete all global & local contents created by Agency or their own Marketers. L2 Marketer can also create, edit, approve, delete all global & local contents but only created by marketers and not the agencies. Similarly the L2 global agency have all privileges over agency created contents. In L3 level marketers and agencies can only access assets & content belonging to their local region.

![Screenshot 2024-11-21 at 21.22.46.png](/images/learn/accelerate/content-hub/attachments/5424513086/5449875617.png?width=760)

In this world, the organization might grow in the future and expand over other countries, so the user groups should be constructed such that creation of new region should not result in creating a complex user group (lot of rules in the user group) but rather a simple user group with 1-2 rules. So, in this kind of situation we should create some base user groups which has common permissions for all local marketers or all local agencies. Additionally we would have a dedicated user group for each local region which has specific rules for assets that belong to their region and for the ones that don’t belong to their region.

For market-specific access, create policies that can be combined at the user group member level using the "Apply all" operator, which enforces all rules from combined policies. When a user belongs to multiple groups, policies can be applied independently or combined using operators like **Any** (permission granted if one group allows it) or **All** (permission granted only if all groups allow it).

So, continuing with out above example, a user who belongs to only the French Agency, should have the following combination of user group.

![Screenshot 2024-11-21 at 21.50.55.png](/images/learn/accelerate/content-hub/attachments/5424513086/5449646377.png?width=760)

Similarly for a marketer in USA, the combination of user group can be as follows:

![Screenshot 2024-11-21 at 21.51.58.png](/images/learn/accelerate/content-hub/attachments/5424513086/5449646384.png?width=760)

The advantage is when the same user moves to another region or takes more responsibility within the organization resulting in working for multiple regoins the assignment of additional user groups would be as simple as follows:

![Screenshot 2024-11-21 at 21.48.46.png](/images/learn/accelerate/content-hub/attachments/5424513086/5449843022.png?width=760)

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Recipes

|     |     |
| --- | --- |
|     | **Recipe** |
| 1   | [https://sitecore.atlassian.net/wiki/x/fAAEQgE](https://sitecore.atlassian.net/wiki/x/fAAEQgE) |
| 2   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Documentation

|     |     |
| --- | --- |
|     | **Documentation Link** |
| 1   | [https://doc.sitecore.com/ch/en/users/content-hub/manage-user-groups-in-content-hub.html](https://doc.sitecore.com/ch/en/users/content-hub/manage-user-groups-in-content-hub.html) |
| 2   | [https://doc.sitecore.com/ch/en/users/content-hub/user-group-policies.html](https://doc.sitecore.com/ch/en/users/content-hub/user-group-policies.html) |
| 3   | [https://doc.sitecore.com/ch/en/users/content-hub/configure-member-security.html](https://doc.sitecore.com/ch/en/users/content-hub/configure-member-security.html) |
| 4   | [https://doc.sitecore.com/ch/en/users/content-hub/security.html#configure-authentication](https://doc.sitecore.com/ch/en/users/content-hub/security.html#configure-authentication) |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |