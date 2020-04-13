# Module: Stacks of Windows

## Overview

Users may "stack" windows on top of each other. This causes multiple tabs
to display in a single window, akin to multiple tabs in a single internet
browser window.

Users create stacks by dragging the tab of one window and dropping it
onto the tab of another window. We refer to this action as "tabbing"
a window onto another window (for example, to say "the user tabbed window A onto window B"
is to say "the user dropped the tab of window A onto the tab of window B).
Likewise, users may remove a window from a stack by dropping its tab onto the desktop. Exactly one
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
    close_stack :: window_action
    ```
1. Activate Tab

    The user can click an inactive tab to cause it to become the
    active tab. Additionally, several other actions also cause
    the same effect; those actions inherit from this one.
    ```
    activate_tab :: window_action
    ```
1. Drag Tab

    The user may drag the tab of a window anywhere on the desktop.
    When the user drags a tab over certain areas, the area is highlighted
    to indicate dropping the tab there will cause some effect. When dragging,
    the coordinates of the mouse are used as the coordinates of the tab. 

    ```
    drag_tab :: action
        attributes
            coordinates : axes -> integers
    ```
1. Drop Tab

    The user may drop the tab of a window onto the desktop. The effect
    of this action depends on what the tab is dropped onto. All such
    actions remove the window from its current position in its stack.
    ```
    drop_tab :: window_action
    ```
1. Tab Onto
    The user may tab a window onto another window.
    This causes the first window to become stacked onto the second (see fluent
    definitions below).

    ```
    tab_onto :: drop_tab, activate_tab
    ```
1. Drop Tab onto Window Body

    The user may drop the tab of a window onto the body of another window.
    The effects of this are discussed in the
    [Tiling Module](./tiling.alm.md).

    ```
    drop_tab_onto_body :: drop_tab
    ```
1. Drop Tab onto Desktop

    The user may drop the tab of a window onto the desktop, moving the window
    to the dropped on coordinates.

    ```
    drop_tab_onto_desktop :: drop_tab, activate_tab
        attributes
            drop_coordinates : axes -> integers
    ```

## Functions
### Fluents
#### Basic

1. Stacked Onto

    ```
    stacked_onto : windows -> windows
    ```

1. Active Tab

    Exactly one window in each stack is said to be the active tab.

    ```
    active_tab : windows -> booleans
    ```


#### Defined

1. Stacked Above

    The transitive closure of Stacked Onto. A window is stacked
    above another window if it is stacked on that window or
    stacked onto a window above that window.

    ```
    stacked_above : windows x windows -> booleans
    ```

2. Stack Root

    A window is said to be the root of its stack if
    it is not stacked onto any other window.
    
    ```
    stack_root : windows -> booleans
    ```

3. Stack Index

    Stacks can be interpreted as an array of windows.
    Each window in the stack has an index in that array,
    where the root has index 0, the window stacked onto the
    root has index 1, etc.

    ```
    stack_index : windows -> integers
    ```

4.  In Same Stack

    Two windows are in the same stack if they are stacked above
    the same root. Additionally, every window is in the same stack as itself.
    In Same Stack forms a partitioning of the windows sort, being reflexive,
    symmetric, and transitive.

    ```
    in_same_stack : windows x windows -> booleans
    ``` 

## Axioms
1. A window may only be stacked on at most one other window.

    ```
    false if
        instance(A, windows),
        #count { B : stacked_on(A, B) } > 1. 
    ```
1. Stacked Above.
    Stacked Above is the transitive closure of Stacked Onto.
    ```
    stacked_above(A, B) if stacked_onto(A, B).
    stacked_above(A, B) if stacked_above(A, C), stacked_above(C, B).
    ```
2. Stacked Root

    A window is a stack root if it is not stacked onto any other window.

    ```
    stack_root(A) if
        instance(A, windows),
        #count { B : stacked_onto(A, B) } = 0.
    ```

3. Stack Index

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

4. In Same Stack

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

1. There is always exactly one active tab in a stack.

    ```
    false if ...
    TODO - Not sure how to express this as a state constraint.
    ```

5. Closing a Stack

    Closing the stack causes every window in the stack to become hidden.
    ```
    occurs(Action) causes -visible(W') if
        instance(Action, close_stack),
        target(Action) = W,
        in_same_stack(W, W').
    ```
1. Activating a Tab

    When the user activates a tab, the target window becomes
    active, and the previously visible window
    becomes inactive.

    ```
    occurs(Action) causes active_tab(A) if
        instance(Action, activate_tab),
        target(Action) = A.
    occurs(Action) causes -active_tab(B) if
        instance(Action, activate_tab),
        target(Action) = A,
        in_same_stack(A, B),
        active_tab(B)>
    ```
1. The active tab is visible. All other windows are hidden.

    ```
    visible(A) if active_tab(A).
    -visible(A) if -active_tab(A).
    ```
    
6. Dropping a Tab

    1. Dropping the tab of a window causes:

       - The target window to no longer be stacked onto the window onto
          which it was previously stacked.
            ```
            occurs(Action) causes -stacked_onto(A, B) if
                instance(Action, drop_tab),
                target(Action) = A,
                stacked_onto(A, B).
            ```

       - Any other window stacked onto that window to no longer
         be stacked onto that window.

            ```
            occurs(Action) causes -stacked_onto(B, A) if
                instance(Action, drop_tab),
                target(Action) = A,
                stacked_onto(B, A).
            ```
    
    2. Dropping the tab of a window causes that window's array
    to "shift" down. That is, dropping the tab of window A
    onto some other window causes B to become stacked onto C if: 
       - A is stacked onto C,
       - B is stacked onto A.
        ```
        occurs(Action) causes stacked_onto(B, C) if
            instance(Action, drop_tab),
            target(Action, A),
            stacked_on(A, C),
            stacked_on(B, A)
        ```

    3. Dropping the tab of window A causes window B to
        become the active tab if
        - A is the active tab.
        - A is not the root of the stack.
        - A is in the same stack as B.
        - B has an index 1 less than A.

        ```
        occurs(Action) causes stacked_onto(B, C) if
            instance(Action, drop_tab),
            target(Action, A),
            -stack_root(A),
            in_same_stack(A, B),
            stack_index(A, AIndex),
            stack_index(B, BIndex),
            AIndex - 1 = BIndex.
        ```
        Similarly, dropping the tab of window A causes window B to
        become the active tab if
        - A is the active tab.
        - A is the root of the stack.
        - B has an index 1 more than A.
        ```
        occurs(Action) causes stacked_onto(B, C) if
            instance(Action, drop_tab),
            target(Action, A),
            stack_root(A),
            in_same_stack(A, B),
            stack_index(A, AIndex),
            stack_index(B, BIndex),
            AIndex + 1 = BIndex.
        ```
7. Tabbing

    1. It is impossible to tab a window onto itself.

        ```
        impossible occurs(Action) if
            instance(Action, tab_onto),
            target(Action) = A,
            drop_target(Action) = A.
        ```

    2. Tabbing window A onto window B
        causes A to become stacked onto B.

        ```
        occurs(Action) causes stacked_onto(A, B) if
            instance(Action, tab_onto),
            target(Action) = A,
            drop_target(Action) = B.
        ```

    3. Tabbing a window onto another window "inserts" that
        window into the target's array. That is,
        tabbing A onto B causes C to become
        stacked onto A if C was stacked onto B.

        ```
        occurs(Action) causes stacked_onto(C, A) if
            instance(Action, tab_onto).
            target(Action) = A,
            drop_target(Action) = B.
            stacked_onto(C, B).
        ```

        **Note**: Because, when tabbing window A onto window B, A is inserted "above"
        B, it's therefore impossible for users to prepend to the stack, i.e  move
        a tab to the very beginning of the stack in a single operation. If we inserted A
        "below" B, this would allow users to prepend to the stack, but appending would 
        likewise be impossible. It will always take up to two moves to move a tab to particular
        index.

    4. Tabbing a window A onto a window B causes A to assume the size and coordinates of B.

        ```
        occurs(Action) causes coordinate(A, Axis) = C if
            instance(Action, tab_onto).
            target(Action) = A,
            drop_target(Action) = B.
            coordinate(B, Axis) = C.

        occurs(Action) causes height(A) = H if
            instance(Action, tab_onto).
            target(Action) = A,
            drop_target(Action) = B.
            height(B) = H.

        occurs(Action) causes width(A) = W if
            instance(Action, tab_onto).
            target(Action) = A,
            drop_target(Action) = B.
            width(B) = W.
        ```
8. Dropping a tab onto the desktop

    Dropping the tab of a window onto the desktop causes that window
    causes that window to assume the coordinates of the point dropped on.
    The window also becomes visible if it was not.

    ```
    occurs(Action) causes coordinates(A, Axis) = C if
            instance(Action, drop_tab_onto_desktop).
            target(Action) = A,
            drop_coordaintes(Action, Axis) = C.
    ```
9.  Closing a Window in a Stack

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