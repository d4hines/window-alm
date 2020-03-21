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
represented by the "stacked with" relation, which forms a partial order on
windows.

## Imports

[Windows](./windows.alm.md)

## Sorts
1. Close Stack
    Closes the whole stack
    ```
    close_stack :: actions
        attributes
            target : windows
    ```
1. Stack On
    Stacks a window on another window
    ```
    stack_on :: actions
        attributes
            source : windows
            target : windows
## Functions
### Fluents
#### Basic

1. Stacked Above
    ```
    stacked_above : windows -> windows
    ```

#### Defined

1. Stack Index

    Stacks can be interpreted as a vector of windows.
    Each window in the stack has an index in that vector.
    ```
    stack_index : windows -> integers
    ```

1. Stacked With (Transitive)

    Like "stacked with", but transitive.

    ```
    stacked_with_trans : windows -> windows
    ```

## Axioms

1. The "Stacked With" Relation

    1. Reflexivity

        Every window is stacked with itself.
    1. 
    

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
