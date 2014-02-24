---
layout: default
title: "Lab 9: JavaScript and jQuery"
---

Your Task
=========

Make the following modifications to the [Canvas Demo](../lectures/lecture09/canvas.html).

Step 1
------

Modify the Canvas Demo so that the circles bounce around inside the box. Each timer tick callback should check the position of each circle to see if it has reached the edge of the canvas, and if so, deflect it away from the edge.

Step 2
------

Modify the Canvas Demo so that a circle is created when the user clicks on the canvas.  The created circle should be positioned at the location of the mouse click.

Step 3
------

Implement collisions between the balls.

Hints
-----

You will need to add fields to each circle object to keep track of each circle object's vertical and horizontal velocity. For example, you could add **dx** and **dy** fields to keep track of the distance the circle moves in the x and y directions on each timer tick.

To handle mouse clicks, try

    $("#canvas").mousedown(function(e) {
        ...code...
    });

Within the mousedown handler, `e.pageX` and `e.pageY` retrieve the coordinates of the mouse click relative to the canvas.
