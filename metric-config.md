# Metric Config

Query method to return the config data for the given [metric](./metric-input.md#metrickey), calculated for the given [`clientKey`](./metric-input.md#clientkey) and [`dataSetKey`](./metric-input.md#datasetkey).

It will allow us to get the

- `breakdownConfigs`: objects that determined the possible values of the [`breakdown`](./metric-input.md#breakdown) input (it has to be one of the breakdownConfig's key)

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
