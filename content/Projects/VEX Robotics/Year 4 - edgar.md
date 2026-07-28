*2020-21 season*

_Robot name origin: an early version of this bot made concerning noises while driving (it still worked) that reminded us of of haunting works of Edgar Allen Poe._

Curveball for this year: doing the entire season virtually and with fewer sponsors (thanks COVID). But I made it work. [Here's](https://www.youtube.com/watch?v=Hxs0q9UoMDQ)the challenge video for Change Up.

With limited resources (and space in garages and living rooms), build took longer this year, and it was easy to feel frustrated. But I did the best I could using what I had, and created Edgar. Though it didn't look as clean as previous builds, it worked, and that's what I cared about for now.

![[edgar.png|415]]

**Notable subsystems and specs:**

- Torqued down (sped) powertrain (in this virtual season, there were no heavy loads to push or other bots to defensively push around. But there _was_ a need for speed)
- Ball indexer with limit switch for easy ball management
    - Edgar could hold up to 3 balls. This system would automatically "lock and load" them to just under the outtake/launcher, so all I had to do as the driver was pull the trigger to deposit the ball into a goal
- Not a subsystem but you'd be surprised how much Edgar relies on rubberbands, masking tape, and friction mesh.
- PID control! Scroll to the bottom for code.

From our realization last year, I decided to focus pretty much entirely on the Skills challenge this year, especially since the Virtual Tournaments were similar to Skills anyway.

Again, with a ton of driving strategy and practice, I was able to get close to maxing out the highest possible score in the Driver Skills challenge.

Though I had a working autonomous program last year, I knew it could be, in the words of Daft Punk, "better, faster, stronger." I therefore started use sensors beyond the quadrature encoders we had been relying on, implementing ultrasonic sensors and an IMU. They were a step up, but I had heard of **PID control**, which I was dying to try. I worried that it might be a waste of time and not be worth it, but I jumped in. It took many Google searches, YouTube videos, and failed tests, but I finally got excellent PID loop for driving.

![](https://www.youtube.com/embed/iapxfOCZfn8)

Focusing on Skills helped me become a stronger all-rounder, and my team won multiple remote competitions, Skills Challenges, and several awards. We were even ranked in the Top 100 worldwide! Since everything was remote, I was able to compete in many more events. My ability to strategize, communicate at lightning speed with my team, and think under pressure on the spot grew much stronger.

And this helped me not only qualify for the (Virtual) World Championship, but even get to the **Division Semifinals**. I had started the year skeptical of what I could achieve with limited resources, but I believe I truly made the best of it, learned, and reached a level higher than I'd ever expected.

# PID code
If you're curious, the PID code (well, technically it's more of a PD) can be viewed [here](https://github.com/anandananya/Vex2021ChangeUp/blob/6523d6ddc1525c1b23637f820998c39f9accd091/World%20v1%20050921/src/bot-functions.cpp#L79C1-L79C1). Here it is copied:

```c
void drivePD(double targetDistance) {
  double motorspeed = 0, currentDistance = 0,
         error = (targetDistance - currentDistance), errorSum = 0, deltaE = 0,
         lastError = 0, maxAllowedError = 0.75, errorTimerMax = 50;
  double kP = 8, kI = 0, kD = 20; //working 8,0,20
  bool timerExpired = false;
  timer errorTimer = timer();
  errorTimer.clear();
  FrontRightMotor
      .resetRotation(); 
  FrontLeftMotor.resetRotation();

  while ((fabs(error) > maxAllowedError) && (timerExpired == false)) {
    currentDistance = ((FrontRightMotor.rotation(rotationUnits::deg) +
                        FrontLeftMotor.rotation(rotationUnits::deg)) /
                       2 / 360) *
                      WHEELCIRCUMFERENCE;
    error = targetDistance - currentDistance;
    errorSum += error;
    deltaE = (error - lastError) / 5000;
    motorspeed = (error * kP) + (errorSum * kI) + (deltaE * kD);

    BackLeftMotor.spin(directionType::fwd, motorspeed, velocityUnits::pct);
    FrontLeftMotor.spin(directionType::fwd, motorspeed, velocityUnits::pct);
    FrontRightMotor.spin(directionType::fwd, motorspeed, velocityUnits::pct);
    BackRightMotor.spin(directionType::fwd, motorspeed, velocityUnits::pct);

    lastError = error;
    
    if (fabs(error) > maxAllowedError) {
      errorTimer.clear();
    } else {
      if (errorTimer.time() > errorTimerMax) {
        timerExpired = true;
      }
    }
    vex::task::sleep(20);
  }
  BackLeftMotor.stop(brake);
  FrontLeftMotor.stop(brake);
  FrontRightMotor.stop(brake);
  BackRightMotor.stop(brake);
}
```