# API : Python SDK

**Chloros Python SDK** suteikia programinę prieigą prie Chloros vaizdų apdorojimo variklio, leidžiančio automatizuoti, kurti individualizuotus darbo srautus ir sklandžiai integruoti su jūsų Python programomis bei tyrimų procesais.

### Pagrindinės savybės

* 🐍 **Native Python** – Švarus, Python stiliaus API vaizdų apdorojimui
* 🔧 **Visiška prieiga prie API** – Visiška kontrolė apdorojant Chloros
* 🚀 **Automatizavimas** – kurkite pasirinktinius paketinio apdorojimo darbo srautus
* 🔗 **Integracija** – įterpkite Chloros į esamas Python programas
* 📊 **Paruošta tyrimams** – puikiai tinka mokslinės analizės procesams
* ⚡ **Lygiagretus apdorojimas** – pritaikoma pagal jūsų procesoriaus branduolių skaičių (Chloros+)

### Reikalavimai

| Reikalavimas          | Išsami informacija                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Įdiegta Chloros** | Windows: darbalaukio diegimo programa; Linux: `.deb` paketas                  |
| **Licencija**          | Chloros+ ([reikalingas mokamas planas](https://cloud.mapir.camera/pricing)) |
| **Operacinė sistema** | Windows 10/11 (64 bitų), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **Python**           | Python 3.7 ar naujesnė versija                                                |
| **Atmintis**           | Mažiausiai 8 GB RAM (rekomenduojama 16 GB)                                  |
| **Internetas**         | Reikalingas licencijos aktyvavimui                                     |

{% hint style="warning" %}
**Licencijos reikalavimas**: Python SDK reikalauja mokamo Chloros+ prenumeratos, kad būtų galima naudotis API. Standartiniai (nemokami) planai nesuteikia prieigos prie API/SDK. Norėdami atnaujinti, apsilankykite [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).
{% endhint %}

## Greitasis pradžios vadovas

### Įdiegimas

Įdiekite per pip:

```bash
pip install chloros-sdk
```

{% hint style="info" %}
**Pirminis nustatymas**: Prieš naudodami SDK, aktyvuokite savo Chloros+ licenciją atidarydami Chloros, Chloros (naršyklė) arba Chloros CLI ir prisijungdami su savo prisijungimo duomenimis. Tai reikia padaryti tik vieną kartą. Linux (be GUI) naudokite: `chloros-cli login user@example.com 'password'`
{% endhint %}

### Pagrindinis naudojimas

Aplanką apdorokite vos keliomis eilutėmis:

```python
from chloros_sdk import process_folder

# One-line processing (Windows)
results = process_folder("C:\\DroneImages\\Flight001")

# One-line processing (Linux)
results = process_folder("/home/user/drone_images/flight001")
```

{% hint style="info" %}
**Kelių platformų keliai**: Šioje puslapyje pateikti kodo pavyzdžiai naudoja Windows stiliaus kelius (pvz., `C:\\DroneImages\\Flight001`). Linux naudokite Linux kelius (pvz., `/home/user/drone_images/flight001` arba `~/drone_images/flight001`). SDK veikia vienodai abiejose platformose.
{% endhint %}

### Visiškas valdymas

Išplėstiniams darbo srautams:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project
chloros.create_project("MyProject", camera="Survey3N_RGN")

# Import images
chloros.import_images("C:\\DroneImages\\Flight001")  # Windows
# chloros.import_images("/home/user/drone_images/flight001")  # Linux

# Configure settings
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE", "GNDVI"]
)

# Process images
chloros.process(mode="parallel", wait=True)
```

***

## Įdiegimo vadovas

### Būtinos sąlygos

Prieš diegdami SDK, įsitikinkite, kad turite:

1. **Įdiegta Chloros** — Windows: darbalaukio diegimo programa ([atsisiųsti](download.md)); Linux: `.deb` paketas ([Linux įdiegimas](linux/linux-installation.md))
2. **Python 3.7+** įdiegta ([python.org](https://www.python.org))
3. **Veikianti Chloros+ licencija** ([atnaujinimas](https://cloud.mapir.camera/pricing))

### Įdiegti per pip

**Standartinis įdiegimas:**

```bash
pip install chloros-sdk
```

**Su pažangos stebėjimo palaikymu:**

```bash
pip install chloros-sdk[progress]
```

**Diegimas kūrimo tikslais:**

```bash
pip install chloros-sdk[dev]
```

### Diegimo patikrinimas

Patikrinkite, ar SDK yra įdiegtas teisingai:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## Pirminis nustatymas

### Licencijos aktyvavimas

SDK naudoja tą pačią licenciją kaip Chloros, Chloros (naršyklė) ir Chloros CLI. Aktyvuokite vieną kartą per GUI arba CLI:**Windows:**Atidarykite**Chloros arba Chloros (naršyklė)** ir prisijunkite vartotojo <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> skirtuką arba naudokite CLI.**Linux:** Naudokite CLI (GUI nėra):

```bash
chloros-cli login user@example.com 'your_password'
```

Licencija yra išsaugoma vietiniame atminties talpykloje ir išlieka po kompiuterio perkrovimo.

{% hint style="success" %}
**Vienkartinis nustatymas**: Prisijungus per GUI arba CLI, SDK automatiškai naudoja išsaugotą licenciją. Papildomo autentifikavimo nereikia!
{% endhint %}

{% hint style="info" %}
**Atsijungimas**: SDK vartotojai gali programiškai išvalyti išsaugotus prisijungimo duomenis naudodami `logout()` metodą. Žr. [logout() metodą](#logout) API žinyne.
{% endhint %}

### Ryšio patikrinimas

Patikrinkite, ar SDK gali prisijungti prie Chloros:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```

***

## API nuoroda

### Klasė „ChlorosLocal“

Pagrindinė klasė, skirta vietiniam Chloros vaizdų apdorojimui.

#### Konstruktorius

```python
ChlorosLocal(
    api_url="http://localhost:5000",     # Backend URL
    auto_start_backend=True,             # Auto-start backend if not running
    backend_exe=None,                    # Backend path (auto-detected)
    timeout=30,                          # Request timeout (seconds)
    backend_startup_timeout=60           # Backend startup timeout
)
```

**Parametrai:**

| Parametras                 | Tipas | Numatytasis                   | Aprašymas                           |
| ------------------------- | ---- | ------------------------- | ------------------------------------- |
| `api_url`                 | str  | `"http://localhost:5000"` | URL vietinio Chloros užpakalinio modulio          |
| `auto_start_backend`      | bool | `True`                    | Automatiškai paleisti backend, jei reikia |
| `backend_exe`             | str  | `None` (automatinis aptikimas)      | Kelias į backend vykdomąjį failą            |
| `timeout`                 | int  | `30`                      | Užklausos laiko limitas sekundėmis            |
| `backend_startup_timeout` | int  | `60`                      | Backend paleidimo laiko limitas (sekundėmis) |

**Pavyzdžiai:**

```python
# Default (auto-start backend, auto-detect path on Windows and Linux)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path (Windows)
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom backend path (Linux)
chloros = ChlorosLocal(backend_exe="/opt/mapir/chloros/backend/chloros-backend")

# Custom timeout with longer startup (e.g., for Jetson)
chloros = ChlorosLocal(timeout=60, backend_startup_timeout=120)
```

{% hint style="info" %}
**Automatinis platformų nustatymas**: SDK automatiškai bando rasti jūsų platformai tinkamą backend kelią:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (rankinis)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

***

### Metodai

#### `create_project(project_name, camera=None)`

Sukurti naują Chloros projektą.

**Parametrai:**

| Parametras      | Tipas | Privalomas | Aprašymas                                              |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `project_name` | str  | Taip      | Projekto pavadinimas                                     |
| `camera`       | str  | Ne       | Kameros šablonas (pvz., „Survey3N\_RGN“, „Survey3W\_OCN“) |

**Grąžina:** `dict` – Atsakymas dėl projekto sukūrimo**Pavyzdys:**

```python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```

***

#### `import_images(folder_path, recursive=False)`

Importuoti vaizdus iš aplanko.

**Parametrai:**

| Parametras     | Tipas     | Privalomas | Aprašymas                        |
| ------------- | -------- | -------- | ---------------------------------- |
| `folder_path` | str/Path | Taip      | Kelias į aplanką su vaizdais         |
| `recursive`   | bool     | Ne       | Ieškoti pakatalogiuose (numatyta: False) |

**Grąžina:** `dict` - Importo rezultatai su failų skaičiumi**Pavyzdys:**

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```

***

#### `configure(**settings)`

Konfigūruoti apdorojimo nustatymus.

**Parametrai:**

| Parametras                 | Tipas | Numatytasis                 | Aprašymas                     |
| ------------------------- | ---- | ----------------------- | ------------------------------- |
| `debayer`                 | str  | „Standartinis (greitas, vidutinė kokybė)“ | Debayerio metodas            |
| `vignette_correction`     | bool | `True`                  | Įjungti vinjetės korekciją      |
| `reflectance_calibration` | bool | `True`                  | Įjungti atspindžio kalibravimą  |
| `indices`                 | list | `None`                  | Apskaičiuotini augmenijos indeksai |
| `export_format`           | str  | „TIFF (16 bitų)“         | Išvesties formatas                   |
| `ppk`                     | bool | `False`                 | Įjungti PPK korekcijas          |
| `custom_settings`         | dict | `None`                  | Išplėstiniai pasirinktiniai nustatymai        |

**Eksporto formatai:**

* `"TIFF (16-bit)"` – Rekomenduojama GIS/fotogrametrijai
* `"TIFF (32-bit, Percent)"` – Mokslinė analizė
* `"PNG (8-bit)"` – Vizualinis patikrinimas
* `"JPG (8-bit)"` – Suspaustas išvesties failas

**Galimi indeksai:**NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2 ir kt.**Pavyzdys:**

```python
# Basic configuration
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE"]
)

# Advanced configuration
chloros.configure(
    debayer="Standard (Fast, Medium Quality)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=True,
    export_format="TIFF (32-bit, Percent)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI", "CIG"]
)
```

***

#### `process(mode="parallel", wait=True, progress_callback=None)`

Apdorokite projekto vaizdus.

**Parametrai:**

| Parametras           | Tipas     | Numatytasis      | Aprašymas                               |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| `mode`              | str      | `"parallel"` | Apdorojimo režimas: „parallel“ arba „serial“   |
| `wait`              | bool     | `True`       | Laukti užbaigimo                       |
| `progress_callback` | callable | `None`       | Pažangos atgalinio skambučio funkcija (progress, msg) |
| `poll_interval`     | float    | `2.0`        | Pažangos tikrinimo intervalas (sekundės)   |

**Grąžina:** `dict` - Apdorojimo rezultatai

{% hint style="warning" %}
**Lygiagretusis režimas**: Reikalinga Chloros+ licencija. Automatiškai pritaikoma prie jūsų procesoriaus branduolių skaičiaus (iki 16 darbininkų).
{% endhint %}

**Pavyzdys:**

```python
# Simple processing
results = chloros.process()

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

# Fire-and-forget (non-blocking)
chloros.process(wait=False)
```

***

#### `get_config()`

Gauti dabartinę projekto konfigūraciją.

**Grąžina:** `dict` - Dabartinė projekto konfigūracija**Pavyzdys:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status()`

Gauti užkulisinės sistemos būsenos informaciją, įskaitant apdorojimo pažangą pagal kiekvieną srautą.

**Grąžina:** `dict` - Užkulisinės sistemos būsena su tokia struktūra:

```python
{
    "running": True,
    "url": "http://localhost:5000",
    "processing": {
        "percent": 75.0,
        "phase": "processing"
    },
    "export": {
        "percent": 50.0,
        "phase": "exporting",
        "active": True
    }
}
```

**Pavyzdys:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
print(f"Processing: {status['processing']['percent']}%")
print(f"Export: {status['export']['percent']}% - Active: {status['export']['active']}")
```

***

#### `shutdown_backend()`

Uždaryti backendą (jei paleistas naudojant SDK).

**Pavyzdys:**

```python
chloros.shutdown_backend()
```

***

#### `logout()`

Išvalykite išsaugotus prisijungimo duomenis iš vietinės sistemos.

**Aprašymas:**

Programiškai atsijungia pašalinant išsaugotus autentifikavimo duomenis. Tai naudinga:
* Perjungiant tarp skirtingų Chloros+ paskyrų
* Autentifikavimo duomenų išvalymui automatizuotose aplinkose
* Saugumo tikslais (pvz., pašalinant autentifikavimo duomenis prieš išdiegimą)

**Grąžina:** `dict` - Atsijungimo operacijos rezultatas**Pavyzdys:**

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Clear cached credentials
result = chloros.logout()
print(f"Logout successful: {result}")

# After logout, login required via GUI/CLI/Browser before next SDK use
```

{% hint style="info" %}
**Reikalingas pakartotinis autentifikavimas**: Po `logout()` iškvietimo turite vėl prisijungti per Chloros, Chloros (naršyklė) arba Chloros CLI, prieš naudodami SDK.
{% endhint %}

***

### Patogios funkcijos

#### `process_folder(folder_path, **options)`

Vienos eilutės patogi funkcija, skirta apdoroti aplanką.

**Parametrai:**

| Parametras                 | Tipas     | Numatytasis         | Aprašymas                    |
| ------------------------- | -------- | --------------- | ------------------------------ |
| `folder_path`             | str/Path | Privaloma        | Kelias į aplanką su vaizdais     |
| `project_name`            | str      | Automatiškai sugeneruota  | Projekto pavadinimas                   |
| `camera`                  | str      | `None`          | Kameros šablonas                |
| `indices`                 | sąrašas     | `["NDVI"]`      | Skaičiuotini indeksai           |
| `vignette_correction`     | bool     | `True`          | Įjungti vinjetės korekciją     |
| `reflectance_calibration` | bool     | `True`          | Įjungti atspindžio kalibravimą |
| `export_format`           | str      | &quot;TIFF (16 bitų)&quot; | Išvesties formatas                  |
| `mode`                    | str      | `"parallel"`    | Apdorojimo režimas                |
| `progress_callback`       | iškviečiamas | `None`          | Pažangos atgalinis skambutis              |

**Grąžina:** `dict` - Apdorojimo rezultatai**Pavyzdys:**

```python
from chloros_sdk import process_folder

# Simple one-liner
results = process_folder("C:\\DroneImages\\Flight001")

# With custom settings
results = process_folder(
    "C:\\DroneImages\\Flight001",
    project_name="Field_A_Survey",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    mode="parallel"
)

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

results = process_folder(
    "C:\\DroneImages\\Flight001",
    progress_callback=show_progress
)
```

***

## Konteksto tvarkyklės palaikymas

SDK palaiko konteksto tvarkytuvus automatiniam valymui:

```python
from chloros_sdk import ChlorosLocal

# Auto-cleanup when done
with ChlorosLocal() as chloros:
    chloros.create_project("MyProject")
    chloros.import_images("C:\\Images")
    chloros.configure(indices=["NDVI"])
    chloros.process()
# Backend automatically shut down here
```

***

## Išsamūs pavyzdžiai

{% hint style="info" %}
**Linux vartotojai**: Visuose žemiau pateiktuose pavyzdžiuose naudojami Windows keliai. Pakeiskite `C:\\...` kelius savo Linux keliais (pvz., `/home/user/...` arba `~/...`). Visos SDK funkcijos yra identiškos visose platformose.
{% endhint %}

### 1 pavyzdys: Pagrindinis apdorojimas

Aplanko apdorojimas naudojant numatytuosius nustatymus:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### 2 pavyzdys: Pasirinktinis darbo srautas

Visiška apdorojimo proceso kontrolė:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project with camera template
chloros.create_project("Research_Plot_A", camera="Survey3N_RGN")

# Import images
import_results = chloros.import_images("C:\\Research\\PlotA")
print(f"Imported {len(import_results.get('files', []))} images")

# Configure advanced settings
chloros.configure(
    debayer="Standard (Fast, Medium Quality)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=False,
    export_format="TIFF (16-bit)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI"]
)

# Process with progress monitoring
def show_progress(progress, message):
    print(f"Progress: {progress}% - {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

print("Processing complete!")
```

***

### 3 pavyzdys: Keleto aplankų apdorojimas partijomis

Apdorokite keletą skrydžių duomenų rinkinių:

```python
from chloros_sdk import ChlorosLocal
from pathlib import Path

# Initialize SDK once
chloros = ChlorosLocal()

# List of flight folders
flights = [
    "C:\\Datasets\\Flight_001",
    "C:\\Datasets\\Flight_002",
    "C:\\Datasets\\Flight_003"
]

for flight_path in flights:
    flight_name = Path(flight_path).name
    print(f"\n{'='*60}")
    print(f"Processing: {flight_name}")
    print('='*60)
    
    try:
        # Create project
        chloros.create_project(flight_name, camera="Survey3N_RGN")
        
        # Import images
        chloros.import_images(flight_path)
        
        # Configure
        chloros.configure(
            vignette_correction=True,
            reflectance_calibration=True,
            indices=["NDVI", "NDRE", "GNDVI"]
        )
        
        # Process
        chloros.process(mode="parallel", wait=True)
        
        print(f"✓ {flight_name} completed successfully")
    
    except Exception as e:
        print(f"✗ {flight_name} failed: {e}")

print("\n" + "="*60)
print("All flights processed!")
```

***

### 4 pavyzdys: Integracija į tyrimų procesą

Chloros integravimas su duomenų analize:

```python
from chloros_sdk import ChlorosLocal
import pandas as pd
import matplotlib.pyplot as plt

# Initialize Chloros
chloros = ChlorosLocal()

# Field survey data
surveys = [
    {"name": "Plot_A", "folder": "C:\\Research\\PlotA", "biomass": 4500},
    {"name": "Plot_B", "folder": "C:\\Research\\PlotB", "biomass": 3800},
    {"name": "Plot_C", "folder": "C:\\Research\\PlotC", "biomass": 5200}
]

results = []

for survey in surveys:
    # Process with Chloros
    chloros.create_project(survey['name'])
    chloros.import_images(survey['folder'])
    chloros.configure(indices=["NDVI", "NDRE"])
    chloros.process(mode="parallel", wait=True)
    
    # Get results
    config = chloros.get_config()
    
    # Extract NDVI values (example - adjust based on your needs)
    # In real implementation, you would read the processed TIFF files
    
    results.append({
        'plot': survey['name'],
        'biomass': survey['biomass'],
        # Add your NDVI extraction here
    })

# Statistical analysis
df = pd.DataFrame(results)
print("\nResults:")
print(df)

# Create correlation plot
# plt.scatter(df['ndvi'], df['biomass'])
# plt.xlabel('NDVI')
# plt.ylabel('Biomass (kg/ha)')
# plt.title('NDVI vs Biomass Correlation')
# plt.show()
```

***

### 5 pavyzdys: Individualus pažangos stebėjimas

Išplėstinis pažangos stebėjimas su registravimu:

```python
from chloros_sdk import ChlorosLocal
from datetime import datetime
import logging

# Setup logging
logging.basicConfig(
    filename=f'processing_{datetime.now():%Y%m%d_%H%M%S}.log',
    level=logging.INFO,
    format='%(asctime)s - %(message)s'
)

# Progress callback with logging
def log_progress(progress, message):
    log_msg = f"[{progress}%] {message}"
    logging.info(log_msg)
    print(log_msg)

# Process with logging
chloros = ChlorosLocal()
chloros.create_project("LoggedProcess")
chloros.import_images("C:\\DroneImages")
chloros.configure(indices=["NDVI", "NDRE"])

logging.info("Starting processing...")
chloros.process(
    mode="parallel",
    progress_callback=log_progress,
    wait=True
)
logging.info("Processing complete!")
```

***

### 6 pavyzdys: Klaidų tvarkymas

Patikimas klaidų tvarkymas gamybiniam naudojimui:

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import (
    ChlorosError,
    ChlorosBackendError,
    ChlorosLicenseError,
    ChlorosProcessingError
)

def process_safely(folder_path):
    """Process with comprehensive error handling"""
    try:
        with ChlorosLocal() as chloros:
            chloros.create_project("SafeProcess")
            chloros.import_images(folder_path)
            chloros.configure(indices=["NDVI"])
            chloros.process()
            
        return True, "Success"
    
    except ChlorosLicenseError as e:
        return False, f"License error: {e}. Upgrade to Chloros+ at cloud.mapir.camera/pricing"
    
    except ChlorosBackendError as e:
        return False, f"Backend error: {e}. Ensure Chloros is installed (Windows installer or Linux .deb package)."
    
    except ChlorosProcessingError as e:
        return False, f"Processing error: {e}"
    
    except FileNotFoundError as e:
        return False, f"Folder not found: {e}"
    
    except ChlorosError as e:
        return False, f"Chloros error: {e}"
    
    except Exception as e:
        return False, f"Unexpected error: {e}"

# Use the safe function
success, message = process_safely("C:\\DroneImages\\Flight001")
if success:
    print(f"✓ {message}")
else:
    print(f"✗ {message}")
```

***

### 7 pavyzdys: Paskyros valdymas ir išsiregistravimas

Valdykite prisijungimo duomenis programiškai:

```python
from chloros_sdk import ChlorosLocal

def switch_account():
    """Clear credentials to switch to a different account"""
    try:
        chloros = ChlorosLocal()
        
        # Clear current credentials
        result = chloros.logout()
        print("✓ Credentials cleared successfully")
        print("Please log in with new account via Chloros, Chloros (Browser), or CLI")
        
        return True
    
    except Exception as e:
        print(f"✗ Logout failed: {e}")
        return False

def secure_cleanup():
    """Remove credentials for security purposes"""
    try:
        chloros = ChlorosLocal()
        chloros.logout()
        print("✓ Credentials removed for security")
        
    except Exception as e:
        print(f"Warning: Cleanup error: {e}")

# Switch accounts
if switch_account():
    print("\nRe-authenticate via Chloros GUI/CLI/Browser before next SDK use")

# Or perform secure cleanup
# secure_cleanup()
```

***

### 8 pavyzdys: Komandinės eilutės įrankis

Sukurkite pasirinktinį CLI įrankį naudodami SDK:

```python
#!/usr/bin/env python
"""
Custom Chloros CLI Tool
Process multiple folders from command line
"""

import sys
import argparse
from pathlib import Path
from chloros_sdk import process_folder

def main():
    parser = argparse.ArgumentParser(description='Custom Chloros Processor')
    parser.add_argument('folders', nargs='+', help='Folders to process')
    parser.add_argument('--indices', nargs='+', default=['NDVI'],
                       help='Indices to calculate (default: NDVI)')
    parser.add_argument('--camera', default=None,
                       help='Camera template')
    parser.add_argument('--format', default='TIFF (16-bit)',
                       help='Export format')
    parser.add_argument('--logout', action='store_true',
                       help='Clear cached credentials before processing')
    
    args = parser.parse_args()
    
    # Handle logout if requested
    if args.logout:
        from chloros_sdk import ChlorosLocal
        chloros = ChlorosLocal()
        chloros.logout()
        print("Credentials cleared. Please re-login via Chloros GUI/CLI/Browser.")
        return 0
    
    successful = []
    failed = []
    
    for folder in args.folders:
        folder_path = Path(folder)
        
        if not folder_path.exists():
            print(f"✗ Skipping {folder}: not found")
            failed.append(folder)
            continue
        
        print(f"\nProcessing: {folder_path.name}...")
        
        try:
            process_folder(
                folder_path,
                camera=args.camera,
                indices=args.indices,
                export_format=args.format
            )
            print(f"✓ {folder_path.name} complete")
            successful.append(folder)
        
        except Exception as e:
            print(f"✗ {folder_path.name} failed: {e}")
            failed.append(folder)
    
    # Summary
    print(f"\n{'='*60}")
    print(f"Summary: {len(successful)} successful, {len(failed)} failed")
    
    return 0 if not failed else 1

if __name__ == '__main__':
    sys.exit(main())
```

**Naudojimas:**

```bash
# Process multiple folders
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI

# Clear cached credentials
python my_processor.py --logout
```

***

## Išimčių tvarkymas

SDK teikia konkrečias išimčių klases skirtingiems klaidų tipams:

### Išimčių hierarchija

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### Išimčių pavyzdžiai

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import *

try:
    chloros = ChlorosLocal()
    chloros.process()

except ChlorosLicenseError:
    print("Chloros+ license required. Upgrade at cloud.mapir.camera/pricing")

except ChlorosBackendError:
    print("Backend failed to start. Ensure Chloros is installed (Windows installer or Linux .deb package).")

except ChlorosProcessingError as e:
    print(f"Processing failed: {e}")

except ChlorosError as e:
    print(f"General Chloros error: {e}")
```

***

## Išplėstinės temos

### Pasirinktinė užkulisio konfigūracija

Naudokite pasirinktinę užkulisio vietą arba konfigūraciją:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### Neužblokuojantis apdorojimas

Pradėkite apdorojimą ir tęskite kitas užduotis:

```python
# Start processing (non-blocking)
chloros.process(wait=False)

# Do other work here...
print("Processing started in background...")

# Check status later
import time
while True:
    status = chloros.get_config()
    if status.get('processing_complete'):
        break
    time.sleep(5)

print("Processing complete!")
```

### Atminties valdymas

Didelės apimties duomenų rinkiniams apdorokite partijomis:

```python
from pathlib import Path

base_folder = Path("C:\\LargeDataset")
batch_size = 100

# Get all image files
images = list(base_folder.glob("*.RAW"))

# Process in batches
for i in range(0, len(images), batch_size):
    batch = images[i:i+batch_size]
    batch_folder = base_folder / f"batch_{i//batch_size}"
    
    # Create batch folder and move images
    # ... (implementation details)
    
    # Process batch
    process_folder(batch_folder)
```

***

## Trikčių šalinimas

### Backend nepaleidžiamas

**Problema:** SDK nepavyksta paleisti backend**Sprendimai:**

1. Patikrinkite, ar įdiegtas Chloros:

```python
import os
import platform

# Auto-detect backend path
if platform.system() == "Windows":
    backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
else:
    backend_path = "/usr/lib/chloros/chloros-backend"

print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. Patikrinkite ugniasienę (Windows) arba prievado prieinamumą (Linux: `lsof -i :5000`)
3. Išbandykite rankinį užpakalinės dalies kelią:

```python
# Windows
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")

# Linux
chloros = ChlorosLocal(backend_exe="/opt/mapir/chloros/backend/chloros-backend")
```

***

### Licencija nerasta**Problema:** SDK įspėja apie trūkstamą licenciją**Sprendimai:**

1. Atidarykite Chloros, Chloros (naršyklė) arba Chloros CLI ir prisijunkite.
2. Patikrinkite, ar licencija yra išsaugota talpykloje:

```python
from pathlib import Path
import os
import platform

# Check cache location
if platform.system() == "Windows":
    cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
else:
    cache_path = Path.home() / '.cache' / 'chloros'

print(f"Cache exists: {cache_path.exists()}")
```

3. Jei kyla problemų su prisijungimo duomenimis, išvalykite talpykloje išsaugotus prisijungimo duomenis ir prisijunkite iš naujo:

```python
from chloros_sdk import ChlorosLocal

# Clear cached credentials
chloros = ChlorosLocal()
chloros.logout()

# Then login again via Chloros, Chloros (Browser), or Chloros CLI
```

4. Susisiekite su pagalbos tarnyba: info@mapir.camera

***

### Importo klaidos**Problema:** `ModuleNotFoundError: No module named 'chloros_sdk'`**Sprendimai:**

```bash
# Verify installation
pip show chloros-sdk

# Reinstall if needed
pip uninstall chloros-sdk
pip install chloros-sdk

# Check Python environment
python -c "import sys; print(sys.path)"
```

***

### Apdorojimo laiko limitas**Problema:** Pasibaigė apdorojimo laiko limitas**Sprendimai:**

1. Padidinkite laiko limitą:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Apdorokite mažesnes partijas
3. Patikrinkite laisvą disko vietą
4. Stebėkite sistemos išteklius

***

### Prievadas jau naudojamas**Problema:** Užimtas vidinis prievadas 5000**Sprendimai:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Arba suraskite ir uždarykite konfliktuojantį procesą:

```powershell
# Windows PowerShell
Get-NetTCPConnection -LocalPort 5000
```

```bash
# Linux
lsof -i :5000
kill $(lsof -t -i :5000)
```

***

## Našumo patarimai

### Optimizuokite apdorojimo greitį

1. **Naudokite lygiagretaus režimo funkciją** (reikia Chloros+)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2. **Sumažinkite išvesties skiriamąją gebą** (jei tai priimtina)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3. **Išjunkite nereikalingus indeksus**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4. **Apdorokite SSD** (ne HDD)***

### Atminties optimizavimas

Didelėms duomenų bazėms:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### Fono apdorojimas

Atlaisvinkite Python kitoms užduotims:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```

***

## Integracijos pavyzdžiai

### Django integracija

```python
# views.py
from django.http import JsonResponse
from chloros_sdk import process_folder

def process_images_view(request):
    if request.method == 'POST':
        folder_path = request.POST.get('folder_path')
        
        try:
            results = process_folder(folder_path)
            return JsonResponse({'success': True, 'results': results})
        except Exception as e:
            return JsonResponse({'success': False, 'error': str(e)})
```

### Flask API

```python
# app.py
from flask import Flask, request, jsonify
from chloros_sdk import process_folder

app = Flask(__name__)

@app.route('/api/process', methods=['POST'])
def process():
    data = request.get_json()
    folder_path = data.get('folder_path')
    
    try:
        results = process_folder(folder_path)
        return jsonify({'success': True, 'results': results})
    except Exception as e:
        return jsonify({'success': False, 'error': str(e)}), 500

if __name__ == '__main__':
    app.run()
```

### Jupyter Notebook

```python
# notebook.ipynb
from chloros_sdk import ChlorosLocal
import matplotlib.pyplot as plt

# Initialize
chloros = ChlorosLocal()

# Process
chloros.create_project("JupyterTest")
chloros.import_images("C:\\Data")
chloros.configure(indices=["NDVI"])

# Progress in notebook
from IPython.display import clear_output

def notebook_progress(progress, message):
    clear_output(wait=True)
    print(f"Progress: {progress}%")
    print(message)

chloros.process(progress_callback=notebook_progress)

# Visualize results
# ... (your visualization code)
```

***

## DUK

### K: Ar SDK reikalauja interneto ryšio?

**A:** Tik pradiniam licencijos aktyvavimui. Prisijungus per Chloros, Chloros (naršyklė) arba Chloros CLI, licencija išsaugoma vietiniame atminties talpykloje ir veikia neprisijungus prie interneto 30 dienų.***

### K: Ar galiu naudoti SDK serveryje be GUI?**A:** Taip! SDK veikia be grafinės sąsajos tiek Windows, tiek Linux serveriuose.**Linux (rekomenduojama be grafinės sąsajos):**
* Įdiekite per `.deb` paketą
* Aktyvuokite licenciją: `chloros-cli login user@example.com 'password'`

**Windows serveris:**
* Windows Server 2016 ar naujesnė versija
* Įdiegta Chloros (vienkartinis)
* Licencija aktyvuota per CLI arba bet kuriame kompiuteryje

***

### K: Koks skirtumas tarp „Desktop“, CLI ir SDK?

| Funkcija         | „Desktop“ GUI | CLI Komandinė eilutė | Python SDK  |
| --------------- | ----------- | ---------------- | ----------- |
| **Sąsaja**   | Spustelėk ir spausk | Komandos          | Python API  |
| **Tinkamiausia**    | Vizualus darbas | Skriptavimas        | Integracija |
| **Automatizavimas**  | Ribotas     | Geras             | Puikus   |
| **Lankstumas** | Bazinis       | Geras             | Maksimalus     |
| **Licencija**     | Chloros+    | Chloros+         | Chloros+    |***

### K: Ar galiu platinti programas, sukurtas naudojant SDK?**A:** SDK kodą galima integruoti į jūsų programas, tačiau:

* Galutiniams vartotojams reikia įdiegti Chloros
* Galutiniams vartotojams reikia aktyvių Chloros+ licencijų
* Komerciniam platinimui reikalinga OEM licencija

Dėl OEM klausimų kreipkitės į info@mapir.camera.

***

### K: Kaip atnaujinti SDK?

```bash
pip install --upgrade chloros-sdk
```

***

### K: Kur išsaugomi apdoroti vaizdai?

Pagal numatytuosius nustatymus – projekto kelyje:

```

Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### K: Ar galiu apdoroti vaizdus iš Python scenarijų, vykdomų pagal tvarkaraštį?**A:** Taip! Naudokite savo operacinės sistemos tvarkaraščio planavimo priemonę su Python scenarijais:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("/data/flights/today")  # Linux
# results = process_folder("C:\\Flights\\Today")  # Windows
```

**Windows:** Nustatykite tvarkaraštį per užduočių planavimo programą, kad skriptas būtų vykdomas kasdien.**Linux:** Nustatykite tvarkaraštį per cron:

```cron
# Run at 2 AM daily
0 2 * ** /usr/bin/python3 /home/user/scheduled_processing.py >> /var/log/chloros.log 2>&1
```

***

### K: Ar SDK palaiko async/await?**A:** Dabartinė versija yra sinchroninė. Norėdami naudoti asinchroninį elgesį, naudokite `wait=False` arba paleiskite atskirame sraute:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

### K: Kaip perjungti skirtingas Chloros+ paskyras?**A:** Naudokite `logout()` metodą, kad išvalytumėte išsaugotus prisijungimo duomenis, tada prisijunkite iš naujo su nauja paskyra:

```python
from chloros_sdk import ChlorosLocal

# Clear current credentials
chloros = ChlorosLocal()
chloros.logout()

# Re-login via Chloros, Chloros (Browser), or Chloros CLI with new account
```

Atsijungę, prieš vėl naudodami SDK, prisijunkite prie naujos paskyros per GUI, naršyklę arba CLI.

***

## Pagalba

### Dokumentacija

* **API nuoroda**: Ši puslapis

### Pagalbos kanalai

* **El. paštas**: info@mapir.camera
* **Svetainė**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Kainos**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### Pavyzdinis kodas

Visi čia pateikti pavyzdžiai yra išbandyti ir paruošti naudoti. Kopijuokite juos ir pritaikykite savo poreikiams.

***

## Licencija**Nuosavybinė programinė įranga** – Autorinės teisės (c) 2025 MAPIR Inc.

SDK reikalauja aktyvios Chloros+ prenumeratos. Neleidžiama naudoti, platinti ar keisti be leidimo.
