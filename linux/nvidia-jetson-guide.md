# „NVIDIA Jetson“ vadovas „

Chloros

“ „NVIDIA Jetson“ platformoje leidžia atlikti multispektrinių vaizdų apdorojimą periferijoje – lauke, nepilotuojamuose orlaiviuose (UAV) ir nuotolinėse įrangos vietose. „Chloros

“ 1.2.0 paleidimo metu nustato jūsų „Jetson“ modelį ir optimizuoja apdorojimo strategiją pagal aptiktą aparatinę įrangą. **Rankinis nustatymas nereikalingas.**

***

## Palaikomi „Jetson“ modeliai

| Modelis                | RAM            | Apdorojimo strategija                                     | Rekomenduojamas naudojimas                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **„Jetson AGX Orin“**  | 32–64 GB bendrai naudojama | `GPU_PARALLEL` (2 darbininkai)                              | Maksimalus našumas, dideli duomenų rinkiniai                      |
| **„Jetson Orin NX“**   | 8–16 GB bendrai naudojama | `GPU_PARALLEL` (2 darbo procesai, 16 GB) / `GPU_SINGLE` (8 GB) | Pagrindinė rekomendacija diegimui orlaiviuose ir lauke |
| **„Jetson Orin Nano“** | 8 GB bendros atminties     | `GPU_SINGLE` (1 darbinis procesorius, nuoseklusis)                     | Pradinio lygio krašto skaičiavimai                                 |

{% hint style="info" %}
„Linux

“ arm64 paketui reikalingas **„JetPack 6“**, kuris yra prieinamas „Jetson Orin“ šeimos įrenginiuose. Senesni modeliai („Nano“, „TX2“, „Xavier NX“) negali paleisti „JetPack 6“ ir nėra palaikomi dabartiniame pakete.
{% endhint %}

***

## Reikalavimai

* **„JetPack 6.x“** (rekomenduojama naujausia versija)
* **„NVIDIA CUDA“** (įtraukta į „JetPack“)
* **Mokamas „Chloros

+“ planas** — „Copper“ lygis ar aukštesnis (būtinas norint naudotis visais „CLI

“ / „SDK

“ ištekliais; taikoma serverio pusėje)

## Įdiegimas

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.2.0"

# Install Python SDK (optional) — the bundled wheel always matches this build
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl

# Run system diagnostics
chloros-cli selftest
```

Bendrą informaciją apie „Linux

“ įdiegimą, failų vietas ir trikčių šalinimą rasite [Linux

Įdiegimas](linux-installation.md).

{% hint style="info" %}
**Išpakuokite failus į greitąją atmintį.** Kiekvieną kartą paleidžiant programą, kompiliuoti dvejetainiai failai išsipakuojami į laikiną katalogą — iš SD kortelės tai vyksta labai lėtai. „Chloros

“ automatiškai naudoja `/mnt/ssd/tmp`, jei toks katalogas egzistuoja; priešingu atveju nustatykite `TMPDIR` į savo NVMe (`export TMPDIR=/mnt/nvme/tmp`).
{% endhint %}

***

## Dinaminis skaičiavimo pritaikymas „Jetson“

### Kaip tai veikia

Paleidimo metu „Chloros

“ sudaro jūsų sistemos profilį:

1. **Nustato „Jetson“ modelį** per `/proc/device-tree/model`
2. **Nuskaito laisvą bendrą GPU/CPU atmintį** („Jetson“ naudoja suvienodintą atmintį)
3. **Pasirenka apdorojimo strategiją** (`GPU_PARALLEL`, `GPU_SINGLE` arba `CPU_PARALLEL`)
4. **Automatiškai nustato darbininkų skaičių, konvejerio tipą ir atminties paskirstymą**Sprendimas priklauso nuo**bendros bendrai naudojamos RAM**, o ne nuo modelio pavadinimo:

* **Jei bendra RAM mažesnė nei 12 GB**(visi 8 GB „Jetson“ įrenginiai): `GPU_SINGLE` su**1 darbininku — sąmoningas nuoseklusis apdorojimas**. Atminties nepakanka vienu metu veikiantiems darbininkams, todėl vaizdai apdorojami po vieną. „Jetson“ įrenginiuose su**8 GB ar mažiau** atminties 3-iasis sriegis visiškai praleidžia darbininkų grupę ir kiekvieno vaizdo apdorojimą atlieka pačiame procese.
* **12 GB ar daugiau**(Orin NX 16 GB, AGX Orin): vieninga atmintis atitinka `GPU_PARALLEL` reikalavimus, tačiau darbininkų skaičius**„Jetson“ įrenginiuose ribojamas iki 2** — GPU, darbininkų procesų RAM ir kiekvieno darbininko CUDA kontekstai visi naudoja tą patį bendrą rezervą, todėl didesnis darbininkų skaičius kelia atminties trūkumo klaidų riziką.

Automatinį pasirinkimą galite pakeisti naudodami aplinkos kintamąjį `CHLOROS_STRATEGY` — žr. [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### Elgsena pagal modelį

| „Jetson“ modelis                | Strategija       | Darbininkai | Vykdymas                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **„Jetson Orin Nano 8 GB“**    | `GPU_SINGLE`   | 1       | Sekvencinė ciklo vykdymo eiga (`tiled_gpu` esant atminties trūkumui) |
| **„Jetson Orin NX“ 8 GB**      | `GPU_SINGLE`   | 1       | Sekvencinė ciklo vykdymo viduje eiga                     |
| **„Jetson Orin NX“ 16 GB**     | `GPU_PARALLEL` | 2       | Lygiagrečiai veikiantys darbo procesai, `fused_gpu` kelias  |
| **„Jetson AGX Orin“ 32–64 GB** | `GPU_PARALLEL` | 2       | Lygiagrečiai veikiantys darbo procesai, `fused_gpu` kelias  |

Pagrindinis platformų skirtumas yra **atmintis**. 8 GB „Jetson“ didelės apkrovos sąlygomis turi apdoroti vaizdus po vieną, naudodamas atmintį taupantį „tiled“ metodą, o 16 GB ir didesnės talpos „Orin“ gali vienu metu apdoroti 2 vaizdus per GPU, naudodamas didesnio pralaidumo sujungtą srautą.

### GPU ištekliai kiekvienam modeliui

Kiekvienas „Jetson“ modelis taip pat turi aparatinės įrangos profilį, kuris apriboja, kiek bendrojo rezervo gali užimti apdorojimas, ir skaluoja partijų dydžius:

| Modelis | Maksimalus GPU išteklių kiekis | Partijos dydžio daugiklis | Rezervuota sistemai / ekranui |
| --- | --- | --- | --- |
| **„Jetson Orin Nano“** | 70 % | ×0,8 | 2,0 GB |
| **„Jetson Orin NX“** | 75 % | ×1,0 | 3,0 GB |
| **„Jetson AGX Orin“** | 80 % | ×1,5 | 4,0 GB |

Profilis koreguojamas pagal aptiktą RAM: „Jetson“ įrenginiui, kurio RAM yra **16 GB ar daugiau**, partijos daugiklis padidinamas iki ×1,2. Bazinis partijos dydis prieš daugiklių taikymą yra 8 vaizdai.

Išsamią informaciją apie skaičiavimo pritaikymą rasite [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md).

***

## GPU dažnio riba „Texture Aware“ funkcijai „Nano“ ir „Orin Nano“ modeliuose

„Texture Aware“ debayeris vykdo GPU neuroninio tinklo išvadų darymą, kuris, esant maksimaliam GPU taktiniam dažniui, gali sukelti **per didelės srovės įspėjimus**mažos galios „Jetson“ modeliuose (10–15 W klasės). Prieš pradedant „Texture Aware“ apdorojimą**„Jetson Nano“ arba „Orin Nano“**, „Chloros

“ patikrina maksimalų GPU dažnį ir, jei jis šiuo metu yra didesnis, apriboja jį iki **510 MHz** (510000000):

* Jei „CLI

“ gali įrašyti į GPU dažnio „sysfs“ mazgą, riba **taikoma automatiškai** ir išvedamas patvirtinimas.
* Jei ne (reikia „root“ teisių), „CLI

“ išveda tikslią komandą „`sudo`“, skirtą ribai taikyti rankiniu būdu, palaukia akimirką, kad galėtumėte ją perskaityti, tada tęsia veiklą — apdorojimas vis dar vyksta, tačiau gali būti rodomi persrovės įspėjimai.

Norėdami patys nustatyti ribą prieš apdorojimą:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

Didesnės galios modeliai („Orin NX“ 25 W, „AGX Orin“ 60 W) veikia visu GPU greičiu; riba netaikoma. Standartinis „debayer“ niekada nesukelia ribos taikymo jokiam modeliui.

{% hint style="info" %}
**„Texture Aware“ „Jetson“ platformoje visada apdoroja po vieną vaizdą.** Kiekvienam darbininkui reikėtų savo CUDA konteksto (~1 GB) ir savo triukšmo šalinimo modelio kopijos, o tai viršija suvienodintos atminties pajėgumus — todėl „Jetson“ įrenginiuose „Texture Aware“ procesas priskiriamas vienam darbininkui, o prieiga prie GPU yra serijinė. Tikėtina, kad „Texture Aware“ bet kuriame „Jetson“ įrenginyje veiks žymiai lėčiau nei „Standard“.
{% endhint %}

***

## Šilumos valdymas

„Jetson“ įrenginiai turi ribotą šiluminį rezervą, ypač uždarose arba ore esančiose diegimo vietose. „Chloros

“ stebi SoC temperatūrą ir automatiškai riboja partijų dydžius:

| Temperatūra         | Veiksmas                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70 °C**          | Įprastas veikimas — pilnas apdorojimo greitis          |
| **70 °C** (Įspėjimas)  | Partijos dydis palaipsniui mažinamas (nuo 100 % iki 50 % esant temperatūrai nuo 70 °C iki 80 °C) |
| **80 °C** (Kritinis) | Agresyvus ribojimas (nuo 80 °C iki 90 °C – nuo 50 % iki 0 %) |
| **90 °C** (Išjungimas) | Visiškai sustabdyti GPU apdorojimą — būtinas aušinimas |

{% hint style="warning" %}
**Užtikrinkite tinkamą vėdinimą ir šilumos šalinimą**, kad apdorojimas vyktų nepertraukiamai, ypač uždarose lauko korpusuose arba orlaivių sistemose. Dėl šiluminio apribojimo apdorojimo našumas sumažės, siekiant apsaugoti aparatinę įrangą.
{% endhint %}

***

## Atminties valdymas

„Jetson“ įrenginiai naudoja **vieningą atmintį** — GPU ir CPU dalijasi ta pačia fizine RAM. Nurodyta VRAM (pvz., ~15,3 GB „Orin NX 16GB“ modelyje) nėra skirta tik GPU; tai ta pati RAM, kurią naudoja operacinė sistema ir visi kiti procesai.

### Įspėjimas dėl keitimo į keitimo sritį ir rekomendacijos

Prieš pradedant apdorojimą „Jetson“ įrenginyje, „CLI

“ suskaičiuoja RAW vaizdus jūsų įvesties aplanke (`.tif`, `.tiff`, `.raw`, `.dng` — JPG peržiūros nuotraukos neskaičiuojamos), įvertina didžiausią procesui reikalingą atminties kiekį ir **įspėja prieš pradedant**, jei RAM + keitimo sritis greičiausiai bus nepakankama. Įspėjimas pavadintas „`LOW MEMORY WARNING - Jetson Detected`“, jame pateikiamas jūsų vaizdų skaičius, RAM, dabartinį keitimosi atminties kiekį ir numatomą didžiausią kiekį, tada pateikia tikslias komandas „`fallocate`“ / „`chmod`“ / `mkswap` / `swapon` komandas, pritaikytas jūsų projektui (niekada ne mažesnės nei 8 GB). Programa sustos kelioms sekundėms, kad pranešimas nepasimestų slinkties juostoje, tada apdorojimas bus tęsiamas.**Įspėjime naudojami atminties apskaičiavimai:**

| „Debayer“ režimas | Bazinis | Vienam vaizdui |
| --- | --- | --- |
| Standartinis | ~1,5 GB | ~10 MB |
| Atsižvelgiantis į tekstūrą | ~2,5 GB (modelis + „Python

“ vykdymo laikas) | ~15 MB |

Įspėjimas suveikia, kai numatoma didžiausia reikšmė viršija RAM + keitimosi atmintį, atėmus 1 GB saugos atsargą, ir skaičiuojama tik **failais pagrįsta** keitimosi atmintis — konfigūracija, kurioje naudojamas tik „zram“, vis tiek bus pažymėta.

Norėdami rankiniu būdu pridėti keitimo erdvę (pavyzdys: 8 GB):

```bash
# Check current memory and swap
free -h

# Create a swap file
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```



<!-- SCREENSHOT-NEEDED: Terminal on a Jetson Orin (SSH session) showing the full "LOW MEMORY WARNING - Jetson Detected" block printed by `chloros-cli process` on a large folder: the image count and debayer mode line, RAM / current swap / estimated peak figures, and the fallocate/chmod/mkswap/swapon command block it recommends -->

### OOM (Out of Memory) tvarkymas

Apdorojimo metu „Chloros

“ stebi GPU atmintį ir, vietoj to, kad užstrigtų, sklandžiai sumažina našumą:

1. Kai GPU atminties panaudojimas viršija **85 %**, partijų dydžiai prevenciškai sumažinami
2. Jei vis tiek įvyksta atminties trūkumo atvejis, partijos dydis **sumažinamas perpus**, o kiekvieno paskesnio OOM atvejo metu – dar perpus; kiekviena sėkmingai apdorota partija šią baudą sumažina vienu žingsniu
3. Esant nuolatiniam apkrovimui, apdorojimo grandinė pereina iš `fused_gpu` į atminties tausojantį `tiled_gpu` kelią, o kraštutiniu atveju – prie apdorojimo CPU

***

## Įdiegimas praktikoje

### Energijos suvartojimo aspektai

| „Jetson“ modelis     | Tipinis energijos suvartojimas | Pastabos                   |
| ---------------- | ------------------ | ----------------------- |
| „Jetson Orin Nano“ | 7–15 W              | DC cilindrinė jungtis          |
| „Jetson Orin NX“   | 10–25 W             | DC cilindrinė jungtis          |
| „Jetson AGX Orin“  | 15–60 W             | USB-C PD arba cilindrinė jungtis |

Suplanuokite maitinimo išteklius, kad užtikrintumėte nepertraukiamą apdorojimą — didžiausia suvartojama galia pasiekiama GPU intensyviame 3-iajame procese („Processing“).

### Rekomendacijos dėl saugojimo

* **NVMe SSD** labai rekomenduojamas „arm64“ diegimams
* SD kortelės yra pernelyg lėtos apdorojimui — naudokite jas tik kaip įkrovos laikmeną
* Apskaičiuokite, kad apdorotų duomenų apimtis bus 2–3 kartus didesnė už neapdorotų vaizdo duomenų dydį

### Darbas be monitoriaus per „SSH

“ „

Chloros

“ „CLI

“ yra idealus sprendimas „Jetson“ diegimui be monitoriaus:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### Nuolat veikianti „backend“ sistema „LATTICE“ / „DAQ-E“ laiko sinchronizavimui

Jei jūsų „Jetson“ be monitoriaus valdo „LATTICE“ kameras arba „DAQ-E“ šviesos jutiklius, įjunkite „systemd“ paslaugą, kad PTP „grandmaster“ veiktų nepertraukiamai (modulis yra įdiegtas, bet pagal numatytuosius nustatymus neįjungtas):

```bash
sudo systemctl enable --now chloros-backend.service
chloros-cli time-sync status
```

Išsamią informaciją, įskaitant tai, kaip paketas leidžia priskirti PTP prievadus 319/320 be „root“ teisių, rasite [Linux

diegimo instrukcijoje](linux-installation.md#always-on-ptp-for-headless-hosts).

### Automatizuotas apdorojimas naudojant „systemd“

Sukurkite „systemd“ paslaugą automatizuotam apdorojimui:

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

`chloros-cli process` grąžina nelygų nuliui rezultatą, kai vykdymo sesija, kurioje buvo užsakyti produktai, nerašo jokių vaizdų, todėl „systemd“ nesėkmės būsena yra reikšminga stebėjimui.

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

## Pavyzdiniai darbo srautai

### Pagrindinis „Jetson“ apdorojimas

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI
```

### „Python

“ ir „SDK

“ naudojimas „Jetson“ sistemoje

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

### Kelių skrydžių apdorojimas partijomis

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format "TIFF (32-bit, Percent)" \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Rekomenduojamos „Jetson“ sistemos naudojimui lauke

Naudojimui lauke ir ore apsvarstykite šias „Jetson Orin NX 16GB“ nešiojamosios plokštės galimybes:

* **Ore/dronuose**: sistemos, atitinkančios vibracijos reikalavimus (MIL-STD), lengvos (mažiau nei 300 g), su pasyviu aušinimu
* **Sunkioji lauko aplinka**: IP67/IP69K atsparūs vandeniui korpusai su PoE GigE kameros jungtimi
* **Minimalus/ekonominis variantas**: Kūrėjų rinkiniai su papildomais korpusais

Susisiekite su [„MAPIR

“ palaikymo tarnyba](https://www.mapir.camera/community/contact), kad gautumėte konkrečias aparatinės įrangos rekomendacijas, pritaikytas jūsų diegimo scenarijui.

***

## Tolimesni veiksmai

* [„Linux

“ įdiegimas](linux-installation.md) — Bendroji informacija apie „Linux

“ įdiegimą
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — Išsamus skaičiavimo strategijų vadovas
* [Apdorojimo srautas](../processing-architecture/processing-pipeline.md) — 4 sriegių srauto apžvalga
* [„CLI

“: komandinė eilutė](../CLI.md) — „CLI

“ vadovas
* [„API

“: „Python

“ ir „SDK

“](../api-python-sdk.md) — „SDK

“ vadovas
* [„CLI

“ žinynas](../reference/cli-reference.md) ir [„SDK

“ žinynas](../reference/sdk-reference.md) — išsamūs komandų ir „API

“ sąrašai versijai 1.2.0
