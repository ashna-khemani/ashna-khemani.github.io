*MEAM 3480 Junior Spring Mechanical Engineering Lab*

A fun project for vibes ✨

**Goal**: Configure the spring-mass-damper constants for a pendulum suspended in a tower, such that when the tower experiences an impact, the shaking in the tower structure itself is mitigated. Accurately predict the motion of our model building in an "earthquake" caused by an impact at the base.

The first step was recognizing which quantities we could physically measure, which quantities we could then estimate, and how we could use those to determine an ODE that modeled our building's behavior.

We then implemented this math in a simple Python script that could be used to plot the generated ODE vs. the observed motion measured using an accelerometer and Arduino (code is at the end of this page). This, and our Arduino instrumentation, was first tested on the classic predictable system of a cantilever fixed at one end and impacted by a instantaneous force at the other (clamping a steel bar to a table and flicking the end).

![[3480Lab2barvibe.png|332]]

We then extend the same logic to the tower system, which was built like the one in [this video](https://youtu.be/f1U4SAgy60c?si=qMZKgFVw6oyrCQmF&t=431). The result was a slightly underdamped system with a log decrement of δ≈4. This was one of the best in class 😄
![[3480Lab2towertuned.png|317]]


**Main lesson:** a good model is really helpful. But empirical testing to adjust your model/find things you didn't account for is a step above.

→ Test like you fly!


Python script for model estimation:
```python
import matplotlib.pyplot as plt
import numpy as np
from numpy import pi, exp, cos, sqrt
from scipy.integrate import solve_ivp

### tighter screw

fig, ax = plt.subplots()
data = np.loadtxt("tunedmass_2.txt", delimiter=",")
# data = data[342: , :]
# data[:,1] = data[:,1] - 9.81
# data[:, 0] = data[:, 0] - 3.98


############ TODO: TUNE THESE VALUES
peak1 = (2.5992, 0.4301)
peak2 = (3.241, 0.2187)
T_elapsed = peak2[0] - peak1[0]
n = 1   # periods between peaks
delta = (1/n) * np.log(peak1[1]/peak2[1])     # logarithmic decrement
zeta = 1 / (np.sqrt(1 + (2*pi/delta)**2))   # damping ratio
w_d = 2*np.pi / T_elapsed
w_n = w_d / np.sqrt(1-zeta**2)


decay_fudge = 0.65
osc_fudge = 0.87
X = 23
phi = 0
def soln(t):
    return X*exp((-zeta*w_n*t)*decay_fudge) * cos(osc_fudge*sqrt(1-zeta**2) * w_n*t - phi)

print(f"omega = {w_n}")
print(f"zeta = {zeta}")
print(f"decay fudge = {decay_fudge}")
print(f"osc fudge = {osc_fudge}")

t = np.linspace(0,4,1000)
# ax.plot(t, soln(t), "b-", label="equation")
ax.plot(data[:, 0], data[:, 1], 'y-', label="data")

ax.legend()
ax.set_xlabel("time [s]")
ax.set_ylabel(r"$a(t) [m/s^{2}]$")
ax.set_title("Vertical Acceleration vs. Time")
fig.suptitle("Tower Task 1")
plt.show()
```