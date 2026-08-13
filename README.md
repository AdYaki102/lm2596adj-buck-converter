# 24V to 3.3-20V, 3A LM2596 Adjustable Buck Converter 
A design of a compact, adjustable 3.3-20V, 3A LM2596 based buck converter created and build to explore complete process of hardware development, from schematics and simulation to PCB design and real world testing.

This file will reveal general structure of this project.
___
# Simulations
LTspice was used as a primary tool to test the circuit under worst case scenario loads and observe switching activity and stability of LM2596 module under these loads. Theoretical data from the simulations can be found in simulations branch of this repo.
Data measured is the following:

| Test load conditions | V<sub>out</sub> | V<sub>out</sub> Ripple|I<sub>L</sub> Ripple | I<sub>L</sub> peak | Efficiency |
|---|---|---|---|---|---|
| Target V / Load resistance Ω | Simulated Load Voltage in V | Simulated Load Ripple in V | Simulated Inductor Current Ripple in A | Simulated Peak Inductor Current | Efficiency P<sub>out</sub>/P<sub>in</sub> |
