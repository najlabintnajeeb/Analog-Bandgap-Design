
## Band Gap Voltage Reference (BGR)

### Introduction:
In this lecture,the Band Gap Voltage Reference (BGR), including its purpose, working principle, components, and applications were discussed.

#### Overview of the Band Gap Voltage Reference:
- A band gap voltage reference provides a stable reference voltage that remains constant over temperature variations.
- Typically, the output voltage is around 1.2 V, which corresponds closely to the silicon band gap energy at 0 K.
- The block diagram of a band gap reference shows a supply connected to VDD and ground, with no additional input, producing a temperature-independent reference voltage.

  <img width="614" height="275" alt="Screenshot 2025-12-15 at 8 31 21 am" src="https://github.com/user-attachments/assets/12d4454e-27dc-4829-b116-db8e929a9ae5" />

#### Why Use a Band Gap Voltage Reference?
Alternative voltage reference options include:
- Battery:Voltage drops over time, making it unreliable.
- Power Supply:Output contains noise and ripple, which is unsuitable for precise reference applications.
- Voltage Reference IC zener diode:
        * Requires additional components for operation.
        * Higher thermal noise.
        * Low-voltage ICs may not be available.
Solution to all these is BGR which can be integrated :
Can be easily integrated in bulk CMOS, bi CMOS and bi polar technologies without external components.

#### Applications of BandGap Voltage Reference:

Band gap voltage references are widely used in electronic systems where a **stable, temperature-independent reference voltage** is critical. Common application areas include:

<details>
  <summary><strong>LDO (Low Dropout Regulators)</strong></summary>

  <p>
  Band gap references provide a precise reference voltage for LDO regulators,
  enabling accurate output regulation and improved line and load regulation
  performance.
  </p>

  <img width="437" height="441" alt="LDO Block Diagram"
       src="https://github.com/user-attachments/assets/391c009a-2437-4930-bc0c-bb692507683e" />
</details>
<details>
  <summary><strong>DC–DC Converters </strong></summary>

  <p>
  Used to generate stable reference voltages for feedback control and regulation loops.
  </p>

  <img width="519" height="441" alt="Screenshot 2025-12-15 at 10 04 46 am" src="https://github.com/user-attachments/assets/69d00bea-e257-4214-a907-d59a4bfa36cd" />

</details>
 
<details>
  <summary><strong>ADC (Analog-to-Digital Converters)</strong></summary>

  <p>
  Provides a precise reference voltage that defines the conversion accuracy and resolution.
  </p>
  
<img width="418" height="463" alt="Screenshot 2025-12-15 at 10 16 28 am" src="https://github.com/user-attachments/assets/ca29b239-816c-4a55-82f6-0ddf8deeeae7" />

  
</details> 
<details>
  <summary><strong>DAC (Digital-to-Analog Converters)</strong></summary>

  <p>
 Ensures accurate and linear analog output corresponding to digital input codes.  </p>

  <img width="562" height="432" alt="Screenshot 2025-12-15 at 10 16 41 am" src="https://github.com/user-attachments/assets/0a2dea56-cab4-43a4-85c5-615cbe22e33f" />

</details> 

---

#### Principle of Operation

The output voltage of a **Band Gap Reference (BGR)** remains constant over temperature by combining two voltages with opposite temperature dependencies:

* **CTAT (Complementary to Absolute Temperature)** voltage
* **PTAT (Proportional to Absolute Temperature)** voltage

When a CTAT voltage (negative temperature slope) is added to a scaled PTAT voltage (positive temperature slope), the temperature variations cancel each other, resulting in a **temperature-independent reference voltage**.

<img width="908" height="544" alt="Screenshot 2025-12-15 at 10 44 23 am" src="https://github.com/user-attachments/assets/8e95ff22-a976-4953-bc68-de4ce19f6007" />


---

## CTAT and PTAT Behavior

* **CTAT Voltage:**
  Decreases with increasing temperature (negative slope).

* **PTAT Voltage:**
  Increases linearly with temperature (positive slope).

<img width="819" height="541" alt="Screenshot 2025-12-15 at 10 44 45 am" src="https://github.com/user-attachments/assets/002a3f8a-fdcc-4c0b-a331-6baaf003bf4f" />


By proper scaling and summation, the combined voltage becomes nearly constant across temperature.

---

## Generation of CTAT Voltage

### CTAT Using Diode or BJT

* CTAT voltage can be generated using:

  * A **diode**
  * A **BJT connected in diode mode**

In IC design, a **BJT in diode-connected configuration** is preferred.

### Why BJT Instead of a Simple PN Diode?

* In CMOS processes, the **P-substrate is tied to ground**, making simple PN diode implementations problematic(fig a).
* Alternative diode structures can introduce **parasitic BJTs**, causing unwanted substrate currents (fig b).
* A **vertical PNP BJT** avoids these issues by confining collector current locally and preventing substrate interference (fig c).

Thus, **PNP BJTs are commonly used** to generate CTAT voltages in band gap references.

<img width="1122" height="760" alt="Screenshot 2025-12-15 at 10 27 49 am" src="https://github.com/user-attachments/assets/74473e0b-f2bd-446f-a891-76136e73b471" />

---

#### CTAT Voltage Characteristics


##### 1. The Circuit Diagram (Top Left)

The diagram shows a simple BJT circuit configuration:
V<sub>DD</sub>: The supply voltage.

I<sub>O</sub> A constant current source that supplies the collector current to the transistor.

Q1 : A Bipolar Junction Transistor (BJT) connected as a diode-connected transistor (base and collector are tied together).
V<sub>CTAT</sub>: The voltage across the diode-connected BJT, which is the base-emitter voltage (V<sub>BE</sub>). This voltage is often referred to as V<sub>CTAT</sub> because it exhibits a Complementary to Absolute Temperature characteristic.
Function: This circuit generates a voltage V<sub>CTAT</sub> that decreases linearly as the temperature increases.

###### 2. The Graph (Top Center)
The graph plots the output voltage V<sub>CTAT</sub>  against Temperature (Temp):

Y-axis: V<sub>CTAT</sub> (Voltage)

X-axis: Temp (Temperature)
The Line: A straight line with a negative slope, confirming the CTAT characteristic.
Slope Label: Slope = −2mv/dg-cent. (−2mV/∘C). This indicates the rate at which V<sub>CTAT</sub>  decreases for every one-degree Celsius increase in temperature. This value is typical for the V<sub>BE</sub> of silicon transistors around room temperature.

## Mathematical Analysis of CTAT Voltage

<img width="1361" height="770" alt="Screenshot 2025-12-15 at 10 31 43 am" src="https://github.com/user-attachments/assets/553d98af-e512-4203-9832-08e1848277d7" />

Where:

* (V_D): CTAT voltage
* (V_T = \frac{kT}{q}): Thermal voltage
* (I_0): Bias current (assumed constant)
* (I_S): Reverse saturation current
V<sub>T</sub> = kT / q  

I<sub>D</sub> = I<sub>S</sub> · exp(V<sub>BE</sub> / V<sub>T</sub>)  

V<sub>BE</sub> = V<sub>T</sub> · ln(I<sub>0</sub> / I<sub>S</sub>)

As temperature increases:
- I<sub>S</sub> increases rapidly
- V<sub>BE</sub> decreases → CTAT behavior




---


In practice:

* Typical values range from **−1.6 to −1.9 mV/°C**
* Depends on operating current and device parameters

---

##### 3. The Equations (Right Side and Bottom)

The following equations provide the mathematical foundation for the behavior observed in the circuit and the temperature plot.

---

### A. Diode / BJT Current and Voltage Relationships

I<sub>D</sub> = I<sub>S</sub> · e<sup>V<sub>D</sub> / V<sub>t</sub></sup>  

This is the diode (or BJT collector) current equation.

Where:
- I<sub>D</sub> : Diode current (or I<sub>C</sub> for the BJT)  
- V<sub>D</sub> : Diode voltage (or V<sub>BE</sub> for the BJT, which is V<sub>CTAT</sub>)  
- I<sub>S</sub> : Saturation current (highly temperature-dependent)  
- V<sub>t</sub> : Thermal voltage  

Rearranging the equation and replacing I<sub>D</sub> with the constant current I<sub>0</sub>:

V<sub>D</sub> = V<sub>t</sub> · ln(I<sub>0</sub> / I<sub>S</sub>)  

This expression represents the CTAT voltage, where V<sub>CTAT</sub> = V<sub>D</sub>.

The thermal voltage is given by:

V<sub>t</sub> = kT / q  

Where:
- k : Boltzmann constant  
- T : Absolute temperature (Kelvin)  
- q : Elementary charge  

This confirms that V<sub>t</sub> is directly proportional to absolute temperature.

---

### B. Temperature Dependence of Parameters

The saturation current is given by:

I<sub>S</sub> = A μ<sub>0</sub> kT n<sub>i</sub><sup>2</sup>  

Where:
- A is a constant  
- μ is carrier mobility  
- n<sub>i</sub> is the intrinsic carrier concentration  

Carrier mobility varies with temperature as:

μ ∝ μ<sub>0</sub> T<sup>m</sup> , m = −3/2  

The square of the intrinsic carrier concentration varies as:

n<sub>i</sub><sup>2</sup> ∝ T<sup>3</sup> · e<sup>−E<sub>g</sub> / kT</sup>  

Substituting the temperature-dependent expressions of μ and n<sub>i</sub><sup>2</sup> into I<sub>S</sub>:

I<sub>S</sub> = A T<sup>(4+m)</sup> · e<sup>−E<sub>g</sub> / kT</sup>  

This equation shows the strong temperature dependence of the saturation current.

Temperature Dependence
The reverse saturation current I<sub>S</sub> strongly depends on temperature:

- Mobility μ varies with temperature
- Intrinsic carrier concentration n<sub>i</sub> increases with temperature

As temperature increases:
- I<sub>S</sub> increases
- V<sub>D</sub> decreases

This behavior produces the CTAT voltage characteristic.

---
---

### C. Temperature Coefficient of V<sub>D</sub> (or V<sub>CTAT</sub>)

The temperature coefficient of the diode voltage is:

dV / dT = [ V<sub>D</sub> − (4 + m)V<sub>t</sub> − E<sub>g</sub>/q ] / T  

This expression is obtained by differentiating V<sub>D</sub> with respect to temperature.  
It confirms the **negative temperature slope (CTAT)** because the term  
(4 + m)V<sub>t</sub> + E<sub>g</sub>/q is typically larger than V<sub>D</sub>.

---

### Numerical Example at 300 K

dV / dT = [ 0.7 − (4 − 1.5) · 0.026 − 1.2 ] / 300  
≈ −1.88 mV/deg K  

Where:
- V<sub>D</sub> (or V<sub>BE</sub>) ≈ 0.7 V  
- 4 + m = 4 + (−1.5) = 2.5  
- V<sub>t</sub> at 300 K ≈ 0.026 V  
- E<sub>g</sub>/q ≈ 1.2 V  
- T = 300 K  

The result (≈ −1.88 mV/deg K or mV/°C) closely matches the typical CTAT slope and confirms the CTAT behavior.
These effects are typically verified through simulation and lab measurements.

<img width="800" height="650" alt="Screenshot 2025-12-15 at 10 33 04 am" src="https://github.com/user-attachments/assets/e4dfff80-fb85-4343-af74-1c0dcd34e43c" />


---

#### Effect of Biasing on CTAT Voltage
- Using a single BJT with constant current produces a fixed CTAT slope.
- Increasing the number of BJTs increases the negative slope.
- Decreasing bias current increases the negative slope.
- Increasing bias current reduces the negative slope.

These effects are observed in simulations and lab measurements.

#### Types of BGR 
Architechture wise BGR can be designed using :

1. **Self-biased current mirror based BGR**
2. **Op-amp based BGR**


Application-Based BGR can be categorized as :

1. **Low-Voltage BGR**: Used in advanced CMOS technologies with limited supply voltage.

2. **Low-Power BGR**: Designed for battery-operated and ultra-low-power systems.

3. **High-PSRR / Low-Noise BGR**: Used where supply noise rejection and reference purity are critical.

4. **Curvature-Compensated BGR**:  Minimizes higher-order temperature curvature for high-precision applications.

---

## Self-biased current mirror based BGR

### Advantages

* Simple topology
* Easy to design and analyze
* Inherently stable

### Limitations

* Low PSRR
* Requires cascode structures to improve PSRR
* Cascode introduces voltage headroom issues
* Requires a **startup circuit**


## Components of a BandGap voltage Reference

1. **CTAT Voltage Generator**
2. **PTAT Voltage Generator**
3. **Cell-Bias Current Mirror**
4. **Reference Voltage Summation Branch**
5. **Startup Circuit**

Each block is essential for reliable and correct BGR operation.

---



​

