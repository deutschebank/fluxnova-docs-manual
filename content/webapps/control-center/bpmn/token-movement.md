---

title: 'Token Movement'
weight: 80

menu:
  main:
    identifier: "user-guide-control-center-token-movement"
    parent: "user-guide-control-center-bpmn"
    name: "Token Movement"

---

The Token Movement feature allows users to manually adjust the execution state of a process instance by relocating, adding, or removing tokens within the BPMN workflow.

This advanced capability is primarily used for troubleshooting, error recovery, or correcting process execution paths when standard flow cannot proceed due to issues.

## Overview

In BPMN, a token represents the current execution point within a process. Token Movement enables users to directly manipulate these execution points on the process diagram.

This provides the ability to:
- Recover from failed or stuck executions
- Skip or revisit specific steps in a process
- Correct inconsistencies caused by unexpected runtime conditions

Note: Token Movement is an advanced operation and should be used with caution, as it can alter the intended process flow.

## Accessing Token Movement

{{< video src="../../img/token-movement.mp4" title="Token Movement" >}}

To use Token Movement:
1. Navigate to the Process Instances list view
2. Select the desired process instance
3. Open the Process Instance Details View
4. Click the Move Tokens button in the header
5. Once enabled, the BPMN canvas becomes interactive for token manipulation.
6. Right click on the desired activity to add or remove tokens.
7. Click on Apply Changes
8. Select preferred modifications and type in annotations to be included in the change.
9. Click on Continue to confirm token movement.  

## Available Actions

When Token Movement mode is active, users can right-click on activities within the canvas to perform the following actions:
- Add Token
  - Introduce a new execution point at a selected activity
- Remove Token
  - Remove an existing token from the selected activity
- Move Token (implicit via add/remove combination)
  - Reposition execution flow by removing a token from one activity and adding it to another
- Undo / Redo
  - Revert or reapply recent token movement actions

## Integration with Canvas & Tabs

Token Movement works seamlessly with the interactive canvas:

- Users can visually identify the current execution state
- Related data (incidents, jobs, variables) can be reviewed in the tabs before making changes

## Best Practices

- Always analyze the current state of the process instance before making changes
- Ensure that required variables and conditions are satisfied at the target activity
- Perform token movement in non-production environments first, when possible
- Document any manual adjustments for audit and traceability
- Considerations and Limitations
  - Token movement may bypass business logic, validations, or integrations defined in the process
  - Improper use can lead to:
    - Data inconsistencies
    - Unexpected process behavior
    - Additional incidents
  - Not all process states may support valid token relocation (depending on model structure)