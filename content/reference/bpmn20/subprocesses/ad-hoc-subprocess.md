---

title: 'Ad Hoc Subprocess'
weight: 50

menu:
  main:
    identifier: "bpmn-subprocess-ad-hoc"
    parent: "bpmn-subprocess"
    pre: "Model a set of activities that can be executed in any order."

---

An ad hoc subprocess is a subprocess in which the contained activities do not have to be executed in a predefined order. Activities can be activated dynamically at runtime in any order and multiple times. An ad hoc subprocess is useful for modeling case-like scenarios where the flow of work is determined at runtime rather than at design time. In Fluxnova, optional sequence flows between activities are permitted to route work through specific activity sequences.

An ad hoc subprocess has one ordering mode:

*   **Parallel** (default and only supported mode): Multiple activities within the subprocess may be active simultaneously. This allows any activity to be started at any time, regardless of whether other activities have already been started or completed.

To enforce sequential execution between activities, use optional sequence flows to connect them within the ad hoc subprocess.

An ad hoc subprocess completes when its `completionCondition` expression evaluates to `true`. If no `completionCondition` is specified, the subprocess completes when all activities have been completed at least once. The optional attribute `cancelRemainingInstances` (default: `true`) controls whether still-active activities are cancelled when the completion condition is met.

Constraints for an ad hoc subprocess:

*   An ad hoc subprocess must not have start or end events (BPMN 2.0 standard).
*   Boundary events on the ad hoc subprocess itself behave the same as for an embedded subprocess.
*   Activities within an ad hoc subprocess may optionally be connected by sequence flows to define activity routing (Fluxnova extension).

An ad hoc subprocess is visualized as a subprocess with a tilde (~) marker at the bottom center:

<div data-bpmn-diagram="../bpmn/ad-hoc-subprocess"></div>

When expanded, the internal activities are shown without connecting sequence flows:

<div data-bpmn-diagram="../bpmn/ad-hoc-subprocess-expanded"></div>

An ad hoc subprocess is represented in XML using the `adHocSubProcess` element. The `ordering` attribute must be set to `"Parallel"` (Fluxnova does not support sequential ordering). The optional `completionCondition` child element defines the expression that, when evaluated as `true`, causes the subprocess to complete:

```xml
<bpmn:adHocSubProcess id="Activity_11nc5qc" 
                      ordering="Parallel"
                      cancelRemainingInstances="false"
                      camunda:asyncBefore="true">
  <bpmn:extensionElements>
    <fluxnova:properties>
      <fluxnova:property name="activeTasksCollection" value="Task_A,Task_B" />
    </fluxnova:properties>
  </bpmn:extensionElements>
  <bpmn:completionCondition xsi:type="bpmn:tFormalExpression">${approved==true}</bpmn:completionCondition>
  
  <bpmn:userTask id="Task_A" name="Task A" />
  <bpmn:userTask id="Task_B" name="Task B">
    <bpmn:extensionElements>
      <camunda:inputOutput>
        <camunda:inputParameter name="Input_1">value</camunda:inputParameter>
      </camunda:inputOutput>
    </bpmn:extensionElements>
  </bpmn:userTask>
  <bpmn:userTask id="Task_C" name="Task C" />
  
  <!-- Optional sequence flows for routing -->
  <bpmn:sequenceFlow id="Flow_1" sourceRef="Task_A" targetRef="Task_C" />
</bpmn:adHocSubProcess>
```


# Fluxnova Extensions

<table class="table table-striped">
  <tr>
    <th>Attributes</th>
    <td>
      <a href="{{< ref "/reference/bpmn20/custom-extensions/extension-attributes.md#asyncbefore" >}}">camunda:asyncBefore</a>,
      <a href="{{< ref "/reference/bpmn20/custom-extensions/extension-attributes.md#asyncafter" >}}">camunda:asyncAfter</a>,
      <a href="{{< ref "/reference/bpmn20/custom-extensions/extension-attributes.md#exclusive" >}}">camunda:exclusive</a>,
      <a href="{{< ref "/reference/bpmn20/custom-extensions/extension-attributes.md#jobpriority" >}}">camunda:jobPriority</a>
    </td>
  </tr>
  <tr>
    <th>Extension Elements</th>
    <td>
      <a href="{{< ref "/reference/bpmn20/custom-extensions/extension-elements.md#failedjobretrytimecycle" >}}">camunda:failedJobRetryTimeCycle</a>,
      <a href="{{< ref "/reference/bpmn20/custom-extensions/extension-elements.md#inputoutput" >}}">camunda:inputOutput</a>,
      <code>fluxnova:properties</code> — Custom property container supporting <code>activeTasksCollection</code> property
    </td>
  </tr>
  <tr>
    <th>Extension Properties</th>
    <td>
      <code>fluxnova:property name="activeTasksCollection"</code> — Comma-separated list of activity IDs to auto-start when the ad hoc subprocess enters. If not specified, the subprocess enters idle state awaiting manual activity triggering via the <code>triggerAdHocActivities()</code> API.
    </td>
  </tr>
  <tr>
    <th>Constraints</th>
    <td>
      The <code>camunda:exclusive</code> attribute is only evaluated if the attribute
      <code>camunda:asyncBefore</code> or <code>camunda:asyncAfter</code> is set to <code>true</code>.
      <br/><br/>
      The <code>cancelRemainingInstances</code> attribute (default: <code>true</code>) controls whether active activities are cancelled when the <code>completionCondition</code> evaluates to true.
    </td>
  </tr>
</table>


# Fluxnova Implementation Details

## Core Components

- **AdHocSubProcessActivityBehavior** — Main behavior implementation
  - Evaluates `completionCondition` after each child activity completes
  - Auto-completes when no completion condition is set AND no active children remain
  - Re-evaluates condition periodically (on child completion, final completion)
  - If condition is false, subprocess stays open indefinitely
- **TriggerAdHocActivitiesCmd** & `RuntimeService.triggerAdHocActivities()` — Dynamic activity triggering
  - Allows activating starter activities after subprocess entry (not BPMN 2.0 standard)
  - Supports per-activity variable mapping
  - Validates activities are startable and not already active
- **CompleteAdHocSubProcessCmd** & `RuntimeService.completeAdHocSubProcess()` — Manual completion API
  - Bypasses completionCondition entirely (forced/manual override)
  - Supports optional variable setting at completion
  - Requires all inner activities to be idle

### REST Endpoints
- `POST /engine-rest/execution/{id}/ad-hoc-activities/trigger` — trigger activities with per-activity variables
- `POST /engine-rest/execution/{id}/ad-hoc-activities/complete` — complete with optional variables

## Fluxnova-Specific Deviations from BPMN 2.0

| Feature                   | BPMN 2.0 Standard                | Fluxnova Implementation                                      |
|--------------------------|-----------------------------------|--------------------------------------------------------------|
| Initial activity selection| All startable activities auto-start| Uses `activeTasksCollection` extension property               |
| Runtime activity triggering| No mechanism defined              | `triggerAdHocActivities()` API allows selective activation    |
| Per-activity variables    | Not supported                     | Each triggered activity receives its own variable map         |
| Manual completion         | No API                            | `completeAdHocSubProcess()` forcibly completes regardless     |
| Completion variables      | Variables set via condition only   | Optional variables can be set at completion time              |

## Key Behaviors

- **Idle state possible** — If no `activeTasksCollection` defined or is empty, subprocess enters idle state awaiting `triggerAdHocActivities()`
- **Condition holds open** — If `completionCondition` evaluates to false after any child finishes, subprocess remains open
- **Manual override** — The complete API is a forced exit mechanism independent of condition evaluation
- **Active tasks collection** — The `activeTasksCollection` property specifies which activities auto-start when the ad hoc subprocess enters. Multiple activities can be specified as a comma-separated list (e.g., `Task_A,Task_B`)
- **Multi-instance support** — Ad hoc subprocesses support `multiInstanceLoopCharacteristics` for creating multiple instances of the entire ad hoc subprocess


# Additional Resources

*   [BPMN 2.0 Specification: Ad Hoc Sub-Process (Section 10.6)](https://www.omg.org/spec/BPMN/2.0/)
