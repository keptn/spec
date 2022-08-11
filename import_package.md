# Import package

An import package is a .zip file that contains a **manifest.yaml** that describes additional 
contents and actions to be performed when processing the package, in addition, all files
referenced within the manifest must be included in the .zip file.

## Meta-data

* `apiVersion` is set to `v1beta1` 

## Tasks

* `tasks`: list of api actions and resource tasks.
    * `id`:  unique identifier of the task.  Must contain only upper-case, 
       lower-case letters, digits or underscores (`[A-Za-z0-9_]+`)
    * `type`: Must be either **api** or **resource**. All required properties of the specified
       type have to be implemented.
    * `name` *(optional)*: A human readable name for the action to easier identify the action
    * `context` *(optional)*: An optional context which can contain additional data for the 
      API action or resource task. A context must only contain string values and supports templating.

### API action
An API action is a task that interacts with a specific Keptn API endpoint. The JSON results of the endpoint
are stored in ` .Tasks.<task-id>.Response.<field>` and are accessible for all following tasks. The following
properties have to be implemented by an API action:
* `action`: The name of the Keptn API endpoint to call. 
  See [Supported API endpoints](#supported-api-endpoints) for more information.
* `payload`: A reference to a file that should be used as body payload for the API action.
  If the action does not take a body, this property is ignored. The file reference has to be
  specified as the relative path within the provided import package.

### Resource task
A resource task adds resources to Keptn. This task is similar to the Keptn CLI 
[add-resource](https://keptn.sh/docs/0.18.x/reference/cli/commands/keptn_add-resource/) argument.
Similar to API actions, resources are also 
processed by the Golang templating engine. The following properties have to be defined for a resource task:
* `resource`: A reference (relative file path) to a file in the import package
* `resourceUri`: The URI at which the file should be stored in Keptn
* `stage` *(optional)*: An optional stage name that can be specified
* `service` *(optional)*: An optional service that can be specified, if specified `stage` has to be non-empty

## Supported API endpoints
If not mentioned otherwise, all payloads support templating and all given properties of the `context` object
can be used in the payload. For more details see the [Templating](#templating) Section. 
The following API actions are supported: 

`keptn-api-v1-create-service`: Create a service
```json
{
    "serviceName": "[[ .Context.service ]]"
}
```

`keptn-api-v1-uniform-create-secret`: Create a secret
```json
{
    "scope":"[[ .Context.scope ]]",
    "data":{
        "token":"[[ .Context.token ]]"
    },
    "name":"[[ .Context.name ]]"
}
```

`keptn-api-v1-uniform-create-webhook-subscription`: Create a webhook subscription
```json
{
    "event": "[[ .Context.event ]]",
    "filter": {
        "projects": ["[[ .Context.project ]]"],              
        "services": ["[[ .Context.service ]]"],              
        "stages": ["[[ .Context.stage ]]"]             
    }
}
```

## Templating
If not mentioned otherwise all string values of the `context` property in a **task** can be used with the
templating syntax of the Golang templating engine. We use `[[` and `]]` as separator. 

### Global Inputs
The following inputs are currently available to be used in all places that support templating:
* `[[ .Inputs.project ]]`: Will be rendered to the current project name

### Tasks references
After a task has been processed, it exposes its response and the already fully rendered context via the 
following properties:
* `[[ .Tasks.<task-id>.Context.<property-name> ]]`: Can be used to access the rendered context property of 
  a given task with the `id`.
* `[[ .Tasks.<task-id>.Response.<property-name> ]]`: Each **API action** provides the response of the action
  the **Response** object. All JSON fields of the response can be accessed via their respective names.

### Resource files
In resource files only properties of the task **context** can be utilized and accessed via 
`[[ .Context.<property-name> ]]`.

# Specification
```json
{
    "definitions": {
        "Task": {
            "required": [
                "id",
                "type"
            ],
            "properties": {
                "id": {
                    "type": "string",
                    "pattern": "[A-Za-z0-9_]+"
                },
                "type": {
                    "type": "string",
                    "enum": [
                        "api",
                        "resource"
                    ]
                },
                "name": {
                    "type": "string"
                },
                "context": {
                    "type": "object",
                    "patternProperties": {
                        "^.*$": {
                            "type": "string"
                        }
                    }
                }
            }
        },
        "APIAction": {
            "$ref": "#/definitions/Task",
            "required": [
                "action",
                "payload"
            ],
            "properties": {
                "type": {
                    "const": "api"
                },
                "action": {
                    "type": "string",
                    "enum": [
                        "keptn-api-v1-create-service",
                        "keptn-api-v1-uniform-create-secret",
                        "keptn-api-v1-uniform-create-webhook-subscription"
                    ]
                },
                "payload": {
                    "type": "string"
                }
            }
        },
        "ResourceTask": {
            "$ref": "#/definitions/Task",
            "required": [
                "resource",
                "resourceUri",
            ],
            "properties": {
                "type": {
                    "const": "resource"
                },
                "resource": {
                    "type": "string"
                },
                "resourceUri": {
                    "type": "string"
                },
                "stage": {
                    "type": "string",
                    "pattern": "[a-z0-9_-]+"
                },
                "service": {
                    "type": "string",
                    "pattern": "[a-z0-9_-]+"
                }
            }
        },
    },
    "required": [
        "apiVersion"
    ],
    "properties": {
        "apiVersion": {
            "const": "v1beta1"
        },
        "tasks": {
            "type": "array",
            "items": {
                "oneOf": [
                    {
                        "$ref": "#/definitions/APIAction"
                    },
                    {
                        "$ref": "#/definitions/ResourceTask"
                    }
                ]
            }
        }
    }
}
```

# Example
The following example contains the contents of an import package to create a service with 
a webhook subscription for a given project. In addition to the payload files from the [](#supported-api-endpoints)
section, the **manifest.yaml** and **webhook.yaml** files have are also used to produce
the following ZIP-archive:
```
.
├── api
│   ├── create-secret.json
│   ├── create-service.json
│   └── create-subscription.json
├── manifest.yaml
├── resources
│   └── webhook.yaml
└── shipyard.yaml
```

**manifest.yaml**:
```yaml
apiVersion: v1beta1
tasks:
  # Create a service
  - name: "Create service"
    id: create_service
    type: api
    action: "keptn-api-v1-create-service"               # Pre-defined endpoint types (we only support a few)
    payload: "api/create-service.json"                  # JSON file which contains the not validated payload 
    context:                                            # context available for templating in the payload
      service: "my-service-name"                        # Can also use go template syntax: [[ .Struct.field ]]

  # Create a secret which we will later use in webhook
  - name: "Create secret"
    id: create_secret
    type: api
    payload: "api/create-secret.json"
    action: "keptn-api-v1-uniform-create-secret"

  # Create a subscription
  #  Internally this figures out the current webhook integration id
  #  and saves the response into .Tasks.<id>.Response.id
  - name: "Create webhook subscription"
    type: api
    id: create_subscription
    payload: "api/create-subscription.json"
    action: "keptn-api-v1-uniform-create-webhook-subscription"
    context:
      event: "sh.keptn.event.evaluation.triggered"
      project: "[[ .Inputs.project ]]"
      stage: "dev"
      service: "[[ .Tasks.create_service.Content.service ]]"

  # Add a webhook config
  - name: "Webhook config"
    id: add_webhook_config
    type: resource
    resource: "resources/webhook.yaml"                  # where is the file stored in the package
    resourceUri: "webhook.yaml"                         # what should the file be called in the upstream repo
    stage: "[[ .Tasks.create_subscription.Content.stage ]]"
    context:                                            # Placeholders for templating in resources/webhook.yaml
      event: "[[ .Tasks.create_subscription.Context.event ]]"
      project: "[[ .Tasks.create_subscription.Context.project ]]"
      subscriptionId: "[[ .Tasks.create_subscription.Response.id ]]"
```

**webhook.yaml**:
```yaml
apiVersion: webhookconfig.keptn.sh/v1alpha1
kind: WebhookConfig
metadata:
  name: webhook-configuration
spec:
  webhooks:
    - type: [[ .Context.event ]]
      requests:
        - curl --request POST https://slack.com/api/[[ .Context.project ]]/{{ .secret.slack-webhook.token }}
      subscriptionID: [[ .Context.subscriptionId ]]
      sendFinished: true
```
