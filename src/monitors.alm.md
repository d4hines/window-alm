# Monitors
2. There are one or more monitors, corresponding to the physical screens plugged into the device running Finsemble, which are rectangular viewports into the desktop. Monitors range in size from 800x600 to 4096x2160 (though in the future, they may grow larger). Typical Finsemble users have between 1 and 9 monitors in a given session.
3. An arbitrary monitor is chosen by the user to be the primary monitor, the top left corner of which is chosen to be the origin of desktop plane. All other monitors are termed secondary monitors. Users may add or remove monitors at any time, and may adjust their resolution, scale, arrangement, and primary status (how Finsemble reacts to these events will be handled later in the document).
4.
1. Monitors

    Monitors are rectangular views into the desktop. A window is visible only when it overlaps with or is contained by a monitor.

    ```
    monitors :: rectangles
    ```
