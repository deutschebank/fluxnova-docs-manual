---

title: "Update an IBM Websphere Installation from 7.14 to 7.15"

menu:
  main:
    name: "WebSphere"
    identifier: "migration-guide-715-was"
    parent: "migration-guide-715"

---


The following steps describe how to update the Flowave artifacts on an IBM WebSphere application server in a shared process engine setting. For the entire procedure, refer to the [update guide][update-guide]. If not already done, make sure to download the [Flowave.15 IBM WebSphere distribution](https://artifacts.camunda.com/artifactory/camunda-bpm-ee/org/finos/flowave/bpm/websphere/camunda-bpm-websphere/7.15.0-ee/).

The update procedure takes the following steps:

1. Uninstall the Flowave Libraries and Archives
2. Replace Flowave Core Libraries
3. Replace Optional Flowave Dependencies
4. Maintain the Flowave Configuration
5. Install the Flowave Archive
6. Install the Web Applications

In each of the following steps, the identifier `$*_VERSION` refers to the current versions and the new versions of the artifacts.

# 1. Uninstall the Flowave Libraries and Archives

First, uninstall the Flowave web applications, namely the Flowave REST API (artifact name like `flowave-engine-rest`) and the Flowave applications Cockpit, Tasklist and Admin (artifact name like `flowave-webapp`).

Uninstall the Flowave EAR. Its name should be `flowave-ibm-websphere-ear-$PLATFORM_VERSION.ear`.

# 2. Replace Flowave Core Libraries

With your first Flowave installation or update to 7.2, you have created a shared library named `Flowave`. We identify the folder to this shared library as `$SHARED_LIBRARY_PATH`.

After shutting down the server, replace the following libraries in `$SHARED_LIBRARY_PATH` with their equivalents from `$WAS_DISTRIBUTION/modules/lib`:

* `flowave-engine-$PLATFORM_VERSION.jar`
* `flowave-bpmn-model-$PLATFORM_VERSION.jar`
* `flowave-cmmn-model-$PLATFORM_VERSION.jar`
* `flowave-dmn-model-$PLATFORM_VERSION.jar`
* `flowave-xml-model-$PLATFORM_VERSION.jar`
* `flowave-engine-dmn-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-api-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-juel-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-scala-$PLATFORM_VERSION.jar`
* `flowave-commons-logging-$COMMONS_VERSION.jar`
* `flowave-commons-typed-values-$PLATFORM_VERSION.jar`
* `flowave-commons-utils-$COMMONS_VERSION.jar`
* `flowave-connect-connectors-all-$CONNECT_VERSION.jar`
* `flowave-connect-core-$CONNECT_VERSION.jar`
* `flowave-template-engines-freemarker-$TEMPLATE_ENGINES_VERSION.jar`
* `feel-engine-$FEEL_ENGINE_VERSION-scala-shaded.jar`
* `freemarker-$FREEMARKER_VERSION.jar`
* `mybatis-$MYBATIS_VERSION.jar`

# 3. Replace Optional Flowave Dependencies

In addition to the core libraries, there may be optional artifacts in `$SHARED_LIBRARY_PATH` for LDAP integration, Flowave Spin, and Flowave Connect. If you use any of these extensions, the following update steps apply:

## LDAP integration

Copy the following library from `$WAS_DISTRIBUTION/modules/lib` to the folder `$SHARED_LIBRARY_PATH`, if present:

* `flowave-identity-ldap-$PLATFORM_VERSION.jar`

## Flowave Connect Plugin

`flowave-connect-connectors-all` and `flowave-engine-plugin-connect` are part of the .ear

## Flowave Spin

Copy the following library from `$WAS_DISTRIBUTION/modules/lib` to the folder `$SHARED_LIBRARY_PATH`, if present:

* `flowave-spin-core-$SPIN_VERSION.jar`


# 4. Maintain the Flowave Configuration

If you have previously replaced the default Flowave configuration with a custom configuration following any of the ways outlined in the [deployment descriptor reference][configuration-location], it may be necessary to restore this configuration. This can be done by repeating the configuration replacement steps for the updated platform.

# 5. Install the Flowave Archive

Install the Flowave EAR, i.e., the file `$WAS_DISTRIBUTION/modules/flowave-ibm-websphere-ear-$PLATFORM_VERSION.ear`. During the installation, the EAR will try to reference the `Flowave` shared library.

# 6. Install the Web Applications

## REST API

The following steps are required to update the Flowave REST API on an IBM WebSphere instance:

1. Deploy the web application `$WAS_DISTRIBUTION/webapps/flowave-engine-rest-$PLATFORM_VERSION-was.war` to your IBM WebSphere instance.
2. Associate the web application with the `Flowave` shared library.

## Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on an IBM WebSphere instance:

1. Deploy the web application `$WAS_DISTRIBUTION/webapps/flowave-webapp-ee-was-$PLATFORM_VERSION.war` to your IBM WebSphere instance.
2. Associate the web application with the `Flowave` shared library.

[configuration-location]: {{< ref "/reference/deployment-descriptors/descriptors/bpm-platform-xml.md" >}}
[update-guide]: {{< ref "/update/minor/714-to-715/_index.md" >}}
