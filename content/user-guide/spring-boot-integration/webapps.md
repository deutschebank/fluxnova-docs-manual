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