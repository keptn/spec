# Keptn CloudEvents
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
* [Monitoring](#monitoring)
* [Problem](#problem)
---
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
    "shkeptnspecversion": {
      "type": "string",
      "minLength": 1,
      "description": "The version of the Keptn specification",
      "value": "0.2.0"
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
## Type
In Keptn, events follow two different formats of event types. One is related to the overall status of a **task sequence execution**, while the other format is related to the execution of a **certain task within a sequence**.
### Task sequence events
The event type of a Keptn CloudEvent concerning the overall state of a task sequence has the following format:

* `sh.keptn.event.[stage].[task sequence].[event status]` - For events concerning the execution of a task sequence

As indicated by the brackets, the event type is defined by a **stage**, **task sequence** and the **event status**.
* The task sequence is declared in the [Shipyard](https://github.com/keptn/spec/blob/master/shipyard.md) of a project.
* The kinds of event states are defined with: `triggered` and `finished`

For example, if a task sequence with the name `delivery` in the stage `hardening` should be executed, it has to be triggered by sending an event with the type `sh.keptn.event.hardening.delivery.triggered`. Once the `delivery` sequence is completed, a `sh.keptn.event.hardening.delivery.finished` event will be sent to indicate the completion of the task sequence.


### Task events
The event type of a Keptn CloudEvent concerning the execution of a certain task within a task sequence has the following format:

* `sh.keptn.event.[task].[event status]` - For events concerning the execution of a certain task within a task sequence

As indicated by the brackets, the event type is defined by a **task** and the **event status**.
* The task is declared in the [Shipyard](https://github.com/keptn/spec/blob/master/shipyard.md) of a project. For example, a Shipyard can contain tasks like: `deployment`, `test`, or `evaluation`. Consequently, the event type for a `deployment` task would be `sh.keptn.event.deployment.[event status]`
* The kinds of event states are defined with: `triggered`, `started`, `status.changed`, and `finished` (`status.changed` is optional)

By combining the *task* and *event status* for the `deployment` task, the event types are:

* `sh.keptn.event.deployment.triggered`
* `sh.keptn.event.deployment.started`
* `sh.keptn.event.deployment.status.changed`
* `sh.keptn.event.deployment.finished`
## Data
The data block of a Keptn CloudEvent carries the Keptn Payload of a specific event and contains the properties:
* labels
* message
* project
* service
* stage
* status: indicates whether the service executing the task was able to perform the task without any unexpected errors. Possible values are `succeeded`, `errored`,or `unknown`
* result: indicates the result of a successful task execution without unexpected problems (i.e. status = `succeeded`), such as the result of an evaluation, or a test execution. Possible values are `pass`, `warning`, or `fail`
* *[task]*

Like the task property in the event type, the task property in the data block depends on the task declaration in the Shipyard. Based on the example of a `deployment` task, the data block contains a `deployment` property of type object. Hence, any payload can be added to this `deployment` property

In the following each data block is described and an example of a CloudEvent containing the data block is given.


### Project
#### Project Create Triggered
##### Type
sh.keptn.event.project.create.triggered
##### Data Json Schema

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

##### Example Cloud Event

```json
{
  "data": {
    "projectName": "sockshop",
    "gitRemoteURL": "https://github.com/project/repository",
    "shipyard": "c3RhZ2VzOg0KICAtIG5hbWU6ICJkZXYiDQogICAgZGVwbG95bWVudF9zdHJhdGVneTogImRpcmVjdCINCiAgICB0ZXN0X3N0cmF0ZWd5OiAiZnVuY3Rpb25hbCINCiAgLSBuYW1lOiAic3RhZ2luZyINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHRlc3Rfc3RyYXRlZ3k6ICJwZXJmb3JtYW5jZSINCiAgLSBuYW1lOiAicHJvZHVjdGlvbiINCiAgICBkZXBsb3ltZW50X3N0cmF0ZWd5OiAiYmx1ZV9ncmVlbl9zZXJ2aWNlIg0KICAgIHJlbWVkaWF0aW9uX3N0cmF0ZWd5OiAiYXV0b21hdGVkIg0K"
  },
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.project.create.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Project Create Started
##### Type
sh.keptn.event.project.create.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.project.create.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ProjectCreateStartedEventData",
  "definitions": {
    "ProjectCreateStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.project.create.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Project Create Finished
##### Type
sh.keptn.event.project.create.finished
##### Data Json Schema

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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.project.create.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Service
#### Service Create Started
##### Type
sh.keptn.event.service.create.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.service.create.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ServiceCreateStartedEventData",
  "definitions": {
    "ServiceCreateStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.service.create.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Service Create Status Changed
##### Type
sh.keptn.event.service.create.status.changed
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.service.create.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ServiceCreateStatusChangedEventData",
  "definitions": {
    "ServiceCreateStatusChangedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.service.create.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Service Create Finished
##### Type
sh.keptn.event.service.create.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.service.create.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ServiceCreateFinishedEventData",
  "definitions": {
    "ServiceCreateFinishedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.service.create.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Approval
#### Approval Triggered
##### Type
sh.keptn.event.approval.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.approval.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalTriggeredEventData",
  "definitions": {
    "Approval": {
      "required": [
        "pass",
        "warning"
      ],
      "properties": {
        "pass": {
          "enum": [
            "automatic",
            "manual"
          ],
          "type": "string"
        },
        "warning": {
          "enum": [
            "automatic",
            "manual"
          ],
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "ApprovalTriggeredEventData": {
      "required": [
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "approval": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/Approval"
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.approval.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Approval Started
##### Type
sh.keptn.event.approval.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.approval.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalStartedEventData",
  "definitions": {
    "ApprovalStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.approval.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Approval Status Changed
##### Type
sh.keptn.event.approval.status.changed
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.approval.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalStatusChangedEventData",
  "definitions": {
    "ApprovalStatusChangedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.approval.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Approval Finished
##### Type
sh.keptn.event.approval.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.approval.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ApprovalFinishedEventData",
  "definitions": {
    "ApprovalFinishedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.approval.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Deployment
#### Deployment Triggered
##### Type
sh.keptn.event.deployment.triggered
##### Data Json Schema

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
    "DeploymentTriggeredData": {
      "required": [
        "deploymentURIsLocal",
        "deploymentstrategy"
      ],
      "properties": {
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
        "deploymentstrategy": {
          "enum": [
            "direct",
            "blue_green_service",
            "user_managed"
          ],
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "DeploymentTriggeredEventData": {
      "required": [
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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
          "$ref": "#/definitions/DeploymentTriggeredData"
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

##### Example Cloud Event

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
      "deploymentURIsLocal": [
        "http://carts.sockshop-staging.svc.cluster.local"
      ],
      "deploymentURIsPublic": [
        "http://carts.sockshot.local:80"
      ],
      "deploymentstrategy": "direct"
    }
  },
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.deployment.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Deployment Started
##### Type
sh.keptn.event.deployment.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.deployment.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/DeploymentStartedEventData",
  "definitions": {
    "DeploymentStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.deployment.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Deployment Status Changed
##### Type
sh.keptn.event.deployment.status.changed
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.deployment.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/DeploymentStatusChangedEventData",
  "definitions": {
    "DeploymentStatusChangedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.deployment.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Deployment Finished
##### Type
sh.keptn.event.deployment.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.deployment.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/DeploymentFinishedEventData",
  "definitions": {
    "DeploymentFinishedData": {
      "required": [
        "deploymentstrategy",
        "deploymentURIsLocal",
        "deploymentNames",
        "gitCommit"
      ],
      "properties": {
        "deploymentstrategy": {
          "enum": [
            "direct",
            "blue_green_service",
            "user_managed"
          ],
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "deployment": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/DeploymentFinishedData"
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

##### Example Cloud Event

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
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.deployment.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Test
#### Test Triggered
##### Type
sh.keptn.event.test.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.test.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestTriggeredEventData",
  "definitions": {
    "TestTriggeredDeploymentDetails": {
      "required": [
        "deploymentURIsLocal"
      ],
      "properties": {
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
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "TestTriggeredDetails": {
      "required": [
        "teststrategy"
      ],
      "properties": {
        "teststrategy": {
          "enum": [
            "real-user",
            "functional",
            "performance",
            "healthcheck"
          ],
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "TestTriggeredEventData": {
      "required": [
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "test": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/TestTriggeredDetails"
        },
        "deployment": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/TestTriggeredDeploymentDetails"
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.test.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Test Started
##### Type
sh.keptn.event.test.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.test.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestStartedEventData",
  "definitions": {
    "TestStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.test.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Test Status Changed
##### Type
sh.keptn.event.test.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.test.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestStatusChangedEventData",
  "definitions": {
    "TestStatusChangedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.test.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Test Finished
##### Type
sh.keptn.event.test.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.test.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/TestFinishedEventData",
  "definitions": {
    "TestFinishedDetails": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "test": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/TestFinishedDetails"
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.test.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Evaluation
#### Evaluation Triggered
##### Type
sh.keptn.event.evaluation.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.evaluation.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EvaluationTriggeredEventData",
  "definitions": {
    "Deployment": {
      "required": [
        "deploymentNames"
      ],
      "properties": {
        "deploymentNames": {
          "items": {
            "type": "string"
          },
          "type": "array"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "Evaluation": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "test": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/Test"
        },
        "evaluation": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/Evaluation"
        },
        "deployment": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/Deployment"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "Test": {
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
    }
  }
}
```
</p>
</details>

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.evaluation.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Evaluation Started
##### Type
sh.keptn.event.evaluation.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.evaluation.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EvaluationStartedEventData",
  "definitions": {
    "EvaluationStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.evaluation.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Evaluation Status Changed
##### Type
sh.keptn.event.evaluation.status.changed
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.evaluation.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EvaluationStatusChangedEventData",
  "definitions": {
    "EvaluationStatusChangedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.evaluation.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Evaluation Finished
##### Type
sh.keptn.event.evaluation.finished
##### Data Json Schema

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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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
        "passTargets",
        "warningTargets",
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
        "displayName": {
          "type": "string"
        },
        "passTargets": {
          "items": {
            "$schema": "http://json-schema.org/draft-04/schema#",
            "$ref": "#/definitions/SLITarget"
          },
          "type": "array"
        },
        "warningTargets": {
          "items": {
            "$ref": "#/definitions/SLITarget"
          },
          "type": "array"
        },
        "keySli": {
          "type": "boolean"
        },
        "status": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
          "passTargets": [
            {
              "criteria": "\u003c=+10%",
              "targetValue": 600,
              "violated": true
            }
          ],
          "warningTargets": [
            {
              "criteria": "\u003c=+20%",
              "targetValue": 800,
              "violated": true
            }
          ],
          "keySli": false,
          "status": "failed"
        }
      ],
      "comparedEvents": [
        "event-id-1",
        "event-id-2"
      ],
      "gitCommit": "ca82a6dff817gc66f44342007202690a93763949"
    }
  },
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.evaluation.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Evaluation Invalidated
##### Type
sh.keptn.event.evaluation.invalidated
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.evaluation.invalidated</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/EventData",
  "definitions": {
    "EventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.evaluation.invalidated"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Release
#### Release Triggered
##### Type
sh.keptn.event.release.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.release.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ReleaseTriggeredEventData",
  "definitions": {
    "DeploymentFinishedData": {
      "required": [
        "deploymentstrategy",
        "deploymentURIsLocal",
        "deploymentNames",
        "gitCommit"
      ],
      "properties": {
        "deploymentstrategy": {
          "enum": [
            "direct",
            "blue_green_service",
            "user_managed"
          ],
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
    "ReleaseTriggeredEventData": {
      "required": [
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "deployment": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/DeploymentFinishedData"
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

##### Example Cloud Event

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
      "deploymentstrategy": "duplicate",
      "deploymentURIsLocal": [
        "http://carts.sockshop-staging.svc.cluster.local"
      ],
      "deploymentURIsPublic": [
        "http://carts.sockshot.local:80"
      ],
      "deploymentNames": [
        "carts-primary",
        "carts-generated"
      ],
      "gitCommit": "ca82a6dff817gc66f44342007202690a93763949"
    }
  },
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.release.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Release Started
##### Type
sh.keptn.event.release.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.release.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ReleaseStartedEventData",
  "definitions": {
    "ReleaseStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.release.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Release Status Changed
##### Type
sh.keptn.event.release.status.changed
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.release.status.changed</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ReleaseStatusChangedEventData",
  "definitions": {
    "ReleaseStatusChangedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.release.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Release Finished
##### Type
sh.keptn.event.release.finished
##### Data Json Schema

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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.release.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Remediation
#### Remediation Triggered
##### Type
sh.keptn.event.remediation.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.remediation.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/RemediationTriggeredEventData",
  "definitions": {
    "ProblemDetails": {
      "required": [
        "State",
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

```json
{
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.remediation.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Remediation Started
##### Type
sh.keptn.event.remediation.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.remediation.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/RemediationStartedEventData",
  "definitions": {
    "RemediationStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.remediation.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Remediation Status Changed
##### Type
sh.keptn.event.remediation.status.changed
##### Data Json Schema

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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.remediation.status.changed"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Remediation Finished
##### Type
sh.keptn.event.remediation.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.remediation.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/RemediationFinishedEventData",
  "definitions": {
    "RemediationFinishedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.remediation.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Action
#### Action Triggered
##### Type
sh.keptn.event.action.triggered
##### Data Json Schema

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
        "action"
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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
        "State",
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

##### Example Cloud Event

```json
{
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.action.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Action Started
##### Type
sh.keptn.event.action.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.action.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ActionStartedEventData",
  "definitions": {
    "ActionStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.action.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Action Finished
##### Type
sh.keptn.event.action.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.action.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ActionFinishedEventData",
  "definitions": {
    "ActionData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
    "action": {
      "gitCommit": "93a5-3fas-a09d-8ckf"
    }
  },
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.action.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Get SLI
#### Get SLI Triggered
##### Type
sh.keptn.event.get-sli.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.get-sli.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/GetSLITriggeredEventData",
  "definitions": {
    "GetSLI": {
      "required": [
        "sliProvider",
        "start",
        "end"
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
          "items": {
            "type": "string"
          },
          "type": "array"
        },
        "customFilters": {
          "items": {
            "$schema": "http://json-schema.org/draft-04/schema#",
            "$ref": "#/definitions/SLIFilter"
          },
          "type": "array"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "GetSLITriggeredEventData": {
      "required": [
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "get-sli": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/GetSLI"
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
  }
}
```
</p>
</details>

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.get-sli.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Get SLI Started
##### Type
sh.keptn.event.get-sli.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.get-sli.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/GetSLIStartedEventData",
  "definitions": {
    "GetSLIStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.get-sli.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Get SLI Finished
##### Type
sh.keptn.event.get-sli.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.get-sli.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/GetSLIFinishedEventData",
  "definitions": {
    "GetSLIFinished": {
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
        },
        "indicatorValues": {
          "items": {
            "$schema": "http://json-schema.org/draft-04/schema#",
            "$ref": "#/definitions/SLIResult"
          },
          "type": "array"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },
    "GetSLIFinishedEventData": {
      "required": [
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
          "type": "string"
        },
        "message": {
          "type": "string"
        },
        "get-sli": {
          "$schema": "http://json-schema.org/draft-04/schema#",
          "$ref": "#/definitions/GetSLIFinished"
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
    }
  }
}
```
</p>
</details>

##### Example Cloud Event

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.get-sli.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Monitoring
#### Configure Monitoring Triggered
##### Type
sh.keptn.event.configure-monitoring.triggered
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.configure-monitoring.triggered</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ConfigureMonitoringTriggeredEventData",
  "definitions": {
    "ConfigureMonitoringTriggeredEventData": {
      "required": [
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

```json
{
  "data": {
    "project": "sockshop",
    "stage": "dev",
    "service": "carts",
    "configureMonitoring": {
      "type": "dynatrace"
    }
  },
  "datacontenttype": "application/json",
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "type": "sh.keptn.event.configure-monitoring.triggered"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Configure Monitoring Started
##### Type
sh.keptn.event.configure-monitoring.started
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.configure-monitoring.started</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ConfigureMonitoringStartedEventData",
  "definitions": {
    "ConfigureMonitoringStartedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.configure-monitoring.started"
}
```

([&uarr; up to index](#keptn-cloud-events))
#### Configure Monitoring Finished
##### Type
sh.keptn.event.configure-monitoring.finished
##### Data Json Schema

<details><summary>Json Schema of sh.keptn.event.configure-monitoring.finished</summary>
<p>

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "$ref": "#/definitions/ConfigureMonitoringFinishedEventData",
  "definitions": {
    "ConfigureMonitoringFinishedEventData": {
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
          "enum": [
            "succeeded",
            "errored",
            "unknown"
          ],
          "type": "string"
        },
        "result": {
          "enum": [
            "pass",
            "warning",
            "fail"
          ],
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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.configure-monitoring.finished"
}
```

([&uarr; up to index](#keptn-cloud-events))
### Problem
#### Problem Open
##### Type
sh.keptn.event.problem.open
##### Data Json Schema

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

##### Example Cloud Event

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
  "id": "c4d3a334-6cb9-4e8c-a372-7e0b45942f53",
  "shkeptncontext": "a3e5f16d-8888-4720-82c7-6995062905c1",
  "source": "source-service",
  "specversion": "1.0",
  "triggeredid": "3f9640b6-1d2a-4f11-95f5-23259f1d82d6",
  "type": "sh.keptn.event.problem.open"
}
```

([&uarr; up to index](#keptn-cloud-events))
