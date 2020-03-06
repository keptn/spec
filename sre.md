# Specifications for Site Reliability Engineering with Keptn

To support site reliability engineering with Keptn and to enable the self-healing use case, Keptn relies on three configurations:
* [Service Level Objectives (SLO)](#service-level-objectives-slo-configuration)
* [Service Level Indicators (SLI)](#service-level-indicators-sli-configuration)
* [Remediation Action](#remediation-action)

---

## Service Level Objectives (SLO) Configuration

The *Service Level Objectives (SLO)* configuration specifies a target value or range of values for a service level that is measured by [Service Level Indicators (SLI)](#service-level-indicators-sli-configuration). Next to SLIs, an SLO consists of a service filter that uniquely identifies a service, and evaluation success criteria that depends on the selected comparison strategy. Here is an example of a minimal SLO configuration - the details to each configuration item are described below.

```yaml
spec_version: '1.0'
filter:
  handler: "ItemsController.addToCart"
comparison:
  compare_with: "single_result"
  include_result_with_score: "pass"
  aggregate_function: avg
objectives:
  - sli: response_time_p95
    pass:
      - criteria:
          - "<=+10%"
          - "<1000"
    warning:
      - criteria:
          - "<=800"
total_score:
  pass: "90%"
  warning: "75%"
```

#### Filter
SLOs are defined per service. A service filter is a key-value pair and the following list contains all valid keys for service filters. The service filters project, stage, and service can be inferred from the Keptn configuration by using $PROJECT, $STAGE, and $SERVICE respectively. These values can also be overwritten in the configuration. The service filter id can be used to specify a unique identifier of that service.
* project
* stage
* service
* id

#### Comparison
By default, Keptn compares with the previous values of the SLIs. The **compare_with** configuration parameter controls how many previous results are compared: *single_result* or *several_results*. The **include_result_with_score** configuration parameter controls which of the previous results are included in the comparison: *pass*, *pass_or_warn*, or *all* (*all* is the default, also used if not specified). 

*1. Example:*

```yaml
comparison:
  compare_with: "single_result"
  include_result_with_score: "pass"
  aggregate_function: avg
```
This comparison configuration means that the current result is only compared to the last result that passed. 

> **Note:** If you configure `compare_with: "single_result"` in combination with **number_of_comparison_results**, **compare_with** will negate the **number_of_comparison_results**. 

*2. Example:*

```yaml
comparison:
  compare_with: "several_results"
  number_of_comparison_results: 3
  include_result_with_score: "pass_or_warn"
  aggregate_function: "avg"
```

This comparison configuration means that the current result is compared to the average of the three previous results that had pass or warning as a result.

#### Objectives
An objective consists of a **sli**, a **pass** criteria, an optional **warning** criteria, an optional **weight** criteria and an optional **key_sli** flag. `pass` represents the upper limit up to which an evaluation is successful. `warning`, if defined, describes the border where the result is not pass and not fail, and a manual approval might be needed to decide. `weight` can be used to emphasize the importance of one SLI over the others. By default, `weight` is 1 for all SLIs and can be overwritten. The weight is important for calculating the score later. The `key_sli` flag can be set to true meaning that the objective is not met if this SLI fails.

The following example demonstrates how the objectives work. Assume the following comparison strategy and SLI objective.

```yaml
objectives:
  - sli: response_time_p95
    pass:
      - criteria:
          - "<=+10%"
          - "<1000"
    warning:
      - criteria:
          - "<=800"
    weight: 2
    key_sli: true
```

Further assume, that in the previous evaluation error_rate was 5%. The upper limit for pass is 5.5% (5 + 10%). The following list shows the evaluation results for all possible error_rate values.
*   pass: <= 5.5%
*   fail: > 5.5%

#### Scoring
An evaluation for *pass* counts for one point, an evaluation for *warning* half a point, and an evaluation for *fail* zero points. The default weight of an SLI is 1 and can be overwritten. The maximum score is the sum of the weights of all SLIs.

The actual evaluation result is divided by the maximum score and gives the `total_score` in percent. For example, the maximum score is 92 and the evaluation result is 85 - the `total_score` is 92.39% (85/92*100).

The pass and warning criteria for the `total_score` use the logical operator ">=" by default.

### Specification
```json
"ServiceLevelObjectives": {
  "required": [
    "spec_version",
    "comparison",
    "objectives",
    "total_score"
  ],
  "properties": {
    "spec_version": {
      "type": "string"
    },
    "filter": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "comparison": {
      "$ref": "#/definitions/Comparison"
    },
    "objectives": {
      "items": {
        "$ref": "#/definitions/Objective"
      },
      "type": "array"
    },
    "total_score": {
      "$ref": "#/definitions/Score"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"Comparison": {
  "required": [
    "compare_with"
  ],
  "properties": {
    "aggregate_function": {
      "type": "string"
    },
    "compare_with": {
      "type": "string"
    },
    "include_result_with_score": {
      "type": "string"
    },
    "number_of_comparison_results": {
      "type": "integer"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"Objective": {
  "required": [
    "sli",
    "pass"
  ],
  "properties": {
    "key_sli": {
      "type": "boolean"
    },
    "pass": {
      "items": {
        "$ref": "#/definitions/SLICriteria"
      },
      "type": "array"
    },
    "sli": {
      "type": "string"
    },
    "warning": {
      "items": {
        "$ref": "#/definitions/SLICriteria"
      },
      "type": "array"
    },
    "weight": {
      "type": "integer"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"SLICriteria": {
  "required": [
    "criteria"
  ],
  "properties": {
    "criteria": {
      "items": {
        "type": "string"
      },
      "type": "array"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"Score": {
  "required": [
    "pass",
    "warning"
  ],
  "properties": {
    "pass": {
      "type": "string"
    },
    "warning": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example of a Service Level Objectives (SLO) configuration (in yaml)

```yaml
spec_version: '1.0'
filter:
  handler: "ItemsController.addToCart"
comparison:
  compare_with: "several_results"
  include_result_with_score: "pass"
  number_of_comparison_results: 3
  aggregate_function: avg
objectives:
  - sli: response_time_p95
    pass:
      - criteria:
          - "<=+10%"
          - "<1000"
    warning:
      - criteria:
          - "<=800"
total_score:
  pass: "90%"
  warning: "75%"
```

([&uarr; up to index](#specifications-for-site-reliability-engineering-with-keptn))

## Service Level Indicators (SLI) Configuration

A Service Level Indicator (SLI) is a defined quantitative measure of some aspects of the service level. The query for an SLI is provider (tool) dependent. This is the reason why each SLI-provider relies on an individual SLI configuration. This SLI configuration lists those SLIs that are supported by the SLI-provider by their name and query whereas the query is provider specific. 

#### Indicators
An indicator is a key-value pair with the SLI name as key and the provider-specific query as value.

### Specification
```json
"ServiceLevelIndicators": {
  "required": [
    "spec_version"
    "indicators"
  ],
  "properties": {
    "spec_version": {
      "type": "string"
    },
    "indicators": {
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
```

### Example of a Service Level Indicators (SLI) configuration (in yaml)

```yaml
spec_version: "1.0"
indicators:
  throughput: "builtin:service.requestCount.total:merge(0):count?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  error_rate: "builtin:service.errors.total.count:merge(0):avg?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  response_time_p50: "builtin:service.response.time:merge(0):percentile(50)?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  response_time_p90: "builtin:service.response.time:merge(0):percentile(90)?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
  response_time_p95: "builtin:service.response.time:merge(0):percentile(95)?scope=tag(keptn_project:$PROJECT),tag(keptn_stage:$STAGE),tag(keptn_service:$SERVICE),tag(keptn_deployment:$DEPLOYMENT)"
```

([&uarr; up to index](#specifications-for-site-reliability-engineering-with-keptn))

## Remediation Action
The *Remediation Action* configuration defines remediation actions to execute in response to a problem. This action is interpreted by Keptn to trigger the proper remediation. 

### Specification
```json
"Remediations": {
  "required": [
    "remediations"
  ],
  "properties": {
    "remediations": {
      "items": {
        "$ref": "#/definitions/Remediation"
      },
      "type": "array"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"Remediation": {
  "required": [
    "name",
    "actions"
  ],
  "properties": {
    "actions": {
      "items": {
        "$ref": "#/definitions/RemediationAction"
      },
      "type": "array"
    },
    "name": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
},
"RemediationAction": {
  "required": [
    "action",
    "value"
  ],
  "properties": {
    "action": {
      "type": "string"
    },
    "value": {
      "type": "string"
    }
  },
  "additionalProperties": false,
  "type": "object"
}
```

### Example of a Remediation Action configuration (in yaml)

```yaml
remediations:
- name: "Response time degradation"
  actions:
  - action: scaling
    value: +1
- name: "Failure rate increase"
  actions:
  - action: featuretoggle
    value: EnablePromotion:off
```

([&uarr; up to index](#specifications-for-site-reliability-engineering-with-keptn))