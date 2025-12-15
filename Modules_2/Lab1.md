## Lab 1: 
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
