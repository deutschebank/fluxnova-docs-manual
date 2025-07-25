---

title: "Update a Tomcat Installation from 7.20 to 7.21"

menu:
  main:
    name: "Tomcat"
    identifier: "migration-guide-721-tomcat"
    parent: "migration-guide-721"

---

The following steps describe how to update the Flowave artifacts on a Tomcat server in a shared process engine setting.

Throughout the procedure, refer to the [update guide][update-guide]. If not already done, download the
[Flowave.21 Tomcat distribution][tomcat-distribution].

The update procedure takes the following steps:

1. Update the Flowave core libraries.
2. Update optional Flowave libraries.
3. Update web applications.

In each of the following steps, the identifier `$*_VERSION` refers to the current versions and the new versions of the artifacts.

# 1. Update the Flowave core libraries

Replace the following libraries in the folder `$TOMCAT_HOME/lib/` with the new versions from the folder `$TOMCAT_DISTRIBUTION/lib/`:

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

# 2. Update optional Flowave libraries

In addition to the core libraries, there may be optional artifacts in `$TOMCAT_HOME/lib/` for LDAP integration, Flowave Connect, Flowave Spin, and scripting. If you use any of these extensions, the following update steps apply:

## LDAP integration

Copy the following library from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `flowave-identity-ldap-$PLATFORM_VERSION.jar`

## Flowave Connect plugin

Copy the following libraries from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `flowave-engine-plugin-connect-$PLATFORM_VERSION.jar`

## Flowave Spin

Copy the following libraries from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `flowave-spin-dataformat-all-$SPIN_VERSION.jar`
* `flowave-spin-core-$SPIN_VERSION.jar`
* `flowave-engine-plugin-spin-$PLATFORM_VERSION.jar`

## GraalVM JavaScript

Copy the following libraries from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `graal-sdk-21.1.0.jar`
* `icu4j-68.2.jar`
* `js-21.1.0.jar`
* `js-scriptengine-21.1.0.jar`
* `regex-21.1.0.jar`
* `truffle-api-21.1.0.jar`

## Groovy

The following libraries replace the single `groovy-all-$GROOVY_VERSION.jar` library. Copy these libraries from
`$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `groovy-$GROOVY_VERSION.jar`
* `groovy-jsr223-$GROOVY_VERSION.jar`
* `groovy-json-$GROOVY_VERSION.jar`
* `groovy-xml-$GROOVY_VERSION.jar`
* `groovy-templates-$GROOVY_VERSION.jar`

# 3. Update web applications

## Update REST API

The following steps are required to update the Flowave REST API on a Tomcat instance:

1. Undeploy an existing web application with a name like `flowave-engine-rest`.
2. Download the REST API web application archive from our [Artifact Repository][artifact-repository-restapi] Alternatively, switch to the private repository for the enterprise version (credentials from license required). Choose the correct version named `$PLATFORM_VERSION/flowave-engine-rest-$PLATFORM_VERSION-tomcat.war`.
3. Deploy the web application archive to your Tomcat instance.

## Update Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on a Tomcat instance:

1. Undeploy an existing web application with a name like `flowave-webapp`.
2. Download the Flowave web application archive from our [Artifact Repository][artifact-repository-webapp]. Alternatively, switch to the private repository for the enterprise version (credentials from license required). Choose the correct version named `$PLATFORM_VERSION/flowave-webapp-tomcat-$PLATFORM_VERSION.war`.
3. Deploy the web application archive to your Tomcat instance.

[update-guide]: {{< ref "/update/minor/720-to-721/_index.md" >}}
[artifact-repository-restapi]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/7.21.0/camunda-engine-rest-7.21.0-tomcat.war
[artifact-repository-webapp]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-tomcat/7.21.0/camunda-webapp-tomcat-7.21.0.war
[tomcat-distribution]: https://downloads.camunda.cloud/release/camunda-bpm/tomcat/7.21/
