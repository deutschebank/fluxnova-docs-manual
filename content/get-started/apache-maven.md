---

title: 'Apache Maven Coordinates'
weight: 13

menu:
  main:
    name: "Maven Coordinates"
    identifier: "get-started-maven"
    parent: "get-started"
    pre: "The most commonly used Apache Maven Coordinates for Flowave."

---

This page lists the most commonly used Apache Maven Coordinates for Flowave.

Most Flowave artifacts are pushed to [maven central](https://central.sonatype.com/).


# Flowave BOM (Bill of Materials)

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.finos.flowave.bpm</groupId>
      <artifactId>flowave-bom</artifactId>
      <version>1.0.0</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
  </dependencies>
</dependencyManagement>
```

{{< note title="Use the BOM!" class="info" >}}
  Please import the Flowave BOM if you use multiple Flowave projects. The BOM defines versions for all Flowave projects. This way it is ensured that no incompatible versions are imported.
{{< /note >}}


# Flowave Engine

```xml
<dependency>
  <groupId>org.finos.flowave.bpm</groupId>
  <artifactId>flowave-engine</artifactId>
</dependency>
```


# Flowave Engine Spring Integration

The `flowave-engine` Spring integration for Spring Framework 5:

```xml
<dependency>
  <groupId>org.finos.flowave.bpm</groupId>
  <artifactId>flowave-engine-spring</artifactId>
</dependency>
```

The `flowave-engine` Spring integration for Spring Framework 6:

```xml
<dependency>
  <groupId>org.finos.flowave.bpm</groupId>
  <artifactId>flowave-engine-spring-6</artifactId>
</dependency>
```

# Flowave Engine CDI Integration

```xml
<dependency>
  <groupId>org.finos.flowave.bpm</groupId>
  <artifactId>flowave-engine-cdi</artifactId>
</dependency>
```

# Flowave DMN Engine BOM (Bill of Materials)
This BOM allows to use the DMN engine standalone without the BPMN engine and the rest of the Flowave Platform.

```xml
<dependencyManagement>
  <dependency>
    <groupId>org.finos.flowave.bpm.dmn</groupId>
    <artifactId>flowave-engine-dmn-bom</artifactId>
    <version>1.0.0</version>
    <type>pom</type>
    <scope>import</scope>
  </dependency>
</dependencyManagement>
```

# Flowave DMN
This dependency allows to use DMN engine standalone without the BPMN engine and the rest of the Flowave Platform.
It is not needed when using `flowave-engine` because that already contains the DMN engine.

```xml
<dependency>
  <groupId>org.finos.flowave.bpm.dmn</groupId>
  <artifactId>flowave-engine-dmn</artifactId>
</dependency>
```

# Process Application EJB Client

```xml
<dependency>
  <groupId>org.finos.flowave.bpm.javaee</groupId>
  <artifactId>flowave-ejb-client</artifactId>
</dependency>
```

# Flowave Artifact Storage

## Artifactory

Flowave relies on JFrog Artifactory to provide Flowave artifacts to users at [artifacts.camunda.com](https://artifacts.camunda.com/). The artifact data is stored in [Amazon S3](https://aws.amazon.com/s3/) storage and gets served by [artifacts.camunda.com](https://artifacts.camunda.com/) via redirects to AWS S3. Users must be able to connect to both endpoints for artifact retrieval.

```xml
<repositories>
  <repository>
    <id>flowave-bpm-nexus</id>
    <name>flowave-bpm-nexus</name>
    <url>
      https://artifacts.camunda.com/artifactory/public/
    </url>
  </repository>
</repositories>
```

### Browse Flowave Artifact Storage
In order to browse the Flowave artifacts, here are the links which you can use.

https://artifacts.camunda.com/ui/native/camunda-bpm

### Known issues

#### cURL artifacts
The files are hosted in AWS S3, therefore, Artifactory rewrites the requests to S3 and sends a 302 as the first response. For cURL this means to add the "`-L`" or "`--location`" option to follow the response.

Example:
```
curl -LO https://artifacts.camunda.com/artifactory/camunda-bpm/org/finos/flowave/bpm/camunda-engine-rest/7.23.0/camunda-engine-rest-7.23.0.war
```

# Other Flowave Modules:

* [DMN Engine](/manual/latest/user-guide/dmn-engine/embed/#maven-coordinates)
* [Flowave Spin](/manual/latest/reference/spin)
* [Flowave Connect](/manual/latest/reference/connect/#maven-coordinates)
* [Templating Engines](/manual/latest/user-guide/process-engine/templating/#install-a-template-engine-for-an-embedded-process-engine)
* [Spring Boot Integration](/manual/latest/user-guide/spring-boot-integration/)
