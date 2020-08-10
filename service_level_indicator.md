# Service Level Indicators (SLI)

* A *Service Level Indicator* (SLI) is a defined quantitative measure of some aspects of the service level. 
* The query for an SLI is provider (tool) dependent. This is the reason why each SLI-provider relies on an individual SLI configuration. 
* A SLI configuration lists those SLIs that are supported by the SLI-provider by their name and query whereas the query is provider specific. 

## Meta-data

* `spec_version`: is set to `1.0`

## Indicators

* `indicators`: A map of key-value pairs with the SLI name as key and the provider-specific query as value.

# Specification

```json
"ServiceLevelIndicators": {
  "required": [
    "indicators",
    "spec_version"
  ],
  "properties": {
    "indicators": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "spec_version": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

# Example of a Service Level Indicators (SLI) configuration (in yaml)

```yaml
spec_version: "1.0"
indicators:
  throughput: "builtin:service.requestCount.total:merge(0):count?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  error_rate: "builtin:service.errors.total.count:merge(0):avg?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  response_time_p50: "builtin:service.response.time:merge(0):percentile(50)?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  response_time_p90: "builtin:service.response.time:merge(0):percentile(90)?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  response_time_p95: "builtin:service.response.time:merge(0):percentile(95)?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
```