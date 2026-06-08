# Open-Source GaN HEMT Verilog-A Compact Model

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Simulator: NGSpice](https://img.shields.io/badge/Simulator-NGSpice-green.svg)
![Compiler: OpenVAF](https://img.shields.io/badge/Compiler-OpenVAF-orange.svg)

A highly scalable, nonlinear, and self-heating capable compact model for Gallium Nitride (GaN) High-Electron-Mobility Transistors (HEMTs). This model is written in Verilog-A and has been specifically hardened and optimized for open-source EDA workflows (OpenVAF + NGSpice).

**Reference Publication:**  
Zhao Li *et al.*, "A Nonlinear Scalable Model for Gallium Nitride (GaN) HEMTs Including Self-Heating Effect," *2022 IEEE International Conference on Microwave and Millimeter Wave Technology (ICMMT)*.  
[DOI: 10.1109/ICMMT55580.2022.10022916](https://ieeexplore.ieee.org/document/10022916)

---

## 📖 Overview

This repository provides a fully functional, open-source implementation of a modified Angelov-based GaN HEMT compact model. It is designed for RF and power electronics researchers who need a robust, scalable device model without relying on expensive commercial EDA tools.

### Key Features
*   **Highly Scalable:** Accurately scales with the number of gate fingers ($N_g$) and unit gate width ($W_g$).
*   **Self-Heating Dynamics:** Includes an integrated thermal subcircuit that dynamically models channel temperature rise ($\Delta T$) and accurately predicts DC I-V thermal droop.
*   **Open-Source EDA Ready:** Hardened and stripped of proprietary commercial attribute tags to compile flawlessly in microseconds using **OpenVAF** for simulation in **NGSpice**.
*   **Robust Convergence:** Includes internal mathematical safeguards (e.g., thermal node $G_{min}$ shunts, hyperbolic function limits, and transient timestep bounds) to prevent simulator floating-point errors and `NaN` crashes.

---

## 🛠️ Prerequisites

To compile and run this model, you need a Linux environment (or Windows Subsystem for Linux - WSL) with the following open-source tools:

1.  **OpenVAF (v23.5.0 or newer):** The high-performance Verilog-A compiler. [Download here](https://openvaf.semimod.de/).
2.  **NGSpice (v38 or newer):** The circuit simulator. *Note: v38+ is strictly required for OSDI (Open Source Device Interface) support.*

---

## 🚀 Getting Started

### 1. Clone the Repository
Clone this repository to your local machine:
```bash
git clone https://github.com/YOUR-GITHUB-USERNAME/GaN-HEMT-VerilogA-Model.git
cd GaN-HEMT-VerilogA-Model
```

### 2. Compile the Model (Crucial Step)
Because system architectures differ (Windows vs. Linux vs. macOS), the compiled binary (`.osdi`) is **not** included in this repository. You must compile the raw Verilog-A physics engine yourself for your specific OS:
```bash
openvaf gan_hemt.va
```
*Success:* A `gan_hemt.osdi` file will be generated in your directory.

---

## 📊 Phase 1: DC Characterization & Validation

The repository includes four testbenches to validate the DC I-V characteristics of the device against Figure 2(a-d) of the reference IEEE paper.

To run a simulation, use NGSpice in batch mode:
```bash
ngspice -b test_dc_2x200.spice
ngspice -b test_dc_4x200.spice
ngspice -b test_dc_2x250.spice
ngspice -b test_dc_4x250.spice
```

These scripts will automatically output raw data texts (`.txt`) and scalable vector graphics (`.svg`) of the $I_{ds}$ vs. $V_{ds}$ curves.

### Verified Results
Below are the DC validation curves showing accurate thermal droop and knee voltages across all four device geometries.

*(Note: The SVGs show the raw model output directly matching the publication data)*

| 2 × 200 µm Reference Device | 4 × 200 µm Scaled Device |
| :---: | :---: |
| <img src="[./dc_2x200.svg]" width="400" alt="2x200 DC Curve"> | <img src="[./dc_4x200.svg]" width="400" alt="4x200 DC Curve"> |
| **2 × 250 µm Scaled Device** | **4 × 250 µm Scaled Device** |
| <img src="[./dc_2x250.svg]" width="400" alt="2x250 DC Curve"> | <img src="[./dc_4x250.svg]" width="400" alt="4x250 DC Curve"> |

---

## 💻 How to Use in Your Own Circuits

To use this device in your custom NGSpice netlists, follow this standard structure:

```spice
* 1. Load the compiled library (Must be the first control block)
.control
pre_osdi gan_hemt.osdi
.endc

* 2. Define the Model (Specify geometry here)
* Ng = Number of fingers, Wg = Finger width in meters
.model my_gan_device gan_hemt Ng=4 Wg=250e-6

* 3. Instantiate the Transistor
* Nodes: Drain Gate Source ModelName
N1  drain_node  gate_node  0  my_gan_device
```

---

## 🎛️ Model Parameters Reference

While the model handles complex nonlinear scaling internally, you can customize the geometry and toggle self-heating via model parameters.

### Geometry Parameters (Scalability)
| Parameter | Default | Description |
|-----------|---------|-------------|
| `Ng` | 2 | Number of gate fingers |
| `Wg` | 200e-6 | Gate finger width [m] |
| `Ng_ref` | 2 | Reference finger count |
| `Wg_ref` | 200e-6 | Reference finger width [m] |

### Core DC & Thermal Parameters
*(For a full list of all 30+ intrinsic and parasitic parameters extracted for the 2x200µm device, see the `gan_hemt.va` source code)*

| Parameter | Default | Description |
|-----------|---------|-------------|
| `selfheat` | 1 | Enable (1) or disable (0) self-heating simulation |
| `Rth` | 21.075 | Thermal resistance [K/W] |
| `Cth_val` | 2.35e-14 | Thermal capacitance [J/K] |
| `Ipk` | 0.231 | Peak drain current [A] |
| `Vpk` | 0.081 | Gate voltage at peak gm [V] |

---

## 📜 Citation & Academic Use

If you use this open-source model in your academic work or research, please ensure you cite the original authors of the physics formulation:

> Z. Li *et al.*, "A Nonlinear Scalable Model for Gallium Nitride (GaN) HEMTs Including Self-Heating Effect," *2022 IEEE International Conference on Microwave and Millimeter Wave Technology (ICMMT)*, 2022.

---

## 📄 License
This project is open-source and released under the **MIT License**. See the `LICENSE` file for details.
