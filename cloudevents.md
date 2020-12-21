# Keptn Cloud Events

* [Project](#project)
* [Service](#service)
* [Approval](#approval)
* [Deployment](#deployment)
* [Test](#test)
* [Evaluation](#evaluation)
* [Release](#release)
* [Remediation](#remediation)
* [Action](#action)
* [Get-SLI](#get-sli)
* [Problem](#problem)
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

## Project
### Project Create Triggered
#### Type
sh.keptn.event.project.create.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.project.create.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ProjectCreateData",
  "definitions": {
    "ProjectCreateData": {
      "required": [
        "projectName",
        "gitRemoteURL",
        "shipyard"
      ],
      "properties": {
        "projectName": {
          "type": "string"
        },
        "gitRemoteURL": {
          "type": "string"
        },
        "shipyard": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "projectName": "sockshop",
    "gitRemoteURL": "https://github.com/project/repository",
    "shipyard": "c3RhZ2VzOg0KICAtIG5hbWU6ICJkZXYiDQogICAgZGVwbG95bWVudF9zdHJhdGVneTogImRpcmVjdCINCiAgICB0ZXN0X3N0cmF0ZWd5OiAiZnVuY3Rpb25hbCINCiAgLSBuYW1lOiAic3RhZ2luZyINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHRlc3Rfc3RyYXRlZ3k6ICJwZXJmb3JtYW5jZSINCiAgLSBuYW1lOiAicHJvZHVjdGlvbiINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHJlbWVkaWF0aW9uX3N0cmF0ZWd5OiAiYXV0b21hdGVkIg0K"
  },
  "datacontenttype": "application/json",
  "id": "ffe7adf0-36ca-4201-89cc-bb9ca3f09911",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.project.create.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Project Create Started
#### Type
sh.keptn.event.project.create.started
#### Data

<details><summary>Json Schema of sh.keptn.event.project.create.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ProjectCreateStartedEventData",
  "definitions": {
    "ProjectCreateStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "8c5b659a-c90e-4644-80cd-a332ab421e60",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.project.create.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Project Create Finished
#### Type
sh.keptn.event.project.create.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.project.create.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ProjectCreateFinishedEventData",
  "definitions": {
    "ProjectCreateData": {
      "required": [
        "projectName",
        "gitRemoteURL",
        "shipyard"
      ],
      "properties": {
        "projectName": {
          "type": "string"
        },
        "gitRemoteURL": {
          "type": "string"
        },
        "shipyard": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ProjectCreateFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "createdProject"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "createdProject": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ProjectCreateData"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "createdProject": {
      "projectName": "sockshop",
      "gitRemoteURL": "https://github.com/project/repository",
      "shipyard": "c3RhZ2VzOg0KICAtIG5hbWU6ICJkZXYiDQogICAgZGVwbG95bWVudF9zdHJhdGVneTogImRpcmVjdCINCiAgICB0ZXN0X3N0cmF0ZWd5OiAiZnVuY3Rpb25hbCINCiAgLSBuYW1lOiAic3RhZ2luZyINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHRlc3Rfc3RyYXRlZ3k6ICJwZXJmb3JtYW5jZSINCiAgLSBuYW1lOiAicHJvZHVjdGlvbiINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHJlbWVkaWF0aW9uX3N0cmF0ZWd5OiAiYXV0b21hdGVkIg0K"
    }
  },
  "datacontenttype": "application/json",
  "id": "7aea8df6-4d1e-4d52-81f6-d20a0c6f6858",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.project.create.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Service
### Service Create Started
#### Type
sh.keptn.event.service.create.started
#### Data

<details><summary>Json Schema of sh.keptn.event.service.create.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ServiceCreateStartedEventData",
  "definitions": {
    "ServiceCreateStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "e78c00fc-e8ac-4f68-a684-cb2e3cfc12cf",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.service.create.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Service Create Status Changed
#### Type
sh.keptn.event.service.create.status.changed
#### Data

<details><summary>Json Schema of sh.keptn.event.service.create.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ServiceCreateStatusChangedEventData",
  "definitions": {
    "ServiceCreateStatusChangedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "531a6cb0-78ae-418b-8a8b-22bea147f676",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.service.create.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Service Create Finished
#### Type
sh.keptn.event.service.create.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.service.create.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ServiceCreateFinishedEventData",
  "definitions": {
    "Helm": {
      "required": [
        "chart"
      ],
      "properties": {
        "chart": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ServiceCreateFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "helm"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "helm": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/Helm"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "helm": {
      "chart": "c3RhZ2VzOg0KICAtIG5hbWU6ICJkZXYiDQogICAgZGVwbG95bWVudF9zdHJhdGVneTogImRpcmVjdCINCiAgICB0ZXN0X3N0cmF0ZWd5OiAiZnVuY3Rpb25hbCINCiAgLSBuYW1lOiAic3RhZ2luZyINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHRlc3Rfc3RyYXRlZ3k6ICJwZXJmb3JtYW5jZSINCiAgLSBuYW1lOiAicHJvZHVjdGlvbiINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHJlbWVkaWF0aW9uX3N0cmF0ZWd5OiAiYXV0b21hdGVkIg0K\""
    }
  },
  "datacontenttype": "application/json",
  "id": "4eba820a-3b97-44e4-aeff-97120140b65b",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.service.create.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Approval
### Approval Triggered
#### Type
sh.keptn.event.approval.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.approval.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalTriggeredEventData",
  "definitions": {
    "": {
      "required": [
        "pass",
        "warning"
      ],
      "properties": {
        "pass": {
          "type": "string"
        },
        "warning": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ApprovalTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "approval"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "approval": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "approval": {
      "pass": "automatic",
      "warning": "manual"
    }
  },
  "datacontenttype": "application/json",
  "id": "41552f2e-7851-4fad-8f98-07b8be6b63ac",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.approval.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Approval Started
#### Type
sh.keptn.event.approval.started
#### Data

<details><summary>Json Schema of sh.keptn.event.approval.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalStartedEventData",
  "definitions": {
    "ApprovalStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "3e3699a5-f134-40e1-b7af-2940fde60d78",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.approval.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Approval Status Changed
#### Type
sh.keptn.event.approval.status.changed
#### Data

<details><summary>Json Schema of sh.keptn.event.approval.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalStatusChangedEventData",
  "definitions": {
    "ApprovalStatusChangedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "31d8334d-7d64-45b0-a939-84828329fe9a",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.approval.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Approval Finished
#### Type
sh.keptn.event.approval.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.approval.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalFinishedEventData",
  "definitions": {
    "ApprovalFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "448c1800-7439-4fd9-90bb-e5cfacfefa24",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.approval.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Deployment
### Deployment Triggered
#### Type
sh.keptn.event.deployment.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.deployment.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/DeploymentTriggeredEventData",
  "definitions": {
    "ConfigurationChange": {
      "required": [
        "values"
      ],
      "properties": {
        "values": {
          "patternProperties": {
            ".*": {
              "additionalProperties": true
            }
          },
          "type": "object"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "DeploymentTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "configurationChange",
        "deployment"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "configurationChange": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ConfigurationChange"
        },
        "deployment": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/DeploymentWithStrategy"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "DeploymentWithStrategy": {
      "properties": {
        "deploymentstrategy": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "configurationChange": {
      "values": {
        "key": "value"
      }
    },
    "deployment": {
      "deploymentstrategy": "direct"
    }
  },
  "datacontenttype": "application/json",
  "id": "28e72586-fa9b-4b8f-a7c6-480e1606f63b",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.deployment.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Deployment Started
#### Type
sh.keptn.event.deployment.started
#### Data

<details><summary>Json Schema of sh.keptn.event.deployment.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/DeploymentStartedEventData",
  "definitions": {
    "DeploymentStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "ac072ee3-cbc2-4a0b-b97a-6fca4eaf4572",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.deployment.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Deployment Status Changed
#### Type
sh.keptn.event.deployment.status.changed
#### Data

<details><summary>Json Schema of sh.keptn.event.deployment.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/DeploymentStatusChangedEventData",
  "definitions": {
    "DeploymentStatusChangedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "eff44e95-1746-49b3-9e42-1f8ea32f2842",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.deployment.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Deployment Finished
#### Type
sh.keptn.event.deployment.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.deployment.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/DeploymentFinishedEventData",
  "definitions": {
    "DeploymentData": {
      "required": [
        "deploymentNames",
        "gitCommit"
      ],
      "properties": {
        "deploymentstrategy": {
          "type": "string"
        },
        "deploymentURIsLocal": {
          "items": {
            "type": "string"
          },
          "type": "array"
        },
        "deploymentURIsPublic": {
          "items": {
            "type": "string"
          },
          "type": "array"
        },
        "deploymentNames": {
          "items": {
            "type": "string"
          },
          "type": "array"
        },
        "gitCommit": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "DeploymentFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "deployment"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "deployment": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/DeploymentData"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "deployment": {
      "deploymentstrategy": "direct",
      "deploymentURIsLocal": [
        "http://carts.sockshop-staging.svc.cluster.local"
      ],
      "deploymentURIsPublic": [
        "http://carts.sockshot.local:80"
      ],
      "deploymentNames": [
        "deployment-1"
      ],
      "gitCommit": "ca82a6dff817gc66f44342007202690a93763949"
    }
  },
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.deployment.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Test
### Test Triggered
#### Type
sh.keptn.event.test.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.test.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestTriggeredEventData",
  "definitions": {
    "": {
      "required": [
        "teststrategy"
      ],
      "properties": {
        "teststrategy": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "TestTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "test",
        "deployment"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "test": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/"
        },
        "deployment": {
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "test": {
      "teststrategy": "functional"
    },
    "deployment": {
      "deploymentURIsLocal": [
        "http://carts.sockshop-staging.svc.cluster.local"
      ],
      "deploymentURIsPublic": [
        "http://carts.sockshot.local:80"
      ]
    }
  },
  "datacontenttype": "application/json",
  "id": "321f428b-ac72-4beb-8841-60dd2af53fae",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.test.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Test Started
#### Type
sh.keptn.event.test.started
#### Data

<details><summary>Json Schema of sh.keptn.event.test.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestStartedEventData",
  "definitions": {
    "TestStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "39780468-9055-4327-bbaa-580e5a57cbef",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.test.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Test Status Changed
#### Type
sh.keptn.event.test.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.test.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestStatusChangedEventData",
  "definitions": {
    "TestStatusChangedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "fa211ed9-30a7-412f-8dc6-3aa789d53239",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.test.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Test Finished
#### Type
sh.keptn.event.test.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.test.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestFinishedEventData",
  "definitions": {
    "": {
      "required": [
        "start",
        "end",
        "gitCommit"
      ],
      "properties": {
        "start": {
          "type": "string"
        },
        "end": {
          "type": "string"
        },
        "gitCommit": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "TestFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "test"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "test": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "test": {
      "start": "2019-10-20T07:57:27.152330783Z",
      "end": "2019-10-20T08:57:27.152330783Z",
      "gitCommit": "ca82a6dff817gc66f44342007202690a93763949"
    }
  },
  "datacontenttype": "application/json",
  "id": "441c3674-f38c-4993-b58e-d250abd5c407",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.test.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Evaluation
### Evaluation Triggered
#### Type
sh.keptn.event.evaluation.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.evaluation.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EvaluationTriggeredEventData",
  "definitions": {
    "": {
      "required": [
        "start",
        "end"
      ],
      "properties": {
        "start": {
          "type": "string"
        },
        "end": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "EvaluationTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "test",
        "evaluation",
        "deployment"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "test": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/"
        },
        "evaluation": {
          "$ref": "#/definitions/"
        },
        "deployment": {
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "",
    "stage": "",
    "service": "",
    "labels": null,
    "status": "",
    "result": "",
    "message": "",
    "test": {
      "start": "2019-10-20T06:57:27.152330783Z",
      "end": "2019-10-20T07:57:27.152330783Z"
    },
    "evaluation": {
      "start": "2019-10-20T07:57:27.152330783Z",
      "end": "2019-10-20T08:57:27.152330783Z"
    },
    "deployment": {
      "deploymentNames": [
        "deployment-1"
      ]
    }
  },
  "datacontenttype": "application/json",
  "id": "e2203413-79fd-4ac2-b4c8-12049a72a8d6",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.evaluation.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Evaluation Started
#### Type
sh.keptn.event.evaluation.started
#### Data

<details><summary>Json Schema of sh.keptn.event.evaluation.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EvaluationStartedEventData",
  "definitions": {
    "EvaluationStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "30941fe4-bf08-4b03-9b52-07de2c00a239",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.evaluation.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Evaluation Status Changed
#### Type
sh.keptn.event.evaluation.status.changed
#### Data

<details><summary>Json Schema of sh.keptn.event.evaluation.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EvaluationStatusChangedEventData",
  "definitions": {
    "EvaluationStatusChangedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "000742aa-2689-47ed-a3ed-96f70c2238f8",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.evaluation.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Evaluation Finished
#### Type
sh.keptn.event.evaluation.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.evaluation.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EvaluationFinishedEventData",
  "definitions": {
    "EvaluationDetails": {
      "required": [
        "timeStart",
        "timeEnd",
        "result",
        "score",
        "sloFileContent",
        "indicatorResults",
        "comparedEvents",
        "gitCommit"
      ],
      "properties": {
        "timeStart": {
          "type": "string"
        },
        "timeEnd": {
          "type": "string"
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
        "indicatorResults": {
          "items": {
            "$schema": "http://json-schema.org/draft-04/schema#",
            "$ref": "#/definitions/SLIEvaluationResult"
          },
          "type": "array"
        },
        "comparedEvents": {
          "items": {
            "type": "string"
          },
          "type": "array"
        },
        "gitCommit": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "EvaluationFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "evaluation"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "evaluation": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/EvaluationDetails"
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
        "value": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/SLIResult"
        },
        "targets": {
          "items": {
            "$schema": "http://json-schema.org/draft-04/schema#",
            "$ref": "#/definitions/SLITarget"
          },
          "type": "array"
        },
        "status": {
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
        "metric": {
          "type": "string"
        },
        "value": {
          "type": "number"
        },
        "success": {
          "type": "boolean"
        },
        "message": {
          "type": "string"
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
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "evaluation": {
      "timeStart": "2019-11-18T11:21:06Z",
      "timeEnd": "2019-11-18T11:29:36Z",
      "result": "fail",
      "score": 0,
      "sloFileContent": "LS0tDQpzcGVjX3ZlcnNpb246ICcxLjAnDQpjb21wYXJpc29uOg0KICBjb21wYXJlX3dpdGg6ICJzaW5nbGVfcmVzdWx0Ig0KICBpbmNsdWRlX3Jlc3VsdF93aXRoX3Njb3JlOiAicGFzcyINCiAgYWdncmVnYXRlX2Z1bmN0aW9uOiBhdmcNCm9iamVjdGl2ZXM6DQogIC0gc2xpOiByZXNwb25zZV90aW1lX3A5NQ0KICAgIHBhc3M6ICAgICAgICAjIHBhc3MgaWYgKHJlbGF0aXZlIGNoYW5nZSA8PSAxMCUgQU5EIGFic29sdXRlIHZhbHVlIGlzIDwgNTAwKQ0KICAgICAgLSBjcml0ZXJpYToNCiAgICAgICAgICAtICI8PSsxMCUiICMgcmVsYXRpdmUgdmFsdWVzIHJlcXVpcmUgYSBwcmVmaXhlZCBzaWduIChwbHVzIG9yIG1pbnVzKQ0KICAgICAgICAgIC0gIjw2MDAiICAgIyBhYnNvbHV0ZSB2YWx1ZXMgb25seSByZXF1aXJlIGEgbG9naWNhbCBvcGVyYXRvcg0KICAgIHdhcm5pbmc6ICAgICAjIGlmIHRoZSByZXNwb25zZSB0aW1lIGlzIGJlbG93IDgwMG1zLCB0aGUgcmVzdWx0IHNob3VsZCBiZSBhIHdhcm5pbmcNCiAgICAgIC0gY3JpdGVyaWE6DQogICAgICAgICAgLSAiPD04MDAiDQp0b3RhbF9zY29yZToNCiAgcGFzczogIjkwJSINCiAgd2FybmluZzogNzUl",
      "indicatorResults": [
        {
          "score": 0,
          "value": {
            "metric": "response_time_p95",
            "value": 1002.6278552658177,
            "success": true,
            "message": "a message"
          },
          "targets": [
            {
              "criteria": "\u003c=+10%",
              "targetValue": 600,
              "violated": true
            }
          ],
          "status": "failed"
        }
      ],
      "comparedEvents": [
        "event-id-1",
        "event-id-2"
      ],
      "gitCommit": ""
    }
  },
  "datacontenttype": "application/json",
  "id": "2e5efe40-6612-4ff7-bf11-2f2c29db88af",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.evaluation.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Release
### Release Triggered
#### Type
sh.keptn.event.release.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.release.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ReleaseTriggeredEventData",
  "definitions": {
    "DeploymentWithStrategy": {
      "properties": {
        "deploymentstrategy": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ReleaseTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "deployment"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "deployment": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/DeploymentWithStrategy"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "deployment": {
      "deploymentstrategy": "duplicate"
    }
  },
  "datacontenttype": "application/json",
  "id": "f5dd3404-8ff5-4eb1-841b-01df053ef52d",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.release.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Release Started
#### Type
sh.keptn.event.release.started
#### Data

<details><summary>Json Schema of sh.keptn.event.release.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ReleaseStartedEventData",
  "definitions": {
    "ReleaseStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "7d4cb894-eafb-479a-8b64-74a9df188ab2",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.release.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Release Status Changed
#### Type
sh.keptn.event.release.status.changed
#### Data

<details><summary>Json Schema of sh.keptn.event.release.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ReleaseStatusChangedEventData",
  "definitions": {
    "ReleaseStatusChangedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "ef887ba9-34f2-4dc4-954b-9f4ed6e4fbe5",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.release.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Release Finished
#### Type
sh.keptn.event.release.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.release.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ReleaseFinishedEventData",
  "definitions": {
    "ReleaseData": {
      "required": [
        "gitCommit"
      ],
      "properties": {
        "gitCommit": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ReleaseFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "release"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "release": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ReleaseData"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "release": {
      "gitCommit": "ca82a6dff817gc66f44342007202690a93763949"
    }
  },
  "datacontenttype": "application/json",
  "id": "764fdc21-ba7e-4937-8480-da75eb0bfb08",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.release.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Remediation
### Remediation Triggered
#### Type
sh.keptn.event.remediation.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.remediation.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/RemediationTriggeredEventData",
  "definitions": {
    "ProblemDetails": {
      "required": [
        "ProblemID",
        "ProblemTitle",
        "ProblemDetails",
        "PID"
      ],
      "properties": {
        "State": {
          "type": "string"
        },
        "ProblemID": {
          "type": "string"
        },
        "ProblemTitle": {
          "type": "string"
        },
        "ProblemDetails": {
          "type": "string",
          "media": {
            "binaryEncoding": "base64"
          }
        },
        "PID": {
          "type": "string"
        },
        "ProblemURL": {
          "type": "string"
        },
        "ImpactedEntity": {
          "type": "string"
        },
        "Tags": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "RemediationTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "problem"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "problem": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ProblemDetails"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "datacontenttype": "application/json",
  "id": "0abd7e21-7106-4dd6-99f1-61f08da17a9b",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.remediation.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Remediation Started
#### Type
sh.keptn.event.remediation.started
#### Data

<details><summary>Json Schema of sh.keptn.event.remediation.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/RemediationStartedEventData",
  "definitions": {
    "RemediationStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "c8a1c19e-9bef-4ee0-9af1-867e03d480ff",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.remediation.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Remediation Status Changed
#### Type
sh.keptn.event.remediation.status.changed
#### Data

<details><summary>Json Schema of sh.keptn.event.remediation.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/RemediationStatusChangedEventData",
  "definitions": {
    "Remediation": {
      "required": [
        "actionIndex",
        "actionName"
      ],
      "properties": {
        "actionIndex": {
          "type": "integer"
        },
        "actionName": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "RemediationStatusChangedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "remediation"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "remediation": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/Remediation"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message",
    "remediation": {
      "actionIndex": 1,
      "actionName": "trigger-runbook"
    }
  },
  "datacontenttype": "application/json",
  "id": "0edd5d02-c4c7-4423-bef6-87343d8c627d",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.remediation.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Remediation Finished
#### Type
sh.keptn.event.remediation.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.remediation.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/RemediationFinishedEventData",
  "definitions": {
    "RemediationFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "c2dd585c-ab35-4510-81bc-e96640218ae7",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.remediation.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Action
### Action Triggered
#### Type
sh.keptn.event.action.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.action.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ActionTriggeredEventData",
  "definitions": {
    "ActionInfo": {
      "required": [
        "name",
        "action",
        "description"
      ],
      "properties": {
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
          "additionalProperties": true
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ActionTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "action",
        "problem"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "action": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ActionInfo"
        },
        "problem": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ProblemDetails"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ProblemDetails": {
      "required": [
        "ProblemID",
        "ProblemTitle",
        "ProblemDetails",
        "PID"
      ],
      "properties": {
        "State": {
          "type": "string"
        },
        "ProblemID": {
          "type": "string"
        },
        "ProblemTitle": {
          "type": "string"
        },
        "ProblemDetails": {
          "type": "string",
          "media": {
            "binaryEncoding": "base64"
          }
        },
        "PID": {
          "type": "string"
        },
        "ProblemURL": {
          "type": "string"
        },
        "ImpactedEntity": {
          "type": "string"
        },
        "Tags": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": null,
    "status": "",
    "result": "",
    "message": "",
    "action": {
      "name": "Feature toggeling",
      "action": "toggle-feature",
      "description": "Toggles a feature flag",
      "value": {
        "EnableItemCache": "on"
      }
    },
    "problem": {
      "State": "OPEN",
      "ProblemID": "762",
      "ProblemTitle": "cpu_usage_sockshop_carts",
      "ProblemDetails": null,
      "PID": "93a5-3fas-a09d-8ckf",
      "ProblemURL": "http://problem.url.com",
      "ImpactedEntity": "carts-primary"
    }
  },
  "datacontenttype": "application/json",
  "id": "fe50c4f9-5d7e-47f2-bb9a-520bcae39cdb",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.action.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Action Started
#### Type
sh.keptn.event.action.started
#### Data

<details><summary>Json Schema of sh.keptn.event.action.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ActionStartedEventData",
  "definitions": {
    "ActionStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "33954420-cc31-48d8-a0b5-8d286f96c66d",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.action.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Action Finished
#### Type
sh.keptn.event.action.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.action.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ActionFinishedEventData",
  "definitions": {
    "ActionData": {
      "required": [
        "gitCommit"
      ],
      "properties": {
        "gitCommit": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ActionFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "action"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "action": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ActionData"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": null,
    "status": "",
    "result": "",
    "message": "",
    "action": {
      "gitCommit": "93a5-3fas-a09d-8ckf"
    }
  },
  "datacontenttype": "application/json",
  "id": "bceff094-8966-46d3-8826-8a685f7da810",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.action.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Get SLI
### Get SLI Triggered
#### Type
sh.keptn.event.get-sli.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.get-sli.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/GetSLITriggeredEventData",
  "definitions": {
    "": {
      "required": [
        "sliProvider",
        "start",
        "end",
        "indicators",
        "customFilters"
      ],
      "properties": {
        "sliProvider": {
          "type": "string"
        },
        "start": {
          "type": "string"
        },
        "end": {
          "type": "string"
        },
        "indicators": {
          "$ref": "#/definitions/"
        },
        "customFilters": {
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "GetSLITriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "get-sli"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "get-sli": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": null,
    "status": "",
    "result": "",
    "message": "",
    "get-sli": {
      "sliProvider": "dynatrace",
      "start": "2019-10-28T15:44:27.152330783Z",
      "end": "2019-10-28T15:54:27.152330783Z",
      "indicators": [
        "throughput",
        "error_rate",
        "request_latency_p95"
      ],
      "customFilters": [
        {
          "key": "dynatraceEntityName",
          "value": "HealthCheckController"
        },
        {
          "key": "tags",
          "value": "test-subject:true"
        }
      ]
    }
  },
  "datacontenttype": "application/json",
  "id": "0da05b3c-a1fa-470c-987c-37c524d4bb4d",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.get-sli.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Get SLI Started
#### Type
sh.keptn.event.get-sli.started
#### Data

<details><summary>Json Schema of sh.keptn.event.get-sli.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/GetSLIStartedEventData",
  "definitions": {
    "GetSLIStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "f63a9d94-1cd6-487f-bfb3-13c8efa04a14",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.get-sli.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Get SLI Finished
#### Type
sh.keptn.event.get-sli.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.get-sli.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/GetSLIFinishedEventData",
  "definitions": {
    "": {
      "required": [
        "start",
        "end",
        "indicatorValues"
      ],
      "properties": {
        "start": {
          "type": "string"
        },
        "end": {
          "type": "string"
        },
        "indicatorValues": {
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "GetSLIFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "get-sli"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "get-sli": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": null,
    "status": "",
    "result": "",
    "message": "",
    "get-sli": {
      "start": "2019-10-20T07:57:27.152330783Z",
      "end": "2019-10-22T08:57:27.152330783Z",
      "indicatorValues": [
        {
          "metric": "response_time_p50",
          "value": 1011.0745528937252,
          "success": true
        }
      ]
    }
  },
  "datacontenttype": "application/json",
  "id": "f8bd3560-b84e-4205-9d5e-e381cb2f9b79",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.get-sli.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Monitoring
### Configure Monitoring Triggered
#### Type
sh.keptn.event.configure-monitoring.triggered
#### Data

<details><summary>Json Schema of sh.keptn.event.configure-monitoring.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ConfigureMonitoringTriggeredEventData",
  "definitions": {
    "ConfigureMonitoringTriggeredEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message",
        "configureMonitoring"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "configureMonitoring": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/ConfigureMonitoringTriggeredParams"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ConfigureMonitoringTriggeredParams": {
      "required": [
        "type"
      ],
      "properties": {
        "type": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": null,
    "status": "",
    "result": "",
    "message": "",
    "configureMonitoring": {
      "type": "dynatrace"
    }
  },
  "datacontenttype": "application/json",
  "id": "80c38596-d607-4586-b69e-c26584288614",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.configure-monitoring.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Configure Monitoring Started
#### Type
sh.keptn.event.configure-monitoring.started
#### Data

<details><summary>Json Schema of sh.keptn.event.configure-monitoring.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ConfigureMonitoringStartedEventData",
  "definitions": {
    "ConfigureMonitoringStartedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "effe5be5-11c8-4369-b1a1-2088aac411a8",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.configure-monitoring.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Configure Monitoring Finished
#### Type
sh.keptn.event.configure-monitoring.finished
#### Data

<details><summary>Json Schema of sh.keptn.event.configure-monitoring.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ConfigureMonitoringFinishedEventData",
  "definitions": {
    "ConfigureMonitoringFinishedEventData": {
      "required": [
        "project",
        "stage",
        "service",
        "labels",
        "status",
        "result",
        "message"
      ],
      "properties": {
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
        "status": {
          "type": "string"
        },
        "result": {
          "type": "string"
        },
        "message": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    }
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "labels": {
      "label-key": "label-value"
    },
    "status": "succeeded",
    "result": "pass",
    "message": "a message"
  },
  "datacontenttype": "application/json",
  "id": "9ff5c763-b352-462e-b8b5-fbc9583a8053",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.configure-monitoring.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
## Problem
### Problem Open
#### Type
sh.keptn.event.problem.open
#### Data

<details><summary>Json Schema of sh.keptn.event.problem.open</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ProblemEventData",
  "definitions": {
    "ProblemEventData": {
      "required": [
        "ProblemID",
        "ProblemTitle",
        "ProblemDetails",
        "PID",
        "labels"
      ],
      "properties": {
        "State": {
          "type": "string"
        },
        "ProblemID": {
          "type": "string"
        },
        "ProblemTitle": {
          "type": "string"
        },
        "ProblemDetails": {
          "type": "string",
          "media": {
            "binaryEncoding": "base64"
          }
        },
        "PID": {
          "type": "string"
        },
        "ProblemURL": {
          "type": "string"
        },
        "ImpactedEntity": {
          "type": "string"
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
  }
}
```
</p>
</details>

#### Example

```json
{
  "data": {
    "State": "OPEN",
    "ProblemID": "ab81-941c-f198",
    "ProblemTitle": "Response Time Degradation",
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
    "PID": "93a5-3fas-a09d-8ckf",
    "ProblemURL": "https://.../#problems/problemdetails;pid=93a5-3fas-a09d-8ckf",
    "ImpactedEntity": "carts-primary",
    "project": "sockshop",
    "stage": "production",
    "service": "service",
    "labels": null
  },
  "datacontenttype": "application/json",
  "id": "6cdf8ec1-b31b-4401-b5d0-9a6471d740e2",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.problem.open"
}
```

([&uarr; up to index](#keptn-cloud-events))
