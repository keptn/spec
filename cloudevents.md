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

---

## Keptn Cloud Event

All Keptn events conform to CloudEvents - [Version 0.2](https://github.com/cloudevents/spec/blob/v0.2/spec.md).  CloudEvents is a vendor-neutral specification for defining the format of event data.

All events will have a payload structure as follows:
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
    "image": "docker.io/keptnexamples/carts" 
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
    "end": "2019-09-01 12:05:00"
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
    "testStrategy": "performance",
    "deploymentStrategy": "direct",
    "start": "2019-09-01 12:00:00",
    "end": "2019-09-01 12:05:00"
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
    "service",
    "stage",
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
    "violations",
    "status"
  ],
  "properties": {
    "score": {
      "type": "number"
    },
    "status": {
      "type": "string"
    },
    "value": {
      "$ref": "#/definitions/SLIResult"
    },
    "violations": {
      "items": {
        "$ref": "#/definitions/SLIViolation"
      },
      "type": "array"
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
"SLIViolation": {
  "required": [
    "criteria",
    "targetValue"
  ],
  "properties": {
    "criteria": {
      "type": "string"
    },
    "targetValue": {
      "type": "number"
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
  "type": "sh.keptn.events.evaluation-done",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/jmeter-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "project": "sockshop",
    "result": "warning",
    "service": "carts",
    "stage": "staging",
    "teststrategy": "performance",
    "deploymentstrategy":"blue_green_service",
    "evaluationdetails": {
      "indicatorResults": [
        {
          "score": 0.5,
          "value": {
              "metric": "request_latency_p95",
              "value": 1.162000,
              "success": true
          },
          "violations": [
            {
              "criteria": "<=+10%",
              "targetValue": 0
            }
          ],
          "status": "warning"
        },
        {
          "score": 2,
          "value": {
              "metric": "error_rate",
              "value": 0,
              "success": true
          },
          "violations": null,
          "status": "pass"
        }
      ],
      "result": "warning",
      "score": 83.34,
      "timeEnd": "2019-11-05T16:35:27.152Z",
      "timeStart": "2019-11-05T16:30:27.152Z"
    }
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Problem Open

The *problem open* event is sent when a monitored service causes any problem.

### type
```json
"type": "sh.keptn.event.problem.open"
```

### data
```json
"ProblemEventData": {
  "required": [
    "ProblemID",
    "ProblemTitle",
    "ProblemDetails",
    "PID"
  ],
  "properties": {
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
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}

```

### Example
<details><summary>Example of sh.keptn.event.problem.open</summary>
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
    "ImpactedEntity": "carts-primary",
    "PID": "93a5-3fas-a09d-8ckf",
    "ProblemDetails": "Pod name",
    "ProblemTitle": "cpu_usage_sockshop_carts",
    "State": "OPEN",
    "project": "sockshop",
    "stage": "production", 
    "service": "service"
  },
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))

## Problem

The *problem* event is sent when a monitored service causes any problem.

### type
```json
"type": "sh.keptn.events.problem"
```

### data
```json
"ProblemEventData": {
  "required": [
    "ProblemID",
    "ProblemTitle",
    "ProblemDetails",
    "PID"
  ],
  "properties": {
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
    "project": {
      "type": "string"
    },
    "service": {
      "type": "string"
    },
    "stage": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}

```

### Example
<details><summary>Example of sh.keptn.events.problem</summary>
<p>

```json
{
  "type": "sh.keptn.events.problem",
  "specversion": "0.2",
  "source": "https://github.com/keptn/keptn/prometheus-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "contenttype": "application/json",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "ImpactedEntity": "carts-primary",
    "PID": "93a5-3fas-a09d-8ckf",
    "ProblemDetails": "Pod name",
    "ProblemTitle": "cpu_usage_sockshop_carts",
    "State": "OPEN"
  },
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
    "type": "prometheus",
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
    "teststrategy":"manual",
    "indicators": ["throughput", "error_rate", "request_latency_p95"],
    "customFilters": [
      { "key" : "dynatraceEntityName", "value": "HealthCheckController" },
      { "key" : "tags", "value": "test-subject:true" }
    ]
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
    ]
  }
}
```
</p>
</details>

([&uarr; up to index](#keptn-cloud-events))