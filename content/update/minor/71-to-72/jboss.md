---

title: "Update a JBoss Installation from 7.1 to 7.2"

menu:
  main:
    name: "JBoss"
    identifier: "migration-guide-71-jboss"
    parent: "migration-guide-71"

---

The following steps describe how to update the Flowave artifacts on a JBoss AS 7 server in a shared process engine setting. For the entire migration procedure, refer to the [migration guide][migration-guide]. If not already done, make sure to download the [Flowave.2 JBoss distribution](https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/jboss/camunda-bpm-jboss/).

The update procedure takes the following steps:

1. Update the Flowave modules
2. Configure process engines
3. Configure optional Flowave extensions (*optional*)
4. Update Flowave web applications

Whenever the instructions are to *replace* a module, make sure to delete the previous version of the module first to avoid orphan jars.


# 1. Update the Flowave Modules

Replace the following modules from the folder `$JBOSS_HOME/modules/` with their new versions from the folder `$JBOSS_DISTRIBUTION/modules/`:

* `org/finos/flowave/bpm/flowave-engine`
* `org/finos/flowave/bpm/jboss/flowave-jboss-subsystem`
* `org/finos/flowave/bpm/model/flowave-bpmn-model`
* `org/finos/flowave/bpm/model/flowave-xml-model`
* `org/mybatis/mybatis`

If present, also replace the following optional module:

* `org/finos/flowave/bpm/identity/flowave-identity-ldap`

Add the following modules from the folder `$JBOSS_DISTRIBUTION/modules/` to the folder `$JBOSS_HOME/modules/`:

* `org/finos/flowave/bpm/model/flowave-cmmn-model`
* `org/finos/flowave/commons/flowave-commons-logging`
* `org/finos/flowave/commons/flowave-commons-utils`
* `org/finos/flowave/connect/flowave-connect-core`
* `org/finos/flowave/connect/flowave-connect-http-client`
* `org/finos/flowave/connect/flowave-connect-soap-http-client`
* `org/finos/flowave/bpm/flowave-engine-plugin-connect`
* `org/apache/httpcomponents/httpclient`
* `org/apache/httpcomponents/httpcore`
* `commons-codec/commons-codec`
* `commons-logging/commons-logging`
* `org/finos/flowave/spin/flowave-spin-core`
* `org/finos/flowave/spin/flowave-spin-dataformat-json-jackson`
* `org/finos/flowave/spin/flowave-spin-dataformat-xml-dom`
* `org/finos/flowave/bpm/flowave-engine-plugin-spin`
* `com/fasterxml/jackson/core/jackson-core`
* `com/fasterxml/jackson/core/jackson-databind`
* `com/fasterxml/jackson/core/jackson-annotations`
* `com/jayway/jsonpath/json-path`
* `org/codehaus/groovy/groovy-all`
* `org/finos/flowave/template-engines/flowave-template-engines-freemarker`
* `org/freemarker/freemarker`


# 2. Configure Process Engines

## Script Variable Storing

As of 7.2, the default behavior of script variables has changed. Script variables are set in e.g., a BPMN Script Task that uses a language such as JavaScript or Groovy. In previous versions, the process engine automatically stored all script variables as process variables. Starting with 7.2, this behavior has changed and the process engine does not automatically store script variables any longer. You can re-enable the legacy behavior by setting the boolean property `autoStoreScriptVariables` to `true` for any process engine in the `standalone.xml`:

```xml
<subsystem xmlns="urn:org.finos.flowave.bpm.jboss:1.1">
  ...
  <process-engines>
    <process-engine name="default" default="true">
      ...
      <properties>
        ... existing properties ...
        <property name="autoStoreScriptVariables">true</property>
      </properties>
      ...
    </process-engine>
  </process-engines>
  ...
</subsystem>
```

As an alternative, process application developers can migrate script code by replacing all implicit declarations of process variables in their scripts with an explicit call to `execution.setVariable('varName', 'value')`.


## 3. Configure Optional Flowave Modules

In addition, there are modules for Flowave Connect, Flowave Spin, the Freemarker template language and Groovy scripting that extend Flowave functionality. Since all these artifacts add new functionality, the following steps are not required for migration.

## Flowave Connect

In order to activate Flowave Connect functionality for a process engine, a process engine plugin has to be registered in `$JBOSS_HOME/standalone/configuration/standalone.xml` as follows:


```xml
<subsystem xmlns="urn:org.finos.flowave.bpm.jboss:1.1">
  ...
  <process-engines>
    <process-engine name="default" default="true">
      ...
      <plugins>
        ... existing plugins ...
        <plugin>
          <class>org.finos.flowave.connect.plugin.impl.ConnectProcessEnginePlugin</class>
        </plugin>
      </plugins>
      ...
    </process-engine>
  </process-engines>
  ...
</subsystem>
```

## Flowave Spin

In order to activate Flowave Spin functionality for a process engine, a process engine plugin has to be registered in `$JBOSS_HOME/standalone/configuration/standalone.xml` as follows:

```xml
<subsystem xmlns="urn:org.finos.flowave.bpm.jboss:1.1">
  ...
  <process-engines>
    <process-engine name="default" default="true">
      ...
      <plugins>
        ... existing plugins ...
        <plugin>
          <class>org.finos.flowave.spin.plugin.impl.SpinProcessEnginePlugin</class>
        </plugin>
      </plugins>
      ...
    </process-engine>
  </process-engines>
  ...
</subsystem>
```

# 4. Update Flowave Web Applications

## Update Flowave REST API

The following steps are required to update the flowave REST API on a JBoss instance:

1. Undeploy an existing web application with a name like `flowave-engine-rest`
2. Download the REST API web application archive from our [Artifact Repository](https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/). Or switch to the private repository for the enterprise version (User and password from license required). Choose the correct version named `$PLATFORM_VERSION/camunda-engine-rest-$PLATFORM_VERSION.war`.
3. Deploy the web application archive to your JBoss instance.

## Update Flowave Cockpit, Tasklist, and Admin

The following steps are required to update the Flowave web applications Cockpit, Tasklist, and Admin on a JBoss instance:

1. Undeploy an existing web application with a name like `flowave-webapp`
2. Download the Flowave web application archive from our [Artifact Repository](https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/webapp/camunda-webapp-jboss/). Or switch to the private repository for the enterprise version (User and password from license required). Choose the correct version named `$PLATFORM_VERSION/camunda-webapp-jboss-$PLATFORM_VERSION.war`.
3. Deploy the web application archive to your JBoss instance.

{{< note title="LDAP Entity Caching" class="info" >}}
With 7.2, it is possible to enable entity caching for Hypertext Application Language (HAL) requests that the flowave web applications make. This can be especially useful when you use flowave in combination with LDAP. To activate caching, the flowave webapp artifact has to be modified and the pre-built application cannot be used as is. See the [REST Api Documentation]({{< ref "/reference/rest/overview/hal.md" >}}) for details.
{{< /note >}}

[configuration-location]: {{< ref "/reference/deployment-descriptors/descriptors/bpm-platform-xml.md" >}}
[migration-guide]: {{< ref "/update/minor/71-to-72/_index.md" >}}
