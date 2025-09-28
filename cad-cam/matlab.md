# MATLAB

**MATLAB and its add-ons are not full CAD/CAM packages**, but they _do_ cover a lot of the modeling, simulation, control-design, visualization and code-generation pieces that surround a CAD/CAM workflow. You can import CAD geometry, simulate multibody dynamics, design controllers, generate embedded code (G-code post-processing is possible by scripting), and connect to hardware — but you wouldn’t use MATLAB instead of SolidWorks/FreeCAD (for precise geometry design) or Mastercam/Fusion CAM (for toolpath generation) as your primary CAD/CAM tool. Think of MATLAB as the _brains_ (math, control, simulation, offline path planning and automation), and dedicated CAD/CAM tools as the _geometry & toolpath specialists_.

Below I put together a **practical, step-by-step “control systems learning project”** you can run _right now_ in MATLAB (and Simulink if you have it). It’s hands-on: derive the math, implement the model, simulate, design controllers (PID and LQR), test robustness, and optionally move to Simulink / hardware. I give runnable MATLAB scripts / snippets so you can copy/paste and learn rather than only “put figures and numbers in”.

## What you’ll get from the project

* Build physical models from first principles (equations of motion).
* Implement transfer-function and state-space models in MATLAB.
* Do time- and frequency-domain analysis (step, Bode, margins).
* Design controllers: PID (pidtune) and state-feedback / LQR.
* Validate with disturbance / parameter variation tests.
* Move the design to Simulink and (optionally) Simscape for physics-level simulation.
* Notes about toolboxes required and next steps (hardware/code generation).

***

## Toolboxes / add-ons you might need

* **Control System Toolbox** (transfer function, bode, pidtune, lqr, place) — highly recommended.
* **Simulink** (block-diagram simulation).
* **Simscape / Simscape Multibody / Simscape Electrical** (detailed physical models and CAD import).
* **Simulink Coder / Embedded Coder** (generate C code for microcontrollers/targets).
* **System Identification Toolbox** (fit models from measured data).\
  (If you don’t have a toolbox, you can still do many steps by coding the math yourself in MATLAB.)



## Electrical dynamics

$$
V(t) = L \, \frac{di(t)}{dt} + R \, i(t) + K_e \, \omega(t)
$$

Mechanical dynamics

$$
J \, \frac{d\omega(t)}{dt} + b \, \omega(t) = K_t \, i(t) - \tau_{\text{load}}(t)
$$

&#x20;Transfer function (voltage to angular velocity)

$$
\frac{\Omega(s)}{V(s)} = \frac{K_t}{(L s + R)(J s + b) + K_e K_t}
$$

First-order approximation (neglect L)

$$
\frac{\Omega(s)}{V(s)} \approx \frac{K_t}{R J s + (R b + K_t K_e)}
$$

## PENDULUM EQUATIONS&#x20;

Nonlinear equation of motion

$$
m L^2 \, \ddot{\theta}(t) + b \, \dot{\theta}(t) + m g L \, \sin \theta(t) = \tau(t)
$$

&#x20;Linearized (small-angle approximation)

$$
m L^2 \, \ddot{\theta}(t) + b \, \dot{\theta}(t) + m g L \, \theta(t) = \tau(t)
$$

Transfer function (torque to angle)

$$
\frac{\Theta(s)}{\Tau(s)} = \frac{1}{m L^2 s^2 + b s + m g L}
$$

Natural frequency

$$
\omega_n = \sqrt{\frac{g}{L}}
$$

***

## Project A — DC motor: from physics → controller → Simulink

**Goal:** model an armature-controlled DC motor, analyze, design PID and LQR controllers, check robustness.

#### 1) Derive the model (read this once and put into code)

Electrical:

$$
V(t) = L \, \frac{di(t)}{dt} + R \, i(t) + K_e \, \omega(t)
$$

Mechanical:

$$
J \, \frac{d\omega(t)}{dt} + b \, \omega(t) = K_t \, i(t) - \tau_{\text{load}}(t)
$$

Laplace domain and eliminate 𝐼(𝑠) ⇒ transfer function from applied voltage 𝑉(𝑠) to angular velocity Ω(𝑠):

$$
\frac{\Omega(s)}{V(s)} = \frac{K_t}{(L s + R)(J s + b) + K_e K_t}
$$

If electrical inductance L is negligible you get a first-order approximation.

#### 2) Example numeric parameters (toy motor)

We'll use (typical demo values):

* J = 0.01 kg·m²
* b = 0.1 N·m·s/rad
* Kt ​= 0.01 N·m/A
* Ke =m0.01 V·s/rad
* R = 1 Ω
* L = 0.5 H

Arithmetic: $$LJ = 0.5 \times 0.01 = 0.005  ...  Lb + RJ = (0.5 \times 0.1) + (1 \times 0.01) = 0.05 + 0.01 = 0.06....Rb + K_t K_e = (1 \times 0.1) + (0.01 \times 0.01) = 0.1 + 0.0001 = 0.1001$$&#x20;

&#x20;DC Motor Arithmetic&#x20;

1\) Product of L and J

$$
LJ = 0.5 \times 0.01 = 0.005
$$

* Represents the product of **electrical inductance** L and **rotor inertia** J.
* This term multiplies s^2, contributing to the **second-order dynamics** of the motor.

&#x20;2\) Lb + RJ term

$$
Lb + RJ = (0.5 \times 0.1) + (1 \times 0.01) = 0.05 + 0.01 = 0.06
$$

* Represents the **cross-coupling** of electrical and mechanical damping.
* LbLbLb = inductance × viscous friction.
* RJRJRJ = resistance × inertia.
* This multiplies sss, i.e., the **damping term** in the dynamics.

&#x20;3\) Rb + K\_t K\_e term

$$
Rb + K_t K_e = (1 \times 0.1) + (0.01 \times 0.01) = 0.1 + 0.0001 = 0.1001
$$

* Contains both **electrical resistance × viscous friction** (RbRbRb) and the **motor constants** (KtKeK\_tK\_eKt​Ke​).
* This is the **constant term** in the denominator (affects steady-state gain and system stability).

👉 In summary:

* LJ → inertia + inductance (second-order dynamics).
* Lb+RJ → damping effects (first-order).
* Rb+Kt​Ke​ → combined losses + motor constants (steady-state term).

#### 3) Run this MATLAB script (dc\_motor\_demo.m)

```matlab
% DC motor demo - copy into dc_motor_demo.m
clear; close all; clc;

% Parameters
J = 0.01;      % kg*m^2
b = 0.1;       % N*m*s/rad
Kt = 0.01;     % N*m/A
Ke = 0.01;     % V*s/rad
R = 1;         % Ohm
L = 0.5;       % H

% Transfer function G(s) = Omega(s)/V(s)
num = Kt;
den = [L*J, (L*b + R*J), (R*b + Kt*Ke)];   % [a2 a1 a0]
G = tf(num, den);

% First-order approx (neglect L*J term)
den1 = [R*J, (R*b + Kt*Ke)];  % a1 s + a0
G_approx = tf(num, den1);

% Plots
figure; bode(G); grid on; title('Bode - full DC motor model');
figure; step(G); grid on; title('Step response - full model');
figure; step(G_approx); grid on; title('Step response - first-order approx');

% Design a PID with pidtune (if available)
if exist('pidtune','file')
    C_pid = pidtune(G,'PID');   % automatic tuning
    T_cl = feedback(C_pid*G, 1); 
    figure; step(T_cl); grid on; title('Closed-loop with pidtune PID');
else
    warning('pidtune not found - design PID manually');
end

% State-space (states: i, omega)
A = [-R/L, -Ke/L;
      Kt/J, -b/J];
B = [1/L; 0];
C = [0 1];   % output: omega
D = 0;
sys_ss = ss(A,B,C,D);

% LQR design (regulator)
Q = diag([1, 100]);   % penalize omega more (adjust to taste)
R_lqr = 0.01;
K = lqr(A,B,Q,R_lqr);

% Closed-loop Acl = A - B*K
Acl = A - B*K;
sys_cl_lqr = ss(Acl, B, C, D);
figure; step(sys_cl_lqr); grid on; title('Step response - LQR state-feedback (no reference gain)');

% Note: to track a reference r you need a reference gain Nbar (not shown).
```

#### 4) Experiments & exercises (do these)

* Compare full 2nd-order model vs 1st-order approximation — when is the approximation valid?
* Use `rlocus(G)` and `nyquist(G)` to design controllers by root locus and frequency methods.
* Add a disturbance torque (e.g., step torque) and test disturbance rejection.
* Vary parameters (±20% J, b) and measure closed-loop performance (overshoot, settling time).
* If you have System Identification Toolbox: generate simulated noisy step data and run `tfest` to estimate G.

#### 5) Simulink & Simscape

* Minimal Simulink: blocks: Step → PID Controller → Plant (Transfer Fcn using num/den) → Sum (feedback) → Scope.
* For physics-level: use **Simscape Electrical + Simscape Driveline / Multibody** to model coil, rotor, load; you can also import SolidWorks/STEP geometry into Simscape Multibody if you want realistic visuals.
