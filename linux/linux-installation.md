# Linux įdiegimas

Chloros platinamas kaip Linux paketas, kurį sudaro `.deb` paketai, įdiegiantys CLI ir užkurtį. Python SDK įdiegiamas atskirai per pip.

***

## Linux amd64 (x86_64)

### Sistemos reikalavimai

| Reikalavimas | Minimalus | Rekomenduojamas |
| --- | --- | --- |
| **Distribucija** | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+ |
| **Procesorius** | x86_64 (Intel/AMD) | Intel Core i7 ar geresnis |
| **Atmintis (RAM)** | 8 GB | 16 GB ar daugiau |
| **Vaizdo plokštė** | Nereikalinga (apdorojama procesoriumi) | NVIDIA GPU su 4 GB+ VRAM |
| **Saugykla** | 2 GB laisvos vietos | SSD su 10 GB+ laisvos vietos |
| **Python** | Python 3.7+ (skirta SDK) | Python 3.10+ |

### Įdiegimas

Atsisiųskite `.deb` paketą ir įdiekite:

```bash
sudo dpkg -i chloros-amd64.deb
```

Patikrinkite įdiegimą:

```bash
chloros-cli --version
```

***

## Linux arm64 (NVIDIA Jetson)

### Sistemos reikalavimai

| Reikalavimas | Minimalus | Rekomenduojamas |
| --- | --- | --- |
| **Platforma** | NVIDIA Jetson su JetPack 6 | Jetson Orin NX 16 GB arba AGX Orin |
| **JetPack** | JetPack 6.x | Naujausia JetPack 6 versija |
| **Atmintis (RAM)** | 8 GB (bendra GPU/CPU) | 16 GB+ bendra |
| **Saugykla** | 2 GB laisvos vietos | NVMe SSD su 10 GB+ laisvos vietos |
| **Python** | Python 3.7+ (skirta SDK) | Python 3.10+ |

### Įdiegimas

Atsisiųskite „JetPack 6 `.deb`“ paketą ir įdiekite:

```bash
sudo dpkg -i chloros-arm64-jp6.deb
```

Patikrinkite įdiegimą:

```bash
chloros-cli --version
```

Išsamią „Jetson“ konfigūraciją, įskaitant šilumos valdymą ir diegimą lauke, rasite [„NVIDIA Jetson“ vadove](nvidia-jetson-guide.md).

***

## Python SDK įdiegimas (Visi Linux)

Python SDK įdiegiamas atskirai per pip ir veikia tiek amd64, tiek arm64:

```bash
pip install chloros-sdk
```

Norėdami įtraukti papildomą pažangos srauto palaikymą:

```bash
pip install chloros-sdk[progress]
```

Patikrinkite SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
Paketas `.deb` įdiegia Chloros, CLI ir užkurtį. Python SDK yra atskiras pip paketas, kuris bendrauja su backend per vietinį HTTP API.
{% endhint %}

***

## Konfigūracijos katalogai

Chloros ant Linux atitinka [XDG bazinio katalogo specifikaciją](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html):

| Paskirtis | Linux Kelias | Windows Atitikmuo |
| --- | --- | --- |
| **Konfigūracija** | `~/.config/chloros/` | `%APPDATA%\Chloros\` |
| **Duomenys / Projektai** | `~/.local/share/chloros/` | `%LOCALAPPDATA%\Chloros\` |
| **Tarpinė atmintis / Autentifikavimo duomenys** | `~/.cache/chloros/` | `%APPDATA%\Chloros\cache\` |

## Vykdomųjų failų vietos

Paketas `.deb` įdiegia vykdomąjį failą į standartinę vietą. CLI ir SDK automatiškai nustato užpakalinės dalies kelią:

| Įdiegimo būdas | Užpakalinės dalies kelias |
| --- | --- |
| `.deb` paketas | `/usr/lib/chloros/chloros-backend` |
| Rankinis / pasirinktinis | `/opt/mapir/chloros/backend/chloros-backend` |

Galite pakeisti backend kelio nustatymus naudodami `--backend-exe` CLI žymę arba `backend_exe` SDK konstruktoriaus parametrą.

***

## Pirminis nustatymas

### 1. Aktyvuokite savo licenciją

Chloros+ licencija reikalinga norint naudotis CLI ir SDK:

```bash
chloros-cli login your@email.com 'your-password'
```

### 2. Patikrinkite savo licencijos būseną

```bash
chloros-cli status
```

### 3. Apdorokite savo pirmąjį duomenų rinkinį

```bash
chloros-cli process ~/datasets/flight001
```

### 4. Vykdykite sistemos diagnostiką

Patikrinkite, ar jūsų sistema yra teisingai sukonfigūruota:

```bash
chloros-cli selftest
```

Tai paleidžia 7 diagnostinius patikrinimus, įskaitant versiją, užkulisio paleidimą, API ryšį ir CUDA/GPU prieinamumą.

***

## „Bash“ skriptų pavyzdžiai

### Apdorokite kelis duomenų rinkinius

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    chloros-cli process "$dataset" --format tiff-32
    echo "Done: $(basename "$dataset")"
done
```

### Apdorokite su pasirinktiniais nustatymais

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

### Automatinis apdorojimas naudojant Cron

Pridėkite į savo crontab (`crontab -e`), kad nauji duomenų rinkiniai būtų apdorojami automatiškai:

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Python SDK pavyzdys

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "NDRE"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## Problemų sprendimas

### CLI nerastas po įdiegimo

Jei `chloros-cli` nerastas po `.deb` paketo įdiegimo:

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# If not in PATH, check the installation
dpkg -L chloros-amd64  # or chloros-arm64-jp6

# Reload your shell
source ~/.bashrc
```

### Atsisakyta suteikti leidimą

```bash
# Ensure the binary is executable
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### Nepavyko paleisti užkulisio

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

### CUDA neaptikta

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

### Trūksta bendrųjų bibliotekų

```bash
# Install common dependencies
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

***

## Atnaujinimas Chloros į Linux

Naudokite įdiegtą atnaujinimo komandą, kad patikrintumėte ir įdiegtumėte atnaujinimus:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

***

## Tolimesni veiksmai

* [„NVIDIA Jetson“ vadovas](nvidia-jetson-guide.md) — „Jetson“ specifinis optimizavimas ir diegimas
* [CLI : Komandinė eilutė](../CLI.md) — Išsamus CLI komandų žinynas
* [API : Python SDK](../api-python-sdk.md) — Išsamus SDK vadovas
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — Kaip Chloros prisitaiko prie jūsų aparatinės įrangos
