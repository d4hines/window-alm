# Module: Stacks of Windows

## Overview

Users may "stack" windows on top of each other. This causes multiple tabs
to display in a single window, akin to multiple tabs in a single internet
browser window.

Users create stacks by dragging the tab of one window and dropping it
onto the tab region of another window. Likewise, users may remove
a window from a stack by dropping its tab onto the desktop. Exactly one
window in the stack is said to be the "active tab". Only the active tab is visible.
The active tab provides controls to switch which window is the active tab.

Stacks have no representation as objects in the system. Instead, they are
represented by the "stacked onto" relation.

## Imports

[Windows](./windows.alm.md)

## Sorts
1. Close Stack

    The user can close a stack by clicking the "X" button on the
    top right of the window. This closes the clicked window, as well
    as every other window in the same stack as the clicked window.
    ```
    close_stack :: close_window
        attributes
            target : windows
    ```
1. Drop Tab

    The user may drop the tab of a window onto the desktop. The effect
    of this action depends on what the tab is dropped onto.
    ```
    drop_tab :: actions
        attributes
            source : windows
    ```

1. Stack Onto

    The user can stack a window A onto another window B by dropping
    the tab of A onto the tab of B.
    
    ```
    stack_onto :: drop_tab
        attributes
            source : windows
            target : windows
    ```

## Functions
### Fluents
#### Basic
1. Stacked On

    A window may be stacked onto at most one other window.

    ```
    stacked_onto : windows -> windows
    ```

#### Defined
1. Stacked Above

    A window is stacked above another window if it is stacked
    on that window (directly or transitively).

    ```
    stacked_above : windows x windows -> booleans
    ```


1. Stack Root

    A window is said to be the root of its stack if
    it is not stacked onto any other window.
    
    ```
    stack_root : windows -> booleans
    ```

1. Stack Index

    Stacks can be interpreted as an array of windows.
    Each window in the stack has an index in that array,
    where the root has index 0, the window stacked onto the
    root has index 1, etc.

    ```
    stack_index : windows -> integers
    ```

1.  In Same Stack

    Two windows are in the same stack if they are stacked above
    the same root. Additionally, every window is in the same stack as itself.
    In Same Stack forms a partitioning of the windows sort, being reflexive,
    symmetric, and transitive.

    ```
    in_same_stack : windows x windows -> booleans
    ``` 

## Axioms

1. Stacked Above.

    ```
    stacked_above(A, B) if stacked_onto(A, B).
    ```

    Stacked above is transitive.
    ```
    stacked_above(A, B) if stacked_above(A, C), stacked_above(C, B).
    ```

1. Stacked Root

    A window is a stack root if it is not stacked onto any other window.

    ```
    stack_root(A) if
        instance(A, windows),
        #count { B : stacked_onto(A, B) } = 0.
    ```

1. Stack Index

    The root of a stack has index 0.
    ```
    stack_index(A, 0) if stack_root(A).
    ```

    Every other window has an index one more than the window it is stacked on.
    ```
    stack_index(A, I + 1) if
        stack_index(B, I),
        stacked_onto(A, B).
    ```

1. In Same Stack

   A root is in the same stack as every window above it.
   ```
   in_same_stack(A, B) if stack_root(A), stacked_above(A, B).
   ```

   In same stack is reflexive, symmetric, and transitive.
   ```
   in_same_stack(A, A) if instance(A, windows).
   in_same_stack(A, B) if stacked_with(B, A).
   in_same_stack(A, B) if stacked_with(A, C), stacked_with(C, B).
   ```

1. Closing a Stack

    ```
    occurs(Action) causes -visible(W') if
        instance(Action, close_stack),
        target(Action) = W,
        in_same_stack(W, W').
    ```
1. Dropping a Tab

    Dropping a tab somewhere on t
    that tab from
    If window A is stacked onto B, stacking A onto some other
    window causes A to no longer be stacked onto B.
   ``` 

1. Stack Onto

    It is impossible for a window to stack onto itself.
    ```
    impossible occurs(Action) if 
        instance(Action, stack_onto),
        source(Action) = A,
        target(Action) = A.
    ```
    It is also impossible for a window to stack onto the
    window on which it is already stacked.
    ```
    impossible occurs(Action) if 
        instance(Action, stack_onto),
        source(Action) = A,
        target(Action) = B,
        stacked_on(A, B).
    ```

    Stacking a window A onto B causes A to become
    stacked onto B.

    ```
    occurs(Action) causes stacked_onto(A, B) if
        instance(Action, stack_onto).
        source(Action) = A,
        target(Action) = B.
    ```

    If window A is stacked onto B, stacking A onto some other
    window causes A to no longer be stacked onto B.
    ```
    occurs(Action) causes -stacked_onto(A, B) if
        instance(Action, stack_onto),
        target(Action) = A,
        stacked_onto(A, B).
    ```

    Stacking a window onto another "inserts" that
    window into the target's array. That is,
    stacking A onto B causes C to become
    stacked onto A if C was stacked onto B.

    ```
    occurs(Action) causes stacked_onto(C, A) if
        instance(Action, stack_onto).
        source(Action) = A,
        target(Action) = B,
        stacked_onto(C, B).
    ```

    Stacking a window causes the source's array to "shift" down.
    That is, stacking window A onto some other window causes
    B to become stacked onto C if: 
    - A is stacked onto C,
    - B is stacked onto A.
    ```
    occurs(Action) causes stacked_onto(B, C) if
        instance(Action, stack_onto),
        stacked_on(A, C),
        stacked_on(B, A)
    ```

    **Note**: Because, when stacking window A onto window B, A is inserted "above"
    B, it's therefore impossible for users to prepend to the stack, i.e  move
    a tab to the very beginning of the stack in a single operation. If we inserted A
    "below" B, this would allow users to prepend to the stack, but appending would 
    likewise be impossible. It will always take up to two moves to move a tab to particular
    index.

1. Closing a Window in a Stack

    Closing a window in a stack causes every other window above it in the stack to
    "shift" down. That is, closing window A causes B to become stacked onto
    window C if:
    - A was stacked onto C.
    - B was stacked onto A.

    ```
    occurs(Action) causes stacked_onto(B, C) if
       instance(Action, close_window),
       target(Action) = A,
       stacked_onto(A, C),
       stacked_onto(B, A).
    ```


TODO
- Swap
- Drop tab