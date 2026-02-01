# Adding Wheel Encoder Odometry
Odometry is the act of figuring out where the robot is using encoder wheel positions.
This can usually be broken into three parts:
- Figure out the change in position relative to the robot in encoder ticks (forward kinematics)
- Rotate this vector to world space
- Convert encoder ticks to real world units (like meters)

This document will focus on **mecanum drivetrains** but concepts from this can be used for other drivetrains.

## Forward Kinematics
This is the most complicated part of odometry: figuring out how we actually convert encoder ticks to a vector that the robot is currently moving. Fortunately, a lot of smarter people have already figured this out for us:
\\[
    \\left\\{\\begin{aligned}\\
    v_x & = (\\omega_{fl} + \\omega_{fr} + \\omega_{rl} + \\omega_{rr}) \\cdot \\frac{r}{4}\\\\
    v_y & = (-\\omega_{fl} + \\omega_{fr} + \\omega_{rl} - \\omega_{rr}) \\cdot \\frac{r}{4}\\\\
    \\omega_z & = (-\\omega_{fl} + \\omega_{fr} - \\omega_{rl} + \\omega_{rr}) \\cdot \\frac{r}{4(l_x + l_y)}
    \\end{aligned}\\right.
\\]

We don't need to worry about rotation (\\( \\omega_z \\)), instead we can use the IMU to figure it out! The Java equivilent of this would be:
```java
double dxLocal = (deltaFrontLeft + deltaFrontRight + deltaBackLeft + deltaBackRight) / 4.0;
double dyLocal = (-deltaFrontLeft + deltaFrontRight + deltaBackLeft - deltaBackRight) / 4.0;
```

## Rotating the vector
Now, we have a movement vector relative to the robot in encoder ticks. The next step is to rotate this vector to be in terms of field. We can use triganometry to figure this out:
\\[
    \\begin{split}x_2 = \\cos(\\beta) x_1 - \\sin(\\beta) y_1 \\\\
    y_2 = \\sin(\\beta) x_1 + \\cos(\\beta) y_1\\end{split}
\\]
The Java code for this is going to be:
```java
double dx = sin(yawRads) * dxLocal + cos(yawRads) * dyLocal;
double dy = cos(yawRads) * dxLocal - sin(yawRads) * dyLocal;
```
## Convert encoder ticks to Real World Units
The last step is to convert the units of the vector to real world units. Use this forumula as a starting point:\
> \\(W_{C}\\): Wheel Circumferance (in the units you want to convert to)\
> \\({T_{rev}}\\): Ticks per rev at the motor (Counts per Revolution)\
> \\({G_{r}}\\): Gear reduction

\\[
    \\frac{W_{C}}{T_{rev}\\cdot G_{r}}
\\]
Example, using [Rev 75mm Mecanum Wheels](https://www.revrobotics.com/rev-45-1655/) using a [HD Hex Motor](https://www.revrobotics.com/REV-41-1291) and a [4:1](https://www.revrobotics.com/rev-41-1602) + [5:1](https://www.revrobotics.com/rev-41-1603/)
Ultraplanetary Gearbox (20:1 gear ratio), we get this:
> \\(W_{C} = 0.075m * \\pi = 0.235m\\)\
> \\({T_{rev}} = 28\\)\
> \\({G_{r}} = 18.8803\\)

> [!CAUTION]
> REV Gear ratios are not exact, check their specifications for the exact numbers. This is why our gear reduction is 18.8, and not 20.

\\[
    \\frac{W_{C}}{T_{rev}\\cdot G_{r}}=\\frac{0.235}{28 \\cdot 18.8803}\\approx 0.00044
\\]

> [!WARNING]
> In practice, this is only an estimation. How far your robot moves depends on way too many variables to calculate exactly. To improve on this, use a program to run your robot a set amount of ticks, and measure how far it moved, then use this data to refine the number.

You can then add this result to your position. From my testing, I have found that the actual sign and directions of these vectors may be different, and you may need to negate some values from testing. Not entirely sure why this happens.
## Conclusion
If you want some more sources to research this topic, here are the sources we used to make this:
- Rotating a vector: [https://matthew-brett.github.io/teaching/rotation_2d.html](https://matthew-brett.github.io/teaching/rotation_2d.html)
- Kinematics: [https://ecam-eurobot.github.io/Tutorials/mechanical/mecanum.html](https://ecam-eurobot.github.io/Tutorials/mechanical/mecanum.html)