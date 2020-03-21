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
    ```
    total width : rectangles -> integers
    ```
1. Height
    ```
    total height : rectangles -> integers
    ```
1. Coordinates:

    A rectangle has two coordinates, X, and Y, which together locate its top
    left corner on the desktop plane.
    ```
    total coordinate : rectangles x axes -> integers
    ```
#### Defined
1. Side

    Each side of a rectangle is represented by a direction and an integer value indicating its placement on its respective asxis.
    ```
    side : rectangles x directions x integers -> booleans.
    ```
1. On Same Line

    Two rectangles are on the same line w.r.t. an axis if there is a line perpendicular to that axis that crosses both rectangles.

    ```
    onSameLine : rectangles x rectangles x axes -> booleans
    ```
1. Overlaps

    ```
    overlaps : rectangles x rectangles -> booleans
    ```

## Axioms
1. Sides of a rectangle.
   1. The value of the left side of a rectangle is equal to its X coordinate, and value of the top side to its y Coordinate.
        ```
        side(Rectangle, left, Value) if coordinate(Rectangle, x) = Value.
        side(Rectangle, top, Value) if coordinate(Rectangle, y) = Value.
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
