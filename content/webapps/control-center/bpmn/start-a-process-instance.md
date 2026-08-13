---

title: 'Start Process Instance'
weight: 40

menu:
  main:
    identifier: "user-guide-control-center-start-process-instance"
    parent: "user-guide-control-center-bpmn"
    name: "Start Process Instance"

---

Fluxnova Control Center enables users to start a new process instance on demand directly from a selected process definition details view.

This feature is typically used for manual triggering, or initiating ad hoc process instances.

{{< video src="../../img/start-process-instance.mp4" title="Start a Process Instance" >}}

## How to Start a Process

To start a process instance:

1. Navigate to the Process Definitions list view
2. Locate and click on the desired process definition ID
3. This opens the Process Definition Details View
4. Click the Start Process button in the top right header
5. This action opens a dialog where input parameters can be provided.
6. Click Confirm to start a process instance for this selected process definition.

## Process Input Configuration

When starting a process, users are prompted to provide input data in JSON format.

### Key Capabilities

- Enter process variables as a JSON payload
- Use the default example JSON as a reference for formatting
- Customize input values based on the requirements of the process

This ensures that the process instance is initialized with the correct data required for execution.

### Best Practices

- Provide all required variables expected by the process
- Ensure the JSON input is valid and properly formatted
- Verify variable names and data types to avoid execution errors