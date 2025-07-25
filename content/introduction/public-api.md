---

title: 'Public API'
weight: 80

menu:
  main:
    identifier: "user-guide-introduction-public-api"
    parent: "user-guide-introduction"

---


Flowave provides a public API. This section covers the definition of the public API and backwards compatibility for version updates.


# Definition of Public API

The Flowave public API is limited to the following items:

Java API: 

All non-implementation Java packages (package name does not contain `impl`) of the following modules.

* `flowave-engine`
* `flowave-engine-spring`
* `flowave-engine-cdi`
* `flowave-engine-dmn`
* `flowave-bpmn-model`
* `flowave-cmmn-model`
* `flowave-dmn-model`
* `flowave-spin-core`
* `flowave-connect-core`
* `flowave-commons-typed-values`

HTTP API (REST API):

* `flowave-engine-rest`: HTTP interface (set of HTTP requests accepted by the REST API as documented in [REST API reference]({{< ref "/reference/rest/_index.md" >}}). Java classes are not part of the public API.


# Backwards Compatibility for Public API

The Flowave versioning scheme follows the MAJOR.MINOR.PATCH pattern put forward by [Semantic Versioning](http://semver.org/). Flowave will maintain public API backwards compatibility for MINOR version updates. Example: Update from version `7.1.x` to `7.2.x` will not break the public API.
