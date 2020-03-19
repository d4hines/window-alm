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
6. Rectangle A is closest to rectangle B if the distance between A and B is the minimum distance between A and any window (closest is not symmetric).

    ```
    closest(A, B) if
        distance(A, B, D),
        #count { C : distance(C, B, D'), D' < D } = 0.
    ```

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




## random

closestWindow : windows x windows x booleans
closestGroup : groups x groups x booleans
change definition of canSnap to use the groups x groups version of closest

resize simply uses canSnap as well.

## Highlighting ALM

Keywords

    - if
    - causes
    - total
    - partial
    - ~x~ (x is technically an operator)

Operators

    - :
    - ::
    - x
    - ->
    - ,
    - +
    - -
    - =
    - *
    - /
    - %
    - <=
    - >=
    - <
    - >
    - #min
    - #max
    - #count

Comments
    - //
