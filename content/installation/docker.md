---

title: "Run Fluxnova using Docker"
weight: 20

menu:
  main:
    name: "Docker"
    identifier: "installation-guide-docker"
    parent: "installation-guide"
    pre: "Run the Full Distribution using Docker"

---

# Community Edition

The Community Edition docker images can be found on [GitHub](https://github.com/finos/docker-fluxnova-bpm-platform) and [Docker Hub](https://hub.docker.com/r/fluxnova/fluxnova-bpm-platform/).

## Start Fluxnova Run using Docker

To start [Fluxnova Run]({{< ref "/user-guide/fluxnova-bpm-run.md" >}}) execute the following commands:

```shell
docker pull fluxnova/fluxnova-bpm-platform:run-latest
docker run -d --name fluxnova -p 8080:8080 fluxnova/fluxnova-bpm-platform:run-latest
```

## Start Fluxnova (Tomcat) using Docker

To start Fluxnova, execute the following commands:

```shell
docker pull fluxnova/fluxnova-bpm-platform:latest
docker run -d --name fluxnova -p 8080:8080 fluxnova/fluxnova-bpm-platform:latest
```

Please note that by default the Apache Tomcat distribution is used. For a guide on how to use one of the other distributions, see the [tag schema](https://github.com/finos/docker-fluxnova-bpm-platform#supported-tagsreleases).


# Enterprise Edition

{{< enterprise >}}
Please note that these docker images are offered only for Fluxnova Enterprise Edition.
{{< /enterprise >}}

Since version 7.9 we offer to our customers a Docker image for the Enterprise edition of Fluxnova.

These images are hosted on our dedicated Docker registry and are available to Enterprise customers only. You can browse the available images in our [Docker registry](https://registry.camunda.cloud) after logging-in with your credentials.

Please note that these images are build using the same Dockerfile of the Community image, but including the Enterprise version of Fluxnova. For this reason, the same [documentation](https://github.com/finos/docker-fluxnova-bpm-platform#database-environment-variables) applies.

Make sure to log-in correctly before trying to pull the image:

```shell
$ docker login registry.fluxnova.cloud
Username: my.username
Password:
Login Succeeded

$ docker pull registry.fluxnova.cloud/cambpm-ee/fluxnova-bpm-platform-ee:{{< minor-version >}}.0
{{< minor-version >}}.0: Pulling from fluxnova-bpm-platform-ee
ff3a5c916c92: Already exists
5de5f69f42d7: Already exists
fa7536dd895a: Pull complete
46ce7a9973c1: Pull complete
6fa1782e4a59: Pull complete
fbf8f17dff48: Pull complete
Digest: sha256:47598932a4aff210ce91819d3b75adbfde675017b13ce9881c9d7dca682fba96
Status: Downloaded newer image for registry.fluxnova.cloud/cambpm-ee/fluxnova-bpm-platform-ee:{{< minor-version >}}.0
```

If you want to build an enterprise image yourself, follow the steps described on [GitHub](https://github.com/finos/docker-fluxnova-bpm-platform#build-a-enterprise-version).