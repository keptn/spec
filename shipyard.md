# Shipyard

* The Shipyard defines the stages an artifact has to go through.
* The Shipyard defines the events that are sent out in each stage.
* A Shipyard is defined at the level of a project. This means that all services in a project share the same Shipyard configuration. 

## Meta-data

* `apiVersion`: The version of the Shipyard specification in the format: `spec.keptn.sh/x.y.z`
* `kind`: is `Shipyard`
* `metadata`: Contains at least the property name, which declares a unique name for the Shipyard.
* `spec`: Consists of the property stages.
  * `stages`: An array of stages and each stage consists of the properties name and task sequences.

## Stage

A Shipyard consists of a list of stages. A stage has the properties:

* `name`: A unique name of the stage.
* `sequences`: An array of sequences declared by name, triggers, and tasks.

## Sequence

A stage consists of a list of sequences whereby a sequence is an ordered list of tasks that are triggered sequentially. A sequence has the properties:

* `name`: A unique name of the sequence
* `triggers` *(optional)*: An array of events that trigger the sequence.
* `tasks`: An array of tasks executed by the sequence in the declared order.

## Task

A sequence consists of a list of tasks whereby a single task is the smallest executable unit. A task has the properties:

* `name`: A unique name of the task
* `properties` *(optional)*: Task properties as individual `key:value` pairs. These properties precise the task and are consumed by the unit that executes the task.

# Specification

```json
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
      "$ref": "#/definitions/Metadata"
    },
    "spec": {
      "$ref": "#/definitions/ShipyardSpec"
    }
  },
  "additionalProperties": false,
  "type": "object"
},

"Metadata": {
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
    "sequences"
  ],
  "properties": {
    "name": {
      "type": "string"
    },
    "sequences": {
      "items": {
        "$ref": "#/definitions/Sequence"
      },
      "type": "array"
    }
  },
  "additionalProperties": false,
  "type": "object"
},

"Sequence": {
  "required": [
    "name",
    "triggers",
    "tasks"
  ],
  "properties": {
    "name": {
      "type": "string"
    },
    "triggers": {
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
    sequences:
    - name: artifact-delivery
      tasks:
      - name: deployment
        properties:  
          deploymentstrategy: direct
      - name: test
        properties:
          teststrategy: functional
      - name: evaluation 
      - name: release 

  - name: hardening
    sequences:
    - name: artifact-delivery
      triggers:
      - dev.artifact-delivery.finished
      tasks:
      - name: deployment
        properties: 
          deploymentstrategy: blue_green_service
      - name: test
        properties:  
          teststrategy: performance
      - name: evaluation
      - name: release
        
  - name: production
    sequences:
    - name: artifact-delivery 
      triggers:
      - hardening.artifact-delivery.finished
      tasks:
      - name: deployment
        properties:
          deploymentstrategy: blue_green
      - name: release
      
    - name: remediation
      tasks:
      - name: remediation
      - name: evaluation
```

# Reserved Keptn Tasks

Reserved Keptn tasks are explained below:
* [**approval**](#approval)
* [**deployment**](#deployment)
* [**evaluation**](#evaluation)
* [**release**](#release)
* [**remediation**](#remediation)
* [**test**](#test)

## approval

Defines the kind of approval, which is required before deploying an artifact in a stage. The approval strategy can be defined based on the evaluation result `pass` and `warning`. Keptn supports the approval strategies for the evaluation results `pass` and `warning` set to:
  * `automatic`: Task sequence continues without requesting approval.
  * `manual`:  Task sequence requests for approval before continuing.
  
*Usage:*
```yaml
- name: approval
  properties: 
    pass: automatic
    warning: manual
```

  > **Note:** Per default, an `automatic` approval strategy is used for evaluation result `pass` and `warning`.

## deployment

Defines the deployment strategy used to deploy a new version of a service. For example, the *helm-service* supports the deployment `strategy` set to: 
  * `direct`: Deploys a new version of a service by replacing the old version of the service.
  * `blue_green_service`: Deploys a new version of a service next to the old one. After a successful validation of this new version, it replaces the old one and is marked as stable.

*Usage:*
```yaml
- name: deployment
  properties: 
    deploymentstrategy: blue_green_service
```

## evaluation

Defines the quality evaluation that is executed to verify the quality of a deplyoment based on its SLOs/SLIs. 

*Usage:*
```yaml
- name: evaluation
```

## release

Defines the releasing task that is executed after a successful deployment happened. 

*Usage:*
```yaml
- name: release
```

## remediation

Defines whether remediation actions are enabled or not.

*Usage:*
```yaml
- name: remediation
```

## test

Defines the test strategy used to validate a deployment. Failed tests result in an automatic roll-back of the latest deployment in case of a blue/green deployment strategy. For example, the *jmeter-service* supports the `teststrategy` set to:
  * `functional`: Test a deployment based on functional tests. 
  * `performance`: Test a deployment based on performance/load tests.

*Usage:*
```yaml
- name: test
  properties: 
    teststrategy: functional
```

