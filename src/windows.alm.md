# Modules: Windows

## Overview

Windows are rectangular views into applications running on the desktop. They are the
primary sort of object in the Finsemble system.

This module describes the properties of windows "at rest". The [Window Motion](window_motion.alm.md)
module will elaborate on these properties to describe how windows move in response to user action.

## Imports
[Rectangles](rectangles.alm.md)

## Sorts
1. Windows

    ```
    windows :: rectangles
    ```

## Functions
### Fluents

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
