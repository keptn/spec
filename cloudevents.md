# Keptn Cloud Events

* [Create Project](#create-project)
* [Delete Project](#delete-project)
* [Create Service](#create-service)
* [Configuration Change](#configuration-change)
* [Deployment Finished](#deployment-finished)
* [Tests Finished](#tests-finished)
* [Start Evaluation](#start-evaluation)
* [Evaluation Done](#evaluation-done)
* [Problem Open](#problem-open)
* [Problem](#problem)
* [Configure Monitoring](#configure-monitoring)
* [Get SLI](#get-sli)
* [Get SLI Done](#get-sli-done)
* [Approval Triggered](#approval-triggered)
* [Approval Finished](#approval-finished)
* [Remediation Triggered](#remediation-triggered)
* [Remediation Started](#remediation-started)
* [Remediation Status Changed](#remediation-status-changed)
* [Remediation Finished](#remediation-finished)
* [Action Triggered](#action-triggered)
* [Action Started](#action-started)
* [Action Finished](#action-finished)

---

## Keptn Cloud Event

All Keptn events conform to CloudEvents - [Version 0.2](https://github.com/cloudevents/spec/blob/v0.2/spec.md).  CloudEvents is a vendor-neutral specification for defining the format of event data.

Events of type `approval.finished`, `action.started`, and `action.finished` 
will have a payload structure as follows (i.e. contain a `triggeredid` as attribute):
```json
"sh.keptn.event": {
  "required": [
    "contenttype",
    "data",
    "id",
    "shkeptncontext",
    "triggeredid",
    "source",
    "specversion",
    "time",
    "type",
  ],
  "properties": {
    "contenttype": {
      "type": "string",
      "description":"Content type of the data attribute value",
      "value": "application/json"
    },
    "data": {
      "type": ["object", "string"],
      "description": "The Keptn event payload. The payload depends on the event type."
    },
    "id": {
      "type": "string",
      "minLength": 1,
      "description": "Unique UUID for the Keptn event"
    },
    "shkeptncontext": {
      "format": "uuid",
      "type": "string",
      "description": "Unique UUID value that connects various events together"
    },
    "triggeredid": {
      "format": "uuid",
      "type": "string",
      "description": "The id which has triggered the step"
    },
    "source": {
      "format": "uri-reference",
      "type": "string",
      "minLength": 1,
      "description": "URL to service implementation in Keptn code repo"
    },
    "specversion": {
      "type": "string",
      "minLength": 1,
      "description": "The version of the CloudEvents specification which the event uses",
      "value": "0.2"
    },
    "time": {
      "format": "date-time",
      "type": "string",
      "description": "Timestamp of when the event happened"
    },
    "type": {
      "type": "string",
      "minLength": 1,
      "description": "Keptn event name"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

All other events will have a payload structure as follows:
```json
"sh.keptn.event": {
  "required": [
    "contenttype",
    "data",
    "id",
    "shkeptncontext",
    "source",
    "specversion",
    "time",
    "type",
  ],
  "properties": {
    "contenttype": {
      "type": "string",
      "description":"Content type of the data attribute value",
      "value": "application/json"
    },
    "data": {
      "type": ["object", "string"],
      "description": "The Keptn event payload. The payload depends on the event type."
    },
    "id": {
      "type": "string",
      "minLength": 1,
      "description": "Unique UUID for the Keptn event"
    },
    "shkeptncontext": {
      "format": "uuid",
      "type": "string",
      "description": "Unique UUID value that connects various events together"
    },
    "source": {
      "format": "uri-reference",
      "type": "string",
      "minLength": 1,
      "description": "URL to service implementation in Keptn code repo"
    },
    "specversion": {
      "type": "string",
      "minLength": 1,
      "description": "The version of the CloudEvents specification which the event uses",
      "value": "0.2"
    },
    "time": {
      "format": "date-time",
      "type": "string",
      "description": "Timestamp of when the event happened"
    },
    "type": {
      "type": "string",
      "minLength": 1,
      "description": "Keptn event name"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

## Create Project

The *project create* event is sent when a new project should be created.

### type
```json
"type": "sh.keptn.internal.event.project.create"
```

### data
```json
"ProjectCreateEventData": {
  "required": [
    "project",
    "shipyard"
  ],
  "properties": {
    "gitRemoteURL": {
      "type": "string"
    },
    "gitToken": {
      "type": "string"
    },
    "gitUser": {
      "type": "string"
    },
    "project": {
      "type": "string"
    },
    "shipyard": {
      "type": "string",
      "format": "base64 encoded"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.internal.event.project.create</summary>
<p>

```json
{
  "type": "sh.keptn.internal.event.project.create",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/api",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "gitUser": "scott",
    "gitToken": "token",
    "gitRemoteURL": "https://github.com/project/repository",
    "shipyard": "c3RhZ2VzOg0KICAtIG5hbWU6ICJkZXYiDQogICAgZGVwbG95bWVudF9zdHJhdGVneTogImRpcmVj
                dCINCiAgICB0ZXN0X3N0cmF0ZWd5OiAiZnVuY3Rpb25hbCINCiAgLSBuYW1lOiAic3RhZ2luZyIN
                CiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHRlc3Rf
                c3RyYXRlZ3k6ICJwZXJmb3JtYW5jZSINCiAgLSBuYW1lOiAicHJvZHVjdGlvbiINCiAgICBkZXBs
                b3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHJlbWVkaWF0aW9uX3N0
                cmF0ZWd5OiAiYXV0b21hdGVkIg0K"
  }
}

```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Delete Project

The *project delete* event is sent when a project should be deleted.

### type
```json
"type": "sh.keptn.internal.event.project.delete"
```

### data
```json
"ProjectDeleteEventData": {
  "required": [
    "project",
  ],
  "properties": {
    "project": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.internal.event.project.delete</summary>
<p>

```json
{
  "type": "sh.keptn.internal.event.project.delete",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/api",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop"
  }
}

```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Create Service

The *service create* event is sent when a new service should be created.

### type
```json
"type": "sh.keptn.internal.event.service.create"
```

### data
```json
"ServiceCreateEventData": {
  "required": [
    "project",
    "service",
    "helmChart",
    "deploymentStrategies"
  ],
  "properties": {
    "deploymentStrategies": {
      "patternProperties": {
        ".*": {
          "type": "integer"
        }
      },
      "type": "object"
    },
    "helmChart": {
      "type": "string",
      "format": "base64 encoded"
    },
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.internal.event.service.create</summary>
<p>

```json
{
  "type": "sh.keptn.internal.event.service.create",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/api",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "service": "carts",
    "helmChart": "c3RhZ2VzOg0KICAtIG5hbWU6ICJkZXYiDQogICAgZGVwbG95bWVudF9zdHJhdGVneTogImRpcmVj
                dCINCiAgICB0ZXN0X3N0cmF0ZWd5OiAiZnVuY3Rpb25hbCINCiAgLSBuYW1lOiAic3RhZ2luZyIN
                CiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHRlc3Rf
                c3RyYXRlZ3k6ICJwZXJmb3JtYW5jZSINCiAgLSBuYW1lOiAicHJvZHVjdGlvbiINCiAgICBkZXBs
                b3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHJlbWVkaWF0aW9uX3N0
                cmF0ZWd5OiAiYXV0b21hdGVkIg0K",
    "deploymentStrategies": {
      "*": 0
    }
  }
}

```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Configuration Change

The *configuration change* event is sent when a desired state for a service is available and has to be updated in its configuration.

### type
```json
"type": "sh.keptn.event.configuration.change"
```

### data
```json
"ConfigurationChangeEventData": {
  "required": [
    "project",
    "service",
    "stage"
  ],
  "properties": {
    "canary": {
      "$ref": "#/definitions/Canary"
    },
    "fileChangesGeneratedChart": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "fileChangesUmbreallaChart": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "fileChangesUserChart": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "valuesCanary": {
      "patternProperties": {
        ".*": {
          "additionalProperties": true,
          "type": "object"
        }
      },
      "type": "object"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"Canary": {
  "required": [
    "action"
  ],
  "properties": {
    "action": {
      "type": "integer"
    },
    "value": {
      "type": "integer"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.configuration.change</summary>
<p>

```json
{
  "type": "sh.keptn.event.configuration.change",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/helm-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "valuesCanary": {
      "image": "docker.io/keptnexamples/carts:0.9.1"
    },
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    }
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Deployment Finished

The *deployment-finished* event is sent when a desired state of a service is deployed in a stage.

### type
```json
"type": "sh.keptn.events.deployment-finished"
```

### data
```json
"DeploymentFinishedEventData": {
  "required": [
    "project",
    "stage",
    "service",
    "teststrategy",
    "deploymentstrategy",
    "tag",
    "image"
  ],
  "properties": {
    "deploymentstrategy": {
      "type": "string"
    },
    "image": {
      "type": "string"
    },
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "tag": {
      "type": "string"
    },
    "teststrategy": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "deploymentURILocal": {
      "type": "string"
    },
    "deploymentURIPublic": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.events.deployment-finished</summary>
<p>

```json
{
  "type": "sh.keptn.events.deployment-finished",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/helm-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "testStrategy": "performance",
    "deploymentStrategy": "direct",
    "tag": "0.9.1",
    "image": "docker.io/keptnexamples/carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
    "deploymentURILocal": "http://carts.sockshop-staging.svc.cluster.local"
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Tests Finished

The *tests-finished* event is sent when the tests for a service in a stage are finished.

### type
```json
"type": "sh.keptn.events.tests-finished"
```

### data
```json
"TestsFinishedEventData": {
  "required": [
    "project",
    "service",
    "stage",
    "teststrategy",
    "deploymentstrategy",
    "start",
    "end",
    "result"
  ],
  "properties": {
    "deploymentstrategy": {
      "type": "string"
    },
    "end": {
      "type": "string"
    },
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "start": {
      "type": "string"
    },
    "teststrategy": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
     },
    "result": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.events.tests-finished</summary>
<p>

```json
{
  "type": "sh.keptn.events.tests-finished",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/jmeter-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "testStrategy": "performance",
    "deploymentStrategy": "direct",
    "start": "2019-09-01 12:00:00",
    "end": "2019-09-01 12:05:00",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
    "result": "pass"
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Start Evaluation

The *start-evaluation* event is sent when the evaluation of a test run should be triggered.

### type
```json
"type": "sh.keptn.event.start-evaluation"
```

### data
```json
"StartEvaluationEventData": {
  "required": [
    "project",
    "service",
    "stage",
    "teststrategy",
    "deploymentstrategy",
    "start",
    "end"
  ],
  "properties": {
    "deploymentstrategy": {
      "type": "string"
    },
    "end": {
      "type": "string"
    },
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "start": {
      "type": "string"
    },
    "teststrategy": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.events.start-evaluation</summary>
<p>

```json
{
  "type": "sh.keptn.events.start-evaluation",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/api",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "teststrategy": "performance",
    "deploymentstrategy": "direct",
    "start": "2019-09-01 12:00:00",
    "end": "2019-09-01 12:05:00",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    }
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Evaluation Done

The *evaluation-done* event is sent when the evaluation of the test execution is completed.

### type
```json
"type": "sh.keptn.events.evaluation-done"
```

### data
```json
"EvaluationDoneEventData": {
  "required": [
    "evaluationdetails",
    "result",
    "project",
    "stage",
    "service",
    "teststrategy",
    "deploymentstrategy"
  ],
  "properties": {
    "deploymentstrategy": {
      "type": "string"
    },
    "evaluationdetails": {
      "$ref": "#/definitions/EvaluationDetails"
    },
    "project": {
      "type": "string"
    },
    "result": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "teststrategy": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"EvaluationDetails": {
  "required": [
    "timeStart",
    "timeEnd",
    "result",
    "score",
    "sloFileContent",
    "indicatorResults"
  ],
  "properties": {
    "indicatorResults": {
      "items": {
        "$ref": "#/definitions/SLIEvaluationResult"
      },
      "type": "array"
    },
    "result": {
      "type": "string"
    },
    "score": {
      "type": "number"
    },
    "sloFileContent": {
      "type": "string"
    },
    "timeEnd": {
      "type": "string"
    },
    "timeStart": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"SLIEvaluationResult": {
  "required": [
    "score",
    "value",
    "targets",
    "status"
  ],
  "properties": {
    "score": {
      "type": "number"
    },
    "status": {
      "type": "string"
    },
    "targets": {
      "items": {
        "$ref": "#/definitions/SLITarget"
      },
      "type": "array"
    },
    "value": {
      "$ref": "#/definitions/SLIResult"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"SLIResult": {
  "required": [
    "metric",
    "value",
    "success"
  ],
  "properties": {
    "message": {
      "type": "string"
    },
    "metric": {
      "type": "string"
    },
    "success": {
      "type": "boolean"
    },
    "value": {
      "type": "number"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"SLITarget": {
  "required": [
    "criteria",
    "targetValue",
    "violated"
  ],
  "properties": {
    "criteria": {
      "type": "string"
    },
    "targetValue": {
      "type": "number"
    },
    "violated": {
      "type": "boolean"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.events.evaluation-done</summary>
<p>

```json
{
   "contenttype":"application/json",
   "data":{
      "deploymentstrategy":"blue_green_service",
      "evaluationdetails":{
         "indicatorResults":[
            {
               "score":0,
               "status":"failed",
               "targets":[
                  {
                     "criteria":"<=800",
                     "targetValue":800,
                     "violated":true
                  },
                  {
                     "criteria":"<=+10%",
                     "targetValue":549.1967956487127,
                     "violated":true
                  },
                  {
                     "criteria":"<600",
                     "targetValue":600,
                     "violated":true
                  }
               ],
               "value":{
                  "metric":"response_time_p95",
                  "success":true,
                  "value":1002.6278552658177
               }
            }
         ],
         "result":"fail",
         "score":0,
         "sloFileContent":"LS0tDQpzcGVjX3ZlcnNpb246ICcxLjAnDQpjb21wYXJpc29uOg0KICBjb21wYXJlX3dpdGg6ICJzaW5nbGVfcmVzdWx0Ig0KICBpbmNsdWRlX3Jlc3VsdF93aXRoX3Njb3JlOiAicGFzcyINCiAgYWdncmVnYXRlX2Z1bmN0aW9uOiBhdmcNCm9iamVjdGl2ZXM6DQogIC0gc2xpOiByZXNwb25zZV90aW1lX3A5NQ0KICAgIHBhc3M6ICAgICAgICAjIHBhc3MgaWYgKHJlbGF0aXZlIGNoYW5nZSA8PSAxMCUgQU5EIGFic29sdXRlIHZhbHVlIGlzIDwgNTAwKQ0KICAgICAgLSBjcml0ZXJpYToNCiAgICAgICAgICAtICI8PSsxMCUiICMgcmVsYXRpdmUgdmFsdWVzIHJlcXVpcmUgYSBwcmVmaXhlZCBzaWduIChwbHVzIG9yIG1pbnVzKQ0KICAgICAgICAgIC0gIjw2MDAiICAgIyBhYnNvbHV0ZSB2YWx1ZXMgb25seSByZXF1aXJlIGEgbG9naWNhbCBvcGVyYXRvcg0KICAgIHdhcm5pbmc6ICAgICAjIGlmIHRoZSByZXNwb25zZSB0aW1lIGlzIGJlbG93IDgwMG1zLCB0aGUgcmVzdWx0IHNob3VsZCBiZSBhIHdhcm5pbmcNCiAgICAgIC0gY3JpdGVyaWE6DQogICAgICAgICAgLSAiPD04MDAiDQp0b3RhbF9zY29yZToNCiAgcGFzczogIjkwJSINCiAgd2FybmluZzogNzUl",
         "timeEnd":"2019-11-18T11:29:36Z",
         "timeStart":"2019-11-18T11:21:06Z"
      },
      "project":"sockshop",
      "result":"fail",
      "service":"carts",
      "stage":"staging",
      "teststrategy":"performance",
      "labels": {
        "testid": "12345",
        "buildnr": "build17",
        "runby": "JohnDoe"
      }
   },
   "id":"1b7cd584-320e-4ef0-8522-8a817263fdab",
   "source":"lighthouse-service",
   "specversion":"0.2",
   "time":"2019-11-18T11:30:45.340Z",
   "type":"sh.keptn.events.evaluation-done",
   "shkeptncontext":"60077081-f902-4407-bc15-7c70be41a836"
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Problem

The generic *problem* event is sent when a problem activity related to a monitored service is happening. For example, a *problem* event is sent when a problem is opened or resolved. 

### type
```json
"type": "sh.keptn.events.problem"
```

### data
```json
"ProblemEventData": {
  "required": [
    "PID",
    "ProblemDetails",
    "ProblemID",
    "ProblemTitle"
  ],
  "properties": {
    "PID": {
      "type": "string",
    },
    "ProblemID": {
      "type": "string"
    },
    "ProblemTitle": {
      "type": "string",
    },
    "ProblemDetails": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "ProblemURL": {
      "type": "string",
    },
    "State": {
      "type": "string"
    },
    "Tags": {
      "type": "string"
    },
    "ImpactedEntities": {
      "type": "string",
    },
    "ImpactedEntity": {
      "type": "string",
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Examples
<details><summary>Example of sh.keptn.events.problem without project, service, and stage</summary>
<p>

```json
{
  "type": "sh.keptn.events.problem",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/dynatrace",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-07-09T09:03:25.437Z",
  "contenttype": "application/json",
  "shkeptncontext": "08635340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "PID": "93a5-3fas-a09d-8ckf",
    "ProblemID": "762",
    "ProblemTitle": "Response time degradation",
    "ProblemDetails": {
      "displayName": "641",
      "endTime": -1,
      "hasRootCause": false,
      "id": "1234_5678V2",
      "impactLevel": "SERVICE",
      "severityLevel": "PERFORMANCE",
      "startTime": 1587624420000,
      "status": "OPEN"
    },
    "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
    "ImpactedEntity": "carts-primary",
    "State": "OPEN",
    "Tags": "keptn_project:sockshop,keptn_stage:production,keptn_service:carts"
  }
}
```

</p>
</details>

<details><summary>Example of sh.keptn.events.problem with project, stage, and service</summary>
<p>

```json
{
  "type": "sh.keptn.events.problem",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/dynatrace",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-07-09T09:03:25.437Z",
  "contenttype": "application/json",
  "shkeptncontext": "08635340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "PID": "93a5-3fas-a09d-8ckf",
    "ProblemID": "762",
    "ProblemTitle": "Response time degradation",
    "ProblemDetails": {
      "displayName": "641",
      "endTime": -1,
      "hasRootCause": false,
      "id": "1234_5678V2",
      "impactLevel": "SERVICE",
      "severityLevel": "PERFORMANCE",
      "startTime": 1587624420000,
      "status": "OPEN"
    },
    "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
    "ImpactedEntity": "carts-primary",
    "State": "OPEN",
    "project": "sockshop",
    "stage": "production",
    "service": "carts"
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Problem Open

The *problem open* event is sent when a monitored service causes any problem **and** the state of the problem is `OPEN`. 

### type
```json
"type": "sh.keptn.event.problem.open"
```

### data
```json
"ProblemEventData": {
  "required": [
    "PID",
    "ProblemDetails",
    "ProblemID",
    "ProblemTitle"
  ],
  "properties": {
    "PID": {
      "type": "string",
    },
    "ProblemID": {
      "type": "string"
    },
    "ProblemTitle": {
      "type": "string",
    },
    "ProblemDetails": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "ProblemURL": {
      "type": "string",
    },
    "ImpactedEntity": {
      "type": "string",
    },
    "State": {
      "type": "string",
      "default": "OPEN"
    },
    "Tags": {
      "type": "string"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "additionalProperties": false,
  "type": "object"
}

```

### Example
<details><summary>Example of sh.keptn.event.problem.open from Prometheus</summary>
<p>

```json
{
  "type": "sh.keptn.event.problem.open",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/prometheus-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "State": "OPEN",
    "PID": "",
    "ProblemID": "762",
    "ProblemTitle": "cpu_usage_sockshop_carts",
    "ProblemDetails": {
      "problemDetails":"Pod name"
    },
    "ImpactedEntity": "carts-primary",
    "project": "sockshop",
    "stage": "production", 
    "service": "service"
  }
}
```
</p>
</details>

<details><summary>Example of sh.keptn.event.problem.open from Dynatrace</summary>
<p>

```json
{
  "type": "sh.keptn.event.problem.open",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/dynatrace-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "State": "OPEN",
    "PID": "93a5-3fas-a09d-8ckf",
    "ProblemID": "ab81-941c-f198",
    "ProblemTitle": "Response time degradation",
    "ProblemDetails": {
      "displayName": "641",
      "endTime": -1,
      "hasRootCause": false,
      "id": "1234_5678V2",
      "impactLevel": "SERVICE",
      "severityLevel": "PERFORMANCE",
      "startTime": 1587624420000,
      "status": "OPEN"
    },
    "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
    "ImpactedEntity": "carts-primary",
    "project": "sockshop",
    "stage": "production", 
    "service": "service"
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))


## Configure Monitoring 

The *monitoring configure* event is sent to configure a monitoring solution for a Keptn cluster.

### type
```json
"type": "sh.keptn.event.monitoring.configure"
```

### data
```json
"ConfigureMonitoringEventData": {
  "required": [
    "project",
    "service",
    "type"
  ],
  "properties": {
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "type": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>sh.keptn.event.monitoring.configure</summary>
<p>

```json
{
  "type": "sh.keptn.event.monitoring.configure",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/prometheus-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "service": "carts",
    "type": "prometheus"
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Get SLI
The *get-sli* event is sent when a SLI provider must be triggered for gathering service level indicators.

### type
```json
"type": "sh.keptn.internal.event.get-sli"
```

### data
```json
"InternalGetSLIEventData": {
  "required": [
    "sliProvider",
    "project",
    "stage",
    "service",
    "start",
    "end",
    "teststrategy",
    "deploymentstrategy",
    "deployment",
    "indicators",
    "customFilters"
  ],
  "properties": {
    "customFilters": {
      "items": {
        "$ref": "#/definitions/SLIFilter"
      },
      "type": "array"
    },
    "end": {
      "type": "string"
    },
    "indicators": {
      "items": {
        "type": "string"
      },
      "type": "array"
    },
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "sliProvider": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "start": {
      "type": "string"
    },
    "teststrategy": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "deploymentstrategy": {
      "type": "string"
    },
    "deployment": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"SLIFilter": {
  "required": [
    "key",
    "value"
  ],
  "properties": {
    "key": {
      "type": "string"
    },
    "value": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.internal.event.get-sli</summary>
<p>

```json
{
  "type": "sh.keptn.internal.event.get-sli",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/lighthouse-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "sliProvider": "dynatrace",
    "project": "sockshop",
    "service": "carts",
    "stage": "dev",
    "start": "2019-10-28T15:44:27.152330783Z",
    "end": "2019-10-28T15:54:27.152330783Z",
    "deployment": "direct",
    "deploymentstrategy": "direct",
    "teststrategy":"manual",
    "indicators": ["throughput", "error_rate", "request_latency_p95"],
    "customFilters": [
      { "key" : "dynatraceEntityName", "value": "HealthCheckController" },
      { "key" : "tags", "value": "test-subject:true" }
    ],
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    }
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Get SLI Done
The *get-sli done* event is sent when the data gathering by a SLI provider is done.

### type
```json
"type": "sh.keptn.internal.event.get-sli.done"
```

### data
```json
"InternalGetSLIDoneEventData": {
  "required": [
    "project",
    "stage",
    "service",
    "start",
    "end",
    "teststrategy",
    "deploymentstrategy",
    "deployment",
    "indicatorValues"
  ],
  "properties": {
    "end": {
      "type": "string"
    },
    "indicatorValues": {
      "items": {
        "$ref": "#/definitions/SLIResult"
      },
      "type": "array"
    },
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "start": {
      "type": "string"
    },
    "teststrategy": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"SLIResult": {
  "required": [
    "metric",
    "value",
    "success"
  ],
  "properties": {
    "message": {
      "type": "string"
    },
    "metric": {
      "type": "string"
    },
    "success": {
      "type": "boolean"
    },
    "value": {
      "type": "number"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.internal.event.get-sli.done</summary>
<p>

```json
{
  "type": "sh.keptn.internal.event.get-sli.done",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/prometheus-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "start": "2019-11-05T16:30:27.152Z",
    "end": "2019-11-05T16:35:27.152Z",
    "teststrategy": "manual",
    "deploymentstrategy": "direct",
    "deployment": "direct",
    "indicatorValues": [
      {
        "metric":"request_latency_p95",
        "value": 1.1620000000000001,
        "success": true
      },
      {
        "metric":"error_rate",
        "value": 0,
        "success": true
      }
    ],
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    }
  }
}
```
</p>
</details>

## Approval Triggered

The *approval.triggered* event is sent when an approval is required before executing the next step 
(e.g. a configuration-change for the next stage).

### type
```json
"type": "sh.keptn.event.approval.triggered"
```

### data
```json
"ApprovalTriggeredEventData": {
  "required": [
    "approval",
    "project",
    "stage",
    "service",
    "image",
    "tag"
  ],
  "properties": {
    "approval": {
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "image": {
      "type": "string"
    },
    "tag": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "deploymentURILocal": {
      "type": "string"
    },
    "deploymentURIPublic": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.approval.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.approval.triggered",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/gatekeeper-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {    
    "approval": {
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "image": "docker.io/keptnexamples/carts",
    "tag": "0.9.1",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
    "deploymentURILocal": "http://carts.sockshop-staging.svc.cluster.local"
  }
}
```

</p>
</details>
([&uarr; up to index](#keptn-cloud-events))

## Approval Finished

The *approval.finished* event is sent for responding an *approval.triggered* event.
This *approval.finished* event contains the result of the approval.

### type
```json
"type": "sh.keptn.event.approval.finished"
```

### data
```json
"ApprovalFinishedEventData": {
  "required": [
    "approval",
    "project",
    "stage",
    "service",
    "image",
    "tag"
  ],
  "properties": {
    "approval": {
      "required": [
        "result",
        "status",
      ],
      "result": { // Enum: pass (represents an approval), failed (represents a disapproval)
        "type": "string" 
      },
      "status": { // Enum: succeeded, errored, unknown
        "type": "string" 
      },
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "image": {
      "type": "string"
    },
    "tag": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.approval.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.approval.finished",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/gatekeeper-service",
  "id": "ggb878d3-03c0-4e8f-bc3f-454bc1b3d888",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid" : "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "approval": {
      "result": "pass",
      "status": "succeeded"
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "image": "docker.io/keptnexamples/carts",
    "tag": "0.9.1",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
    "deploymentURILocal": "http://carts.sockshop-staging.svc.cluster.local"
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Remediation Triggered

The *remediation.triggered* event indicates the start of following remediation actions.

### type
```json
"type": "sh.keptn.event.remediation.triggered"
```

### data
```json
"RemediationTriggeredEventData": {
  "required": [
    "remediation",
    "problem",
    "project",
    "stage",
    "service"
  ],
  "properties": {
    "remediation": {
      "type": "object"
    },
    "problem": {
      "required": [
        "PID",
        "ProblemDetails",
        "ProblemID",
        "ProblemTitle"
      ],
      "ImpactedEntities": {
        "type": "string",
      },
      "PID": {
        "type": "string",
      },
      "ProblemDetails": {
        "items": {
          "type": "integer"
        },
        "type": "array"
      },
      "ProblemID": {
        "type": "string"
      },
      "ProblemTitle": {
        "type": "string",
      },
      "State": {
        "type": "string"
      },
      "Tags": {
        "type": "string"
      },
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.remediation.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.triggered",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {    
    "remediation": {
    },
    "problem": {
      "ImpactedEntity": "carts-primary",
      "PID": "93a5-3fas-a09d-8ckf",
      "ProblemDetails": "Pod name",
      "ProblemID": "762",
      "ProblemTitle": "cpu_usage_sockshop_carts",
      "State": "OPEN",
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
  }
}
```

</p>
</details>
([&uarr; up to index](#keptn-cloud-events))

## Remediation Started

The *remediation.started* event is sent when a remediation workflow is started.

### type
```json
"type": "sh.keptn.event.remediation.started"
```

### data
```json
"RemediationStartedEventData": {
  "required": [
    "remediation",
    "project",
    "stage",
    "service",
  ],
  "properties": {
    "remediation": {
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.remediation.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.started",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "ggb878d3-03c0-4e8f-bc3f-454bc1b3d888",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "remediation": {
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Remediation Status Changed

The *remediation.status.changed* event is sent when a remediation action was triggered but there are further remediation actions available.

### type
```json
"type": "sh.keptn.event.remediation.status.changed"
```

### data
```json
"RemediationStatusChangedEventData": {
  "required": [
    "remediation",
    "project",
    "stage",
    "service",
  ],
  "properties": {
    "remediation": {
      "required": [
        "status",
        "result",
      ],
      "status": { // Enum: succeeded, errored, unknown
        "type": "string" 
      },
      "result": {
        "required": [
          "actionIndex",
          "actionName",
        ],
        "actionIndex": {
          "type": "integer"
        },      
        "actionName": {
          "type": "string"
        },
      },
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.remediation.status.changed</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.status.changed",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "ggb878d3-03c0-4e8f-bc3f-454bc1b3d888",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "remediation": {
      "status": "succeeded",
      "result": {
        "actionIndex": 0,
        "actionName": "scaling",
      }
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Remediation Finished

The *remediation.finished* event is sent when a remediation action is finished

### type
```json
"type": "sh.keptn.event.remediation.finished"
```

### data
```json
"RemediationFinishedEventData": {
  "required": [
    "remediation",
    "project",
    "stage",
    "service",
  ],
  "properties": {
    "remediation": {
      "required": [
        "status",
        "result",
      ],
      "status": { // Enum: succeeded, errored, unknown
        "type": "string" 
      },
      "result": {
        "type": "string"  // Enum: Pass or failed
      },
      "message": {
        "type": "string"
      },
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.remediation.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.finished",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "ggb878d3-03c0-4e8f-bc3f-454bc1b3d888",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "remediation": {
      "status": "succeeded",
      "result": "pass"
    },
    "problem": {
      "ImpactedEntity": "carts-primary",
      "PID": "93a5-3fas-a09d-8ckf",
      "ProblemDetails": "Pod name",
      "ProblemID": "762",
      "ProblemTitle": "cpu_usage_sockshop_carts",
      "State": "OPEN",
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Action Triggered

The *action.triggered* event triggers a remediation action.

### type
```json
"type": "sh.keptn.event.action.triggered"
```

### data
```json
"ActionTriggeredEventData": {
  "required": [
    "action",
    "problem",
    "project",
    "stage",
    "service"
  ],
  "properties": {
    "action": {
      "required": [
        "name",
        "action",
      ],     
      "name": {
        "type": "string"
      },
      "action": {
        "type": "string"
      },
      "description": {
        "type": "string"
      },
      "value": {
        "type": ["object", "string"]
      },
      "type": "object"
    },
    "problem": {
      "required": [
        "PID",
        "ProblemDetails",
        "ProblemID",
        "ProblemTitle"
      ],
      "ImpactedEntities": {
        "type": "string",
      },
      "PID": {
        "type": "string",
      },
      "ProblemDetails": {
        "items": {
          "type": "integer"
        },
        "type": "array"
      },
      "ProblemID": {
        "type": "string"
      },
      "ProblemTitle": {
        "type": "string",
      },
      "State": {
        "type": "string"
      },
      "Tags": {
        "type": "string"
      },
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.action.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.action.triggered",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {    
    "action": {
      "name": "Feature toggeling",
      "action": "toggle-feature",
      "description": "toggles a feature",
      "value": {
        "EnableItemCache": "on"
      }
    },
    "problem": {
      "ImpactedEntity": "carts-primary",
      "PID": "93a5-3fas-a09d-8ckf",
      "ProblemDetails": "Pod name",
      "ProblemID": "762",
      "ProblemTitle": "cpu_usage_sockshop_carts",
      "State": "OPEN",
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Action Started

The *action.started* event is sent when a remediation action is started by the action provider.

### type
```json
"type": "sh.keptn.event.action.started"
```

### data
```json
"ActionStartedEventData": {
  "required": [
    "action",
    "project",
    "stage",
    "service",
  ],
  "properties": {
    "action": {
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.action.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.action.started",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/unleash-service",
  "id": "ggb878d3-03c0-4e8f-bc3f-454bc1b3d888",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "action": {
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Action Finished

The *action.finished* event is sent when a remediation action is finished.

### type
```json
"type": "sh.keptn.event.action.finished"
```

### data
```json
"ActionFinishedEventData": {
  "required": [
    "action",
    "problem",
    "project",
    "stage",
    "service",
  ],
  "properties": {
    "action": {
      "required": [
        "result",
        "status",
      ],
      "result": { // Enum: pass , failed 
        "type": "string" 
      },
      "status": { // Enum: succeeded, errored, unknown
        "type": "string" 
      },
      "type": "object"
    },
    "problem": {
      "required": [
        "PID",
        "ProblemDetails",
        "ProblemID",
        "ProblemTitle"
      ],
      "ImpactedEntities": {
        "type": "string",
      },
      "PID": {
        "type": "string",
      },
      "ProblemDetails": {
        "items": {
          "type": "integer"
        },
        "type": "array"
      },
      "ProblemID": {
        "type": "string"
      },
      "ProblemTitle": {
        "type": "string",
      },
      "State": {
        "type": "string"
      },
      "Tags": {
        "type": "string"
      },
      "type": "object"
    },
    "project": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example
<details><summary>Example of sh.keptn.event.action.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.action.finished",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/unleash-service",
  "id": "ggb878d3-03c0-4e8f-bc3f-454bc1b3d888",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "action": {
      "result": "pass",
      "status": "succeeded"
    },
    "problem": {
      "ImpactedEntity": "carts-primary",
      "PID": "93a5-3fas-a09d-8ckf",
      "ProblemDetails": "Pod name",
      "ProblemID": "762",
      "ProblemTitle": "cpu_usage_sockshop_carts",
      "State": "OPEN",
    },
    "project": "sockshop",
    "stage": "staging",
    "service": "carts",
    "labels": {
      "testid": "12345",
      "buildnr": "build17",
      "runby": "JohnDoe"
    },
  }
}
```

</p>
</details>

([&uarr; up to index](#keptn-cloud-events))