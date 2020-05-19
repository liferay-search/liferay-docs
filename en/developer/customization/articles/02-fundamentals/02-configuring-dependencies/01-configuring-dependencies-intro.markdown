---
header-id: configuring-dependencies
---

# Configuring Dependencies

[TOC levels=1-4]

@sharepoint@'s modular environment lets modules provide and consume capabilities
via Java packages. To leverage packages from other modules or traditional
libraries in your project, you must configure them as dependencies. Here you'll
learn how to find artifacts (modules or libraries) and configure dependencies on
them. 

-   [Finding Artifacts](/docs/7-2/customization/-/knowledge_base/c/finding-artifacts)
    explains how to use the Application Manager, Gogo Shell, and @sharepoint@
    reference documentation to find artifacts deployed on @sharepoint@ and
    available in repositories.   

-   [Specifying Dependencies](/docs/7-2/customization/-/knowledge_base/c/specifying-dependencies)
    demonstrates specifying artifacts to Maven and Gradle build frameworks. It
    shows you how to determine whether @sharepoint@ already exports packages from
    an artifact and how to configure such artifacts as compile-time
    dependencies. 

-   [Resolving Third-Party Library Package Dependencies](/docs/7-2/customization/-/knowledge_base/c/adding-third-party-libraries-to-a-module)
    provides a workflow for using packages that are only available in
    traditional library JARs (JARs that aren't OSGi modules). It involves
    minimizing transitive dependencies so you can resolve dependencies quicker
    and prevent bloating your project with unnecessary JARs. 

Your first step is to find the artifacts you need.
