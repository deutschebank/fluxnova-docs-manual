---

title: 'Connectors'
weight: 100

menu:
  main:
    identifier: "user-guide-process-engine-connectors"
    parent: "user-guide-process-engine"

---


With the dependency [flowave-connect](https://github.com/finos/flowave-bpm-platform/tree/master/connect), the process engine supports simple
connectors. Currently the following connector implementations exist:

<table class="table">
  <tr>
    <th>Connector</th>
    <th>ID</th>
  </tr>
  <tr>
    <td>REST HTTP</td>
    <td>http-connector</td>
  </tr>
  <tr>
    <td>SOAP HTTP</td>
    <td>soap-http-connector</td>
  </tr>
</table>

It is also possible to implement your own custom connector in flowave. For more information about extending connectors please visit the [Connector reference]({{< ref "/reference/connect/extending-connect.md" >}}). 


# Configure Flowave Connect

As Flowave Connect is available only partially when using the process engine (check the list below). With a pre-built distribution, Flowave Connect is already preconfigured.

The following `connect` artifacts exist:

* `flowave-connect-core`: a jar that contains only the core Connect classes. The artifact already is available as dependency to the process engine. In addition to `flowave-connect-core`, single connector implementations like `flowave-connect-http-client` and `flowave-connect-soap-http-client` exist. These dependencies should be used when the default connectors have to be reconfigured or when custom connector implementations are used.
* `flowave-connect-connectors-all`: a single jar without dependencies that contains the HTTP and SOAP connectors.
* `flowave-engine-plugin-connect`: a process engine plugin to add Connect to Flowave.


# Maven Coordinates

{{< note title="" class="info" >}}
  Please import the [Flowave BOM](/get-started/apache-maven/) to ensure correct versions for every Flowave project.
{{< /note >}}


## flowave-connect-core

`flowave-connect-core` contains the core classes of Connect. Additionally, the HTTP and SOAP connectors can be added with the dependencies `flowave-connect-http-client` and `flowave-connect-soap-http-client`. These artifacts will transitively pull in their dependencies, like Apache HTTP client. For integration with the engine, the artifact `flowave-engine-plugin-connect` is needed. Given that the BOM is imported, the Maven coordinates are as follows:

```xml
<dependency>
  <groupId>org.finos.flowave.connect</groupId>
  <artifactId>flowave-connect-core</artifactId>
</dependency>
```

```xml
<dependency>
  <groupId>org.finos.flowave.connect</groupId>
  <artifactId>flowave-connect-http-client</artifactId>
</dependency>
```

```xml
<dependency>
  <groupId>org.finos.flowave.connect</groupId>
  <artifactId>flowave-connect-soap-http-client</artifactId>
</dependency>
```

```xml
<dependency>
  <groupId>org.finos.flowave.bpm</groupId>
  <artifactId>flowave-engine-plugin-connect</artifactId>
</dependency>
```


## flowave-connect-connectors-all

This artifact contains the HTTP and SOAP connectors as well as their dependencies. To avoid conflicts with other versions of these dependencies, the dependencies are relocated to different packages. `flowave-connect-connectors-all` has the following Maven coordinates:

```xml
<dependency>
  <groupId>org.finos.flowave.connect</groupId>
  <artifactId>flowave-connect-connectors-all</artifactId>
</dependency>
```


## Configure the Process Engine Plugin

`flowave-engine-plugin-connect` contains a class called `org.finos.flowave.connect.plugin.impl.ConnectProcessEnginePlugin` that can be registered with a process engine using the [plugin mechanism]({{< ref "/user-guide/process-engine/process-engine-plugins.md" >}}). For example, a `bpm-platform.xml` file with the plugin enabled would look as follows:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<bpm-platform xmlns="http://www.camunda.org/schema/1.0/BpmPlatform"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.camunda.org/schema/1.0/BpmPlatform http://www.camunda.org/schema/1.0/BpmPlatform ">
  ...
  <process-engine name="default">
    ...
    <plugins>
      <plugin>
        <class>org.finos.flowave.connect.plugin.impl.ConnectProcessEnginePlugin</class>
      </plugin>
    </plugins>
    ...
  </process-engine>
</bpm-platform>
```

{{< note title="" class="info" >}}
  When using a pre-built distribution of Flowave, the plugin is already pre-configured.
{{< /note >}}


# Use Connectors

To use a connector, you have to add the Flowave extension element [connector]({{< ref "/reference/bpmn20/custom-extensions/extension-elements.md#flowave-connector" >}}). The connector is configured by a unique [connectorId]({{< ref "/reference/bpmn20/custom-extensions/extension-elements.md#flowave-connectorid" >}}), which specifies the used connector implementation. The ids of the currently supported connectors can be found at the beginning of this section. Additionally, an [input/output mapping]({{< ref "/user-guide/process-engine/variables.md#input-output-variable-mapping" >}}) is used to configure the connector. The required input parameters and the available output parameters depend on the connector implementation. Additional input parameters can also be provided to be used within the connector.

As an example, a shortened configuration of the Flowave SOAP connector implementation is shown. A complete [example](https://github.com/finos/flowave-bpm-examples/tree/master/servicetask/soap-service) can be found in the [Flowave examples repository](https://github.com/finos/flowave-bpm-examples) on GitHub.

```xml
<serviceTask id="soapRequest" name="Simple SOAP Request">
  <extensionElements>
    <camunda:connector>
      <camunda:connectorId>soap-http-connector</camunda:connectorId>
      <camunda:inputOutput>
        <camunda:inputParameter name="url">
          http://example.com/webservice
        </camunda:inputParameter>
        <camunda:inputParameter name="payload">
          <![CDATA[
            <soap:Envelope ...>
              ... // the request envelope
            </soap:Envelope>
          ]]>
        </camunda:inputParameter>
        <camunda:outputParameter name="result">
          <![CDATA[
            ... // process response body
          ]]>
        </camunda:outputParameter>
      </camunda:inputOutput>
    </camunda:connector>
  </extensionElements>
</serviceTask>
```

A full [example](https://github.com/finos/flowave-bpm-examples/tree/master/servicetask/rest-service) of the REST connector can also be found in the [Flowave examples repository](https://github.com/finos/flowave-bpm-examples) on GitHub.
