# Webhooks

* A *Webhook* specifies a request that should be performed by Keptn's webhook service when a certain event occurs during a task sequence execution.
* Webhooks need to be linked to a specific subscription of the Keptn Uniform to be executed.
* Webhooks are defined in a file called `webhook.yaml`, which can be stored in either the *project*, *stage* or *service*  directory of a project's upstream repository.

### Metadata

* `apiVersion` is set to `webhookconfig.keptn.sh/v1beta1`
* `Kind` is set to `Webhook`

### Webhooks

* `webhooks`: Defines a list of webhooks which can be executed by the webhook-service
  * `type`: The event type that should trigger the webhook
  * `subscriptionID`: The ID of the subscription for which the webhook should be executed
  * `sendStarted`: Indicates whether the webhook-service should send a `sh.keptn.event.<task>.started` event before executing the requests. Only applicable for webhooks that are triggered by `sh.keptn.event.<task>.triggered` events.
  * `sendFinished`: Indicates whether the webhook-service should send a `sh.keptn.event.<task>.finished` event after executing the requests. Only applicable for webhooks that are triggered by `sh.keptn.event.<task>.triggered` events.
  * `envFrom`: Allows including sensitive data (e.g. API Tokens) in the executed request by referring to a value stored as a Kubernetes secret
  * `requests`: List of requests that should be executed for the webhook

### EnvFrom

* `envFrom` defines a list of references to Kubernetes secrets to include sensitive data in a webhook request.
  * `name`: Name of variable by which it's value can be addressed in the request
  * `secretRef`: Specifies the reference to the Kubernetes secret
    * `name`: Name of the secret
    * `key`: Key within the secret. The value of that key will be bound to `envFrom.name`

### Requests

* `requests`: Defines a list of requests that should be executed for a webhook
  * `url`: Defines the URL that should be called 
  * `method`: Defines the HTTP method for the request (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`)
  * `headers`: Defines a list of HTTP headers that should be used for the request.
  * `payload`: The payload that should be used for the request

## Specification

```json
{
    "Webhook": {
        "type": "object",
        "additionalProperties": false,
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
                "$ref": "#/definitions/Spec"
            }
        },
        "required": [
            "apiVersion",
            "kind",
            "spec"
        ],
        "title": "Webhook"
    },
    "Metadata": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "name": {
                "type": "string"
            }
        },
        "required": [
            "name"
        ],
        "title": "Metadata"
    },
    "Spec": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "webhooks": {
                "type": "array",
                "items": {
                    "$ref": "#/definitions/Webhook"
                }
            }
        },
        "required": [
            "webhooks"
        ],
        "title": "Spec"
    },
    "Webhook": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "type": {
                "type": "string"
            },
            "subscriptionID": {
                "type": "string"
            },
            "sendStarted": {
                "type": "boolean"
            },
            "sendFinished": {
                "type": "boolean"
            },
            "envFrom": {
                "type": "array",
                "items": {
                    "$ref": "#/definitions/EnvFrom"
                }
            },
            "requests": {
                "type": "array",
                "items": {
                    "$ref": "#/definitions/Request"
                }
            }
        },
        "required": [
            "requests",
            "subscriptionID",
            "type"
        ],
        "title": "Webhook"
    },
    "EnvFrom": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "name": {
                "type": "string"
            },
            "secretRef": {
                "$ref": "#/definitions/SecretRef"
            }
        },
        "required": [
            "name",
            "secretRef"
        ],
        "title": "EnvFrom"
    },
    "SecretRef": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "name": {
                "type": "string"
            },
            "key": {
                "type": "string"
            }
        },
        "required": [
            "key",
            "name"
        ],
        "title": "SecretRef"
    },
    "Request": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "url": {
                "type": "string"
            },
            "method": {
                "type": "string"
            },
            "headers": {
                "type": "array",
                "items": {
                    "$ref": "#/definitions/Header"
                }
            },
            "payload": {
                "type": "string"
            }
        },
        "required": [
            "method",
            "url"
        ],
        "title": "Request"
    },
    "Header": {
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "key": {
                "type": "string"
            },
            "value": {
                "type": "string"
            }
        },
        "required": [
            "key",
            "value"
        ],
        "title": "Header"
    }
}
```

## Example of a Webhook configuration (in yaml)

```yaml
apiVersion: webhookconfig.keptn.sh/v1beta1
kind: WebhookConfig
metadata:
  name: webhook-configuration
spec:
  webhooks:
    - type: sh.keptn.event.evaluation.finished
      subscriptionID: "0123456987"
      sendStarted: true
      sendFinished: true
      envFrom: 
        - name: "secretKey"
          secretRef:
            name: "my-webhook-secret"
            key: "my-key"
      requests:
        - url: https://hooks.slack.com/services/{{.env.secretKey}}
          method: POST
          headers:
            - key: Content-type
              value: application/json
          payload: >-
            {"text": "Evaluation {{.data.evaluation.result}} with a score of {{.data.evaluation.score}} "}
```