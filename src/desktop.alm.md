# Module: Desktop

## Overview
### The Desktop Plane
Finsemble exists on a desktop, a virtual unbounded Cartesian plane 
reﬂected on the X axis (such that the Y axis increases as you move 
“down” the screen). The plane is divided into discrete pixels, which 
are the smallest unit of display. Since pixels are the only unit
of measure in this book, it is the assumed unit for all integer
values given unless otherwise speciﬁed.

The origin of the plane is defined to be the top-left corner of the primary monitor. See
[Monitors](./monitors.alm.md) for more detail.

### The Mouse
The primary means by which a user interacts with the desktop user is via the mouse, a pointer which may be moved around the
desktop.

Currently the mouse has no direct representation in the system - rather, clicks
and moves are captured indirectly through other, higher-level actions.

## Sorts
### Objects
1. Axes

   The desktop has two axes, X, and Y.
   ```
   axes :: { x, y }
   ```
1. Directions

    The two axes form four directions: top, bottom, left, and right.

    ```
    directions :: { left, right, top, bottom }
    ```

## Functions

### Statics

1. Opposite Directions

    Each direction has an opposite.
    ```
    oppositeDirection : directions -> directions.
    ```

1. Opposite Axes

   Each axis has an opposite.
   ```
   oppositeAxis : axes -> axes
   ```
1. Directions and Axes

    Each direction has a corresponding axis.
    ```
    axis : direction -> axes
    ```

1. Directions and the Desktop Cartesian grid.

    In operating systems, the origin of the desktop space is defined as the top left corner of
    the primary monitor. The Y axis increases as you move down, and the X axis increases as you
    move right. We represent this fact by assigning each direction a factor (1 or -1 to represent postive or negative motion on the axis).
    ```
    directionFactor : directions -> integers
    ```

### Fluents 
#### Basic
1. The Primary Monitor

    An arbitrary monitor is chosen by the user to be the primary monitor. The top left corner
    of the primary monitor is defined to be the origin of desktop plane. All other monitors are
    termed secondary monitors. 
    ```
    primaryMonitor : monitors
    ``` 
    
## Axioms 

We can now give values for the static functions. Because these are always true, we include
them as axioms, rather than as configuration.

 1. Opposite Directions

    Left is opposite right, and vice versa. Top is opposite bottom, and vice versa.
    ```
    oppositeDirection(left) = right.
    oppositeDirection(top) = bottom.
    oppositeDirection(A) = B if oppositeDirection(B) = A.
    ```

1. Opposite Axes

   The x axis is opposite the y axis, and vice versa.
   ```
   oppositeAxis(x) = y.
   oppositeAxis(y) = x.
   ```
1. Directions and Axes

    Left and right correspond to the X axis, while the top and bottom correspond to the Y axis.
    ```
    axis(left) = x.
    axis(top) = y.
    axis(A, B) if axis(B, A), oppositeDirection(A, B).
    ```

1. Directions and the Desktop Cartesian grid.
    The value of the X and Y axis increase as you move right and down respectively,
    and decrease as you move left and top.

    ```
    directionFactor(right) = 1.
    directionFactor(bottom) = 1.
    directionFactor(left) = -1.
    directionFactor(top) = -1.
    ```
