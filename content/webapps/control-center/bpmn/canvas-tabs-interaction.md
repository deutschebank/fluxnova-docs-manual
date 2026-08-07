---

title: 'Canvas & Tabs Interaction'
weight: 120

menu:
  main:
    identifier: "user-guide-control-center-canvas-tabs-interaction"
    parent: "user-guide-control-center-bpmn"
    name: "Canvas & Tabs Interaction"

---
The Canvas & Tabs Interaction feature provides a synchronized and intuitive way to explore process behavior by linking the visual process model with detailed data.

This interaction enables users to seamlessly navigate between the BPMN diagram (canvas) and the data tables (tabs), improving efficiency in analysis, troubleshooting, and process understanding.

## Overview

The Process Definition and Process Instance detail views combine:
- A BPMN canvas for visualizing the process flow
- A set of data tabs containing operational details such as instances, incidents, jobs, and variables

These components are interconnected through bi-directional interaction, ensuring that selections in one view are reflected in the other.

## Bi-Directional Interaction

{{< video src="../../img/canvas-tab-interaction-loop.mp4" title="Canvas Tab Interaction Demo" >}}

### Canvas → Tabs

- Selecting an activity on the BPMN canvas filters the data displayed in the tabs below
- Only records relevant to the selected activity are shown (e.g., related incidents, or jobs)

### Tabs → Canvas

- Selecting a row in any tab highlights the corresponding activity in the BPMN diagram
- This helps users quickly locate where in the process a specific event or issue is occurring

Note: History tab currently supports Tab → Canvas interaction only. 

### Key Benefits

- Contextual analysis
  - Link process structure with real-time execution data
- Faster troubleshooting
  - Identify exactly where incidents or failures occur within the process
- Improved navigation
  - Eliminate the need to manually search for related data across multiple views
- Enhanced visibility
  - Gain a complete understanding of process behavior from both visual and data perspectives