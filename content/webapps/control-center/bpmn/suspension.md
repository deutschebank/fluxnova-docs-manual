---

title: 'Suspension'
weight: 110

menu:
  main:
    identifier: "user-guide-control-center-bpmn-suspension"
    parent: "user-guide-control-center-bpmn"
    name: "Suspension"

---

In the process definition view and in the process instance view you can suspend the selected process definition or process instance by using the suspend button.

## Process Definition Suspension

If you suspend the process definition, you prevent the process definition from being instantiated. No further operations can be done while the process definition is in the suspended state. You can simply re-activate the process definition by using the activate button. You have the option of suspending/reactivating all process instances of the process definition as well as defining if the process definition (and process instances) should instantly be suspended/reactivated or at a specific time in a confirmation dialog. Find more information about this functionality in the [suspending process definitions]({{< ref "/user-guide/process-engine/process-engine-concepts.md#suspend-process-definitions" >}}) section of the process engine chapter.

## Process Instance Suspension

If you suspend the process instance, you can prevent the process instance from being executed any further. This includes suspending all tasks included in the process instance. You can simply re-activate the process instance by using the activate button. Find more information about this functionality in the [suspending process instances]({{< ref "/user-guide/process-engine/process-engine-concepts.md#suspend-process-instances" >}}) section of the process engine chapter.


## Job Definition & Job Suspension

In the Process Definition Details View you have the option of suspending a job definition within the tab data. This can be done by using the suspend button displayed in the action row of the Job Definitions tab at the bottom of the screen. By doing this, you can prevent this job definition from being processed in all process instances of the selected process definition. You can simply re-activate the job definition by using the activate button in the same action row. Find more information about this functionality in the suspending and [activating job execution]({{< ref "/user-guide/process-engine/process-engine-concepts.md#suspend-and-activate-job-execution" >}}) section of the user guide.
