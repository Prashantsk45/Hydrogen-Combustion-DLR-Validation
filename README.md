# Decarbonizing Gas Turbines: Multi-Physics Modeling of Lean Hydrogen Combustion and NOx Formation
[![engrXiv DOI](https://img.shields.io/badge/engrXiv-DOI--10.31224%2F8088-blue.svg)](https://doi.org/10.31224/8088)

**Author:** Prashant Kamble  
**Field:** Computational Fluid Dynamics (CFD), Reacting Flows, Hydrogen Decarbonization  
**Solvers:** ANSYS Fluent

## 1. Project Overview
This repository contains a high-fidelity 3D Computational Fluid Dynamics (CFD) validation study of a co-swirling lean hydrogen-air combustor based on the **DLR Stuttgart Burner Benchmark**. The primary objective is to validate reacting flow models, turbulent-chemistry interactions, and thermal NOx predictions against experimental CARS (Coherent Anti-Stokes Raman Spectroscopy) and LDV (Laser Doppler Velocimetry) data.

This study validates:
* **Turbulence Modeling:** Shear Stress Transport (SST) $k$-$\omega$ with Curvature Correction (SST-CC).
* **Chemistry Modeling:** Non-adiabatic Equilibrium Probability Density Function (PDF) flamelet modeling.
* **Emission Pathways:** Thermal and prompt NOx pathways.
* **Grid Independence:** Verified using the ASME V&V 20 Grid Convergence Index (GCI).

<p align="center">
  <img src="FIGURES/Figure_01_Combustor_Domain_and_Injector.png" width="550" alt="Combustor Domain and Injector Configuration">
  <br>
  <em>Figure 1: Geometric configuration of the co-swirling DLR combustor domain and injector nozzles.</em>
</p>

---

## 2. Flow Parameters & Boundary Conditions
The physical combustor boundary conditions are configured to match the DLR experimental benchmark:

| Parameter | Symbol | Value | Units |
| :--- | :---: | :---: | :---: |
| Air Mass Flow Rate (Inner Swirled) | $\dot{m}_{\text{air,inner}}$ | 11.24 | g/s (0.01124 kg/s) |
| Air Mass Flow Rate (Outer Swirled) | $\dot{m}_{\text{air,outer}}$ | 32.97 | g/s (0.03297 kg/s) |
| Total Air Mass Flow Rate | $\dot{m}_{\text{air,total}}$ | 44.21 | g/s (0.04421 kg/s) |
| Hydrogen Mass Flow Rate (Jet) | $\dot{m}_{\text{H2}}$ | 0.85 | g/s (0.00085 kg/s) |
| Global Equivalence Ratio | $\Phi$ | 0.66 | - |
| Inlet Temperature | $T_0$ | 298 | K |
| Pressure | $P_0$ | 101.325 | kPa |
| Swirl Number (Geometric) | $S$ | 0.55 | - |

---

## 3. Mathematical Models & Governing Equations

### SST k-omega Curvature Correction (SST-CC)
To capture vortex breakdown and the Central Toroidal Recirculation Zone (CTRZ) induced by the swirler, the turbulent eddy viscosity is resolved using the SST $k$-$\omega$ model with a curvature correction multiplier ($f_{\text{rotation}}$):
$$\mu_t = \rho \frac{k}{\omega} \frac{1}{\max\left[\frac{1}{\alpha^*}, \frac{S F_2}{a_1 \omega}\right]} \cdot f_{\text{rotation}}$$

### Non-Adiabatic Equilibrium PDF Combustion Model
The mean species mass fractions and local temperature are computed by integrating the PDF lookup table over the mean mixture fraction ($\bar{f}$) and mixture fraction variance ($\overline{f'^2}$):
$$\tilde{\phi} = \int_0^1 \phi(f) P(f; \bar{f}, \overline{f'^2}) df$$
where $\phi$ represents species mass fractions and local enthalpy, and $P(f)$ is modeled using a $\beta$-PDF shape.

### Thermal NOx Pathway (Zeldovich Mechanism)
Nitric oxide (NO) emissions are resolved using the three-step Zeldovich reacting mechanism:
$$\text{O} + \text{N}_2 \rightleftharpoons \text{NO} + \text{N}$$
$$\text{N} + \text{O}_2 \rightleftharpoons \text{NO} + \text{O}$$
$$\text{N} + \text{OH} \rightleftharpoons \text{NO} + \text{H}$$

---

## 4. Grid Independence Study (ASME V&V 20)
Three structured/hex-dominant meshes were generated to conduct a spatial grid convergence study:
* **Coarse Mesh:** ~500k cells
* **Medium Mesh:** 1,073,531 cells
* **Fine Mesh:** ~2.79M cells

The Grid Convergence Index (GCI) was evaluated on the exit temperature profile at the $z = 5\text{ mm}$ line:
* **Grid Refinement Ratio ($r$):** 1.41
* **Fine Grid GCI ($GCI_{32}$):** **0.58%** (satisfying asymptotic grid convergence limits).

<p align="center">
  <img src="FIGURES/Figure_03_Computational_Mesh_Frontal.png" width="500" alt="Frontal View of Computational Mesh">
  <br>
  <em>Figure 2: Frontal view of the computational domain structured mesh, showing swirler nozzles and grid refinement.</em>
</p>

---

## 5. Key Results & Validation Summary
CFD evaluations show excellent agreement with the experimental DLR CARS dataset:

### Grid Independence Results (Table 2)
| Mesh Density | Peak Static Temperature | Peak Time-Averaged Temperature | Net Mass Imbalance |
| :--- | :---: | :---: | :---: |
| **Coarse Mesh** | 2099.47 K | 1847.31 K | -0.33% |
| **Medium Mesh** | 1959.00 K | 1763.78 K | -0.54% |
| **Fine Mesh** | 1927.84 K | 1943.74 K | -0.55% |

### Validation Statistics vs. Benchmark (Table 3)
| Model / Case | Peak Flame Temperature | Error vs. CARS (1950.00 K) | Physical Interpretation |
| :--- | :---: | :---: | :--- |
| **Analytical (No Dissociation)** | 2467.87 K | 26.56% | Theoretical adiabatic limit (complete conversion) |
| **Analytical (With Dissociation)** | 2380.66 K | 22.09% | Adiabatic equilibrium limit (dissociation included) |
| **Adiabatic CFD Model** | 2416.00 K | 23.90% | Fluent solver with adiabatic wall boundaries |
| **Fine Grid (Non-Adiabatic CFD)** | 1943.74 K | **0.32%** | Fluent solver with wall radiation/convective heat losses |
| **Experimental CARS (DLR)** | 1950.00 K | Reference Case | Physical combustor measurements |

<p align="center">
  <table>
    <tr>
      <td align="center" width="33%">
        <img src="FIGURES/Figure_08_Static_Temperature_Contour.png" width="240" alt="Static Temperature"><br>
        <strong>Static Temperature (K)</strong>
      </td>
      <td align="center" width="33%">
        <img src="FIGURES/Figure_09_Mean_Axial_Velocity_Contour.png" width="240" alt="Mean Axial Velocity"><br>
        <strong>Mean Axial Velocity (m/s)</strong>
      </td>
      <td align="center" width="33%">
        <img src="FIGURES/Figure_12_NO_Mass_Fraction_Contour.png" width="240" alt="NO Mass Fraction"><br>
        <strong>Nitric Oxide Mole Fraction</strong>
      </td>
    </tr>
  </table>
  <br>
  <em>Figure 3: Contour fields displaying reacting zone static temperature (left), mean axial velocity streamlines indicating CTRZ (middle), and nitric oxide (NO) mass fraction distributions (right).</em>
</p>

### Key Observations:
* **Curvature Correction:** The inclusion of Curvature Correction (SST-CC) is mandatory to capture the central vortex breakdown CTRZ (which extends **45 mm** axially and spans **22 mm** in diameter). Standard SST without CC fails to predict this stagnation boundary accurately.
* **Emissions & Heat Loss:** The peak temperature drops to 1927.84 K (instantaneous static temperature) and settles at 1943.74 K (time-averaged peak temperature) in the non-adiabatic case (0.32% error vs CARS). This demonstrates that the non-adiabatic model captures the radiative boundary conditions necessary for single-digit NOx calculations (exit concentration of **0.135 ppm**), preventing the order-of-magnitude overprediction seen in the adiabatic CFD model (2416 K, yielding 171.94 ppm NOx).

---

## 6. How to Run the Simulations
The reacting flow simulation is configured as a three-dimensional Fluent case:
1. Open **ANSYS Fluent** (3D, double-precision mode).
2. Read the mesh or case file from your database directory.
3. The boundary parameters can be verified or re-initialized using the boundary values defined in Section 2.
4. Run the calculation using:
   * **Coupled pressure-based solver** (pseudo-transient formulation).
   * **Second-order upwind schemes** for momentum, turbulent kinetic energy, and mixture fractions.
