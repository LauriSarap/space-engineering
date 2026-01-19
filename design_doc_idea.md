# Project Aetherius: End-to-End Aerospace Simulation Design Document

## 1. Project Vision
Project Aetherius is a professional-grade simulation suite designed to engineer and validate a budget-friendly Small Satellite Launch Vehicle (SLV) and its CubeSat payload. The system must provide high-fidelity physics, real-time telemetry, and structural engineering validation.

---

## 2. Technical Environment (Host Specs)
AI agents must optimize code for the following environment:
* **OS:** Arch Linux (KDE Plasma)
* **CPU:** Intel Core i9-14900HX (24 Cores / 32 Threads)
* **GPU:** NVIDIA GeForce RTX 4070 (8GB VRAM) with CUDA support
* **Language:** Python 3.13
* **Primary IDE:** VS Code

---

## 3. Engineering Requirements

### 3.1 Rocket Engineering (The Launch Vehicle)
* **Propulsion:** Support for Liquid (RP-1/LOX & Methalox) gas-generator cycles and Solid/Hybrid motors.
* **Dynamics:** 6-Degrees-of-Freedom (6DOF) flight dynamics including variable mass, CoG shifting, and moment of inertia changes.
* **Multi-Stage Logic:** Calculation of stage separation, fairing deployment, and orbital insertion burn timing.

### 3.2 Satellite Engineering (The CubeSat)
* **Standardization:** Support for 1U to 12U form factors.
* **ADCS (Attitude Determination and Control):** Simulation of reaction wheels and magnetorquers using PID control loops.
* **Power & Thermal:** Solar flux calculation based on orbital position and eclipse periods.

### 3.3 Earth-Space Environment
* **Atmospheric Model:** US Standard Atmosphere 1976 (0 - 1000km).
* **Gravity:** WGS84 Ellipsoidal model with $J_2$ perturbations (Nodal regression and Perigee shift).
* **Aerodynamics:** Mach-dependent Drag Coefficient ($C_d$) and dynamic pressure ($q$) calculations.

---

## 4. Mathematical Framework
AI agents must implement the following core equations within the physics engine:

* **The Ideal Rocket Equation:**
    $$\Delta v = I_{sp} \cdot g_0 \cdot \ln \left( \frac{m_{initial}}{m_{final}} \right)$$

* **Effective Thrust (Atmosphere Dependent):**
    $$F = \dot{m} v_e + (P_e - P_a) A_e$$
    *Where $P_e$ is exit pressure and $P_a$ is ambient atmospheric pressure.*

* **Dynamic Pressure:**
    $$q = \frac{1}{2} \rho v^2$$

---

## 5. Tech Stack & Library Selection

| Component | Technology | Implementation Detail |
| :--- | :--- | :--- |
| **Math Core** | **NumPy / SciPy** | For ODE integration (RK45) and linear algebra. |
| **GPU Acceleration** | **CuPy** | Move 6DOF matrix calculations to the RTX 4070. |
| **Astrodynamics** | **Poliastro / Orekit** | High-precision orbital propagation and J2 effects. |
| **Data Engine** | **Polars** | Handle high-frequency telemetry logging (faster than Pandas). |
| **UI/Dashboard** | **Streamlit** | Browser-based dashboard for real-time graphs and 3D views. |
| **Visualization** | **PyVista (VTK)** | 3D rendering of the Earth and Rocket models. |
| **Validation** | **Pydantic** | Strict type and unit enforcement for all physical inputs. |

---

## 6. AI Agent Coding Guidelines

### 6.1 Architecture: Domain-Driven Design (DDD)
The codebase must be strictly modular to allow independent agent work:
* `/src/physics/`: Pure math functions. No dependencies on UI.
* `/src/environment/`: Atmospheric and Gravity data tables.
* `/src/hardware/`: Classes for `Engine`, `Tank`, `ReactionWheel`.
* `/src/sim/`: The integration loop and state-vector management.
* No use of .env variables
* Only comment relevant code, no unnecessary comments
* More code is not neccesarily better code, try to keep it simple and efficient, especially elegant -> achieve elegance by writing simple, readable code.
* You may occasioanly sacrefice performance for readability
* All requirements to requirements.txt, preferrably without version specifiers

### 6.2 Data Integrity & Units
1.  **SI Units Only:** All internal calculations must use Meters, Kilograms, Seconds, Newtons.
2.  **Type Hinting:** Use Python 3.13 strict typing.
3.  **Validation:** Every class must use Pydantic to prevent "impossible physics" (e.g., negative mass or $I_{sp} > 500$).

### 6.3 Performance Optimization
* **Vectorization:** Avoid `for` loops in the integration step. Use NumPy/CuPy arrays.
* **Multiprocessing:** Utilize the 32 threads of the i9-14900HX for Monte Carlo launch simulations.

---

## 7. Scientific Constants (Project Standard)
* **Standard Gravity ($g_0$):** $9.80665 \, m/s^2$
* **Earth Radius ($R_e$):** $6378137.0 \, m$
* **Earth Gravitational Parameter ($\mu$):** $3.986004418 \times 10^{14} \, m^3/s^2$
* **J2 Constant:** $1.08262668 \times 10^{-3}$