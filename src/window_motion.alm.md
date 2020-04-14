# Module: Window Motion

## Overview
## Sorts

1. Move
   Move is an action described by a moving window, direction, and distance.

    ```
    move :: actions
       attributes
            moving_window : windows
            direction : directions
            distance : integers

    ```
## Functions
### Fluents
#### Basic
#### Defined
1. Attracts

    Two rectangles attract each other when they are in close proximity.
    ```
    attracts : rectangles x rectangles x directions -> booleans
    ```
1. Final Coordinates

    A window's final coordinates identify its coordinates after special conditions (if any) are applied.
    
    ```
    finalCoordinate : windows x axes x integers -> booleans
    ``` 
1. Snapped

    A window may snap to another rectangle after movement.
    ```
    snapped : windows x rectangles -> booleans
    ```
1. Snapped to Corner

    As a special case of snapping, a window may snap to the corner of another rectangle after movement.
    ```
    snappedToCorner : windows x rectangles -> booleans
    ```

1. Moving and Stationary

    When a user moves a window, it becomes moving. All other windows become stationary.
    ```
    moving : windows -> booleans
    stationary : windows -> booleans
    ```

## Axioms
1. Nearest Side
   1. Between windows:
    The nearest side of window A to window B is the direction D where the measure between
    side D of A and the opposite side of B is equal to the distance between A and B.

        ```
        nearestSide(A, B, Dir) if
            instance(A, windows),
            instance(B, windows),
            side(A, Dir, EdgeA),
            oppositeDirection(Dir) = Dir',
            side(B, Dir', EdgeB),
            distance(A, B) = D,
            EdgeA - EdgeB = D.
        ```

    In the below picture, the nearest side of A to B is right, while the nearest side of B to A is left.
    ![](https://i.imgur.com/zYOB0rS.png)

   2. Between a window and a monitor

    The nearest side of window W to monitor M is the direction D where the measure between
    side D of W and side D of M is equal to the distance between W and M.

    ```
    nearestSide(W, M, Dir) if
        instance(W, windows),
        instance(M, monitors),
        side(W, Dir, EdgeW),
        side(M, Dir, EdgeM),
        distance(W, M) = D,
        EdgeW - Edge = D.
    ```
    
    For both monitors and windows, the nearest side relation is symmetric about opposite directions:
    ```
    nearestSide(A, B, Dir) if
        oppositeDirection(Dir) = Dir',
        nearesetSide(B, A, Dir').
    ```

   The nearest side relation is undefined between two monitors.

### Causal Laws
1. Moving a window causes the window's coordinates to equal its current coordinates moved by the distance and in the direction of the move.
    ```
    occurs(Action) causes coordinate(Window, Axis, NewCoord) if
        instance(Action, move),
        [movingObject or synonym](Action) = Window,
        distance(Action) = D,
        direction(Action) = Dir,
        axis(Dir) = Axis,
        directionFactor(Dir) = F,
        coordinate(Window, Axis, Coord),
        Coord + (D * F) = NewCoord
    ```

1.  Dragging a window causes it to become moving.

    ```
    occurs(Action) causes moving(Window) if
        instance(Action, move),
        mover(Action) = Window.
    ```
1. Dragging a window causes every other window to become stationary (i.e, not moving).

    ```
    occurs(Action) causes -moving(Window) if
        instance(Action, move),
        mover(Action) = MovingWindow,
        Window != MovingWindow.
    ``` 
 ### Defined Fluent Axioms
1. Window A is snapped to rectangle B if:
    - A is moving.
    - B attracts A along direction D.
    - Nothing attracts A in some other direction.
    ```
    snapped(A, B) if
        moving(A),
        attracts(B, A, Dir),
        #count { C : attracts(C, A, Dir'),  Dir' != Dir } = 0
    ```
1. Window A is snapped to the corner of rectangle B if:
    - A is snapped to B.
    - The nearest side of A to B is Dir
    - The nearest corner of A to B is (Dir, Dir'), where Dir' is orthogonal to Dir (see
    definition of nearest corner).
    - This distance between the Dir' side of A and the Dir' side of B is less than the corner snapping threshold.

    ```
    snappedToCorner(A, AX', NewCoord) if
            snapped(A, B),
            nearestSide(A, B, Dir),
            axis(Dir) = AX,
            oppositeAxis(AX) = AX'
            nearestCorner(A, B, Dir, Dir'),
            side(A, Dir') = SideA,
            side(B, Dir') = SideB,
            |SideB - SideA| < corner_snapping_threshold.
    ```
1. Snapping and Final Coordinates

   The final coordinate of window A w.r.t axis AX equals the like coordinate of A moved by
   the distance D in direction Dir, where:
      - A is snapped to B.
      - The distance between A and B is D.
      - The nearest side of A to B is Dir.

        ```
        finalCoordinate(A, AX, NewCoord) if
            snapped(A, B),
            distance(A, B, D),
            nearestSide(A, B, Dir),
            axis(Dir) = AX,
            coordinate(A, AX) = Coord,
            directionFactor(Dir) = F,
            Coord + D * F = NewCoord.
        ```
    The final coordinate of the other axis AX' depends on whether A is snapped to the
    corner of B:
    1. The final coordinate of A w.r.t. axis AX' are moved by the distance and in the direction
        of the nearest corner of A to B if A is snapped to the corner of B.
        ```
        finalCoordinate(A, AX', NewCoord) if
            snapped(A, B),
            snappedToCorner(A, B),
            nearestSide(A, B, Dir),
            axis(Dir) = AX,
            oppositeAxis(AX) = AX'
            nearestCorner(A, B, Dir, Dir'),
            coordinate(A, AX') = Coord,
            side(A, Dir') = SideA,
            side(B, Dir') = SideB,
            Coord + (SideB - SideA) = NewCoord
        ```
    
    1. The final coordinates of A w.r.t axis AX' remain unchanged if A does not snap to the corner of B.
        ```
        finalCoordinate(A, AX', Coord) if
            snapped(A, B),
            -snappedToCorner(A, B),
            nearestSide(A, B, Dir),
            axis(Dir) = AX,
            oppositeAxis(Axis) = AX'
            coordinate(A, AX', Coord).
        ```
2. A window's final coordinates are equal to its coordinates if it does not snap to any other rectangle.
    ```
    finalCoordinate(Window, Axis, Coord) if
        coordinate(Window, Axis) = Coord,
        #count { Other : snapped(Window, Other) } = 0.
    ```

5. Rectangle A attracts window B in direction D if:
    - The distance between A and B is less than the snapping threshold.
    - A is closest to B
    - The nearest side of B to A is D

    ```
    attracts(A, B, D) if
        distance(A, B) > snapping_threshold,
        closest(A, B),
        nearestSide(B, A, D).
    ```
    In the below picture, A attracts B left, while B attracts A right:
    ![](https://i.imgur.com/zYOB0rS.png)


9.  Nearest Side
    1. Between windows:

        The nearest side of window A to window B is the direction D where the measure between
        side D of A and the opposite side of B is equal to the distance between A and B.

        ```
        nearestSide(A, B, Dir) if
            instance(A, windows),
            instance(B, windows),
            side(A, Dir, EdgeA),
            oppositeDirection(Dir) = Dir',
            side(B, Dir', EdgeB),
            distance(A, B) = D,
            EdgeA - EdgeB = D.
        
        
        ```

        In the below picture, the nearest side of A to B is right, while the nearest side of B to A is left.
        ![](https://i.imgur.com/zYOB0rS.png)

    2. Between a window and a monitor

        The nearest side of window W to monitor M is the direction D where the measure between
        side D of W and side D of M is equal to the distance between W and M.

        ```
        nearestSide(W, M, Dir) if
            instance(W, windows),
            instance(M, monitors),
            side(W, Dir, EdgeW),
            side(M, Dir, EdgeM),
            distance(W, M) = D,
            EdgeW - Edge = D.
        ```
    
    For both monitors and windows, the nearest side relation is symmetric about opposite directions:
    ```
    nearestSide(A, B, Dir) if
        oppositeDirection(Dir) = Dir',
        nearesetSide(B, A, Dir').
    ```

    The nearest side relation is undefined between two monitors.

