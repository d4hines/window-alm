# Modules: Windows

## Overview

Windows are rectangular views into applications running on the desktop. They are the
primary sort of object in the Finsemble system.

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
    total maximized : windows -> booleans
    ```
1. Minimize State

    Various operating system actions cause a 
    window to become minimized. 
    ```
    total minimized : windows -> booleans
    ```

1. Transparent
    Various actions cause a window to become
    transparent. While the operating system supports
    a centigrade scale for transperency, it is represented
    here only as a boolean: a window is transperent,
    or it is not.
    ```
    total transparent : windows -> booleans
    ```

1. Visible

    ```
    total visible : windows -> booleans
    ```

#### Defined
1. Distance

    Distance is the measure between the nearest sides of two rectangles. Distance 
    is irreflexive, and is also undefined between overlaping rectangles. Distance is not 
    defined between every two rectangles. Namely, it is only defined between 
    windows and other rectangles. See the [Window](windows.alm.md) module for axioms.
    ```
    distance : rectangles x rectangles x integers -> booleans
    ```
1. Closest

    A rectangle A is said to be closest to rectangle B if A is closer to B than every
    other rectangle.
   ```
   closest : rectangles x rectangles -> booleans
   ```

1. Nearest Side

    A window has a nearest side to some other rectangle w.r.t. a particular direciton.
    Nearest side is defined differently for different kinds of rectangles, so axioms will be given in later modules.

    ```
    nearestSide : windows x rectangles x directions -> booleans
    ```

1. Nearest Corner

    Each of the 4 corners of a rectangle can be identified by two directions, e.g. the top-left corner
    or the bottom-right corner (note: the tuples "bottom-right" and "right-bottom" identify the same
    corner). The nearest corner of a rectangle A to some other rectangle B is
    the corner of A with the shortest measure to some corner of B. The nearest corner is undefined if multiple corners are equally close.

    ```
    nearestCorner : rectangles x rectangles x directions x directions -> booleans
    ```

## Axioms

1. Distance

    The distance between a window and another rectangle is different depending on 
    whether the other rectangle is a window or a monitor.

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
           #min { | E1 - E2 | :
                    side(A, Dir) = E1,
                    side(B, Dir') = E2,
                    opposite(Dir, Dir') }
                 = D.
       ```
       The distance between two windows is symmetric.

       ```
        distance(A, B) if
            distance(B, A),
            instance(A, windows).
        ```
    2. Between a window and a monitor:
        The distance between a window W and monitor M is the smallest 
        measure between like sides of W and M (e.g. the measure from top of W
        to top of M).
        ```
        distance(W, M, D) if
            instance(A, windows),
            instance(M, monitors),
            #min { |E1 - E2| :
                     side(A, D) = E1,
                     side(B, D) = E2 }
                = D,
        ```
    Distance is a symmetric property.
    ```
    distance(A, B) if distance(B, A).
    ```
3. Nearest Corner

    The nearest corner of rectangle A to rectangle B is (Dir, Dir') where:

    - The nearest side of A to B is Dir.
    - The axis of Dir' is opposite the axis of Dir.
    - The opposite direction of Dir' is Dir''.
    - The absolute difference between the Dir' side of A and the Dir' side of B
        is less than the absolute difference between the Dir'' side of A and the Dir''
        side of B.
    
    ```
    nearestCorner(A, B, Dir, Dir') if
        nearestSide(A, B, Dir),
        axis(Dir) = AX,
        axis(Dir') = AX'
        oppositeAxis(AX) = AX',
        oppositeDirection(Dir') = Dir'',
        side(A, Dir', A1),
        side(B, Dir', B1),
        side(A, Dir'', A2),
        side(B, Dir'', B2),
        | A1 - B1 | < | A2 - B2|
    ```
    The order of the directions is interchangeable.
    ```
    nearestCorner(A, B, Dir, Dir') if nearestCorner(A, B, Dir', Dir).
    ```
6. Rectangle A is closest to rectangle B if the distance between A and B is the minimum distance between A and any window (closest is not symmetric).

    ```
    closest(A, B) if
        distance(A, B, D),
        #count { C : distance(C, B, D'), D' < D } = 0.
    ```
