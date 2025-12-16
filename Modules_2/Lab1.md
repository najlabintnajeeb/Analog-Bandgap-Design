## Lab 1: 
Lab 1 is installation and cloning of required files , since we are doing it on cloud , codespace we dont need to install anything , eveerthy ing is readu to use already .
#### Step 1 – Open the Codespace
- Go to the GitHub repository: https://github.com/vsdip/vsd-bandgap
- Click “Code” → “Open with Codespaces” to launch the environment directly in your browser.
<img width="450" height="356" alt="Screenshot 2025-12-15 at 1 51 07 pm" src="https://github.com/user-attachments/assets/331f46c1-c469-42fc-bcd8-512d6f48601d" />
#### Step 2: option 1:
Once the Codespace opens, go to the TERMINAL tab and run:
```
magic
```
or
```
ngspice
```
or
```
netgen
```
<img width="1406" height="754" alt="Screenshot 2025-12-15 at 2 02 23 pm" src="https://github.com/user-attachments/assets/78346f16-2516-4831-90d6-24a680e3d7a7" />

<img width="549" height="245" alt="Screenshot 2025-12-15 at 2 08 34 pm" src="https://github.com/user-attachments/assets/e2026f4a-469e-43b6-919e-ece6fe90d0ae" />

<img width="1399" height="653" alt="Screenshot 2025-12-15 at 2 03 46 pm" src="https://github.com/user-attachments/assets/abbff00d-411a-4bc8-9150-40bc8a17f6ad" />

##### Option 2 – Use the GUI (noVNC Desktop)

In your Codespace, open the PORTS tab.

Find the forwarded port named noVNC Desktop (6080).

Click the forwarded URL.

On the web page, select vnc_lite.html to open the XFCE desktop.

Inside the desktop terminal, run:
```
magic
ngspice
netgen
```

<img width="250" height="70" alt="Screenshot 2025-12-15 at 2 10 58 pm" src="https://github.com/user-attachments/assets/4a7d19cf-5782-4397-9b79-b39349009b01" />

<img width="250" height="270" alt="Screenshot 2025-12-15 at 1 53 00 pm" src="https://github.com/user-attachments/assets/f7eb800d-d146-4fc5-9572-cf4db688e998" />


### Project Folder Location

The design files are available under the folder: /workspaces/vsd-bandgap as shown in the image below:

<img width="818" height="534" alt="Screenshot 2025-12-15 at 2 18 39 pm" src="https://github.com/user-attachments/assets/94792ad4-9eee-4de1-9d7a-b74d344353c2" />

SkyWater PDK files are available under the location:
``` /opt/pdk/sky130A/ ```



Lab2:the design specifications, device dataset, and circuit design methodology for the Bandgap Reference (BGR).

### Design Specifications
The target specifications for the BGR design are as follows:
**Supply Voltage:** Nominal: 1.8 V
Allowed variation: ±10%
(2 V operation is also acceptable)
**Operating Temperature Range:** −40 °C to 125 °C (commercial range)
**Power Consumption:** < 60 µW
**Off Current** (when the BGR is fully powered ON): < 2 µA
**Startup Time:** < 2 µs
**Temperature Coefficient of Reference Voltage**: < 50 ppm/°C 

These specifications are targeted and verified during the design process.


### Device Datasheets

<details> <summary><strong>MOSFET</strong></summary>
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/35556d93-1541-44a1-941a-8322fc7e0221" />

<img width="953" height="73" alt="MOSFET Parameters" src="https://github.com/user-attachments/assets/b47b0788-8c2a-4fa7-a9da-63f2f8309dd2" />
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
<img width="1729" height="515" alt="image" src="https://github.com/user-attachments/assets/681f6819-f955-4e4b-b3bc-d4fee1272c4a" />


  
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
<img width="1879" height="556" alt="image" src="https://github.com/user-attachments/assets/99f14b9f-05a0-47d4-97de-88880846ed7b" />

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







Lab 3:
## CTAT Voltage Generation Lab – Sky130 PDK

Objective:
To understand and simulate the Complementary to Absolute Temperature (CTAT) voltage generation circuit as part of the Vizier sub-circuits. CTAT voltage decreases with increasing temperature and is essential for designing PTAT (Proportional to Absolute Temperature) circuits in analog ICs.

CTAT voltage can be generated using:
Diode (less preferred in modern semiconductor design)
BJT in diode-connected mode (preferred approach)
In this lab, a BJT-based CTAT circuit is used.

circuit diagram with expected waveforms:
<img width="676" height="384" alt="Screenshot 2025-12-16 at 11 26 19 am" src="https://github.com/user-attachments/assets/1f60f9fd-6043-4e28-972d-d144af3b2885" />



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

<img width="685" height="309" alt="Screenshot 2025-12-16 at 10 27 17 am" src="https://github.com/user-attachments/assets/b713dd13-20f9-4f70-b2f6-1a3941898547" />


#### Simulation
Command used:
```ngspice ./ctat_voltage_gen.sp```

Observed Error:

<img width="818" height="450" alt="Screenshot 2025-12-16 at 9 25 42 am" src="https://github.com/user-attachments/assets/72d25721-b336-4182-80fb-1105830149d4" />

**Analysis of the Error**

**1.Incorrect instance definition in netlist:**

Original line:
``` xqp1 gnd gnd qp1 gnd sky130_fd_pr__pnp_05v5_W3p40L3p40 m=1 ```

<img width="679" height="342" alt="Screenshot 2025-12-16 at 9 26 25 am" src="https://github.com/user-attachments/assets/94495d02-34e3-4910-8f57-d5ee66c58412" />


- Issue identified:
4 nodes provided instead of 3 (Collector Base Emitter)

**2.Root cause:**
The Sky130 PNP models are fixed-geometry subcircuits with internal parameters and exactly three pins:
```.subckt sky130_fd_pr__pnp_05v5_W3p40L3p40 Collector Base Emitter```

<img width="922" height="517" alt="Screenshot 2025-12-16 at 9 44 31 am" src="https://github.com/user-attachments/assets/0d4f1952-5087-4a4b-a916-b37ce6fe5702" />

Ngspice detects the extra parameters/nodes and raises the "Too many parameters" error.

**Reference:**

- SkyWater Sky130 PDK documentation
- /opt/pdk/sky130A/libs.ref/sky130_fd_pr/spice/sky130_fd_pr__pnp_05v5_W3p40L3p40.model.spice
  
<img width="897" height="298" alt="Screenshot 2025-12-16 at 9 45 37 am" src="https://github.com/user-attachments/assets/96857298-fca3-49a1-a0ab-4018cb6100f9" />

**Solution Implemented**
Corrected instance in the netlist:

```xqp1 qp1 qp1 gnd sky130_fd_pr__pnp_05v5_w3p40l3p40```

<img width="881" height="329" alt="Screenshot 2025-12-16 at 9 46 58 am" src="https://github.com/user-attachments/assets/dea2c7ea-1d99-43a8-b07a-3708030a46b4" />

**Simulation After Fix**

<img width="881" height="329" alt="Screenshot 2025-12-16 at 9 46 58 am" src="https://github.com/user-attachments/assets/f2d4aeb0-185d-4c8d-946e-9feb66f2cb56" />

<img width="700" height="541" alt="Screenshot 2025-12-16 at 10 40 30 am" src="https://github.com/user-attachments/assets/1cc3575a-6747-4e7f-badb-3a6b2bd222b3" />

slope value :

<img width="544" height="71" alt="Screenshot 2025-12-16 at 10 41 21 am" src="https://github.com/user-attachments/assets/150ebd2f-2c15-4152-92d0-2ab551cef7a6" />

Observed slope: **-1.725 mV/°C**

Notes: Slightly lower than textbook value of -2 mV/°C due to device-specific characteristics.


### Case 2: Multiple BJTs (m=8 units), constant current (10 μA)

Objective: Study effect of transistor multiplication on CTAT voltage.

File:
```ctat_voltage_gen_mul_bjt.sp```

netlist file :

<img width="752" height="323" alt="Screenshot 2025-12-16 at 10 55 30 am" src="https://github.com/user-attachments/assets/9dc3fad4-ab5a-4fba-8c9c-d8bf6ff3b9c1" />

simulation:
```ngspice ctat_voltage_gen_mul_bjt.sp```

<img width="772" height="342" alt="Screenshot 2025-12-16 at 11 03 30 am" src="https://github.com/user-attachments/assets/2d57f2d3-5a86-4e8c-9b33-006442fbbbf1" />


output /slope calculation:

<img width="702" height="541" alt="Screenshot 2025-12-16 at 11 03 42 am" src="https://github.com/user-attachments/assets/c3d40a21-fc30-44cb-969c-e3b3c23d5dbe" />

Slope: 

<img width="574" height="185" alt="Screenshot 2025-12-16 at 11 05 03 am" src="https://github.com/user-attachments/assets/2307f14b-46c7-48bf-9f4d-2c15c4e0c428" />

Observed slope: **-1.9124 mV/°C**

Notes: Increasing the number of BJTs increases the negative slope, useful for PTAT circuit design.


### Case 3: Single BJT, variable current (1.25 μA to 10 μA)
Objective: Study slope variation with current.

File:
```ctat_voltage_gen_var_current.sp``

netlist file :
<img width="753" height="310" alt="Screenshot 2025-12-16 at 10 56 14 am" src="https://github.com/user-attachments/assets/fc40ad65-94bb-4e6b-938e-06c845c0b4fa" />


simulation:
```ctat_voltage_gen_var_current.sp``
<img width="790" height="344" alt="Screenshot 2025-12-16 at 11 15 02 am" src="https://github.com/user-attachments/assets/656c63d5-3212-4bea-9e23-9d7a224a79ab" />



output /slope calculation:

<img width="705" height="539" alt="Screenshot 2025-12-16 at 11 15 19 am" src="https://github.com/user-attachments/assets/2b7f3be8-81de-475a-9756-4102537beb28" />

Slope: 
<img width="503" height="117" alt="Screenshot 2025-12-16 at 11 23 17 am" src="https://github.com/user-attachments/assets/64ff8ed7-78ff-4b8f-ae8e-c09eba97bc3f" />

Observed slope range: -1.894 mV/°C (1.25 μA) to -1.704 mV/°C (10 μA)
Notes: Slope varies with current, highlighting design considerations for PTAT circuits.
