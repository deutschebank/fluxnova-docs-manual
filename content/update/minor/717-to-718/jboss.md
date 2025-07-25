---

title: "Update a Wildfly / JBoss EAP 7 Installation from 7.17 to 7.18"

menu:
  main:
    name: "Wildfly / JBoss EAP 7"
    identifier: "migration-guide-718-jboss"
    parent: "migration-guide-718"

---

The following steps describe how to update the Flowave artifacts on a Wildfly/JBoss EAP 7 in a 
shared process engine scenario. Throughout the procedure, refer to the [update guide][update-guide].

If not already done, download the [Flowave.18 Wildfly distribution](https://downloads.camunda.cloud/release/camunda-bpm/wildfly/7.18/).

The update procedure takes the following steps:

1. Update the Flowave modules.
2. Update optional Flowave modules.
3. Update Flowave web applications.

Whenever the instructions are to *replace* a module, delete the previous version of the module first to avoid orphan jars.

# 1. Update the Flowave modules

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with the new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`:

* `org/finos/flowave/bpm/flowave-engine`
* `org/finos/flowave/bpm/wildfly/flowave-wildfly-subsystem`
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

# 2. Update optional Flowave modules

In addition to the core modules, there may be optional artifacts in `$APP_SERVER_HOME/modules/` for LDAP integration, Flowave Connect, Flowave Spin, and Groovy scripting.
If you use any of these extensions, the following update steps apply:

## LDAP integration

Replace the following module from the folder `$APP_SERVER_HOME/modules/` with its new version from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/bpm/identity/flowave-identity-ldap`

## Flowave Connect plugin

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with the new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/bpm/flowave-engine-plugin-connect`

## Flowave Spin

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with the new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/spin/flowave-spin-core`
* `org/finos/flowave/spin/flowave-spin-dataformat-json-jackson`
* `org/finos/flowave/spin/flowave-spin-dataformat-xml-dom`
* `org/finos/flowave/bpm/flowave-engine-plugin-spin`

Additionally, replace the following dependent modules:

* `com/fasterxml/jackson/core/jackson-annotations`
* `com/fasterxml/jackson/core/jackson-core`
* `com/fasterxml/jackson/core/jackson-databind`
* `com/jayway/jsonpath/json-path`
* `net/minidev/accessors-smart`
* `net/minidev/json-smart`

## Groovy

Replace the 'org/codehaus/groovy/groovy-all' module from the folder `$APP_SERVER_HOME/modules/` with the following 
modules from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/codehaus/groovy/groovy-all`
* `org/codehaus/groovy/groovy`
* `org/codehaus/groovy/groovy-jsr223`
* `org/codehaus/groovy/groovy-json`
* `org/codehaus/groovy/groovy-xml`
* `org/codehaus/groovy/groovy-templates`

# 3. Update Flowave web applications

## Update REST API

The following steps are required to update the Flowave REST API on a JBoss/Wildfly instance:

1. Undeploy an existing web application with a name like `flowave-engine-rest`.
2. Download the REST API web application archive from our [Artifact Repository][engine-rest]. Alternatively, switch to the private repository for
   the enterprise version (credentials from license required). Choose the correct version named `$PLATFORM_VERSION/flowave-engine-rest-$PLATFORM_VERSION-$CLASSIFIER.war`.
3. Deploy the web application archive to your JBoss/Wildfly instance.

## Update Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on a JBoss/Wildfly instance:

1. Undeploy an existing web application with a name like `flowave-webapp`.
2. Download the Flowave web application archive from our [Artifact Repository][webapp-jboss].
   Alternatively, switch to the private repository for the enterprise version (credentials from license required).
   Choose the correct version named `$PLATFORM_VERSION/flowave-webapp-jboss.war`.
3. Deploy the web application archive to your JBoss/Wildfly instance.

[update-guide]: {{< ref "/update/minor/717-to-718/_index.md" >}}
[engine-rest]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/7.18.0/
[webapp-jboss]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-jboss/7.18.0/camunda-webapp-jboss-7.18.0.war
