---

title: 'Extension Attributes'
weight: 60

menu:
  main:
    identifier: "user-guide-dmn-model-api-extension-attributes"
    parent: "user-guide-dmn-model-api"

---


[Custom extensions]({{< ref "/reference/dmn/custom-extensions/_index.md" >}}) are a standardized way to extend the DMN model.
The [Flowave extension attributes]({{< ref "/reference/dmn/custom-extensions/flowave-attributes.md" >}}) are fully implemented in the DMN model API.

Every DMN `Decision` element can have the attributes `historyTimeToLive` and `versionTag`.
To access the extension attributes, you have to call the `Decision#getFlowaveHistoryTimeToLiveString()` and 
`Decision#getVersionTag()` methods. 

```java
String historyTimeToLive = decision.getFlowaveHistoryTimeToLiveString();
String versionTag = decision.getVersionTag();
```
To set attributes, use `Decision#setFlowaveHistoryTimeToLiveString()` and `Decision#setVersionTag()`
```java
decision.setFlowaveHistoryTimeToLiveString("1000");
decision.setVersionTag("1.0.0");
```

Every `Input` element can have an `inputVariable` attribute.
This attribute specifies the variable name which can be used to access the result of the input expression in an input entry expression.
It can be set and fetched similarly, calling `Input#setFlowaveInputVariable()` and `Input#getFlowaveInputVariable()`:

```java
input.setFlowaveInputVariable("flowaveInput");
String flowaveInput = input.getFlowaveInputVariable();
```
