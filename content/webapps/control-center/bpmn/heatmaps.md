---

title: 'Heatmap'
weight: 60

menu:
  main:
    identifier: "user-guide-control-center-heatmap"
    parent: "user-guide-control-center-bpmn"
    name: "Heatmap"

---

The Heatmap feature provides a visual representation of process execution patterns directly on the BPMN canvas. It helps users quickly identify high traffic areas, bottlenecks, and overall process behavior based on runtime and historic data.

The heatmap overlays execution metrics onto the process model, using color intensity as a visual indicators to highlight quantity and time spent on different activities over a selected timeframe. 

{{< img src="../../img/heatmap-example.png" title="Heatmap Example" >}}

## Accessing Heatmap

To view the heatmap:

1. Navigate to the Process Definitions list.
2. Select a process definition and open its Details View
3. Enable the Heatmap visualization from the canvas controls on the top right.
4. Once enabled, the BPMN diagram updates dynamically to reflect execution data.
5. Modify view by clicking on Heatmap settings dropdown. Settings allow customizing view by quantity or time spent for a selected timeframe. 

## Interactivity with Canvas

The heatmap works seamlessly with the interactive canvas:
- Selecting an activity still enables filtering of related data in the tabs below
- Users can correlate visual activity levels with Incidents and Jobs Definitions data in the bottom tabs section. 

## Key Use Cases

- Identify bottlenecks
  - Detect activities with unusually high load that may be slowing down the process

- Analyze process flow
  - Understand which paths are most commonly taken in a process

- Optimize performance
  - Focus improvement efforts on heavily used or problematic areas

- Support troubleshooting
  - Correlate high activity regions with incidents or delays