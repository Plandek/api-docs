# Available Sprints

query to get the list of the sprints available to be used with the given filters.

Can be used to see which values of [`sprintKey`](./metric-input.md#sprintkey) are valid.


```graphql
# Returns the list of sprints available for the given set of filters
# `sprintDateRangeFieldOverride` determines which field to use as `dateRange.to`, either `rawEndDate` or `rawCompletedDate`
query(
  $filters: FiltersOnlyDataSetAndEntitiesInput!
  $sprintDateRangeFieldOverride: SprintDateRangeField!
) {
  availableSprints(
    filters: $filters
    sprintDateRangeFieldOverride: $sprintDateRangeFieldOverride
  ) {
    sprintKey
    sprintLabel
    sprintDateRangeField
    dateRange {
      from
      to
    }
    rawStartDate
    rawEndDate
    rawCompletedDate
  }
}
```

variables:

```json
{
  "filters": {
    "clientKey": "plandek",
    "dataSetKey": "all",
    "entitySetRefinement": {
      "boardsKeySet": { "type": "ALL" },
      "peopleKeySet": { "type": "ALL" },
      "repositoriesKeySet": { "type": "ALL" },
      "ciPipelinesKeySet": { "type": "ALL" },
      "monitoredServicesKeySet": { "type": "ALL" }
    }
  },
  "sprintDateRangeFieldOverride": "END_DATE"
}
```
