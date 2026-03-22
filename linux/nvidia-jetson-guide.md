# „NVIDIA Jetson“ vadovas

„Chloros“ ant „NVIDIA Jetson“ platformos leidžia atlikti daugiaspektrinių vaizdų apdorojimą periferijoje – lauke, ant nepilotuojamų orlaivių (UAV) ir nuotolinėse įrangos vietose. „Chloros“ automatiškai atpažįsta jūsų „Jetson“ modelį ir optimizuoja apdorojimo strategiją pagal jūsų aparatinę įrangą.

***

## Palaikomi „Jetson“ modeliai

| Modelis                | RAM            | Apdorojimo strategija                                   | Rekomenduojamas naudojimas                                          |
| -------------------- | -------------- | ----------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32–64 GB bendrai naudojama | `GPU_PARALLEL` (4 darbininkai)                            | Maksimalus našumas, dideli duomenų rinkiniai                      |
| **Jetson Orin NX**   | 8–16 GB bendrai naudojama  | `GPU_PARALLEL` (3 darbininkai, 16 GB) / `GPU_SINGLE` (8 GB) | Pagrindinė rekomendacija diegimui ore ir lauke |
| **Jetson Orin Nano** | 8 GB bendros atminties     | `GPU_SINGLE` (1 darbininkas)                               | Pradinio lygio krašto skaičiavimai                                 |
| **Jetson Nano**      | 4–8 GB bendrai naudojama   | `GPU_SINGLE` (1 darbinis procesorius)                               | Pradinio lygio, ribotos atminties                          |

{% hint style="info" %}
**Senesni „Jetson“ modeliai** (TX2, TX1, Xavier NX) gali būti nepalaikomi. Našumas priklausys nuo turimos GPU atminties ir CUDA galimybių.
{% endhint %}

***

## Reikalavimai

* **„JetPack 6.x“** (rekomenduojama naujausia versija)
* **NVIDIA CUDA** (įtraukta į JetPack)
* **Chloros+ licencija** (reikalinga norint naudotis CLI/SDK)

## Įdiegimas

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros-arm64-jp6.deb

# Verify installation
chloros-cli --version

# Install Python SDK (optional)
pip install chloros-sdk

# Run system diagnostics
chloros-cli selftest
```

Bendrą informaciją apie Linux diegimą rasite [Linux diegimo](linux-installation.md) skyriuje.

***

## Dinaminis skaičiavimo pritaikymas „Jetson“

Chloros automatiškai aptinka jūsų „Jetson“ modelį ir pasirenka optimalų apdorojimo būdą. **Rankinis nustatymas nereikalingas.**

### Kaip tai veikia

Paleidimo metu Chloros sukuria jūsų sistemos profilį:

1. **Aptinka „Jetson“ modelį** per `/proc/device-tree/model`
2. **Skaito laisvą GPU/bendrąją atmintį**

3.**Pasirenka apdorojimo strategiją** (`GPU_PARALLEL`, `GPU_SINGLE` arba `CPU_PARALLEL`)
4. **Automatiškai nustato darbininkų skaičių, konvejerio tipą ir atminties paskirstymą**

### Elgsena pagal modelį

| „Jetson“ modelis                | Strategija       | Darbininkai | Konvejeris                       | Lygiagretumas |
| --------------------------- | -------------- | ------- | ------------------------------ | ----------- |
| **Jetson Nano 8GB**         | `GPU_SINGLE`   | 1       | `tiled_gpu` (taupus atminties atžvilgiu) | Serijinis  |
| **Jetson Orin Nano 8GB**    | `GPU_SINGLE`   | 1       | `tiled_gpu`                    | Serijinis  |
| **Jetson Orin NX 8 GB**      | `GPU_SINGLE`   | 2       | `tiled_gpu`                    | Serijinis  |
| **Jetson Orin NX 16 GB**     | `GPU_PARALLEL` | 3       | `fused_gpu` (visas GPU kelias)    | Lygiagretus  |
| **Jetson AGX Orin 32–64 GB** | `GPU_PARALLEL` | 4       | `fused_gpu`                    | Lygiagretus  |

{% hint style="success" %}
**Jetson Orin NX 16 GB** yra optimalus pasirinkimas diegimui periferijoje — jam taikoma `GPU_PARALLEL` strategija su 3 lygiagrečiais darbuotojais, užtikrinanti tikrą lygiagretų GPU apdorojimą kompaktiškame korpuse.
{% endhint %}

Pagrindinis platformų skirtumas yra **atmintis**. „Jetson Nano“ su 8 GB bendros atminties turi apdoroti vaizdus po vieną, naudodamas atminties tausojantį plytelių metodą, o „Orin NX“ su 16 GB gali vienu metu apdoroti 3 vaizdus per GPU, naudodamas didesnio pralaidumo sujungtą srautą.

Išsamią skaičiavimo pritaikymo informaciją rasite [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md).

***

## Šilumos valdymas

„Jetson“ įrenginiai turi ribotą šilumos rezervą, ypač uždarose arba ore esančiose aplinkose. „Chloros“ įtraukia automatinį šilumos stebėjimą ir ribojimą:

| Temperatūra         | Veiksmas                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70 °C**          | Įprastas veikimas — pilnas apdorojimo greitis          |
| **70 °C** (Įspėjimas)  | Automatiškai sumažinti partijos dydį                   |
| **80 °C** (Kritinis) | Agresyvus greičio ribojimas — mažesnis lygiagretumas         |
| **90°C** (Išjungimas) | Visiškai sustabdyti GPU apdorojimą — reikia atvėsinti |

{% hint style="warning" %}
**Užtikrinkite tinkamą ventiliaciją ir šilumos šalinimą**, kad apdorojimas būtų nepertraukiamas, ypač uždarose lauko korpusuose ar orlaivių sistemose. Terminis greičio ribojimas sumažins apdorojimo našumą, siekiant apsaugoti aparatūrą.
{% endhint %}

***

## Atminties valdymas

„Jetson“ įrenginiai naudoja **vieningą atmintį** — GPU ir CPU dalijasi ta pačia fizine RAM. Tai reiškia, kad nurodyta VRAM (pvz., 15,3 GB „Orin NX 16GB“) nėra skirta tik GPU; ji dalijamasi su operacine sistema ir kitais procesais.

### Rekomendacijos dėl keitimosi atminties

Didelėms duomenų bazėms arba „Texture Aware“ debayer apdorojimui Chloros gali rekomenduoti sukurti keitimosi atminties erdvę:

```bash
# Check current memory and swap
free -h

# Create a swap file (example: 8GB)
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**Atminties sąnaudos vienam vaizdui:**

* Standartinis debayer: ~10 MB vienam vaizdui
* Tekstūrą atpažįstantis debayer: ~15 MB vienam vaizdui

Chloros automatiškai apskaičiuoja reikalingą atmintį pagal jūsų duomenų rinkinio dydį ir įspėja, jei rekomenduojama naudoti keitimo erdvę.

### OOM (Out of Memory) atsarginis variantas

Jei apdorojimo metu aptinkama atminties trūkumo sąlyga:

1. Chloros automatiškai sumažina GPU darbininkų skaičių
2. Pereina iš `fused_gpu` į `tiled_gpu` procesų grandinę (taupesnė atminties atžvilgiu)
3. Tęsiama apdorojimo operacija sumažintu našumu, o ne nutraukiant veiklą

***

## Įdiegimas lauke

### Maitinimo aspektai

| „Jetson“ modelis     | Tipinis energijos suvartojimas | Pastabos                   |
| ---------------- | ------------------ | ----------------------- |
| „Jetson Nano“      | 5–10 W              | USB-C arba cilindrinis lizdas    |
| „Jetson Orin Nano“ | 7–15 W              | DC cilindrinis lizdas          |
| „Jetson Orin NX“   | 10–25 W             | DC cilindrinis lizdas          |
| „Jetson AGX Orin“  | 15–60 W             | USB-C PD arba cilindrinis lizdas |

Suplanuokite energijos sąnaudas ilgalaikiam apdorojimui — didžiausias energijos suvartojimas pasiekiamas GPU intensyviame 3-iajame procese (apdorojimas).

### Rekomendacijos dėl saugojimo

* **NVMe SSD** labai rekomenduojamas arm64 diegimams
* SD kortelės yra per lėtos apdorojimui — naudokite jas tik kaip paleidimo laikmeną
* Planuokite 2–3 kartus didesnį nei neapdorotų vaizdo duomenų dydį apdorotam rezultatui

### Veikimas be monitoriaus per SSH

Chloros CLI idealiai tinka „Jetson“ diegimams be monitoriaus:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format tiff-32

# Monitor export progress
chloros-cli export-status
```

### Automatinis apdorojimas su „systemd“

Sukurkite „systemd“ paslaugą automatiniam apdorojimui:

```ini
# /etc/systemd/system/chloros-process.service
[Unit]
Description=Chloros Automated Processing
After=network.target

[Service]
Type=oneshot
User=chloros
ExecStart=/usr/bin/chloros-cli process /data/incoming --output /data/processed
StandardOutput=append:/var/log/chloros-process.log
StandardError=append:/var/log/chloros-process.log

[Install]
WantedBy=multi-user.target
```

Suderinkite su „systemd“ laikmačiu, kad apdorojimas vyktų pagal tvarkaraštį:

```ini
# /etc/systemd/system/chloros-process.timer
[Unit]
Description=Run Chloros Processing Every Hour

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable chloros-process.timer
sudo systemctl start chloros-process.timer
```

***

## Darbo eigos pavyzdžiai

### Pagrindinis „Jetson“ apdorojimas

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI
```

### Python SDK „Jetson“

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (32-bit, Percent)",
        reflectance_calibration=True
    )
    chloros.process(mode="parallel")

print("Processing complete!")
```

### Daugelio skrydžių paketinis apdorojimas

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format tiff-32 \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Rekomenduojamos „Jetson“ sistemos naudojimui lauke

Lauko ir oro sąlygoms pritaikytoms sistemoms rekomenduojame šias „Jetson Orin NX 16GB“ nešiklio plokščių versijas:

* **Oro transportas/dronai**: sistemos, atitinkančios vibracijos standartą (MIL-STD), lengvos (mažiau nei 300 g), su pasyviąja aušinimo sistema
* **Sunkios lauko sąlygos**: IP67/IP69K atsparūs vandeniui korpusai su PoE GigE kameros jungtimi
* **Minimalus/ekonomiškas**: kūrėjų rinkiniai su papildomais korpusais

Susisiekite su [MAPIR palaikymo tarnyba](https://www.mapir.camera/community/contact), kad gautumėte konkrečius aparatinės įrangos rekomendavimus jūsų diegimo scenarijui.

***

## Tolimesni veiksmai

* [Linux diegimas](linux-installation.md) — Bendrieji Linux diegimo duomenys
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — Išsamus skaičiavimo strategijos vadovas
* [Apdorojimo srautas](../processing-architecture/processing-pipeline.md) — 4 sriegių srauto supratimas
* [CLI : Komandinė eilutė](../CLI.md) — Išsamus CLI vadovas
* [API : Python SDK](../api-python-sdk.md) — Išsamus SDK vadovas
