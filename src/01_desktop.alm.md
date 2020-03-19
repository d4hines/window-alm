# Module: Desktop

_(Note: presently, this module is purely for the understanding of the reader and contains no ALM code.)_

## The Desktop Plane
Finsemble exists on a desktop, a virtual unbounded Cartesian plane reﬂected on the X axis (such that the Y axis increases as you move “down”). The plane is divided into discrete pixels, which are the smallest unit of display. Since pixels are the only unit of measure in this book, it is the assumed unit for all integer values given unless otherwise speciﬁed.

## The Mouse
The primary means by which a user interacts with the desktop user interaction is via the mouse, a pointer the user may move anywhere on the desktop. For the purposes of this document, the state of the mouse can be reduced to a pair of X and Y coordinates representing its location on the desktop, and single boolean value representing whether the left click button is down, (also termed mouse down) or up (also termed mouse up).
