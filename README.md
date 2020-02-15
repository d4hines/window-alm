# window-alm
Description of window movement in the ALM action language.

## Sorts and Function Declarations
```
    module dragging
        sort declarations
            windows :: universe
            % directions is a set comprised of the atoms left, right, top, and bottom.
            directions :: { left, right, top, bottom }
            % axes is a set comprised of the atoms x and y.
            axes :: { x, y }
            drag :: actions
                attributes
                    target : windows
                    offset : axes -> integers
        function declarations
            fluents
                basic
                    total coordinate : windows x axes -> integers
                    total width : windows -> integers
                    total height : windows -> integers
                    total possibleCoordinate : windows x axes -> integers
                defined
                    coordinate : windows x axes x integers -> booleans
                    snaps : windows x windows -> booleans
                    attracts : windows x windows -> booleans
                    nearestSide : windows x windows x directions -> booleans
                    distance : windows x windows x integers -> booleans
                    onSameLine : windows x windows x axes -> booleans
                    overlaps : windows x windows -> booleans
                    closest : windows x windows -> booleans
                    moving : windows -> booleans
                    % inverse of moving.
                    stationary : windows -> booleans
```

## Axioms

### Causal Laws
- `Dragging` a window by an offset causes the window' `possible coordinates` to equal to its current `coordinates` plus the offset.
- `Dragging` a window causes it to become `moving`.
- `Dragging` a window causes every other window to become `stationary`.
### Defined Fluent Axioms
- Window A `snaps` to Window B if:
    - A is `moving`.
    - B `attracts` A along direction D.
    - Nothing `attracts` A in some other direction.
- If window A `snaps` to window B, the `coordinates` of A become the `possible coordinates` of A moved
by the `distance` D between A and B in the direction of the `nearest side` of A to B.
- A window's `coordinates` are equal to its `possible coordinates` if it does not `snap` to any other window.
- The `distance` between A and B is the smallest measure between opposite edges of A and B (e.g. the measure from the top edge of A to the bottom edge of B, etc.), where:
    - A is `on the same line` as B.
    - A does not `overlap` B.
    - (`distance` is symmetric)
- Window A `attracts` window B in direction D if:
    - The `distance` between A and B is less than the snapping threshold.
    - A is `closest` to B
    - The `nearest side` of B to A is D

In the below picture, A attracts B left, while B attracts A right:
![](https://i.imgur.com/zYOB0rS.png)
- Window A is `closest` to B if the `distance` between A and B is the minimum distance between A and any window (`closest` is not symmetric).
- A is `on the same line` as B w.r.t an axis AX if there is a line on AX that intersects both A and B. (`on same line` is symmetric).
- A `overlaps` B if A is `on the same line` as B w.r.t to both x and y.
- The `nearest side` of A to B is the direction D where the measure between edge D of A and the opposite edge of B is equal to the `distance` between A and B.
In the below picture, the `nearest side` of A to B is right, while the `nearest side` of B to A is left.
![](https://i.imgur.com/zYOB0rS.png)



