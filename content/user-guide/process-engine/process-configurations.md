---

title: "Process Configurations"
weight: 25

menu:
  main:
    identifier: "user-guide-process-engine-process-configurations"
    parent: "user-guide-process-engine"

---

Process configurations are persistent key/value entries managed by the process engine. An entry can be global or scoped to a tenant.

## Java API

Use the `ConfigurationService` exposed by the process engine to create an entry and retrieve it by its ID:

```java
ConfigurationService configurationService = processEngine.getConfigurationService();

Configuration tenantConfiguration = configurationService.createConfiguration(
    "mail.from",
    "noreply@example.org",
    "tenant-a");

Configuration globalConfiguration = configurationService.createConfiguration(
    "mail.from",
    "noreply-global@example.org",
    null);

Configuration configuration = configurationService.getConfiguration(tenantConfiguration.getId());
```

`configKey` and `configValue` are required. Pass a `null` tenant ID to create a global entry; otherwise, the entry applies to the specified tenant. An active key can be created once in each scope, so a global entry and an entry with the same key for a particular tenant are distinct.

Configurations can also be listed, updated, and deleted:

```java
// list configurations applicable to a tenant, merging global and tenant-level active entries
List<Configuration> configurations = configurationService.getConfigurations("tenant-a", false);

// list every entry, including inactive/historical versions, without merging
List<Configuration> allVersions = configurationService.getConfigurations("tenant-a", true);

// update the value of an active configuration; this creates a new version
Configuration updated = configurationService.updateConfiguration(configuration.getId(), "notifications@example.org");

// soft-delete an active configuration by marking it inactive
configurationService.deleteConfiguration(configuration.getId());
```

When `tenantId` is `null`, `getConfigurations` returns only the global default configurations. When a tenant is specified, active global configurations are returned with any active tenant-level entry replacing the global entry for the same `configKey`. Setting `includeInactive` to `true` instead returns every entry of every status (global, and the tenant's own entries if `tenantId` is set), without merging — useful for inspecting a key's full version history.

`updateConfiguration` does not modify the existing entry in place: it marks the current active entry `INACTIVE` and creates a new `ACTIVE` entry with the same key and tenant scope, with its version incremented. `deleteConfiguration` similarly soft-deletes an active entry by marking it `INACTIVE`; the entry is retained and remains retrievable by ID or via `includeInactive`.

## REST API

Create a configuration by sending a `POST` request to `/engine-rest/configurations` (relative to the REST API root):

```http
POST /engine-rest/configurations
Content-Type: application/json
```

```json
{
  "configKey": "mail.from",
  "configValue": "noreply@example.org",
  "tenantId": "tenant-a"
}
```

Omit `tenantId` or provide a blank value to create a global entry. A successful request returns `201 Created` with the new entry, including its ID, key, tenant ID, value, version, status, and audit fields. The response includes a `Location` header for the created resource. Missing or blank keys and values return `400 Bad Request`; creating a duplicate active key in the same scope returns `409 Conflict`.

### Listing, Updating, and Deleting Configurations

Retrieve the configurations applicable to a tenant with `GET /engine-rest/configurations`:

```http
GET /engine-rest/configurations?tenantId=tenant-a
```

If `tenantId` is omitted or blank, only global default configurations are returned. If `tenantId` is set, active global configurations are returned with the tenant's active entry replacing the global entry for the same `configKey`. Add `includeInactive=true` to instead return all global and tenant entries of every status, unmerged. Results are ordered by `configKey`, then global before tenant, then `version`.

Update the value of an active configuration with `PUT /engine-rest/configurations/{id}`:

```http
PUT /engine-rest/configurations/8a6cf4aa-6db5-4b95-9871-1f0f9ef8b2f5
Content-Type: application/json
```

```json
{
  "configValue": "notifications@example.org"
}
```

A successful request returns `200 OK` with the newly created active version (a new ID and incremented version). The existing entry is marked `INACTIVE`. A missing or blank `configValue` returns `400 Bad Request`; an unknown `id` returns `404 Not Found`; attempting to update an entry that is not currently active returns `409 Conflict`.

Soft-delete an active configuration with `DELETE /engine-rest/configurations/{id}`. A successful request returns `204 No Content`; an unknown `id` returns `404 Not Found`, and attempting to delete an entry that is not active returns `409 Conflict`.

For the complete REST API specification, see the [REST API reference]({{< relref "/reference/rest/specification/" >}}).
