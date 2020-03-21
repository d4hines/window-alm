# Module: Stacks of Windows

## Overview

Users may "stack" windows on top of each other. This causes multiple tabs
to display in a single window, akin to multiple tabs in a single internet
browser window.

Users create stacks by dragging the tab of one window and dropping it
onto the tab region of another window. Likewise, users may "unstack"
a window by dropping a tab onto the desktop. Exactly one window in the
the stack is said to be the "active tab". Only the active tab is visible.
The active tab provides controls to switch which window is the active tab.

Stacks have no representation as objects in the system. Instead, they are
represented by the "stackedOn" relation, which forms partial ordering of
windows.

## Imports

[Windows](./windows.alm.md)

## Sorts
1. Close Stack
    Closes the whole stack
    ```
    close_stack :: actions
        attributes
    ```
## Functions
### Fluents
1. The Primary Monitor

    An arbitrary monitor is chosen by the user to be the primary monitor, the top left corner
    of which is defined to be the origin of desktop plane. All other monitors are termed
    secondary monitors. 
    ```
    primaryMonitor : monitors
    ``` 
## Axioms

1. Rearranging a Monitor

    Rearranging a monitor may cause it to take on new coordinates.
    ```
    occurs(Action) causes coordinate(Monitor, Dir) = Value if
        instance(Action) = rearrange_monitor,
        new_coordinate(Action, Dir) = Value,
        target(Action) = Monitor.
    ```

    Rearranging a monitor may also cause it to take on new dimensions.
    ```
    occurs(Action) causes width(Monitor) = Value if
        instance(Action) = rearrange_monitor,
        target = Monitor,
        resolution(Action, x) = Value.

    occurs(Action) causes height(Monitor) = Value if
        instance(Action) = rearrange_monitor,
        target = Monitor,
        resolution(Action, x) = Value.
    ```
1. Removing a Monitor

    When a user removes a monitor, every group of windows on that
    monitor are moved to the (possibly new) primary monitor.

    Specifically, let P (dX, dY) represent the measure between the
    top left corner of the top left window in the group and the top
    left corner of the 

    ```

    ```
