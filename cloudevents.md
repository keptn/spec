# Keptn Cloud Events

* [Approval](#approval)
* [Deployment](#deployment)
* [Test](#test)
* [Evaluation](#evaluation)
* [Release](#release)
* [Remediation](#remediation)
* [Action](#action)
* [Get-SLI](#get-sli) - not implemented yet

---

## Keptn CloudEvents

All Keptn events conform to the CloudEvents spec in [version 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md). The CloudEvents specification is a vendor-neutral specification for defining the format of event data.

In Keptn, events have a payload structure as follows (*Note:* The `triggeredid` is not contained in events of type `triggered` mentioned below):

```json
"sh.keptn.event": {
  "required": [
    "data",
    "id",
    "shkeptncontext",
    "source",
    "specversion",
    "time",
    "triggeredid",
    "type"
  ],
  "properties": {
    "data": {
      "type": ["object", "string"],
      "description": "The Keptn event payload depending on the type."
    },
    "id": {
      "type": "string",
      "minLength": 1,
      "description": "Unique UUID of the Keptn event"
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
      "description": "The version of the CloudEvents specification",
      "value": "1.0"
    },
    "time": {
      "format": "date-time",
      "type": "string",
      "description": "Timestamp of when the event happened"
    },
    "triggeredid": {
      "format": "uuid",
      "type": "string",
      "description": "The event ID that has triggered the step"
    },
    "type": {
      "type": "string",
      "minLength": 1,
      "description": "Type of the Keptn event"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Type

The event type of a Keptn CloudEvent has the format: 

* `sh.keptn.event.[task].[event status]`

As indicated by the brackets, the event type is defined by a **task** and the **event status**. 
* The task is declared in the [Shipyard](https://github.com/keptn/spec/blob/master/shipyard.md) of a project. For example, a Shipyard can contain tasks like: `deployment`, `test`, or `evaluation`. Consequently, the event type for a `deployment` task would be `sh.keptn.event.deployment.[event status]`
* The kinds of event states are defined with: `triggered`, `started`, `status.changed`*, and `finished` (* is optional)

By combining the *task* and *event status* for the `deployment` task, the event types are:

- `sh.keptn.event.deployment.triggered`
- `sh.keptn.event.deployment.started`
- `sh.keptn.event.deployment.status.changed`
- `sh.keptn.event.deployment.finished`

### Data

The data block of a Keptn CloudEvent is structured as follows:

```json
"data": {
  "required": [
    "labels",
    "message",
    "project",
    "result",
    "service",
    "stage",
    "status",
    "[task]"
  ],
  "properties": {
    "labels": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "message": {
      "type": "string",
      "description": "A message from the last task"
    },
    "project": {
      "type": "string",
      "description": "The name of the project"
    },
    "result": {
      "type": "string",
      "description": "The result of the last task"
    },
    "service": {
      "type": "string",
      "description": "The name of the service"
    },
    "stage": {
      "type": "string",
      "description": "The name of the stage"
    },
    "status": {
      "type": "string",
      "description": "The status of the last task"
    },
    "[task]": {
      "type": "object"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

The data block of a Keptn CloudEvent contains the properties: 
- labels
- message
- project
- result
- service
- stage
- status
- *[task]*

Like the task property in the event type, the task property in the data block depends on the task declaration in the Shipyard. Based on the example of a `deployment` task, the data block contains a `deployment` property of type object. Hence, any payload can be added to this `deployment` property.

This is an example of a data block for a `sh.keptn.event.deployment.finished` event:

```
"data": {
  "deployment": {
    "deploymentNames": [ "carts" ],
    "deploymentURIsLocal": [ "carts.svc.cluster.local" ],
    "deploymentURIsPublic": [ "carts.dev.xyz" ],
    "gitCommit": "aaa6c32e817b9435a4b3f6078b4826fd1aefbccc"
  },
  "labels": null,
  "message": "",
  "project": "sockshop",
  "result": "pass",
  "service": "carts",
  "stage": "dev",
  "status": "succeeded"
}
```

## Approval 
([&uarr; up to index](#keptn-cloud-events))

### Approval Triggered

#### Type

```json
"type": "sh.keptn.event.approval.triggered"
```

#### Data

```json
"data": {
  "approval": {
    "pass":"", 
    "warning":"",
  }
}
```

#### Example

<details><summary>Example of sh.keptn.event.approval.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.approval.triggered",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/shipyard-controller",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "approval": {
      "pass":"", 
      "warning":"",
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Approval Started

#### Type

```json
"type": "sh.keptn.event.approval.started"
```

#### Data

*No type specific data block specified.* 

#### Example

<details><summary>Example of sh.keptn.event.approval.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.approval.started",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/bridge",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "approval": {
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Approval Finished

#### Type

```json
"type": "sh.keptn.event.approval.finished"
```

#### Data

*No type specific data block specified.* 

#### Example
<details><summary>Example of sh.keptn.event.approval.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.approval.finished",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/bridge",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "approval": {
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

## Deployment 
([&uarr; up to index](#keptn-cloud-events))

### Deployment Triggered

#### Type

```json
"type": "sh.keptn.event.deployment.triggered"
```

#### Data

```json
"data": {
  "deployment": {
    "deploymentstrategy":""
  },
  "configurationChange": {
    "values": { }
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.deployment.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.deployment.triggered",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/shipyard-controller",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "configurationChange": {
      "values": { }
    },
    "deployment": {
      "deploymentstrategy":""
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Deployment Started


#### Type

```json
"type": "sh.keptn.event.deployment.started"
```

#### Data

*No type specific data block specified.* 

#### Example
<details><summary>Example of sh.keptn.event.deployment.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.deployment.started",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/helm-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "deployment": {
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Deployment Finished


#### Type

```json
"type": "sh.keptn.event.deployment.finished"
```

#### Data

```json
"data": {
  "deployment": {
    "deploymentNames": [ "carts" ],
    "deploymentURIsLocal": [ "carts.svc.cluster.local" ],
    "deploymentURIsPublic": [ "carts.dev.xyz" ],
    "deploymentstrategy": "",                                               #!! does we need this property?
    "gitCommit": "aaa6c32e817b9435a4b3f6078b4826fd1aefbccc"
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.deployment.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.deployment.finished",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/helm-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "deployment": {
      "deploymentNames": [ "carts" ],
      "deploymentURIsLocal": [ "carts.svc.cluster.local" ],
      "deploymentURIsPublic": [ "carts.dev.xyz" ],
      "deploymentstrategy": "",                                               #!! does we need this property?
      "gitCommit": "aaa6c32e817b9435a4b3f6078b4826fd1aefbccc"
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>


## Test 
([&uarr; up to index](#keptn-cloud-events))

### Test Triggered

#### Type

```json
"type": "sh.keptn.event.test.triggered"
```

#### Data

```json
"data": {
  "deployment":{
    "deploymentURIsLocal": [ "carts.svc.cluster.local" ],
    "deploymentURIsPublic": [ "carts.dev.xyz" ]
  },
  "test": {
    "teststrategy": "functional"
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.test.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.test.triggered",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/shipyard-controller",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "deployment":{
      "deploymentURIsLocal": [ "carts.svc.cluster.local" ],
      "deploymentURIsPublic": [ "carts.dev.xyz" ]
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded",
    "test": {
      "teststrategy": "functional"
    }
  }
}
```

</p>
</details>

### Test Started


#### Type

```json
"type": "sh.keptn.event.test.started"
```

#### Data

*No type specific data block specified.* 

#### Example
<details><summary>Example of sh.keptn.event.test.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.test.started",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/helm-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded",
    "test": {
    }
  }
}
```

</p>
</details>

### Test Finished

#### Type

```json
"type": "sh.keptn.event.test.finished"
```

#### Data

```json
"data": {
  "test": {
    "end": "2019-06-07T07:02:15.64489Z",
    "gitCommit": "aaa6c32e817b9435a4b3f6078b4826fd1aefbccc",
    "start": "2019-06-07T07:02:15.64489Z"
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.test.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.test.finished",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/helm-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded",
    "test": {
      "end": "2019-06-07T07:02:15.64489Z",
      "gitCommit": "aaa6c32e817b9435a4b3f6078b4826fd1aefbccc",
      "start": "2019-06-07T07:02:15.64489Z"
    }
  }
}
```

</p>
</details>

## Evaluation 
([&uarr; up to index](#keptn-cloud-events))

### Evaluation Triggered

#### Type

```json
"type": "sh.keptn.event.evaluation.triggered"
```

#### Data

```json
"data": {
  "deployment": {
    "deploymentNames": [ "carts" ]
  },
  "evaluation": {
    "end": "2019-06-07T07:02:15.64489Z",
    "start": "2019-06-07T07:02:15.64489Z"
  },
  "test": {
    "end": "2019-06-07T07:02:15.64489Z",
    "start": "2019-06-07T07:02:15.64489Z"
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.evaluation.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.evaluation.triggered",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/shipyard-controller",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "deployment": {
      "deploymentNames": [ "carts" ]
    },
    "evaluation": {
      "end": "2019-06-07T07:02:15.64489Z",
      "start": "2019-06-07T07:02:15.64489Z"
    },   
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded",
    "test": {
      "end": "2019-06-07T07:02:15.64489Z",
      "start": "2019-06-07T07:02:15.64489Z"
    }
  }
}
```

</p>
</details>

### Evaluation Started


#### Type

```json
"type": "sh.keptn.event.evaluation.started"
```

#### Data

*No type specific data block specified.* 

#### Example
<details><summary>Example of sh.keptn.event.evaluation.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.evaluation.started",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/lighthouse-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "evaluation": {
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Evaluation Finished

#### Type

```json
"type": "sh.keptn.event.evaluation.finished"
```

#### Data

```json
"data": {
  "evaluation": {
    "comparedEvents": [],
    "gitCommit": "",
    "indicatorResults": [
      {
        "score": 0,
        "status": "",
        "targets": [
          {
            "criteria": "",
            "targetValue": 0,
            "violated": true
          }
        ],
        "value": {
          "message": "",
          "metric": "",
          "success": true,
          "value": 0
        }
      }
    ],
    "result": "pass",
    "score": 0,
    "sloFileContent": "base64 encoded",
    "timeEnd": "",
    "timeStart": ""
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.evaluation.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.evaluation.finished",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/lighthouse-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "evaluation": {
      "comparedEvents": [],
      "gitCommit": "",
      "indicatorResults": [
        {
          "score": 0,
          "status": "",
          "targets": [
            {
              "criteria": "",
              "targetValue": 0,
              "violated": true
            }
          ],
          "value": {
            "message": "",
            "metric": "",
            "success": true,
            "value": 0
          }
        }
      ],
      "result": "pass",
      "score": 0,
      "sloFileContent": "",
      "timeEnd": "",
      "timeStart": ""
    },    
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

## Remediation 
([&uarr; up to index](#keptn-cloud-events))

### Remediation Triggered


#### Type

```json
"type": "sh.keptn.event.remediation.triggered"
```

#### Data

```json
"data": {
  "remediation": {
    "problem": {
      "ImpactedEntity": "carts-primary",
      "PID": "93a5-3fas-a09d-8ckf",
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
      "ProblemID": "ab81-941c-f198",
      "ProblemTitle": "Response time degradation",
      "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
      "State": "OPEN",
      "Tags": ""
    }
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.remediation.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.triggered",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/shipyard-controller",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "problem": {
      "ImpactedEntity": "carts-primary",
      "PID": "93a5-3fas-a09d-8ckf",
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
      "ProblemID": "ab81-941c-f198",
      "ProblemTitle": "Response time degradation",
      "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
      "State": "OPEN",
      "Tags": ""
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Remediation Started


#### Type

```json
"type": "sh.keptn.event.remediation.started"
```

#### Data

*No type specific data block specified.* 

#### Example
<details><summary>Example of sh.keptn.event.remediation.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.started",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "remediation": {
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Remediation Status Changed


#### Type

```json
"type": "sh.keptn.event.remediation.status.changed"
```

#### Data

```json
"data": {
  "remediation": {
    "actionIndex": 1, 
    "actionName": "trigger-runbook"
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.remediation.status.changed</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.status.changed",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "remediation": {
      "actionIndex": 1, 
      "actionName": "trigger-runbook"
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Remediation Finished

#### Type

```json
"type": "sh.keptn.event.remediation.finished"
```

#### Data

*No type specific data block specified.* 

#### Example
<details><summary>Example of sh.keptn.event.remediation.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.remediation.finished",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/remediation-service",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "remediation": {
    },    
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

## Action 
([&uarr; up to index](#keptn-cloud-events))

### Action Triggered


#### Type

```json
"type": "sh.keptn.event.action.triggered"
```

#### Data

```json
"data": {
  "action": {
    "name": "",
    "action": "",
    "description": "",
    "value": { }
  },
  "problem": {
    "ImpactedEntity": "carts-primary",
    "PID": "93a5-3fas-a09d-8ckf",
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
    "ProblemID": "ab81-941c-f198",
    "ProblemTitle": "Response time degradation",
    "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
    "State": "OPEN",
    "Tags": ""
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.action.triggered</summary>
<p>

```json
{
  "type": "sh.keptn.event.action.triggered",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/shipyard-controller",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "data": {
    "action": {
      "name": "",
      "action": "",
      "description": "",
      "value": { }
    },
    "problem": {
      "ImpactedEntity": "carts-primary",
      "PID": "93a5-3fas-a09d-8ckf",
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
      "ProblemID": "ab81-941c-f198",
      "ProblemTitle": "Response time degradation",
      "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
      "State": "OPEN",
      "Tags": ""
    },  
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>

### Action Started

#### Type

```json
"type": "sh.keptn.event.action.started"
```

#### Data

*No type specific data block specified.* 

#### Example
<details><summary>Example of sh.keptn.event.action.started</summary>
<p>

```json
{
  "type": "sh.keptn.event.action.started",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/action-provider",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "action": {
    },
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>


### Action Finished

#### Type

```json
"type": "sh.keptn.event.action.finished"
```

#### Data

```json
"data": {
  "action": {
    "gitCommit": ""
  }
}
```

#### Example
<details><summary>Example of sh.keptn.event.action.finished</summary>
<p>

```json
{
  "type": "sh.keptn.event.action.finished",
  "specversion": "1.0",
  "source": "https://github.com/keptn/keptn/action-provider",
  "id": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "time": "2019-06-07T07:02:15.64489Z",
  "shkeptncontext": "08735340-6f9e-4b32-97ff-3b6c292bc509",
  "triggeredid": "f2b878d3-03c0-4e8f-bc3f-454bc1b3d79d",
  "data": {
    "action": {
      "gitCommit": ""
    },    
    "labels": null,
    "message": "",
    "project": "sockshop",
    "result": "pass",
    "service": "carts",
    "stage": "dev",
    "status": "succeeded"
  }
}
```

</p>
</details>