# Design Calculations & Component Selection

This document describes the calculations and engineering decisions used
to select the major components of the LM2596T-ADJ buck converter.

The converter is intended to operate across a relatively wide output
voltage and load range. Consequently, component selection was performed
with the full operating range in mind rather than optimizing for a
single operating point.

---

# Inductor Selection

## LM2596T-ADJ Datasheet Nomograph

The LM2596T-ADJ datasheet provides a simplified method for selecting the
inductor using an `E × T` nomograph.

<img width="338" height="267" alt="image" src="https://github.com/user-attachments/assets/21999bfd-d51d-4234-b509-7ff4c5921e72" />

The value of `E × T` is determined from the input voltage, output voltage,
switching period, and voltage drops associated with the switching
elements.

<img width="489" height="54" alt="image" src="https://github.com/user-attachments/assets/07b5ad6b-1f82-41c2-b57a-e0055caf8855" />

For this design of LM2596, the relevant voltage drops were approximated as:

- Diode forward voltage: V<sub>D</sub> $\approx 0.55$ V
- Internal switch saturation voltage: V<sub>sat</sub> $\approx 1.16$ V

Computing the `E x T` for the range of operating values yields the following graph:

<img width="500" height="340" alt="image" src="https://github.com/user-attachments/assets/de86072e-ece1-42bb-b5c3-4a707a631a3f" />


### Worst-Case Operating Point

From the graph, the maximum value of `E x T` is 38.98 V*us at approximately 3 A and
11 V output. The nomograph indicates inductance values corresponding
approximately to:

- L39 → 47 µH
- L44 → 68 µH

Therefore, an inductance of approximately 47–68 µH would satisfy the
datasheet advise on selection procedure.

---

## Final selection

A **100 µH** inductor was selected instead of the minimum recommended
value.

The primary reason for selecting the larger inductance was to reduce
the inductor current ripple and extend the continuous-conduction
operating range toward lower load currents.

For an ideal buck converter, the peak-to-peak inductor current ripple
can be approximated by:

$$
\Delta I_L =
\frac{(V_{IN}-V_{OUT})D}{L f_S}
$$

where:

- $\Delta I_L$ = peak-to-peak inductor current ripple
- $V_{IN}$ = input voltage
- $V_{OUT}$ = output voltage
- $D$ = duty cycle
- $L$ = inductance
- $f_S$ = switching frequency

For an ideal buck converter:

$$
D \approx \frac{V_{OUT}}{V_{IN}}
$$

Therefore:

$$
\Delta I_L =
\frac{V_{OUT}}{L f_S}
\left(1-\frac{V_{OUT}}{V_{IN}}\right)
$$

Increasing the inductance from 47–68 µH to 100 µH reduces the expected
peak-to-peak ripple current for the same operating conditions.

For the worst-case operating point considered during component
selection:

$$
V_{IN}=24\text{ V}
$$

$$
V_{OUT}\approx11\text{ V}
$$

$$
f_S\approx150\text{ kHz}
$$

The ideal buck-converter duty cycle is approximately:

$$
D\approx\frac{V_{OUT}}{V_{IN}}
=\frac{11}{24}
\approx0.458
$$

This gives the following comparison:

| **Inductance** | **Peak-to-Peak Ripple $\Delta I_L$** |
|---:|---:|
| 47 µH | 0.845 A |
| 68 µH | 0.584 A |
| 100 µH | 0.397 A | 

The 100 µH inductor therefore reduces the calculated peak-to-peak
inductor ripple by approximately 53% compared with 47 µH:

$$
\frac{0.845-0.397}{0.845}\times100\ 
\approx53\
$$ 

Compared with 68 µH, the reduction is approximately 32%.

This reduction also lowers the approximate load current at which the
converter transitions from continuous conduction mode (CCM) to
discontinuous conduction mode (DCM):

$$
I_{OUT,crit}\approx\frac{\Delta I_L}{2}
$$

For the 47 µH inductor, the calculated CCM boundary is approximately
0.423 A, while the 100 µH inductor reduces this to approximately
0.199 A.

Therefore, the 100 µH selection provides substantially lower current
ripple and extends the expected CCM operating range toward lower load
currents.
The larger inductance also reduces the current swing around the average
load current, allowing the converter to remain in continuous conduction
down to a lower load current.

The final inductor was also selected based on its current rating. Having a 3 A maximum load
current and accounting for overhead and inrush currents, an inductor with 5 A saturation current was chosen.

---

## Tradeoffs 

The 100 µH inductor was not optimal in every aspect, since this design implies
rather large range of operating conditions. Thus the 100 µH value was 
chosen as a compromise favoring lower ripple and more continuous operation 
over minimum component size and price.

---

# Catch Diode Selection

The LM2596 requires a catch diode to conduct the inductor current while
the internal switch is turned off.

The datasheet specifies a minimum current rating of 1.3 times the maximum
load current. For a 3 A design, a diode should be rated for approximately:

$$
I_{D,rated} \geq 1.3(3A)=3.9A
$$

A **5 A, 40 V Schottky diode** was therefore selected.

A Schottky diode is particularly beneficial because of its low forward voltage and
very low reverse-recovery behavior.

The low forward voltage reduces conduction losses when the diode is
carrying the inductor current:

The 5 A rating provides additional current margin above the calculated
minimum requirement.

The 40 V reverse-voltage rating also provides margin above the nominal
24 V input voltage.

---

#  Output Capacitor

##  Capacitor Selection

The LM2596T-ADJ datasheet recommends a low-ESR electrolytic output
capacitor within 82µF - 820µF.

220 µF, 50 V low-ESR electrolytic capacitor was chosen for this design

A larger capacitance is not automatically better for a switching
converter.

A smaller capacitor, such as 82-120 µF, would reduce size and stored
energy but provide less filtering and transient energy storage.

A very large capacitor would provide additional filtering but would
increase physical size and startup requirements without necessarily
providing a proportional improvement in performance.

220 µF was therefore selected as a practical middle ground between
filtering performance, transient response, physical size, and cost.
Additionally 100nF small ceramic capacitor was placed in parallel 
for high frequency filtering.

---

## Low ESR

Low equivalent series resistance (ESR) is particularly important for
the LM2596 output capacitor.

The capacitor's ESR contributes directly to output ripple:

$$
V_{ESR,ripple} \approx \Delta I_L \cdot ESR
$$

Therefore, reducing ESR reduces the portion of output ripple generated
by the capacitor's internal resistance.

The total output ripple is influenced by both the capacitor's impedance
and the inductor current ripple. Selecting a low-ESR capacitor helps
reduce the output-voltage ripple produced by the switching current.

---

# Input Filtering

The input stage uses both bulk and high-frequency filtering.

The primary input capacitor provides energy storage for the switching
converter, while the smaller ceramic capacitor provides a low-impedance
path for high-frequency switching currents.

The design uses:

- 470 µF bulk electrolytic capacitor
- 100 nF ceramic bypass capacitor

The 470 µF capacitor handles relatively low-frequency input-current
variation and reduces input-voltage fluctuations.

The 100 nF ceramic capacitor has significantly lower parasitic
inductance and impedance at high frequencies, making it more effective
at suppressing high-frequency switching transients.


---

#  Feed-Forward Capacitor

A small feed-forward capacitor was included in the feedback network to
improve behavior for output voltages greater than 10V. 

The selected value is 820pF

The LM2596 datasheet provides recommended feedforward capacitor values
as a function of output voltage. 

| Output Voltage | Recommended $C_{FF}$ |
|---:|---:|
| 12 V | 1 nF |
| 15 V | 680 pF |
| 24 V | 560 pF |
| 28 V | 390 pF |

Since those are recommended values for static designs, the middle value of 820pF was chosen based on this data
and further simulated in LTspice to ensure stability at any point of operation.

---

#  Feedback Resistors

The LM2596T-ADJ regulates the output by comparing the feedback voltage
against its internal reference.

The output voltage is determined by the feedback resistor divider:


$$
V_{OUT}=V_{REF}
\left(1+\frac{R_2}{R_1}\right)
$$

where $V_{REF}$ is the LM2596 feedback reference voltage.

## Feedback Resistor Network

The resistor network is designed around $$20\\text{k}\Omega$$ potentiometer. 

The design target is an adjustable output range of approximately
3.3–20 V.

For the feedback network, $R_1$ was selected as:

$$
R_1=1.00\\text{k}\Omega
$$

The required value of the upper feedback resistance can therefore be
determined from the desired output voltage.

---

### Determining the Minimum Output Voltage

The minimum output voltage is targeted at 3.3 V. Rearranging the
feedback equation to solve for $R_2$:

$$
R_2=R_1\left(\frac{V_{OUT}}{V_{FB}}-1\right)
$$

For the minimum output:

$$
R_{2,\min} =
(1.00\\text{k}\Omega)
\left(
\frac{3.3}{1.23}-1
\right)
$$

$$
R_{2,\min}\approx1.683\\text{k}\Omega
$$

A standard 1.69 kΩ, 1% resistor was therefore selected.

Using the actual selected value:

$$
V_{OUT,\min} =
1.23\left(1+\frac{1.69\\text{k}\Omega}
{1.00\\text{k}\Omega}\right)
$$

$$
\boxed{V_{OUT,\min}\approx3.31\text{ V}}
$$

This establishes the lower end of the desired adjustment range.

---

### Determining the Maximum Output Resistance

The same equation can be used to determine the total feedback
resistance required to reach 20 V:

$$
R_{2,\max} =
R_1\left(
\frac{20}{1.23}-1
\right)
$$

$$
R_{2,\max} =(1.00\\text{k}\Omega)
\left(
\frac{20}{1.23}-1
\right)
$$

$$
\boxed{R_{2,\max}\approx15.26\\text{k}\Omega}
$$

Therefore, after accounting for the 1.69 kΩ fixed resistor, the
adjustable portion of the feedback network needs to provide
approximately:

$$
R_{ADJ} =
15.26\\text{k}\Omega-1.69\\text{k}\Omega
$$

$$
\boxed{R_{ADJ}\approx13.57\\text{k}\Omega}
$$

However, using the full 20 kΩ potentiometer would produce too large a
maximum feedback resistance. A resistor was therefore placed in
parallel with the potentiometer to limit its effective maximum
resistance.

---

### Determining the Parallel Resistor

The effective resistance of the potentiometer and parallel resistor is:

$$
R_{ADJ}=R_V\parallel R_P
$$

or:

$$
R_{ADJ} =
\frac{R_VR_P}{R_V+R_P}
$$

Solving for the required parallel resistor:

$$
R_P=
\frac{R_VR_{ADJ}}
{R_V-R_{ADJ}}
$$

Using:

$$
R_V=20\\text{k}\Omega
$$

and the required:

$$
R_{ADJ}\approx13.57\\text{k}\Omega
$$

gives:

$$
R_P \approx
\frac{(20)(13.57)}
{20-13.57}
\\text{k}\Omega
$$

$$
R_P\approx42.2\\text{k}\Omega
$$

A standard **41.2 kΩ, 1% resistor** was selected as the closest
practical value.

The resulting effective maximum resistance is:

$$
R_{ADJ,\max} =
20\\text{k}\Omega\parallel41.2\\text{k}\Omega
$$

$$
R_{ADJ,\max} =
\frac{(20)(41.2)}
{20+41.2}
\\text{k}\Omega
$$

$$
\boxed{R_{ADJ,\max}\approx13.46\\text{k}\Omega}
$$

Therefore, the maximum total feedback resistance is:

$$
R_{2,\max} =
1.69\\text{k}\Omega+13.46\\text{k}\Omega
$$

$$
R_{2,\max}\approx15.15\\text{k}\Omega
$$

The resulting maximum output voltage is:

$$
V_{OUT,\max} =
1.23
\left(
1+\frac{15.15\\text{k}\Omega}
{1.00\\text{k}\Omega}
\right)
$$

$$
\boxed{V_{OUT,\max}\approx19.87\text{ V}}
$$

---

#  Summary of Major Component Decisions

| Component | Selected Value | 
|---|---:|
| Inductor | 100 µH / 5 A Saturation|  
| Catch diode | 5 A / 40 V Schottky | 
| Output capacitor | 220 µF / 50 V | 
| Input  capacitor | 470 µF / 50 V | 
| Ceramic capacitors | 100 nF / 50 V ceramic | 
| Feed-forward capacitor | 820 pF | 
| Feedback resistors | 1.69 kΩ + resistor network | 

---
