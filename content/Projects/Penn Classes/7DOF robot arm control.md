*MEAM 5200 Introduction to Robotics (spring 2025)*

For this course, I built up a Python library to model and manipulate a 7DOF robot arm. This included
- Forward Kinematics, establishing DH parameters → joint positions
- Velocity Fwd and Inverse Kinematics → trajectory following
- Secondary tasks → follow trajectories more exactly
- Pathfinding with APF and RRT → obstacle avoidance

Code was initially tested in a ROS + Gazebo simulation, then verified on a physical robot, as shown in the videos below.


| ![[meam5200-traj.mov]]                               | ![[meam5200-final.mov]]                                                  |
| ---------------------------------------------------- | ------------------------------------------------------------------------ |
| Left: trajectory following verified on physical bot. | Right: final pick-and-place challenge to stack labeled blocks (4x speed) |

It was cool to build up the math to model all this, and translate it into algorithms and code. It was a great way to extend and apply the theory I had learned in my undergrad dynamics class. 

Code can't be released yet since the labs are still being used.






