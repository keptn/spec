# Shipyard

The `shipyard.yml` file defines the stages each deployment has to go through until it is released into production. 

Shipyard files are defined at the level of a project. This means that all services in a project share the same shipyard configuration. 

## Stages

A shipyard file can consist of any number of stages. A stage has the following properties:

* **Name**: The name of the stage. This name will be used for the GitOps branch and the Kubernetes namespace to which services at this stage will be deployed to. Future versions will allow to explicitly define the target namespace.

* **Deployment Strategy**: Defines the deployment strategy used to deploy a new version of a service. Keptn supports deployment strategies of type: 
  * `direct`: Deploys a new version of a service by replacing the old version of the service.
  * `blue_green_service`: Deploys a new version of a service next to the old one. After a successful validation of this new version, it replaces the old one and is marked as stable (i.e., it becomes the `primary`-version).

* **Test Strategy**: Defines the test strategy used to validate a deployment. Failed tests result in an automatic roll-back of the latest deployment in case of a blue/green deployment strategy. Keptn supports tests of type:
  * `functional` 
  * `performance` 
  
* **Approval Strategy**: The approval strategy specifies the kind of approval, which is required before promoting an artifact into the next stage. The approval strategy can be defined based on the evaluation result `pass` and `warning`. 
Keptn supports the following approval strategies for the evaluation results `pass` and `warning`:
  * `automatic`: The artifact is promoted automatically.
  * `manual`: The user is asked for approval.
Per default, an `automatic` approval strategy is used for evaluation result `pass` and `warning`.

* **Remediation Strategy**: The remediation strategy specifies whether remediation actions are enabled or not. To enable remediation actions, the `remediation_strategy` property has to be set to `automated`. The actions are specified in the *Remediation Action* configuration as described [here](./sre.md/#remediation-action).

## Example of a shipyard.yml file

```yaml
stages:
  - name: "dev"
    deployment_strategy: "direct"
    test_strategy: "functional"
  - name: "staging"
    deployment_strategy: "blue_green_service"
    test_strategy: "performance"
    approval_strategy: 
    - pass: "automatic"
    - warning: "manual"
  - name: "production"
    deployment_strategy: "blue_green_service"
    remediation_strategy: "automated"
```
