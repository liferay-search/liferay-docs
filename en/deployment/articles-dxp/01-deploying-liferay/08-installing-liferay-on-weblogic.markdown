---
header-id: installing-liferay-dxp-on-weblogic-12c-r2
---

# Installing @sharepoint@ on WebLogic 12c R2

[TOC levels=1-4]

Although you can install @sharepoint@ in a WebLogic Admin Server, this isn't
recommended. It's a best practice to install web apps, including @sharepoint@, in
a WebLogic Managed server. Deploying to a Managed Server lets you start or shut
down @sharepoint@ more quickly and facilitates transitioning into a cluster
configuration. This article therefore focuses on installing @sharepoint@ in
a Managed Server.

Before getting started, create your Admin and Managed Servers. See
[WebLogic's documentation](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)
for instructions on setting up and configuring Admin and Managed Servers. 

Also familiarize yourself with [preparing for
install](/docs/7-2/deploy/-/knowledge_base/d/preparing-for-install). 

Now, [download the @sharepoint@ WAR and Dependency
JARs](/docs/7-2/deploy/-/knowledge_base/d/obtaining-sharepoint#downloading-the-liferay-war-and-dependency-jars):

-   @sharepoint@ WAR file
-   Dependencies ZIP file
-   OSGi Dependencies ZIP file

**Checkpoint**

The following jars should be present within the `liferay-dxp-dependencies-[version].zip`:

1. `com.liferay.petra.concurrent.jar`
2. `com.liferay.petra.executor.jar`
3. `com.liferay.petra.function.jar`
4. `com.liferay.petra.io.jar`
5. `com.liferay.petra.lang.jar`
6. `com.liferay.petra.memory.jar`
7. `com.liferay.petra.nio.jar`
8. `com.liferay.petra.process.jar`
9. `com.liferay.petra.reflect.jar`
10. `com.liferay.petra.string.jar`
11. `com.liferay.registry.api.jar`
12. `hsql.jar`
13. `portal-kernel.jar`
14. `portlet.jar`

The following folders should be present within the `/liferay/osgi` folder: 

1. `Configs`
2. `Core`
3. `Marketplace`
4. `War`

Without any further ado, get ready to install @sharepoint@ in WebLogic! 

## Configuring WebLogic's Node Manager

WebLogic's Node Manager starts and stops managed servers.

If you're running WebLogic on a UNIX system other than Solaris or Linux, use the Java Node Manager, instead of the native version of the Node Manager, by configuring these Node Manager properties in the `domains/your_domain_name/nodemanager/nodemanager.properties` file:

```properties
NativeVersionEnabled=false

StartScriptEnabled=true
```

| **Note:**   By default, SSL is used with Node Manager. If you want to disable SSL during development, for example, set `SecureListener=false` in your `nodemanager.properties` file.

See Oracle's [Configuring Java Node Manager](https://docs.oracle.com/middleware/1212/wls/NODEM/java_nodemgr.htm#NODEM173) documentation for details.

## Configuring WebLogic

Next, you must set some variables in two WebLogic startup scripts. These 
variables and scripts are as follows. Be sure to use `set` instead of `export` 
if you're on Windows. 

1.  `your-domain/startWebLogic.[cmd|sh]`: This is the Admin Server's startup
    script. 

2.  `your-domain/bin/startWebLogic.[cmd|sh]`: This is the startup script for
    Managed Servers. 

    Add the following variables to both `startWebLogic.[cmd|sh]` scripts:

    ```bash
    export DERBY_FLAG="false"
    export JAVA_OPTIONS="${JAVA_OPTIONS} -Dfile.encoding=UTF-8 -Duser.timezone=GMT -da:org.apache.lucene... -da:org.aspectj..."
    export MW_HOME="/your/weblogic/directory"
    export USER_MEM_ARGS="-Xmx2560m -Xms2560m"
    ```

    | **Important:** For @sharepoint@ to work properly, the application server JVM
    | must use the `GMT` time zone and `UTF-8` file encoding.
    
    The `DERBY_FLAG` setting disables the Derby server built in to WebLogic, as
    @sharepoint@ doesn't require this server. The remaining settings support
    @sharepoint@'s  memory requirements, UTF-8 requirement, Lucene usage, and
    Aspect Oriented  Programming via AspectJ. Also make sure to set `MW_HOME` to
    the directory  containing your WebLogic server on your machine. For example: 

    ```bash
    export MW_HOME="/Users/ray/Oracle/wls12210"
    ```

3.  Some of the settings are also found in the
    `your-domain/bin/SetDomainEnv.[cmd|sh]` . Add the following variables
    (Windows):

    ```bash
    set WLS_MEM_ARGS_64BIT=-Xms2560m -Xmx2560m 
    set WLS_MEM_ARGS_32BIT=-Xms2560m -Xmx2560m
    ```

    or on Mac or Linux:

    ```bash
    WLS_MEM_ARGS_64BIT="-Xms2560m -Xmx2560m"
    export WLS_MEM_ARGS_64BIT

    WLS_MEM_ARGS_32BIT="-Xms2560m -Xmx2560m"
    export WLS_MEM_ARGS_32BIT
    ```

4.  Set the Java file encoding to UTF-8 in 
    `your-domain/bin/SetDomainEnv.[cmd|sh]` by appending `-Dfile.encoding=UTF-8`
    ahead of your other Java properties:  

    ```bash
    JAVA_PROPERTIES="-Dfile.encoding=UTF-8 ${JAVA_PROPERTIES} ${CLUSTER_PROPERTIES}"
    ```

5.  You must also ensure that the Node Manager sets @sharepoint@'s memory
    requirements when starting the Managed Server. In the Admin Server's console
    UI, navigate to the Managed Server you want to deploy @sharepoint@ to and
    select the *Server Start* tab. Enter the following parameters into the
    *Arguments* field: 

    ```bash
    -Xmx2560m -Xms2560m -XX:MaxMetaspaceSize=512m
    ```

    Click *Save* when you're finished. 

Next, you'll set some @sharepoint@-specific properties for your @sharepoint@ installation. 

## Setting @sharepoint@ Properties

Before installing @sharepoint@, you must set the  [*Liferay
Home*](/docs/7-2/deploy/-/knowledge_base/d/liferay-home) folder's location via
the `liferay.home` property in a
[`portal-ext.properties`](/docs/7-2/deploy/-/knowledge_base/d/portal-properties)
file. You can also use this file to override [other @sharepoint@
properties](@platform-ref@/7.2-latest/propertiesdoc/portal.properties.html)
that you may need. 

First, decide which folder you want to serve as Liferay Home. In WebLogic, your
domain's folder is generally Liferay Home, but you can choose any folder on your
machine. Then create your `portal-ext.properties` file and add the
`liferay.home` property: 

```properties
liferay.home=/full/path/to/your/liferay/home/folder
```

Remember to change this file path to the location on your machine that you want 
to serve as Liferay Home. 

Now that you've created your `portal-ext.properties` file, you must put it
inside the @sharepoint@ WAR file. Expand the @sharepoint@ WAR file and place
`portal-ext.properties` in the `WEB-INF/classes` folder. Later, you can deploy
the expanded archive to WebLogic. Alternatively, you can re-WAR the expanded
archive for later deployment. In either case, @sharepoint@ reads your property
settings once it starts up. 

If you need to make any changes to `portal-ext.properties` after @sharepoint@ 
deploys, you can find it in your domain's `autodeploy/ROOT/WEB-INF/classes` 
folder. Note that the `autodeploy/ROOT` folder contains the @sharepoint@ 
deployment. 

Next, you'll install @sharepoint@'s dependencies. 

## Installing @sharepoint@ Dependencies

You must now install @sharepoint@'s dependencies. Recall that earlier you 
downloaded two ZIP files containing these dependencies. Install their contents 
now: 

1.  Unzip the Dependencies ZIP file and place its contents in your WebLogic
    domain's `lib` folder. 

2.  Unzip the OSGi Dependencies ZIP file and place its contents in the 
    `Liferay_Home/osgi` folder (create this folder if it doesn't exist).

You must also add your database's driver JAR file to your domain's `lib` folder.
Note that although Hypersonic is fine for testing purposes, you **should not**
use it for sharepointion @sharepoint@ instances. 

**Checkpoint**

Your domain `lib` folder has these jars:

* `com.liferay.petra.concurrent.jar`
* `com.liferay.petra.executor.jar`
* `com.liferay.petra.function.jar`
* `com.liferay.petra.io.jar`
* `com.liferay.petra.lang.jar`
* `com.liferay.petra.memory.jar`
* `com.liferay.petra.nio.jar`
* `com.liferay.petra.process.jar`
* `com.liferay.petra.reflect.jar`
* `com.liferay.petra.string.jar`
* `com.liferay.registry.api.jar`
* `hsql.jar`
* `portal-kernel.jar`
* `portlet.jar`

A JDBC driver for your database has been added to your domain's `lib` folder.
Here are some common JDBC drivers:

* [`mariadb.jar`](https://downloads.mariadb.org/) 
* [`mysql.jar`](http://dev.mysql.com/downloads/connector/j)
* [`postgres.jar`](https://jdbc.postgresql.org/download/postgresql-42.0.0.jar)

Your `[Liferay Home]/osgi` folder has these subfolders:

* `Configs`
* `Core`
* `Marketplace`
* `War`

Next, you'll configure your database. 

## Database Configuration

Use the following procedure if you want WebLogic to manage your [database](/docs/7-2/deploy/-/knowledge_base/d/preparing-for-install#using-the-built-in-data-source) for @sharepoint@. You can skip this section if you want to use @sharepoint@'s built-in 
Hypersonic database. 

1.  Log in to your AdminServer console.

2.  In the *Domain Structure* tree, find your domain and navigate to *Services*
    &rarr; *JDBC* &rarr; *Data Sources*.

3.  To create a new data source, click *New*. Fill in the *Name* field with 
    `Liferay Data Source` and the *JNDI Name* field with `jdbc/LiferayPool`.
    Select your database type and driver. For example, MySQL is *MySQL's Driver
    (Type 4) Versions:using com.mysql.jdbc.Driver*. Click *Next* to continue.

4.  Accept the default settings on this page and click *Next* to move on. 

5.  Fill in your database information for your MySQL database. 

6.  If using MySQL, add the text 
    `?useUnicode=true&characterEncoding=UTF-8&\useFastDateParsing=false` to the
    URL line and test the connection. If it works, click *Next*. 

7.  Select the target for the data source and click *Finish*. 

8.  You must now tell @sharepoint@ about the JDBC data source. Create a 
    `portal-ext.propreties` file in your Liferay Home directory, and add the
    line:

    ```propreties
    jdbc.default.jndi.name=jdbc/LiferayPool
    ```

Alternatively, you can make the above configuration strictly via properties in 
the `portal-ext.properties` file. To do so, place the following properties and 
values in the file. Be sure to change the `your*` values with the values 
appropriate for your database's configuration (if using MySQL): 

```propreties
jdbc.default.driverClassName=com.mysql.jdbc.Driver
jdbc.default.url=jdbc:mysql://your.db.ip.address/yourdbname?useUnicode?useUnicode=true&characterEncoding=UTF-8&useFastDateParsing=false
jdbc.default.username=yourdbuser
jdbc.default.password=yourdbpassword
```

Next, you'll configure your mail session. 

## Mail Configuration

If you want WebLogic to manage your [mail session](/docs/7-2/deploy/-/knowledge_base/d/configuring-mail), use the following procedure. 
If you want to use Liferay's built-in mail session (recommended), skip this
section. 

1.  Start WebLogic and log in to your Admin Server's console.

2.  Select *Services* &rarr; *Mail Sessions* from the *Domain Structure* box on
    the left hand side of your Admin Server's console UI. 

3.  Click *New* to begin creating a new mail session. 

4.  Name the session *LiferayMail* and give it the JNDI name `mail/MailSession`.
    Then fill out the *Session Username*, *Session Password*, *Confirm Session
    Password*, and *JavaMail Properties* fields as necessary for your mail
    server. See the 
    [WebLogic documentation](http://docs.oracle.com/middleware/1221/wls/FMWCH/pagehelp/Mailcreatemailsessiontitle.html)
    for more information on these fields. Click *Next* when you're done. 

5.  Choose the Managed Server that you'll install @sharepoint@ on, and click
    *Finish*. Then shut down your Managed and Admin Servers. 

6.  With your Managed and Admin servers shut down, add the following property to
    your `portal-ext.properties` file in Liferay Home: 

    ```propreties
    mail.session.jndi.name=mail/MailSession
    ```

@sharepoint@ references your WebLogic mail session via this property setting. If
you've already deployed @sharepoint@, you can find your `portal-ext.properties`
file in your domain's `autodeploy/ROOT/WEB-INF/classes` folder. 

Your changes take effect upon restarting your Managed and Admin servers. 

## Deploying @sharepoint@

As mentioned earlier, although you can deploy @sharepoint@ to a WebLogic Admin 
Server, you should instead deploy it to a WebLogic Managed Server. Dedicating 
the Admin Server to managing other servers that run your apps is a best 
practice. 

Follow these steps to deploy @sharepoint@ to a Managed Server: 

1.  Make sure the Managed Server you want to deploy @sharepoint@ to is shut down. 

2.  In your Admin Server's console UI, select *Deployments* from the *Domain 
    Structure* box on the left hand side. Then click *Install* to start a new
    deployment. 

3.  Select the @sharepoint@ WAR file or its expanded contents on your file system.
    Alternatively, you can upload the WAR file by clicking the *Upload your
    file(s)* link. Click *Next*. 

4.  Select *Install this deployment as an application* and click *Next*.

5.  Select the Managed Server you want to deploy @sharepoint@ to and click *Next*. 

6.  If the default name is appropriate for your installation, keep it.
    Otherwise, give it a name of your choosing and click *Next*. 

7.  Click *Finish*. After the deployment finishes, click *Save* if you want to
    save the configuration. 

8.  Start the Managed Server where you deployed @sharepoint@. @sharepoint@ precompiles
    all the JSPs and then launches. 

Nice work! Now you're running @sharepoint@ on WebLogic. 

| After deploying @sharepoint@, you may see excessive warnings and log messages, 
| such as the ones below, involving `PhaseOptimizer`. These are benign and can
| be ignored. Make sure to adjust your app server's logging level or log filters
| to avoid excessive benign log messages.
| 
|     May 02, 2018 9:12:27 PM com.google.javascript.jscomp.PhaseOptimizer$NamedPass process
|     WARNING: Skipping pass gatherExternProperties
|     May 02, 2018 9:12:27 PM com.google.javascript.jscomp.PhaseOptimizer$NamedPass process
|     WARNING: Skipping pass checkControlFlow
|     May 02, 2018 9:12:27 PM com.google.javascript.jscomp.PhaseOptimizer$NamedPass process
|     INFO: pass supports: [ES3 keywords as identifiers, getters, reserved words as properties, setters, string continuation, trailing comma, array pattern rest, arrow function, binary literal, block-scoped function declaration, class, computed property, const declaration, default parameter, destructuring, extended object literal, for-of loop, generator, let declaration, member declaration, new.target, octal literal, RegExp flag 'u', RegExp flag 'y', rest parameter, spread expression, super, template literal, modules, exponent operator (**), async function, trailing comma in param list]
|     current AST contains: [ES3 keywords as identifiers, getters, reserved words as properties, setters, string continuation, trailing comma, array pattern rest, arrow function, binary literal, block-scoped function declaration, class, computed property, const declaration, default parameter, destructuring, extended object literal, for-of loop, generator, let declaration, member declaration, new.target, octal literal, RegExp flag 'u', RegExp flag 'y', rest parameter, spread expression, super, template literal, exponent operator (**), async function, trailing comma in param list, object literals with spread, object pattern rest]
