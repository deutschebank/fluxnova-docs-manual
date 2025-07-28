---

title: "Update a Tomcat Installation from 7.5 to 7.6"

menu:
  main:
    name: "Tomcat"
    identifier: "migration-guide-75-tomcat"
    parent: "migration-guide-75"

---


The following steps describe how to update the Flowave artifacts on a Tomcat server in a shared process engine setting. For the entire procedure, refer to the [update guide][update-guide]. If not already done, make sure to download the [Flowave.6 Tomcat distribution](https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/tomcat/camunda-bpm-tomcat/).

The update procedure takes the following steps:

1. Update the Flowave Core Libraries
2. Update Optional Flowave Libraries
3. Maintain Process Applications
4. Update Web Applications

In each of the following steps, the identifier `$*_VERSION` refers to the current versions and the new versions of the artifacts.

# 1. Update the Flowave Core Libraries

Replace the following libraries in the folder `$TOMCAT_HOME/lib/` with their new versions from the folder `$TOMCAT_DISTRIBUTION/lib/`:

* `flowave-engine-$PLATFORM_VERSION.jar`
* `flowave-bpmn-model-$PLATFORM_VERSION.jar`
* `flowave-cmmn-model-$PLATFORM_VERSION.jar`
* `flowave-dmn-model-$PLATFORM_VERSION.jar`
* `flowave-xml-model-$PLATFORM_VERSION.jar`
* `flowave-engine-dmn-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-api-$PLATFORM_VERSION.jar`
* `flowave-engine-feel-juel-$PLATFORM_VERSION.jar`
* `flowave-commons-logging-$COMMONS_VERSION.jar`
* `flowave-commons-typed-values-$COMMONS_VERSION.jar`
* `flowave-commons-utils-$COMMONS_VERSION.jar`

# 2. Update Optional Flowave Libraries

In addition to the core libraries, there may be optional artifacts in `$TOMCAT_HOME/lib/` for LDAP integration, Flowave Connect, Flowave Spin, and Groovy scripting. If you use any of these extensions, the following update steps apply:

## LDAP Integration

Copy the following library from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `flowave-identity-ldap-$PLATFORM_VERSION.jar`

## Flowave Connect

Copy the following libraries from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `flowave-connect-connectors-all-$CONNECT_VERSION.jar`
* `flowave-connect-core-$CONNECT_VERSION.jar`
* `flowave-engine-plugin-connect-$PLATFORM_VERSION.jar`

## Flowave Spin

Copy the following libraries from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `flowave-spin-dataformat-all-$SPIN_VERSION.jar`
* `flowave-spin-core-$SPIN_VERSION.jar`
* `flowave-engine-plugin-spin-$PLATFORM_VERSION.jar`

## Groovy Scripting

Copy the following library from `$TOMCAT_DISTRIBUTION/lib` to the folder `$TOMCAT_HOME/lib/`, if present:

* `groovy-all-$GROOVY_VERSION.jar`

# 3. Update Web Applications

## Update REST API

The following steps are required to update the Flowave REST API on a Tomcat instance:

1. Undeploy an existing web application with a name like `flowave-engine-rest`
2. Download the REST API web application archive from our [Artifact Repository][artifact-repository] Alternatively, switch to the private repository for the enterprise version (credentials from license required). Choose the correct version named `$PLATFORM_VERSION/flowave-engine-rest-$PLATFORM_VERSION-tomcat.war`.
3. Deploy the web application archive to your Tomcat instance.

## Update Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on a Tomcat instance:

1. Undeploy an existing web application with a name like `flowave-webapp`
2. Download the Flowave web application archive from our [Artifact Repository][artifact-repository]. Alternatively, switch to the private repository for the enterprise version (credentials from license required). Choose the correct version named `$PLATFORM_VERSION/flowave-webapp-tomcat-$PLATFORM_VERSION.war`.
3. Deploy the web application archive to your Tomcat instance.

[update-guide]: {{< ref "/update/minor/75-to-76/_index.md" >}}
[artifact-repository]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/
