# Basics

## 🚀 Learning Control Systems: From Basics to Interactive Dashboards

This blog captures our full session on control systems — from understanding **PID controllers** and **root locus design**, to modeling real-world systems like the **mass–spring–damper**. Along the way, we build **interactive Python dashboards** to experiment with system responses, overlay performance metrics, and explore the concepts of controllability and observability. This log is designed to serve as a **comprehensive reference** for you (or anyone else) whenever you get stuck.

***

### 1. Why Controllers Matter

When dealing with real-world systems (motors, robots, electrical circuits), raw physical dynamics rarely meet desired performance specs (fast response, minimal overshoot, stability under disturbances). Controllers (like **PI** and **PID**) are inserted into the loop to shape system response:

* **Proportional (P):** Scales the error; reduces rise time but can leave steady-state error.
* **Integral (I):** Eliminates steady-state error by adding memory of past errors.
* **Derivative (D):** Predicts future error; improves damping and stability.

Together, **PID** controllers let you balance speed, stability, and accuracy. This matters in everything from drones to car cruise control to industrial machinery.

***

### 2. The Root Locus Method

The **Root Locus** is a graphical method to design controllers:

* Shows how closed-loop poles move as gain varies.
* Lets you place poles in stable and well-damped regions.
* A PI controller adds a **pole at the origin** and a **zero at (-k\_i/k\_p)**.

By adjusting (K\_p) and (K\_i), you change the zero location and loop gain, reshaping the root locus and thus the closed-loop response.

***

### 3. A Real Example: DC Motor Position Control

Plant transfer function (position output / voltage input):

\[\
G(s) = \frac{10}{0.021s^2 + 4.845s}\
]

Performance requirements:

* Overshoot < 40%
* Rise time < 1s
* Settling time < 3s
* Gain margin > 20 dB
* Phase margin > 30°
* Bandwidth > 5 rad/s

Using only proportional control (K\_p = 0.55) looked great in simulation, but failed on the **real motor** due to unmodeled nonlinearities. Adding I and D terms compensates better in hardware.

***

### 4. Interactive Python Tools

We built several interactive Python dashboards (with `matplotlib`, `ipywidgets`, and `control`) to:

* Tune (K\_p, K\_i) with sliders.
* Visualize **root locus** and **step response** together.
* Overlay performance metrics: rise time, overshoot, settling time.
* Display **controllability** and **observability**.

#### Example Code Snippet (Step Response with Performance Overlays)

```python
info = step_info(sys_tf)
rise_time = info.get('RiseTime', None)
settling_time = info.get('SettlingTime', None)
overshoot = info.get('Overshoot', None)

plt.axhline(1, linestyle='--', color='k', label='Reference')
if rise_time: plt.axvline(rise_time, linestyle='--', color='g', label=f"Rise ≈ {rise_time:.2f}s")
if settling_time: plt.axvline(settling_time, linestyle='--', color='r', label=f"Settling ≈ {settling_time:.2f}s")
if overshoot: plt.axhline(1+overshoot/100, linestyle='--', color='m', label=f"Overshoot ≈ {overshoot:.1f}%")
```

This turns your simulation into a **real lab environment**.

***

### 5. Mechanical System Case Study: Mass–Spring–Damper

Equation:\
\[\
m\ddot{x} + b\dot{x} + kx = F\
]

Transfer Function:\
\[\
G(s) = \frac{1}{ms^2 + bs + k}\
]

Key parameters:

* (m): mass
* (b): damping coefficient
* (k): spring constant

#### Time-Domain Specs (approximate):

* Rise time: (T\_r \approx 1.8/\omega\_n)
* Settling time: (T\_s \approx 4/(\zeta \omega\_n))
* Overshoot: (M\_p(%) = 100 e^{-\zeta\pi/\sqrt{1-\zeta^2\}})

Where:\
\[\
\omega\_n = \sqrt{\tfrac{k}{m\}}, \quad \zeta = \frac{b}{2\sqrt{km\}}.\
]

#### Trade-offs:

* High (\omega\_n): faster system (short rise/settling) but possible resonance.
* Higher (\zeta): smoother, less overshoot, but slower.

Sweet spot: (\zeta = 0.6-0.8).

***

### 6. Controllability & Observability

From state-space form:\
\[\
\dot{x} = Ax + Bu, \quad y = Cx + Du\
]

* **Controllability matrix:** ( \mathcal{C} = \[B, AB, A^2B, ...] )
* **Observability matrix:** ( \mathcal{O} = \[C^T, A^TC^T, ...]^T )

If ranks = system order → fully controllable/observable. Otherwise → some modes cannot be controlled or measured.

***

### 7. Practical Tuning Strategy

1. **Choose (\zeta)** ≈ 0.6–0.8 → balances overshoot and damping.
2. **Pick (\omega\_n)** from desired settling time: (\omega\_n ≈ 4/(\zeta T\_s)).
3. **Back-calculate parameters:**
   * (k = m\omega\_n^2)
   * (b = 2\zeta \sqrt{km})
4. **Check response:** adjust as needed.

***

### 8. Putting It All Together: The Dashboard

Your final interactive tool shows:

* Step & impulse responses.
* Root locus with moving poles/zeros.
* Bode magnitude plot.
* Performance overlays (rise, overshoot, settling).
* Controllability & observability checks.

This is a **mini control systems lab on your laptop**, ready before moving to ESP32 or hardware.

***

### 9. Conclusion

* **Controllers (PI, PID)** are essential to shape dynamics into useful, reliable systems.
* **Root locus** gives insight into pole movement and system stability.
* **Mass–spring–damper** is the prototype for both mechanical and electrical systems.
* **Interactive Python tools** make abstract equations real and intuitive.
* **Controllability/observability** connect to what is physically possible in control.

With this reference, you can now:

* Model a system.
* Analyze time/frequency response.
* Tune controllers visually.
* Understand parameter effects deeply.

***

✨ Next Steps: Add Nyquist plots and pole-zero maps into the dashboard for **full analysis coverage**.
