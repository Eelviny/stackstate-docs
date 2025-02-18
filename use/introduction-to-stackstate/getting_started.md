# Getting Started

Hi! So, you've just installed StackState and you are ready to get started.

## StackPacks

The first step to take is integrating StackState with your IT systems. This can be done by installing one or more [StackPacks](../../stackpacks/about-stackpacks.md).

![StackPacks overview](../../.gitbook/assets/v43_stackpacks.png)

## Explore topology

After installing one or more StackPacks, you can go to the Explore Mode to explore your IT landscape or visit a specific [view](../stackstate-ui/views/about_views.md) from your installed StackPacks.

StackState visualizes components by the layer and domain they are placed in. These are logical groupings of components. Layers are displayed on the vertical axis. Domains are displayed on the horizontal axis.

You can change which part of the landscape you are viewing \(e.g. layers and domains\) with the [filter options](../stackstate-ui/filters.md) on the left-hand side bar, or by [hovering over a component](../stackstate-ui/perspectives/topology-perspective.md#interactive-navigation).

![Explore topology](../../.gitbook/assets/v43_topology.png)

## Topology elements

A topology consists of components and relations combined with their health state. Because topologies can get very large, StackState automatically groups the components.

The health state of a component is indicated by two colors:

* The inner color of a component indicates the health state that is calculated for the component itself.
* The outer color indicates the potential impact of components that this component depends on.

The direction of a relation's arrow indicates dependency. For example, `app -> db` means: `app` depends on `db`. Health propagates in the opposite direction. So if the `db` component turns red, the `app` component will turn red too.

![Component](../../.gitbook/assets/021_topology_elements.png)

## Timeline

The [timeline](/use/stackstate-ui/timeline-time-travel.md) at the bottom of the screen gives you the ability to go to any point in time. All the information that you see \(component details, metric streams, etc.\) is relative to the topology that existed at the currently selected topology time. Normally, StackState is in **live mode**, this means that StackState automatically displays the latest state of the stack.

![Timeline](../../.gitbook/assets/v43_timeline.png)

## Component / relation details

To see the details of a component or a relation, you can click on it. Click on **SHOW COMPONENT PROPERTIES** to see all details of a component.

![Component details](../../.gitbook/assets/v43_component_details.png)

## Telemetry browser

Both components and relations can have one or multiple telemetry streams. The most common type is a metric stream also known as time series. If you click on a metric stream, you can see the metric stream in a popup.

![Telemetry browser](../../.gitbook/assets/v43_component_details_inspect_metric_stream.png)

If you click on a log stream, you can see the log stream in a popup. Again, there are a number of drill-down capabilities available on the left-hand side of the popup.

![Telemetry browser](../../.gitbook/assets/v43_component_details_inspect_log_stream.png)

## Problems

To quickly find the cause of any deviating component, head to the right-hand pane in the selected view where you can find the **Problems** section of the sidebar. It reduces the cognitive flow and provides immediate understanding of ongoing problems. The Problems section pane will show you the cause of problems in your view. This summary is based on the components impacted in your current view combined with all \(potential\) causes. Problems are automatically clustered by their root cause.

Problems and issues are displayed in the following order order:

* StackState prioritizes the most oldest problem and presents the rest from the oldest on the top to the most recent on the bottom.
* Component-specific issues grouped inside problems are displayed from the most recent ones at the top to the oldest at the bottom of the list.

Not all components displayed in the problem pane are necessarily also visible in the current topology view. To make the root cause of a problem visible, hover a mouse pointer over the component and select `show -> root cause` from the `Actions` menu.

* Read more about [problems](../problem-analysis/problems.md)

![Component details](../../.gitbook/assets/v43_problem_summary.png)

## Events

To show all events for the selected Topology, select the Events Perspective from the top of the screen. Examples of important events that may appear here are health state changes and changes to the components themselves, like version changes. With [event handlers](../metrics-and-events/send-event-notifications.md), you can configure StackState to react to any events, for example, by automatically creating a ticket or triggering some automation.

![Events Perspective](../../.gitbook/assets/v43_events-perspective.png)

The Events Perspective is not the only place you can find events; you can find the latest events in the Events section of the View Details pane on the right of the screen.

![Events section](../../.gitbook/assets/v43_events-section.png)

