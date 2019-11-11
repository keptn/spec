# Shipyard

The `shipyard.yml` file defines the stages each deployment has to go through until it is released into production. 

Shipyard files are defined at the level of a project. This means that all services in a project share the same shipyard configuration. 

## Stages


A shipyard file can consist of any number of stages. A stage has the following properties:

* **Name**. The name of the stage. This name will be used for the GitOps branch and the Kubernetes namespace to which services at this stage will be deployed to. Future versions will allow to explicitly define the target namespace.

* **Deployment Strategy**. The deployment strategy defines how an artifact is deployed. Currently, the following strategies are supported: 
  * `direct`: Deploys a new version of a service by replacing the old version of the service.
  * `blue_green_service`: Deploys a new version of a service next to the old one. After a successful validation of this new version, it replaces the old one and is marked as stable (i.e. it becomes the `primary`-version).

   Future versions of Keptn will also support canary releases.

* **Test Strategy**. Defines the test strategy used to validate a deployment. Failed tests result in an automatic roll-back of the latest deployment in case of a blue/green deployment strategy is used. Keptn supports tests of type:
  * `functional` 
  * `performance` 

  In future versions of Keptn, it is not necessary to distinguish between functional and performance tests. But it is necessary to define multiple tests for a stage that can either be executed sequentially or in parallel. Therefore, we need a link between the shipyard and the uniform (i.e. using selectors and labels as known by k8s).

* **Remediation Strategy**. The remediation strategy specifies whether remediation actions should automatically be executed in the respective stage. If Keptn receives a problem event (of type `sh.keptn.event.problem.open`), a remediation action is defined for this problem (in the `remediation.yaml` file), and the remediation is set to `automated`, then the remediation action is executed. 
Keptn supports remediations of type:
  * `automated`
  
* **Approval Strategy**. The approval strategy specifies the kind of approval before promoting an artifact into the next stage. Additionally, it specifies the evaluation result (i.e. can be `pass`, `warning`, and `fail`) that needs to be passed for promoting the artifact. 
Keptn will/should support the following approval strategies:
  * `automatic_pass`: The artifact is promoted in an automatic fashion if the evaluation result is `pass`.
  * `automatic_warning`: The artifact is promoted in an automatic fashion if the evaluation result is `warning` or `pass`.
  * `automatic_fail`: The artifact is promoted in an automatic fashion if the evaluation result is `fail`, `warning`, or `pass`.
  * `manual_pass`: The user is asked for approval if the evaluation result is `pass`.
  * `manual_warning`: The user is asked for approval if the evaluation result is `warning` or `pass`.
  * `manual_fail`: The user is asked for approval if the evaluation result is `fail`, `warning`, or `pass`.

## Example of a shipyard.yml file

```yaml
stages:
  - name: "dev"
    deployment_strategy: "direct"
    test_strategy: "functional"
    approval_strategy: "automatic_warning"
  - name: "staging"
    deployment_strategy: "blue_green_service"
    test_strategy: "performance"
    approval_strategy: "manual_pass"
  - name: "production"
    deployment_strategy: "blue_green_service"
    remediation_strategy: "automated"
```
