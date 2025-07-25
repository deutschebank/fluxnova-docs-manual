---

title: "Update an Oracle WebLogic Installation from 7.2 to 7.3"

menu:
  main:
    name: "WebLogic"
    identifier: "migration-guide-72-weblogic"
    parent: "migration-guide-72"

---

The following steps describe how to update the Flowave artifacts on an Oracle WebLogic application server in a shared process engine setting. For the entire migration procedure, refer to the [migration guide][migration-guide]. If not already done, make sure to download the [Flowave.3 Oracle WebLogic distribution](https://artifacts.camunda.com/artifactory/internal/org/finos/flowave/bpm/weblogic/camunda-bpm-weblogic/).

The update procedure takes the following steps:

1. Uninstall the Flowave libraries and archives
2. Replace Flowave core libraries
3. Replace optional Flowave dependencies
4. Maintain Flowave configuration (*optional*)
5. Install the Flowave archive
6. Install the Flowave web applications

In each of the following steps, the identifiers `$*_VERSION` refer to the current version and the new versions of the artifacts.


# 1. Uninstall the Flowave Applications and Archives

First, uninstall the Flowave web applications, namely the Flowave REST API (artifact name like `flowave-engine-rest`) and the Flowave applications Cockpit, Tasklist and Admin (artifact name like `flowave-webapp`).

Uninstall the flowave EAR. Its name should be `flowave-oracle-weblogic-ear-$PLATFORM_VERSION.ear`.


# 2. Replace Flowave Core Libraries

After shutting down the server, replace the following libraries in `$WLS_DOMAIN_HOME/lib` with their equivalents from `$WLS_DISTRIBUTION/modules/lib`:

* `flowave-engine-$PLATFORM_VERSION.jar`
* `flowave-bpmn-model-$PLATFORM_VERSION.jar`
* `flowave-cmmn-model-$PLATFORM_VERSION.jar`
* `flowave-xml-model-$PLATFORM_VERSION.jar`


# 3. Replace Optional Flowave Dependencies

In addition to the core libraries, there may be optional artifacts in `$WLS_DOMAIN_HOME/lib` for LDAP integration, Flowave Connect, and Flowave Spin. If you use any of these extensions, the following update steps apply:

## LDAP integration

Copy the following libraries from `$WLS_DISTRIBUTION/modules/lib` to the folder `$WLS_DOMAIN_HOME/lib` if present:

* `flowave-identity-ldap-$PLATFORM_VERSION.jar`

## Flowave Connect

Copy the following libraries from `$WLS_DISTRIBUTION/modules/lib` to the folder `$WLS_DOMAIN_HOME/lib` if present:

* `flowave-connect-core-$CONNECT_VERSION.jar`

## Flowave Spin

Copy the following libraries from `$WLS_DISTRIBUTION/modules/lib` to the folder `$WLS_DOMAIN_HOME/lib` if present:

* `flowave-spin-core-$SPIN_VERSION.jar`


# 4. Maintain the Flowave Configuration

If you have previously replaced the default Flowave configuration by a custom configuration following any of the ways outlined in the [deployment descriptor reference][configuration-location], it may be necessary to restore this configuration. This can be done by repeating the configuration replacement steps for the updated platform.

## Task Query Expressions

As of 7.3.3, the default handling of expressions submitted as parameters of task queries has changed. Passing EL expressions in a task query enables execution of arbitrary code when the query is evaluated. The process engine no longer evaluates these expressions by default and throws an exception instead. This behavior can be toggled in the process engine configuration using the properties `enableExpressionsInAdhocQueries` (default `false`) and `enableExpressionsInStoredQueries` (default `true`). To restore the engine's previous behavior, set both flags to `true`. See the user guide on [security considerations for custom code]({{< ref "/user-guide/process-engine/securing-custom-code.md" >}}) for details.
This is already the default for Flowave versions after and including 7.2.8.


# 5. Install the Flowave Archive

Install the Flowave EAR, i.e., the file `$WLS_DISTRIBUTION/modules/flowave-oracle-weblogic-ear-$PLATFORM_VERSION.ear`.


# 6. Install the Web Applications

## REST API

Deploy the web application `$WLS_DISTRIBUTION/webapps/flowave-engine-rest-$PLATFORM_VERSION-wls.war` to your Oracle WebLogic instance.

## Cockpit, Tasklist, and Admin

Deploy the web application `$WLS_DISTRIBUTION/webapps/flowave-webapp-ee-wls-$PLATFORM_VERSION.war` to your Oracle WebLogic instance.

{{< note title="LDAP Entity Caching" class="info" >}}
It is possible to enable entity caching for Hypertext Application Language (HAL) requests that the flowave web applications make. This can be especially useful when you use flowave in combination with LDAP. To activate caching, the flowave webapp artifact has to be modified and the pre-built application cannot be used as is. See the [REST Api Documentation]({{< ref "/reference/rest/overview/hal.md" >}}) for details.
{{< /note >}}

[configuration-location]: {{< ref "/reference/deployment-descriptors/descriptors/bpm-platform-xml.md" >}}
[migration-guide]: {{< ref "/update/minor/72-to-73/_index.md" >}}
