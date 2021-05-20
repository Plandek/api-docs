# `queryMethodsFor(metricKey)`

This endpoint will return the list of queries that are relevant for the given Metric. We can [use it in the GraphQL Playground](./use-graphql-playground.md).

## Example Query
```graphql
query {
  queryMethodsFor(metricKey: COMMITS_COUNT) {
    methodName
    description
  }
}
```

```json
{
  "data": {
    "queryMethodsFor": [
      {
        "methodName": "commitsMetric",
        "description": "load the data for the given Metric (summaries, deltas, time-series)"
      },
      {
        "methodName": "commitsMetricDetails",
        "description": "load the detail data (list of commits) for the given metric"
      },
      {
        "methodName": "commitsMetricFacets",
        "description": "load the allowed filter configs (for ExtraFilters) and their facet values."
      }
    ]
  }
}
```

## Description of methods

We can have 3 types of methods:

- [Metric methods](#metric-methods)
- [Facets methods](#facets-methods)
- [Details methods](#details-methods)

### Metric methods

They are the main methods, responsible for returning the calculations for the given metric. The responses include things like:

- `summariesList`: List of summaries, the global values of the metric for the given input. If the input includes a `breakdown`, then it will have a summary per entity of the breakdown; otherwise it will have a single summary for "general"
- `deltasList`: Deltas objects for the summaries, which compares the results of the current summaries with the result of the same metric with the same input except using the previous date range period (e.g. if looking from `2020-01-10` to `2020-01-19`, it will look from `2020-01-01` to `2020-01-09`)
- `generalSummary`: The Summary for "general", ignoring the potential `breakdown` in the input.
- `generalDelta`: delta for the `generalSummary`
- `timeSeriesList`: List of time series with the data divided per time period. Like the `summariesList`, it will be a single "general" one or many depending on the `breakdown` in the input. Each point describes the time period that it represents, which is controlled by the `granularity` in the input.
- `rollingAverageTimeSeriesList`: Time Series calculated using the rolling average.

[Here we have more information about the metric input](./metric-input.md). 

### Details methods

They will return the different items that are taken 

```graphql
query($input: CommitsMetricDetailInput!) {
  commitsMetricDetails(input: $input) {
    count
    details {
      commitHash
      committerPersonId
      insertedLines
      deletedLines
      repoName
      inDefaultBranch
      commitUrl
    }
  }
}
```

variables:

```json
{
  "input": {
    "filters": {
      "clientKey": "plandek",
      "dataSetKey": "all",
      "dateRange": {
        "from": "2020-02-01",
        "to": "2020-02-10"
      },
      "percentileRange": {
        "from": 0,
        "to": 100
      },
      "sprintKey": null,
      "pastSprintKey": null,
      "entitySetRefinement": {
        "boardsKeySet": { "type": "ALL" },
        "peopleKeySet": { "type": "ALL" },
        "repositoriesKeySet": { "type": "ALL" },
        "ciPipelinesKeySet": { "type": "ALL" },
        "monitoredServicesKeySet": { "type": "ALL" }
      },
      "multiFilters": [],
      "boolFilters": [],
      "rangeFilters": []
    },
    "overrideFilters": {},
    "sprintDateRangeFieldOverride": "END_DATE",
    "metricKey": "COMMITS_COUNT"
  }
}
```

example of response

```json
{
  "data": {
    "commitsMetricDetails": {
      "count": 2,
      "details": [
        {
          "commitHash": "f2c764363c829ad26a0d1c26c95a086ec827e408",
          "committerPersonId": "17766",
          "insertedLines": 97,
          "deletedLines": 0,
          "repoName": "repo1",
          "inDefaultBranch": true,
          "commitUrl": "https://github.com/example-org/repo1/commit/f2c764363c829ad26a0d1c26c95a086ec827e408"
        },
        {
          "commitHash": "b88e0c552343ac686a14a2e3b19325a95ff72b94",
          "committerPersonId": "17766",
          "insertedLines": 5,
          "deletedLines": 4,
          "repoName": "repo1",
          "inDefaultBranch": true,
          "commitUrl": "https://github.com/example-org/repo1/commit/b88e0c552343ac686a14a2e3b19325a95ff72b94"
        }
      ]
    }
  }
}
```

### `Facets` methods

They will return the potential 

```graphql
query($input: CommitsMetricFacetsInput!) {
  commitsMetricFacets(input: $input) {
    filterConfig {
      key
      label
    }
    values {
      count
      key
      label
    }
  }
}
```

variables

```json
{
  "input": {
    "metricKey": "COMMITS_COUNT",
    "overrideFilters": {},
    "sprintDateRangeFieldOverride": "END_DATE",
    "filters": {
      "clientKey": "plandek",
      "dataSetKey": "all",
      "dateRange": {
        "from": "2020-02-01",
        "to": "2020-02-10"
      },
      "percentileRange": {
        "from": 0,
        "to": 100
      },
      "sprintKey": null,
      "pastSprintKey": null,
      "entitySetRefinement": {
        "boardsKeySet": { "type": "ALL" },
        "peopleKeySet": { "type": "ALL" },
        "repositoriesKeySet": { "type": "ALL" },
        "ciPipelinesKeySet": { "type": "ALL" },
        "monitoredServicesKeySet": { "type": "ALL" }
      },
      "multiFilters": [],
      "boolFilters": [],
      "rangeFilters": []
    }
  }
}
```

example of response

```json
{
  "data": {
    "commitsMetricFacets": [
      {
        "filterConfig": {
          "key": "committer_id",
          "label": "Committer"
        },
        "values": [
          {
            "count": 37,
            "key": "10173",
            "label": "<REDACTED>"
          },
          {
            "count": 28,
            "key": "17766",
            "label": "<REDACTED2>"
          }
        ]
      },
      {
        "filterConfig": {
          "key": "repo_key",
          "label": "Repository"
        },
        "values": [
          {
            "count": 79,
            "key": "https-00003A-00002F-00002Fgithub-00002Ecom-00002Fexample-00002Dorg-00002Frepo1",
            "label": "https://github.com/example-org/repo1"
          },
          {
            "count": 32,
            "key": "https-00003A-00002F-00002Fgithub-00002Ecom-00002Fexample-00002Dorg-00002Frepo2",
            "label": "https://github.com/example-org/repo2"
          }
        ]
      }
    ]
  }
}
```