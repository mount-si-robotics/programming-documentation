# Using Apriltags with Limelight
The [Limelight 3A](https://limelightvision.io/products/limelight-3a) (hopefully the one you have) is a Very Fancy Camera™ that can automatically read apriltags and give you the position using Megatag2.
Apriltags.

## Setting up apriltags
> [!NOTE]
> [Limelight's offical apriltag guide](https://docs.limelightvision.io/docs/docs-limelight/pipeline-apriltag/apriltags) can be more helpful and updated, and it's recommended you read that first ! This section will only contain some tips and tricks.
- If you have issues with moving at high speeds with the camera blurring, try setting your exposure lower.

## Reading apriltags
I recommend having a function you can call to get your results from limelight. The one I have used for a couple years is:
```java
/**
 * This function gets the current limelight result, and returns null if there isn't one.
 * @param yaw The current yaw of the robot, should be just what you plug in
 * @param yawOffsetFromOrigin The offset yaw, incase you start the robot in a different position than what you want the actual field rotation to be
 * @return Limelight data (LLResult) or null, if the result is not valid.
 */
public LLResult getLimelightData(double yaw, double yawOffsetFromOrigin) {
    // Add the yaw offset and wrap it as needed.
    // If you are using odometry, make sure odometry is also aware of this.
    double limelightOrientation = (yaw + yawOffsetFromOrigin) % 360;
    if (limelightOrientation > 180) limelightOrientation -= 360;
    
    // Update the robot orientation for Megatag 2 and grab the result
    limelight.updateRobotOrientation(limelightOrientation);
    LLResult result = limelight.getLatestResult();

    // Check if the result is valid and/or exists
    if (result != null && result.isValid()) {
        return result;
    }

    // Return null if not valid
    return null;
}
```
And to use the result:

```java
LLResult result = getLimelightData(yawDeg);
if (result != null) {
    Position mt2 = result.getBotpose_MT2().getPosition();
    double x = mt2.x;
    double y = mt2.y;
}
```

## Conclusion
Even with Megatag2, limelight data can be jittery. It's up to you to figure out how to deal with this! At higher speeds limelight data is definately less reliable but it is decently stable at lower speeds. Be sure to test this if you are planning to use it on your robot.