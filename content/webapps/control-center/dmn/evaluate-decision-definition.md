---

title: 'Evaluate Decision Definition'
weight: 20

menu:
  main:
    identifier: "user-guide-control-center-dmn-evaluate-decision-definition"
    parent: "user-guide-control-center-dmn"
    name: "Evaluate Decision Definition"

---

The Evaluate Decision Definition feature allows users to execute and validate decision logic directly within Fluxnova Control Center. It provides a simple interface to test decision outcomes by supplying input data and reviewing the resulting output.

This functionality is primarily used for verification, debugging, and analysis of decision models.

### Overview

Decision evaluation is available within the Decision Definition Details View and enables users to:

- Execute decision logic
- Validate rule behavior using custom input values
- Quickly test different scenarios and outcomes

## Accessing Decision Evaluation

{{< video src="../../img/decision-evaluation.mp4" title="Evaluate Decision" >}}

To evaluate a decision definition:

1. Navigate to the Decision Definition List View
2. Select the desired decision definition
3. Open the Decision Definition Details View
4. Click the Evaluate Decision option in the top-right header
5. This opens the evaluation interface where input data can be provided in JSON format.
6. Click on Evaluate to see populated results displayed on the right section of the interface.  

### Input Configuration

To execute a decision, users must provide input data in JSON format.

### Key Capabilities

- Enter input variables required by the decision logic
- Modify values to test different scenarios
- Use example or reference input to guide formatting

#### Example Input

{{< code language="json">}}
{
  "variables": {
    "season": {
      "value": "Spring",
      "type": "String"
    },
    "guestCount": {
      "value": 42,
      "type": "Double"
    }
  }
}
{{< /code >}}

### Use Cases

- Decision Validation
  - Ensure that decision rules produce expected results
- Scenario Testing
  - Test multiple input combinations without invoking BPMN processes
- Debugging
  - Identify incorrect inputs or unexpected decision outcomes
- Pre-deployment Verification
  - Confirm correctness of decision logic before use in production

## Best Practices

- Ensure JSON input is valid and properly structured
- Provide all required variables expected by the decision
- Verify data types (e.g., string, number, boolean)
- Test both typical and edge-case scenarios
- Re-evaluate decisions after updates or version changes
- Considerations
  - Decision evaluation is independent of process execution
  - Incorrect or incomplete input may result in unexpected or incomplete output
  - Results are based solely on provided input and current decision definition version