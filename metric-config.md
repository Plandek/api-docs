# Metric Config

Query method to return the config data for the given [metric](./metric-input.md#metrickey), calculated for the given [`clientKey`](./metric-input.md#clientkey) and [`dataSetKey`](./metric-input.md#datasetkey).

It will allow us to get the

- `breakdownConfigs`: objects that determine the possible values of the [`breakdown`](./metric-input.md#breakdown) input (it has to be one of the breakdownConfig's key)
- `facetConfigs`: objects that determine the possible filters to be used in the [filters in the metric input](./metric-input.md#filters) as [multiFilters](./metric-input.md#multifilters), [boolFilters](./metric-input.md#boolfilters), [rangeFilters](./metric-input.md#rangefilters), or [entity filters](./metric-input.md#entitysetrefinement).

query
```graphql
query($clientKey: KeyString!, $dataSetKey: KeyString!, $metricKey: MetricKey!) {
  metricConfig(
    clientKey: $clientKey
    dataSetKey: $dataSetKey
    metricKey: $metricKey
  ) {
    enabled
    metricKey

    facetConfigs {
      key
      label
      valueType
      filterType
    }

    breakdownConfigs {
      key
      label
      valueType
      filterType
    }
  }
}
```

variables

```json
{
  "clientKey": "plandek",
  "dataSetKey": "all",
  "metricKey": "COMMITS_COUNT"
}
```

response

```json
{
  "data": {
    "metricConfig": {
      "enabled": true,
      "metricKey": "COMMITS_COUNT",
      "facetConfigs": [
        {
          "key": "committer_id",
          "label": "Committer",
          "valueType": "PERSON",
          "filterType": "MULTI"
        },
        {
          "key": "repo_key",
          "label": "Repository",
          "valueType": "REPOSITORY",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.component_string",
          "label": "Ticket Component",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.current-status_string",
          "label": "Ticket Current Status",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.epic-link_string",
          "label": "Ticket Epic Link",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.epic-name_string",
          "label": "Ticket Epic Name",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.epic-status_string",
          "label": "Ticket Epic Status",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.epic-workflow-status_string",
          "label": "Ticket Epic Workflow Status",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.fix-version_string",
          "label": "Ticket Fix Version",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.jira_issue_type",
          "label": "Ticket Issue Type",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.labels_string",
          "label": "Ticket Labels",
          "valueType": "SAME",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.priority_string",
          "label": "Ticket Priority",
          "valueType": "SAME",
          "filterType": "MULTI"
        }
      ],
      "breakdownConfigs": [
        {
          "key": "committer_id",
          "label": "Committer",
          "valueType": "PERSON",
          "filterType": "MULTI"
        },
        {
          "key": "_data_set_category_--teams",
          "label": "Teams",
          "valueType": "DATA_SET",
          "filterType": "DATA_SET_CATEGORY"
        },
        {
          "key": "repo_key",
          "label": "Repository",
          "valueType": "REPOSITORY",
          "filterType": "MULTI"
        },
        {
          "key": "nested_tickets.dynamic_extra_fields.component_string",
          "label": "Ticket Component",
          "valueType": "SAME",
          "filterType": "MULTI"
        }
      ]
    }
  }
}
```

## valueType

Describes what the value is. Modelled by the enum `FilterValueType` in the GraphQL schema, which contains documentation for each value.

The relevant ones are the valueTypes that determines that these are entities, and should be used as an [`entitySetRefinement`](./metric-input.md#entitysetrefinement) instead of a [`multiFilter`](./metric-input.md#multifilters).

They are:

- `BOARD`: `boardsKeySet`
- `CI_PIPELINE`: `ciPipelinesKeySet`
- `MONITORED_SERVICE`: `monitoredServicesKeySet`
- `PERSON`: `peopleKeySet`
- `REPOSITORY`: `repositoriesKeySet`

We also have the `SPRINT` valueType which determines that the values are [sprintKeys](./metric-input.md#sprintkey).


## filterType

Describes how the filter can be used in the [metric input](./metric-input.md#filters). 

Modelled by the enum `FilterType` in the GraphQL schema, which contains documentation for each value.

The most relevant ones are:

- `MULTI`: means that it can be used as a [`multiFilter`](./metric-input.md#multifilters)
- `BOOL`: means that it can be used as a [`boolFilter`](./metric-input.md#boolfilters)
- `RANGE`: means that it can be used as a [`rangeFilter`](./metric-input.md#rangefilters)
