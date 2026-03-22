# CLI : Komandinė eilutė

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>**Chloros CLI** suteikia galingą komandinės eilutės prieigą prie Chloros vaizdų apdorojimo variklio, leidžiantį automatizuoti, kurti scenarijus ir vykdyti beinterfeisinį vaizdų apdorojimo procesų valdymą.

### Pagrindinės funkcijos

* 🚀 **Automatizavimas** – kelių duomenų rinkinių paketinis apdorojimas naudojant skriptus
* 🔗 **Integracija** – įterpimas į esamus darbo srautus ir procesų grandines
* 💻 **Veikimas be grafinės sąsajos** – veikimas be GUI
* 🌍 **Daugiakalbystė** – 38 kalbų palaikymas
* ⚡ **Lygiagretus apdorojimas** – [Dinaminis skaičiavimo pritaikymas](processing-architecture/dynamic-compute-adaptation.md) automatiškai optimizuoja jūsų aparatinę įrangą

### Reikalavimai

| Reikalavimas          | Išsami informacija                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Operacinė sistema** | Windows 10/11 (64 bitų), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **Licencija**          | Chloros+ ([reikalingas mokamas planas](https://cloud.mapir.camera/pricing)) |
| **Atmintis**           | Mažiausiai 8 GB RAM (rekomenduojama 16 GB)                                  |
| **Internetas**         | Reikalingas licencijos aktyvavimui                                     |
| **Diskų vietos**       | Priklauso nuo projekto dydžio                                              |

{% hint style="warning" %}
**Licencijos reikalavimas**: CLI reikalauja mokamo Chloros+ prenumeratos. Standartiniai (nemokami) planai neturi prieigos prie CLI. Norėdami atnaujinti, apsilankykite [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).
{% endhint %}

## Greitasis pradžios vadovas

### Įdiegimas

#### Windows

CLI automatiškai įtraukiamas į Chloros diegimo programą:

1. Atsisiųskite ir paleiskite **Chloros Installer.exe**

2. Užbaigite diegimo vedlio veiksmus
3. CLI įdiegtas į: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style="success" %}
Diegimo programa automatiškai įtraukia `chloros-cli` į jūsų sistemos PATH. Po diegimo paleiskite terminalą iš naujo.
{% endhint %}

#### Linux

Įdiekite `.deb` paketą, skirtą jūsų architektūrai:

```bash
# Linux amd64
sudo dpkg -i chloros-amd64.deb

# Linux arm64 (NVIDIA Jetson, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

Išsamią Linux konfigūraciją rasite [Linux diegime](linux/linux-installation.md).

### Pirminis nustatymas

Prieš naudodami CLI, aktyvuokite savo Chloros+ licenciją:

**Windows:**

```powershell
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

**Linux:**

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process ~/images/dataset001
```

### Pagrindinis naudojimas

Aplanką apdorokite naudodami numatytuosius nustatymus:

**Windows:**

```powershell
chloros-cli process "C:\Images\Dataset001"
```

**Linux:**

```bash
chloros-cli process ~/images/dataset001
```

***

## Komandų žinynas

### Bendroji sintaksė

```
chloros-cli [global-options] <command> [command-options]
```

***

## Komandos

### `process` - Apdoroti vaizdus

Apdoroti aplanko vaizdus su kalibravimu.

**Sintaksė:**

```bash
chloros-cli process <input-folder> [options]
```

**Pavyzdžiai:**

```bash
# Windows
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance

# Linux
chloros-cli process ~/datasets/survey_001 --vignette --reflectance
```

#### Komandos apdorojimo parinktys

| Parinktis                | Tipas    | Numatytasis        | Aprašymas                                                                            |
| --------------------- | ------- | -------------- | -------------------------------------------------------------------------------------- |
| `<input-folder>`      | Kelias    | _Privaloma_     | Aplankas, kuriame yra RAW/JPG multispektriniai vaizdai                                         |
| `-o, --output`        | Kelias    | Tas pats kaip įvesties  | Aplankas, į kurį bus išsaugoti apdoroti vaizdai                                                     |
| `-n, --project-name`  | Stygas  | Sukurtas automatiškai | Pasirinktas projekto pavadinimas                                                                    |
| `--vignette`          | Žymė    | Įjungta        | Įjungti vinjetės korekciją                                                             |
| `--no-vignette`       | Žymė    | -              | Išjungti vinjetės korekciją                                                            |
| `--reflectance`       | Žymė    | Įjungta        | Įjungti atspindžio kalibravimą                                                         |
| `--no-reflectance`    | Žymė    | -              | Išjungti atspindžio kalibravimą                                                        |
| `--ppk`               | Žymė    | Išjungta       | Taikyti PPK korekcijas iš .daq šviesos jutiklio duomenų                                      |
| `--format`            | Pasirinkimas  | TIFF (16 bitų)  | Išvesties formatas: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size`   | Sveikasis skaičius | Automatinis           | Mažiausias tikslo dydis pikseliais kalibravimo skydelio aptikimui                          |
| `--target-clustering` | Sveikasis skaičius | Automatinis           | Tikslo grupuotės riba (0–100)                                                    |
| `--debayer`           | Pasirinkimas  | `standard`     | Debayerio metodas: `standard` arba `texture-aware` (tik Chloros+)                          |
| `--target`, `--targets` | Žymė  | Išjungta       | Ieškoti kalibravimo taškų tik „target“ arba „targets“ pakatalogiuose (pagreitina apdorojimą) |
| `--indices`           | Sąrašas    | Nėra           | Apskaičiuotini augmenijos indeksai (pvz., `--indices NDVI NDRE GNDVI`)                    |
| `--exposure-pin-1`    | Stygas  | Nėra           | Užfiksuoti kameros modelio ekspoziciją (1 kontaktas)                                                 |
| `--exposure-pin-2`    | Stygos  | Nėra           | Ekspozicijos fiksavimas kameros modeliui (2 kontaktas)                                                 |
| `--recal-interval`    | Sveikasis skaičius | Automatinis           | Pakalibravimo intervalas sekundėmis                                                      |
| `--timezone-offset`   | Sveikasis skaičius | 0              | Laiko juostos nuokrypis valandomis                                                               |

***

### `login` - Autentiškumo patvirtinimas

Prisijunkite naudodami savo Chloros+ prisijungimo duomenis, kad įgalintumėte CLI apdorojimą.

**Sintaksė:**

```bash
chloros-cli login <email> <password>
```

**Pavyzdys:**

```bash
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Specialieji simboliai**: Naudokite viengubas kabutes aplink slaptažodžius, kuriuose yra simbolių, pvz., `$`, `!`, arba tarpų.
{% endhint %}

**Rezultatas:**<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - Išvalyti prisijungimo duomenis

Išvalykite išsaugotus prisijungimo duomenis ir atsijunkite nuo savo paskyros.

**Sintaksė:**

```bash
chloros-cli logout
```

**Pavyzdys:**

```bash
chloros-cli logout
```

**Rezultatas:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% hint style="info" %}
**SDK Vartotojai**: Python SDK taip pat teikia programinį `logout()` metodą prisijungimo duomenims išvalyti Python skriptuose. Išsamią informaciją rasite [Python SDK dokumentacijoje](api-python-sdk.md#logout).
{% endhint %}

***

### `status` – Licencijos būklės patikrinimas

Rodo dabartinę licencijos ir autentiškumo būseną.

**Sintaksė:**

```bash
chloros-cli status
```

**Pavyzdys:**

```bash
chloros-cli status
```

**Rezultatas:**

```
╔══════════════════════════════════════╗
║     LICENSE & ACCOUNT INFORMATION    ║
╚══════════════════════════════════════╝

📧 Email: user@example.com
📋 Plan: Chloros+ Professional
🔓 API/CLI Access: Enabled
✓ Status: Active
```

***

### `export-status` – Eksporto eigos patikrinimas

Stebėkite 4-ojo srauto eksporto eigą apdorojimo metu arba po jo.

**Sintaksė:**

```bash
chloros-cli export-status
```

**Pavyzdys:**

```bash
chloros-cli export-status
```

**Naudojimo atvejis:** Vykdydami šią komandą apdorojimo metu, galite patikrinti eksporto eigą.***

### `language` – Sąsajos kalbos valdymas

Peržiūrėkite arba pakeiskite CLI sąsajos kalbą.

**Sintaksė:**

```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```

**Pavyzdžiai:**

```bash
# View current language
chloros-cli language

# List all 38 supported languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Change to Japanese
chloros-cli language ja
```

#### Palaikomos kalbos (iš viso 38)

| Kodas    | Kalba              | Pavadinimas kalba      |
| ------- | --------------------- | ---------------- |
| `en`    | Anglų               | English          |
| `es`    | Ispanų               | Español          |
| `pt`    | Portugalų            | Português        |
| `fr`    | Prancūzų                | Français         |
| `de`    | Vokiečių kalba                | Deutsch          |
| `it`    | Italų kalba               | Italiano         |
| `ja`    | Japonų kalba              | 日本語              |
| `ko`    | Korėjiečių kalba                | 한국어              |
| `zh`    | Kinų (supaprastinta)  | 简体中文             |
| `zh-TW` | Kinų (tradicinė) | 繁體中文             |
| `ru`    | Rusų               | Русский          |
| `nl`    | Olandų                 | Nederlands       |
| `ar`    | Arabų                | العربية          |
| `pl`    | Lenkų                | Polski           |
| `tr`    | Turkų               | Türkçe           |
| `hi`    | Hindi                 | हिंदी            |
| `id`    | Indoneziečių            | Bahasa Indonesia |
| `vi`    | Vietnamo            | Tiếng Việt       |
| `th`    | Tailandiečių                  | ไทย              |
| `sv`    | Švedų               | Svenska          |
| `da`    | Danų                | Dansk            |
| `no`    | Norvegų             | Norsk            |
| `fi`    | Suomi               | Suomi            |
| `el`    | Graikų                 | Ελληνικά         |
| `cs`    | Čekų                 | Čeština          |
| `hu`    | Vengrų             | Magyar           |
| `ro`    | Rumunų              | Română           |
| `uk`    | Ukrainiečių             | Українська       |
| `pt-BR` | Brazilijos portugalų  | Português Brasileiro |
| `zh-HK` | Kantono kalba             | 粵語             |
| `ms`    | Malajų kalba                 | Bahasa Melayu    |
| `sk`    | Slovakų kalba                | Slovenčina       |
| `bg`    | Bulgarų             | Български        |
| `hr`    | Kroatų              | Hrvatski         |
| `lt`    | Lietuvių            | Lietuvių         |
| `lv`    | Latvų               | Latviešu         |
| `et`    | Estų              | Eesti            |
| `sl`    | Slovėnų             | Slovenščina      |

{% hint style="success" %}
**Automatinis išsaugojimas**: Jūsų kalbos nustatymas išsaugomas `~/.chloros/cli_language.json` ir išlieka per visas sesijas.
{% endhint %}

***

### `set-project-folder` - Nustatyti numatytąjį projekto aplanką

Pakeisti numatytąjį projekto aplanko vietą (bendrai naudojamą su GUI Windows).

**Sintaksė:**

```bash
chloros-cli set-project-folder <folder-path>
```

**Pavyzdžiai:**

```bash
# Windows
chloros-cli set-project-folder "C:\Projects\2025"

# Linux
chloros-cli set-project-folder ~/projects/2025
```

***

### `get-project-folder` - Rodyti projekto aplanką

Rodo dabartinę numatytą projekto aplanko vietą.

**Sintaksė:**

```bash
chloros-cli get-project-folder
```

**Pavyzdys:**

```bash
chloros-cli get-project-folder
```

**Rezultatas:**

```

# Windows
ℹ Current project folder: C:\Projects\2025

# Linux
ℹ Current project folder: /home/user/.local/share/chloros/projects
```

***

### `reset-project-folder` – Atkurti numatytuosius nustatymus

Atkuria numatytąją projekto aplanko vietą.

**Sintaksė:**

```bash
chloros-cli reset-project-folder
```

***

### `selftest` – Vykdyti sistemos diagnostiką

Vykdykite 7 diagnostinius patikrinimus, kad patikrintumėte savo sistemos konfigūraciją.

**Sintaksė:**

```bash
chloros-cli selftest
```

**Atliekama diagnostika:**

1. Versijos patikrinimas
2. Prievado prieinamumas (5000)
3. Backend paleidimas
4. API ryšio testas
5. Sistemos informacija ir GPU aptikimas
6. Triukšmo šalinimo modelių patikrinimas
7. CUDA prieinamumo patikrinimas

{% hint style="info" %}
**Naudinga trikčių šalinimui**: Po įdiegimo paleiskite `selftest`, kad patikrintumėte, ar jūsų sistema sukonfigūruota teisingai, ypač Linux/Jetson, kur gali prireikti patikrinti GPU ir CUDA nustatymus.
{% endhint %}

***

### `update` – Atnaujinimų paieška (tik Linux)

Ieškokite ir įdiekite CLI atnaujinimus Linux sistemose.

**Sintaksė:**

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

| Parinktis    | Aprašymas                        |
| --------- | ---------------------------------- |
| `--check` | Tik ieškoti atnaujinimų, neįdiegti |

{% hint style="info" %}
Ši komanda prieinama tik Linux sistemose. Windows sistemose atnaujinimai pateikiami per diegimo programą.
{% endhint %}

***

## Bendrosios parinktys

Šios parinktys taikomos visoms komandoms:

| Parinktis            | Tipas    | Numatytasis       | Aprašymas                                      |
| ----------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe`   | Kelias    | Nustatoma automatiškai | Kelias į vykdomąjį failą |
| `--port`          | Sveikasis skaičius | 5000          | API užpakalinės dalies prievado numeris                          |
| `--restart`       | Žymė    | -             | Priversti paleisti foninį procesą iš naujo (nutraukia esamus procesus) |
| `--version`       | Žymė    | -             | Rodyti versijos informaciją ir uždaryti                |
| `--help`          | Žymė    | -             | Rodyti pagalbos informaciją ir uždaryti                   |

{% hint style="info" %}
**Automatinis užpakalinės dalies nustatymas**: `--backend-exe` kelias nustatomas automatiškai pagal platformą:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (rankinis)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

**Pavyzdys su bendrosiomis parinktimis:**

**Windows:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

**Linux:**

```bash
chloros-cli --port 5001 process ~/datasets/survey_001
```

***

## Apdorojimo nustatymų vadovas

### Lygiagretusis apdorojimas ir dinaminis skaičiavimo pritaikymas

Chloros 1.1.0 versijoje įtrauktas [dinaminis skaičiavimo pritaikymas](processing-architecture/dynamic-compute-adaptation.md) — apdorojimo variklis **automatiškai aptinka jūsų aparatinę įrangą** ir pasirenka optimalų strategiją:

| Platforma | Strategija | Darbininkai | Konvejeris | Pastabos |
| --- | --- | --- | --- | --- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` | Taupus atminties atžvilgiu, serijinis |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` | Lygiagretus GPU apdorojimas |
| **Stalinis kompiuteris su 8 GB GPU** | `GPU_SINGLE` | 3 | `tiled_gpu` | Geras stalinio kompiuterio našumas |
| **Stalinis kompiuteris su 12 GB ir daugiau GPU** | `GPU_PARALLEL` | 3–4 | `fused_gpu` | Optimalus stalinio kompiuterio našumas |
| **Tik CPU sistema** | `CPU_PARALLEL` | branduoliai – 1 | `cpu_fallback` | GPU nereikalingas |

{% hint style="success" %}
**Nereikia rankinio konfigūravimo!** Chloros automatiškai aptinka jūsų CPU, GPU, RAM ir (Jetson) šiluminius jutiklius, tada automatiškai sukonfigūruoja optimalų apdorojimo procesą.
{% endhint %}

### Debayer metodai

| Metodas | CLI Žymė | Kokybė | Greitis | Licencija |
| --- | --- | --- | --- | --- |
| **Standartinis (greitas, vidutinė kokybė)** | `--debayer standard` | Geras | Greitas | Nemokamas / Chloros+ |
| **Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė)** | `--debayer texture-aware` | Aukščiausia | Lėtas | Tik Chloros+ |

Numatytasis debayerio metodas yra **Standartinis**.**Tekstūros atpažinimo** metodas naudoja AI/ML triukšmo šalinimo modelį, kad būtų užtikrinta aukščiausia išvesties kokybė, tačiau tam reikalinga Chloros+ licencija ir NVIDIA GPU.

```bash
# Use Texture Aware debayer (Chloros+ only)
chloros-cli process ~/datasets/field_a --debayer texture-aware
```

### Vignette korekcija

**Ką daro:** Koreguoja šviesos silpimą vaizdo kraštuose (tamsesni kampai, dažni fotoaparato vaizduose).

* **Įjungta pagal numatytuosius nustatymus** – Daugumai vartotojų reikėtų palikti šią funkciją įjungtą
* Naudokite `--no-vignette`, kad išjungtumėte

{% hint style="success" %}
**Rekomendacija**: Visada įjunkite vinjetės korekciją, kad užtikrintumėte vienodą ryškumą visame kadre.
{% endhint %}

### Atspindžio kalibravimas

Naudojant kalibravimo plokštes konvertuoja neapdorotus jutiklio duomenis į standartizuotus atspindžio procentus.

* **Įjungta pagal numatytuosius nustatymus** – Būtina augmenijos analizei
* Reikia kalibravimo tikslinių plokščių vaizduose
* Naudokite `--no-reflectance`, kad išjungtumėte

{% hint style="info" %}
**Reikalavimai**: Užtikrinkite, kad kalibravimo plokštės būtų tinkamai eksponuotos ir matomos jūsų vaizduose, kad atspindžio konversija būtų tiksli.
{% endhint %}

### PPK korekcijos

**Ką tai daro:** Taiko post-processed kinematines korekcijas, naudodamas DAQ-A-SD žurnalo duomenis, siekiant pagerinti GPS tikslumą.

* **Išjungta pagal numatytuosius nustatymus**
* Naudokite `--ppk`, kad įjungtumėte
* Reikalauja .daq failų projekto aplanke iš MAPIR DAQ-A-SD šviesos jutiklio.

### Išvesties formatai

<table><thead><tr><th width="197">Formatas</th><th width="130.20001220703125">Bitų gylis</th><th width="116.5999755859375">Failo dydis</th><th>Tinkamiausias</th></tr></thead><tbody><tr><td><strong>TIFF (16 bitų)</strong> ⭐</td><td>16 bitų sveikasis skaičius</td><td>Didelis</td><td>GIS analizė, fotogrametrija (rekomenduojama)</td></tr><tr><td><strong>TIFF (32 bitai, procentai)</strong></td><td>32 bitų plūduriuojantis</td><td>Labai didelis</td><td>Mokslinė analizė, tyrimai</td></tr><tr><td><strong>PNG (8 bitų)</strong></td><td>8 bitų sveikasis skaičius</td><td>Vidutinis</td><td>Vizualinė apžiūra, dalijimasis internete</td></tr><tr><td><strong>JPG (8 bitų)</strong></td><td>8 bitų sveikasis skaičius</td><td>Mažas</td><td>Greitas peržiūrėjimas, suspaustas išvesties failas</td></tr></tbody></table>***

## Automatizavimas ir skriptų kūrimas

### PowerShell paketinis apdorojimas (Windows)

Automatiškai apdorokite kelis duomenų rinkinių aplankus naudojant Windows:

```powershell
# process_all_datasets.ps1

$datasets = Get-ChildItem "C:\Datasets\2025" -Directory

foreach ($dataset in $datasets) {
    Write-Host "Processing $($dataset.Name)..." -ForegroundColor Cyan
    
    chloros-cli process $dataset.FullName `
        --vignette `
        --reflectance
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "✓ $($dataset.Name) complete" -ForegroundColor Green
    } else {
        Write-Host "✗ $($dataset.Name) failed" -ForegroundColor Red
    }
}

Write-Host "All datasets processed!" -ForegroundColor Green
```

### Windows paketinis scenarijus (Windows)

Paprastas ciklas paketiniam apdorojimui Windows:

```batch
@echo off
echo Starting batch processing...

for /d %%i in (C:\Datasets\2025\*) do (
    echo.
    echo ========================================
    echo Processing: %%i
    echo ========================================
    chloros-cli process "%%i"
    
    if %ERRORLEVEL% EQU 0 (
        echo SUCCESS: %%i processed
    ) else (
        echo ERROR: %%i failed
    )
)

echo.
echo All datasets processed!
pause
```

### Bash paketinis apdorojimas (Linux)

Apdorokite kelis duomenų rinkinių aplankus Linux:

```bash
#!/bin/bash
# process_all_datasets.sh

for dataset in ~/datasets/2026/*/; do
    name=$(basename "$dataset")
    echo "Processing $name..."

    chloros-cli process "$dataset" \
        --vignette \
        --reflectance

    if [ $? -eq 0 ]; then
        echo "✓ $name complete"
    else
        echo "✗ $name failed"
    fi
done

echo "All datasets processed!"
```

### Python automatizavimo scenarijus (tarpusavio platformos)

Išplėstinė automatizacija su klaidų tvarkymu (veikia Windows ir Linux):

```python
import subprocess
import os
import sys
from pathlib import Path
from datetime import datetime

def process_dataset(input_folder):
    """Process a folder using Chloros CLI"""
    cmd = ['chloros-cli', 'process', str(input_folder)]
    
    # Execute command
    result = subprocess.run(
        cmd, 
        capture_output=True, 
        text=True,
        encoding='utf-8'
    )
    
    return result.returncode == 0, result.stdout, result.stderr

def main():
    """Process all datasets in a directory"""
    # Adjust path for your platform
    # Windows: Path('C:/Datasets/2025')
    # Linux:   Path.home() / 'datasets' / '2025'
    datasets_dir = Path('C:/Datasets/2025')
    log_file = Path('processing_log.txt')
    
    successful = []
    failed = []
    
    # Start processing
    print(f"Starting batch processing: {datetime.now()}")
    print(f"Scanning: {datasets_dir}")
    print("=" * 60)
    
    for dataset_folder in sorted(datasets_dir.iterdir()):
        if not dataset_folder.is_dir():
            continue
        
        print(f"\nProcessing: {dataset_folder.name}")
        
        success, stdout, stderr = process_dataset(dataset_folder)
        
        if success:
            print(f"✓ {dataset_folder.name} - SUCCESS")
            successful.append(dataset_folder.name)
        else:
            print(f"✗ {dataset_folder.name} - FAILED")
            failed.append(dataset_folder.name)
            
            # Log error details
            with open(log_file, 'a', encoding='utf-8') as f:
                f.write(f"\n=== {dataset_folder.name} - {datetime.now()} ===\n")
                f.write(f"STDOUT:\n{stdout}\n")
                f.write(f"STDERR:\n{stderr}\n")
    
    # Print summary
    print("\n" + "=" * 60)
    print(f"SUMMARY - Completed: {datetime.now()}")
    print(f"  Successful: {len(successful)}")
    print(f"  Failed: {len(failed)}")
    
    if failed:
        print(f"\nFailed folders:")
        for folder in failed:
            print(f"  - {folder}")
        print(f"\nCheck {log_file} for error details")
        sys.exit(1)
    else:
        print("\nAll datasets processed successfully!")
        sys.exit(0)

if __name__ == '__main__':
    main()
```

***

## Apdorojimo darbo eiga

### Standartinis darbo srautas

1. **Įvestis**: Aplankas, kuriame yra RAW/JPG vaizdų poros
2. **Aptikimas**: CLI automatiškai nuskaito palaikomus vaizdo failus
3. **Apdorojimas**: Lygiagretusis režimas pritaikomas prie jūsų procesoriaus branduolių skaičiaus (Chloros+)
4. **Išvestis**: Sukuria fotoaparato modelio pakatalogius su apdorotais vaizdais

### Pavyzdinė išvesties struktūra

```

MyProject/
├── project.json                             # Project metadata
├── 2025_0203_193056_008.JPG                # Original JPG
├── 2025_0203_193055_007.RAW                # Original RAW
└── Survey3N_RGN/                           # Processed outputs ✓
    ├── 2025_0203_193056_008_Reflectance.tif   # Calibrated reflectance
    ├── 2025_0203_193056_008_Target.tif        # Target detection
    └── ...
```

### Apdorojimo trukmės įvertinimai

Tipinė 100 vaizdų (kiekvienas 12 MP) apdorojimo trukmė:

| Platforma | Režimas | Numatoma trukmė | Pastabos |
| --- | --- | --- | --- |
| **Stalinis kompiuteris su 12 GB+ GPU** | `GPU_PARALLEL` | 5–10 min. | Greičiausias variantas |
| **Stalinis kompiuteris su 8 GB GPU** | `GPU_SINGLE` | 10–15 min. | Geras našumas |
| **Jetson Orin NX 16 GB** | `GPU_PARALLEL` | 15–25 min. | Atskirosios skaičiavimo sistemos |
| **Jetson Nano 8 GB** | `GPU_SINGLE` | 30–60 min. | Ribota atmintis |
| **Tik CPU** | `CPU_PARALLEL` | 20–40 min | Nereikalingas GPU |

{% hint style="info" %}
**Našumo patarimas**: Apdorojimo laikas priklauso nuo vaizdų skaičiaus, skiriamosios gebos, debayerio metodo ir aparatinės įrangos. Tekstūrą atpažįstantis debayeris užtrunka žymiai ilgiau nei standartinis. Išsamią informaciją rasite skyriuje [Dinaminis skaičiavimo pritaikymas](processing-architecture/dynamic-compute-adaptation.md).
{% endhint %}

***

## Problemų sprendimas

### CLI nerastas

**Windows klaida:**

```
'chloros-cli' is not recognized as an internal or external command
```

**Windows Sprendimai:**

1. Patikrinkite įdiegimo vietą:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. Jei nėra PATH, naudokite pilną kelią:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. Pridėkite į PATH rankiniu būdu:
   * Atidarykite „Sistemos savybės“ → „Aplinkos kintamieji“
   * Redaguokite kintamąjį PATH
   * Pridėkite: `C:\Program Files\Chloros\resources\cli`
   * Perkraukite terminalą

**Linux Klaida:**

```
chloros-cli: command not found
```

**Linux Sprendimai:**

1. Patikrinkite įdiegimą:

```bash
which chloros-cli
dpkg -L chloros-amd64  # or chloros-arm64-jp6
```

2. Perkraukite savo aplinką:

```bash
source ~/.bashrc
```

3. Patikrinkite leidimus:

```bash
sudo chmod +x /usr/bin/chloros-cli
```

***

### Nepavyko paleisti užkulisio**Klaida:**

```

Backend failed to start within 30 seconds
```

**Sprendimai:**

1. Patikrinkite, ar užkulisiai jau veikia (pirmiausia juos uždarykite)
2. Patikrinkite, ar ugniasienė neblokuoja (Windows) arba patikrinkite prievado prieinamumą (Linux: `lsof -i :5000`)
3. Išbandykite kitą prievadą:

```bash
# Windows
chloros-cli --port 5001 process "C:\Datasets\Field_A"

# Linux
chloros-cli --port 5001 process ~/datasets/field_a
```

4. Priverstinis backend paleidimas iš naujo:

```bash
# Windows
chloros-cli --restart process "C:\Datasets\Field_A"

# Linux
chloros-cli --restart process ~/datasets/field_a
```

5. Esant klaidai Linux, patikrinkite, ar yra backend vykdomasis failas:

```bash
ls -la /usr/lib/chloros/chloros-backend
```

***

### Licencijos / Autentifikavimo problemos**Klaida:**

```

Chloros+ license required for CLI access
```

**Sprendimai:**

1. Patikrinkite, ar turite aktyvią Chloros+ prenumeratą
2. Prisijunkite naudodami savo prisijungimo duomenis:

```bash
chloros-cli login user@example.com 'password'
```

3. Patikrinkite licencijos būseną:

```bash
chloros-cli status
```

4. Susisiekite su palaikymo tarnyba: info@mapir.camera

***

### Nerasta vaizdų**Klaida:**

```

No images found in the specified folder
```

**Sprendimai:**

1. Patikrinkite, ar aplanke yra palaikomi formatai (.RAW, .TIF, .JPG)
2. Patikrinkite, ar aplanko kelias yra teisingas (keliams su tarpais naudokite kabutes)
3. Įsitikinkite, kad turite skaitymo teises į aplanką
4. Patikrinkite, ar failų plėtiniai yra teisingi

***

### Apdorojimas sustoja arba įstrigo**Sprendimai:**

1. Patikrinkite laisvą disko vietą (įsitikinkite, kad jos pakanka išvesties duomenims)
2. Uždarykite kitas programas, kad atlaisvintumėte atmintį
3. Sumažinkite vaizdų skaičių (apdorokite partijomis)

***

### Prievadas jau naudojamas**Klaida:**

```

Port 5000 is already in use
```

**Sprendimai:**

**Windows:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

**Linux:**

```bash
# Find what's using port 5000
lsof -i :5000

# Use a different port
chloros-cli --port 5001 process ~/datasets/field_a
```

***

## DUK

### K: Ar man reikia licencijos CLI?

**A:**Taip! CLI reikalauja mokamos**Chloros+ licencijos**.

* ❌ Standartinis (nemokamas) planas: CLI išjungta
* ✅ Chloros+ (mokami) planai: CLI visiškai įjungta

Prenumeruokite adresu: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### K: Ar galiu naudoti CLI serveryje be GUI?**A:** Taip! CLI veikia visiškai be grafinės sąsajos. Tai yra pagrindinis Linux naudojimo atvejis.**Windows serveris:**
* Windows Server 2016 ar naujesnė versija
* Įdiegta „Visual C++ Redistributable“

**Linux serveris:**
* „Ubuntu 20.04+“ / „Debian 11+“ (amd64) arba „JetPack 6“ (arm64)
* Įdiegti per `.deb` paketą

**Abi platformos:**
* Mažiausiai 8 GB RAM (rekomenduojama 16 GB)
* Vienkartinis licencijos aktyvavimas: `chloros-cli login user@example.com 'password'`

***

### K: Kur išsaugomi apdoroti vaizdai?**A:**Pagal numatytuosius nustatymus apdoroti vaizdai išsaugomi**tame pačiame aplanke kaip ir įvesties failai**, kameros modelio pakatalogiuose (pvz., `Survey3N_RGN/`).

Naudokite parinktį `-o`, jei norite nurodyti kitą išvesties aplanką:

```bash
# Windows
chloros-cli process "C:\Input" -o "D:\Output"

# Linux
chloros-cli process ~/input -o ~/output
```

***

### K: Ar galiu apdoroti kelis aplankus vienu metu?**A:** Ne tiesiogiai vienu komandu, bet galite naudoti skriptus, kad apdorotumėte aplankus paeiliui. Žr. skyrių [Automatizavimas ir skriptavimas](CLI.md#automation--scripting).***

### K: Kaip išsaugoti CLI išvestį į žurnalo failą?**PowerShell:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**Batch:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

**Linux Bash:**

```bash
chloros-cli process ~/datasets/field_a 2>&1 | tee processing.log
```

***

### K: Kas nutiks, jei apdorojimo metu paspausiu Ctrl+C?**A:** CLI:

1. Tvarkingai sustabdys apdorojimą
2. Uždarys užkurtį
3. Išsijungs su kodu 130

Iš dalies apdoroti vaizdai gali likti išvesties aplanke.

***

### K: Ar galiu automatizuoti CLI apdorojimą?**A:** Žinoma! CLI yra sukurtas automatizavimui. Žiūrėkite [Automatizavimas ir skriptavimas](CLI.md#automation--scripting) pavyzdžius, skirtus PowerShell (Windows), Batch (Windows), Bash (Linux) ir Python (tarpusavio platformos) pavyzdžius.***

### K: Kaip patikrinti CLI versiją?**A:**

```bash
chloros-cli --version
```

**Rezultatas:**

```

Chloros CLI 1.1.0
```

***

## Pagalba

### Pagalba iš komandinės eilutės

Peržiūrėkite pagalbos informaciją tiesiogiai CLI:

```bash
# General help
chloros-cli --help

# Command-specific help
chloros-cli process --help
chloros-cli login --help
chloros-cli language --help
```

### Pagalbos kanalai

* **El. paštas**: info@mapir.camera
* **Svetainė**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Kainos**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)***

## Išsamūs pavyzdžiai

### 1 pavyzdys: Pagrindinis apdorojimas

Apdorojimas naudojant numatytuosius nustatymus (vignette, atspindys):

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a_2025_01_15
```

***

### 2 pavyzdys: Aukštos kokybės moksliniai rezultatai

32 bitų plūduriuojantis skaičius TIFF:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --format "TIFF (32-bit, Percent)" \
  --vignette \
  --reflectance
```

***

### 3 pavyzdys: Greitas peržiūros apdorojimas

8 bitų PNG be kalibravimo greitam peržiūrėjimui:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --format "PNG (8-bit)" \
  --no-vignette \
  --no-reflectance
```

***

### 4 pavyzdys: apdorojimas su PPK korekcija

Taikykite PPK korekcijas su atspindžio koeficientu:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --ppk \
  --reflectance
```

***

### 5 pavyzdys: Pasirinktinė išvesties vieta

Apdorokite į kitą vietą su konkrečiu formatu:

**Windows:**

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

**Linux:**

```bash
chloros-cli process ~/input/raw_images \
  -o ~/output/processed \
  --format "TIFF (16-bit)"
```

***

### 6 pavyzdys: Autentifikavimo eiga

Pilnas autentiškumo patvirtinimo procesas (vienodas visose platformose):

```bash
# Step 1: Login
chloros-cli login user@example.com 'MyP@ssw0rd'

# Step 2: Verify status
chloros-cli status

# Step 3: Process images
# Windows: chloros-cli process "C:\Datasets\Field_A"
# Linux:   chloros-cli process ~/datasets/field_a
chloros-cli process ~/datasets/field_a

# Step 4: Logout (optional, when switching accounts)
chloros-cli logout
```

***

### 7 pavyzdys: Daugiakalbė sąsaja

Sąsajos kalbos keitimas (vienodas visose platformose):

```bash
# List available languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Process with Spanish interface
# Windows: chloros-cli process "C:\Vuelos\Campo_A"
# Linux:   chloros-cli process ~/vuelos/campo_a
chloros-cli process ~/vuelos/campo_a

# Change back to English
chloros-cli language en
```
