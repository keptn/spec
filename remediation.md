# Remediation

* The *Remediation* defines remediation actions to execute in response to a problem. 
* This configuration is interpreted by Keptn to trigger the proper remediation actions. 

The *problem type* maps a problem to a remediation by a matching problem title (implemented as string match).
* It is possible to declare multiple problem types for a remediation.
* For the case of triggering a remediation based on an unknown problem, the problem type `default` is supported.

## Meta-data

* `apiVersion`: The version of the Remediation specification in the format: `spec.keptn.sh/x.y.z`
* `kind`: is `Remediation`
* `metadata`: Contains at least the property name, which declares a unique name for the Remediation.
* `spec`: Consists of the property remediations.
  * `remediations`: An array of remediations triggered in response to a problem.

## Remediation

A Remediation consisits of a list of remediations. A remediation has the properties:

* `problemType`: Maps a problem to a remediation by a matching problem title (implemented as string match).
* `actionsOnOpen`:  Declares a list of actions triggered in course of the remediation.

## Action

A remediation consists of a list of actions. An action has the properties:

* `name`: A name used for display purposes.
* `action`: A unique name required by the action-provider (Keptn-service) that executes the action.
* `description`: Provides more details about the action.
* `value`: Allows adding an arbitrary list of values for configuring the action.

# Specification

```json
"Remediation": {
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
    "spec": {
      "$ref": "#/definitions/Spec"
    },
  },
  "additionalProperties": false,
  "type": "object"
},

"Spec": {
  "required": [
    "remediations"
  ],
  "properties": {
    "remediations": {
      "items": {
        "$ref": "#/definitions/Remediation"
      },
      "type": "array"
    },
  },
  "additionalProperties": false,
  "type": "object"
},

"Remediation": {
  "required": [
    "actionsOnOpen",
    "problemType"
  ],
  "properties": {
    "actionsOnOpen": {
      "items": {
        "$ref": "#/definitions/Action"
      },
      "type": "array"
    },
    "problemType": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}

"Action": {
  "required": [
    "action",
    "description",
    "name",
    "value"
  ],
  "properties": {
    "action": {
      "type": "string"
    },
    "description": {
      "type": "string"
    },
    "name": {
      "type": "string"
    },
    "value": {      
      "type": ["object", "string"]
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

# Example of a Remediation (in yaml)

```yaml
apiVersion: spec.keptn.sh/0.1.4
kind: Remediation
metadata:
  name: remediation-configuration
spec:
  remediations: 
  - problemType: "Response time degradation"
    actionsOnOpen:
    - name: Toogle feature flag
      action: togglefeature
      description: Toggle feature flag EnablePromotion from ON to OFF
      value:
        EnablePromotion: off
  - problemType: "default"
    actionsOnOpen:
    - name: Escalate problem
      action: escalate
      description: Escalate the problem using a SlackBot
      value:
```

([&uarr; up to index](#specifications-for-site-reliability-engineering-with-keptn))
