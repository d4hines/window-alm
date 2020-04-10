# Module: Monitor Actions

## Overview

## Imports

[Desktop](./desktop.alm.md)

[Rectangles](./rectangles.alm.md)

[Windows](./windows.alm.md)

## Sorts
### Actions
1. Remove Monitor

    When the user removes the primary monitor, the operating system immediately
    assigns a new primary monitor. The new primary monitor must included
    in the remove monitor action. 
    ```
    remove_monitor :: actions
        attributes
            new_primary_monitor : monitor
    ```
1. Rearrange
    ```
    rearrange_monitor :: actions
        attributes
            target : monitors
            resolution : axes -> integers
            new_coordinate : axes -> integers
    ```
1. Set Primary

    Users can change the primary monitor using the native function of the operating system.
    When this happens, the operating system redefines the origin of the desktop plane, and
    Finsemble must adjust the position of every other rectangle relative to this new origin.
    ```
    rearrange_monitor :: actions
        attributes
            new_position : axes -> integers
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

## Overview

Users may add or remove monitors at any time via native operating system function,
and may also adjust their resolution, scale, arrangement, and primary status. These
actions have various effects on the positions of windows. The primary motivation
of this module is to prevent the act removing monitors from leaving windows permanently
out of view from the user.

## Imports

[Monitors](./monitors.alm.md)

[Windows](./windows.alm.md)

## Functions
### Fluents

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

    When a user removes a monitor, all groups 
    ```

    ```
    
 1. Impossible to Remove Primary Monitor

    It is impossible to remove the primary monitor.

    ```
    impossible Occurs(Action) if
        instance(Action) = remove_monitor,
        target(Action) = primaryMonitor.
    ```

   

# Module: Monitors

## Overview

## Imports

[Desktop](./desktop.alm.md)

[Rectangles](./rectangles.alm.md)

## Sorts
### Objects
1. Monitors

    There are one or more monitors, corresponding to the physical screens connected
    to the device running Finsemble, which are rectangular views into the desktop.
    Monitors range in size from 800x600 to 4096x2160 (though in the future, they may
    grow larger). Typical Finsemble users have between 1 and 9 monitors in a given session.
    ```
    monitors :: rectangles
    ```

## Functions
### Fluents

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
