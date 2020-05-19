---
header-id: patching-liferay
---

# Patching @sharepoint@

[TOC levels=1-4]

While we strive for perfection with every @sharepoint@ release, the reality of the
human condition dictates that releases may not be as perfect as originally
intended. But we've planned for that. Included with every @sharepoint@ bundle is a
Patching Tool that handles installing two types of patches: fix packs and
hotfixes. 

| **Important:** Make sure to
| [back up your @sharepoint@ installation and database](/docs/7-2/deploy/-/knowledge_base/d/backing-up-a-liferay-installation)
| regularly, especially before patching. The patching tool installs code changes
| and some of these make data changes (if necessary) automatically on startup.
| 
| Certain fix packs (service packs) can include data/schema
| micro changes---they're optional and revertible. Module upgrades and any 
| micro changes they include are applied at server startup by default, or can be 
| applied manually by
| [disabling the `autoUpgrade` property](/docs/7-2/deploy/-/knowledge_base/d/configuring-the-data-upgrade#configuring-non-core-module-data-upgrades).
| Server startup skips all Core micro changes. Instead, you
| can apply them using the [upgrade
| tool](/docs/7-2/deploy/-/knowledge_base/d/upgrading-to-sharepoint-ver)
| before server startup.

| **Important:** Installing the latest service pack on top of @sharepoint@ 7.2 
| GA1/FP1 requires running the
| [data upgrade tool](/docs/7-2/deploy/-/knowledge_base/d/upgrading-the-sharepoint-data).
| Examine the
| [@sharepoint@ upgrade instructions](/docs/7-2/deploy/-/knowledge_base/d/upgrading-to-sharepoint-ver)
| to determine preparations, testing, and post upgrade steps that are 
| appropriate for you. To eliminate system down time during upgrade, consider 
| using the 
| [Blue-green deployment technique](/docs/7-2/deploy/-/knowledge_base/d/other-cluster-update-techniques). 
|
| @sharepoint@ 7.2 FP2/SP1's data schema change adds version columns to several 
| tables.
| [Hibernate's optimistic locking system](https://docs.jboss.org/hibernate/orm/4.0/devguide/en-US/html/ch05.html#d0e2225)
| uses the columns to preserve data integrity during concurrent data 
| modifications. 

| **Note:** [Patching a cluster](/docs/7-2/deploy/-/knowledge_base/d/updating-a-cluster)
| requires additional considerations.
