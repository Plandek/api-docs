# Metric Input

Here is a description of the common elements needed on most metric query inputs.

## `metricKey`

Determines which specific metric we are requesting. The same query method can serve to calculate many different metrics. The subset of metrics covered by each method is modelled as their own enums in the GraphQL schema.

For example the metrics covered by `ticketsMetric` query method is described in the `TicketsMetric` enum.

## `granularity`

Only relevant for the [Metric methods](./query-methods-for.md#metric-methods).

Modelled with the enum `Granularity` in the GraphQL schema.

```graphql
enum Granularity {
  day
  week
  month
  year
}
```

It defines the expected time range that each point in a [timeSeries](./query-methods-for.md#metric-methods) will cover.


## `breakdown`

If blank, states that the metric should return a single summary, delta and Time Series for the global result.

If it has a value, it will determine by which entity or attribute we are breaking down the values.

The value of `breakdown` has to be the key of one of the [available breakdownConfigs](./metric-config.md).

## `sprintDateRangeFieldOverride`

Only needed if we are using sprints, either filtering or returning them in the metric.
It will ignore the default setting for our tenant (`clientKey`) and sets if the date range covered by a sprint will finish on its EndDate or on its CompletedDate.

Modelled in the enum `SprintDateRangeField` in the GraphQL schema.

## `filters`

This is the object that determines which items will be taken into account in the calculation.

### `clientKey`

This is the tenant key.

### `dataSetKey`

This is the key of the DataSet to be used. Please see the help section in the UI for a description of what a DataSet is.

A data set applies a set of filters based on the entities it covers.

There is always a data set with key `all` that applies no filter.

### `dateRange`

Determines the section of time to use in the calculations.

```json
{
  "from": "2020-01-01",
  "to": "2020-01-10"
}
```

### `sprintKey`

To be used instead of `dateRange`. It determines the key of the sprint to be used as a filter. 
e.g. "only tickets in the given ticket."

See [Available Sprints query](./available-sprints.md)

### `pastSprintKey`

only relevant if we are using `sprintKey`. It determines the key of the sprint to be considered the "past sprint" to [calculate deltas](./query-methods-for.md#metric-methods).

### `percentileRange`

Used to remove outliers. The default is `{ "from": 0, "to": 100 }` which does not remove anything.

If we send `{ "from": 0, "to": 99 }` it will calculate the value in the position of the percentile 100, and remove all the items with that value from the calculation.

### `multiFilters`

Specifies a filter of a specific attribute of the items, using multiple values. It can also specify values that will be used to ignore items.

The values and keys of the multiFilters are determined by the results of the [Facets query](./query-methods-for.md#facets-methods)

See the following info about [how to know how to apply each specific filter](#entity-multifilter-boolfilter-or-rangefilter).

### `boolFilters`

Similar to the [`multiFilters`](#multifilters) but the value is only either `true` or `false`.

See the following info about [how to know how to apply each specific filter](#entity-multifilter-boolfilter-or-rangefilter).


### `rangeFilters`

Similar to the [`multiFilters`](#multifilters) but the value is a numeric range.

See the following info about [how to know how to apply each specific filter](#entity-multifilter-boolfilter-or-rangefilter).


### `entitySetRefinement`

Describe the intention to refine the filters of the DataSet, applying extra filters on top of the DataSet ones.

It is an object with an entry for each type of entity. The default is:

```json
 {
  "boardsKeySet": {
    "type": "ALL"
  },
  "peopleKeySet": {
    "type": "ALL"
  },
  "repositoriesKeySet": {
    "type": "ALL"
  },
  "ciPipelinesKeySet": {
    "type": "ALL"
  },
  "monitoredServicesKeySet": {
    "type": "ALL"
  }
}
```

The value of each entity is a [KeySet](#keyset).

## `overrideFilters`

Mainly relevant for the [Details methods](./query-methods-for.md#details-methods), and in the majority of the other cases it should be an empty object.

Determines an extra list of filters to be applied on top of the [`filters`](#filters). Its main use case is to apply extra filters after calculating the items to exclude based on the [`percentileRange`](#percentilerange).

## Entity, MultiFilter, BoolFilter or RangeFilter

We have many ways of filtering and it can be unclear where to use them. We can see if a specific filter is supposed to be used as a `multiFilter`, a `boolFilter`, or a `rangeFilter` by checking the [`filterType` of its config](./metric-config.md#filtertype). We can also see if we are supposed to use it as an `entitySetRefinement` by checking the [`valueType` of its config](./metric-config.md#valuetype) 

## KeySet

Describes a set of elements, which is used to describe a filter of entities.

They are modelled using the [key-set library](https://github.com/eturino/ts-key-set)

There are 4 types of KeySets

- `ALL`: represents the entirety of possible keys (`𝕌`). Does not apply a filter.
- `NONE`: represents an empty set (`∅`). Excludes every possible item
- `SOME`: represents a concrete set (`A ⊂ 𝕌`). Applies a filter to include only the given elements.
- `ALL_EXCEPT_SOME`: represents the complementary of a set, all the elements except the given ones (`A' = {x ∈ 𝕌 | x ∉ A}`) _(see [Complement in Wikipedia](https://en.wikipedia.org/wiki/Complement_\(set*theory\)))* Applies a filter to exclude the given elements.

The JSONs to use for each one are:

- `ALL`: `{ "type": "ALL" }`
- `NONE`: `{ "type": "NONE" }`
- `SOME`: `{ "type": "SOME", "elements": ["a", "b"] }`
- `ALL_EXCEPT_SOME`: `{ "type": "ALL_EXCEPT_SOME", "elements": ["a", "b"] }`
