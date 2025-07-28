---

title: 'Install the Full Distribution on an IBM WebSphere Server Liberty'
weight: 30

menu:
  main:
    name: "Manual Installation: WebSphere Liberty"
    identifier: "installation-guide-full-was-liberty-install-manual"
    parent: "installation-guide-full-was"
    pre: "Install and configure the Full Distribution on IBM WebSphere Application Server Liberty."

---

This section will describe how you can install Flowave and its components on an [IBM WebSphere
Application Server Liberty][liberty]. To perform the Flowave installation on WebSphere Liberty
you will need the following:

* The `flowave-ee-ibm-was-{{< minor-version >}}.0-ee` `.tar.gz` or `.zip` archive available from
  [the enterprise downloads page][ee-downloads].
* A basic understanding on [how to create a WebSphere Liberty server][liberty-server-create].
* A basic understanding on [how to deploy applications in Websphere Liberty][liberty-app-deploy]. 
* You should also consider the [WebSphere Liberty Java support documentation][liberty-java].

{{< note title="Reading this Guide" class="info" >}}
This section provides examples for Flowave `{{< minor-version >}}.0-ee`. If you are installing a patch version
please replace the version numbers in the examples with the patch version you are using.

Throughout this section we will use a number of variables to denote common path names and constants.
You don't have to create these variables in your environment. They are just used in this guide to make it more readable.

* `$WAS_DISTRIBUTION` represents the downloaded Flowave distribution for the IBM WebSphere Application Server, e.g., `flowave-ee-ibm-was-{{< minor-version >}}.0-ee.zip`.
* `$SERVER_CONFIG_DIR` points to the IBM WebSphere Liberty server configuration directory like `wlp/usr/servers/flowaveServer`.

The distribution is available at the [Flowave enterprise release page](https://downloads.camunda.cloud/enterprise-release/camunda-bpm/ibm-was-liberty).
You will be asked to enter the credentials you received during the trial or subscription process.
{{< /note >}}

To fully install Flowave on WebSphere Liberty, you need to configure the following components:

1. Flowave EAR and shared libraries
2. Datasource
3. Work Manager
4. Optional components: Flowave web apps, REST API, etc.

# Server configuration

WebSphere Liberty relies on [Liberty features][liberty-features] to make servers lightweight and composable.
A Liberty server configuration is centralized in a single `server.xml` file that exists in the `$SERVER_CONFIG_DIR`
folder.

{{< note title="WebSphere Liberty vs Open Liberty" class="info" >}}
Flowave requires certain Liberty features that are only available in the WebSphere Liberty edition. As a
result, Open Liberty is not supported by Flowave.
{{< /note >}}

{{< note title="Java EE Liberty features support" class="info" >}}
Liberty supports multiple Java/Jakarta EE versions through features with multiple versions (ex. `cdi-1.0`, `cdi-1.2`,
`cdi-2.0`, etc.).

Flowave doesn't support Java EE 6, Java EE 7, or Jakarta EE 9+ features since they don't provide all the APIs 
required to run Flowave correctly.

We recommend using Java EE 8 Liberty features to run Flowave on WebSphere Liberty.

If you decide to mix Liberty features from different Java EE versions, check the
<a href="https://www.ibm.com/docs/en/was-liberty/base?topic=architecture-supported-java-ee-7-8-feature-combinations" >Java EE feature combinations page</a>
to ensure that the all the features are inter-compatible.
{{< /note >}}

This guide will show you what to place in the `$SERVER_CONFIG_DIR` folder, and what to add in the `server.xml` file
to successfully install Flowave on WebSphere Liberty.

At the end of the installation, your Liberty server folder should have the following structure:

```
wlp/usr/servers/
|-- flowaveServer/
      |-- lib/  <-- The shared libs
           |-- flowave-engine-{{< minor-version >}}.0-ee.jar
           |-- java-uuid-generator-XX.jar
           |-- mybatis-XX.jar
           |-- joda-time-XX.jar
           |-- ...
           |-- JDBC_DRIVER.jar
      |-- apps/ <-- EAR & WAR applications
           |-- flowave-ibm-websphere-ear-{{< minor-version >}}.0-ee.ear
           |-- ..
      |-- server.xml <-- configuration file
```

Furthermore, when your Liberty server is fully configured for Flowave, the `server.xml` file should have the
following content:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<server description="flowave server">

  <!-- Enable features -->
  <featureManager>
    <feature>jdbc-4.2</feature>
    <feature>concurrent-1.0</feature>
    <feature>heritageAPIs-1.1</feature>
    <feature>mdb-3.2</feature>
    <feature>jca-1.7</feature>
    <feature>ejbRemote-3.2</feature>
    <feature>jndi-1.0</feature>
    <feature>servlet-4.0</feature>
    <feature>bells-1.0</feature>
  </featureManager>

  <!-- To access this server from a remote client add a host attribute to the following element, e.g. host="*" -->
  <httpEndpoint id="defaultHttpEndpoint"
                httpPort="9080"
                httpsPort="9443" />

  <!-- Automatically expand WAR and EAR files -->
  <applicationManager autoExpand="true" />

  <!-- Default SSL configuration enables trust for default certificates from the Java runtime -->
  <ssl id="defaultSSLConfig" trustDefaultCerts="true" />

  <!-- JDBC library definition -->
  <library id="flowaveJDBCLib">
      <fileset dir="${server.config.dir}/lib" includes="db2jcc-*.jar" />
  </library>

  <!-- Flowave libraries definition -->
  <library id="Flowave">
    <fileset dir="${server.config.dir}/lib" includes="*.jar" />
  </library>
  <bell libraryRef="Flowave"/> <!-- used to enable SPI detection -->

  <!-- Datasource Configuration -->
  <dataSource
      id="FlowaveBpmDataSource"
      jndiName="jdbc/ProcessEngine"
      type="javax.sql.DataSource"
      isolationLevel="TRANSACTION_READ_COMMITTED" >
      <jdbcDriver
          javax.sql.DataSource="com.ibm.db2.jcc.DB2SimpleDataSource"
          libraryRef="flowaveJDBCLib"/> <!-- reference to the JDBC library defined above -->
      <properties.db2.jcc
          databaseName="${database.name}"
          serverName="${database.host}"
          portNumber="${database.port}"
          user="${database.username}"
          password="${database.password}"/>
  </dataSource>

  <!-- work manager definition - the jndiName value must remain the same -->
  <managedExecutorService id="flowave-platform-jobexecutor-WM"
                          jndiName="wm/flowave-bpm-workmanager"
                          contextServiceRef="DefaultContextService">
    <concurrencyPolicy max="4" maxQueueSize="5" maxPolicy="strict"/>
  </managedExecutorService>

  <!-- required for correct ExecutorService configuration -->
  <!-- the values of the jndiName and id attributes must have these values  -->
  <connectionFactory jndiName="eis/JcaExecutorServiceConnectionFactory">
    <!-- this element must be added - required for correct classpath scanning -->
    <properties.flowave-bpm-platform.flowave-ibm-websphere-rar />
  </connectionFactory>
  <activationSpec id="eis/PlatformJobExecutorActivation">
    <!-- this element must be added - required for correct classpath scanning -->
    <properties.flowave-bpm-platform.flowave-ibm-websphere-rar />
  </activationSpec>

  <!-- applications definition -->
  <enterpriseApplication id="flowaveBpmPlatform"
                         name="flowave-bpm-platform"
                         location="${server.config.dir}/apps/flowave-ibm-websphere-ear-{{< minor-version >}}.0-ee.ear" >
    <classloader commonLibraryRef="Flowave"/>
  </enterpriseApplication>

</server>
```

The [Liberty directory location][liberty-dirs] documentation provides more details on the file structure of
the WebSphere Liberty application server, and the [Liberty server configuration][liberty-server-opts] section provides
information on all the possible configuration elements of a `server.xml` file.

The sections below provide more details for each configuration section of the `server.xml`.

# Required components

To correctly install Flowave process engine, you need to perform the following steps:

1. Configure the Flowave shared library.
2. Configure the Flowave EAR deployment.
3. Configure a datasource.
4. Configure the work manager.

## Flowave shared library

The Flowave shared library is used by all the Flowave EAR and WAR-packaged applications, as well as any process
applications you may deploy. As such, it's one of the core components of the Flowave installation, and it is
important to configure it correctly.

To configure the Flowave shared library, you need to perform the following steps:

1. Add the `.jar` artifacts from the `$WAS_DISTRIBUTION/server/lib/` folder to the
   `$SERVER_CONFIG_DIR/lib` folder.
2. Add the `bells-1.0` feature to the `server.xml`.
3. Define a `library` element in the `server.xml`.
* The `library#id` attribute should be set to `Flowave`.
* All the `.jar` artifacts should be included. See the [Liberty shared lib][liberty-lib-conf] page for more details.
4. Define a `bell` element in the `server.xml`.
* The `bell#libraryRef` attribute should be set to the value of the `library#id` attribute, i.e. `Flowave`.

Once you perform all the steps, the `server.xml` should have the following content:

```xml
<server>

  <!-- Enable features -->
  <featureManager>
    <feature>bells-1.0</feature> <!-- used to enable SPI  -->
  </featureManager>

  <!-- Flowave libraries definition -->
  <library id="Flowave">
    <fileset dir="${server.config.dir}/lib" includes="*.jar" />
  </library>
  <bell libraryRef="Flowave"/>

</server>
```

{{< note title="WebSphere Liberty SPI discovery" class="info" >}}
WebSphere Liberty doesn't perform SPI discovery by default. Since Flowave uses SPI for certain features,
you need to enable SPI discovery by adding the `bells-1.0` Liberty feature, and use the `bell` element in the
`server.xml` to specify any shared libraries that rely on SPI.
{{< /note >}}

## Flowave EAR

The flowave-ibm-websphere-ear is a Java EE application enterprise archive (EAR) providing the Flowave services.
It contains an embedded rar module. This flowave-ibm-websphere-rar module is a JCA Resource Adapter providing the
`jobexecutor` service to Flowave.

You need to perform the following steps to install the EAR archive on WebSphere Liberty:

1. Add the `flowave-ibm-websphere-ear-{{< minor-version >}}.0-ee.ear` from the `$WAS_DISTRIBUTION/server/apps` folder to
   the `$SERVER_CONFIG_DIR/apps` folder.
2. Add the `servlet-4.0` Liberty feature to the `server.xml`.
3. Define an `enterpriseApplication` element in the `server.xml`.
4. Reference the Flowave shared library in the `enterpriseApplication` inside the `server.xml`.
* The Flowave shared library is referenced by adding a `classloader` element.
5. (optional) [Configure location of the `bpm-platform.xml` file][bpm-platform-xml-config].

After performing the steps above, the `server.xml` should contain the following:

```xml
<server>

  <!-- Enable features -->
  <featureManager>
    <feature>servlet-4.0</feature>
  </featureManager>

  <!-- EAR application definition -->
  <enterpriseApplication id="flowaveBpmPlatform"
                         name="flowave-bpm-platform"
                         location="${server.config.dir}/apps/flowave-ibm-websphere-ear-{{< minor-version >}}.0-ee.ear" >
    <classloader commonLibraryRef="Flowave"/>
  </enterpriseApplication>

</server>
```

Note that if you decide to change the value of the `enterpriseApplication#name` attribute, you should adjust the
`properties` elements defined in the [Work manager](#work-manager) configuration.

You can check the [Liberty `enterpriseApplication` docs][liberty-ear] for more details on how to configure an EAR
application deployment.

## Datasource

Flowave requires a datasource that will be used by the process engine. To provide a datasource in
WebSphere Liberty, the following steps need to be performed:

1. Create the database schema and tables
1. Configure a JDBC driver library and a datasource in the `server.xml`

### Create the database schema and tables

In the default Flowave configuration, the database schema and all required tables are automatically
created when the engine starts up for the first time. If you do not want the database schema and tables to be
automatically created, you have to perform the following:

1. Create a database schema for Flowave yourself.
2. Install the database schema to create all required tables and default indices using our
  [database schema installation guide][db-schema-install].

When you create the tables manually, you have to configure the engine to **not** create tables at startup by
setting the `databaseSchemaUpdate` property to `false` (or, in case you are using Oracle, to `noop`). In WebSphere
Liberty, this is done in the `bpm-platform.xml`, located in the
`$WAS_DISTRIBUTION/server/apps/flowave-ibm-websphere-ear-{{< minor-version >}}.0-ee.ear/flowave-ibm-websphere-service.jar/META-INF/` 
folder.

### Configure a datasource

Flowave uses one or multiple process engines which must be connected to a datasource. To configure a
datasource in WebSphere Liberty, you need to perform the following:

1. Add a JDBC driver library to the `$SERVER_CONFIG_DIR/lib` folder.
2. Add the `jdbc-4.2` and `jndi-1.0` Liberty features to the `server.xml`.
3. Define a JDBC shared library in the `server.xml`.
4. Configure a datasource element in the `server.xml`.
5. Set a JNDI name for the datasource element in the `server.xml`.

For example, to use an DB2 database as the datasource, you should first place the DB2 JDBC driver `.jar` archive in
`$SERVER_CONFIG_DIR/lib`. Next, you should add the following to the `server.xml` file:

```xml
<server>

  <!-- Enable features -->
  <featureManager>
    <feature>jdbc-4.2</feature>
    <feature>jndi-1.0</feature>
  </featureManager>

  <!-- Specify JDBC shared library -->
  <library id="flowaveJDBCLib">
    <fileset dir="${server.config.dir}/lib" includes="db2jcc-*.jar" />
  </library>

  <!-- Configure datasource -->
  <dataSource
      id="FlowaveBpmDataSource"
      jndiName="jdbc/ProcessEngine"
      type="javax.sql.DataSource"
      isolationLevel="TRANSACTION_READ_COMMITTED" >
    <jdbcDriver
        javax.sql.DataSource="com.ibm.db2.jcc.DB2SimpleDataSource"
        libraryRef="flowaveJDBCLib"/>
    <properties
        URL="${database.url}"
        user="${database.username}"
        password="${database.password}" />
  </dataSource>
...
</server>
```

WebSphere Liberty provides more details on [configuring a datasource][liberty-datasource-conf] and
[shared library][liberty-lib-conf] definition.

Note that you may configure multiple datasources used by different process engine instances.
See the [User Guide]({{< ref "/user-guide/_index.md" >}}) for details.

#### Datasource JNDI name

In the XML code snippet above, you can see that the datasource JNDI name was set to `jdbc/ProcessEngine`. This is the
default datasource JNDI name used by the process engine.

The JNDI name of the datasource must be equal to the name used in the datasource-Element of the process engine(s)
configured in the `bpm-platform.xml` file.

## Work manager

Flowave uses a Job Executor to support asynchronous execution. To integrate the Flowave Job Executor
with WebSphere Liberty, Flowave relies on the `CommonJ Work manager`.

The Flowave Job Executor integration with Liberty requires the following Liberty features:

* `concurrent-1.0`
* `heritageAPIs-1.1`
* `mdb-3.2`
* `jca-1.7`
* `ejbRemote-3.2`
* `jndi-1.0`

To correctly configure the Flowave Job Executor in WebSphere Liberty, you need to define the following
elements in the `server.xml` file:

* `managedExecutorService` - used to configure the thread pool.
* `connectionFactory` - used to correctly instantiate a JCA connection factory for the executor service.
* `activationSpec` - used to correctly instantiate an MDB activation specification.

The `server.xml` should look like the following example:

```xml
<server>
  <featureManager>
    <feature>concurrent-1.0</feature>
    <feature>heritageAPIs-1.1</feature>
    <feature>mdb-3.2</feature>
    <feature>jca-1.7</feature>
    <feature>ejbRemote-3.2</feature>
    <feature>jndi-1.0</feature> <!-- if not already added -->
  </featureManager>

  <!-- work manager definition -->
  <managedExecutorService id="flowave-platform-jobexecutor-WM"
                          jndiName="wm/flowave-bpm-workmanager"
                          contextServiceRef="DefaultContextService">
    <concurrencyPolicy max="4" maxQueueSize="5" maxPolicy="strict" runIfQueueFull="false" />
  </managedExecutorService>

  <!-- required for correct ExecutorService configuration -->
  <connectionFactory jndiName="eis/JcaExecutorServiceConnectionFactory">
    <properties.flowave-bpm-platform.flowave-ibm-websphere-rar />
  </connectionFactory>
  <activationSpec id="eis/PlatformJobExecutorActivation">
    <properties.flowave-bpm-platform.flowave-ibm-websphere-rar />
  </activationSpec>

</server>
```

### Concurrency policy attributes

You can use the following attributes of the `concurrencyPolicy` element to configure the CommonJ Work manager:

<table class="table">
  <tbody>
    <tr>
      <th>Property</th>
      <th>Default Value</th>
      <th>Explanation</th>
    </tr>
    <tr>
      <td>max</td>
      <td>4</td>
      <td>Specifies the maximum number of threads that are available in this work manager used by the JobExecutor.</td>
    </tr>
    <tr>
      <td>maxQueueSize</td>
      <td>5</td>
      <td>Specifies the size of the work request queue. The work request queue is a buffer that holds scheduled work 
          objects and may be a value of 1 or greater. The thread pool pulls work from this queue. If you do not specify a 
          value or the value is 0, the queue size is managed automatically. Large values can consume significant system 
          resources.
      </td>
    </tr>
    <tr>
      <td>runIfQueueFull</td>
      <td>false</td>
      <td>Specifies the action that is taken when the thread pool is exhausted, and the work request queue is full. This 
          action starts when you submit non-daemon work to the work manager. The value should be set to <code>false</code>.
      </td>
    </tr>
  </tbody>
</table>


### Required values

The following JNDI names and IDs are mandatory, and shouldn't be changed:

<table class="table">
  <tbody>
    <tr>
      <th>Configuration element</th>
      <th>Required Value</th>
      <th>Explanation</th>
    </tr>
    <tr>
      <td><code>managedExecutorService#jndiName</code></td>
      <td><code>wm/flowave-bpm-workmanager</code></td>
      <td>The JNDI name of the Work Manager.</td>
    </tr>
    <tr>
      <td><code>connectionFactory#jndiName</code></td>
      <td><code>eis/JcaExecutorServiceConnectionFactory</code></td>
      <td>The JNDI name of the JCA ConnectionFactory used by the JobExecutor service.</td>
    </tr>
    <tr>
      <td><code>activationSpec#id</code></td>
      <td><code>eis/PlatformJobExecutorActivation</code></td>
      <td>The ID of the MDB activationSpec used for passing Jobs to the JobExecutor.</td>
    </tr>
  </tbody>
</table>

Furthermore, to correctly activate, the `connectionFactory` and `activationSpec` elements must contain the
following empty XML sub-element:

```xml
<properties.flowave-bpm-platform.flowave-ibm-websphere-rar />
```

In the code snippet above, the `flowave-bpm-platform` segment of the `properties` element is defined by the `name`
attribute of the [Flowave EAR application](#flowave-platform-ear), while the `flowave-ibm-websphere-rar` is
the name of the EAR JCA Resource Adapter flowave-ibm-websphere-rar module, and shouldn't be changed.

# Optional components

This section describes how to install optional components. None of these are required to work with the core platform.

## Cockpit, Tasklist, and Admin

The web application archive that contains Flowave Cockpit, Flowave Admin, and Flowave Tasklist resides under
`$WAS_DISTRIBUTION/server/apps/flowave-webapp-ee-was-{{< minor-version >}}.0-ee.war` in the IBM WebSphere Application Server
distribution archive.

You need to perform the following steps to install the WAR archive on WebSphere Liberty:

1. Add the `flowave-webapp-ee-was-{{< minor-version >}}.0-ee.war` from the `$WAS_DISTRIBUTION/server/apps` folder to
   the `$SERVER_CONFIG_DIR/apps` folder
2. Add the `jaxrs-2.1` Liberty feature to the `server.xml`
3. Define a `webApplication` element in the `server.xml`
4. Reference the Flowave shared library in the `webApplication` inside the `server.xml`
* The Flowave shared library is referenced by adding a `classloader` element.

After performing the steps above, the `server.xml` should contain the following:

```xml
<server>

  <!-- Enable features -->
  <featureManager>
    <feature>jaxrs-2.1</feature>
  </featureManager>

  <!-- applications definition -->
  <webApplication id="flowaveBpmPlatformWebapps"
                  name="flowave"
                  startAfterRef="flowaveBpmPlatform"
                  location="${server.config.dir}/apps/flowave-webapp-ee-was-{{< minor-version >}}.0-ee.war" >
    <classloader commonLibraryRef="Flowave"/>
  </webApplication>

</server>
```

You can check if everything went well by accessing Cockpit, Admin, and Tasklist via `/flowave/app/cockpit`,
`/flowave/app/admin`, and `/flowave/app/tasklist` or under the context path you configured.

You can check the [Liberty `webApplication` docs][liberty-webapp] for more details on how to configure a WAR
application deployment.

## REST API

The Flowave REST API WAR file resides under `$WAS_DISTRIBUTION/server/apps/flowave-engine-rest-{{< minor-version >}}.0-ee-was.war`
in the IBM WebSphere Application Server distribution archive.

You need to perform the following steps to install the WAR archive on WebSphere Liberty:

1. Add the `flowave-engine-rest-{{< minor-version >}}.0-ee-was.war` from the `$WAS_DISTRIBUTION/webapps/` folder to
   the `$SERVER_CONFIG_DIR/apps` folder.
2. Add the `jaxrs-2.1` and `beanValidation-2.0` Liberty features to the `server.xml`.
3. Define a `webApplication` element in the `server.xml`.
4. Reference the Flowave shared library in the `webApplication` inside the `server.xml`.
* The Flowave shared library is referenced by adding a `classloader` element.

After performing the steps above, the `server.xml` should contain the following:

```xml
<server>

  <!-- Enable features -->
  <featureManager>
    <feature>jaxrs-2.1</feature> <!-- if not already added -->
    <feature>beanValidation-2.0</feature>
  </featureManager>

  <!-- applications definition -->
  <webApplication id="flowaveBpmPlatformRestApi"
                  name="engine-rest"
                  startAfterRef="flowaveBpmPlatform"
                  location="${server.config.dir}/apps/flowave-engine-rest-{{< minor-version >}}.0-ee-was.war" >
    <classloader commonLibraryRef="Flowave"/>
  </webApplication>

</server>
```

You can check the [Liberty `webApplication` docs][liberty-webapp] for more details on how to configure a WAR
application deployment.

## Flowave Connect plugin

Add the following artifacts (if not existing) from the folder `$WAS_DISTRIBUTION/server/lib` to the
`$SERVER_CONFIG_DIR/lib` folder:

* `flowave-commons-utils-$PLATFORM_VERSION.jar`

In order to activate Flowave Connect functionality for a process engine, you need to register a process engine plugin
in the Flowave configuration as follows:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<bpm-platform ... >
  <process-engine name="default">
    ...
    <plugins>
      ... existing plugins ...
      <plugin>
        <class>org.finos.flowave.connect.plugin.impl.ConnectProcessEnginePlugin</class>
      </plugin>
    </plugins>
    ...
  </process-engine>

</bpm-platform>
```

Note that this requires a custom `bpm-platform.xml` file.

## Flowave Spin

Add the following artifacts (if not existing) from the folder `$WAS_DISTRIBUTION/server/lib/` to the
`$SERVER_CONFIG_DIR/lib` folder:

* `flowave-spin-core-$PLATFORM_VERSION.jar`
* `flowave-commons-utils-$PLATFORM_VERSION.jar`

In order to activate Flowave Spin functionality for a process engine, you need to register a process engine plugin
in the Flowave configuration as follows:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<bpm-platform ...>
  ...
  <process-engine name="default">
    ...
    <plugins>
      ... existing plugins ...
      <plugin>
        <class>org.finos.flowave.spin.plugin.impl.SpinProcessEnginePlugin</class>
      </plugin>
    </plugins>
    ...
  </process-engine>
  ...
</bpm-platform>
```

Note that this requires a custom `bpm-platform.xml` file.

## Groovy scripting

Add the following artifacts (if not existing) from the folder `$WAS_DISTRIBUTION/server/lib/` to the
`$SERVER_CONFIG_DIR/lib` folder:

* `groovy-$GROOVY_VERSION.jar`
* `groovy-jsr223-$GROOVY_VERSION.jar`
* `groovy-json-$GROOVY_VERSION.jar`
* `groovy-xml-$GROOVY_VERSION.jar`
* `groovy-templates-$GROOVY_VERSION.jar`
* `groovy-dateutil-$GROOVY_VERSION.jar`
* `groovy-datetime-$GROOVY_VERSION.jar`

## GraalVM JavaScript integration

Add the following artifacts (if not existing) from the folder `$WAS_DISTRIBUTION/server/lib/` to the
`$SERVER_CONFIG_DIR/lib` folder:

* `graal-sdk-21.1.0.jar`
* `icu4j-68.2.jar`
* `js-21.1.0.jar`
* `js-scriptengine-21.1.0.jar`
* `regex-21.1.0.jar`
* `truffle-api-21.1.0.jar`

# Process Applications

After installing a Process Application (PA) in your IBM WebSphere Liberty Server, which **does not** include the
Flowave dependencies, you must reference the previously created [**"Flowave"** shared library](#flowave-shared-library)
with the Process Application deployment.

Your Process Application deployment in the `server.xml` file should look like the following example:

```xml
<server>

  <!-- process application definition -->
  <application id="flowavePA"
               type="..."
               name="flowave-process-application"
               startAfterRef="flowaveBpmPlatform"
               location="${server.config.dir}/apps/flowave-process-application.ear" >
    <classloader commonLibraryRef="Flowave"/>
  </application>

</server>
```

You can check the [Liberty `application` docs][liberty-app] for more details on how to configure an application
deployment.

[liberty]: https://www.ibm.com/cloud/websphere-liberty
[liberty-java]: https://www.ibm.com/docs/en/was-liberty/base?topic=liberty-runtime-environment-known-restrictions#d127870e215
[liberty-server-create]: https://www.ibm.com/docs/en/was-liberty/base?topic=liberty-creating-server-manually
[liberty-app-deploy]: https://www.ibm.com/docs/en/was-liberty/base?topic=deploying-applications-in-liberty
[liberty-features]: https://www.ibm.com/docs/en/was-liberty/core?topic=management-liberty-features
[liberty-dirs]: https://www.ibm.com/docs/en/was-liberty/base?topic=liberty-directory-locations-properties
[liberty-server-opts]: https://www.ibm.com/docs/en/was-liberty/core?topic=overview-server-configuration
[liberty-ear]: https://www.ibm.com/docs/en/was-liberty/core?topic=configuration-enterpriseapplication
[liberty-webapp]: https://www.ibm.com/docs/en/was-liberty/core?topic=configuration-webapplication
[liberty-app]: https://www.ibm.com/docs/en/was-liberty/base?topic=deploying-applications-in-liberty
[liberty-lib-conf]: https://www.ibm.com/docs/en/was-liberty/base?topic=overview-shared-libraries
[liberty-datasource-opts]: https://www.ibm.com/docs/en/was-liberty/base?topic=SSEQTP_liberty/com.ibm.websphere.liberty.autogen.nd.doc/ae/rwlp_config_dataSource.html
[liberty-datasource-conf]: https://www.ibm.com/docs/en/was-liberty/base?topic=liberty-configuring-default-data-source

[ee-downloads]: /enterprise/download
[bpm-platform-xml-config]: {{< ref "/reference/deployment-descriptors/descriptors/bpm-platform-xml.md#configure-location-of-the-bpm-platform-xml-file" >}}
[db-schema-install]: {{< ref "/installation/database-schema.md" >}}