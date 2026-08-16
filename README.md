# 24V to 3.3-20V, 3A LM2596 Adjustable Buck Converter 
> A compact adjustable low ripple DC-DC Converter designed around LM2596-ADJ,
> converting 24V to an adjustable 3.3-20V at up to 3A.

## Overview
This project documents the complete process of development, from initial circuit design and simulation through PCB
layout, fabrication, assembly, and hardware characterization.

The primary goals were to:

- Design a 24 V to 3.3–20 V adjustable buck converter
- Support output currents up to 3 A
- Select power components based on worst-case electrical stresses
- Validate the design using LTspice simulations
- Design and fabricate a custom two-layer PCB
- Compare simulated performance with measured hardware performance

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
          24V DC -> Input Filtering -> LM2596T-ADJ -> Buck Topology + Feedback -> Output Filtering -> Load 

---
# Design

## Topology

The converter uses buck topology to step down voltage. LM2596T-ADJ controls the duty cycle to regulate the output voltage. Inductor and a capacitor is used to smooth out the switching waveform. Additional capacitors used to smooth out the noise.

## Component selection

Process of the component selection was based on the LM2596ADJ official datasheet and the worst-case scenario for particular target voltage/load, while taking in consideration tradeoffs for other voltage/load scenarios.

---
## Switching Loop
### Inductor
LM2596 datasheet offers a simplified procedure for choosing an inductor using a provided nomograph. 

<img width="338" height="267" alt="image" src="https://github.com/user-attachments/assets/21999bfd-d51d-4234-b509-7ff4c5921e72" />

Calculating the E*T for desired range of operating conditions:

<img width="489" height="54" alt="image" src="https://github.com/user-attachments/assets/07b5ad6b-1f82-41c2-b57a-e0055caf8855" />

Where:
- V<sub>d</sub>= diode forward voltage drop = 0.55V 
- V<sub>sat</sub>= internal switch saturation voltage=1.16V

Yields the following graph:

<img width="200" height="140" alt="image" src="https://github.com/user-attachments/assets/de86072e-ece1-42bb-b5c3-4a707a631a3f" />

Based on the worst case scenario current load of 3A the inductor of choice should be L39 or L44, they correspond to 47uH and 68uH inductors respectively. Nevertheless, 100uH inductor was chosen to reduce peak-to-peak inductor ripple and provide extra margin against entering discontinuous conduction mode at low output currents and high output voltages. 

## Simulations
LTspice was used as a primary tool to test the circuit under worst case scenario loads and observe switching activity and stability of LM2596 module under these loads. Theoretical data from the simulations can be found in simulations branch of this repo.
Data measured is the following:

| Test load conditions | V<sub>out</sub> | V<sub>out</sub> Ripple|I<sub>L</sub> Ripple | I<sub>L</sub> peak | Efficiency |
|---|---|---|---|---|---|
| Target V / Load resistance Ω | Simulated Load Voltage in V | Simulated Load Ripple in V | Simulated Inductor Current Ripple in A | Simulated Peak Inductor Current | Efficiency P<sub>out</sub>/P<sub>in</sub> |
