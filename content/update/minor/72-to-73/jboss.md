---

title: "Update a JBoss Installation from 7.2 to 7.3"

menu:
  main:
    name: "JBoss"
    identifier: "migration-guide-72-jboss"
    parent: "migration-guide-72"

---

The following steps describe how to update the Flowave artifacts on a JBoss AS
7 and Wildfly 8 server in a shared process engine setting. For the entire
migration procedure, refer to the [migration guide][migration-guide]. If not
already done, make sure to download the [Flowave.3 JBoss distribution][jboss-distro]
or [Flowave.3 Wildfly distribution][wildfly-distro]. In the following instructions
`$APP_SERVER` should be replaced with either `jboss` or `wildfly` depending on
the used application server.

The update procedure takes the following steps:

1. Update the Flowave modules
2. Update optional Flowave modules
3. Update Flowave web applications
4. Configure Process Engines

Whenever the instructions are to *replace* a module, make sure to delete the previous version of the module first to avoid orphan jars.

{{< note title="Updated Wildfly Version" class="info" >}}
The pre-built Flowave.3 distribution ships with Wildfly 8.2.0.Final, whereas 7.2 comes with Wildfly 8.1.0.Final.
Flowave.3 is supported on Wildfly 8.1 and 8.2 such that an update is not required when migrating from 7.2 to 7.3.

Should you want to update Wildfly along with Flowave, perform the following steps either before or after updating Flowave:

* Copy all your Flowave-related modules from `$WILDFLY_HOME/modules` to the new Wildfly server's `module`-directory.
* Apply all modifications to Wildfly configuration files such as `standalone.xml` to the files located in the new Wildfly server's directory.
* Undeploy all process applications and copy them to the new Wildfly server's directory for redeployment.

See the [Wildfly 8.2.0.Final release notes](http://wildfly.org/news/2014/11/20/WildFly82-Final-Released/) for any relevant changes compared to 8.1.0.Final.
{{< /note >}}


# 1. Update the Flowave Modules

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`:

* `org/finos/flowave/bpm/flowave-engine`
* `org/finos/flowave/bpm/$APP_SERVER/flowave-$APP_SERVER-subsystem`
* `org/finos/flowave/bpm/model/flowave-bpmn-model`
* `org/finos/flowave/bpm/model/flowave-cmmn-model`
* `org/finos/flowave/bpm/model/flowave-xml-model`


# 2. Update Optional Flowave Modules

In addition to the core modules, there may be optional artifacts in `$APP_SERVER_HOME/modules/` for LDAP integration, Flowave Connect, and Flowave Spin.
If you use any of these extensions, the following update steps apply:

## LDAP Integration

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/` if present:

* `org/finos/flowave/bpm/identity/flowave-identity-ldap`

## Flowave Connect

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/` if present:

* `org/finos/flowave/connect/flowave-connect-core`
* `org/finos/flowave/connect/flowave-connect-http-client`
* `org/finos/flowave/connect/flowave-connect-soap-http-client`
* `org/finos/flowave/bpm/flowave-engine-plugin-connect`

## Flowave Spin

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/` if present:

* `org/finos/flowave/spin/flowave-spin-core`
* `org/finos/flowave/spin/flowave-spin-dataformat-json-jackson`
* `org/finos/flowave/spin/flowave-spin-dataformat-xml-dom`
* `org/finos/flowave/bpm/flowave-engine-plugin-spin`
* `com/fasterxml/jackson/core/jackson-core`
* `com/fasterxml/jackson/core/jackson-databind`
* `com/fasterxml/jackson/core/jackson-annotations`


# 3. Update Flowave Web Applications

## Update REST API

The following steps are required to update the flowave REST API on a JBoss/Wildfly instance:

1. Undeploy an existing web application with a name like `flowave-engine-rest`
2. Download the REST API web application archive from our [Artifact Repository][engine-rest]. Or switch to the private repository for
   the enterprise version (User and password from license required). Choose the correct version named `$PLATFORM_VERSION/flowave-engine-rest-$PLATFORM_VERSION.war`.
3. Deploy the web application archive to your JBoss/Wildfly instance.

## Update Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on a JBoss/Wildfly instance:

1. Undeploy an existing web application with a name like `flowave-webapp`
2. Download the Flowave web application archive from our [Artifact Repository][webapp-jboss].
   Or switch to the private repository for the enterprise version (User and password from license required).
   Choose the correct version named `$PLATFORM_VERSION/flowave-webapp-jboss.war`.
3. Deploy the web application archive to your JBoss/Wildfly instance.

{{< note title="LDAP Entity Caching" class="info" >}}
It is possible to enable entity caching for Hypertext Application Language (HAL) requests that the flowave web applications make. This can be especially useful when you use flowave in combination with LDAP. To activate caching, the flowave webapp artifact has to be modified and the pre-built application cannot be used as is. See the [REST Api Documentation]({{< ref "/reference/rest/overview/hal.md" >}}) for details.
{{< /note >}}

# 4. Configure Process Engines

## Task Query Expressions

As of 7.3.3, the default handling of expressions submitted as parameters of task queries has changed. Passing EL expressions in a task query enables execution of arbitrary code when the query is evaluated. The process engine no longer evaluates these expressions by default and throws an exception instead. This behavior can be toggled in the process engine configuration using the properties `enableExpressionsInAdhocQueries` (default `false`) and `enableExpressionsInStoredQueries` (default `true`). To restore the engine's previous behavior, set both flags to `true`. See the user guide on [security considerations for custom code]({{< ref "/user-guide/process-engine/securing-custom-code.md" >}}) for details.
This is already the default for Flowave versions after and including 7.2.8.

[migration-guide]: {{< ref "/update/minor/72-to-73/_index.md" >}}
[jboss-distro]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/jboss/camunda-bpm-jboss/
[wildfly-distro]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/wildfly/camunda-bpm-wildfly/
[engine-rest]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/
[webapp-jboss]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-jboss/
