---

title: 'Instance Migration'
weight: 50

menu:
  main:
    identifier: "user-guide-control-center-instance-migration"
    parent: "user-guide-control-center-bpmn"
    name: "Instance Migration"

---

As process models evolve to accommodate enhancements, bug fixes, or changing business requirements, it becomes necessary to manage the lifecycle of running process instances that were started on older versions.

Instance Migration enables users to move active process instances from one process definition version to another within the same process definition. 

## Overview

When a new version of a process is deployed, existing instances continue running on the version in which they were started. The migration feature allows these instances to be transitioned to a newer version without restarting them.

This capability ensures:
- Continuity of long-running processes
- Alignment with updated process logic
- Reduced operational overhead compared to restarting instances

Note: Migration must comply with defined compatibility rules. It is recommended to review Process Migration guidelines to ensure safe execution.

## How to Migrate Process Instances

{{< video src="../../img/instance-migration.mp4" title="Process Instance Migration" >}}

To migrate process instances:
1. Navigate to the Process Definitions list view
2. Select the relevant process definition
3. Open the Process Definition Details View 
4. Using the checkboxes in the Instances tab select the desired instances to perform migration. 
5. Click on the Migrate 'x' Instances button to the top right corner of the bottom section of the page. (Here x refers to the number of selected instances)
6. Alternatively, for migrating all instances click on Migrate all Instances button before selecting instances.
7. Choose the target version from the version selector
8. View version models and confirm modifications required 
9. Click on Migrate to initiate the migration for the selected instances.

To monitor the status of the migration, click the View link in the success toast message after initiating the migration operation.

### Migration Considerations

Before performing a migration, consider the following:
- Model compatibility - Activities and flow structure should align between source and target versions
- Runtime state - Current execution points must exist in the target model
- Variables and data dependencies - Required variables must be present and valid

Failure to meet these conditions may result in migration errors or runtime failures.

## Migration Best Practices

To ensure a successful migration, follow these best practices:

### 1. Analyze Breaking Changes

Carefully evaluate differences between process versions:
- Ensure that existing execution paths remain valid
- Verify that no required tasks or transitions have been removed
- Confirm that newly introduced logic does not disrupt current instances

Example:
If a new version introduces required variables that do not exist in running instances, those instances may fail after migration.

### 2. Test Migration in a Non-Production Environment

Always validate migrations before applying them in production:
- Deploy both old and new versions in a pre-production environment
- Start instances using the old version
- Migrate them to the new version
- Execute remaining steps and verify successful completion

### 3. Validate Process Behavior Post-Migration

- Monitor migrated instances for unexpected behavior
- Check incidents, job execution, and variable states
- Ensure process completion aligns with expected outcomes

### 4. Migrate in Batches

If applicable, for large volumes of instances:
- Perform migration in controlled batches
- Monitor system impact and execution results