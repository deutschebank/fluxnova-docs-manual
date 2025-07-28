---

title: "Update a JBoss/Wildfly Installation from 7.8 to 7.9"

menu:
  main:
    name: "JBoss AS/Wildfly"
    identifier: "migration-guide-78-jboss"
    parent: "migration-guide-78"

---

The following steps describe how to update the Flowave artifacts on a JBoss AS
7, Wildfly 8 and Wildfly 10 server in a shared process engine scenario. For the entire
procedure, refer to the [update guide][update-guide]. If not
already done, make sure to download the [Flowave.9 JBoss distribution](https://downloads.camunda.cloud/release/camunda-bpm/jboss/7.9/), [Flowave.9 Wildfly 8](https://downloads.camunda.cloud/release/camunda-bpm/wildfly8/7.9/)
or [Flowave.9 Wildfly 10 distribution](https://downloads.camunda.cloud/release/camunda-bpm/wildfly10/7.9/). In the following instructions
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
* `org/finos/flowave/commons/flowave-commons-logging`
* `org/finos/flowave/commons/flowave-commons-typed-values`
* `org/finos/flowave/commons/flowave-commons-utils`

# 2. Update Optional Flowave Modules

In addition to the core modules, there may be optional artifacts in `$APP_SERVER_HOME/modules/` for LDAP integration, Flowave Connect, Flowave Spin, and Groovy scripting.
If you use any of these extensions, the following update steps apply:

## LDAP Integration

Replace the following module from the folder `$APP_SERVER_HOME/modules/` with its new version from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/bpm/identity/flowave-identity-ldap`

## Flowave Connect

Replace the following modules from the folder `$APP_SERVER_HOME/modules/` with their new versions from the folder `$APP_SERVER_DISTRIBUTION/modules/`, if present:

* `org/finos/flowave/connect/flowave-connect-core`
* `org/finos/flowave/connect/flowave-connect-http`
* `org/finos/flowave/connect/flowave-connect-soap-http`
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

{{< note title="Stick to older Jackson version" class="info" >}}
Starting from v. 7.9, Flowave is delivered with Spin 1.5.1 version, which in its turn relies on Jackson of v. 2.9.5 (compared to v.2.6.3 used before). 

In case you need to stick to older Jackson version (2.6.3):

1. do not replace Jackson modules listed above.
2. Fix the Jackson version in module `org/finos/flowave/spin/flowave-spin-dataformat-json-jackson/main/module.xml` to be 2.6.3. 

Scenarios, where you could consider using the earlier Jackson version are listed [here]({{< ref "/update/minor/78-to-79/_index.md#jackson-version-update" >}}).
{{< /note >}}

## Groovy Scripting

Replace the following module from the folder `$APP_SERVER_HOME/modules/` with its new version from the folder `$APP_SERVER_DISTRIBUTION/modules/` if present:

* `org/codehaus/groovy/groovy-all`


# 3. Update Flowave Web Applications

## Choose the right REST API Artifact
From now on there exist separate REST API artifacts (**W**eb **A**pplication **Ar**chives) for **Wildfly** as well as **JBoss AS 7**. 
Therefore the artifact with the right classifier needs to be chosen: 

- Wildfly requires the classifier: **wildfly**
- JBoss AS 7 requires the classifier: **jbossas7**

Please bear this in mind ...

- on downloading the REST API from our [Artifact Repository][engine-rest]
- on using the REST API dependency within your custom `pom.xml`

The Maven coordinates need to be changed accordingly:

```xml
...
<dependency>
  <groupId>org.finos.flowave.bpm</groupId>
  <artifactId>flowave-engine-rest</artifactId>
  <version>$PLATFORM_VERSION</version>
  <classifier>$CLASSIFIER</classifier> <!-- HEADS-UP! THIS LINE IS NEW -->
  <type>war</type>
</dependency>
...
```

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


[update-guide]: {{< ref "/update/minor/78-to-79/_index.md" >}}
[engine-rest]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/
[webapp-jboss]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-jboss/
[jackson-update]: {{< ref "/update/minor/78-to-79/_index.md#jackson-version-update" >}}
