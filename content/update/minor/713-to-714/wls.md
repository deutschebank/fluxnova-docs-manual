---

title: "Update an Oracle WebLogic Installation from 7.13 to 7.14"

menu:
  main:
    name: "WebLogic"
    identifier: "migration-guide-714-weblogic"
    parent: "migration-guide-714"

---

The following steps describe how to update the Flowave artifacts on an Oracle WebLogic application server in a shared process engine setting. For the entire procedure, refer to the [update guide][update-guide]. If not already done, make sure to download the [Flowave.14 Oracle WebLogic distribution](https://artifacts.camunda.com/artifactory/camunda-bpm-ee/org/finos/flowave/bpm/weblogic/camunda-bpm-weblogic/7.14.0-ee/).

The update procedure takes the following steps:

1. Uninstall the Flowave Applications and Archives
2. Replace Flowave Core Libraries
3. Replace Optional Flowave Dependencies
4. Maintain the Flowave Configuration
5. Install the Flowave Archive
6. Install the Web Applications

In each of the following steps, the identifier `$*_VERSION` refers to the current versions and the new versions of the artifacts.

# 1. Uninstall the Flowave Applications and Archives

First, uninstall the Flowave web applications, namely the Flowave REST API (artifact name like `flowave-engine-rest`) and the Flowave applications Cockpit, Tasklist, and Admin (artifact name like `flowave-webapp`).

Uninstall the Flowave EAR. Its name should be `flowave-oracle-weblogic-ear-$PLATFORM_VERSION.ear`.

# 2. Replace Flowave Core Libraries

After shutting down the server, replace the following libraries in `$WLS_DOMAIN_HOME/lib` with their equivalents from `$WLS_DISTRIBUTION/modules/lib`:

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

In addition to the core libraries, there may be optional artifacts in `$WLS_DOMAIN_HOME/lib` for LDAP integration, Flowave Spin, and Groovy scripting. If you use any of these extensions, the following update steps apply:

## LDAP integration

Copy the following library from `$WLS_DISTRIBUTION/modules/lib` to the folder `$WLS_DOMAIN_HOME/lib`, if present:

* `flowave-identity-ldap-$PLATFORM_VERSION.jar`

## Flowave Connect Plugin


`flowave-connect-http-client`, `flowave-connect-soap-http-client`, and `flowave-engine-plugin-connect` are part of the .ear


## Flowave Spin

Copy the following library from `$WLS_DISTRIBUTION/modules/lib` to the folder `$WLS_DOMAIN_HOME/lib`, if present:

* `flowave-spin-core-$SPIN_VERSION.jar`

--- 
`flowave-spin-dataformat-json-jackson`, `flowave-spin-dataformat-xml-dom`, and `flowave-engine-plugin-spin` are part of the .ear

# 4. Maintain the Flowave Configuration

If you have previously replaced the default Flowave configuration by a custom configuration following any of the ways outlined in the [deployment descriptor reference][configuration-location], it may be necessary to restore this configuration. This can be done by repeating the configuration replacement steps for the updated platform.

# 5. Install the Flowave Archive

Install the Flowave EAR, i.e., the file `$WLS_DISTRIBUTION/modules/flowave-oracle-weblogic-ear-$PLATFORM_VERSION.ear`.

# 6. Install the Web Applications

## REST API

Deploy the web application `$WLS_DISTRIBUTION/webapps/flowave-engine-rest-$PLATFORM_VERSION-wls.war` to your Oracle WebLogic instance.

## Cockpit, Tasklist, and Admin

Deploy the web application `$WLS_DISTRIBUTION/webapps/flowave-webapp-ee-wls-$PLATFORM_VERSION.war` to your Oracle WebLogic instance.

[configuration-location]: {{< ref "/reference/deployment-descriptors/descriptors/bpm-platform-xml.md" >}}
[update-guide]: {{< ref "/update/minor/713-to-714/_index.md" >}}
