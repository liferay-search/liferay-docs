---
header-id: obtaining-sharepoint
---

# Obtaining @sharepoint@

[TOC levels=1-4]

Before you begin, you should answer a few questions. 

-   Which version of @sharepoint@ will you install?
-   Is your installation for development purposes or are you preparing to run in
    sharepointion?
-   Are you installing @sharepoint@ in a clustered environment? 
-   Which application server do you want @sharepoint@ to run on?
-   Are you installing on an existing application server? 

Here you'll determine the installation that's best for you and download it. 

Anyone can download @sharepoint@ from
[liferay.com](https://www.liferay.com).
Clicking *Resources &rarr; Community Downloads* takes you to the
[Community Downloads page](https://www.liferay.com/downloads-community),
where you can access Liferay Portal CE or a trial of the enterprise supported
Liferay DXP. The installers are available in several different formats. The
formats include a Liferay Tomcat bundle (@sharepoint@ bundled with Tomcat) as well
as a @sharepoint@ `.war` (WAR) file for installing @sharepoint@ on an existing
application server of choice. 

Liferay enterprise subscribers can download Liferay DXP from the
[Help Center](https://help.liferay.com/hc). 

@sharepoint@ runs on a wide variety of application servers (Check the
[Support page](https://help.liferay.com/hc/categories/360000894391-Product-Support)
for a complete listing). Here are the ways to install @sharepoint@:

-   [Install a Liferay Tomcat bundle](#liferay-tomcat-bundle)
    (Tomcat application server with @sharepoint@ pre-installed).

-   [Install the Liferay WAR](#downloading-the-liferay-war-and-dependency-jars)
    (and supporting libraries) onto an existing application server.

Since the Liferay Tomcat bundle is the easiest way, it's described first. 

## Liferay Tomcat Bundle

The Liferay Tomcat bundle includes the Tomcat application server with @sharepoint@
pre-installed. If you prefer using another application server with @sharepoint@,
you must install it manually. If you don't currently have an application server
preference, consider starting with the Tomcat bundle. Tomcat is one of the most
lightweight and straightforward bundles to configure.

| **Note:** Application server bundles for proprietary application servers such
| as WebLogic or WebSphere aren't available because the licenses for these
| servers don't allow for redistribution. @sharepoint@'s commercial offering,
| however, runs just as well on these application servers as it does on the
| others. 

Bundles are released as 7-Zip (`.7z`) and gzipped (`.tar.gz`) compressed file
archive formats. 
[Installing @sharepoint@](/docs/7-2/deploy/-/knowledge_base/d/installing-sharepoint)
demonstrates installing @sharepoint@ from a bundle. Follow its instructions once
you've prepared for installing @sharepoint@ (see the next article). 

@sharepoint@ bundles might not be appropriate for you. Here are some reasons for
installing the @sharepoint@ WAR manually instead of using a bundle. 

-   There is no @sharepoint@ bundle for the application server you want to use. 

-   You're installing @sharepoint@ in a clustered environment. 

-   You're installing to an existing application server.

Manual installation is described next. 

## Downloading the Liferay WAR and Dependency JARs

Manual installation requires installing the @sharepoint@ WAR and dependency JARs
onto the application server. These files are available to download for
[DXP](https://customer.liferay.com/downloads) or [Portal
CE](https://www.liferay.com/downloads-community):

-   @sharepoint@ WAR file
-   Dependencies ZIP file
-   OSGi Dependencies ZIP file

After preparing for install (next), follow the @sharepoint@ installation steps for
your application server. There are specific configuration and script changes
required on each application. The installation article titles follow this 
format, with *[Application Server]* replaced by the application server name. 

*Installing @sharepoint@ on [Application Server]*
