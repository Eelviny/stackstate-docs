# Create and edit views

## Overview

You can create and save views in StackState to bookmark a part of your topology that is of particular interest to your team. By default, saved will be visible to all users, these can be [secured by a StackState administrator](about_views.md#secure-views-with-rbac) if required. Not all views are manually created. Many [StackPacks](/stackpacks/about-stackpacks.md) generate views after installation. It is recommended to use these views only as starting points for creating your own views.

## Create a view

{% hint style="info" %}
By default, saved will be visible to all users, these can be [secured by a StackState administrator](about_views.md#secure-views-with-rbac) if required.
{% endhint %}

To create a new view navigate to **Explore Mode** via the hamburger menu or use another view as a starting point. Whenever you change any of the [View filters](../filters.md), a **Save View** button will appear at the top of the screen. Click this button to save your current selection to a view. To create a new view from the current view use the dropdown menu next to the button and select **Save View As**.

In the dialog the following options appear:

| Field Name | Description |
| :--- | :--- |
| View name | The name of the view. |
| View health state enabled | Whether the view has a health state. If this is disabled, the health state, depicted by the colored circle next to the view name, will always be gray. When disabled, the StackState backend will not need to spend resources calculating a view health state each time the view changes. |
| Configuration function | When view health state is enabled, you can choose a [view state configuration function](../../../develop/developer-guides/custom-functions/view-health-state-configuration-functions.md#view-health-state-configuration-function-minimum-health-states) that is used to calculate the view health state whenever there are changes in the view. The default choice is **minimum health states**. |
| Arguments | The required arguments will vary depending on the chosen configuration function. |
| Identifier | \(Optional\) this field can be used to give the view a unique [identifier](../../../configure/identifiers.md). This makes the view uniquely referencable from exported configuration, like the exported configuration in a StackPack. |

## Delete or edit a view

{% hint style="info" %}
* It is not recommended to delete or edit views created by StackPacks. When doing so, you will get a warning that the view is locked. If you proceed anyway the issue needs to be resolved when upgrading the StackPack that created the view.
* Note that changes made to a view will be applied for all users. 
{% endhint %}

A saved view can be edited or deleted from either the **all views** screen, or the view details pane.

1. All views screen:
    - Click **Views** in the main menu to open the **all views** screen.
    - Hover over the view you would like to edit or delete.
    - Click the **Edit view** or **Delete view** button on the right.
2. View details pane:
    - Open the view.
    - Select the context menu (accessed through the triple dots) to the right of the view name in the view details pane.
    - Click **Edit** or **Delete**.