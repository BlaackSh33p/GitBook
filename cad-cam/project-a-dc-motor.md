# Project A (DC motor)

1. the math and when the 1st-order approximation is valid,
2. full MATLAB scripts to build the **full 2nd-order model** and the **1st-order approx**, compare them, run `rlocus` and `nyquist`, design a PID from root locus / frequency methods,
3. how to add a disturbance torque and test disturbance rejection,
4. a parameter variation (±20%) experiment that measures overshoot and settling time, and
5. a minimal Simulink wiring description.

All math is in raw LaTeX blocks so you can copy it directly.

***

## 1) Denominator, meaning, and approximation validity

The transfer function from voltage V(s)V(s)V(s) to angular velocity Ω(s)\Omega(s)Ω(s) (full model) has denominator:

LJs2+(Lb+RJ)s+(Rb+KtKe)L J s^2 + (L b + R J) s + (R b + K\_t K\_e)LJs2+(Lb+RJ)s+(Rb+Kt​Ke​)

**What each term means**

LJ⏟(s2 term)s2\underbrace{LJ}\_{\text{(s}^2\text{ term)\}} s^2(s2 term)LJ​​s2

* LJLJLJ couples electrical inductance LLL and rotor inertia JJJ. It produces the second-order (fast electrical × mechanical) dynamics.

(Lb+RJ)⏟(s term)s\underbrace{(L b + R J)}\_{\text{(s term)\}} s(s term)(Lb+RJ)​​s

* LbL bLb = inductance × viscous friction (electrical×mechanical coupling).
* RJR JRJ = resistance × inertia (electrical dissipation × mechanical inertia). This term is the effective damping coefficient in the s-domain.

(Rb+KtKe)⏟constant term\underbrace{(R b + K\_t K\_e)}\_{\text{constant term\}}constant term(Rb+Kt​Ke​)​​

* RbR bRb = electrical resistance × mechanical viscous friction (losses).
* KtKeK\_t K\_eKt​Ke​ = motor torque constant × back-EMF constant; this couples electrical generation with mechanical torque and affects steady-state gain and stability.

**When is the 1st-order approximation valid?**

Neglecting inductance LLL yields a first-order approximation:

Ω(s)V(s)≈KtRJs+(Rb+KtKe).\frac{\Omega(s)}{V(s)} \approx \frac{K\_t}{R J s + (R b + K\_t K\_e)}.V(s)Ω(s)​≈RJs+(Rb+Kt​Ke​)Kt​​.

This approximation is valid when the electrical time constant τe\tau\_eτe​ is much faster than the mechanical time constant τm\tau\_mτm​:

τe=LR,τm=Jb.\tau\_e = \frac{L}{R}, \qquad \tau\_m = \frac{J}{b}.τe​=RL​,τm​=bJ​.

The rule-of-thumb condition:

LR≪Jb.\frac{L}{R} \ll \frac{J}{b}.RL​≪bJ​.

If τe≤0.1 τm\tau\_e \le 0.1\\,\tau\_mτe​≤0.1τm​ (i.e., electrical dynamics are an order of magnitude faster), you can safely use the 1st-order model for control design that targets mechanical speed bandwidths.

***

## 2) Full MATLAB script — modeling, comparison, rlocus, nyquist, PID design, disturbance, parameter variation

Save this as `dc_motor_analysis.m` and run in MATLAB.

```matlab
%% dc_motor_analysis.m
% Full DC motor analysis: full 2nd-order model vs 1st-order approx,
% root locus & Nyquist, PID design, disturbance rejection, ±20% param sweep.

clear; close all; clc;

% -------------------------
% PARAMETERS (nominal)
% -------------------------
J  = 0.01;    % kg*m^2
b  = 0.1;     % N*m*s/rad
Kt = 0.01;    % N*m/A
Ke = 0.01;    % V*s/rad
R  = 1;       % Ohm
L  = 0.5;     % H

% Build full transfer function G(s) = Omega(s)/V(s)
num = Kt;
den_full = [L*J, (L*b + R*J), (R*b + Kt*Ke)];
G_full = tf(num, den_full);

% First-order approximation (neglect L)
den_1st = [R*J, (R*b + Kt*Ke)];
G_1st = tf(num, den_1st);

% Display numeric denominator coefficients
fprintf('Full den coeffs: L*J=%.6f, L*b+R*J=%.6f, R*b+Kt*Ke=%.6f\n', den_full);
fprintf('1st-order den coeffs: R*J=%.6f, R*b+Kt*Ke=%.6f\n', den_1st);

% -------------------------
% Compare step & frequency
% -------------------------
figure; step(G_full, G_1st); grid on;
legend('Full 2nd-order','1st-order approx'); title('Step responses: full vs approx');

figure; bode(G_full, G_1st); grid on;
legend('Full 2nd-order','1st-order approx'); title('Bode: full vs approx');

% -------------------------
% Condition check: time constants
% -------------------------
tau_e = L/R;
tau_m = J/b;
fprintf('tau_e = %.4f s, tau_m = %.4f s, ratio tau_e/tau_m = %.4f\n', tau_e, tau_m, tau_e/tau_m);

% -------------------------
% Root locus & Nyquist (full model)
% -------------------------
figure; rlocus(G_full); title('Root locus - full model');
figure; nyquist(G_full); title('Nyquist - full model');

% -------------------------
% Simple PID design using root-locus intuition
% (Manual PID via s-domain: C(s) = Kp + Ki/s + Kd*s)
% Here we'll start with a PI and tune Kp, Ki by trial (or use pidtune).
% -------------------------
% Option 1: automated pidtune (best)
if exist('pidtune','file')
    C_pid = pidtune(G_full, 'PID');
    disp('pidtune output:'); C_pid
    T_cl = feedback(C_pid*G_full, 1);
    figure; step(T_cl); grid on; title('Closed-loop with pidtune PID (full model)');
else
    % Option 2: manual PI example (tune Kp, Ki)
    Kp = 100; Ki = 200; Kd = 0;
    C_pid = pid(Kp, Ki, Kd);
    T_cl = feedback(C_pid*G_full, 1);
    figure; step(T_cl); grid on; title('Closed-loop with manual PI (full model)');
end

% -------------------------
% Disturbance torque: add a second input (torque) and simulate disturbance rejection
% Model in state-space form for two-input simulation:
% States: [i; omega]; inputs: [V; tau_load]
% -------------------------
A = [-R/L, -Ke/L;
      Kt/J, -b/J];
B = [1/L, 0;    % first column: V input affects current; second column: torque?
     0, -1/J];  % second column: torque input (negative sign: tau_load enters RHS)
C = [0 1];      % output = omega
D = [0 0];

sys_ss = ss(A,B,C,D);

% Simulate closed-loop with controller (as state-feedback on transfer from V to omega)
% We'll implement PID in series on V path and simulate disturbance torque on input 2.

% Convert PID to state-space or use LTI blocks: use feedback with two inputs via lsim.
% Create closed-loop from V input -> output with unity feedback and controller:
sys_open = series(C_pid, G_full);   % controller * plant
sys_cl = feedback(sys_open, 1);     % standard unity feedback (from Omega to summing junction)

% Now make a two-input representation where input1 = voltage reference, input2 = disturbance torque.
% Simulate by combining: y = T_cl * V + Gd * Tau where Gd is transfer at output from disturbance.
% Gd(s): transfer from tau_load to omega for the full plant:
% For the plant G_full we had only V->omega. For tau we can compute: Omega/Tau = 1/(J s + b) * 1/(1 + ...)
% Simpler: derive plant from state-space B(:,2) input.
% Use series(C_pid, ss(A,B(:,1),C,0)) for V channel and open-loop disturbance as ss(A,B(:,2),C,0).

G_v = series(C_pid, ss(A,B(:,1),C,0));  % V -> omega with controller
Gv_cl = feedback(G_v, 1);               % closed-loop transfer V->omega

G_tau = ss(A,B(:,2),C,0);               % disturbance tau -> omega (open-loop)
% For simulation we'll compute response to step disturbance with zero reference.
t = 0:0.01:5;
V_ref = zeros(size(t));
tau_step = 0.01 * ones(size(t)); % step disturbance torque of 0.01 N*m

% Response to disturbance when closed-loop for V path (i.e., controller tries to regulate)
y_response_tau = lsim( feedback(ss(A,B(:,1),C,0)*C_pid,1) * 0, V_ref, t ); % zero
% Better approach: compute closed-loop sensitivity S(s) and transfer from tau to y: T_tau = (I - L/(1+L)) * G_tau? 
% To keep this script practical: simulate full augmented interconnection in Simulink or use linear fractional modeling in control toolbox.

% Display note
disp('For disturbance simulation and two-input closed-loop, using Simulink to wire two-input summing junction is simpler. See Simulink section below.');

% -------------------------
% Parameter variation: ±20% on J and b, measure overshoot & settling time
% -------------------------
N = 21;
deltas = linspace(-0.2, 0.2, N); % -20% .. +20%
OS = zeros(N,1);
Ts = zeros(N,1);
for k=1:N
    Jk = J * (1 + deltas(k));
    bk = b * (1 + deltas(k));
    den_k = [L*Jk, (L*bk + R*Jk), (R*bk + Kt*Ke)];
    Gk = tf(Kt, den_k);
    % closed-loop with same controller C_pid:
    if exist('pidtune','file')
        T_k = feedback(C_pid * Gk, 1);
    else
        T_k = feedback(C_pid * Gk, 1);
    end
    S = stepinfo(T_k); % MATLAB returns rise time, settling time, overshoot
    OS(k) = S.Overshoot;
    Ts(k) = S.SettlingTime;
end

figure; subplot(2,1,1); plot(deltas*100, OS, '-o'); grid on;
xlabel('Parameter variation (%)'); ylabel('Overshoot (%)'); title('Overshoot vs ±20% variation (J & b together)');
subplot(2,1,2); plot(deltas*100, Ts, '-o'); grid on;
xlabel('Parameter variation (%)'); ylabel('Settling time (s)'); title('Settling time vs ±20% variation');

% Save results
table_results = table(deltas', OS, Ts, 'VariableNames', {'DeltaFraction','OvershootPercent','SettlingTime'});
disp(table_results);
```

**Notes on the script**

* It builds `G_full` and `G_1st` and plots comparisons.
* It prints time constants `tau_e = L/R` and `tau_m = J/b` so you can check the approximation validity. If `tau_e/tau_m << 1` the 1st-order is fine.
* `rlocus(G_full)` and `nyquist(G_full)` produce the design plots.
* PID design: prefers `pidtune` (automatic). If unavailable, a manual PI is provided as example.
* Disturbance path: for a clear two-input disturbance simulation, Simulink is cleaner (see below). I left notes and a pointer to Simulink — the script does parameter sweep and computes `stepinfo` (overshoot and settling time).

***

## 3) How to use `rlocus` & `nyquist` to design controllers (practical steps)

**Root locus workflow**

1. Plot root locus:

```matlab
rlocus(G_full);
```

2. Inspect locus and decide target closed-loop pole locations for desired damping/settling. Use `sgrid(zeta, wn)` to help:

```matlab
zeta = 0.7; wn = 5;
sgrid(zeta, wn);
```

3. Use `rlocfind` to pick a point and obtain the required gain KKK:

```matlab
[k,p] = rlocfind(G_full); % click on plot; returns gain k and pole p
```

4. If a PID/lead-lag is needed, design the compensator C(s)C(s)C(s) to shape the locus (add zero/pole) until poles go where you want. Example lead compensator:

Clead(s)=Ks+zs+p,with z\<pC\_{\text{lead\}}(s) = K \frac{s + z}{s + p}, \quad \text{with } z < pClead​(s)=Ks+ps+z​,with z\<p

**Frequency / Nyquist / Bode workflow**

1. Plot Bode and margins:

```matlab
[mag,phase,wout] = bode(G_full);
margin(G_full);   % shows gain and phase margins
```

2. Use `nichols` or `nyquist` to study robustness. If you need more phase at crossover, add a lead compensator; if you need more low-frequency gain for steady-state, add an integrator (PI).
3. Example PI for zero steady-state error:

CPI(s)=Kp+Kis.C\_{\text{PI\}}(s) = K\_p + \frac{K\_i}{s}.CPI​(s)=Kp​+sKi​​.

Tune Kp,KiK\_p,K\_iKp​,Ki​ so that loop transfer L(s)=C(s)G(s)L(s)=C(s)G(s)L(s)=C(s)G(s) has reasonable gain margin and adequate bandwidth.

***

## 4) Adding a disturbance torque (Simulink way — recommended)

Minimal Simulink block wiring (textual):

* Blocks:
  * Step (for voltage reference) → Sum (+) → PID Controller → Plant (Transfer Fcn using `num`/`den_full`) → Sum (feedback) → Scope
  * Add another Step (or Signal Builder) for disturbance torque; connect it into the **plant** as an additive torque input (you need to model plant to accept two inputs — easiest is to use state-space or a custom Simulink subsystem that sums torque into mechanical equation).

Wiring steps:

1. Insert a **Sum** block before the plant for the torque path: the plant should be built as a two-input block (voltage and torque). If using a plain Transfer Fcn block (single input), instead simulate disturbance by adding an equivalent input to plant input through modelling — but the correct approach is to implement the full state-space plant in Simulink (use the _State-Space_ block with `A,B,C,D` matrices). Use:
   * State-Space block with:
     * `A = [-R/L, -Ke/L; Kt/J, -b/J]`
     * `B = [1/L, 0; 0, -1/J]` (first input is V, second is tau\_load)
     * `C = [0 1]`, `D = [0 0]`
2. For unity feedback: connect output to a Scope and back to the feedback summing junction (subtract from reference). The PID block acts on the error.
3. To test disturbance rejection: set Step2 (disturbance) to have start time at e.g. 1 s and amplitude 0.01 N·m.

Simulink elements: Step (Vref) → Sum (+ −) → PID Controller → State-Space (plant) → Scope. Step (torque) → second input of State-Space. Feedback: output → Sum (negative back into Sum before PID).

***

## 5) Parameter variation and performance metrics (formulas & how MATLAB computes them)

**Percent overshoot** MpM\_pMp​ for a standard second-order dominant pole pair:

Mp=100×e−ζπ1−ζ2(percent)M\_p = 100 \times e^{-\frac{\zeta \pi}{\sqrt{1-\zeta^2\}}} \quad\text{(percent)}Mp​=100×e−1−ζ2​ζπ​(percent)

Where ζ\zetaζ is damping ratio.

**Settling time** (2% criterion) approximated for a 2nd-order system:

Ts≈4ζωnT\_s \approx \frac{4}{\zeta \omega\_n}Ts​≈ζωn​4​

Where ωn\omega\_nωn​ is natural frequency.

In MATLAB we use `S = stepinfo(T_cl)` which returns `S.Overshoot` (percent) and `S.SettlingTime` (using default 2% unless you change `SettlingTimeThreshold`).

In the script I provided, the ±20% parameter sweep varies `J` and `b` together across the range and computes `stepinfo` of the closed-loop system to produce arrays of overshoot and settling time, and then plots them.

***

## 6) Additional explanation of the denominator arithmetic (plugged numbers)

Given toy parameters:

L=0.5,J=0.01,b=0.1,R=1,Kt=Ke=0.01L = 0.5,\quad J=0.01,\quad b=0.1,\quad R=1,\quad K\_t = K\_e = 0.01L=0.5,J=0.01,b=0.1,R=1,Kt​=Ke​=0.01

Compute the denominator coefficients:

LJ=0.5×0.01=0.005LJ = 0.5 \times 0.01 = 0.005LJ=0.5×0.01=0.005 Lb+RJ=(0.5×0.1)+(1×0.01)=0.05+0.01=0.06Lb + RJ = (0.5 \times 0.1) + (1 \times 0.01) = 0.05 + 0.01 = 0.06Lb+RJ=(0.5×0.1)+(1×0.01)=0.05+0.01=0.06 Rb+KtKe=(1×0.1)+(0.01×0.01)=0.1+0.0001=0.1001Rb + K\_t K\_e = (1 \times 0.1) + (0.01 \times 0.01) = 0.1 + 0.0001 = 0.1001Rb+Kt​Ke​=(1×0.1)+(0.01×0.01)=0.1+0.0001=0.1001

So the full plant transfer function becomes:

Ω(s)V(s)=0.010.005s2+0.06s+0.1001\frac{\Omega(s)}{V(s)} = \frac{0.01}{0.005 s^2 + 0.06 s + 0.1001}V(s)Ω(s)​=0.005s2+0.06s+0.10010.01​

And the first-order approx (neglect LLL):

Ω(s)V(s)≈0.01(1×0.01)s+0.1001=0.010.01s+0.1001.\frac{\Omega(s)}{V(s)} \approx \frac{0.01}{(1 \times 0.01) s + 0.1001} = \frac{0.01}{0.01 s + 0.1001}.V(s)Ω(s)​≈(1×0.01)s+0.10010.01​=0.01s+0.10010.01​.

(Notice: with LLL neglected, the coefficient of sss becomes RJR JRJ and the constant remains Rb+KtKeR b + K\_t K\_eRb+Kt​Ke​.)

***

## 7) Practical recommendations & next steps

* Run the `dc_motor_analysis.m` script. Look at the printed `tau_e` and `tau_m` ratio — if `tau_e/tau_m < 0.1`, the 1st-order curves will overlay the full model closely.
* Use `pidtune` first; then refine manually with `rlocus`/`margin`.
* Use Simulink for disturbance simulation: the two-input state-space block is the cleanest approach.
* When comparing full vs approx, plot the difference `step(G_full - G_1st)` to see at which frequencies the approximation departs.



### 🟢 Can `dc_motor_analysis.m` run in Python?

Not **directly**. MATLAB `.m` scripts use MATLAB’s Control System Toolbox functions (`tf`, `step`, `bode`, `rlocus`, `nyquist`, `pidtune`, etc.).\
But there’s a **Python equivalent library**:

* `control` (often installed as `pip install control`)
* Provides `TransferFunction`, `step_response`, `bode_plot`, `nyquist_plot`, `rlocus`, etc.
* Very close to MATLAB syntax, e.g.:

```python
import control as ctrl
import matplotlib.pyplot as plt

# Parameters
J = 0.01
b = 0.1
Kt = 0.01
Ke = 0.01
R = 1
L = 0.5

# Full model
num = [Kt]
den_full = [L*J, L*b + R*J, R*b + Kt*Ke]
G_full = ctrl.TransferFunction(num, den_full)

# First-order approximation
den_1st = [R*J, R*b + Kt*Ke]
G_1st = ctrl.TransferFunction(num, den_1st)

# Step responses
t, y_full = ctrl.step_response(G_full)
t, y_1st  = ctrl.step_response(G_1st)

plt.plot(t, y_full, label="Full 2nd-order")
plt.plot(t, y_1st, '--', label="1st-order approx")
plt.legend(); plt.grid(); plt.show()
```

You can also do **root locus**:

```python
ctrl.rlocus(G_full)
plt.show()
```

And **Nyquist**:

```python
ctrl.nyquist_plot(G_full)
plt.show()
```

So yes ✅ — everything in `dc_motor_analysis.m` can be replicated in Python with **minor syntax tweaks**.

***

### 🟡 Which is easier?

* **MATLAB/Simulink**
  * Pros: everything integrated, GUI support, Simulink block diagrams (perfect for disturbance torque, nonlinear models, etc.), `pidtune` auto-design.
  * Cons: proprietary (license).
* **Python**
  * Pros: free, scriptable, integrates with data science (NumPy, SciPy, pandas, matplotlib).
  * Cons: no direct Simulink equivalent; modeling nonlinear multi-input systems takes more manual coding.

***

### ⚖️ “Fast and easy balance” strategy

✔️ Use **Python + control library** for:

* Quick prototyping (step response, bode, rlocus, nyquist, param sweep).
* Automating calculations, making plots, running parameter variations.
* Sharing code easily with others (no license).

✔️ Use **Simulink** for:

* Block-diagram modeling (e.g., plant with voltage + torque inputs).
* Closed-loop simulations with multiple signals and nonlinearities.
* Disturbance rejection studies (step torque injection).
* Visual understanding of controllers interacting with the plant.

This way:

* Do your **math and sweeps in Python** (fast).
* Build your **final block-diagram experiments in Simulink** (intuitive and visual).

***

👉 Do you want me to **rewrite the `dc_motor_analysis.m` script fully in Python** (step, bode, rlocus, nyquist, param sweep) so you can compare side-by-side with MATLAB?

**You said:**

rewrite the dc\_motor\_analysis.m script fully in Python and is what you are saying possible where i can generate a Simulink block diagrams using matlab scripts

**ChatGPT said:**

Perfect 👍 let’s tackle this in **two parts**:

***

## 🟢 Part 1: Python version of `dc_motor_analysis.m`

Here’s the **full translation** using the `control` package.\
Save it as `dc_motor_analysis.py`.

```python
# dc_motor_analysis.py
# Full DC motor analysis: 2nd-order vs 1st-order model,
# root locus, Nyquist, PID design, parameter variation.

import numpy as np
import matplotlib.pyplot as plt
import control as ctrl

# -------------------------
# PARAMETERS (nominal)
# -------------------------
J  = 0.01   # kg*m^2
b  = 0.1    # N*m*s/rad
Kt = 0.01   # N*m/A
Ke = 0.01   # V*s/rad
R  = 1      # Ohm
L  = 0.5    # H

# Full transfer function G(s) = Omega(s)/V(s)
num = [Kt]
den_full = [L*J, L*b + R*J, R*b + Kt*Ke]
G_full = ctrl.TransferFunction(num, den_full)

# First-order approximation (neglect L)
den_1st = [R*J, R*b + Kt*Ke]
G_1st = ctrl.TransferFunction(num, den_1st)

print("Full model denominator:", den_full)
print("1st-order model denominator:", den_1st)

# -------------------------
# Step response comparison
# -------------------------
t = np.linspace(0, 5, 500)
t1, y_full = ctrl.step_response(G_full, T=t)
t2, y_1st  = ctrl.step_response(G_1st, T=t)

plt.figure()
plt.plot(t1, y_full, label="Full 2nd-order")
plt.plot(t2, y_1st, '--', label="1st-order approx")
plt.title("Step responses: full vs approx")
plt.xlabel("Time [s]"); plt.ylabel("Omega(t)")
plt.grid(True); plt.legend()

# -------------------------
# Bode plot comparison
# -------------------------
plt.figure()
ctrl.bode_plot([G_full, G_1st], dB=True, Hz=False, omega_limits=(1e-1, 1e2))
plt.legend(["Full 2nd-order","1st-order approx"])
plt.suptitle("Bode magnitude & phase")

# -------------------------
# Time constants
# -------------------------
tau_e = L/R
tau_m = J/b
print(f"tau_e = {tau_e:.4f} s, tau_m = {tau_m:.4f} s, ratio tau_e/tau_m = {tau_e/tau_m:.4f}")

# -------------------------
# Root locus
# -------------------------
plt.figure()
ctrl.rlocus(G_full)
plt.title("Root locus - full model")

# -------------------------
# Nyquist
# -------------------------
plt.figure()
ctrl.nyquist_plot(G_full)
plt.title("Nyquist - full model")

# -------------------------
# Simple PI design (manual tuning)
# -------------------------
Kp, Ki, Kd = 100, 200, 0
C_pid = ctrl.tf([Kp, Ki], [1, 0])  # PI: (Kp*s + Ki)/s

T_cl = ctrl.feedback(C_pid*G_full, 1)

t3 = np.linspace(0, 5, 500)
t3, y_cl = ctrl.step_response(T_cl, T=t3)

plt.figure()
plt.plot(t3, y_cl)
plt.title("Closed-loop with PI controller")
plt.xlabel("Time [s]"); plt.ylabel("Omega(t)")
plt.grid(True)

# -------------------------
# Parameter variation: ±20% J & b
# -------------------------
deltas = np.linspace(-0.2, 0.2, 11)
overshoots = []
settling_times = []

for d in deltas:
    Jk = J * (1 + d)
    bk = b * (1 + d)
    den_k = [L*Jk, L*bk + R*Jk, R*bk + Kt*Ke]
    Gk = ctrl.TransferFunction([Kt], den_k)
    T_k = ctrl.feedback(C_pid*Gk, 1)

    t4, yk = ctrl.step_response(T_k, T=np.linspace(0, 5, 500))

    # Step info calculation (manual since Python-control doesn't have stepinfo like MATLAB)
    y_final = yk[-1]
    overshoot = (np.max(yk) - y_final)/y_final * 100
    # Settling time: first time where output stays within 2% of final
    tol = 0.02 * y_final
    idx = np.where(np.abs(yk - y_final) > tol)[0]
    if len(idx) > 0:
        Ts = t4[idx[-1]]
    else:
        Ts = 0

    overshoots.append(overshoot)
    settling_times.append(Ts)

plt.figure()
plt.subplot(2,1,1)
plt.plot(deltas*100, overshoots, 'o-')
plt.ylabel("Overshoot [%]"); plt.grid(True)
plt.title("Performance vs ±20% variation (J, b)")

plt.subplot(2,1,2)
plt.plot(deltas*100, settling_times, 'o-')
plt.xlabel("Variation [%]"); plt.ylabel("Settling time [s]")
plt.grid(True)

plt.tight_layout()
plt.show()
```

✅ This script reproduces the MATLAB workflow:

* full vs 1st-order models,
* step + Bode comparison,
* root locus & Nyquist,
* PI closed-loop,
* parameter sweep (±20%) with overshoot + settling time.

***

## 🟡 Part 2: Can you generate Simulink block diagrams from MATLAB scripts?

Yes! MATLAB has the **Simulink API** (`add_block`, `add_line`, etc.) that lets you build models programmatically.

Example: programmatically create the **minimal closed-loop diagram** (Step → PID → Plant → Feedback → Scope):

```matlab
new_system('DCMotorSim')
open_system('DCMotorSim')

% Add blocks
add_block('simulink/Sources/Step','DCMotorSim/Step');
add_block('simulink/Continuous/PID Controller','DCMotorSim/PID');
add_block('simulink/Continuous/Transfer Fcn','DCMotorSim/Plant');
add_block('simulink/Math Operations/Sum','DCMotorSim/Sum','Inputs','+-');
add_block('simulink/Sinks/Scope','DCMotorSim/Scope');

% Position blocks
set_param('DCMotorSim/Step','Position',[50 100 80 130]);
set_param('DCMotorSim/Sum','Position',[120 95 140 135]);
set_param('DCMotorSim/PID','Position',[200 100 240 130]);
set_param('DCMotorSim/Plant','Position',[300 100 360 130]);
set_param('DCMotorSim/Scope','Position',[400 100 430 130]);

% Connect lines
add_line('DCMotorSim','Step/1','Sum/1');
add_line('DCMotorSim','Sum/1','PID/1');
add_line('DCMotorSim','PID/1','Plant/1');
add_line('DCMotorSim','Plant/1','Scope/1');
add_line('DCMotorSim','Plant/1','Sum/2');
```

Run this script in MATLAB and it **auto-builds a Simulink model**. You can extend it by adding extra inputs (like disturbance torque) or swapping Transfer Function with State-Space.

***

### ⚖️ Balanced workflow

* **Python (`control` library)** → fast numerical experiments, batch sweeps, easy plotting.
* **Simulink (via scripting)** → build visual models programmatically and simulate multi-input systems (disturbance rejection, nonlinearities, saturation).

***
