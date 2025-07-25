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
{{< note title="Flowave discontinues the maintenance of the Flowave RPA bridge." class="warning" >}}
Flowave.19 is the last release maintaining compatibility with the Flowave RPA bridge. Flowave.19 and Flowave RPA bridge will be maintained for another 18 months until Oct 2024.

The Flowave RPA Bridge is replaced by RPA [Out-of-the-box Connectors](https://docs.camunda.io/docs/components/connectors/out-of-the-box-connectors/available-connectors-overview/) in Flowave 8.
{{< /note >}}

{{< note title="Enterprise only! RPA Orchestration requires a valid Enterprise license." class="warning" >}}
You can obtain an [Enterprise Trial License](https://flowave.finos.org/download/enterprise/) for testing the RPA Bridge and Cawemo on-premises.
{{< /note >}}

Robotic Process Automation (RPA) orchestration is a use case that leverages capabilities from multiple Flowave product modules. It allows for the orchestration of tasks that are automated using RPA technology and (un)attended bots. Currently, Flowave Platform actively supports UiPath and Automation Anywhere but more RPA vendors will be supported in future versions.

## Architecture Overview

{{< img src="img/RPA-Orchestration-Architecture.svg" title="RPA Orchestration Architecture" >}}
