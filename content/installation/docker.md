---

title: "Run Flowave using Docker"
weight: 20

menu:
  main:
    name: "Docker"
    identifier: "installation-guide-docker"
    parent: "installation-guide"
    pre: "Run the Full Distribution using Docker"

---

# Community Edition

The Community Edition docker images can be found on [GitHub](https://github.com/finos/docker-flowave-bpm-platform) and [Docker Hub](https://hub.docker.com/r/flowave/flowave-bpm-platform/).

## Start Flowave Run using Docker

To start [Flowave Run]({{< ref "/user-guide/flowave-bpm-run.md" >}}) execute the following commands:

```shell
docker pull flowave/flowave-bpm-platform:run-latest
docker run -d --name flowave -p 8080:8080 flowave/flowave-bpm-platform:run-latest
```

## Start Flowave (Tomcat) using Docker

To start Flowave, execute the following commands:

```shell
docker pull flowave/flowave-bpm-platform:latest
docker run -d --name flowave -p 8080:8080 flowave/flowave-bpm-platform:latest
```

Please note that by default the Apache Tomcat distribution is used. For a guide on how to use one of the other distributions, see the [tag schema](https://github.com/finos/docker-flowave-bpm-platform#supported-tagsreleases).


# Enterprise Edition

{{< enterprise >}}
Please note that these docker images are offered only for Flowave Enterprise Edition.
{{< /enterprise >}}

Since version 7.9 we offer to our customers a Docker image for the Enterprise edition of Flowave.

These images are hosted on our dedicated Docker registry and are available to Enterprise customers only. You can browse the available images in our [Docker registry](https://registry.camunda.cloud) after logging-in with your credentials.

Please note that these images are build using the same Dockerfile of the Community image, but including the Enterprise version of Flowave. For this reason, the same [documentation](https://github.com/finos/docker-flowave-bpm-platform#database-environment-variables) applies.

Make sure to log-in correctly before trying to pull the image:

```shell
$ docker login registry.flowave.cloud
Username: my.username
Password:
Login Succeeded

$ docker pull registry.flowave.cloud/cambpm-ee/flowave-bpm-platform-ee:{{< minor-version >}}.0
{{< minor-version >}}.0: Pulling from flowave-bpm-platform-ee
ff3a5c916c92: Already exists
5de5f69f42d7: Already exists
fa7536dd895a: Pull complete
46ce7a9973c1: Pull complete
6fa1782e4a59: Pull complete
fbf8f17dff48: Pull complete
Digest: sha256:47598932a4aff210ce91819d3b75adbfde675017b13ce9881c9d7dca682fba96
Status: Downloaded newer image for registry.flowave.cloud/cambpm-ee/flowave-bpm-platform-ee:{{< minor-version >}}.0
```

If you want to build an enterprise image yourself, follow the steps described on [GitHub](https://github.com/finos/docker-flowave-bpm-platform#build-a-enterprise-version).