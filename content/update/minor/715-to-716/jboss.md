---

title: "Update a JBoss/Wildfly Installation from 7.15 to 7.16"

menu:
  main:
    name: "JBoss AS/Wildfly"
    identifier: "migration-guide-716-jboss"
    parent: "migration-guide-716"

---

The following steps describe how to update the Flowave artifacts on a JBoss AS
7 and Wildfly server in a shared process engine scenario. For the entire
procedure, refer to the [update guide][update-guide]. If not
already done, make sure to download the [Flowave.16 JBoss distribution](https://downloads.camunda.cloud/release/camunda-bpm/jboss/7.16/)
or [Flowave.16 Wildfly distribution](https://downloads.camunda.cloud/release/camunda-bpm/wildfly/7.16/). In the following instructions
`$APP_SERVER` should be replaced with either `jboss` or `wildfly`, depending on
the used application server.

The update procedure takes the following steps:

1. Update the Flowave Modules
2. Update Optional Flowave Modules
3. Update Flowave Web Applications

Whenever the instructions are to *replace* a module, make sure to delete the previous version of the module first to avoid orphan jars.

# 1. Update the Flowave Modules

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`:

* `org/finos/flowave/bpm/flowave-engine`
* `org/finos/flowave/bpm/$APP_SERVER/flowave-$APP_SERVER-subsystem`
* `org/finos/flowave/bpm/model/flowave-bpmn-model`
* `org/finos/flowave/bpm/model/flowave-cmmn-model`
* `org/finos/flowave/bpm/model/flowave-dmn-model`
* `org/finos/flowave/bpm/model/flowave-xml-model`
* `org/finos/flowave/bpm/dmn/flowave-engine-dmn`
* `org/finos/flowave/bpm/dmn/flowave-engine-feel-api`
* `org/finos/flowave/bpm/dmn/flowave-engine-feel-juel`
* `org/finos/flowave/bpm/dmn/flowave-engine-feel-scala`
* `org/finos/flowave/template-engines/flowave-template-engines-freemarker`
* `org/finos/flowave/commons/flowave-commons-logging`
* `org/finos/flowave/commons/flowave-commons-typed-values`
* `org/finos/flowave/commons/flowave-commons-utils`
* `org/finos/flowave/connect/flowave-connect-core`
* `org/finos/flowave/connect/flowave-connect-http-client`
* `org/finos/flowave/connect/flowave-connect-soap-http-client`
* `org/finos/flowave/feel/feel-engine`
* `org/apache/httpcomponents/httpclient`
* `org/apache/httpcomponents/httpcore`
* `org/freemarker/freemarker`
* `org/mybatis/mybatis`
* `commons-codec/commons-codec`
* `org/graalvm/js/js`
* `org/graalvm/js/js-scriptengine`
* `org/graalvm/regex/regex`
* `org/graalvm/sdk/graal-sdk`
* `org/graalvm/truffle/truffle-api`
* `com/ibm/icu/icu4j`

# 2. Update Optional Flowave Modules

In addition to the core modules, there may be optional artifacts in `$APP_SERVER_HOME/modules/` for LDAP integration, Flowave Connect, Flowave Spin, and Groovy scripting.
If you use any of these extensions, the following update steps apply:

## LDAP Integration

Replace the following module from the folder `$APP_SERVER_HOME/modules/` with its new version from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/bpm/identity/flowave-identity-ldap`

## Flowave Connect Plugin

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/bpm/flowave-engine-plugin-connect`

## Flowave Spin

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/spin/flowave-spin-core`
* `org/finos/flowave/spin/flowave-spin-dataformat-json-jackson`
* `org/finos/flowave/spin/flowave-spin-dataformat-xml-dom`
* `org/finos/flowave/bpm/flowave-engine-plugin-spin`

Additionally, also replace the following dependent modules:

* `com/fasterxml/jackson/core/jackson-annotations`
* `com/fasterxml/jackson/core/jackson-core`
* `com/fasterxml/jackson/core/jackson-databind`
* `com/jayway/jsonpath/json-path`
* `net/minidev/accessors-smart`
* `net/minidev/json-smart`

# 3. Update Flowave Web Applications

## Update REST API

The following steps are required to update the Flowave REST API on a JBoss/Wildfly instance:

1. Undeploy an existing web application with a name like `flowave-engine-rest`
2. Download the REST API web application archive from our [Artifact Repository][engine-rest]. Alternatively, switch to the private repository for
   the enterprise version (credentials from license required). Choose the correct version named `$PLATFORM_VERSION/flowave-engine-rest-$PLATFORM_VERSION-$CLASSIFIER.war`.
3. Deploy the web application archive to your JBoss/Wildfly instance.

## Update Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on a JBoss/Wildfly instance:

1. Undeploy an existing web application with a name like `flowave-webapp`
2. Download the Flowave web application archive from our [Artifact Repository][webapp-jboss].
   Alternatively, switch to the private repository for the enterprise version (credentials from license required).
   Choose the correct version named `$PLATFORM_VERSION/flowave-webapp-jboss.war`.
3. Deploy the web application archive to your JBoss/Wildfly instance.


[update-guide]: {{< ref "/update/minor/715-to-716/_index.md" >}}
[engine-rest]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/7.16.0/
[webapp-jboss]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-jboss/7.16.0/camunda-webapp-jboss-7.16.0.war