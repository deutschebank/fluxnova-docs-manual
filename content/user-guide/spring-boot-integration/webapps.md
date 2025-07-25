---

title: "Web applications"
weight: 40

menu:
  main:
    name: "Web Applications"
    identifier: "user-guide-spring-boot-webapps"
    parent: "user-guide-spring-boot-integration"

---

To enable the [Web Applications]({{<ref "/webapps/_index.md">}}) you can use the following starter in your `pom.xml`:

```xml
<dependency>
  <groupId>org.finos.flowave.bpm.springboot</groupId>
  <artifactId>flowave-bpm-spring-boot-starter-webapp</artifactId>
  <version>{project-version}</version>
</dependency>
```

By default the application path is `/flowave`, so without any further configuration you can access 
the Webapps under [http://localhost:8080/flowave/app/](http://localhost:8080/flowave/app/).

## Enterprise webapps

{{< enterprise >}}
Please note that this feature is only included in the enterprise edition of Flowave, it is not available in the community edition.
{{< /enterprise >}}

To use the enterprise Web applications, include another starter:
```xml
<dependency>
  <groupId>org.finos.flowave.bpm.springboot</groupId>
  <artifactId>flowave-bpm-spring-boot-starter-webapp-ee</artifactId>
  <version>${project-version}</version>
</dependency>
```

Also don't forget to define the appropriate Flowave engine version (with "ee" suffix): see [Using Enterprise Edition](../#using-enterprise-edition).

If you are using the enterprise edition, you can also use the [`flowave.bpm.license-file`]({{<ref "/user-guide/spring-boot-integration/configuration.md#license-file">}}) 
property to provide a license file that is inserted on application start. Or copy your license file under the name 
`flowave-license.txt` to your `src/main/resources`. See the dedicated [License docs section]({{< ref "/user-guide/license-use.md" >}})
for more details on how to add a License key to your Flowave installation.

## Configurations

You can change the application path with the following configuration property in your `application.yaml` file:
```properties
flowave.bpm.webapp.application-path=/my/application/path
```

By default, the starter registers a controller to redirect `/` to Flowave's bundled `index.html`.
To disable this, you have to add to your application properties:
```properties
flowave.bpm.webapp.index-redirect-enabled=false
```

## Error Pages

The default error handling coming with the Spring Boot ('whitelabel' error page) is enabled in the starter. To switch to the Flowave error pages (`webjar/META-INF/resources/webjars/flowave/error-XYZ-page.html`), please put them to the application folder structure under `/src/main/resources/public/error/XYZ.html`.

## Building Custom REST APIs

The Flowave Web Applications use a `CSRF Prevention Filter` that expects a `CSRF Token` on any 
modifying request for paths beginning with `/flowave/api/` or `/flowave/app/`. Any modifying requests 
mapped to these paths will fail, and the current session will be ended if no CSRF Token is present.
You can avoid this by registering your resources on different paths or add your resources to the
CSRF Prevention Filter Whitelist (via the configuration property `flowave.bpm.webapp.csrf.entry-points`).