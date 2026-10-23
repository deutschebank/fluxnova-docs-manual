---

title: "REST API"
weight: 30

menu:
  main:
    name: "REST API"
    identifier: "user-guide-spring-boot-rest-api"
    parent: "user-guide-spring-boot-integration"

---

To enable the [REST API]({{< relref "/reference/rest/_index.md">}}) you can use the following starter in your `pom.xml`:

```xml
<dependency>
  <groupId>org.finos.fluxnova.bpm.springboot</groupId>
  <artifactId>fluxnova-bpm-spring-boot-starter-rest</artifactId>
  <version>{project-version}</version>
</dependency>
```

By default the application path is `engine-rest`, so without any further configuration you can access the api at `http://localhost:8080/engine-rest`.

Because we use jersey, one can use spring boot's [common application properties](https://docs.spring.io/spring-boot/appendix/application-properties/index.html). 
For example, to change the application path, use 
```properties
spring.jersey.application-path=myapplicationpath
```

To modify the configuration or register additional resources, one can provide a bean which extends from 
`org.finos.fluxnova.bpm.spring.boot.starter.rest.FluxnovaJerseyResourceConfig`:

```java
@Component
@ApplicationPath("/engine-rest")
public class JerseyConfig extends FluxnovaJerseyResourceConfig {

  @Override
  protected void registerAdditionalResources() {
    register(...);
  }

}
```

# Authentication

The `fluxnova-bpm-spring-boot-starter-rest` starter can secure the REST API at runtime through properties, without requiring a custom `@Configuration` class. Both basic and JWT/OIDC authentication are supported and can be switched between using properties alone.

```yaml
fluxnova:
  bpm:
    auth:
      enabled: true
      type: basic # or jwt
```

Setting only `fluxnova.bpm.auth.enabled=true` enables REST authentication and defaults to `basic`, which authenticates against the configured identity source (for example the built-in admin user). No additional dependencies are required for basic authentication.

## JWT Authentication

To authenticate requests with a bearer token validated against an OIDC-compliant identity provider, set `fluxnova.bpm.auth.type` to `jwt` and configure the JWT properties:

```yaml
fluxnova:
  bpm:
    auth:
      enabled: true
      type: jwt
    jwt:
      jwks-url: https://login.microsoftonline.com/<tenant>/discovery/v2.0/keys
      issuer: https://login.microsoftonline.com/<tenant>/v2.0
      audience: api://your-client-id
      user-claim-name: preferred_username
      groups-claim-name: groups
      header-name: Authorization
      header-prefix: Bearer
```

* `jwks-url`, `issuer`, and `audience` configure token validation against the identity provider.
* `user-claim-name` and `groups-claim-name` control which token claims are used to resolve the Fluxnova user ID and groups.
* `header-name` and `header-prefix` control which HTTP header and prefix the token is read from. The prefix automatically gets a trailing space appended if missing.

JWT authentication requires the optional `nimbus-jose-jwt` dependency, which is only pulled in when JWT authentication is configured.

{{< note title="Note" class="info" >}}
Fluxnova Run exposes equivalent JWT/basic auth switching for the REST API through `fluxnova.bpm.run.auth.*` properties. See the [Fluxnova Run]({{< relref "/user-guide/fluxnova-bpm-run.md#authentication" >}}) documentation for details.
{{< /note >}}
