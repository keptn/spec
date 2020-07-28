# Shipyard

* The Shipyard defines the stages each deployment has to go through as well as the events that are sent out in each stage. 

* A Shipyard is defined at the level of a project. This means that all services in a project share the same Shipyard configuration. 

# Specification of a Shipyard

## Meta-data

* `apiVersion`: The version of the Shipyard specification in the format: `spec.keptn.sh/x.y.z`
* `kind`: is `Shipyard`
* `metadata`: Contains at least the property name, which declares a unique name for the Shipyard.
* `spec`: Consists of the property stages.
  * `stages`: An array of stages and each stage consists of the properties name and workflows.

## Definition of Stage:

A Shipyard consists of any number of stages. A stage has the properties:

* `name`: A unique name of the stage.
* `workflows`: An array of workflows declared by name, listen, and tasks.

## Defintion of Workflow:

A stage consists of any number of workflows. A workflow has the properties:

* `name`: A unique name of the workflow
* `listen` *(optional)*: An array of events that trigger the workflow.
* `tasks`: An array of tasks executed by the workflow in the declared order.

## Definition of Task:

* Reserved key tasks are: **approval**, **deployment**, **evaluate**, **remediation**, **release**, **test**
* labels *(optional)*: Task properties as individual `key:value` pairs. These labels precise the task and are consumed by the unit that executes the task.

### approval

Defines the kind of approval, which is required before deploying an artifact in this stage. The approval strategy can be defined based on the evaluation result `pass` and `warning`. Keptn supports the approval strategies for the evaluation results `pass` and `warning`:
  * `automatic`: Workflow continues without requesting approval.
  * `manual`:  Workflow requests for approval before continuing.
  
  > **Note:** Per default, an `automatic` approval strategy is used for evaluation result `pass` and `warning`.

*Example:*

```yaml
- approval: 
    pass: automatic
    warning: manual
```

### deployment

Defines the deployment strategy used to deploy a new version of a service. Keptn supports the deployment `strategy` set to: 
  * `direct`: Deploys a new version of a service by replacing the old version of the service.
  * `blue_green_service`: Deploys a new version of a service next to the old one. After a successful validation of this new version, it replaces the old one and is marked as stable.

*Example:*

```yaml
- deployment: 
    strategy: blue_green_service
```

### evaluate

### remediation

### release

The remediation strategy specifies whether remediation actions are enabled or not. To enable remediation actions, the `remediation_strategy` property has to be set to `automated`. The actions are specified in the *Remediation* configuration as described [here](./sre.md/#remediation).

### test

Defines the test strategy used to validate a deployment. Failed tests result in an automatic roll-back of the latest deployment in case of a blue/green deployment strategy. Keptn supports the test `kind` property set to:
  * `functional` 
  * `performance` 

*Example:*

```yaml
- test: 
    strategy: blue_green_service
```


# JSON schema




# Example of a Shipyard

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
      - deployment:
          strategy: direct
      - test:
          kind: functional
      - evaluation: 
      - release: 

  - name: hardening
    workflows:
    - name: artifact-delivery
      listen:
      - dev.artifact-delivery.finished
      tasks:
      - deployment:
          strategy: blue_green_service
      - test:
          kind: performance
      - evaluation:
      - release:
        
  
  - name: production
    workflows:
    - name: artifact-delivery 
      listen:
      - hardening.artifact-delivery.finished
      tasks:
      - deployment:
          strategy: blue_green
      - release:
      
    - name: remediation
      tasks:
      - remediation:
      - evaluation:
```
