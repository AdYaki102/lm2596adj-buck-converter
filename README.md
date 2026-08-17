# 24V to 3.3-20V, 3A LM2596T-ADJ Adjustable Buck Converter 
> A compact, low-ripple adjustable DC-DC converter based on the LM2596T-ADJ, converting a 24 V DC input to an adjustable 3.3–20 V output at up to 3 A.

## Overview
This project documents the complete process of development, from initial circuit design and simulation through PCB
layout, fabrication, assembly, and hardware characterization.

The primary goals were to:

- Design a 24 V to 3.3–20 V adjustable buck converter
- Support output currents up to 3 A
- Select power components based on worst-case electrical stresses
- Validate the design using LTspice simulations
- Design and fabricate a custom two-layer PCB
- Compare simulated performance with measured hardware performance and investigate differences

---
## Specifications

| Parameter | Target |
|---|---:|
| Input voltage | 24 V DC |
| Output voltage | 3.3–20 V |
| Maximum output current | 3 A |
| Controller | LM2596T-ADJ |
| Switching frequency | ~150 kHz |
___

## System Schematic
<img width="1076" height="302" alt="image" src="https://github.com/user-attachments/assets/17bd7b1d-2620-45ed-88bf-f6059aeb8df5" />

---
# Design

## Topology

The converter uses a buck topology to step down the 24 V input. The LM2596T-ADJ regulates the output by controlling the switching duty cycle. The inductor and output capacitor filter the switching waveform and deliver a relatively continuous output voltage to the load. Additional ceramic capacitors provide high-frequency bypassing and reduce high-frequency switching noise.

---
## Component selection

Process of the component selection was based on the LM2596ADJ official datasheet and the worst-case scenario for particular target voltage/load, while taking in consideration tradeoffs for other voltage/load scenarios. Detailed calculations and reasons for selecting every component can be found in calculations. Switching loop components will be briefly discussed here.

---
## Switching Loop
### Inductor
LM2596 datasheet offers a simplified procedure for choosing an inductor using a provided nomograph. 

Based on the worst case scenario current load of 3A at around 11V the inductor of choice should be L39 or L44, they correspond to 47uH and 68uH inductors respectively. Nevertheless, 100uH inductor was chosen to reduce peak-to-peak inductor ripple and extend continuous conduction operating range at low output currents and high output voltages. Effect of higher inductance is evaluated quantitatively in calulations file of this repo.

---

### Diode

According to the datasheet the catch diode must be rated at least 1.3 times greater the load current (3A in this design). To have a considerable overhead and fast recovery time, essential for smooth operation of the switcher, 5A 40V Schottky Diode was chosen.

---
### C<sub>out</sub>

The LM2596T-ADJ datasheet specifies a low-ESR electrolytic output capacitor within the recommended capacitance range of 82 µF to 820 µF. A 470 µF, 50 V electrolytic capacitor was selected to provide substantial output filtering while remaining within the recommended range.

---

## Simulations
LTspice was used to evaluate the converter under a range of operating conditions, with particular emphasis on worst-case loads. Simulations were used to evaluate startup behavior, switching activity, inductor current, output ripple, and efficiency, as well as to identify potential component stresses and stability issues.
Detailed simulation results are available in simulations file of this repo.
Following are the results of final simulation.

| **Test** | **V<sub>out</sub> (V)** | **ΔV<sub>out</sub> (V)** | **ΔI<sub>L</sub> (A)** | **I<sub>L,peak</sub> (A)** | **Efficiency** |
| :---: | ---: | ---: | ---: | ---: | ---: |
| 3.3 V / 100 Ω | 3.32 | 0.01 | 0.29 | 0.27 | 64.0% |
| 3.3 V / 1.1 Ω | 3.31 | 0.04 | 0.42 | 3.24 | 79.7% |
| 5 V / 100 Ω | 5.00 | 0.02 | 0.19 | 0.19 | 68.4% |
| 5 V / 1.67 Ω | 5.00 | 0.04 | 0.40 | 3.20 | 81.8% |
| 12 V / 100 Ω | 12.00 | 0.04 | 0.36 | 0.36 | 73.4% |
| 12 V / 4 Ω | 11.97 | 0.04 | 0.38 | 3.22 | 88.6% |
| 20 V / 100 Ω | 19.98 | 0.02 | 0.22 | 0.37 | 75.2% |
| 20 V / 13.3 Ω | 19.98 | 0.02 | 0.19 | 1.66 | 92.1% |
| 20 V / 6.67 Ω | 19.84 | 0.30 | 0.15 | 3.13 | 90.2% |

---

## PCB Design
The PCB was designed in KiCad, with focus on maintaining compactness and minimizing parasitic effects in the switching power stage.
Because the converter operates at around 150kHz switching frequency, the layout was designed around minimizing high current loop areas and
maintaining short current paths.

---

### Component placement and Routing 
Component placement was focused around creating shortest practical current paths through the switching loop. 
The LM2596T-ADJ, Schottky diode, and inductor were placed close together to minimize the area of the high-current switching path. The input
capacitors were positioned close to the regulator input and ground connections to reduce high-frequency parasitic inductance, by allowing shorter switching loop connections. Additionally, bottom plane was designed as continuous ground, creating a natural return path which minimizes area of current loop even further.
Feedback network was placed further away from switching node to minimize possibility of switching noise coupling into the feedback signal.
High current paths were routed using wider copper traces to reduce resistive losses and parasitic inductance. 

---
### Pictures
<img width="980" height="800" alt="image" src="https://github.com/user-attachments/assets/8827cde0-c4cb-4879-a30e-af4a5cf3cdd3" /> 
 <p align="center"><b>Figure 1: PCB Layout</b></p>




<img width="980" height="800" alt="image" src="https://github.com/user-attachments/assets/28f003c8-d803-4d0b-b644-33edd4ac0b14" /> 

<p align="center"><b>Figure 2: PCB Front</b></p>

<img width="980" height="800" alt="image" src="https://github.com/user-attachments/assets/43f5219c-0205-479d-8790-6c540c272b72" />
<p align="center"><b>Figure 3: PCB Back</b></p>
