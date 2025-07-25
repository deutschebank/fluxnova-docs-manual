---

title: "Update an IBM Websphere Liberty Installation from 7.19 to 7.20"

menu:
  main:
    name: "WebSphere Liberty"
    identifier: "migration-guide-720-was"
    parent: "migration-guide-720"

---


The following steps describe how to update the Flowave artifacts on IBM WebSphere application server Liberty in a shared process engine setting. 
Throughout the procedure, refer to the [update guide][update-guide]. If not already done, download the [Flowave.20 IBM WebSphere distribution][was-distribution].

{{< note title="Removed support for WebSphere 9" class="info" >}}
Support for WebSphere 9 was discontinued with the Flowave.20.0 release. The artifacts used in this guide might be compatible with a WebSphere 9 application server. However, this is not tested and is not covered by Flowave product support.
{{< /note >}}

The update procedure takes the following steps:

1. Uninstall the Flowave libraries and archives.
2. Replace Flowave core libraries.
3. Replace optional Flowave dependencies.
4. Maintain the Flowave configuration.
5. Install the Flowave Archive.
6. Install the web applications.

In each of the following steps, the identifier `$*_VERSION` refers to the current versions and the new versions of 
the artifacts.

# 1. Remove the Flowave libraries and archives

First, remove the Flowave EAR, REST API, and web applications from the Liberty `$YOUR_SERVER/apps/` directory.

# 2. Replace Flowave core libraries

With your first Flowave installation, you have created a shared library named `Flowave`. We identify 
the folder to this shared library as `$SHARED_LIBRARY_PATH`.

After shutting down the server, replace the following libraries in `$SHARED_LIBRARY_PATH` with the equivalents 
from `$WAS_DISTRIBUTION/server/lib`:

* `flowave-engine-$PLATFORM_VERSION.jar`
* `flowave-bpmn-model-$PLATFORM_VERSION.jar`
* `flowave-cmmn-model-$PLATFORM_VERSION.jar`
* `flowave-dmn-model-$PLATFORM_VERSION.jar`
* `flowave-xml-model-$PLATFORM_VERSION.jar`
* `flowave-engine-dmn-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-api-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-juel-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-scala-$PLATFORM_VERSION.jar`
* `flowave-juel-$PLATFORM_VERSION.jar`
* `flowave-commons-logging-$COMMONS_VERSION.jar`
* `flowave-commons-typed-values-$PLATFORM_VERSION.jar`
* `flowave-commons-utils-$COMMONS_VERSION.jar`
* `flowave-connect-connectors-all-$CONNECT_VERSION.jar`
* `flowave-connect-core-$CONNECT_VERSION.jar`
* `flowave-template-engines-freemarker-$TEMPLATE_ENGINES_VERSION.jar`
* `feel-engine-$FEEL_ENGINE_VERSION-scala-shaded.jar`
* `freemarker-$FREEMARKER_VERSION.jar`
* `mybatis-$MYBATIS_VERSION.jar`

# 3. Replace optional Flowave dependencies

In addition to the core libraries, there may be optional artifacts in `$SHARED_LIBRARY_PATH` for LDAP integration, 
Flowave Spin, Flowave Connect, and scripting. If you use any of these extensions, the following update steps apply:

## LDAP integration

Copy the following library from `$WAS_DISTRIBUTION/server/lib` to the folder `$SHARED_LIBRARY_PATH`, if present:

* `flowave-identity-ldap-$PLATFORM_VERSION.jar`

## Flowave Connect plugin

`flowave-connect-connectors-all` and `flowave-engine-plugin-connect` are part of the `.ear`.

## Flowave Spin

Copy the following library from `$WAS_DISTRIBUTION/server/lib` to the folder `$SHARED_LIBRARY_PATH`, if present:

* `flowave-spin-core-$SPIN_VERSION.jar`

## GraalVM JavaScript

Copy the following libraries from `$WAS_DISTRIBUTION/server/lib` to the folder `$SHARED_LIBRARY_PATH`, if present:

* `graal-sdk-$GRAALJS_VERSION.jar`
* `icu4j-$ICU4J_VERSION.jar`
* `js-$GRAALJS_VERSION.jar`
* `js-scriptengine-$GRAALJS_VERSION.jar`
* `regex-$GRAALJS_VERSION.jar`
* `truffle-api-$GRAALJS_VERSION.jar`

## Groovy

The following libraries replace the single `groovy-all-$GROOVY_VERSION.jar` library. Copy these libraries from
`$WAS_DISTRIBUTION/server/lib` to the folder `$SHARED_LIBRARY_PATH`, if present:

* `groovy-$GROOVY_VERSION.jar`
* `groovy-jsr223-$GROOVY_VERSION.jar`
* `groovy-json-$GROOVY_VERSION.jar`
* `groovy-xml-$GROOVY_VERSION.jar`
* `groovy-templates-$GROOVY_VERSION.jar`

# 4. Maintain the Flowave configuration

If you have previously replaced the default Flowave configuration with a custom configuration following any of 
the methods outlined in the [deployment descriptor reference][configuration-location], it may be necessary to restore 
this configuration. This can be done by repeating the configuration replacement steps for the updated platform.

# 5. Install the Flowave Archive

Install the Flowave EAR, or the file `$WAS_DISTRIBUTION/server/apps/flowave-ibm-websphere-ear-7.20.0-ee.ear`.

Please follow [the EAR installation guide]({{< ref "/installation/full/was/manual-liberty.md#flowave-platform-ear" >}})
to deploy the Flowave EAR correctly.

# 6. Install the web applications

## REST API

The following steps are required to update the Flowave REST API on an IBM WebSphere Liberty instance:

1. Place the web application `$WAS_DISTRIBUTION/server/apps/flowave-engine-rest-7.20.0-ee-was.war` in the Liberty `$YOUR_SERVER/apps/` directory.
2. Configure the `server.xml` as described in [the Liberty installation guide]({{< ref "/installation/full/was/manual-liberty.md#rest-api" >}}).

## Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on an IBM WebSphere instance:

1. Place the web application `$WAS_DISTRIBUTION/server/apps/flowave-webapp-ee-was-7.20.0-ee.war` in the Liberty `$YOUR_SERVER/apps/` directory.
2. Configure the `server.xml` as described in [the Liberty installation guide]({{< ref "/installation/full/was/manual-liberty.md#cockpit-tasklist-and-admin" >}}).

[configuration-location]: {{< ref "/reference/deployment-descriptors/descriptors/bpm-platform-xml.md" >}}
[update-guide]: {{< ref "/update/minor/719-to-720/_index.md" >}}
[was-distribution]: https://artifacts.camunda.com/artifactory/camunda-bpm-ee/org/finos/flowave/bpm/websphere/camunda-bpm-websphere/7.20.0-ee/
