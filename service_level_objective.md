# Service Level Objectives (SLO)

* The *Service Level Objective* (SLO) specifies a target value or range of values for a service level that is measured by Service Level Indicators (SLIs). 
* An SLO is defined per service and consists of a filter that uniquely identifies a deployment of a service, and objectives that depend on the selected comparison strategy.

## Meta-data

* `spec_version`: is set to `1.0`

## Filter

* `filter`: Declares a list of key-value pairs that are used to uniquely identify a deployment of a service. This means that the key of a filter can be used as a placeholder in an SLI query. For example, the filter `svc_id: "a14b-cd87-0d51"` specifies a unique identifier of the deployment of a service. Consequently, the key of the filter (i.e., `svc_id`) can be referenced in an SLI query by `$svc_id`. The filters *project*, *stage*, *service*, and *deployment* can be inferred from the Keptn configuration by using `$PROJECT`, `$STAGE`, `$SERVICE`, and `$DEPLOYMENT` in SLI queries respectively. These values can also be overwritten in the configuration. The default filters are:
  * project
  * stage
  * service
  * deployment

*Example of an SLO with a list of filters:*
```yaml
spec_version: '1.0'
filter:
  mz_id: "4711"
  svc_id: "a14b-cd87-0d51"
comparison:
...
```

*Example of an SLI with reference to the `mz_id` filter from the SLO:*
```yaml
spec_version: "1.0"
indicators:
  throughput: "builtin:service.requestCount.total:merge(0):count?scope=tag(keptn_service:$SERVICE),mzId($mz_id)"
```

## Comparison

* `comparison`: By default, Keptn compares with the previous values of the SLIs. This property allows configuring the comparison strategy based on:
  * `compare_with`: Defines how many previous results are compared: *single_result* or *several_results*. 
  * `include_result_with_score`: Controls which of the previous results are included in the comparison: *pass*, *pass_or_warn*, or *all* (*all* is the default, also used if not specified). 
  * `number_of_comparison_results`: Defines the exact number of previous results to consider.
  * `aggregate_function` *(optional)*: Allows overriding the default aggregation function which is `avg`. 

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

## Objectives

* `objectives`: Defines a list of objectives whereas an objective consists of a **sli** name, a **pass** criteria, an optional **warning** criteria, an optional **weight** criteria and an optional **key_sli** flag. 
  * `sli`: Refers to the name of the SLI.
  * `pass`: Represents the upper limit up to which an evaluation is successful. 
  * `warning` *(optional)*: Describes the border where the result is not pass and not fail, and a manual approval might be needed to decide. 
  * `weight` *(optional)*: Can be used to emphasize the importance of one SLI over the others. By default, `weight` is 1 for all SLIs and can be overwritten. The weight is important for calculating the score later. 
  * `key_sli` *(optional)*: Can be set to true meaning that the objective is not met if this SLI fails.

The following example demonstrates how the objectives work. Assume the following comparison strategy and SLI objective.

```yaml
objectives:
- sli: response_time_p95
  pass:
  - criteria:
    - "<=+10%"
    - "<600"
  warning:
  - criteria:
    - "<=800"
  weight: 2
  key_sli: true
```

Further assume, that in the previous evaluation error_rate was 5%. The upper limit for pass is 5.5% (5 + 10%). The following list shows the evaluation results for all possible error_rate values.
*   pass: <= 5.5%
*   fail: > 5.5%

## Scoring

* `total_score`: An evaluation for *pass* counts for one point, an evaluation for *warning* half a point, and an evaluation for *fail* zero points. The default weight of an SLI is 1 and can be overwritten. The maximum score is the sum of the weights of all SLIs.

> **Note:** The actual evaluation result is divided by the maximum score and gives the `total_score` in percent. For example, the maximum score is 92 and the evaluation result is 85 - the `total_score` is 92.39% (85/92*100).

> **Note:** The pass and warning criteria for the `total_score` use the logical operator ">=" by default.

# Specification

```json
"ServiceLevelObjectives": {
  "required": [
    "comparison",
    "objectives",
    "spec_version",
    "total_score"
  ],
  "properties": {
    "comparison": {
      "$ref": "#/definitions/Comparison"
    },
    "filter": {
      "patternProperties": {
        ".*": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "objectives": {
      "items": {
        "$ref": "#/definitions/Objective"
      },
      "type": "array"
    },
    "spec_version": {
      "type": "string"
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
    "pass",
    "sli"
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

# Example of a Service Level Objectives (SLO) configuration (in yaml)

```yaml
spec_version: '1.0'
filter:
  mz_id: "4711"
  svc_id: "a14b-cd87-0d51"
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
    - "<600"
  warning:
  - criteria:
    - "<=800"
total_score:
  pass: "90%"
  warning: "75%"
```