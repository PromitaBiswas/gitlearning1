# Incremental Refresh using Dataflow in Power BI
## Introduction

With dataflows, we can bring large amounts of data into Power BI. But
it's not practical to refresh the full copy of the data whenever new
data comes, especially when the old data is not going to change. In
another scenario where sources do not store historical data, a full
refresh will overwrite old data with new data and historical data will
be lost. A good solution to these is Incremental Refresh.

Incremental Refresh in available to configure in Power BI desktop also
but data source needs to support query folding. Sources like API do
not support query folding and Dataflow incremental refresh can be used
in that scenario.

## Benefits

- **Refresh occurs faster:** The data refreshes much faster than when
    we truncate and load the data as the incremental refresh only
    refreshes the incremental range.

- **Refresh is more reliable:** For example, it\'s not necessary to
    maintain long-running connections to volatile source systems.

- **Resource consumption is reduced:** Less data to refresh reduces
    overall consumption of memory and other resources.

## Prerequisites

- Dataflows need to be created in Premium Workspace.

- Using incremental refresh requires that source data ingested into
    the dataflow have a DateTime field on which incremental refresh can
    filter.

## Configuration

One dataflow can have multiple tables. Incremental refresh is set up at
the table level.

1. Inside premium workspace we need to define a dataflow.

2. To set up an incremental-refreshed table, we need to start by configuring the table inside the dataflow just like any other tables.

3. After saving the dataflow, in the table view we need to select
**Incremental Refresh** option.
<img src="/images/IncrementalRefreshImages/Table View.png" alt="Table View" width="600">
4. Once the setting window appears, turn on Incremental Refresh.
<img src="/images/IncrementalRefreshImages/Incremental Refresh Setting.png" alt="Incremental Refresh Setting" width="600">

Below is the description of the settings we need to configure.

- **Incremental refresh on/off toggle**: Turns the incremental refresh policy on or off for the table.

- **DateTime column to filter by**: Need to select the query field on which the table should be filtered for increments. This field only contains DateTime fields.

- **Store rows from the past and Refresh rows from the past**: For
    example, if we need to define a policy where we want to store 5
    years of data in total and only incrementally refresh last 5 days of
    data, we will select the value 5 years in "Store rows from the past"
    and 5 days in "Refresh rows from the past" settings.

- **Detect data change:** Refreshing only last 5 days data is
    efficient and faster than refreshing full data but this option helps
    us to only refresh data for the days where data has changed. We need
    to select a datetime column which can be used to identify the
    change. The maximum value of this column is evaluated for each of
    the periods in the incremental range. If that data hasn\'t changed
    since the last refresh, data won't be refreshed for that period.

- **Only refresh complete days:** If we select this option, data will
    be refreshed till the last completed day, and it will ignore the
    remaining data.

5. After the configuration is completed, we can save it.

6. Next step will be to go to settings and configure scheduled refresh.

<img src="/images/IncrementalRefreshImages/Schedule setting option.png" alt="Schedule setting option" width="600">
<img src="/images/IncrementalRefreshImages/Schedule setting.png" alt="Schedule setting" width="600">

## Changing between incremental and full refresh

When moving a dataflow from full refresh to incremental, the new refresh
logic updates the dataflow by adhering to the refresh window and
increment as defined in the incremental refresh settings.

When moving a dataflow from incremental to full refresh, all data
accumulated in the incremental refresh is overwritten by the policy
defined in the full refresh.

## Incremental refresh functionality

Dataflows use partitioning for incremental refresh. Incremental refresh
in dataflows keeps the minimum number of partitions to meet refresh
policy requirements. Old partitions that go out of range are dropped,
which maintains a rolling window. Partitions get merged, reducing the
total number of partitions required. This improves compression and can
improve query performance.

When the schema for a table in an analytical dataflow change, a full
refresh takes place to ensure that all the resulting data matches the
new schema. As a result, any data stored incrementally is refreshed,
also in some cases, if the source system doesn\'t retain historic data,
data will be lost.
