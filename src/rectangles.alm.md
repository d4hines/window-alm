# Module: Rectangles

## Overview
Many objects in Finsemble are rectangles. We'll now describe the basic properties of rectangles
in ALM for use in later modules.

## Imports
[Desktop](./desktop.alm.md)

## Sorts
1. Rectangles

    Rectangles exist on the Cartesian plane defined by the desktop.
    ```
    rectangles :: universe
    ```
    
## Functions

### Fluents

#### Basic

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
### Defined
1. Side

    Each side of a rectangle is represented by a direction and an integer value indicating its placement on its respective asxis.
    ```
    side : rectangle x directions x integers -> booleans.
    ```
1. Nearest Side

    A rectangle has a nearest side to some other rectangle w.r.t. a particular direciton.
    Nearest side is defined differently for different kinds of rectangles, so axioms will be given in later modules.

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

    Distance is the measure between the nearest sides of two rectangles. Distance is defined differently
    for different kinds of rectangles, so axioms will be given in later modules.
    ```
    distance : rectangles x rectangles x integers -> booleans
    ```
1. On Same Line

    Two rectangles are on the same line w.r.t. an axis if there is a line perpendicular to that axis that crosses both rectangles.

    ```
    onSameLine : rectangles x rectangles x axes -> booleans
    ```

## Axioms
1. Sides of a rectangle.
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
1. Rectangle A is on the same line as rectangle B w.r.t an axis AX if there is
    a line on AX that intersects both A and B. This relation is symmetric.

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

2.  A overlaps B if A is on the same line as B w.r.t to both x and y.

    ```
    overlaps(A, B) if onSameLine(A, B, x), onSameLine(A, B, y).
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
