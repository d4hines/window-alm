# window-alm
Description of window movement in the ALM action language.

## Sorts and Function Declarations

### Sorts            
1. Windows

   Windows are rectangular user interfaces that exist on the desktop.

    ```
    windows :: universe
    ```
2. Directions

   Direcitons is a set comprised of the atoms left, right, top, and bottom.

    ```
    directions :: { left, right, top, bottom }
    ```
3. Axes

   Axes is a set comprised of the atoms x and y.

    ```
    axes :: { x, y }
    ```
4. Move

   Move is an action described by a mover, direction, and distance.

    ```
    move :: actions
       attributes
            target : windows
            distance : integers
            direction : directions

    ```
### Statics
1. Opposite Directions
    Left is opposite right, and vice versa. Top is opposite bottom, and vice versa.
    ```
    oppositeDirection : directions -> directions.

    oppositeDirection(left) = right.
    oppositeDirection(top) = bottom.
    oppsoiteDirection(A) = B if oppositeDirection(B) = A.
    ```

1. Opposite Axes
   The x axis is opposite the y axis, and vice versa.
   ```
   oppositeAxis : axes -> axes
   oppositeAxis(x) = y.
   oppositeAxis(y) = x.
   ```
1. Directions and the Cartesian grid.
    In operating systems, the origin of the desktop space is defined as the top left corner of the primary
    monitor. The Y axis increases as you move down, and the X axis increases as you move right. We represent
    this fact by assigning each direction a factor (1 or -1 to represent postive or negative motion on the axis).
    ```
    directionFactor : directions -> integers

    directionFactor(bottom) = 1.
    directionFactor(right) = 1.
    directionFactor(top) = -1.
    directionFactor(left) = -1.
    ```

### Fluents
#### Basic
1. Width

    Every window has a width.
    ```
    total width : windows -> integers
    ```
1. Height

    Every window has a height.
    ```
    total height : windows -> integers
    ```
1. Coordinates:

    A window has two coordinates, X, and Y, which together locate the top
    left corner of the window. 
    ```
    total coordinate : windows x axes -> integers
    ```
#### Defined
1. Final Coordinates

    A window's final coordinates identify its coordinates after snapping is applied.
    
    ```
    finalCoordinate : windows x axes x integers -> booleans
    ``` 
1. Edge

    A window has an edge associated with each direction. The value of the edge is the placement
    of its line on its respective axis.
    ```
    edge : windows x directions x integers -> booleans.
    ```
1. Snap

    A window may snap to another window after movement.
    ```
    snap : windows x windows -> booleans
    ```
1. Attracts

    Two windows attract each other when they are in close proximity.
    ```
    attracts : windows x windows x directions -> booleans
    ```
1. Nearest Side

    A window has a nearest side to some other window w.r.t. a particular direciton.
    ```
    nearestSide : windows x windows x directions -> booleans
    ```
1. Distance

    ```
    distance : windows x windows x integers -> booleans
    ```
1. On Same Line

    ```
    onSameLine : windows x windows x axes -> booleans
    ```
1. Overlaps

    ```
    overlaps : windows x windows -> booleans
    ```
1. Closest
    One or more windows may be closest to a given window if there are no other closer windows.
    ```
    closest : windows x windows -> booleans
    ```
1. Moving and Stationary

    When a user moves a window, it becomes moving. All other windows become stationary.
    ```
    moving : windows -> booleans
    stationary : windows -> booleans
    ```
## Axioms

### Causal Laws
1. Moving a window causes the window's MovedToCoordiantes to equal its current coordinates offset by the distance and in the direction of the move.
    ```
    occurs(Action) causes coordinate(Window, Axis, NewCoord) if
        instance(Action, move),
        target(Action) = Window,
        distance(Action) = D,
        direction(Action) = Dir,
        axis(Dir) = Axis,
        directionFactor(Dir) = F,
        coordinate(Window, Axis) = Coord,
        Coord + (D * F) = NewCoord
    ```

1.  Dragging a window causes it to become moving.

    ```
    occurs(Action) causes moving(Window) if
        target(Action) = Window.
    ```
1. Dragging a window causes every other window to become stationary (i.e, not moving).

    ```
    occurs(Action) causes -moving(Window) if
        target(Action) = TargetWindow,
        Window != TargetWindow.
    ``` 

### Defined Fluent Axioms
1. Window A snaps to Window B if:
    - A is moving.
    - B attracts A along direction D.
    - Nothing attracts A in some other direction.
    ```
    snaps(A, B) if
        moving(A),
        attracts(B, A, D),
        #count { C : attracts(C, A, D'),  D' != D } = 0
    ```
1. The final coordinates of window A are equal to the coordinates of A moved by the distance between A and B and in the direction of the nearest side of A to be if A snaps to B.
    ```
    finalCoordinates(A, Axis, NewCoord) if
        snaps(A, B),
        coordinate(A, Axis) = Coord,
        distance(A,B,D),
        nearestSide(A, B, Dir),
        directionFactor(Dir) = F,
        Coord + D * F = NewCoord.
    ```
1. A window's final coordinates are equal to its coordinates if it does not snap to any other window.
    ```
    finalCoordinates(Window, Axis, Coord) if
        coordinate(Window, Axis) = Coord,
        #count { Other : snap(Window, Other) } = 0.
    ```

1. The distance between A and B is the smallest measure between opposite edges of A and B (e.g. the measure from the top edge of A to the bottom edge of B, etc.), where:
    - A is on the same line as B.
    - A does not overlap B.
    - (distance is symmetric)
    ```
    distance(A, B) if
        onSameLine(A, B),
        -overlap(A, B),
        #min { E1 - E2 : edge(A, D) = E1, edge(B, O) = E2, opposite(D, O), E1 > E2 } = D,
    ```
1. The left edge of a window is equal to its final X coordinate, and top edge to its y Coordinate.
    ```
    edge(Window, left, Value) if finalCoordinate(Window, x, Value).
    edge(Window, top, Value) if finalCoordinate(Window, y, Value).
    ```
1. The right of a window is equal to its left edge plus its width. Likewise, the bottom edge is equal to
the top edge plus its height.

    ```
    edge(Window, right, RValue) if
        edge(Window, left, LValue),
        width(Window) = W,
        LValue + W = RValue.

    edge(Window, right, RValue) if
        edge(Window, left, LValue),
        width(Window) = W,
        LValue + W = RValue.
    ```
1. Window A attracts window B in direction D if:
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
1. Window A is closest to B if the distance between A and B is the minimum distance between A and any window (closest is not symmetric).

    ```
    closest(A, B) if
        distance(A, B, D),
        #count { C : distance(C, B, D'), D' < D } = 0.
    ```
1. A is on the same line as B w.r.t an axis AX if there is a line on AX that intersects both A and B. (on the same line is symmetric).

    ```
    onSameLine(A, B, Axis) if
        edge(A, Direction) = A1,
        oppositeDirection(Direction) = Opposite
        edge(A, Opposite) = A2,
        edge(B, Direction) = B1,
        edge(B, Opposite) = B2,
        A != B,
        A1 < B2,
        A2 > B1.

    onSameLine(A, B, Axis) if onSameLine(B, A, Axis).
    ```

1. A overlaps B if A is on the same line as B w.r.t to both x and y.

    ```
    overlaps(A, B) if onSameLine(A, B, x), onSameLine(A, B, y).
    ```

1. The nearest side of A to B is the direction D where the measure between edge D of A and the opposite edge of B is equal to the distance between A and B.

    ```
    nearestSide(A, B, Dir) if
        edge(A, Dir, EdgeA)
        oppositeDirection(Dir) = O
        edge(B, O, EdgeB),
        distance() = D
        EdgeA - EdgeB = 
    ```
    In the below picture, the nearest side of A to B is right, while the nearest side of B to A is left.
    ![](https://i.imgur.com/zYOB0rS.png)



