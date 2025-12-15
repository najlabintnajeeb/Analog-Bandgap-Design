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
MOSFET models
BJT models
Resistor models
Simulation steps:
Pre-layout simulation
Individual block layout
LVS (Layout vs Schematic) check
Parasitic extraction
Post-layout simulation
