# API : Python SDK

**Chloros Python SDK** suteikia programinę prieigą prie Chloros vaizdų apdorojimo variklio, leidžiant automatizavimą, pritaikytus darbo srautus ir sklandų integravimą su jūsų Python programomis ir tyrimų procesais.

### Pagrindinės savybės

* 🐍 **Gimtoji Python** - Švarus, Pythonic API vaizdų apdorojimui
* 🔧 **Visiška API prieiga** - Visiška kontrolė Chloros apdorojimui
* 🚀 **Automatizavimas** - Sukurkite individualizuotas paketinio apdorojimo darbo eigas
* 🔗 **Integracija** – įterpkite Chloros į esamas Python programas
* 📊 **Parengtas tyrimams** – puikiai tinka mokslinių tyrimų analizės procesams
* ⚡ **Lygiagretus apdorojimas** – pritaikomas prie jūsų CPU branduolių (Chloros+)

### Reikalavimai

| Reikalavimas          | Išsami informacija                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Chloros Desktop**  | Turi būti įdiegta lokaliai                                           |
| **Licencija**          | Chloros+ ([reikalingas mokamas planas](https://cloud.mapir.camera/pricing)) |
| **Operacinė sistema** | Windows 10/11 (64 bitai)                                              |
| **Python**           | Python 3.7 ar naujesnė versija                                                |
| **Atmintis**           | Mažiausiai 8 GB RAM (rekomenduojama 16 GB)                                  |
| **Internetas**         | Reikalingas licencijos aktyvavimui                                     |

{% hint style=&quot;warning&quot; %}
**Licencijos reikalavimas**: Python SDK reikalauja mokamo Chloros+ prenumeratos, kad būtų galima naudotis API. Standartiniai (nemokami) planai neturi API/SDK prieigos. Apsilankykite [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing), kad atnaujintumėte.
{% endhint %}

## Greitasis pradžios vadovas

### Įdiegimas

Įdiekite per pip:

```bash
pip install chloros-sdk
```

{% hint style=&quot;info&quot; %}
**Pirmasis nustatymas**: Prieš naudodami SDK, aktyvuokite savo Chloros+ licenciją atidarydami Chloros, Chloros (naršyklė) arba Chloros CLI ir prisijungdami su savo prisijungimo duomenimis. Tai reikia padaryti tik vieną kartą.
{% endhint %}

### Pagrindinis naudojimas

Apdorokite aplanką vos keliais eilutėmis:

```python
from chloros_sdk import process_folder

# One-line processing
results = process_folder("C:\\DroneImages\\Flight001")
```

### Visas valdymas

Išplėstiniams darbo srautams:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project
chloros.create_project("MyProject", camera="Survey3N_RGN")

# Import images
chloros.import_images("C:\\DroneImages\\Flight001")

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

### Privalomi reikalavimai

Prieš diegdami SDK, įsitikinkite, kad turite:

1. **Chloros Desktop** ([atsisiųsti](download.md))
2. **Python 3.7+** įdiegta ([python.org](https://www.python.org))
3. **Aktyvi Chloros+ licencija** ([atnaujinimas](https://cloud.mapir.camera/pricing))

### Įdiegti per pip

**Standartinis įdiegimas:**

```bash
pip install chloros-sdk
```

**Su pažangos stebėjimo palaikymu:**

```bash
pip install chloros-sdk[progress]
```

**Diegimas plėtrai:**

```bash
pip install chloros-sdk[dev]
```

### Diegimo patikrinimas

Patikrinkite, ar SDK yra teisingai įdiegtas:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## Pirmasis nustatymas

### Licencijos aktyvavimas

SDK naudoja tą pačią licenciją kaip Chloros, Chloros (naršyklė) ir Chloros CLI. Aktyvuokite vieną kartą per GUI arba CLI:

1. Atidarykite **Chloros arba Chloros (naršyklė)** ir prisijunkite naudotojo <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> . Arba atidarykite **CLI**.
2. Įveskite savo Chloros+ prisijungimo duomenis ir prisijunkite
3. Licencija yra saugoma vietiniame cache (išlieka po perkrovimo)

{% hint style=&quot;success&quot; %}
**Vienkartinis nustatymas**: prisijungus per GUI arba CLI, SDK automatiškai naudoja išsaugotą licenciją. Papildomo autentifikavimo nereikia!
{% endhint %}

### Ryšio testavimas

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

### ChlorosLocal klasė

Pagrindinė klasė vietiniam Chloros vaizdų apdorojimui.

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
| `api_url`                 | str  | `"http://localhost:5000"` | URL vietinio Chloros užpakalinės dalies          |
| `auto_start_backend`      | bool | `True`                    | Automatiškai paleisti backend, jei reikia |
| `backend_exe`             | str  | `None` (automatinis aptikimas)      | Kelias į backend vykdomąjį failą            |
| `timeout`                 | int  | `30`                      | Prašymo laiko limitas sekundėmis            |
| `backend_startup_timeout` | int  | `60`                      | Laiko limitas backend paleidimui (sekundėmis) |

**Pavyzdžiai:**

```python
# Default (auto-start backend)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom timeout
chloros = ChlorosLocal(timeout=60)
```

***

### Metodai

#### `create_project(project_name, camera=None)`

Sukurti naują Chloros projektą.

**Parametrai:**

| Parametras      | Tipas | Būtinas | Aprašymas                                              |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `project_name` | str  | Taip      | Projekto pavadinimas                                     |
| `camera`       | str  | Ne       | Kameros šablonas (pvz., „Survey3N\_RGN“, „Survey3W\_OCN“) |

**Grąžina:** `dict` – Projekto sukūrimo atsakymas

**Pavyzdys:**

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

| Parametras     | Tipas     | Būtinas | Aprašymas                        |
| ------------- | -------- | -------- | ---------------------------------- |
| `folder_path` | str/Path | Taip      | Kelias į aplanką su vaizdais         |
| `recursive`   | bool     | Ne       | Paieška pakatalogiuose (numatyta: False) |

**Grąžina:** `dict` - Importavimo rezultatai su failų skaičiumi

**Pavyzdys:**

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
| `debayer`                 | str  | „Aukšta kokybė (greičiau)“ | Debayer metodas                  |
| `vignette_correction`     | bool | `True`                  | Įjungti vinjetės korekciją      |
| `reflectance_calibration` | bool | `True`                  | Įjungti atspindžio kalibravimą  |
| `indices`                 | sąrašas | `None`                  | Apskaičiuotini augmenijos indeksai |
| `export_format`           | str  | „TIFF (16 bitų)“         | Išvesties formatas                   |
| `ppk`                     | bool | `False`                 | Įjungti PPK pataisas          |
| `custom_settings`         | dict | `None`                  | Išplėstiniai pasirinktiniai nustatymai        |

**Eksporto formatai:**

* `"TIFF (16-bit)"` – rekomenduojama GIS/fotogrametrijai
* `"TIFF (32-bit, Percent)"` – mokslinė analizė
* `"PNG (8-bit)"` – vizualinis patikrinimas
* `"JPG (8-bit)"` – suspaustas išvesties formatas

**Galimi indeksai:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2 ir kt.

**Pavyzdys:**

```python
# Basic configuration
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE"]
)

# Advanced configuration
chloros.configure(
    debayer="High Quality (Faster)",
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
| `progress_callback` | callable | `None`       | Pažangos atgalinio skambučio funkcija (pažanga, pranešimas) |
| `poll_interval`     | float    | `2.0`        | Pažangos apklausos intervalas (sekundės)   |

**Grąžina:** `dict` - Apdorojimo rezultatai

{% hint style=&quot;warning&quot; %}
**Lygiagretusis režimas**: Reikalinga Chloros+ licencija. Automatiškai pritaikoma prie jūsų CPU branduolių (iki 16 darbininkų).
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

**Grąžina:** `dict` - Dabartinė projekto konfigūracija

**Pavyzdys:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status()`

Gauti informaciją apie užpakalinės dalies būseną.

**Grąžina:** `dict` - Backend būsena

**Pavyzdys:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
```

***

#### `shutdown_backend()`

Išjungti backend (jei paleistas SDK).

**Pavyzdys:**

```python
chloros.shutdown_backend()
```

***

### Patogios funkcijos

#### `process_folder(folder_path, **options)`

Vienos eilutės patogi funkcija, skirta apdoroti aplanką.

**Parametrai:**

| Parametras                 | Tipas     | Numatytasis         | Aprašymas                    |
| ------------------------- | -------- | --------------- | ------------------------------ |
| `folder_path`             | str/Path | Reikalingas        | Kelias į aplanką su vaizdais     |
| `project_name`            | str      | Automatiškai sukurtas  | Projekto pavadinimas                   |
| `camera`                  | str      | `None`          | Kameros šablonas                |
| `indices`                 | list     | `["NDVI"]`      | Skaičiuojami indeksai           |
| `vignette_correction`     | bool     | `True`          | Įjungti vinjetės korekciją     |
| `reflectance_calibration` | bool     | `True`          | Įjungti atspindžio kalibravimą |
| `export_format`           | str      | „TIFF (16 bitų)“ | Išvesties formatas                  |
| `mode`                    | str      | `"parallel"`    | Apdorojimo režimas                |
| `progress_callback`       | callable | `None`          | Pažangos atgalinis skambutis              |

**Grąžina:** `dict` - Apdorojimo rezultatai

**Pavyzdys:**

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

SDK palaiko konteksto tvarkytojus automatiniam valymui:

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

### 1 pavyzdys: pagrindinis apdorojimas

Apdorokite aplanką naudodami numatytuosius nustatymus:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### 2 pavyzdys: pasirinktinis darbo srautas

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
    debayer="High Quality (Faster)",
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

### 3 pavyzdys: kelių aplankų apdorojimas partijomis

Kelių skrydžių duomenų rinkinių apdorojimas:

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

### 4 pavyzdys: Tyrimų proceso integravimas

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

### 5 pavyzdys: individualus pažangos stebėjimas

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

### 6 pavyzdys: klaidų tvarkymas

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
        return False, f"Backend error: {e}. Ensure Chloros Desktop is installed."
    
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

### 7 pavyzdys: Komandinės eilutės įrankis

Sukurkite pasirinktinį CLI įrankį su SDK:

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
    
    args = parser.parse_args()
    
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
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI
```

***

## Išimčių tvarkymas

SDK teikia specifines išimčių klases skirtingiems klaidų tipams:

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
    print("Backend failed to start. Ensure Chloros Desktop is installed.")

except ChlorosProcessingError as e:
    print(f"Processing failed: {e}")

except ChlorosError as e:
    print(f"General Chloros error: {e}")
```

***

## Išplėstinės temos

### Pasirinktinis užpakalinės dalies konfigūravimas

Naudokite pasirinktinę užpakalinės dalies vietą arba konfigūraciją:

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

Didelės apimties duomenų rinkinius apdorokite partijomis:

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

**Problema:** SDK nepavyksta paleisti backend

**Sprendimai:**

1. Patikrinkite, ar įdiegta Chloros Desktop:

```python
import os
backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. Patikrinkite, ar Windows ugniasienė neblokuoja
3. Išbandykite rankinį backend kelią:

```python
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")
```

***

### Licencija neaptikta

**Problema:** SDK įspėja apie trūkstamą licenciją

**Sprendimai:**

1. Atidarykite Chloros, Chloros (naršyklė) arba Chloros CLI ir prisijunkite.
2. Patikrinkite, ar licencija yra įrašyta į talpyklą:

```python
from pathlib import Path
import os

# Check cache location (Windows)
cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
print(f"Cache exists: {cache_path.exists()}")
```

3. Susisiekite su pagalbos tarnyba: info@mapir.camera

***

### Importavimo klaidos

**Problema:** `ModuleNotFoundError: No module named 'chloros_sdk'`

**Sprendimai:**

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

### Apdorojimo laiko limitas

**Problema:** Apdorojimo laiko limitas

**Sprendimai:**

1. Padidinkite laiko limitą:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Apdorokite mažesnes partijas
3. Patikrinkite laisvą disko vietą
4. Stebėkite sistemos išteklius

***

### Prieiga jau naudojama

**Problema:** Backend prieiga 5000 užimta

**Sprendimai:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Arba raskite ir uždarykite konfliktuojantį procesą:

```powershell
# PowerShell
Get-NetTCPConnection -LocalPort 5000
```

***

## Našumo patarimai

### Optimizuokite apdorojimo greitį

1. **Naudokite lygiagretųjį režimą** (reikia Chloros+)

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

4. **Apdorokite SSD** (ne HDD)

***

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

**A:** Tik pradiniam licencijos aktyvavimui. Prisijungus per Chloros, Chloros (naršyklė) arba Chloros CLI, licencija yra išsaugoma vietiniame cache ir veikia neprisijungus prie interneto 30 dienų.

***

### K: Ar galiu naudoti SDK serveryje be GUI?

**A:** Taip! Reikalavimai:

* Windows Server 2016 arba naujesnė versija
* Chloros įdiegta (vienkartinis)
* Licencija aktyvuota bet kuriame kompiuteryje (kaupiamoji licencija nukopijuota į serverį)

***

### K: Koks skirtumas tarp Desktop, CLI ir SDK?

| Funkcija         | Desktop GUI | CLI Komandų eilutė | Python SDK  |
| --------------- | ----------- | ---------------- | ----------- |
| **Sąsaja**   | Taškas-spustelėjimas | Komanda          | Python API  |
| **Tinkamiausia**    | Vizualus darbas | Skriptų kūrimas        | Integracija |
| **Automatizavimas**  | Ribotas     | Geras             | Puikus   |
| **Lankstumas** | Pagrindinis       | Geras             | Maksimalus     |
| **Licencija**     | Chloros+    | Chloros+         | Chloros+    |

***

### K: Ar galiu platinti programas, sukurtas naudojant SDK?

**A:** SDK kodą galima integruoti į jūsų programas, tačiau:

* Galutiniai vartotojai turi turėti įdiegtą Chloros
* Galutiniai vartotojai turi turėti aktyvias Chloros+ licencijas
* Komerciniam platinimui reikalingos OEM licencijos

Dėl OEM klausimų kreipkitės į info@mapir.camera.

***

### K: Kaip atnaujinti SDK?

```bash
pip install --upgrade chloros-sdk
```

***

### K: Kur saugomi apdoroti vaizdai?

Pagal numatytuosius nustatymus, projekto kelyje:

```
Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### K: Ar galiu apdoroti vaizdus iš Python scenarijų, veikiančių pagal tvarkaraštį?

**A:** Taip! Naudokite Windows užduočių planavimo programą su Python skriptais:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("C:\\Flights\\Today")
```

Užduočių planavimo programoje nustatykite kasdienį vykdymą.

***

### K: Ar SDK palaiko async/await?

**A:** Dabartinė versija yra sinchroninė. Asinchroniniam veikimui naudokite `wait=False` arba vykdykite atskirame sraute:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

## Pagalba

### Dokumentacija

* **API nuoroda**: Ši puslapis

### Pagalbos kanalai

* **El. paštas**: info@mapir.camera
* **Svetainė**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Kainos**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### Pavyzdinis kodas

Visi čia pateikti pavyzdžiai yra išbandyti ir paruošti naudoti. Kopijuokite juos ir pritaikykite savo naudojimo atvejui.

***

## Licencija

**Nuosavybinė programinė įranga** – Autorinės teisės (c) 2025 MAPIR Inc.

SDK reikalauja aktyvios Chloros+ prenumeratos. Neteisėtas naudojimas, platinimas ar modifikavimas yra draudžiamas.
