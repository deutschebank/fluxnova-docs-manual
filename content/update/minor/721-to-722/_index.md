---

title: "Update from 7.21 to 7.22"
weight: 2
layout: "single"

menu:
  main:
    name: "7.21 to 7.22"
    identifier: "migration-guide-722"
    parent: "migration-guide-minor"
    pre: "Update from `7.21.x` to `7.22.0`."

---

This document guides you through the update from Flowave `7.21.x` to `7.22.0` and covers the following use cases:

1. For administrators and developers: [Database updates](#database-updates)
1. For administrators and developers: [Full distribution update](#full-distribution)
1. For administrators and developers: [Flowave Spin](#flowave-spin)
1. For developers: [Flowave Commons](#flowave-commons)
1. For developers: [Flowave Template Engines FreeMarker](#flowave-template-engines-freemarker)
1. For developers: [Flowave Connect](#flowave-connect)
1. For developers: [Flowave Connect dependency removed from `flowave-engine`](#flowave-connect-dependency-removed-from-flowave-engine)
1. For administrators and developers: [Flowave license check dependency moved to public repository](#flowave-license-check-dependency-moved-to-public-repository)
1. For administrators and developers: [Update to JBoss EAP 8.0](#update-to-jboss-eap-8)
1. For administrators and developers: [Update to Tomcat 10 Server](#update-to-tomcat-10-server)
1. For administrators and developers: [Flowave Run and Swagger Update](#flowave-run-and-swagger-update)
1. For administrators and developers: [Update to Groovy 4.0](#update-to-groovy-4)
1. For administrators and developers: [Sending telemetry feature removed](#sending-telemetry-feature-removed)
1. For administrators: [Database transaction isolation level `READ_COMMITTED` is enforced](#database-transaction-isolation-level-read-committed-is-enforced)
1. For developers: [Quarkus 3.14 Extension Update](#quarkus-3-14-extension-update)
1. For administrators and developers: [Cockpit: Process Instance Batch Modification](#cockpit-process-instance-batch-modification)

This guide covers mandatory migration steps and optional considerations for the initial configuration of new functionality included in Flowave.22.

# Database updates

Every Flowave installation requires a database schema update. Check our [database schema update guide]({{< ref "/installation/database-schema.md#update" >}})
for further instructions.

# Full distribution

This section is applicable if you installed the
[Full Distribution]({{< ref "/introduction/downloading-flowave.md#full-distribution" >}})
with a **shared process engine**.

The following steps are required:

1. Update the Flowave libraries and applications inside the application server.
2. Migrate custom process applications.

Before starting, ensure you have downloaded the Flowave.22 distribution for the application server you use. This contains the SQL scripts and libraries required for the update. This guide assumes you have unpacked the distribution to a path named `$DISTRIBUTION_PATH`.

# Flowave Spin
 We’ve moved the `flowave-spin` project from its [previous location](https://github.com/finos/flowave-spin) into the [mono repository](https://github.com/finos/flowave-bpm-platform). We’re no longer versioning it independently. Instead, we’ve integrated it into the 7.X.Y versioning scheme, so you can conveniently declare Flowave `7.22.0-alpha1` to use the latest release of Flowave Spin. 

# Flowave Commons
 We’ve moved the `flowave-commons` project from its [previous location](https://github.com/finos/flowave-commons) into the [mono repository](https://github.com/finos/flowave-bpm-platform). We’re no longer versioning it independently. Instead, we’ve integrated it into the 7.X.Y versioning scheme, so you can conveniently declare Flowave `7.22.0-alpha1` to use the latest release of Flowave Commons.
 We've also updated the `flowave-commons-bom` to include `flowave-commons-typed-values`. Now, you can manage all Flowave commons dependency versions directly through the `flowave-commons-bom`.

# Flowave Template Engines FreeMarker
 We’ve moved the `flowave-template-engines-freemarker` project from its [previous location](https://github.com/finos/flowave-template-engines-jsr223) into the [mono repository](https://github.com/finos/flowave-bpm-platform). We’re no longer versioning it independently. Instead, we’ve integrated it into the 7.X.Y versioning scheme, so you can conveniently declare Flowave `7.22.0-alpha2` to use the latest release of Flowave Template Engines FreeMarker.

# Flowave Connect
 We’ve moved the `flowave-connect` project from its [previous location](https://github.com/finos/flowave-connect) into the [mono repository](https://github.com/finos/flowave-bpm-platform). We’re no longer versioning it independently. Instead, we’ve integrated it into the 7.X.Y versioning scheme, so you can conveniently declare Flowave `7.22.0-alpha2` to use the latest release of Flowave Connect.
 
# Flowave Connect dependency removed from `flowave-engine`

`flowave-connect` is no longer dependency to `flowave-engine` and respectively in pre-packaged distributions (Tomcat, WildFly).
 If you use Flowave Connect functionality, check if you need to re-introduce the following dependencies to your project:

* `flowave-connect-core`
* `flowave-connect-connectors-all`
* `flowave-connect-http-client`
* `flowave-connect-soap-http-client`

# Flowave license check dependency moved to public repository

For enterprise users, the `flowave-license-check` is no longer in the private repository. Instead, it can now be found in the public repository which you need to add to your configured repositories. Note if you are using the community edition, this does not affect you. For enterprise users, the complete repository configuration should now look like this:

```xml
<repositories>
  <repository>
    <id>flowave-bpm-nexus</id>
    <name>flowave-bpm-nexus</name>
    <url>
    https://artifacts.camunda.com/artifactory/public/
    </url>
  </repository>
  <repository>
    <id>Flowave-bpm-nexus-ee</id>
    <name>flowave-bpm-nexus</name>
    <url>
    https://artifacts.camunda.com/artifactory/private/
    </url>
  </repository>
</repositories>
```

# Update to JBoss EAP 8

With this release, we support JBoss EAP 8.0, it's Jakarta EE compliant platform. The artifacts are shipped with the latest pre-packaged [Flowave WildFly distribution]({{< ref "/installation/full/jboss/manual.md#setup" >}}).
If you prefer to stay on JBoss EAP 7, you can still download the Java EE compliant [modules][wildfly26-modules], [web application][wildfly26-webapp], and [REST API][wildfly26-rest-api]. 

To work with JBoss EAP 8, consider the following when migrating your process applications and replacing artifacts on the application server:

[wildfly26-modules]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/wildfly/camunda-wildfly26-modules/
[wildfly26-webapp]: https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-jboss/
[wildfly26-rest-api]: https://artifacts.camunda.com/artifactory/public/org/finos/flowave/bpm/camunda-engine-rest/

## Migrate process applications

* Replace Java EE class references (`javax.*`) with Jakarta class references (`jakarta.*`)
  * You might have a look at [`org.eclipse.transformer:transformer-maven-plugin`](https://github.com/eclipse/transformer)
* Replace Flowave class references:
  * `org.finos.flowave.bpm.application.impl.EjbProcessApplication` → `org.finos.flowave.bpm.application.impl.JakartaEjbProcessApplication`
  * `org.finos.flowave.bpm.application.impl.ServletProcessApplicationDeployer` → `org.finos.flowave.bpm.application.impl.JakartaServletProcessApplicationDeployer`
  * `org.finos.flowave.bpm.application.impl.ServletProcessApplication` → `org.finos.flowave.bpm.application.impl.JakartaServletProcessApplication`
  * `org.finos.flowave.bpm.engine.impl.cfg.jta.JtaTransactionContext` → `org.finos.flowave.bpm.engine.impl.cfg.jta.JakartaTransactionContext`
  * `org.finos.flowave.bpm.engine.impl.cfg.jta.JtaTransactionContextFactory` → `org.finos.flowave.bpm.engine.impl.cfg.jta.JakartaTransactionContextFactory`
  * `org.finos.flowave.bpm.engine.impl.cfg.JtaProcessEngineConfiguration` → `org.finos.flowave.bpm.engine.impl.cfg.JakartaTransactionProcessEngineConfiguration`
  * `org.finos.flowave.bpm.engine.impl.interceptor.JtaTransactionInterceptor` → `org.finos.flowave.bpm.engine.impl.interceptor.JakartaTransactionInterceptor`
* Replace Flowave Maven dependencies:
  * `org.finos.flowave.bpm.javaee:flowave-ejb-client` → `org.finos.flowave.bpm.javaee:flowave-ejb-client-jakarta`
  * `org.finos.flowave.bpm:flowave-engine-cdi` → `org.finos.flowave.bpm:flowave-engine-cdi-jakarta`

## Replace artifacts on the application server

You can find the new artifacts either in the current WildFly distribution or in the [`camunda-wildfly-modules`](https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/wildfly/camunda-wildfly-modules/).

### Replace modules

* `$WILDFLY_HOME/modules/org/finos/flowave/spin/flowave-spin-dataformat-xml-dom` → `$WILDFLY_HOME/modules/org/finos/flowave/spin/flowave-spin-dataformat-xml-dom-jakarta`
* Flowave WildFly Subsystem under `$JBOSS_HOME/modules/org/finos/flowave/bpm/$APP_SERVER/flowave-wildfly-subsystem`
  
### Replace web application (Cockpit, Admin, Tasklist, Welcome) deployment

Replace the artifact `flowave-webapp-jboss-$PLATFORM_VERSION.war` with `flowave-webapp-wildfly-$PLATFORM_VERSION.war` under `$JBOSS_HOME/standalone/deployments`.

### Replace REST API deployment

Replace the artifact `flowave-engine-rest-$PLATFORM_VERSION-wildfly.war` with `flowave-engine-rest-jakarta-$PLATFORM_VERSION-wildfly.war` under `$JBOSS_HOME/standalone/deployments`.

# Update to Tomcat 10 Server

This version brings support for `Tomcat 10.1`. A few reasons to upgrade are:

* Namespace Change: Switch from `javax.*` to `jakarta.*` for future compatibility.
* Enhanced Security: Improved security features and fixes.
* Modern Features: Supports `Servlet 6.0`, `JSP 3.1`, and `WebSocket 2.1`.
* Performance Improvements: Faster response times and better resource efficiency.
* Simplified Migration: Tools and documentation for easier transition from earlier versions.
* Better Integration: Enhanced compatibility with `Jakarta EE` components and third-party libraries.

From now on, our pre-packaged Tomcat distribution is built with `Tomcat 10.1`.
Additionally, the Tomcat Docker image will, from now on, utilize `Tomcat 10.1`.

If you prefer to stay on `Tomcat 9`, you can still download the `Java EE` compliant [web application][tomcat9-webapp], and [REST API][tomcat9-rest-api].

To work with `Tomcat 10`, consider the following when migrating your process applications and replacing artifacts on the application server:

[tomcat9-webapp]: https://artifacts.camunda.com/ui/native/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-tomcat/
[tomcat9-rest-api]: https://artifacts.camunda.com/artifactory/public/org/finos/flowave/bpm/camunda-engine-rest/

## Migrate process applications

* Replace Java EE class references (`javax.*`) with Jakarta class references (`jakarta.*`)
 * You might have a look at [`org.eclipse.transformer:transformer-maven-plugin`](https://github.com/eclipse/transformer)
* Replace Flowave class references:
 * `org.finos.flowave.bpm.application.impl.EjbProcessApplication` → `org.finos.flowave.bpm.application.impl.JakartaEjbProcessApplication`
 * `org.finos.flowave.bpm.application.impl.ServletProcessApplicationDeployer` → `org.finos.flowave.bpm.application.impl.JakartaServletProcessApplicationDeployer`
 * `org.finos.flowave.bpm.application.impl.ServletProcessApplication` → `org.finos.flowave.bpm.application.impl.JakartaServletProcessApplication`
 * `org.finos.flowave.bpm.engine.impl.cfg.jta.JtaTransactionContext` → `org.finos.flowave.bpm.engine.impl.cfg.jta.JakartaTransactionContext`
 * `org.finos.flowave.bpm.engine.impl.cfg.jta.JtaTransactionContextFactory` → `org.finos.flowave.bpm.engine.impl.cfg.jta.JakartaTransactionContextFactory`
 * `org.finos.flowave.bpm.engine.impl.cfg.JtaProcessEngineConfiguration` → `org.finos.flowave.bpm.engine.impl.cfg.JakartaTransactionProcessEngineConfiguration`
 * `org.finos.flowave.bpm.engine.impl.interceptor.JtaTransactionInterceptor` → `org.finos.flowave.bpm.engine.impl.interceptor.JakartaTransactionInterceptor`
* Replace Flowave Maven dependencies:
 * `org.finos.flowave.bpm.javaee:flowave-ejb-client` → `org.finos.flowave.bpm.javaee:flowave-ejb-client-jakarta`
 * `org.finos.flowave.bpm:flowave-engine-cdi` → `org.finos.flowave.bpm:flowave-engine-cdi-jakarta`

## Migrate Java webapp plugins

Replace Java EE class references (`javax.*`) with Jakarta class references (`jakarta.*`)

## Replace web application (Cockpit, Admin, Tasklist, Welcome) deployment

Replace the artifact `flowave-webapp-tomcat-$PLATFORM_VERSION.war` with `flowave-webapp-tomcat-jakarta-$PLATFORM_VERSION.war` under `$CATALINA_HOME/webapps`.

## Replace REST API deployment

Replace the artifact `flowave-engine-rest-$PLATFORM_VERSION-tomcat.war` with `flowave-engine-rest-jakarta-$PLATFORM_VERSION-tomcat.war` under `$CATALINA_HOME/webapps`.

## Migrating to the Tomcat 10 Docker Image

If your application uses a Docker image based on `Tomcat 9`, you need to perform the above migration steps yourself before your application is compatible with the `jakarta` namespace changes the new Tomcat version introduces.

If your application uses a Docker image based on `Tomcat 9`, you need to perform the above migration steps yourself before your application is compatible with the `jakarta` namespace changes the new Tomcat version introduces.

### Weld Class Loading Issues

In deployment scenarios involving one or more process applications with managed beans, classloading issues may occur if the WELD library is directly embedded in the WAR's `/WEB-INF/lib` folder.
To resolve this, move the weld library away from the war and place it into the `$CATALINA_HOME/lib` folder.

The above workaround is not guaranteed to work for cases with bean references between WAR deployments (WAR A referencing a bean from WAR B).

The following test scenarios fail on Tomcat 10:

* [CallActivityContextSwitchTest](https://github.com/finos/flowave-bpm-platform/blob/f37877b822dabcbf3cee5806bd5833d18cdcb543/qa/integration-tests-engine/src/test/java/org/finos/flowave/bpm/integrationtest/functional/context/CallActivityContextSwitchTest.java)
* [CdiBeanCallActivityResolutionTest](https://github.com/finos/flowave-bpm-platform/blob/f37877b822dabcbf3cee5806bd5833d18cdcb543/qa/integration-tests-engine/src/test/java/org/finos/flowave/bpm/integrationtest/functional/cdi/CdiBeanCallActivityResolutionTest.java)

# Flowave Run and Swagger Update

Swagger UI was included in Flowave Run distros for a long time. Unfortunately, maintaining Swagger for Run was a lot of work. Other tools (like Postman or Swagger Editor) provide the same functionality outside of Flowave run. That is why we decided to discontinue Swagger in `flowave-run` to reduce maintenance efforts.

From now on, the `--swaggerui` argument is not available for Flowave Run start scripts, and the Swagger artifacts are not included in the Flowave Run distros anymore.
Instead, you can always use [open-api][open-api] in conjunction with a `REST` client of your choice such as POSTMAN to achieve similar results. 

[open-api]: {{< ref "/reference/rest/openapi/_index.md" >}}

# Update to Groovy 4

We have updated the Groovy version from `2.4` to `4.0`.

## Migrate your scripts

To continue running your Groovy scripts in the Flowave Engine, please read and act on the following release notes, especially the breaking changes:

- [Groovy 2.5](https://groovy-lang.org/releasenotes/groovy-2.5.html#Groovy2.5releasenotes-Breakingchanges)
- [Groovy 3.0](http://groovy-lang.org/releasenotes/groovy-3.0.html#Groovy3.0releasenotes-OtherBreaking)
- [Groovy 4.0](https://groovy-lang.org/releasenotes/groovy-4.0.html#Groovy4.0-breaking)

To make the migration process easier, we have included the `groovy-dateutil` and `groovy-datetime` libraries in the Flowave Platform release. `groovy-dateutil` contains the extension methods for the `java.util.Date` class, while `groovy-datetime` allows using operator overloading, closures, and Groovy's dynamic methods on `java.time`.

## What if I can't migrate my scripts?

It is possible to keep using Flowave Platform with a lower version of Groovy if GROOVY-VERSION is the desired version.

### If using a full distribution of Flowave:

- Remove `groovy-dateutil-4.0.x.jar` and `groovy-datetime-4.0.x.jar`.
- Replace `groovy-4.0.x.jar`, `groovy-json-4.0.x.jar`, `groovy-xml-4.0.x.jar`, and `groovy-templates-4.0.x.jar` libraries with `groovy-{GROOVY-VERSION}.jar`, `groovy-json-{GROOVY-VERSION}.jar`, `groovy-xml-{GROOVY-VERSION}.jar`, and `groovy-templates-{GROOVY-VERSION}.jar`.

### If using Embedded Engine:

- You can keep using your Groovy version without any extra effort.

# Sending telemetry feature removed

Sending telemetry data has been introduced in Flowave `7.14.0+` (and `7.13.7`, `7.12.12`, `7.11.19`)
 and removed in Flowave `7.22.0`. The public API is marked as deprecated and emptied out.
 The telemetry reporter and scheduled timer and all related process engine configuration properties are removed.
 The [diagnostic data][] is still being collected but not sent by any mean.
  You can decide to share it with Flowave if requested in tickets or use it for your diagnostic purposes.

In previous Flowave versions - `7.21.0+`, `7.20.8+`, `7.19.15+`, reporting telemetry data is disabled by default.

[diagnostic data]: {{< ref "/user-guide/process-engine/diagnostics-data.md" >}}

## Configuration properties removed

To clean up and refactor our source code, the following process engine configuration properties have been removed.
Please remove all of the occurrences of those properties, regardless of the setup that you are using
(share or embedded process engine, pre-packaged or other distribution).
You need to remove the properties from your tests as well.

* `initializeTelemetry       `
* `telemetryReporterActivate `
* `telemetryReportingPeriod  `
* `telemetryReporterActivate `
* `telemetryRequestRetries   `
* `telemetryRequestTimeout   `

## Public API deprecation

The public API for configuring telemetry and fetching the telemetry configuration have been emptied out and marked as deprecated. 
We recommend to remove the usage of the following API as the endpoints no longer do anything and their usage is unnecessary.

* Java API
  * [`ManagementService#isTelemetryEnabled()`](https://docs.flowave.finos.org/javadoc/flowave-bpm-platform/7.22/org/finos/flowave/bpm/engine/ManagementService.html#isTelemetryEnabled()) (always returns `false`)
  * [`ManagementService#toggleTelemetry(boolean)`](https://docs.flowave.finos.org/javadoc/flowave-bpm-platform/7.22/org/finos/flowave/bpm/engine/ManagementService.html#toggleTelemetry(boolean))
* REST API
  * [Fetch Telemetry Configuration](https://docs.flowave.finos.org/rest/flowave-bpm-platform/7.22/#tag/Telemetry/operation/getTelemetryConfiguration) (always returns `false`)
  * [Configure Telemetry](https://docs.flowave.finos.org/rest/flowave-bpm-platform/7.22/#tag/Telemetry/operation/configureTelemetry)

# Database transaction isolation level `READ_COMMITTED` is enforced

When starting the engine, a check is performed in order to determine if the transaction isolation level set for the database is different from the recommended one `READ_COMMITTED`. If it is, an exception will be thrown.

This behaviour can be disabled by setting the `skipIsolationLevelCheck` flag to `true`. Doing this will prevent an exception from being thrown and a warning message will be logged instead.

[See here]({{< ref "/reference/deployment-descriptors/tags/process-engine.md#configuration-properties" >}}) for more details about this and other properties.

# Quarkus 3.15 Extension Update

The Flowave Quarkus Extension has been updated to use Quarkus `3.15`. This  version brings its own features and changes.
For a complete list, see the [Quarkus 3.15 LTS Release](https://quarkus.io/blog/quarkus-3-15-1-released) blog post.

## Breaking Changes

`Quarkus 3.15` and previous versions extensions introduce **breaking changes** in the way the Quarkus runtime treats configuration.

A configuration migration is required to remain consistent with the new behavior of the framework (see [property examples](#property-examples) below).

The config properties now follow a similar scheme to the [Flowave Spring Boot Starter Configuration]({{< ref "/user-guide/spring-boot-integration/configuration.md#generic-properties" >}}).

The change affects the configuration of the [process engine]({{< ref "/reference/deployment-descriptors/tags/process-engine.md#configuration-properties" >}}) and the [job executor]({{< ref "/reference/deployment-descriptors/tags/job-executor.md" >}}).

**Reason for Change**: The Quarkus migration guide encourages using named `ConfigMappings`, and we chose to adopt it to future-proof the extension.
This requires using the new namespace `generic-config`.

- `quarkus.flowave.enforce-history-time-to-live` **becomes** `quarkus.flowave.generic-config.enforce-history-time-to-live`

- `quarkus.flowave.dmn-enabled` **becomes** `quarkus.flowave.generic-config.dmn-enabled`

- **Non-generic** properties such as `quarkus.flowave.job-executor.thread-pool.max-pool-size` remain the same.

For a detailed guide on the new Quarkus properties, visit the updated [Quarkus Configuration]({{< ref "/user-guide/quarkus-integration/configuration.md" >}}) page.

To read more on how Quarkus extensions treat configuration differently, see this [Quarkus Migration Guide](https://github.com/quarkusio/quarkus/wiki/Migration-Guide-3.14#for-extension-developers) and the [Mapping Configuration to Objects Guide](https://quarkus.io/guides/config-mappings).

# Cockpit: Process Instance Batch Modification

This release introduces changes in Cockpit when performing a [Process Instance Batch Modification][process-instance-modification]:

1. Querying for process instances now uses the historic instead of the runtime process instance query. This streamlines the query behavior with the standalone batch operation page.
2. Users that have saved queries cannot access them anymore after the upgrade. Please inform your users to migrate their saved queries accordingly.

### Reverted behavior change of the `Actvitiy ID` filter when upgrading from 7.22.0 to 7.22.1

{{< note title="Heads-up!" class="warning" >}}
After migrating to 7.22.1, the behavior of the `Activity ID` filter when batch modifying process instances will change back to the old behavior you are used to from Flowave versions <= 7.21.X.
{{< /note >}}

The 7.22.0 release introduced a limitation for the `Activity ID` filter: Filtering for activities marked as `asyncBefore`/`asyncAfter` with active instances didn't yield process instances when using the `Activity ID` filter.

After some user feedback, we understood that there are use cases that cannot be catered with this limitation in place leading to undesired behavior.
With this patch release, we lifted this limitation by opting for a different solution approach which restored the previous behavior.

[process-instance-modification]: {{< ref "/webapps/cockpit/bpmn/process-instance-modification.md#perform-a-batch-modification" >}}
