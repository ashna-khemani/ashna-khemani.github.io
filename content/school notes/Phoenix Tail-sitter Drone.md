https://ar5iv.labs.arxiv.org/html/1810.03196 
https://github.com/utiasSTARS/PhoenixDrone

# Notable sources
- Wingtra
	- Design is based off: [[Full_Attitude_Control_of_a_VTOL_tailsitter_UAV.pdf|S. Verling, B. Weibel, M. Boosfeld, K. Alexis, M. Burri, and R. Siegwart, “Full Attitude Control of a VTOL Tailsitter UAV,” in _Proceedings of the IEEE International Conference on Robotics and Automation (ICRA’16)_, May 2016, pp. 3006–3012.]]


# Issues encountered

- Need to cast with carbon fiber tubes embedded
- No CAD for the elevons?? Or attachment methods
- No proof that this can actually flip to horizontal flight. All demos are in vertical configuration
	- ⇒ No control architecture for horizontal config. Hovering can ignore drag, but horiz prob cannot. Will need to find a way to factor this in ([code](https://www.rcgroups.com/forums/showpost.php?p=51594219&postcount=1406) from Taming Tail-sitter [video](https://www.youtube.com/watch?v=RabFZzRyZo8))

# Mechanical design
- E168 airfoil ([example](http://airfoiltools.com/airfoil/details?airfoil=e168-il)) 
	- Wanted a symmetric airfoil to hover at 0 pitch
	- Wanted low $Re$ number
- 21cm span, 8in prop
- 30% elevon (relative to entire wing chord I assume)
- Want larger-ish elevons for aggressive maneuvers (I assume this is a product of the "flip" to go between H and V configs)
- Material considerations
	- Wings: avoid shattering or permanent deformation during crashes → foam

# Dynamics

Force and moment sum:

$$f_{tot}=\sum_{i}(f_{prop,i}+f_{aero,i})+R_{w}^{b}mg$$
$$M_{tot}=\sum_{i}(M_{prop,i}+M_{aero,i})$$

where
![[eqnvars.png|300]]


# Controls
## Position
- Desired acceleration: $\vec{a_{des}}$
- 2ODE with time constant $\tau_{p}$ and damping ratio $\zeta_{p}$ 
	- $\zeta=\frac{c}{c_{c}}$ and $c_{c}=2m\omega_{n}=2\sqrt{km}$ 

$$
\vec{a_{des}}=-g+\frac{1}{\tau_{p}^{2}} (p_{des}-p_{est}) + \frac{2\zeta_{p}}{\tau_{p}}(\vec{v_{des}} - \vec{v_{est}})
$$
