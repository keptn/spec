# Shipyard

* The Shipyard defines the stages an artifact has to go through
* The Shipyard defines the events that are sent out in each stage 
* A Shipyard is defined at the level of a project. This means that all services in a project share the same Shipyard configuration. 

## Meta-data

* `apiVersion`: The version of the Shipyard specification in the format: `spec.keptn.sh/x.y.z`
* `kind`: is `Shipyard`
* `metadata`: Contains at least the property name, which declares a unique name for the Shipyard.
* `spec`: Consists of the property stages.
  * `stages`: An array of stages and each stage consists of the properties name and workflows.

## Stage

A Shipyard consists of any number of stages. A stage has the properties:

* `name`: A unique name of the stage.
* `workflows`: An array of workflows declared by name, listen, and tasks.

## Workflow

A stage consists of any number of workflows. A workflow has the properties:

* `name`: A unique name of the workflow
* `listen` *(optional)*: An array of events that trigger the workflow.
* `tasks`: An array of tasks executed by the workflow in the declared order.

## Task

A workflow consists of any number of tasks. A tasks has the properties:

* `name`: A unique name of the task
* `properties` *(optional)*: Task properties as individual `key:value` pairs. These properties precise the task and are consumed by the unit that executes the task.

# Specification

```json
{
  "$ref": "#/definitions/Shipyard",
  "definitions": {
    "Shipyard": {
      "required": [
        "apiVersion",
        "kind",
        "metadata",
        "spec"
      ],
      "properties": {
        "apiVersion": {
          "type": "string"
        },
        "kind": {
          "type": "string"
        },
        "metadata": {
          "$ref": "#/definitions/ShipyardMetadata"
        },
        "spec": {
          "$ref": "#/definitions/ShipyardSpec"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },

    "ShipyardMetadata": {
      "required": [
        "name"
      ],
      "properties": {
        "name": {
          "type": "string"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },

    "ShipyardSpec": {
      "required": [
        "stages"
      ],
      "properties": {
        "stages": {
          "items": {
            "$ref": "#/definitions/Stage"
          },
          "type": "array"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },

    "Stage": {
      "required": [
        "name",
        "workflow"
      ],
      "properties": {
        "name": {
          "type": "string"
        },
        "workflow": {
          "items": {
            "$ref": "#/definitions/Workflow"
          },
          "type": "array"
        }
      },
      "additionalProperties": false,
      "type": "object"
    },

    "Task": {
      "required": [
        "name",
        "properties"
      ],
      "properties": {
        "name": {
          "type": "string"
        },
        "properties": {
          "additionalProperties": true
        }
      },
      "additionalProperties": false,
      "type": "object"
    },

    "Workflow": {
      "required": [
        "name",
        "listen",
        "tasks"
      ],
      "properties": {
        "name": {
          "type": "string"
        },
        "listen": {
          "items": {
            "type": "string"
          },
          "type": "array"
        },
        "tasks": {
          "items": {
            "$ref": "#/definitions/Task"
          },
          "type": "array"
        }
      },

      "additionalProperties": false,
      "type": "object"
    }
  }
}
```

# Example of a Shipyard (in yaml)

```yaml
apiVersion: spec.keptn.sh/0.2.0
kind: Shipyard
metadata:
  name: shipyard-sockshop
spec:
  stages:
  - name: dev
    workflows:
    - name: artifact-delivery
      tasks:
      - name: deployment
        properties:  
          strategy: direct
      - name: test
        properties:
          kind: functional
      - name: evaluation 
      - name: release 

  - name: hardening
    workflows:
    - name: artifact-delivery
      listen:
      - dev.artifact-delivery.finished
      tasks:
      - name: deployment
        properties: 
          strategy: blue_green_service
      - name: test
        properties:  
          kind: performance
      - name: evaluation
      - name: release
        
  - name: production
    workflows:
    - name: artifact-delivery 
      listen:
      - hardening.artifact-delivery.finished
      tasks:
      - name: deployment
        properties:
          strategy: blue_green
      - name: release
      
    - name: remediation
      tasks:
      - name: remediation
      - name: evaluation
```

# Reserved Keptn Tasks

Reserved Keptn tasks are explained below:
* **approval**
* **deployment**
* **evaluate**
* **remediation**
* **release**
* **test**

## approval

Defines the kind of approval, which is required before deploying an artifact in this stage. The approval strategy can be defined based on the evaluation result `pass` and `warning`. Keptn supports the approval strategies for the evaluation results `pass` and `warning`:
  * `automatic`: Workflow continues without requesting approval.
  * `manual`:  Workflow requests for approval before continuing.
  
  > **Note:** Per default, an `automatic` approval strategy is used for evaluation result `pass` and `warning`.

*Example:*

```yaml
- name: approval
  properties: 
    pass: automatic
    warning: manual
```

## deployment

Defines the deployment strategy used to deploy a new version of a service. Keptn supports the deployment `strategy` set to: 
  * `direct`: Deploys a new version of a service by replacing the old version of the service.
  * `blue_green_service`: Deploys a new version of a service next to the old one. After a successful validation of this new version, it replaces the old one and is marked as stable.

*Example:*

```yaml
- name: deployment
  properties: 
    strategy: blue_green_service
```

## evaluate

## remediation

## release

The remediation strategy specifies whether remediation actions are enabled or not. To enable remediation actions, the `remediation_strategy` property has to be set to `automated`. The actions are specified in the *Remediation* configuration as described [here](./sre.md/#remediation).

## test

Defines the test strategy used to validate a deployment. Failed tests result in an automatic roll-back of the latest deployment in case of a blue/green deployment strategy. Keptn supports the test `kind` property set to:
  * `functional` 
  * `performance` 

*Example:*

```yaml
- name: test
  properties: 
    strategy: blue_green_service
```

