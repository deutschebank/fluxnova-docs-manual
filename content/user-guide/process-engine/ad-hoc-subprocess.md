---

title: 'Ad Hoc Subprocess'
weight: 65

menu:
  main:
    identifier: "user-guide-process-engine-ad-hoc-subprocess"
    parent: "user-guide-process-engine"

---

This section describes how to work with ad hoc subprocesses at runtime in Fluxnova. For modeling information, see the [Ad Hoc Subprocess Reference]({{< ref "/reference/bpmn20/subprocesses/ad-hoc-subprocess.md" >}}).

# Finding the Ad Hoc Subprocess Execution

To interact with an ad hoc subprocess at runtime, you need to find its execution ID. The ad hoc subprocess creates a separate execution scope that you can query using the `ExecutionQuery` API:

```java
import java.util.*;
import org.finos.fluxnova.bpm.engine.RuntimeService;
import org.finos.fluxnova.bpm.engine.runtime.Execution;
import org.finos.fluxnova.bpm.engine.runtime.ProcessInstance;

RuntimeService runtimeService = processEngine.getRuntimeService();

// Start or locate your process instance
ProcessInstance pi = runtimeService.startProcessInstanceByKey("myProcess");

// Find the ad hoc subprocess scope execution by its BPMN activity ID
Execution adHocExecution = runtimeService.createExecutionQuery()
  .processInstanceId(pi.getId())
  .activityId("adHocSubProcess")  // BPMN id of the adHocSubProcess element
  .singleResult();

// Now you have the execution ID for triggering activities or completing
String adHocExecutionId = adHocExecution.getId();
```

The `adHocSubProcess` activity ID refers to the `id` attribute of the `<bpmn:adHocSubProcess>` element in your BPMN model.

# Ordering

Fluxnova ad hoc subprocesses support **parallel ordering only**. When an ad hoc subprocess is active, multiple child activities can execute concurrently.

To enforce sequential execution between specific activities, use optional sequence flows to connect them within the ad hoc subprocess. For example:

```xml
<bpmn:adHocSubProcess id="AdHocProc" ordering="Parallel">
  <bpmn:userTask id="Task_A" name="Task A" />
  <bpmn:userTask id="Task_B" name="Task B" />
  <bpmn:userTask id="Task_C" name="Task C" />
  
  <!-- Enforce Task_A → Task_C sequence -->
  <bpmn:sequenceFlow id="Flow_1" sourceRef="Task_A" targetRef="Task_C" />
</bpmn:adHocSubProcess>
```

In this example, `Task_A` and `Task_B` can run in parallel, but `Task_C` cannot start until `Task_A` completes.



Ad hoc subprocesses in Fluxnova support dynamic activity triggering, which allows you to activate specific activities at runtime independently of the initial configuration. This is useful for implementing case management scenarios where work tasks are activated on-demand.

## Using triggerAdHocActivities()

The `RuntimeService.triggerAdHocActivities()` method allows you to activate one or more activities within an ad hoc subprocess execution:

```java
RuntimeService runtimeService = processEngine.getRuntimeService();

// Build per-activity variables (keyed by activity id)
Map<String, Map<String, Object>> activityVariables = new HashMap<>();
activityVariables.put("taskA", Map.of("priority", "HIGH", "owner", "alex"));
activityVariables.put("taskB", Map.of("source", "manual"));

// Trigger multiple activities with their respective variables
runtimeService.triggerAdHocActivities(
  adHocExecution.getId(),
  Arrays.asList("taskA", "taskB"),
  activityVariables
);
```

## REST Endpoint for Triggering Activities

```
POST /engine-rest/execution/{id}/ad-hoc-activities/trigger
```

### Request Body

```json
{
  "activities": [
    {
      "activityId": "Task_A",
      "variables": {
        "myStringVar": { "value": "hello", "type": "String" },
        "myIntVar": { "value": 42, "type": "Integer" },
        "myBoolVar": { "value": true, "type": "Boolean" }
      }
    },
    {
      "activityId": "Task_B",
      "variables": {
        "myStringVar": { "value": "world", "type": "String" }
      }
    }
  ]
}
```

### Response

Returns `204 No Content` on successful trigger.

# Manual Completion

In addition to the `completionCondition` defined in the process model, Fluxnova allows manual completion of ad hoc subprocesses via the runtime API. This provides a way to forcibly exit an ad hoc subprocess regardless of its completion condition state.

## Using completeAdHocSubProcess()

The `RuntimeService.completeAdHocSubProcess()` method allows you to manually complete an ad hoc subprocess:

```java
RuntimeService runtimeService = processEngine.getRuntimeService();

// Complete without variables
runtimeService.completeAdHocSubProcess(executionId);

// Complete with variables
Map<String, Object> variables = new HashMap<>();
variables.put("approved", true);
variables.put("reason", "All required tasks completed");
runtimeService.completeAdHocSubProcess(executionId, variables);
```

The manual completion bypasses the `completionCondition` entirely and requires all child activities to be idle (not currently active).

## REST Endpoint for Completion

```
POST /engine-rest/execution/{id}/ad-hoc-activities/complete
```

### Request Body

```json
{
  "variables": {
    "approved": {
      "value": true,
      "type": "Boolean"
    },
    "reason": {
      "value": "All required tasks completed",
      "type": "String"
    }
  }
}
```

### Response

Returns `204 No Content` on successful completion.

# Initial Activity Selection

By default, activities specified in the `activeTasksCollection` property are automatically started when the ad hoc subprocess enters. If `activeTasksCollection` is not specified or is empty, the subprocess enters an idle state and awaits manual activity triggering.

## Specifying Initial Activities

```xml
<bpmn:adHocSubProcess id="AdHocProc" ordering="Parallel">
  <bpmn:extensionElements>
    <fluxnova:properties>
      <fluxnova:property name="activeTasksCollection" value="Task_A,Task_B" />
    </fluxnova:properties>
  </bpmn:extensionElements>
  ...
</bpmn:adHocSubProcess>
```

In this example, both `Task_A` and `Task_B` are automatically activated when the ad hoc subprocess starts.

# Completion Condition

The `completionCondition` element defines an expression that determines when the ad hoc subprocess completes. This is re-evaluated each time a child activity completes.

## Example Completion Conditions

```xml
<!-- Complete when at least 2 tasks have been completed -->
<completionCondition xsi:type="bpmn:tFormalExpression">
  ${nrOfCompletedInstances >= 2}
</completionCondition>

<!-- Complete when a specific variable flag is true -->
<completionCondition xsi:type="bpmn:tFormalExpression">
  ${approved==true}
</completionCondition>

<!-- Complete when all tasks are finished -->
<completionCondition xsi:type="bpmn:tFormalExpression">
  ${nrOfCompletedInstances==nrOfInstances}
</completionCondition>
```

If the `completionCondition` evaluates to `false`, the ad hoc subprocess remains open indefinitely, awaiting either the condition to become `true` or manual completion via `completeAdHocSubProcess()`.

# Cancellation Behavior

The `cancelRemainingInstances` attribute (default: `true`) controls whether active child activities are cancelled when the ad hoc subprocess completes:

- **`true`** (default) — All active child activities are cancelled when completion occurs
- **`false`** — Active child activities continue execution after the subprocess completes

```xml
<bpmn:adHocSubProcess id="AdHocProc" 
                      ordering="Parallel"
                      cancelRemainingInstances="false">
  ...
</bpmn:adHocSubProcess>
```

# Multi-Instance Ad Hoc Subprocesses

Ad hoc subprocesses support `multiInstanceLoopCharacteristics`, allowing you to create multiple instances of the entire ad hoc subprocess:

```xml
<bpmn:adHocSubProcess id="AdHocProc" ordering="Parallel">
  <bpmn:multiInstanceLoopCharacteristics>
    <bpmn:loopCardinality xsi:type="bpmn:tFormalExpression">3</bpmn:loopCardinality>
  </bpmn:multiInstanceLoopCharacteristics>
  <bpmn:completionCondition xsi:type="bpmn:tFormalExpression">
    ${nrOfCompletedInstances >= nrOfInstances}
  </bpmn:completionCondition>
  ...
</bpmn:adHocSubProcess>
```

This creates three parallel instances of the ad hoc subprocess, each with its own set of child activities.
