---

title: "Spring Boot Version Compatibility"
weight: 10

menu:
  main:
    name: "Version Compatibility"
    identifier: "user-guide-spring-boot-version-compatibility"
    parent: "user-guide-spring-boot-integration"

---

Each version of the Flowave Spring Boot Starter is bound to a specific version of Flowave and Spring Boot. 
Only these default combinations are recommended (and supported) by Flowave.
Other combinations must be thoroughly tested before being used in production.

{{< note title="Heads Up" class="info" >}}
  Flowave and its compatible Spring Boot Starter always share the same version.
  Also, the Flowave version used in the Spring Boot Starter doesn't have to be overridden anymore. Simply pick
  the version of the Starter that resembles the version of Flowave you want to use.
{{< /note >}}

<table class="table table-striped">
  <tr>
    <th>Spring Boot Starter version</th>
    <th>Flowave version</th>
    <th>Spring Boot version</th>
  </tr>
  <tr>
    <td>1.0.x</td>
    <td>1.0.x</td>
    <td>3.4.x</td>
  </tr>
</table>

\* For these versions, use the following Maven coordinates:
```
<dependency>
  <groupId>org.finos.flowave.bpm.extension</groupId>
  <artifactId>flowave-bpm-spring-boot-starter</artifactId>
  <version>1.x</version> <!-- set correct version here -->
</dependency>
```

\*\* For these versions, use the following Maven coordinates:
```
<dependency>
  <groupId>org.finos.flowave.bpm.extension.springboot</groupId>
  <artifactId>flowave-bpm-spring-boot-starter</artifactId>
  <version>2.x</version> <!-- set correct version here -->
</dependency>
```

\*\*\* For these versions, all listed Spring Boot versions are supported 
while the oldest one is used by default. If you want to use a newer supported version,
configure `dependencyManagement` in your application, e.g. add the following when using Maven:
```
<dependencyManagement>
  <dependencies>
  ...
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.x.y.RELEASE</version> <!-- set correct version here -->
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  ...
  </dependencies>
</dependencyManagement>
```
