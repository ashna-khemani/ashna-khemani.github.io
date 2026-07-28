*ESE 4500/4510: Electrical Engineering Senior Design*

**Goals**: 
- VTOL with forward flight → fixed-wing tail-sitter bicopter, capable of transitioning between "glider" mode that uses the fixed wings to travel longer distances, and "copter" mode that uses the rotors vertically to hover.
- Aerial-aquatic → waterproof, neutrally buoyant with an appropriate CG to allow maneuverability underwater.

**My focus**:
- Everything mechanical. 
	- Airframe design: wing and body design to fit avionics, elevon actuation methods. Iterating on this as our stability requirements changed (ex: we switched to a larger battery, which pushed our CG backwards. This required designing a new body and enlarging the elevons to regain stability).
	- Manufacture, and iterating/experimenting with different 3D print methods and materials.
	- Motor and propellor research and analysis, based on estimated thrust requirements.
	- Waterproofing. Experimenting with rubber, epoxy, and silicone sealants, and TPU 3D prints in early versions.
	- Buoyancy and weight distribution adjustments to enable swimming.
- Tuning PID controller for vertical and horizontal flight modes. Mostly based on observation.

Below are clips of the drone flying and swimming:

| ![[SnrDesFlight.mp4]] | ![[SnrDesSwim.mov]] |
| --------------------- | ------------------- |

Final presentation:
![[SnrDesPres.pdf]]



Note: "glider" mode clips are not shown because it's hard to find safe open fields in the middle of Philadelphia 😬. We tried it once, it worked, and we never got the chance to try again.

All in all... this project was a *ride*. Took up a huge portion of my senior year, but it was a great reminder of a few things: 
1) First principles always persist, and using them to do basic analysis at the beginning can illuminate a lot.
2) Well-designed experiments are critical.
3) Iterative/experiment-heavy work only makes sense if each iteration has clear goals and rationale behind it.