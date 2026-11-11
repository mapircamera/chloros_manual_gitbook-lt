# „NVIDIA Jetson“ vadovas

Chloros
programinė įranga, veikianti „NVIDIA Jetson“ platformoje, leidžia apdoroti daugiaspektrinius vaizdus „edge“ aplinkoje – lauke, nepilotuojamuose orlaiviuose (UAV) ir nuotolinėse įrangos vietose. Chloros
1.3.0 paleidimo metu atpažįsta jūsų „Jetson“ modelį ir optimizuoja apdorojimo strategiją pagal aptiktą aparatinę įrangą. **Rankinis nustatymas nereikalingas.**
{% hint style="info" %}
**„Jetson“ paketas 1.3.0 apdoroja duomenis „Jetson“ GPU.** „`chloros_1.3.0_arm64_jp6.deb`“ sukurtas „JetPack 6“ ir naudoja „JetPack 6“ CUDA versiją „PyTorch“, todėl apdorojimas, įskaitant „Texture Aware“ debayerį, vyksta „Orin“ GPU. Paketas pats neturi CUDA: jis naudoja CUDA ir cuDNN bibliotekas, kurias įdiegia „JetPack 6“ (`sudo apt install nvidia-jetpack` – atvaizde, kuriame jų nėra). Jei CUDA GPU negalima naudoti, apdorojimas vyksta CPU, o „Texture Aware“ naudoja standartinį „debayer“.
{% endhint %}

***

## Palaikomi „Jetson“ modeliai

| Modelis                | RAM            | Apdorojimo strategija                                     | Rekomenduojamas naudojimas                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **„Jetson AGX Orin“**  | 32–64 GB bendrai naudojama | `GPU_PARALLEL` (2 darbininkai)                              | Maksimalus našumas, dideli duomenų rinkiniai                      |
| **„Jetson Orin NX“**   | 8–16 GB bendrai naudojamos | `GPU_PARALLEL` (2 darbo procesai, 16 GB) / `GPU_SINGLE` (8 GB)   | Pagrindinė rekomendacija diegimui lauke              |
| **Jetson Orin Nano** | 8 GB bendros atminties     | `GPU_SINGLE` (1 darbinis procesas, nuoseklusis)                     | Pradinio lygio krašto skaičiavimai                                 |

{% hint style="info" %}
Linux
arm64 paketui reikalingas **„JetPack 6“**, kuris yra prieinamas „Jetson Orin“ šeimos įrenginiuose. Senesni modeliai („Nano“, „TX2“, „Xavier NX“) negali paleisti „JetPack 6“ ir nėra palaikomi dabartiniame pakete.
{% endhint %}

***

## Reikalavimai

* **„JetPack 6.x“** (rekomenduojama naujausia versija)
* **„NVIDIA CUDA“ ir „cuDNN“** iš „JetPack 6“ (`sudo apt install nvidia-jetpack`, jei jūsų atvaizde jų nėra)
* **Mokamas „Chloros
+“ planas** — „Copper“ lygis ar aukštesnis (būtinas visai prieigai prie CLI
ir SDK
; taikomas serverio pusėje)

## Įdiegimas

```bash
# Install the arm64 .deb package (JetPack 6) -- apt, not dpkg -i, so the
# Python SDK's requirements (python3-numpy, python3-requests) come too
sudo apt install ./chloros_1.3.0_arm64_jp6.deb

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.3.0"
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # the .deb installs the SDK

# Run system diagnostics
chloros-cli selftest
```

`.deb` įdiegia Python
SDK
sistemai `python3`, atitinkančią šią versiją. Tik į „virtualenv“ reikia įdiegti pridedamą „wheel“ failą: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` (visiškai neprisijungus prie interneto). Bendrą informaciją apie diegimą Linux
sistemoje, failų vietas ir trikčių šalinimą rasite [Linux
diegimo vadove](linux-installation.md).

{% hint style="info" %}
**Paleidžiant programą nebereikia išpakuoti.** Nuo 1.3.0 versijos užkulisinė dalis ir CLI
yra įdiegiamos neišpakuotos į katalogą `/usr/lib/chloros/`, todėl jos nebeišpakuojamos į laikiną katalogą kiekvieną kartą paleidžiant. Laikinieji failai vis dar automatiškai perkeliami į `/mnt/ssd/tmp`, jei tas katalogas egzistuoja; priešingu atveju galite nustatyti `TMPDIR` kaip kelią į savo NVMe (`export TMPDIR=/mnt/nvme/tmp`).
{% endhint %}

***

## Dinaminis skaičiavimo pritaikymas „Jetson“

### Kaip tai veikia

Paleidimo metu Chloros
sudaro jūsų sistemos profilį:

1. **Aptinka „Jetson“ modelį** per „`/proc/device-tree/model`“
2. **Nuskaito laisvą bendrą GPU/CPU atmintį** (Jetson naudoja suvienodintą atmintį)
3. **Paklausia PyTorch, ar gali naudoti CUDA** — jei įdiegta JetPack 6 su CUDA ir cuDNN, tai įmanoma
4. **Automatiškai pasirenka apdorojimo strategiją** (`GPU_PARALLEL`, `GPU_SINGLE` arba `CPU_PARALLEL`) ir darbininkų skaičių

Sprendimas priklauso nuo **bendros bendrai naudojamos RAM**, o ne nuo modelio pavadinimo:

* **Jei bendra RAM mažesnė nei 12 GB**(visi 8 GB „Jetson“ įrenginiai):**1 darbininkas — sąmoningas nuoseklusis apdorojimas**. Atminties nepakanka vienu metu veikiantiems darbininkams, todėl vaizdai apdorojami po vieną. „Jetson“ įrenginiuose su**8 GB ar mažiau** atminties 3-iasis sriegis visiškai praleidžia darbininkų grupę ir kiekvieno vaizdo apdorojimą atlieka pačiame procese. Užkulisinis žurnalas šią strategiją pavadina `GPU_SINGLE`.
* **12 GB ar daugiau**(Orin NX 16 GB, AGX Orin): suvienodinta atmintis atitinka `GPU_PARALLEL` reikalavimus, tačiau darbininkų skaičius**„Jetson“ įrenginiuose ribojamas iki 2** — GPU, darbininkų procesų RAM ir kiekvieno darbininko CUDA kontekstai visi naudoja tą patį bendrą rezervą, todėl didesnis darbininkų skaičius kelia atminties trūkumo gedimų riziką.
* **Nėra veikiančios CUDA** (trūksta „JetPack“ CUDA bibliotekų): `CPU_PARALLEL`, kai atminties yra 12 GB ar daugiau; žemiau pateikiamas vienas atvaizdas.

Automatinį pasirinkimą galite pakeisti naudodami aplinkos kintamąjį `CHLOROS_STRATEGY` — žr. [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### Elgsena pagal modelį

| „Jetson“ modelis                | Strategija       | Darbininkai | Vykdymas                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **„Jetson Orin Nano“ 8 GB**    | `GPU_SINGLE`   | 1       | Sekvencinė ciklo vykdymo eiga (`tiled_gpu` esant atminties trūkumui) |
| **„Jetson Orin NX 8 GB“**      | `GPU_SINGLE`   | 1       | Sekvencinė ciklo eiga procese                     |
| **„Jetson Orin NX“ 16 GB**     | `GPU_PARALLEL` | 2       | Lygiagrečiai veikiantys darbo procesai, `fused_gpu` kelias  |
| **„Jetson AGX Orin“ 32–64 GB** | `GPU_PARALLEL` | 2       | Lygiagrečiai veikiantys darbo procesai, `fused_gpu` kelias  |

Pagrindinis platformų skirtumas yra **atmintis**. 8 GB „Jetson“ didelio apkrovimo sąlygomis turi apdoroti vaizdus po vieną, naudodamas atmintį taupantį „tiled“ metodą, o 16 GB ir didesnės talpos „Orin“ gali vienu metu apdoroti 2 vaizdus per GPU, naudodamas didesnio pralaidumo sujungtąjį srautą.

### GPU ištekliai pagal modelį

Kiekvienas „Jetson“ modelis taip pat turi aparatinės įrangos profilį, kuris apriboja, kiek bendrojo rezervo gali užimti apdorojimas, ir skaluoja partijų dydžius:

| Modelis | Maksimalus GPU išteklių limitas | Partijos dydžio daugiklis | Rezervuota sistemai / ekranui |
| --- | --- | --- | --- |
| **„Jetson Orin Nano“** | 70 % | ×0,8 | 2,0 GB |
| **„Jetson Orin NX“** | 75 % | ×1,0 | 3,0 GB |
| **Jetson AGX Orin** | 80 % | ×1,5 | 4,0 GB |

Profilis koreguojamas pagal aptiktą RAM: „Jetson“ įrenginiui, pranešančiam apie **16 GB ar daugiau**, partijos daugiklis padidinamas iki ×1,2. Bazinis partijos dydis prieš taikant daugiklius yra 8 vaizdai.

Išsamią informaciją apie skaičiavimo pritaikymą rasite [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md).

***

## GPU dažnio riba „Texture Aware“ funkcijai „Nano“ ir „Orin Nano“ modeliuose

„Texture Aware“ debayeris vykdo GPU neuroninio tinklo išvadų darymą, kuris, esant maksimaliam GPU takto dažniui, gali sukelti **per didelės srovės įspėjimus**mažos galios „Jetson“ modeliuose (10–15 W klasės). Kai perduodate `--debayer texture-aware`**„Jetson Nano“ arba „Orin Nano“**, CLI
patikrina maksimalų GPU dažnį ir, jei jis šiuo metu yra didesnis, apriboja jį iki **510 MHz** (510000000):

* Jei CLI
gali įrašyti į GPU dažnio „sysfs“ mazgą, apribojimas **taikomas automatiškai** ir ekrane parodomas patvirtinimas.
* Jei ne (reikia root teisių), CLI
išveda tikslią komandą „`sudo`“, skirtą ribai taikyti rankiniu būdu, palaukia akimirką, kad galėtumėte ją perskaityti, tada tęsia veiklą — apdorojimas vis dar vyksta, tačiau gali rodyti persrovės įspėjimus.

Norėdami patys nustatyti ribą prieš apdorojimą:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

Didesnės galios modeliai („Orin NX“ 25 W, „AGX Orin“ 60 W) veikia visu GPU greičiu; riba netaikoma. Standartinis „debayer“ niekada nesukelia ribos taikymo jokiam modeliui.

{% hint style="info" %}
**„Texture Aware“ „Jetson“ platformoje visada apdoroja po vieną vaizdą.** Kiekvienam darbininkui reikėtų savo CUDA konteksto (~1 GB) ir savo triukšmo šalinimo modelio kopijos, o tai viršija suvienodintos atminties pajėgumus — todėl „Jetson“ platformoje „Texture Aware“ apdorojimo kelias priskiriamas vienam darbininkui, o prieiga prie GPU yra serijinė. Tikėtina, kad „Texture Aware“ bet kuriame „Jetson“ įrenginyje veiks žymiai lėčiau nei „Standard“. „Texture Aware“ taikoma Survey3
vaizdams: „LATTICE“ užfiksuoti vaizdai visada apdorojami naudojant „Standard“ debayerį. Jei nėra tinkamo CUDA GPU, vykdymo metu naudojamas „Standard“ režimas, o užkulisinė sistema užregistruoja vieną eilutę „`[DEBAYER] … needs a CUDA GPU …`“, paaiškinančią priežastį (parodyta su „`chloros-cli --verbose`“).
{% endhint %}

***

## Šilumos valdymas

„Jetson“ įrenginiai turi ribotą šiluminį rezervą, ypač uždarose arba ore esančiose aplinkose. Chloros
stebi SoC temperatūrą ir automatiškai riboja partijų dydžius:

| Temperatūra         | Veiksmas                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70 °C**          | Įprastas veikimas — pilnas apdorojimo greitis          |
| **70 °C** (Įspėjimas)  | Partijos dydis palaipsniui mažinamas (nuo 100 % iki 50 % esant temperatūrai nuo 70 °C iki 80 °C) |
| **80 °C** (Kritinis) | Agresyvus ribojimas (nuo 80 °C iki 90 °C – nuo 50 % iki 0 %) |
| **90 °C** (Išjungimas) | Visiškai sustabdyti GPU apdorojimą – reikia atvėsinti |

{% hint style="warning" %}
**Užtikrinkite tinkamą ventiliaciją ir šilumos šalinimą**, kad apdorojimas vyktų nepertraukiamai, ypač uždarose lauko korpusuose arba orlaivių sistemose. Dėl šiluminio apribojimo apdorojimo našumas sumažės, siekiant apsaugoti aparatinę įrangą.
{% endhint %}

***

## Atminties valdymas

„Jetson“ įrenginiai naudoja **vieningą atmintį** — GPU ir CPU dalijasi ta pačia fizine RAM. Nurodyta VRAM (pvz., ~15,3 GB „Orin NX 16GB“ modelyje) nėra skirta tik GPU; tai ta pati RAM, kurią naudoja operacinė sistema ir visi kiti procesai.

### Įspėjimas dėl keitimosi atminties ir rekomendacijos

Prieš pradedant apdorojimą „Jetson“ įrenginyje, CLI
suskaičiuoja RAW vaizdus jūsų įvesties aplanke (`.tif`, `.tiff`, `.raw`, `.dng` — JPG peržiūros neįskaičiuojamos), įvertina didžiausią procesui reikalingą atminties kiekį ir **įspėja prieš pradedant**, jei RAM + keitimo sritis greičiausiai bus nepakankama. Įspėjimo antraštė yra „`LOW MEMORY WARNING - Jetson Detected`“, jame nurodomas vaizdų skaičius, RAM, dabartinį keitimosi atminties kiekį ir numatomą didžiausią kiekį, tada pateikia tikslias komandas „`fallocate`“ / „`chmod`“ / „`mkswap`“ / „`swapon`“, pritaikytas jūsų projektui jūsų projektui (niekada ne mažesnės nei 8 GB). Ji sustabdo veikimą kelioms sekundėms, kad pranešimas nepasimestų slinkties istorijoje, tada apdorojimas tęsiamas.**Įspėjime naudojami atminties įvertinimai:**

| „Debayer“ režimas | Bazinis | Vienam vaizdui |
| --- | --- | --- |
| Standartinis | ~1,5 GB | ~10 MB |
| Tekstūrų atpažinimas | ~2,5 GB (modelis + „Python“ vykdymo aplinka) | ~15 MB |

Įspėjimas pasirodo, kai numatoma didžiausia reikšmė viršija RAM + keitimosi atmintį atėmus 1 GB saugumo atsargą, ir skaičiuojama tik **failais pagrįsta** keitimosi atmintis — konfigūracija, kurioje naudojamas tik „zram“, vis tiek bus pažymėta.

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

Apdorojimo metu Chloros
stebi GPU atmintį ir, užuot sugriuvęs, sklandžiai sumažina našumą:

1. Kai GPU atminties panaudojimas viršija **85 %**, partijų dydžiai prevenciškai sumažinami
2. Jei vis tiek įvyksta atminties trūkumo atvejis, partijos dydis **sumažinamas perpus**, o kiekvieno paskesnio OOM atvejo metu – dar perpus; kiekviena sėkmingai apdorota partija šią baudą sumažina vienu žingsniu
3. Esant nuolatiniam apkrovos spaudimui, apdorojimo grandinė pereina iš `fused_gpu` į atminties tausojantį `tiled_gpu` maršrutą, o kraštutiniu atveju – prie CPU apdorojimo

Kadangi GPU dalijasi „Jetson“ RAM atmintimi, RAM ir keitimo sritis vis dar riboja veikimą — aukščiau pateiktas įspėjimas apie keitimo sritį yra patikrinimas, į kurį reikia atkreipti dėmesį.

***

## Įdiegimas lauke

### Maitinimo aspektai

| „Jetson“ modelis     | Tipinis energijos suvartojimas | Pastabos                   |
| ---------------- | ------------------ | ----------------------- |
| „Jetson Orin Nano“ | 7–15 W              | DC cilindrinis lizdas          |
| „Jetson Orin NX“   | 10–25 W             | DC cilindrinis lizdas          |
| „Jetson AGX Orin“  | 15–60 W             | USB-C PD arba cilindrinis lizdas |

Suplanuokite energijos sąnaudas, kad užtikrintumėte nepertraukiamą apdorojimą — didžiausia energijos sąnauda pasiekiama GPU intensyviame 3-iajame procese (apdorojimas).

### Rekomendacijos dėl saugyklos

* **NVMe SSD** labai rekomenduojamas „arm64“ diegimams
* SD kortelės yra per lėtos apdorojimui — naudokite jas tik kaip paleidimo laikmeną
* Apskaičiuokite, kad apdorotų duomenų apimtis bus 2–3 kartus didesnė už neapdorotų vaizdo duomenų apimtį

### Darbas be monitoriaus per SSH


Chloros
CLI
yra idealus sprendimas „Jetson“ diegimui be monitoriaus:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### Nuolat veikiantis užpakalinis serveris „LATTICE“ / „DAQ-E“ laiko sinchronizavimui

Jei jūsų „Jetson“ be monitoriaus valdo „LATTICE“ kameras arba „DAQ-E“ šviesos jutiklius, įjunkite „systemd“ paslaugą **savo vartotojo vardu**, kad PTP „grandmaster“ veiktų nepertraukiamai, o SDK
skriptai rastų veikiančią paslaugą (modulis yra įdiegtas, bet pagal numatytuosius nustatymus neįjungtas):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
chloros-cli time-sync status
```

Tuomet foninė sistema veiks kaip jūsų vartotojas: jos įrašomi duomenys priklausys jums, o ji naudos jūsų `chloros-cli login`. Išsamią informaciją, įskaitant „root“ `chloros-backend.service` ir tai, kaip paketas leidžia priskirti PTP prievadus 319/320 be „root“ teisių, rasite [Linux
diegimo](linux-installation.md#always-on-ptp-for-headless-hosts) skyriuje.

### Automatinis apdorojimas naudojant „systemd“

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

„`chloros-cli process`“ baigia veikimą su nelygiu nuliui rezultatu, kai užklausą pateikęs procesas neįrašo jokių vaizdų, todėl „systemd“ nesėkmės būsena yra reikšminga stebėjimui.

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
    --indices NDVI GNDVI OSAVI
```

### Python
SDK
„Jetson“ platformoje

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (32-bit, Percent)",
        reflectance_calibration=True
    )
    chloros.process(mode="parallel")

print("Processing complete!")
```

### Daugelio skrydžių apdorojimas partijomis

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format "TIFF (32-bit, Percent)" \
        --indices NDVI OSAVI
    echo "Completed $name"
done
```

***

## Rekomenduojamos „Jetson“ sistemos naudojimui lauke

Naudojimui lauke ir ore apsvarstykite šias „Jetson Orin NX 16GB“ nešiklio plokščių variantus:

* **Ore/dronuose**: sistemos, atitinkančios vibracijos standartą (MIL-STD), lengvos (mažiau nei 300 g), su pasyviu aušinimu
* **Atšiauriomis lauko sąlygomis**: IP67/IP69K atsparūs vandeniui korpusai su PoE GigE kameros jungtimi
* **Minimalūs/ekonominiai**: Kūrėjų rinkiniai su papildomais korpusais

Susisiekite su [MAPIR
palaikymo tarnyba](https://www.mapir.camera/community/contact), kad gautumėte konkrečius aparatinės įrangos rekomendavimus, pritaikytus jūsų diegimo scenarijui.

***

## Tolimesni veiksmai

* [Linux
diegimas](linux-installation.md) — Bendroji informacija apie Linux
diegimą
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — Išsamus skaičiavimo strategijos vadovas
* [Apdorojimo srautas](../processing-architecture/processing-pipeline.md) — 4 sriegių srauto supratimas
* [CLI: Komandinė eilutė](../CLI.md) — CLI
vadovas
* [API
: Python
SDK
](../api-python-sdk.md) — SDK
vadovas
* [CLI
vadovas](../reference/cli-reference.md) ir [SDK
vadovas](../reference/sdk-reference.md) — Išsamūs komandų ir API
sąrašai versijai 1.3.0
