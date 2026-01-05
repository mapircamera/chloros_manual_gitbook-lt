# CLI : Komandinė eilutė

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>**Chloros CLI** suteikia galingą komandinės eilutės prieigą prie Chloros vaizdų apdorojimo variklio, leidžiant automatizuoti, kurti scenarijus ir vykdyti be galvutės operacijas jūsų vaizdų apdorojimo darbo srautams.

### Pagrindinės funkcijos

* 🚀 **Automatizavimas** – scenarijų paketinis apdorojimas kelių duomenų rinkinių
* 🔗 **Integracija** – įterpimas į esamus darbo srautus ir procesus
* 💻 **Veikimas be grafinės sąsajos** – veikimas be GUI
* 🌍 **Daugiakalbė** – 38 kalbų palaikymas
* ⚡ **Lygiagretus apdorojimas** – dinamiškai pritaikomas prie jūsų CPU (iki 16 lygiagrečių darbininkų)

### Reikalavimai

| Reikalavimas          | Išsami informacija                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Operacinė sistema** | Windows 10/11 (64 bitai)                                              |
| **Licencija**          | Chloros+ ([reikalingas mokamas planas](https://cloud.mapir.camera/pricing)) |
| **Atmintis**           | Mažiausiai 8 GB RAM (rekomenduojama 16 GB)                                  |
| **Internetas**         | Reikalingas licencijos aktyvavimui                                     |
| **Diskų erdvė**       | Priklauso nuo projekto dydžio                                              |

{% hint style=&quot;warning&quot; %}
**Licencijos reikalavimas**: CLI reikalauja mokamo Chloros+ prenumeratos. Standartiniai (nemokami) planai neturi CLI prieigos. Norėdami atnaujinti, apsilankykite [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).
{% endhint %}

## Greitasis pradžios vadovas

### Įdiegimas

CLI automatiškai įtraukiamas į Chloros diegimo programą:

1. Atsisiųskite ir paleiskite **Chloros Installer.exe**

2. Užbaigite diegimo vedlio veiksmus
3. CLI įdiegta į: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style=&quot;success&quot; %}
Diegimo programa automatiškai įtraukia `chloros-cli` į jūsų sistemos PATH. Po diegimo iš naujo paleiskite terminalą.
{% endhint %}

### Pirmasis nustatymas

Prieš naudodami CLI, aktyvuokite savo Chloros+ licenciją:

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

### Pagrindinis naudojimas

Apdorokite aplanką su numatytaisiais nustatymais:

```powershell
chloros-cli process "C:\Images\Dataset001"
```

***

## Komandų žinynas

### Bendroji sintaksė

```
chloros-cli [global-options] <command> [command-options]
```

***

## Komandos

### `process` - Apdorokite vaizdus

Apdorokite aplanką su kalibravimu.

**Sintaksė:**

```bash
chloros-cli process <input-folder> [options]
```

**Pavyzdys:**

```powershell
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance
```

#### Apdorojimo komandos parinktys

| Parinktis                | Tipas    | Numatytasis        | Aprašymas                                                                            |
| --------------------- | ------- | -------------- | -------------------------------------------------------------------------------------- |
| `<input-folder>`      | Kelias    | _Reikalingas_     | Aplankas, kuriame yra RAW/JPG daugiaspektriniai vaizdai                                         |
| `-o, --output`        | Kelias    | Tas pats kaip įvesties  | Apdorotų vaizdų išvesties aplankas                                                     |
| `-n, --project-name`  | Stygos  | Automatiškai sugeneruotas | Pasirinktinis projekto pavadinimas                                                                    |
| `--vignette`          | Žymė    | Įjungta        | Įjungti vinjetės korekciją                                                             |
| `--no-vignette`       | Žymė    | -              | Išjungti vinjetės korekciją                                                            |
| `--reflectance`       | Žymė    | Įjungta        | Įjungti atspindžio kalibravimą                                                         |
| `--no-reflectance`    | Žymė    | -              | Išjungti atspindžio kalibravimą                                                        |
| `--ppk`               | Žymė    | Išjungta       | Taikyti PPK korekcijas iš .daq šviesos jutiklio duomenų                                      |
| `--format`            | Pasirinkimas  | TIFF (16 bitų)  | Išvesties formatas: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size`   | Sveikasis skaičius | Automatinis           | Minimalus tikslinis dydis pikseliais kalibravimo skydelio aptikimui                          |
| `--target-clustering` | Sveikasis skaičius | Automatinis           | Tikslinio klasterio riba (0–100)                                                    |
| `--exposure-pin-1`    | Stygos  | Nėra           | Užrakinti ekspoziciją kameros modeliui (1 kontaktas)                                                 |
| `--exposure-pin-2`    | Stygos  | Nėra           | Užrakinti ekspoziciją kameros modeliui (2 kontaktas)                                                 |
| `--recal-interval`    | Sveikasis skaičius | Automatinis           | Pakalibravimo intervalas sekundėmis                                                      |
| `--timezone-offset`   | Sveikasis skaičius | 0              | Laiko juostos nuokrypis valandomis                                                               |

***

### `login` – Autentiškumo patvirtinimas

Prisijunkite naudodami savo Chloros+ prisijungimo duomenis, kad įgalintumėte CLI apdorojimą.

**Sintaksė:**

```bash
chloros-cli login <email> <password>
```

**Pavyzdys:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style=&quot;warning&quot; %}
**Specialieji simboliai**: Naudokite viengubas kabutes aplink slaptažodžius, kuriuose yra simbolių, pvz., `$`, `!`, arba tarpai.
{% endhint %}

**Rezultatas:**<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - Išvalyti prisijungimo duomenis

Išvalykite saugomus prisijungimo duomenis ir atsijunkite nuo savo paskyros.

**Sintaksė:**

```bash
chloros-cli logout
```

**Pavyzdys:**

```powershell
chloros-cli logout
```

**Rezultatas:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% hint style=&quot;info&quot; %}
**SDK Vartotojai**: Python SDK taip pat teikia programinį `logout()` metodą, skirtą išvalyti kredencialus Python scenarijuose. Išsamią informaciją rasite [Python SDK dokumentacijoje](api-python-sdk.md#logout).
{% endhint %}

***

### `status` – licencijos būsenos patikrinimas

Rodo dabartinę licenciją ir autentiškumo būseną.

**Sintaksė:**

```bash
chloros-cli status
```

**Pavyzdys:**

```powershell
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

### `export-status` – eksporto pažangos patikrinimas

Stebėti 4-ojo sriegio eksporto pažangą apdorojimo metu arba po jo.

**Sintaksė:**

```bash
chloros-cli export-status
```

**Pavyzdys:**

```powershell
chloros-cli export-status
```

**Naudojimo atvejis:** Šią komandą vykdykite apdorojimo metu, kad patikrintumėte eksporto pažangą.***

### `language` – sąsajos kalbos valdymas

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

```powershell
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

| Kodas    | Kalba              | Gimtoji pavadinimas      |
| ------- | --------------------- | ---------------- |
| `en`    | Anglų               | Anglų          |
| `es`    | Ispanų               | Ispanų          |
| `pt`    | Portugalų            | Portugalų        |
| `fr`    | Prancūzų                | Français         |
| `de`    | Vokiečių                | Deutsch          |
| `it`    | Italų               | Italiano         |
| `ja`    | Japonų              | 日本語              |
| `ko`    | Korėjiečių                | 한국어              |
| `zh`    | Kinų (supaprastinta)  | 简体中文             |
| `zh-TW` | Kinų (tradicinė) | 繁體中文             |
| `ru`    | Rusų               | Русский          |
| `nl`    | Olandų                 | Nederlands       |
| `ar`    | Arabų                | العربية          |
| `pl`    | Lenkų                | Polski           |
| `tr`    | Turkų               | Türkçe           |
| `hi`    | Hindi                 | हिंदी            |
| `id`    | Indoneziečių            | Bahasa Indonesia |
| `vi`    | Vietnamo kalba            | Tiếng Việt       |
| `th`    | Tailando kalba                  | ไทย              |
| `sv`    | Švedų kalba               | Svenska          |
| `da`    | Danų kalba                | Dansk            |
| `no`    | Norvegų             | Norsk            |
| `fi`    | Suomų               | Suomi            |
| `el`    | Graikų                 | Ελληνικά         |
| `cs`    | Čekų                 | Čeština          |
| `hu`    | Vengrų             | Magyar           |
| `ro`    | Rumunų              | Română           |
| `uk`    | Ukrainiečių             | Українська       |
| `pt-BR` | Brazilijos portugalų  | Português Brasileiro |
| `zh-HK` | Kantono             | 粵語             |
| `ms`    | Malajų                 | Bahasa Melayu    |
| `sk`    | Slovakų                | Slovenčina       |
| `bg`    | Bulgarų             | Български        |
| `hr`    | Kroatų kalba              | Hrvatski         |
| `lt`    | Lietuvių kalba            | Lietuvių         |
| `lv`    | Latvų kalba               | Latviešu         |
| `et`    | Estų              | Eesti            |
| `sl`    | Slovėnų             | Slovenščina      |

{% hint style=&quot;success&quot; %}
**Automatinis išsaugojimas**: Jūsų kalbos pasirinkimas išsaugomas `~/.chloros/cli_language.json` ir išlieka visose sesijose.
{% endhint %}

***

### `set-project-folder` - Nustatyti numatytąjį projekto aplanką

Pakeisti numatytąjį projekto aplanko vietą (bendrai su GUI).

**Sintaksė:**

```bash
chloros-cli set-project-folder <folder-path>
```

**Pavyzdys:**

```powershell
chloros-cli set-project-folder "C:\Projects\2025"
```

***

### `get-project-folder` – Rodyti projekto aplanką

Rodyti dabartinę numatytą projekto aplanko vietą.

**Sintaksė:**

```bash
chloros-cli get-project-folder
```

**Pavyzdys:**

```powershell
chloros-cli get-project-folder
```

**Rezultatas:**

```
ℹ Current project folder: C:\Projects\2025
```

***

### `reset-project-folder` – Atstatyti numatytąją vietą

Atstatyti numatytąją projekto aplanko vietą.

**Sintaksė:**

```bash
chloros-cli reset-project-folder
```

***

## Bendrosios parinktys

Šios parinktys taikomos visoms komandoms:

| Parinktis          | Tipas    | Numatytasis       | Aprašymas                                      |
| --------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe` | Kelias    | Automatiškai nustatytas | Kelias į pagrindinio kompiuterio vykdomąjį failą                       |
| `--port`        | Sveikasis skaičius | 5000          | Pagrindinio kompiuterio API prievado numeris                          |
| `--restart`     | Žymė    | -             | Priversti paleisti iš naujo backend (nutraukia esamus procesus) |
| `--version`     | Žymė    | -             | Rodyti versijos informaciją ir uždaryti                |
| `--help`        | Žymė    | -             | Rodyti pagalbos informaciją ir uždaryti                   |

**Pavyzdys su bendrosiomis parinktimis:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

***

## Apdorojimo nustatymų vadovas

### Lygiagretus apdorojimas

Chloros+ CLI **automatiškai pritaiko**lygiagretų apdorojimą prie jūsų kompiuterio galimybių:**Kaip tai veikia:**

* Nustato jūsų CPU branduolius ir RAM
* Paskirsto darbininkus: **2× CPU branduoliai** (naudoja hiperthreading)
* **Maksimalus skaičius: 16 lygiagrečių darbininkų** (dėl stabilumo)**Sistemos lygiai:**

| Sistemos tipas   | CPU        | RAM      | Darbininkai  | Našumas     |
| ------------- | ---------- | -------- | -------- | --------------- |
| **Aukščiausios klasės**  | 16+ branduoliai  | 32+ GB   | Iki 16 | Maksimalus greitis   |
| **Vidutinio lygio** | 8–15 branduolių | 16–31 GB | 8–16     | Puikus greitis |
| **Žemo lygio**   | 4–7 branduolių  | 8–15 GB  | 4–8      | Geras greitis      |

{% hint style=&quot;success&quot; %}
**Automatinis optimizavimas**: CLI automatiškai nustato jūsų sistemos specifikacijas ir konfigūruoja optimalų lygiagretų apdorojimą. Nereikia jokio rankinio konfigūravimo!
{% endhint %}

### Debayer metodai

CLI naudoja **Aukštą kokybę (greičiau)** kaip numatytąjį ir rekomenduojamą debayer algoritmą:

| Metodas                      | Kokybė | Greitis | Aprašymas                                 |
| --------------------------- | ------- | ----- | ------------------------------------------- |
| **Aukšta kokybė (greičiau)** ⭐ | ⭐⭐⭐⭐    | ⚡⚡⚡   | Kraštų atpažinimo algoritmas (numatyta, rekomenduojama) |

### Vignette korekcija

**Ką daro:** Koreguoja šviesos silpimą vaizdo kraštuose (tamsesni kampai, dažni kameros vaizduose).

* **Įjungta pagal numatytuosius nustatymus** – dauguma vartotojų turėtų palikti šią funkciją įjungtą
* Norėdami išjungti, naudokite `--no-vignette`

{% hint style=&quot;success&quot; %}
**Rekomendacija**: visada įjunkite vinjetės korekciją, kad užtikrintumėte vienodą ryškumą visame kadre.
{% endhint %}

### Atspindžio kalibravimas

Naudojant kalibravimo skydelius, konvertuoja neapdorotus jutiklio vertes į standartizuotus atspindžio procentus.

* **Įjungta pagal numatytuosius nustatymus** – būtina augmenijos analizei.
* Reikia kalibravimo tikslinių plokščių vaizduose.
* Norėdami išjungti, naudokite `--no-reflectance`.

{% hint style=&quot;info&quot; %}
**Reikalavimai**: užtikrinkite, kad kalibravimo plokštės būtų tinkamai eksponuotos ir matomos jūsų vaizduose, kad atspindžio konversija būtų tiksli.
{% endhint %}

### PPK korekcijos

**Ką daro:** taiko post-processed kinematic korekcijas, naudojant DAQ-A-SD žurnalo duomenis, siekiant pagerinti GPS tikslumą.

* **Pagal numatytuosius nustatymus išjungta**
* Norėdami įjungti, naudokite `--ppk`
* Reikalingi .daq failai projekto aplanke iš MAPIR DAQ-A-SD šviesos jutiklio.

### Išvesties formatai

<table><thead><tr><th width="197">Formatas</th><th width="130.20001220703125">Bitų gylis</th><th width="116.5999755859375">Failo dydis</th><th>Tinkamiausias</th></tr></thead><tbody><tr><td><strong>TIFF (16 bitų)</strong> ⭐</td><td>16 bitų sveikasis skaičius</td><td>Didelis</td><td>GIS analizė, fotogrametrija (rekomenduojama)</td></tr><tr><td><strong>TIFF (32 bitai, procentai)</strong></td><td>32 bitų plūduriuojantis</td><td>Labai didelis</td><td>Mokslinė analizė, tyrimai</td></tr><tr><td><strong>PNG (8 bitai)</strong></td><td>8 bitų sveikasis skaičius</td><td>Vidutinis</td><td>Vizualinis patikrinimas, dalijimasis internete</td></tr><tr><td><strong>JPG (8 bitai)</strong></td><td>8 bitų sveikasis skaičius</td><td>Mažas</td><td>Greitas peržiūrėjimas, suspaustas išvesties failas</td></tr></tbody></table>***

## Automatizavimas ir scenarijų kūrimas

### PowerShell paketinis apdorojimas

Automatinis kelių duomenų rinkinių aplankų apdorojimas:

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

### Windows paketinis scenarijus

Paprastas ciklas paketinio apdorojimo:

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

### Python automatizavimo scenarijus

Išplėstinis automatizavimas su klaidų tvarkymu:

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

### Standartinė darbo eiga

1. **Įvestis**: aplankas, kuriame yra RAW/JPG vaizdų poros
2. **Atrandimas**: CLI automatiškai nuskaito palaikomus vaizdo failus
3. **Apdorojimas**: Lygiagretusis režimas pritaikomas prie jūsų CPU branduolių (Chloros+)
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

| Režimas              | Laikas      | Techninė įranga                                     |
| ----------------- | --------- | -------------------------------------------- |
| **Lygiagretus režimas** | 5–10 min.  | i7/Ryzen 7, 16 GB RAM, SSD (iki 16 darbuotojų) |
| **Lygiagretus režimas** | 10–15 min. | i5/Ryzen 5, 8 GB RAM, HDD (iki 8 darbuotojų)   |

{% hint style=&quot;info&quot; %}
**Naudingas patarimas**: apdorojimo laikas priklauso nuo vaizdų skaičiaus, skiriamosios gebos ir kompiuterio specifikacijų.
{% endhint %}

***

## Trikčių šalinimas

### CLI nerastas

**Klaida:**

```
'chloros-cli' is not recognized as an internal or external command
```

**Sprendimai:**

1. Patikrinkite įdiegimo vietą:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. Jei nėra PATH, naudokite pilną kelią:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. Rankiniu būdu pridėkite į PATH:
   * Atidarykite „Sistemos savybės“ → „Aplinkos kintamieji“
   * Redaguokite PATH kintamąjį
   * Pridėkite: `C:\Program Files\Chloros\resources\cli`
   * Paleiskite terminalą iš naujo.

***

### Nepavyko paleisti užpakalinės dalies**Klaida:**

```

Backend failed to start within 30 seconds
```

**Sprendimai:**

1. Patikrinkite, ar užpakalinė dalis jau veikia (pirmiausia ją uždarykite).
2. Patikrinkite, ar Windows ugniasienė neblokuoja.
3. Išbandykite kitą prievadą:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

4. Priversti paleisti backend:

```powershell
chloros-cli --restart process "C:\Datasets\Field_A"
```

***

### Licencijos / autentiškumo problemos**Klaida:**

```

Chloros+ license required for CLI access
```

**Sprendimai:**

1. Patikrinkite, ar turite aktyvų Chloros+ prenumeratą.
2. Prisijunkite naudodami savo prisijungimo duomenis:

```powershell
chloros-cli login user@example.com 'password'
```

3. Patikrinkite licencijos būseną:

```powershell
chloros-cli status
```

4. Susisiekite su pagalbos tarnyba: info@mapir.camera

***

### Nėra rastų vaizdų**Klaida:**

```

No images found in the specified folder
```

**Sprendimai:**

1. Patikrinkite, ar aplanke yra palaikomi formatai (.RAW, .TIF, .JPG).
2. Patikrinkite, ar aplanko kelias yra teisingas (keliuose su tarpais naudokite kabutes).
3. Įsitikinkite, kad turite teisę skaityti aplanką.
4. Patikrinkite, ar failų plėtiniai yra teisingi.

***

### Apdorojimas sustoja arba užstringa**Sprendimai:**

1. Patikrinkite laisvą disko vietą (įsitikinkite, kad jos pakanka išvesties failams).
2. Uždarykite kitas programas, kad atlaisvintumėte atmintį.
3. Sumažinkite vaizdų skaičių (apdorokite partijomis).

***

### Prieiga jau naudojama**Klaida:**

```

Port 5000 is already in use
```

**Sprendimas:**

Nurodykite kitą prievadą:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

***

## DUK

### K: Ar man reikia licencijos CLI?

**Atsakymas:**Taip! CLI reikalauja mokamos**Chloros+ licencijos**.

* ❌ Standartinis (nemokamas) planas: CLI išjungtas
* ✅ Chloros+ (mokami) planai: CLI visiškai įjungtas

Prenumeruokite: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### K: Ar galiu naudoti CLI serveryje be GUI?**A:** Taip! CLI veikia visiškai be grafinės sąsajos. Reikalavimai:

* Windows Server 2016 arba naujesnė versija
* Įdiegta Visual C++ Redistributable
* Pakankamai RAM (mažiausiai 8 GB, rekomenduojama 16 GB)
* Vienkartinė GUI licencijos aktyvacija bet kuriame kompiuteryje

***

### K: Kur saugomi apdoroti vaizdai?**A:**Pagal numatytuosius nustatymus apdoroti vaizdai saugomi**tame pačiame aplanke kaip ir įvesties** vaizdai, kameros modelio pakatalogiuose (pvz., `Survey3N_RGN/`).

Naudokite `-o` parinktį, kad nurodytumėte kitą išvesties aplanką:

```powershell
chloros-cli process "C:\Input" -o "D:\Output"
```

***

### K: Ar galiu apdoroti kelis aplankus vienu metu?**A:** Ne tiesiogiai vienu komandomis, bet galite naudoti scenarijus, kad apdorotumėte aplankus paeiliui. Žr. skyrių [Automatizavimas ir scenarijai](CLI.md#automation--scripting).***

### K: Kaip išsaugoti CLI išvestį į žurnalo failą?**PowerShell:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**Paketas:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

***

### K: Kas atsitiks, jei apdorojimo metu paspausiu Ctrl+C?**A:** CLI:

1. Gražiai sustabdys apdorojimą
2. Išjungs užpakalinę sistemą
3. Uždarys su kodu 130

Iš dalies apdoroti vaizdai gali likti išvesties aplanke.

***

### K: Ar galiu automatizuoti CLI apdorojimą?**A:** Žinoma! CLI yra sukurtas automatizavimui. Žr. [Automatizavimas ir scenarijų kūrimas](CLI.md#automation--scripting) dėl PowerShell, Batch ir Python pavyzdžių.***

### K: Kaip patikrinti CLI versiją?**A:**

```powershell
chloros-cli --version
```

**Rezultatas:**

```

Chloros CLI 1.0.2
```

***

## Pagalba

### Komandinės eilutės pagalba

Pagalbos informaciją galite peržiūrėti tiesiogiai CLI:

```powershell
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

### 1 pavyzdys: pagrindinis apdorojimas

Apdorojimas naudojant numatytuosius nustatymus (vinjetė, atspindys):

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

***

### 2 pavyzdys: aukštos kokybės moksliniai rezultatai

32 bitų plūduriuojantis TIFF:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

***

### 3 pavyzdys: greitas peržiūros apdorojimas

8 bitų PNG be kalibravimo greitam peržiūrėjimui:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

***

### 4 pavyzdys: PPK koreguotas apdorojimas

Taikykite PPK korekcijas su atspindžiu:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

***

### 5 pavyzdys: Pasirinktinė išvesties vieta

Apdorokite į kitą diską su konkrečiu formatu:

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

***

### 6 pavyzdys: Autentiškumo patvirtinimo darbo eiga

Užbaigti autentiškumo patvirtinimo eigą:

```powershell
# Step 1: Login
chloros-cli login user@example.com 'MyP@ssw0rd'

# Step 2: Verify status
chloros-cli status

# Step 3: Process images
chloros-cli process "C:\Datasets\Field_A"

# Step 4: Logout (optional, when switching accounts)
chloros-cli logout
```

***

### 7 pavyzdys: Daugiakalbė naudojimo galimybė

Pakeisti sąsajos kalbą:

```powershell
# List available languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Process with Spanish interface
chloros-cli process "C:\Vuelos\Campo_A"

# Change back to English
chloros-cli language en
```
