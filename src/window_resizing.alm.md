# Modules: Windows Resizing

## Overview

Windows are rectangular views into applications running on the
desktop. They are the primary sort of object in the Finsemble
system.

This module describes the properties of windows "at rest". The [Window Motion](window_motion.alm.md)
module will elaborate on these properties to describe how windows move in response to user action.

## Imports

[Rectangles](rectangles.alm.md)

[Monitors](./monitors.alm.md)

## Sorts
1. Windows

    ```
    windows :: rectangles
    ```
1. Close Window

    ```
    close_window :: actions
        target : windows
    ```

1. Toggle Maximize

    ```
    toggle_maximize :: actions
        target : windows
    ```
1. Minimize
    ```
    minimize :: actions
        target : windows
    ```
1. Unminimize
    ```
    restore :: actions
        target : windows
    ```

## Functions
### Fluents
#### Basic

1. Maximize State 
    ```
    maximized : windows -> booleans
    ```
1. Minimize State

    Various operating system actions cause a 
    window to become minimized. 
    ```
    minimized : windows -> booleans
    ```

1. Transparent
    Various actions cause a window to become
    transparent. While the operating system supports
    a centigrade scale for transperency, it is represented
    here only as a boolean: a window is transperent,
    or it is not.
    ```
    transparent : windows -> booleans
    ```

1. Visible

    ```
    visible : windows -> booleans
    ```



#### Defined
1. Distance


#### Defined
3. Distance is defined differently when measuring between two windows and between a window and
   a monitor. Distance is undefined between two monitors.

    1. Between two windows:

       The distance between two windows A and B is the smallest measure between opposite sides
       of A and B (e.g. the measure from the top side of A to the bottom side of B, etc.), where:
       - A is on the same line as B.
       - A does not overlap B.

       ```
       distance(A, B, D) if
           instance(A, windows),
           instance(B, windows),
           onSameLine(A, B),
           -overlap(A, B),
           #min { E1 - E2 : side(A, Dir) = E1, side(B, Dir') = E2, opposite(Dir, Dir'), E1 > E2 } = D,
       ```
    2. Between a window and a monitor:
        The distance between a window W and monitor M is the smallest 
        measure between like sides of W and M (e.g. the measure from top of W
        to top of M).
        ```
        distance(W, M, D) if
            instance(A, windows),
            instance(M, monitors),
            #min { E1 - E2 : side(A, D) = E1, side(B, D) = E2, E1 > E2 } = D,
        ```
    Distance is a symmetric property.
    ```
    distance(A, B) if distance(B, A).
    ```
