---
title: "Concrete Material Modeling and ANSYS Implementation Using SOLID65 Elements"
date: 2025-02-27
permalink: /posts/2025/02/ansys-solid65/
tags:
  - ANSYS
  - Concrete
  - SOLID65
  - Material Modeling
  - Finite Element Analysis
---

# **Concrete Material Modeling and ANSYS Implementation Using SOLID65 Elements**

## **1. Overview of Concrete Material Properties**
Concrete is a **quasi-brittle** material that exhibits **nonlinear stress-strain behavior**, cracking under tension, and crushing under compression. In ANSYS, the **SOLID65** element is used to model **reinforced and unreinforced concrete**, capturing:

- **Cracking in tension**
- **Crushing in compression**
- **Shear transfer across cracks**
- **Plastic deformation**

This post covers:

- **Material property definitions (Eurocode, ACI)**
- **Drucker-Prager and MISO models**
- **Stress-strain relationship**
- **Tensile strength and shear transfer**
- **Example calculations for \( f_c = 40 \) MPa**
- **Interpolation for cohesion and friction angles**
- **Full ANSYS implementation**

---

## **2. Key Material Parameters for Concrete in ANSYS Using SOLID65 Elements**
For accurate simulation, the following material properties must be defined in ANSYS:

1. **Density (\( \rho \))**
2. **Elastic modulus (\( E \)) and Poisson’s ratio (\( \nu \))**
3. **Yield surface**:
   - Drucker-Prager (DP) parameters: **cohesion (c), friction angle (\( \phi \))**
   - MISO parameters: **stress-strain curve**
4. **Concrete-specific properties (cracking and shear transfer)**
5. **Tensile strength (\( f_t \))**

---

## **3. Density of Concrete**
Concrete density typically ranges from **2300 - 2500 kg/m³**. A common value is:

\[
\rho = 2400 \text{ kg/m}^3
\]

### **ANSYS Implementation**
```apdl
MP,DENS,1,2400  ! Density of Concrete (kg/m³)
```

---

## **4. Elastic Modulus and Poisson’s Ratio**
### **(a) Eurocode Formula**
\[
E_c = 22 \times \left(\frac{f_{cm}}{10} \right)^{0.3} \text{ GPa}
\]
where:

- \( f_{cm} = f_c + 8 \) MPa (mean compressive strength)

For **\( f_c = 40 \) MPa**:

\[
f_{cm} = 40 + 8 = 48 \text{ MPa}
\]
\[
E_c = 22 \times \left(\frac{48}{10}\right)^{0.3} = 34.08 \text{ GPa}
\]

### **(b) ACI Formula**
\[
E_c = 4700 \sqrt{f_c} \text{ MPa}
\]
For **\( f_c = 40 \) MPa**:

\[
E_c = 4700 \times \sqrt{40} = 29718 \text{ MPa} = 29.7 \text{ GPa}
\]

### **ANSYS Implementation**
```apdl
MP,EX,1,34080  ! Elastic Modulus from Eurocode (MPa)
MP,EX,1,29718  ! Elastic Modulus from ACI (MPa)
MP,PRXY,1,0.2  ! Poisson's Ratio
```

---

## **5. Yield Surface**
### **(a) Drucker-Prager (DP) Model**
The **Drucker-Prager yield function** is defined as:

\[
f = \alpha I_1 + J_2^{0.5} - k
\]

where:

- **Cohesion (\( c \))**
- **Internal friction angle (\( \phi \))**

### **Updated Table from Ertekin Öztekin et al.**
| \( f_c \) (MPa) | Cohesion \( c \) (MPa) | Internal Friction Angle \( \phi \) (°) |
|----------------|-----------------|---------------------------|
| 22.2          | 5.25            | 27.5                      |
| 30.2          | 8.14            | 29.5                      |
| 41.2          | 10.85           | 30.9                      |
| 51.9          | 12.40           | 32.8                      |
| 70.1          | 16.22           | 36.7                      |

### **Python Code for Interpolation (\( f_c = 40 \) MPa)**
```python
import numpy as np
from scipy.interpolate import interp1d

fc_values = [22.2, 30.2, 41.2, 51.9, 70.1]
cohesion_values = [5.25, 8.14, 10.85, 12.40, 16.22]
friction_values = [27.5, 29.5, 30.9, 32.8, 36.7]

interp_cohesion = interp1d(fc_values, cohesion_values, kind='linear')
interp_friction = interp1d(fc_values, friction_values, kind='linear')

fc_target = 40
c_interp = interp_cohesion(fc_target)
phi_interp = interp_friction(fc_target)

print(f'Cohesion (c) for fc = {fc_target} MPa: {c_interp:.2f} MPa')
print(f'Internal Friction Angle (phi) for fc = {fc_target} MPa: {phi_interp:.2f}°')
```

---

## **7. Example: Complete ANSYS Code**
```apdl
/PREP7
MP,EX,1,34080  ! Elastic Modulus (MPa)
MP,PRXY,1,0.2  ! Poisson's Ratio
MP,DENS,1,2400  ! Density (kg/m³)

! Drucker-Prager Parameters
TB,DP,1
TBDATA,1,10.64,30.75,0  ! Cohesion, Friction Angle, Dilatancy Angle

! Tensile Strength
MP,FT,1,3.47  ! Eurocode Tensile Strength

! Shear Transfer
TB,CONC,1
TBDATA,1,0.3,0.8,ft,-1

FINISH
/SOLU
SOLVE
/POST1
PLNSOL,U,SUM
```

---

## **Conclusion**
This document provides a **detailed framework for defining concrete properties in ANSYS**, incorporating **experimental data, standard equations, and interpolation methods**. The **full ANSYS implementation** ensures accurate simulation of **concrete behavior using the SOLID65 element**.

