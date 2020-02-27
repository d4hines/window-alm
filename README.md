# window-alm
Description of window movement in the ALM action language.

Highlighted text represents ALM code.

```
theory windows
    module movement
```
## Sorts

### Sorts            
```
sort declarations
```
1. Rectangles

    Rectangles exist on the Cartesian plane defined by the desktop.
    ```
    rectangles :: universe
    ```
1. Windows

   Windows are rectangles that provide user interfaces into applications running on the desktop.

    ```
    windows :: rectangles
    ```
1. Monitors

    Monitors are rectangular views into the desktop. A window is visible only when it overlaps with or is contained by a monitor.

    ```
    monitors :: rectangles
    ```
1. Directions

   Direcitons is a set comprised of the atoms left, right, top, and bottom.

    ```
    directions :: { left, right, top, bottom }
    ```
1. Axes

   Axes is a set comprised of the atoms x and y.

    ```
    axes :: { x, y }
    ```
1. Move

   Move is an action described by a mover, direction, and distance.

    ```
    move :: actions
       attributes
            mover : windows
            direction : directions
            distance : integers

    ```
## Functions
```
function declarations
```
### Statics
(I've also included the values of the statics here as well).
1. Opposite Directions

    Left is opposite right, and vice versa. Top is opposite bottom, and vice versa.
    ```
    oppositeDirection : directions -> directions.

    oppositeDirection(left) = right.
    oppositeDirection(top) = bottom.
    oppositeDirection(A) = B if oppositeDirection(B) = A.
    ```

1. Opposite Axes

   The x axis is opposite the y axis, and vice versa.
   ```
   oppositeAxis : axes -> axes
   
   oppositeAxis(x) = y.
   oppositeAxis(y) = x.
   ```
1. Directions and Axes

    Left and right correspond to the X axis, while the top and bottom correspond to the Y axis.
    ```
    axis : direction -> axes

    axis(left) = x.
    axis(right) = x.
    axis(top) = y.
    axis(bottom) = y.
    ```

1. Directions and the Desktop Cartesian grid.

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
```
fluents
```
#### Basic
```
basic
```
1. Width

    Every rectangle has a width.
    ```
    total width : rectangles -> integers
    ```
1. Height

    Every rectangle has a height.
    ```
    total height : rectangles -> integers
    ```
1. Coordinates:

    A rectangle has two coordinates, X, and Y, which together locate its top
    left corner.
    ```
    total coordinate : rectangles x axes -> integers
    ```
#### Defined
1. Side

    Each side of a rectangle is represented by a direction and an integer value indicating its placement on its respective asxis.
    ```
    side : rectangle x directions x integers -> booleans.
    ```
1. Attracts

    Two rectangles attract each other when they are in close proximity.
    ```
    attracts : rectangles x rectangles x directions -> booleans
    ```
1. Nearest Side

    A rectangle has a nearest side to some other rectangle w.r.t. a particular direciton.
    ```
    nearestSide : rectangles x rectangles x directions -> booleans
    ```
1. Nearest Corner

    Each of the 4 corners of a rectangle can be identified by two directions, e.g. the top-left corner
    or the bottom-right corner (note: the tuples "bottom-right" and "right-bottom" identify the same
    corner). The nearest corner of a rectangle A to some other rectangle B is
    the corner of A with the shortest measure to some corner of B. The nearest corner is undefined if multiple corners are equally close.

    ```
    nearestCorner : rectangles x rectangles x directions x directions -> booleans
    ```

1. Distance

    ```
    distance : rectangles x rectangles x integers -> booleans
    ```
1. On Same Line

    Two windows are on the same line w.r.t. an axis if there is a line on that axis that crosses both windows.

    ```
    onSameLine : windows x windows x axes -> booleans
    ```
1. Overlaps

    ```
    overlaps : rectangles x rectangles -> booleans
    ```
1. Closest
    One or more rectangles may be closest to a given rectangle if there are no other closer rectangles.
    ```
    closest : rectangles x rectangles -> booleans
    ```
1. Final Coordinates

    A window's final coordinates identify its coordinates after special conditions (if any) are applied.
    
    ```
    finalCoordinate : windows x axes x integers -> booleans
    ``` 
1. Snaps

    A window may snap to another rectangle after movement.
    ```
    snaps : windows x rectangles -> booleans
    ```
1. Snaps to Corner

    As a special case of snapping, a window may snap to the corner of another rectangle after movement.
    ```
    snapsCorner : windows x rectangles -> booleans
    ```

1. Moving and Stationary

    When a user moves a window, it becomes moving. All other windows become stationary.
    ```
    moving : windows -> booleans
    stationary : windows -> booleans
    ```
## Axioms

### Causal Laws
1. Moving a window causes the window's coordinates to equal its current coordinates moved by the distance and in the direction of the move.
    ```
    occurs(Action) causes coordinate(Window, Axis, NewCoord) if
        instance(Action, move),
        mover(Action) = Window,
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
        mover(Action) = Window.
    ```
1. Dragging a window causes every other window to become stationary (i.e, not moving).

    ```
    occurs(Action) causes -moving(Window) if
        mover(Action) = MovingWindow,
        Window != MovingWindow.
    ``` 

### Defined Fluent Axioms
1. Window A snaps to rectangle B if:
    - A is moving.
    - B attracts A along direction D.
    - Nothing attracts A in some other direction.
    ```
    snaps(A, B) if
        moving(A),
        attracts(B, A, Dir),
        #count { C : attracts(C, A, Dir'),  Dir' != Dir } = 0
    ```
1. Window A snaps to the corner of rectangle B if:
    - A snaps to B.
    - The nearest side of A to B is Dir
    - The nearest corner of A to B is (Dir, Dir'), where Dir' is orthogonal to Dir (see
    definition of nearest corner).
    - This distance between the Dir' side of A and the Dir' side of B is less than the corner snapping threshold.

    ```
    snapsCorner(A, AX', NewCoord) if
            snaps(A, B),
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
      - A snaps to B.
      - The distance between A and B is D.
      - The nearest side of A to B.

        ```
        finalCoordinate(A, AX, NewCoord) if
            snaps(A, B),
            distance(A, B, D),
            nearestSide(A, B, Dir),
            axis(Dir) = AX,
            coordinate(A, AX) = Coord,
            directionFactor(Dir) = F,
            Coord + D * F = NewCoord.
        ```
    The final coordinate of the other axis AX' depends on whether A snaps to the corner of B:
    1. The final coordinate of A w.r.t. axis AX' are moved by the distance and in the direction
        of the nearest corner of A to B if A snaps to the corner of B.
        ```
        finalCoordinate(A, AX', NewCoord) if
            snaps(A, B),
            snapsCorner(A, B),
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
            snaps(A, B),
            -snapsCorner(A, B),
            nearestSide(A, B, Dir),
            axis(Dir) = AX,
            oppositeAxis(Axis) = AX'
            coordinate(A, AX', Coord).
        ```
2. A window's final coordinates are equal to its coordinates if it does not snap to any other rectangle.
    ```
    finalCoordinate(Window, Axis, Coord) if
        coordinate(Window, Axis) = Coord,
        #count { Other : snaps(Window, Other) } = 0.
    ```

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
4. Sides of a rectangle.
   1. The value of the left side of a rectangle is equal to its X coordinate, and value of the top side to its y Coordinate.
        ```
        side(Rectangle, left, Value) if coordinate(Rectangle, x, Value).
        side(Rectangle, top, Value) if coordinate(Rectangle, y, Value).
        ```
   2. The value of the right side of a rectangle is equal to the value its left side plus its width. 
        Likewise, the value of the bottom side is equal to the value of its top side plus its height.

        ```
        side(Rectangle, right, RValue) if
            side(Rectangle, left, LValue),
            width(Rectangle) = W,
            LValue + W = RValue.

        side(Rectangle, right, RValue) if
            side(Rectangle, left, LValue),
            width(Rectangle) = W,
            LValue + W = RValue.
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
7. Window A is on the same line as window B w.r.t an axis AX if there is a line on AX that intersects both A and B. (on the same line is symmetric property).

    ```
    onSameLine(A, B, Axis) if
        side(A, Dir) = A1,
        oppositeDirection(Dir) = Dir'
        side(A, Dir') = A2,
        side(B, Dir) = B1,
        side(B, Dir') = B2,
        A != B,
        A1 < B2,
        A2 > B1.
    ```

8.  A overlaps B if A is on the same line as B w.r.t to both x and y.

    ```
    overlaps(A, B) if onSameLine(A, B, x), onSameLine(A, B, y).
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

10. Nearest Corner

    The nearest corner of rectangle A to rectangle B is (Dir, Dir') where:

    - Dir is the nearest side of A to B.
    - The axis of Dir' is opposite the axis of Dir.
    - The absolute difference between the Dir' side of A and the Dir' side of B
        is less than the absolute difference between the opposite side of A and the opposite
        side of B.
    
    ```
    nearestCorner(A,B, Dir, Dir') if
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
