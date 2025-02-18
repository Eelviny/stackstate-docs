# View filters

## Overview

The **View Filters** pane on the left-hand side of the StackState UI allows you to filter the components \(topology\), events and traces displayed in each perspective. Applied filters can be [saved as a view](filters.md#save-filters-as-a-view) to open directly in the future.

## Filter Topology

Topology filters can be used to select a sub-set of topology components to be shown in any one of the available perspectives. You can browse your topology using basic filters or build an advanced topology filter that zooms in on a specific area of your topology using the StackState in-built query language \(STQL\). Read more about:

* [Basic filters](filters.md#basic-topology-filters)
* [Advanced filters](filters.md#advanced-topology-filters)
* [Topology filtering limits](filters.md#topology-filtering-limits)

### Basic topology filters

The main way of filtering the topology is by using the basic filters. When you set a filter, the open perspective will update to show only the visualization or data for the subset of your topology that matches the filter. Setting multiple filters will narrow down your search further. You can set more than one value for each filter to expand your search

| Filter | Description |
| :--- | :--- |
| Layers, Domains, Environments and Component types | Filter by the component details included when components are imported or created. |
| Component health | Only include components with the named [health state](/use/health-state/health-state-in-stackstate.md) as reported by the associated health check. |
| Component labels | Only include components with a [custom label](../../configure/topology/tagging.md) or a default integration label, for example the [Dynatrace integration](../../stackpacks/integrations/dynatrace.md#dynatrace-filters-for-stackstate-views). |
| Include components | Components named here will be included in the topology **in addition to** the components returned from other filters. |

The example below uses basic filters to return components that match the following conditions:

* In the **Domain** `security check`
* AND has a **Health** state of `Clear` OR `Deviating`
* OR is the **Component** with the name `bambDB`

![Filtering example](../../.gitbook/assets/v43_basic_filter_example.png)

This could also be written as an advanced filter, see [advanced topology filters](filters.md#advanced-topology-filters).

### Advanced topology filters

You can use the in-built [StackState Query Language \(STQL\)](../../develop/reference/stql_reference.md) to build an advanced topology filter that zooms in on a specific area of your topology.

The example below uses an advanced filter to return components that match the following conditions:

* In the domain `security check`
* AND has a health state of `CLEAR` OR `DEVIATING`
* OR has the name `bambDB`

![Filtering \(advanced filter\)](../../.gitbook/assets/v43_advanced_filter_example.png)

This could also be done using basic filters, see [basic topology filters](filters.md#basic-topology-filters).

### Topology filtering limits

To optimize performance, a configurable limit is placed on the amount of elements that can be loaded to produce a topology visualization. The filtering limit has a default value of 10000 elements, this can be manually configured in `etc/application_stackstate.conf` using the parameter `stackstate.topologyQueryService.maxStackElementsPerQuery`.

If a [basic filter](filters.md#basic-topology-filters) or [advanced filter query](filters.md#advanced-topology-filters) exceeds the configured filtering limit, you will be presented with an error on screen and no topology visualization will be displayed.

Note that the filtering limit is applied to the total amount of elements that need to be loaded and not the amount of elements that will be displayed.

In the example below, we first LOAD all neighbors of every component in our topology and then SHOW only the ones that belong to the `applications` layer. This would likely fail with a filtering limit error, as it requires all components to be loaded.

```text
withNeighborsOf(direction = "both", components = (name = "*"), levels = "15")
   AND layer = "applications"
```

To successfully produce this topology visualization, we would need to either re-write the query to keep the number of components loaded below the configured filtering limit, or increase the filtering limit.

## Filter Events

The **View Filters** pane on the left-hand side of the StackState UI can be used to filter the events shown in the [Events Perspective](perspectives/events_perspective.md) and in the **Event** list in the View Details pane on the right of the StackState UI.

The following event filters are available:

| Filter | Description |
| :--- | :--- |
| **Category** | Show only events from one or more [categories](perspectives/events_perspective.md#event-category). |
| **Type** | Click on the **Type** filter box to open a list of all event types that have been generated for the currently filtered components in the current time window. You can select one or more event types to refine the events displayed. |
| **Source** | Events can be generated by StackState or retrieved from an external source system, such as Kubernetes or ServiceNow, by an integration. Click on the **Source** filter box to open a list of all source systems for events that have been generated for the currently filtered components in the current time window. Select one or more source systems to see only those events. |
| **Tags** | Relevant event properties will be added as tags when an event is retrieved from an external system. For example `status:open` or `status:production`. This can help to identify events relevant to a specific problem or environment. |

## Filter Traces

Traces shown in the [Traces Perspective](perspectives/traces-perspective.md) can be filtered based on two properties of the spans they contain:

* Span types
* Span tags

For example, if you filter the trace list for all spans of type `database`, this will return all traces that have at least one span whose type is `database`.

## Save filters as a view

To update the existing view with the currently applied filters, click **Save view** at the top of the screen. To save the current filters as a new view, click **Save view as**. Read more about [StackState views](views/about_views.md).

## Clear applied filters

To clear any filters you have added and return to the saved view filters or initial clean state, click on the view name at the top of the screen. Alternatively, you can select **Reset view** from the **Save view** dropdown menu at the top of the screen, or **Reset** from the **...** menu in the View details pane on the right of the screen.

