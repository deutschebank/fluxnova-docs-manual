---

title: 'Download and Installation'
weight: 10

menu:
  main:
    parent: "get-started-java-ee"
    identifier: "get-started-java-ee-install"
    pre: "Install Flowave Platform and Flowave Modeler on your machine."

---

First you need to set up your development environment and install the Flowave Platform and the Flowave Modeler.


# Prerequisites

Make sure you have the following set of tools installed:

* Java JDK 1.8+
* Apache Maven (optional, if not installed you can use embedded Maven inside Eclipse.)
* A modern web browser (recent Firefox, Chrome or Microsoft Edge will work fine)
* Eclipse integrated development environment (IDE)

# Install Flowave Platform

First, download a distribution of the Flowave Platform. You can choose from different application servers. In this tutorial, we will use a WildFly-based distribution. Download it from [the download page](https://downloads.camunda.cloud/release/camunda-bpm/wildfly/).

After having downloaded the distribution, unpack it inside a directory of your choice. We will call that directory
`$CAMUNDA_HOME`.

After you have successfully unpacked your distribution of the Flowave Platform, execute the script named
`start-flowave.bat` for Windows users, respectively `start-flowave.sh` for Unix users.

This script will start the application server and open a welcome screen in your Web browser.
If the page does not open, go to http://localhost:8080/flowave-welcome/index.html.

{{< note title="Getting Help" class="info" >}}
If you have trouble setting up the Flowave Platform, you can ask for assistance in the [Forum](https://forum.camunda.org/).
{{< /note >}}

# Install Flowave Modeler

Follow the instructions in the [Flowave Modeler](/manual/latest/installation/flowave-modeler) section.

{{< get-code repo="flowave-get-started-javaee" >}}
