---

title: 'RPA Orchestration'
weight: 40

menu:
  main:
    name: "RPA Orchestration"
    parent: "get-started-archive"
    identifier: "rpa"
    pre: ""

---
{{< note title="Fluxnova discontinues the maintenance of the Fluxnova RPA bridge." class="warning" >}}
Fluxnova.19 is the last release maintaining compatibility with the Fluxnova RPA bridge. Fluxnova.19 and Fluxnova RPA bridge will be maintained for another 18 months until Oct 2024.

The Fluxnova RPA Bridge is replaced by RPA [Out-of-the-box Connectors](https://docs.camunda.io/docs/components/connectors/out-of-the-box-connectors/available-connectors-overview/) in Fluxnova 8.
{{< /note >}}

{{< note title="Enterprise only! RPA Orchestration requires a valid Enterprise license." class="warning" >}}
You can obtain an [Enterprise Trial License](https://fluxnova.finos.org/download/enterprise/) for testing the RPA Bridge and Cawemo on-premises.
{{< /note >}}

Robotic Process Automation (RPA) orchestration is a use case that leverages capabilities from multiple Fluxnova product modules. It allows for the orchestration of tasks that are automated using RPA technology and (un)attended bots. Currently, Fluxnova Platform actively supports UiPath and Automation Anywhere but more RPA vendors will be supported in future versions.

## Architecture Overview

{{< img src="img/RPA-Orchestration-Architecture.svg" title="RPA Orchestration Architecture" >}}
