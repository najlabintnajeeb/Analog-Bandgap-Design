## Lab Setup
The lab environment is based on a cloud-hosted GitHub Codespace, which already includes all required tools and libraries. Since the simulation is performed entirely in the cloud, no local installation or manual cloning is required. The environment is preconfigured and ready to use.

### Step 1 – Open the Codespace

Navigate to the GitHub repository: https://github.com/vsdip/vsd-bandgap

Click Code → Open with Codespaces to launch the development environment directly in your browser.

<details>
  <summary><strong>View reference screenshot</strong></summary>
  
  <img width="200" height="200" alt="Screenshot 2025-12-15 at 1 51 07 pm" src="https://github.com/user-attachments/assets/331f46c1-c469-42fc-bcd8-512d6f48601d" />

</details>

  ### Step 2: Verify Tool Availability

Once the Codespace is fully launched, verify that the required EDA tools are available. This can be done using either the terminal directly or the GUI desktop. 

<details><summary><strong>Option 1 – Using the Terminal</strong></summary>



1.Open the Terminal tab in the Codespace.

2.Run the following commands to verify tool availability:

```magic```

``` netgen ```

``` ngspice```

If the command launches successfully, it confirms that the corresponding tool is correctly installed and ready for use.




<details>
  <summary><strong>View reference screenshots</strong></summary>

<img width="400" height="400" alt="Screenshot 2025-12-15 at 2 02 23 pm" src="https://github.com/user-attachments/assets/78346f16-2516-4831-90d6-24a680e3d7a7" />



<img width="400" height="400" alt="Screenshot 2025-12-15 at 2 08 34 pm" src="https://github.com/user-attachments/assets/e2026f4a-469e-43b6-919e-ece6fe90d0ae" />



<img width="400" height="400" alt="Screenshot 2025-12-15 at 2 03 46 pm" src="https://github.com/user-attachments/assets/abbff00d-411a-4bc8-9150-40bc8a17f6ad" />


</details>

</details>
<details><summary><strong>Option 2 – Use the GUI (noVNC Desktop):</strong></summary> 

- In your Codespace, open the PORTS tab.

- Find the forwarded port named noVNC Desktop (6080).

- Click the forwarded URL.

- On the web page, select vnc_lite.html to open the XFCE desktop.

Inside the desktop terminal, run:
```
magic
ngspice
netgen
```
<details>
  
  <summary><strong>View reference screenshot</strong></summary>
  
  
<img width="250" height="70" alt="Screenshot 2025-12-15 at 2 10 58 pm" src="https://github.com/user-attachments/assets/4a7d19cf-5782-4397-9b79-b39349009b01" />


<img width="250" height="270" alt="Screenshot 2025-12-15 at 1 53 00 pm" src="https://github.com/user-attachments/assets/f7eb800d-d146-4fc5-9572-cf4db688e998" />

</details>
</details>

### Project Folder Location

The design files are available under the folder: ```/workspaces/vsd-bandgap``` 

The directory structure is shown in the screenshot below.

<details> <summary><strong>View reference screenshot</strong></summary> 
  
  
  <img width="500" height="534" alt="Project folder structure in Codespace" src="https://github.com/user-attachments/assets/94792ad4-9eee-4de1-9d7a-b74d344353c2" /> </details>


SkyWater PDK files are available under the location:
``` /opt/pdk/sky130A/ ```


### Lab 2 – Bandgap Reference (BGR) Design
This lab covers the design specifications, device dataset, and circuit design methodology for the Bandgap Reference (BGR).

### Design Specifications
The target specifications for the BGR design are listed below. These specifications guide the design and are verified through simulation.

**Supply Voltage:** Nominal: 1.8 V
Allowed variation: ±10% (2 V operation is also acceptable)
**Operating Temperature Range:** −40 °C to 125 °C (commercial range)
**Power Consumption:** < 60 µW
**Off Current** (when the BGR is fully powered ON): < 2 µA
**Startup Time:** < 2 µs
**Temperature Coefficient of Reference Voltage**: < 50 ppm/°C 

These specifications are targeted and verified during the design process.


### Device Datasheets

<details> <summary><strong>MOSFET</strong></summary>
  
<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/35556d93-1541-44a1-941a-8322fc7e0221" />

<br>

<img width="400" height="73" alt="MOSFET Parameters" src="https://github.com/user-attachments/assets/b47b0788-8c2a-4fa7-a9da-63f2f8309dd2" />
<br>

Two types of MOSFETs are used:
- NMOS (NFET)
- PMOS (PFET)
**Key Characteristics**
  
Type: **LVT** (Low-Threshold Voltage) MOSFETs
Reason for LVT Selection:
- BJT-based topologies suffer from voltage headroom issues
- Low-threshold devices are required for reliable operation at 1.8 V
**Electrical Parameters**
NMOS Threshold Voltage: ~0.4 V
PMOS Threshold Voltage: ~−0.6 V
**Simulation Model:** PDK: SKY130
**MOSFET Model Name:**

```
sky130_fd_pr__nfet_01v8_lvt
sky130_fd_pr__pfet_01v8_lvt
```

</details>

<details> <summary><strong>BJT</strong></summary>
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/681f6819-f955-4e4b-b3bc-d4fee1272c4a" />


  
Two types of BJTs are available in the PDK:
**Large-area BJT**
Model: W3P40 L3P40
Selected for this design
**Small-area BJT**
Model: W0P68 L0P68
Not used due to limited current handling

**Reasons for Choosing Large BJT**
- Larger emitter area
- Easier control and estimation of current
- Suitable current range: **1 µA to 10 µA per µm²**
- Typical β (gain): ~12
  
</details>

<details> <summary><strong>Resistor</strong></summary>
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/99f14b9f-05a0-47d4-97de-88880846ed7b" />

Type: Semiconductor resistor
Nominal Resistance: ~350 Ω
Available Width Options:
0.35 µm, 0.6 µm, 1.41 µm, 2.8 µm, 5.7 µm
Selected Width: 1.41 µm
Temperature Coefficient: 2.5 Ω/°C
Simulation Model:
``` SKY130FPPA_reg_high_po```
</details>


### Circuit Design
The design starts from the power consumption constraint and proceeds through component sizing.

**A. Calculation of Current**
 - Max Power consumption <**60μW**

VDD = 1.8 V 
- Max Total current = 60μW / 1.8V  ≈ **33.33μA**

Since the circuit has three main branches, the current per branch is set to approximately **10μA** (with 1 to 2μA reserved for startup).

Target Branch current ≈10 μA (Actual design uses 10.7 μA).

**B. BJT Selection for Branch 2**
-Eight BJTs used in parallel
- Trade-off considered:
*Fewer BJTs → lower resistance, poorer matching
*More BJTs → better matching, larger area

**C. Calculation of Resistor R1**

R1 is calculated based on the branch current and the number of parallel BJTs (using a formula derived from the BJT BGR topology, VTln(n)).

- Theoretical Value:

<img width="100" height="50" alt="Screenshot 2025-12-15 at 3 26 57 pm" src="https://github.com/user-attachments/assets/3a0b9d02-3f72-43ec-bbe5-f988606cb815" />
- Implementation: **R1 =5 kΩ**, achieved by using four units of the resistor (two in series, two in parallel) with W=1.4 μm and L=7.8 μm (giving **≈2 kΩ** per unit).


**D. Calculation of Resistor R2**

- R2 is determined by equating the positive Tempco of the voltage across R2(VR2) with the negative Tempco of the base-emitter voltage (VBE or VQ3), resulting in a zero overall Tempco.

- VT slope ≈ +115 µV/°C((Note: 115 μV/∘C is derived from qk plus resistor Tempco effects, whereas the ideal VT slope is ≈85 μV/∘C)

- VQ3 Slope: ∂T∂VQ3 ≈−1.6 mV/∘C
- By Equating slopes:we get Resulting Value: R2 ≈33 kΩ.
-**Implementation:** R2=33 kΩ, achieved by using 16 in series, 2 in parallel.

### E. MOSFET Sizing (Cell-Based Current Mirror)

The PMOS and NMOS transistors are sized to establish the 10 μA branch current.
  
| Device              | Operating Region                          | Length (L) | Width (W) | Multiplier (M/N) | Notes                                                     |
|---------------------|--------------------------------------------|------------|-----------|------------------|-----------------------------------------------------------|
| PMOS (MP1, MP2)     | Saturation                                 | 2 µm       | 5 µm      | 4                | Long L to minimize channel-length modulation (λ)          |
| NMOS (MN1, MN2)     | Sub-threshold (preferred) or Saturation   | 1 µm       | 5 µm      | 8                |                                                           |

**Final Circuit Implementation**

All transistor dimensions (L, W), multipliers (M), and resistor configurations are explicitly defined
The complete circuit can be directly translated into a SPICE netlist
Values provided enable immediate simulation without additional assumptions 

**Simulation and Lab Flow**

SPICE netlist creation using SKY130 PDK

Inclusion of:
- MOSFET models
- BJT models
- Resistor models

Simulation steps:

- Pre-layout simulation
- Individual block layout
- LVS (Layout vs Schematic) check
- Parasitic extraction
- Post-layout simulation







### Lab 3: CTAT Voltage Generation Lab – Sky130 PDK

Objective:

To understand and simulate the Complementary to Absolute Temperature (CTAT) voltage generation circuit as part of the Vizier sub-circuits. CTAT voltage decreases with increasing temperature and is essential for designing PTAT (Proportional to Absolute Temperature) circuits in analog ICs.

CTAT voltage can be generated using:

Diode (less preferred in modern semiconductor design)
BJT in diode-connected mode (preferred approach)
In this lab, a BJT-based CTAT circuit is used.

circuit diagram with expected waveforms:
<br>

<img width="400" height="300" alt="Screenshot 2025-12-16 at 11 26 19 am" src="https://github.com/user-attachments/assets/1f60f9fd-6043-4e28-972d-d144af3b2885" />

<br>

Circuit Parameters:
Constant current source: I₀ = 10 μA
BJT: Sky113 PDK, 5.5V BJT, emitter area L = W = 3.40 μm → total emitter area 11.56 μm²
Maximum sustainable current: 100 μA
Beta (β) of BJT: 30


Simulation Setup
Work Folder: /workspaces/vsd-bandgap/bandgap/prelayout
BJT Model: QP1 (diode-connected, collector & base grounded, emitter connected to current source)
Current Source Node: Between emitter of QP1 and ground
Temperature Sweep: -40°C to 125°C, step of 5°C
Supply Voltage: Included but not actively used

Location of files : ```/workspaces/vsd-bandgap/bandgap/prelayout```
Simulation Cases

### Case 1: Single BJT, constant current (10 μA)
Objective: Measure the slope of V_CTAT.

File:
```ctat_voltage_gen.sp```


**Description:**
This netlist implements a CTAT (Complementary to Absolute Temperature) voltage generator using a single Sky130 parasitic PNP BJT. The CTAT voltage is generated by leveraging the negative temperature coefficient of the BJT base-emitter voltage (V_BE) under constant bias current.

**Screenshot of the netlist:**

<img width="400" height="300" alt="Screenshot 2025-12-16 at 10 27 17 am" src="https://github.com/user-attachments/assets/b713dd13-20f9-4f70-b2f6-1a3941898547" />


#### Simulation
Command used:
```ngspice ./ctat_voltage_gen.sp```

Observed Error:

<img width="400" height="400" alt="Screenshot 2025-12-16 at 9 25 42 am" src="https://github.com/user-attachments/assets/72d25721-b336-4182-80fb-1105830149d4" />

**Analysis of the Error**

**1.Incorrect instance definition in netlist:**

Original line:
``` xqp1 gnd gnd qp1 gnd sky130_fd_pr__pnp_05v5_W3p40L3p40 m=1 ```

<img width="400" height="300" alt="Screenshot 2025-12-16 at 9 26 25 am" src="https://github.com/user-attachments/assets/94495d02-34e3-4910-8f57-d5ee66c58412" />


- Issue identified:
4 nodes provided instead of 3 (Collector Base Emitter)

**2.Root cause:**
The Sky130 PNP models are fixed-geometry subcircuits with internal parameters and exactly three pins:
```.subckt sky130_fd_pr__pnp_05v5_W3p40L3p40 Collector Base Emitter```

<img width="400" height="400" alt="Screenshot 2025-12-16 at 9 44 31 am" src="https://github.com/user-attachments/assets/0d4f1952-5087-4a4b-a916-b37ce6fe5702" />

Ngspice detects the extra parameters/nodes and raises the "Too many parameters" error.

**Reference:**

- SkyWater Sky130 PDK documentation
- /opt/pdk/sky130A/libs.ref/sky130_fd_pr/spice/sky130_fd_pr__pnp_05v5_W3p40L3p40.model.spice
  
<img width="400" height="298" alt="Screenshot 2025-12-16 at 9 45 37 am" src="https://github.com/user-attachments/assets/96857298-fca3-49a1-a0ab-4018cb6100f9" />

**Solution Implemented**
Corrected instance in the netlist:

```xqp1 qp1 qp1 gnd sky130_fd_pr__pnp_05v5_w3p40l3p40```

<img width="400" height="329" alt="Screenshot 2025-12-16 at 9 46 58 am" src="https://github.com/user-attachments/assets/dea2c7ea-1d99-43a8-b07a-3708030a46b4" />

**Simulation After Fix**

<img width="400" height="329" alt="Screenshot 2025-12-16 at 9 46 58 am" src="https://github.com/user-attachments/assets/f2d4aeb0-185d-4c8d-946e-9feb66f2cb56" />

<img width="400" height="541" alt="Screenshot 2025-12-16 at 10 40 30 am" src="https://github.com/user-attachments/assets/1cc3575a-6747-4e7f-badb-3a6b2bd222b3" />

slope value :

<img width="400" height="71" alt="Screenshot 2025-12-16 at 10 41 21 am" src="https://github.com/user-attachments/assets/150ebd2f-2c15-4152-92d0-2ab551cef7a6" />

Observed slope: **-1.725 mV/°C**

Notes: Slightly lower than textbook value of -2 mV/°C due to device-specific characteristics.


### Case 2: Multiple BJTs (m=8 units), constant current (10 μA)

Objective: Study effect of transistor multiplication on CTAT voltage.

File:
```ctat_voltage_gen_mul_bjt.sp```

netlist file :

<img width="400" height="323" alt="Screenshot 2025-12-16 at 10 55 30 am" src="https://github.com/user-attachments/assets/9dc3fad4-ab5a-4fba-8c9c-d8bf6ff3b9c1" />

simulation:
```ngspice ctat_voltage_gen_mul_bjt.sp```

<img width="400" height="342" alt="Screenshot 2025-12-16 at 11 03 30 am" src="https://github.com/user-attachments/assets/2d57f2d3-5a86-4e8c-9b33-006442fbbbf1" />


output /slope calculation:

<img width="400" height="400" alt="Screenshot 2025-12-16 at 11 03 42 am" src="https://github.com/user-attachments/assets/c3d40a21-fc30-44cb-969c-e3b3c23d5dbe" />

Slope: 

<img width="400" height="185" alt="Screenshot 2025-12-16 at 11 05 03 am" src="https://github.com/user-attachments/assets/2307f14b-46c7-48bf-9f4d-2c15c4e0c428" />

Observed slope: **-1.9124 mV/°C**

Notes: Increasing the number of BJTs increases the negative slope, useful for PTAT circuit design.


### Case 3: Single BJT, variable current (1.25 μA to 10 μA)
Objective: Study slope variation with current.

File:
```ctat_voltage_gen_var_current.sp``

netlist file :
<img width="400" height="310" alt="Screenshot 2025-12-16 at 10 56 14 am" src="https://github.com/user-attachments/assets/fc40ad65-94bb-4e6b-938e-06c845c0b4fa" />


simulation:
```ctat_voltage_gen_var_current.sp``
<img width="400" height="344" alt="Screenshot 2025-12-16 at 11 15 02 am" src="https://github.com/user-attachments/assets/656c63d5-3212-4bea-9e23-9d7a224a79ab" />



output /slope calculation:

<img width="400" height="400" alt="Screenshot 2025-12-16 at 11 15 19 am" src="https://github.com/user-attachments/assets/2b7f3be8-81de-475a-9756-4102537beb28" />

Slope: 
<img width="400" height="117" alt="Screenshot 2025-12-16 at 11 23 17 am" src="https://github.com/user-attachments/assets/64ff8ed7-78ff-4b8f-ae8e-c09eba97bc3f" />

Observed slope range: -1.894 mV/°C (1.25 μA) to -1.704 mV/°C (10 μA)
Notes: Slope varies with current, highlighting design considerations for PTAT circuits.



# Lab 4: PTAT Voltage Generation – Pre-layout Simulation

## Objective
To simulate and analyze the **Proportional to Absolute Temperature (PTAT)** voltage generation circuit as part of the Vizier sub-circuits.  
The PTAT voltage increases with temperature and is generated using CTAT voltage differences across a series of BJTs.

---

<details>
<summary><strong>Circuit Overview</strong></summary>

- The PTAT circuit is constructed using **BJT arrays** and resistors.  
- **CTAT voltages** from different nodes are subtracted to generate PTAT voltage.  
- Key nodes:
  - **qp1, qp2:** CTAT voltage nodes  
  - **ra1:** Resistor node  
  - **vra1 - vqp2:** PTAT voltage

**Circuit Diagram:**  

<img width="400" height="274" alt="Screenshot 2025-12-16 at 11 54 16 am" src="https://github.com/user-attachments/assets/f13ac7a9-0ebf-42f4-a611-361cd3c98fca" />

</details>

<details>
<summary><strong>Netlist file :</strong></summary>

```

**** ptat voltage generation circuit *****

.lib "/opt/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt"

.global vdd gnd
.temp 27

*** vcvs definition
e1      net2    gnd     ra1     qp1     gain=1000
xmp1    q1      net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    q2      net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4

*** bjt definition
xqp1    gnd     gnd     qp1             sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1
xqp2    gnd     gnd     qp2          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8

*** voltage sources for current measurement
vid1    q1      qp1     dc      0
vid2    q2      ra1     dc      0

*** supply voltage
vsup    vdd     gnd     dc      2
.dc     temp    -40     125     5

*** control statement
.control
run
plot v(qp1) v(ra1) v(qp2) v(net2)
plot vid1#branch vid2#branch
.endc
.end

```

  
</details>

<details>
<summary><strong>Simulation</strong></summary>
- **Simulation Command:**  
```bash
ngspice ./ptat_voltage_gen.sp
```
  
  <img width="400" height="377" alt="Screenshot 2025-12-16 at 12 59 53 pm" src="https://github.com/user-attachments/assets/7ad093ef-6f4a-4e92-8030-8e4d2ac9b2f7" />

</details>


<details> <summary><strong>Observations & Results</strong></summary>


<strong> 1. Voltage Analysis: CTAT Voltage Nodes</strong>


We first examined the voltages at key nodes, v(qp1) and v(ra1), which are expected to exhibit a Complementary To Absolute Temperature (CTAT) nature.


<strong>Plot:</strong> ```plot v(qp1) v(ra1)```

<strong>Result:</strong> The plots for v(qp1) and v(ra1) are exactly the same.

<img width="400" height="450" alt="Screenshot 2025-12-16 at 1 08 53 pm" src="https://github.com/user-attachments/assets/345c6e8a-8c39-42a0-a3ea-27ba43389e08" />

<strong>Slope Measurement (CTAT Nature):</strong> The measured slope for both nodes is approximately −1.61 mV/∘C. This confirms the expected CTAT behavior.

<img width="400" height="400" alt="Screenshot 2025-12-16 at 1 18 05 pm" src="https://github.com/user-attachments/assets/381f85d3-c1b7-4b9b-a248-aac30ba6ed0f" />


<strong>2.Generating the PTAT Voltage</strong>

The PTAT voltage is generated by taking the difference between two CTAT-like nodes with different slopes: v(ra1) and v(qp2). v(qp2) (across the N=8  BJT) has a more negative slope compared to v(ra1) (across the M=1  BJT).

<strong>Plot:</strong> ```plot v(qp2) v(ra1)```.

<strong>Result:</strong> The plot of v(qp2) (red) shows a steeper negative slope than v(ra1) (blue).

<img width="400" height="400" alt="Screenshot 2025-12-16 at 1 25 31 pm" src="https://github.com/user-attachments/assets/be8db0f0-aba1-4424-aa51-989371fe0f50" />


<strong>PTAT Voltage Calculation: </strong>

The PTAT voltage, V_PTAT, is generated by subtracting the voltage with the stronger negative slope from the one with the lesser negative slope:
<strong>(v(ra1) - v(qp2))</strong>

<strong>Plot:</strong> ```plot v(ra1) - v(qp2)``` 
<strong>Result:</strong> 

<img width="400" height="400" alt="Screenshot 2025-12-16 at 1 32 51 pm" src="https://github.com/user-attachments/assets/4c166355-bdd0-43fb-84e8-8ed0ce9cd5ee" />

The plot clearly shows a voltage that is slowly increasing with temperature, which is the definition of a PTAT voltage.

<strong>Slope Measurement (CTAT Nature):</strong>

<img width="400" height="400" alt="Screenshot 2025-12-16 at 1 42 22 pm" src="https://github.com/user-attachments/assets/ed1c5d38-7500-41e3-baa3-bc59fefd8f8c" />

<img width="400" height="66" alt="Screenshot 2025-12-16 at 1 42 12 pm" src="https://github.com/user-attachments/assets/2f022b01-6c8a-40e2-8f60-fd7eb401ab64" />

The measured slope is approximately : 188 μV/°C
Confirms proportional increase with temperature.

<strong>Branch Currents</strong>

To verify the DC-based circuit operation, the currents in the two respective branches, (vid1 and vid2), are checked for equality.

<strong>Plot:</strong> ```plot vid1#branch  vid2#branch```


<strong>Result:</strong> The two plots are overlapped, confirming that the currents in both branches are identical as expected in this topology.
Current at room temperature (~27°C): 10.8 μA

<img width="400" height="400" alt="Screenshot 2025-12-16 at 3 22 05 pm" src="https://github.com/user-attachments/assets/ecd7d268-c9a0-4668-9f3c-4120cbb081dd" />


</details>


#### Lab 5: BGR (ideal) design and prelayout simulation


##### BGR circuit using VCVS Opamp

This lab covers the design of a complete Bandgap Reference (BGR) circuit using an ideal operational amplifier, implemented as a VCVS (Voltage-Controlled Voltage Source). At this stage, the design focuses on the core reference branch (MP3–R2–Q3) to generate a temperature-independent voltage, excluding the startup circuit for initial verification.
<details><summary><strong>Circuit Diagram </strong></summary>

  ![Unknown](https://github.com/user-attachments/assets/30cba537-7055-4341-a10e-249645db8b80)

</details>

<details><summary><strong>The Core Components</strong></summary>
The BGR combines two voltages with opposite temperature coefficients:
CTAT (Complementary To Absolute Temperature): decreases with temperature, generated by diode-connected BJTs.
PTAT (Proportional To Absolute Temperature): increases with temperature, generated across resistor R1 due to V<sub>BE</sub> difference.
By combining these, a temperature-independent reference voltage V<sub>REF</sub> is produced.

<details><summary><strong>1.PMOS Current Mirrors (Top Section: MP1, MP2, MP3)</strong></summary>  
  
- MP1, MP2, MP3 Forms matched current sources to bias BJTs.
- All PMOS devices have identical dimensions: L = 2 µm, W = 5 µm, M = 4.
- Gates are tied together at net2, controlled by the op-amp output.
- Ensures equal currents I1 = I2 = I3 for consistent branch biasing.

**Role:** Forces input voltages to be equal by adjusting PMOS gates, thereby setting proper branch currents.

</details>

<details><summary><strong>2.VCVS as Ideal Op-Amp</strong></summary>  

- The triangular block labeled vcvs acts as an ideal operational amplifier:
- Negative input (–): connected to the left BJT branch (Q1)
- Positive input (+): connected to the middle branch across R1 and Q2
- Output controls net2, which drives the PMOS gates
  
**Role:** Forces the voltages at its inputs to be equal by adjusting the PMOS gate voltage, thereby setting correct currents.

</details>

<details><summary><strong>3.BJT Branches (Bottom Section)</strong></summary>  
  
<summary><strong>Q1 (Left Branch)</strong></summary>  

- Emitter area ratio: Q1 = 1
- Acts as a diode-connected BJT
- Produces a CTAT voltage V<sub>BE1</sub>
- Collector current flows through MP1

<summary><strong>Q2 (Middle Branch)</strong></summary>  

- Emitter area ratio: Q2 = 8
- Connected in series with R1 = 5 kΩ
- Generates a different base–emitter voltage V<sub>BE2</sub>V



<summary><strong>Q3 (Right Branch)</strong></summary>  

Identical to Q1
Generates CTAT voltage (V<sub>BE3</sub>)
Resistor R2 (ra2) scales PTAT voltage to compensate CTAT, producing temperature-independent V<sub>REF</sub>
**Resistor Scaling**

The PTAT voltage across R<sub>1</sub> is small and must be amplified using a resistor ratio.  
From theory, the required multiplication factor (α) is approximately 9. Hence:

- R<sub>1</sub> = 5 kΩ  
- R<sub>2</sub> = 45 kΩ

This ensures the reference voltage is:

V<sub>REF</sub> = V<sub>BE</sub> + α · ΔV<sub>BE</sub>

By combining the PTAT and CTAT components in this way, their temperature slopes **cancel each other**, producing a **temperature-independent reference voltage**.

  
</details>
</details>

<details><summary><strong>Working principle</strong></summary>  
  
  **Step 1 : Generation of PTAT Current**


Because the op-amp enforces  
V<sub>qp1</sub> = V<sub>ra1</sub>, the voltage drop across resistor R<sub>1</sub> equals the difference between the base–emitter voltages of transistors Q<sub>1</sub> and Q<sub>2</sub>.


ΔV<sub>BE</sub> = V<sub>BE1</sub> − V<sub>BE2</sub>

The expression for the base–emitter voltage difference is given by:

ΔV<sub>BE</sub> = V<sub>T</sub> · ln(N)

where V<sub>T</sub> = kT / q is the thermal voltage, and N represents the emitter area ratio of the BJTs (N = 8 in this design).

Since the thermal voltage V<sub>T</sub> increases linearly with absolute temperature, ΔV<sub>BE</sub> exhibits PTAT (Proportional To Absolute Temperature) behavior.

The resulting PTAT current flowing through the middle branch is therefore:

I = ΔV<sub>BE</sub> / R<sub>1</sub>

This current:
Increases linearly with temperature
Is mirrored into the right branch via MP3




**Step 2.Generation of Vref**

### Bandgap Reference – PTAT and CTAT Compensation

Q<sub>3</sub> is identical to Q<sub>1</sub>, so it generates a CTAT (Complementary To Absolute Temperature) voltage with the same slope.  
If left uncompensated, this CTAT voltage would **decrease with temperature**.

To cancel this slope, we add a **positive PTAT voltage** across resistor R<sub>2</sub>.

#### Resistor Scaling

The PTAT voltage across R<sub>1</sub> is small and must be amplified using a resistor ratio.  
From theory, the required multiplication factor (α) is approximately 9. Hence:

- R<sub>1</sub> = 5 kΩ  
- R<sub>2</sub> = 45 kΩ

This ensures the reference voltage is:

V<sub>REF</sub> = V<sub>BE</sub> + α · ΔV<sub>BE</sub>

By combining the PTAT and CTAT components in this way, their temperature slopes **cancel each other**, producing a **temperature-independent reference voltage**.


</details>

<details><summary><strong>Simulation</strong></summary>

<details><summary><strong>1.Setup</strong></summary>

- Ideal op-amp implemented using VCV
- PMOS transistors: L = 2 µm, W = 5 µm, M = 4
- BJTs:
Q1 = 1 unit
Q2 = 8 units
Q3 = 1 unit
- Designed for 10 µA per branch
- Supply voltage: can be 1.2 V, 1.8 V, or 2 V
- Temperature sweep: –40°C to 125°C, step size 5°C
- Zero-voltage sources (VID1, VID2, VID3) used to measure branch currents



</details>

<details><summary><strong>2. Netlist file</strong></summary>

File: ```bgr_using_ideal_opamp.sp ```


```bash
**** bgr using ideal opamp (vcvs) *****

.lib "/opt/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt"

.global vdd gnd
.temp 27

*** vcvs definition
e1 net2 gnd ra1 qp1 gain=1000


xmp1    q1      net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    q2      net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp3    q3      net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4

*** bjt definition
xqp1    gnd     gnd     qp1             sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1
xqp2    gnd     gnd     qp2          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8
xqp3    gnd     gnd     qp3          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8

xrb1    ref     nb1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb2    nb1     nb2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb3    nb2     nb3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb4    nb3     nb4     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb5    nb4     nb5     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb6    nb5     nb6     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb7    nb6     nb7     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb8    nb7     nb8     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb9    nb8     nb9     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb10   nb9     nb10    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb11   nb10    nb11    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb12   nb11    nb12    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb13   nb12    nb13    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb14   nb13    nb14    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb15   nb14    nb15    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb16   nb15    nb16    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb17   nb16    nb17    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb18   nb17    nb18    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb19   nb18    nb19    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb20   nb19    nb20    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41        l=7.8
xrb21   nb20    nb21    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb22   nb21    nb22    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41        l=7.8
xrb23   nb22    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8
xrb24   nb22    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41       l=7.8

*** voltage source for current measurement

vid1    q1      qp1     dc      0
vid2    q2      ra1     dc      0
vid3    q3      ref     dc      0

*** supply voltage
vsup    vdd     gnd     dc      2
*.dc    vsup    0       3.3     0.3.3

.dc     temp    -40     125     5

*vsup    vdd     gnd     pulse   0       2       10n     1u      1u      1m      100u
*.tran   5n      10u

.control
RUN
plot v(vdd) v(qp1) v(ra1) v(qp2) v(ref) v(qp3)
plot v(ref)


.endc
.end

```




<details><summary><strong>3. Component details</strong></summary>
  
| Component / Command       | SPICE Example                                                                 | Function / Purpose                                                | Syntax / Format |
|---------------------------|-------------------------------------------------------------------------------|------------------------------------------------------------------|----------------|
| **VCVS (ideal op-amp)**   | `e1 net2 gnd ra1 qp1 gain=1000`                                              | Amplifies voltage difference between `ra1` and `qp1`. Output at `net2`. | `Ename Nout+ Nout- NC+ NC- gain=Value` |
| **PMOS transistor**       | `xmp1 q1 net2 vdd vdd sky130_fd_pr__pfet_01v8_lvt l=2 w=5 m=4`<br>`xmp2 q2 net2 vdd vdd sky130_fd_pr__pfet_01v8_lvt l=2 w=5 m=4`<br>`xmp3 q3 net2 vdd vdd sky130_fd_pr__pfet_01v8_lvt l=2 w=5 m=4` | Forms current mirrors to bias BJTs. Output nodes `q1`, `q2`, `q3`. | `Xname Drain Gate Source Bulk ModelName [Parameters]` |
| **PNP transistor**        | `xqp1 gnd gnd qp1 sky130_fd_pr__pnp_05v5_W3p40L3p40 m=1`<br>`xqp2 gnd gnd qp2 sky130_fd_pr__pnp_05v5_W3p40L3p40 m=8`<br>`xqp3 gnd gnd qp3 sky130_fd_pr__pnp_05v5_W3p40L3p40 m=1` | Generates CTAT voltage (`Vbe`). Collector/Emitter/Base connections follow Sky130 PNP convention. | `Xname Collector Emitter Base ModelName [Multiplier]` |
| **High-poly resistor**    | `xra1 ra1 na1 vdd sky130_fd_pr__res_high_po_1p41 w=1.41 l=7.8`<br>`xrb1 ref nb1 vdd sky130_fd_pr__res_high_po_1p41 w=1.41 l=7.8` | Scales PTAT voltage to match CTAT voltage and sets ratios.       | `Rname Node1 Node2 Value` |
| **Voltage source (supply)** | `vsup vdd gnd dc 2`                                                        | Provides 2 V supply.                                             | `Vname Node+ Node- [DC/AC/PULSE] Value` |
| **Voltage sources (current measurement)** | `vid1 q1 qp1 dc 0`<br>`vid2 q2 ra1 dc 0`<br>`vid3 q3 ref dc 0` | Inserts 0 V sources to measure branch currents. Currents read via `I(vid1)` etc. | `Vname Node+ Node- dc 0` |
| **Temperature sweep**     | `.dc temp -40 125 5`                                                         | Sweeps simulation over temperature range -40°C to 125°C.         | `.dc temp Start Stop Step` |
| **DC operating point sweep** | `.dc vsup 0 3.3 0.3`                                                       | Sweeps supply voltage to observe operating points.               | `.dc Vsource Start Stop Step` |
| **Transient analysis**    | `.tran 5n 10u`                                                               | Time-domain simulation to observe dynamic response.              | `.tran Tstep Tstop [Tstart [Tmax]]` |
| **Plotting node voltages**| `plot v(vdd) v(qp1) v(ra1) v(qp2) v(ref) v(qp3)`<br>`plot v(ref)`           | Visualizes node voltages and reference output.                   | `plot v(Node1) v(Node2) ...` |


</details>

</details>


<details><summary><strong>Simulation Results and Verification</strong></summary>

<strong>1.Reference Voltage (VREF)</strong>

```ngspice 9 -> plot v(ref)```

<img width="400" height="400" alt="Screenshot 2025-12-17 at 2 10 44 pm" src="https://github.com/user-attachments/assets/30047208-a87a-46dc-b5c2-c49f7f6ab955" />


<strong>2. Op-Amp Action Verification></strong>

```ngspice 10 -> plot v(qp1) v(ra1) ```

<img width="400" height="400" alt="Screenshot 2025-12-17 at 2 19 56 pm" src="https://github.com/user-attachments/assets/d99b648e-0db9-430d-900a-c05a9b966da3" />


- Voltages at the op-amp input nodes are identical

  ```ngspice 20 -> plot vid1#branch vid2#branch```
  
  - Branch currents are also equal
  - Confirms proper operation of the ideal op-amp and current mirrors
  
<img width="400" height="400" alt="Screenshot 2025-12-17 at 3 17 50 pm" src="https://github.com/user-attachments/assets/b98606d3-a366-4deb-a616-85f0d2e52cee" />

  
<strong> 3. CTAT and PTAT Slope Cancellation</strong>

The voltage at q<sub>p3</sub> shows a CTAT (Complementary To Absolute Temperature) 
slope:≈ −1.638 mV/°C


<img width="400" height="400" alt="Screenshot 2025-12-17 at 2 49 00 pm" src="https://github.com/user-attachments/assets/fbde745d-18ae-47f5-a0fa-6ce2e49cc047" />


The difference between the reference voltage and q<sub>p3</sub> voltage is:   v<sub>ref</sub> − v<sub>qp3</sub>


```bash
x0 = -23.2653, y0 = 0.410145    x1 = 113.061, y1 = 0.636232
dx = 136.327, dy = 0.226087
dy/dx = 0.00165842    dx/dy = 602.983shows a PTAT slope: ≈ +1.65 mV/°C

```

Slopes approximately match in magnitude and cancels.

<img width="400" height="400" alt="Screenshot 2025-12-17 at 3 44 24 pm" src="https://github.com/user-attachments/assets/5822be75-bde7-479f-baac-f4d6a1277145" />


<strong>5. PTAT Scaling Verification</strong>

The small ptat voltage across ra<sub>1</sub> is amplified using the resistor ratio α ≈ 9:

``` plot v(ra1)-v(qp2) ```


<img width="400" height="400" alt="Screenshot 2025-12-17 at 3 49 57 pm" src="https://github.com/user-attachments/assets/9f7e46ad-99d2-4f8f-9a7a-74743a2f107d" />



```
x0 = 109.091, y0 = 0.0716429    x1 = 3.83838, y1 = 0.0518571
dx = -105.253, dy = -0.0197857
dy/dx = 0.000187983    dx/dy = 5319.62

```

- Voltage across R1 has a slope ≈ 187 µV/°C
- Δv<sub>ptat, amplified</sub> = 187 μv/°c × 9 ≈ 1.68 mv/°c
- Confirms correct PTAT amplification by R2
- this amplified ptat voltage cancels the ctat slope of q<sub>p3</sub>, ensuring a temperature-independent reference voltage.


</details>

</details>

### Lab 6: Complete Bandgap Reference (BGR) Design and Pre-Layout Simulation

**Objective:**  

Design and verify a complete CMOS Bandgap Reference (BGR) with startup circuit using Sky130 PDK and evaluate temperature performance through pre-layout simulations.


<details><summary><strong>Schematic Diagram</strong></summary>
<br>
<img width="505" height="556" alt="BGR Circuit Diagram" src="https://github.com/user-attachments/assets/21e05938-8e75-4d64-aba1-efe21896d79c" />
<br>
</details>

<details><summary><strong>Circuit Architecture and Operation</strong></summary>

**Description:**

The BGR uses a self-biased current mirror with a startup circuit. BJTs generate CTAT voltage while the resistor network produces PTAT voltage. Their combination provides a temperature-independent reference. The startup circuit ensures the circuit leaves the zero-current stable state during power-on.

</details>


<details><summary><strong>Pre-Layout Simulation Setup</strong></summary>


Simulations are performed in **ngspice** using the Sky130 PDK to evaluate functionality, temperature stability, and supply voltage variation.

---

##### 1. TT Corner Simulation

*File:* `ngspice ./bgr_lvt_rpolyh_3p40.sp`  


[Spice NetList ↓](#tt-corner-netlist)


**Plot:**


<img width="698" height="539" alt="Screenshot 2025-12-18 at 12 51 08 pm" src="https://github.com/user-attachments/assets/49f7d6c0-cff9-41c3-adb5-d0d9d63920e4" />
<br>

**Temperature Coefficient (TC):**
<br>

<img width="398" height="84" alt="Screenshot 2025-12-18 at 12 51 44 pm" src="https://github.com/user-attachments/assets/be2e3ce8-c4f1-4b14-a59e-841b07206521" />

<br>


- Vmax = 1.109 V
- Vmin = 1.105 V
- Vnom = 1.107 V
- Tmax = 125 °C, Tmin = −40 °C

TC = ((Vmax − Vmin) / (Vnom × (Tmax − Tmin))) × 106 ≈ **22 ppm/°C**

Observation: Umbrella-shaped curve, stable reference voltage.
 
##### 2. SS Corner Simulation
   
File: ngspice ./bgr_lvt_rpolyh_3p40_ss.sp

[ss Corner Netlist ↓](#ss-corner-netlist)



Plot: ```plot v(vref)```

<img width="500" height="542" alt="Screenshot 2025-12-18 at 9 36 19 am" src="https://github.com/user-attachments/assets/469ca645-db0c-47ab-a59b-f1ce3a9c9ee1" />

<br>

Temperature Coefficient (TC):

<img width="400" height="393" alt="Screenshot 2025-12-18 at 9 37 22 am" src="https://github.com/user-attachments/assets/dce7bbb2-1c9e-48c0-9c76-403c54ec0967" />

<br>

- Vmax = 1.097 V
- Vmin = 1.089 V
- Vnom = 1.093 V

TC = (0.008 / (1.093 × 165)) × 10<sup>6</sup>  
- **Temperature Coefficient (SS Corner) ≈ 44 ppm/°C**

Observation: Slight deviation due to slow-slow corner; circuit remains functional.
 
3. FF Corner Simulation

File: ``ngspice ./bgr_lvt_rpolyh_3p40_ff.sp``

[ff Corner Netlist ↓](#ff-corner-netlist)


Plot: ``plot v(vref)``
<br>

<img width="500" height="541" alt="Screenshot 2025-12-18 at 9 51 58 am" src="https://github.com/user-attachments/assets/9d9dc00d-a4c5-478b-a715-9f3d1ab6672c" />



<br>

Temperature Coefficient (TC):

<img width="500" height="394" alt="Screenshot 2025-12-18 at 9 52 43 am" src="https://github.com/user-attachments/assets/3f7e62c8-fc75-4fe7-8b2f-915f45e7d88e" 

<br>

- Vmax = 1.122 V
- Vmin = 1.120 V
- Vnom = 1.121 V

- **Temperature Coefficient (FF Corner) ≈ 10.21 ppm/°C**



Observation: Internally compensated, minimal variation, best corner performance.
 
4. Transient Analysis Simulation

File: ```ngspice ./bgr_lvt_rpolyh_3p40_transient.sp```

[Spice Netlist ↓](#transient-analysis-netlist)
  
Plots:


<img width="500" height="500" alt="Screenshot 2025-12-18 at 12 38 32 pm" src="https://github.com/user-attachments/assets/0fc1233d-7ce0-4fc5-aad8-54bbfc536c39" />
<br>
<img width="500" height="500" alt="Screenshot 2025-12-18 at 12 39 10 pm" src="https://github.com/user-attachments/assets/04f1b39a-0b36-4baf-aebe-219532621512" />

<br>










</details>

---

### Lab 7: Startup Circuit and Simulation Results

Objective: Observe startup behavior and verify that the circuit reaches a stable state.

**Purpose of the Startup Circuit**

- The startup circuit ensures that current flows in the main path, turning the relevant node off initially, and then stabilizing the circuit.
- The transistor responsible for initiating current must be strong, which is why we used n = 2 and the same L and W as in MP1, MP2, and MP3. This ensures proper matching in the layout stack.
- While matching is not critical for this transistor, it helps maintain consistency when used in the stack.
- Another transistor in the startup loop is intentionally made weak: Connected as two series transistors, increasing L, which reduces current flow after the circuit stabilizes.
- After stabilization, the node voltage (Net6) becomes high, creating a reverse voltage across this transistor, ensuring it turns completely off.
- Two diode-connected MOSFETs are used to create the necessary voltage drop: Each drops ~0.8 V, giving a total drop of ~1.6 V.
- During normal operation, this voltage reduces to ~1.2–1.4 V, keeping the transistor off.
- The weak transistor ensures only a very small current flows through the startup path once the circuit stabilizes.


<details><summary><strong>Simulation Results</strong></summary>


  
##### 1. VDD and VREF Response

Plot: ```plot v(vdd) v(vref)```

  <br>
<img width="500" height="399" alt="VDD vs VREF" src="https://github.com/user-attachments/assets/2816bad9-a7fc-4205-b5e1-82b2fd0ad0af" />

<br>

- VDD is ramped from 0 V to 2 V during transient simulation.
- VREF starts rising once the startup circuit becomes active.
- Startup completes at approximately 1 µs, meeting the design specification.

##### 2. Net1 and Net2 Behavior During Startup

Plot: ```plot v(vdd) v(net2) v(net1)```


<img width="500" height="542" alt="Net1 and Net2" src="https://github.com/user-attachments/assets/6d73573d-512f-413d-b69d-6d02074734fd" />


After startup:
- Net1 goes high
- Net2 goes low
This confirms correct biasing of the BGR core after startup.


Zoomed View Around Startup (~1 µs):


<img width="400" height="300" alt="Zoomed Startup" src="https://github.com/user-attachments/assets/d504992a-0c8b-4b7e-9b59-4b731322013b" />


- The exact moment of startup completion is clearly visible.


  
##### 3. Voltage Difference Between Net2 and Net6

Plot: plot v(vdd) v(net2) v(net6)

<img width="500" height="537" alt="Net2 vs Net6" src="https://github.com/user-attachments/assets/5326b6d0-03b4-4cf1-81a6-3485cbb7814d" />

- Both Net2 and Net6 initially follow VDD.
- A voltage difference gradually develops between Net2 and Net6.
- When the difference exceeds approximately 0.6 V (V<sub>T</sub>), the startup transistor turns ON.
- This triggers current flow and initiates proper circuit startup.
- After stabilization, the voltage difference ensures correct steady-state operation.

##### 4.Current in the Startup Branch

```plot vid4#branch```


<img width="500" height="500" alt="Screenshot 2025-12-18 at 1 59 09 pm" src="https://github.com/user-attachments/assets/814ce798-f0e6-4381-984a-1ba9a66a0983" />



- Before 1 μs: ~0.5 μA flows through the startup transistor.
- After stabilization: current drops to 0, isolating the startup circuit.

```plot vid5#branch```

<img width="500" height="500" alt="Screenshot 2025-12-18 at 2 12 37 pm" src="https://github.com/user-attachments/assets/4955fb8b-a90c-4450-a066-694b31530330" />
Confirms that post-startup current is minimal.


##### 5.Effect of Removing the Startup Circuit

Modification:
- Commented out the mp6 transistor in the netlist.
  
Observed Behavior:

<img width="702" height="536" alt="No Startup Result" src="https://github.com/user-attachments/assets/73626dfe-5dce-46c0-a49f-9b50a317bc80" />

- Net1 remains near ground
- Net2 remains near VDD
- No current flows in the startup path.
- Simulation confirms the circuit fails to start.



</details>




---




#### Lab 8: Layout of the components

<details><summary><strong> Reference Diagram</strong></summary>




<img width="702" height="456" alt="Screenshot 2025-12-18 at 2 51 49 pm" src="https://github.com/user-attachments/assets/0d89a153-a115-4176-9466-e1d0549eae17" />
</details>




#### Magic

```magic -T /opt/pdk/sky130A/libs.tech/magic/sky130A.tech ```



1.Register bank layout

File: ``resbank.mag``

<img width="964" height="628" alt="Screenshot 2025-12-18 at 3 12 24 pm" src="https://github.com/user-attachments/assets/00eb7ca3-0f41-414b-a7f6-3a69d42bf996" />


2.  PFETS Layout

<img width="691" height="365" alt="Screenshot 2025-12-18 at 3 37 25 pm" src="https://github.com/user-attachments/assets/a3b2e32f-2a9a-47f4-81bb-a43c976c49ab" />



3. NFETS Layout

<img width="958" height="630" alt="Screenshot 2025-12-18 at 3 36 23 pm" src="https://github.com/user-attachments/assets/176dac3e-c5be-4a17-9077-4c8a3c0cc9c3" />



3.  BJT layout
<img width="953" height="630" alt="Screenshot 2025-12-18 at 3 51 48 pm" src="https://github.com/user-attachments/assets/60524d8c-14d5-4fa4-a9d3-7d8b9cb1b1d2" />


4. top level
   top.mag

 magic -T /opt/pdk/sky130A/libs.tech/magic/sky130A.tech -rcfile /opt/pdk/sky130A/libs.tech/magic/sky130A.magicrc





   ### LVS

   

---


---




## Spice Netlists

### TT Corner Netlist
<a name="tt-corner-netlist"></a>

```bash


**** bandgap reference circuit using self-biase current mirror *****

.lib "/opt/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt"

.global vdd gnd
.temp 27

*** circuit definition ***

*** mosfet definitions self-biased current mirror and output branch
xmp1    net1    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    net2    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp3    net3    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmn1    net1    net1    q1      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8
xmn2    net2    net1    q2      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8

*** start-upcircuit
xmp4    net4    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp5    net5    net2    net4    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp6    net7    net6    net2    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=2
xmn3    net6    net6    net8    gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1
xmn4    net8    net8    gnd     gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1

*** bjt definition
xqp1    gnd     gnd     qp1             sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1
xqp2    gnd     gnd     qp2          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8
xqp3    gnd     gnd     qp3          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

xrb1    vref    nb1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb2    nb1     nb2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb3    nb2     nb3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb4    nb3     nb4     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb5    nb4     nb5     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb6    nb5     nb6     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb7    nb6     nb7     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb8    nb7     nb8     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb9    nb8     nb9     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb10   nb9     nb10    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb11   nb10    nb11    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb12   nb11    nb12    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb13   nb12    nb13    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb14   nb13    nb14    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb15   nb14    nb15    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb16   nb15    nb16    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb17   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb18   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

*** voltage source for current measurement
vid1    q1      qp1     dc      0
vid2    q2      ra1     dc      0
vid3    net3    vref    dc      0
vid4    net7    net1    dc      0
vid5    net5    net6    dc      0

*** supply voltage
vsup    vdd     gnd     dc      2
*.dc    vsup    0       3.3     0.3.3
.dc     temp    -40     125     5

*vsup   vdd     gnd     pulse   0       2       10n     1u      1u      1m      100u
*.tran  5n      10u

.control
run

plot v(vdd) v(net1) v(net2) v(qp1) v(ra1) v(qp2) v(vref) v(qp3)
plot vid1#branch vid2#branch vid3#branch vid4#branch vid5#branch

.endc
.end


```
---

### ss Corner Netlist
<a name="ss-corner-netlist"></a>


```bash

**** bandgap reference circuit using self-biase current mirror *****

.lib "/opt/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt"

.global vdd gnd
.temp 27

*** circuit definition ***

*** mosfet definitions self-biased current mirror and output branch
xmp1    net1    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    net2    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp3    net3    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmn1    net1    net1    q1      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8
xmn2    net2    net1    q2      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8

*** start-upcircuit
xmp4    net4    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp5    net5    net2    net4    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp6    net7    net6    net2    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=2
xmn3    net6    net6    net8    gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1
xmn4    net8    net8    gnd     gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1

*** bjt definition
xqp1    gnd     gnd     qp1             sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1
xqp2    gnd     gnd     qp2          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8
xqp3    gnd     gnd     qp3          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

xrb1    vref    nb1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb2    nb1     nb2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb3    nb2     nb3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb4    nb3     nb4     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb5    nb4     nb5     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb6    nb5     nb6     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb7    nb6     nb7     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb8    nb7     nb8     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb9    nb8     nb9     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb10   nb9     nb10    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb11   nb10    nb11    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb12   nb11    nb12    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb13   nb12    nb13    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb14   nb13    nb14    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb15   nb14    nb15    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb16   nb15    nb16    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb17   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb18   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

*** voltage source for current measurement
vid1    q1      qp1     dc      0
vid2    q2      ra1     dc      0
vid3    net3    vref    dc      0
vid4    net7    net1    dc      0
vid5    net5    net6    dc      0

*** supply voltage
vsup    vdd     gnd     dc      2
*.dc    vsup    0       3.3     0.3.3
.dc     temp    -40     125     5

*vsup   vdd     gnd     pulse   0       2       10n     1u      1u      1m      100u
*.tran  5n      10u

.control
run

plot v(vdd) v(net1) v(net2) v(qp1) v(ra1) v(qp2) v(vref) v(qp3)
plot vid1#branch vid2#branch vid3#branch vid4#branch vid5#branch

.endc
.end

```


---

### ff Corner Netlist
<a name="ff-corner-netlist"></a>

```bash

**** bandgap reference circuit using self-biase current mirror at ff corner*****

.lib "/opt/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice ff"

.global vdd gnd
.temp 27

*** circuit definition ***

*** mosfet definitions self-biased current mirror and output branch
xmp1    net1    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    net2    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp3    net3    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmn1    net1    net1    q1      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8
xmn2    net2    net1    q2      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8

*** start-upcircuit
xmp4    net4    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp5    net5    net2    net4    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp6    net7    net6    net2    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=2
xmn3    net6    net6    net8    gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1
xmn4    net8    net8    gnd     gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1

*** bjt definition
xqp1    gnd     gnd     qp1             sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1
xqp2    gnd     gnd     qp2          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8
xqp3    gnd     gnd     qp3          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

xrb1    vref    nb1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb2    nb1     nb2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb3    nb2     nb3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb4    nb3     nb4     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb5    nb4     nb5     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb6    nb5     nb6     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb7    nb6     nb7     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb8    nb7     nb8     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb9    nb8     nb9     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb10   nb9     nb10    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb11   nb10    nb11    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb12   nb11    nb12    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb13   nb12    nb13    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb14   nb13    nb14    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb15   nb14    nb15    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb16   nb15    nb16    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb17   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb18   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

*** voltage source for current measurement
vid1    q1      qp1     dc      0
vid2    q2      ra1     dc      0
vid3    net3    vref    dc      0
vid4    net7    net1    dc      0
vid5    net5    net6    dc      0

*** supply voltage
vsup    vdd     gnd     dc      2
*.dc    vsup    0       3.3     0.3.3
.dc     temp    -40     125     5

*vsup   vdd     gnd     pulse   0       2       10n     1u      1u      1m      100u
*.tran  5n      10u

.control
run

plot v(vdd) v(net1) v(net2) v(qp1) v(ra1) v(qp2) v(vref) v(qp3)
plot vid1#branch vid2#branch vid3#branch vid4#branch vid5#branch

.endc
.end

                                  
```





---

### Transient analysis Netlist
<a name="Transient-analysis-netlist"></a>

```bash



**** bandgap reference circuit using self-biase current mirror *****

.lib "/opt/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt"

.global vdd gnd
.temp 27

*** circuit definition ***

*** mosfet definitions self-biased current mirror and output branch
xmp1    net1    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    net2    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp3    net3    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmn1    net1    net1    q1      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8
xmn2    net2    net1    q2      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8

*** start-upcircuit
xmp4    net4    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp5    net5    net2    net4    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp6    net7    net6    net2    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=2
xmn3    net6    net6    net8    gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1
xmn4    net8    net8    gnd     gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1

*** bjt definition
xqp1    gnd     gnd     qp1             sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1
xqp2    gnd     gnd     qp2          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8
xqp3    gnd     gnd     qp3          sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

xrb1    vref    nb1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb2    nb1     nb2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb3    nb2     nb3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb4    nb3     nb4     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb5    nb4     nb5     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb6    nb5     nb6     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb7    nb6     nb7     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb8    nb7     nb8     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb9    nb8     nb9     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb10   nb9     nb10    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb11   nb10    nb11    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb12   nb11    nb12    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb13   nb12    nb13    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb14   nb13    nb14    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb15   nb14    nb15    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb16   nb15    nb16    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb17   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb18   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

*** voltage source for current measurement
vid1    q1      qp1     dc      0
vid2    q2      ra1     dc      0
vid3    net3    vref    dc      0
vid4    net7    net1    dc      0
vid5    net5    net6    dc      0

*** supply voltage
*vsup    vdd     gnd     dc      2
*.dc    vsup    0       3.3     0.3.3
.dc     temp    -40     125     5

vsup   vdd     gnd     pulse   0       2       10n     1u      1u      1m      100u
.tran  5n      10u

.control
run

plot v(vdd) v(net1) v(net2) v(qp1) v(ra1) v(qp2) v(vref) v(qp3)
plot vid1#branch vid2#branch vid3#branch vid4#branch vid5#branch

.endc
.end

```
---



