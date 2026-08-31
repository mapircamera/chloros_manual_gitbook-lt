# Linux įdiegimas

Chloros platinamas kaip Linux, kaip `.deb` paketai, kurie įdiegia CLI ir užkulisinį serverį. Python SDK yra atskiras „pip“ paketas (taip pat įtrauktas į `.deb` kaip versijai atitinkantis „wheel“).

Paketo failų pavadinimuose nurodyta versija ir architektūra: „`chloros_1.2.0_amd64.deb`“ – x86_64, o „`chloros_1.2.0_arm64_jp6.deb`“ – „JetPack 6 Jetson“ versijoms. Toliau pateiktose komandose įrašykite failą, kurį iš tikrųjų atsisiuntėte.

***

## Linux amd64 (x86_64)

### Sistemos reikalavimai

| Reikalavimas | Minimalus | Rekomenduojamas |
| --- | --- | --- |
| **Distribucija** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **Procesorius** | x86_64 (Intel/AMD) | „Intel Core i7“ arba geresnis |
| **Atmintis (RAM)** | 8 GB | 16 GB arba daugiau |
| **Vaizdo plokštė** | Nereikalinga (apdorojama procesoriumi) | „NVIDIA“ vaizdo plokštė su 4 GB ir daugiau VRAM (12 GB ir daugiau atblokuoja `GPU_PARALLEL`, 7 GB ir daugiau išlaiko „Texture Aware“ išjungtą vieno vaizdo kelyje) |
| **Saugykla** | 2 GB laisvos vietos | SSD su 10 GB ar daugiau laisvos vietos |
| **Python** | Python 3.7+ (skirta SDK) | Python 3.10+ |

> **„Ubuntu 20.04“ ir „Debian 11“ nepalaikomi.** `.deb` priklausomybių sąrašas
> sudarytas remiantis tuo, su kuo iš tikrųjų susiejamas Chloros užkulisinis modulis, ir tai apima
> „`libc6 (>= 2.34)`“. „Focal“ ir „bullseye“ versijose yra įdiegta „glibc 2.31“, todėl „`apt`“ atsisako
> įdiegti iš karto, o ne leidžia, kad įdiegimas žlugtų vėliau, vykdymo metu.

### Įdiegimas

```bash
sudo dpkg -i chloros_1.2.0_amd64.deb
sudo apt-get install -f    # pulls the declared dependencies (libibverbs1, libcap2-bin)
```

{% hint style="info" %}
`dpkg -i` neišsprendžia priklausomybių. Jei pranešama apie trūkstamus paketus, `sudo apt-get install -f` (arba `sudo apt --fix-broken install`) užbaigia diegimą — tai yra įprastas procesas, o ne klaida.
{% endhint %}

Patikrinkite diegimą:



<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo dpkg -i chloros_1.2.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block naming the bundled wheel path under /usr/lib/chloros/sdk/, any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend.service" hint -->

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```***

## Linux arm64 (NVIDIA Jetson)

### Sistemos reikalavimai

| Reikalavimas | Minimalus | Rekomenduojamas |
| --- | --- | --- |
| **Platforma** | „NVIDIA Jetson“ su „JetPack 6“ | „Jetson Orin NX“ 16 GB arba „AGX Orin“ |
| **„JetPack“** | „JetPack 6.x“ | Naujausia „JetPack 6“ versija |
| **Atmintis (RAM)** | 8 GB (bendrai naudojama GPU/CPU) | 16 GB+ bendrai naudojama (12 GB+ yra riba, reikalinga lygiagretiems GPU darbininkams) |
| **Saugykla** | 2 GB laisvos vietos | NVMe SSD su 10 GB+ laisvos vietos |
| **Python** | Python 3.7+ (skirta SDK) | Python 3.10+ |

### Įdiegimas

```bash
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f
chloros-cli --version
```

Toks pats išdėstymas kaip ir amd64 `.deb`, su CUDA versija, pritaikyta „Jetson Orin“ / „Orin NX“ / „Orin Nano“. Informaciją apie „Jetson“ atmintį, šilumines savybes ir diegimą praktikoje rasite [„NVIDIA Jetson“ vadove](nvidia-jetson-guide.md).

***

## Python ir SDK diegimas (visi Linux)

„SDK“ yra grynasis „Python“ „HTTP“ klientas, skirtas užkulisiniam moduluii, todėl tas pats paketas veikia tiek „amd64“, tiek „arm64“ platformose. Du šaltiniai:**Iš PyPI** — paskelbta stabili versija:

```bash
pip install chloros-sdk
```

**Iš pridedamo „wheel“ failo** — garantuotai suderinamas su ką tik įdiegtu CLI/backend (naudokite tai, jei jūsų `.deb` yra naujesnis nei PyPI):

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

{% hint style="warning" %}
**PEP 668 distribucijos** (Ubuntu 23.10+, Debian 12+) neleidžia diegti per pip visoje sistemoje. Naudokite `pip install --user …`, virtualią aplinką arba `sudo pip install --break-system-packages …`. Paketų diegimo programa niekada automatiškai neįdiegia „SDK“ į jūsų sistemos „Python“ — šį sprendimą paliekame jums.
{% endhint %}

Pasirenkami priedai:

| Priedas | Komanda | Prideda |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py`, skirtas tiesioginiam pažangos transliavimui |
| `camera` | `pip install chloros-sdk[camera]` | `bleak`, skirtas BLE (DAQ-M) perdavimui |

Patikrinkite SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb` įdiegia Chloros, CLI ir užkulisinę sistemą. Python, SDK bendrauja su tuo užkulisiniu komponentu per vietinį HTTP API (`http://127.0.0.1:5000`) ir prireikus jį paleidžia automatiškai. Visada naudokite tiesioginį IPv4 adresą, o ne „`localhost`“ — „`localhost`“ gali būti išspręstas kaip „`::1`“ ir užtrukti maždaug dvi sekundes vienam užklausimui.
{% endhint %}

***

## Pirminis nustatymas

### 1. Prisijunkite

Prieigai prie CLI ir SDK reikalingas mokamas Chloros+ lygis (**Copper** ar aukštesnis), kuris taikomas serverio pusėje: atsijungęs vartotojas gauna `401 AUTH_REQUIRED`, o nemokamo lygio („Iron“) vartotojas – `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

Prisijungimo duomenys yra išsaugomi `~/.chloros/user_session.json`.

{% hint style="warning" %}
**Po kiekvienos įdiegos ar atnaujinimo turite prisijungti iš naujo.** Paketo scenarijus „`prerm`“ sąmoningai išvalo „`~/.chloros/user_session.json`“ ir įkeptą licenciją kiekvienam kompiuterio vartotojui, todėl nauja versija visada iš naujo patvirtina licenciją, o ne pasikliauja pasenusiais įkeptais duomenimis.
{% endhint %}

### 2. Patikrinkite savo licencijos būseną

```bash
chloros-cli status
```

`chloros-cli status` veikia bet kuriame lygyje (įskaitant nemokamą), todėl visada galite pamatyti, kodėl prieiga yra arba nėra prieinama.

### 3. Vykdykite sistemos diagnostiką

```bash
chloros-cli selftest
```

Septyni patikrinimai atliekami eilės tvarka, o komanda baigiasi nulinės vertės kodu, jei bent vienas iš jų nepavyksta:

| # | Patikrinimas | Ką patvirtina |
| --- | --- | --- |
| 1 | **Versija** | „CLI“ praneša savo versiją (`v1.2.0`). |
| 2 | **Laisvas prievadas** | Prievadas 5000 yra laisvas, *arba* į jį jau atsakė tinkamai veikiantis „Chloros“ užpakalinis modulis (tai laikoma sėkmingu patikrinimu). |
| 3 | **Užpakalinio modulio paleidimas** | Paleidžiamas užpakalinio modulio vykdomasis failas. |
| 4 | **API testas (`/api/test`)** | Užkulisinė programa atsako `status: ok`. |
| 5 | **Sistemos informacija** | Išspausdina `GPU: <name>, CUDA: <bool>, PyTorch: <version>` iš `/api/system-info`. |
| 6 | **Triukšmo šalinimo modeliai** | Randa `*.pth.enc` modelius (Linux: `/usr/lib/chloros/models`). |
| 7 | **CUDA + triukšmo šalinimo įrankis**| „Texture Aware“ iš tiesų veikia — reikalinga CUDA**ir** bent vienas modelio failas. |

Vykdymas baigiasi su „`N/7 checks passed`“, išvardijant visus nesėkmių atvejus pagal pavadinimus.

### 4. Apdorokite savo pirmąjį duomenų rinkinį

```bash
chloros-cli process ~/datasets/flight001
```

***

## Failai ir katalogai

### Vartotojui

Chloros saugo savo prisijungimo duomenis ir CLI konfigūraciją viename tarpplatforminiame kataloge, **`~/.chloros/`** (Windows, `%USERPROFILE%\.chloros\`). Dvi Linux specifinės talpyklos laikosi XDG konvencijų — jos atsižvelgia į nustatymus `XDG_CONFIG_HOME` / `XDG_CACHE_HOME`, jei tokie yra nustatyti.

| Kelias | Paskirtis |
| --- | --- |
| `~/.chloros/user_session.json` | Prisijungimo sesijos talpyklė, kurią įrašo `chloros-cli login` (išvaloma kiekvieną kartą įdiegiant ar atnaujinant paketą) |
| `~/.chloros/working_directory.txt` | Numatytasis projekto aplanko perrašymas (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | CLI kalbos nustatymas (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Kalbos nustatymas, bendras su Windows grafine vartotojo sąsaja — čia `language` turi pirmenybę prieš `cli_language.json` |
| `~/.chloros/update_cache.json` | Vienos valandos talpyklė, skirta „Linux“/„Jetson“ paleidimo atnaujinimų patikrinimui |
| `~/.chloros/backend.log` | Užpakalinės sistemos žurnalas, kai užpakalinė sistema buvo paleista per CLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Į talpyklą įrašyti kiekvienos kameros „LATTICE“ kalibravimo paketai, susieti pagal serijos numerį ir paketo hash |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | Pasirinktiniai vartotojo pakeitimai DAQ ribų koregavimo profiliams |
| `~/.config/chloros/system_config.json` | „Dynamic Compute Adaptation“ įrenginio profilio talpyklos duomenys — ištrinkite juos, kad būtų priverstinai atliktas naujas įrenginio aptikimas |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Backend serverio žurnalai, po vieną failą kiekvienam paleidimui |
| `~/Chloros Projects/` | Numatytasis projekto aplankas, kai nėra nustatytas joks pakeitimas |

### Visoje sistemoje

| Kelias | Paskirtis |
| --- | --- |
| `/usr/bin/chloros-cli` | Apgaubimo scenarijus — nustato `LD_LIBRARY_PATH` įtrauktoms natūralioms bibliotekoms, tada paleidžia tikrąjį dvejetinį failą |
| `/usr/bin/chloros-backend` | Apvyniojimo scenarijus — tas pats, plius `CHLOROS_PRODUCTION=1`, kad užkulisio autentiškumo patikrinimo vartai niekada negalėtų tyliai išsijungti |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Kompiliuoti binariniai failai |
| `/usr/lib/chloros/arena_runtime/` | „Arena“ SDK vykdymo aplinka, reikalinga „LATTICE“ kameroms |
| `/usr/lib/chloros/models/*.pth.enc` | Šifruoti triukšmo šalinimo modeliai, kuriuos naudoja „Texture Aware“ debayeris |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK ratas, atitinkantis būtent šią versiją |
| `/usr/lib/chloros/exiftool` | Pridėta „exiftool“ programa (simbolinė nuoroda į `/usr/local/bin/exiftool` nustatoma tik tuo atveju, jei sistemoje nėra „exiftool“) |
| `/etc/chloros/update.conf` | Atnaujinimo kanalo konfigūracija, kurią nuskaito `chloros-cli update` |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Nustato `net.ipv4.ip_unprivileged_port_start = 319`, kad užkulisinis modulis galėtų priskirti PTP prievadus be root teisių |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Dinaminį įkėlėją nukreipia į `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Suteikia prisijungusiam vartotojui prieigą prie DAQ-U USB serijinio tilto (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend.service` | Įjungia nuolat veikiančią foninę paslaugą (įdiegta, **neįjungta**) |
| `/usr/share/applications/chloros-cli.desktop` | Programų meniu punktas „Chloros CLI“, kuris atidaro terminalą |

## Fono programos vykdomojo failo vieta

CLI ir SDK automatiškai aptinka foninę programą:

| Komponentas | Kelias |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Užkulisiai | `/usr/lib/chloros/chloros-backend` |

Pakeiskite užpakalinės sistemos kelią naudodami vėliavėlę „`--backend-exe` CLI“ arba konstruktoriaus parametrą „`backend_exe` SDK“, o prievadą – naudojant `--port` (numatyta reikšmė – `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` nukreipia į **`lattice`**,**`project`**ir**`daq pool-*`** komandų šeimas nuotolinėje galinėje dalyje. Pagrindinės komandos (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) sąmoningai jį ignoruoja ir visada nukreipia į `http://127.0.0.1:<port>`.
{% endhint %}

***

## „LATTICE“ kameros ir DAQ šviesos jutikliai ant „Linux“

Visos „live-hardware“ komandų šeimos veikia „Linux“ (amd64 ir „Jetson“):

* **`chloros-cli lattice`** — aptinka, prijungia, konfigūruoja ir fiksuoja vaizdą iš „LATTICE“ kamerų ir sinchronizuotų matricos. `.deb` įtraukia jiems reikalingą „Arena SDK“ vykdymo aplinką ir ją užregistruoja dinaminėje įkėlimo sistemoje.
* **`chloros-cli daq pool-*`** — prijungti DAQ-U/M/E šviesos jutiklius per užkulisinį rezervą, transliuoti kalibruotus spektrus ir įrašyti `.daq` failus. Kompiliuotas „CLI“ pateikia tik „`pool-*`“ šeimos elementus: „`pool-connect`“, „`pool-disconnect`“, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`.
* **`chloros-cli project`** — paleiskite išsaugotą projektą (jo kameras, jutiklius ir apdorojimo nustatymus) be vartotojo sąsajos.
* **`chloros-cli time-sync`** — patikrinti PTP „grandmaster“, kurį „Chloros“ užkulisinė programa naudoja „LATTICE“ kameroms ir „DAQ-E“ jutikliams.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` yra privalomas `pool-latest`, `pool-stream`, `pool-record` ir `pool-set-cap`; `pool-list` rodo ID, kurie šiuo metu yra rezervoje.

{% hint style="info" %}
**Pirmojo DAQ-E prisijungimo prie kompiuterio su keliais tinklo adresais atveju rekomenduojama naudoti `--eth-host`.** Automatinis aptikimas naršo mDNS ir gali nepranešti apie jutiklio sąsają dėl tuščios ARP talpyklos, todėl pirmasis `pool-connect --eth` po paleidimo gali nepavykti net ir tada, kai jutiklis veikia nepriekaištingai. Nurodžius jutiklio IP adresą arba kompiuterio vardą, aptikimas visiškai praleidžiamas.
{% endhint %}

**DAQ-U serijinės teisės** tvarkomos pagal įdiegtą „udev“ taisyklę (`uaccess` + grupė `dialout`). Jei jau prijungtas jutiklis lieka nepasiekiamas, perkraukite taisykles arba prijunkite jį iš naujo:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Visą komandų rinkinį rasite [CLI nuorodoje](../CLI.md).

### Nuolat veikiantis PTP be monitoriaus kompiuteriams

Pirmą kartą įdiegus, sukuriama „systemd“ vienetas `chloros-backend.service`, tačiau jis **nėra įjungtas**. Jei naudojate kompiuterį be monitoriaus „Jetson“ arba serverį, kuriame PTP laiko sinchronizavimas turi veikti nuolat, kad veiktų DAQ-E jutikliai ir „LATTICE“ kameros, įjunkite jį:

```bash
sudo systemctl enable --now chloros-backend.service
sudo systemctl status chloros-backend.service
```

Be jo PTP veikia tik tada, kai veikia „Chloros“ užkulisinė programa — tai yra, aktyvios „CLI“ / „SDK“ sesijos metu.

Įrenginys susieja foną su `127.0.0.1:5000` (`CHLOROS_HOST` / `CHLOROS_PORT` aplinkos nustatymai įrenginyje; perrašykite į `sudo systemctl edit chloros-backend.service`) ir, jei įvyksta gedimas, po 5 sekundžių jį paleidžia iš naujo.

**Kaip PTP gauna savo prievadus.** PTP naudoja UDP 319/320, abu žemiau įprastos 1024 privilegijuotų prievadų ribos. Paketo `postinst` įrašo `/etc/sysctl.d/60-chloros-ptp.conf` su `net.ipv4.ip_unprivileged_port_start = 319`, o tai leidžia užpakalinės dalies programai juos susieti, kai ji veikia jūsų vartotojo vardu. Be to, kaip papildoma apsaugos priemonė, `setcap cap_net_bind_service,cap_net_raw=+ep` pritaikomas ir užkulisinio modulio binariniam failui – būtent todėl `libcap2-bin` yra deklaruota paketo priklausomybė.***

## „Bash“ skriptų pavyzdžiai

{% hint style="info" %}
**Skriptams pritaikyti išėjimo kodai.**`chloros-cli process` baigia veiklą su kodu `0` sėkmės atveju ir**su nelyginiu nuliui kodu nesėkmės atveju — įskaitant vykdymą, kurio metu buvo prašoma vaizdų produktų, bet nė vienas nebuvo įrašytas** (jis išveda `Processing finished but wrote no image products.` ir nurodo projekto aplanką bei įprastas priežastis). Sėkmingai įvykdyti vykdymai praneša, kiek vaizdų produktų buvo įrašyta (`Image products written: N`). Išeitimo kodai: `0` – sėkmė, `1` – nesėkmė, `2` – argumento klaida, `130` – nutraukta.
{% endhint %}

### Daugelio duomenų rinkinių apdorojimas

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    if chloros-cli process "$dataset" --format "TIFF (32-bit, Percent)"; then
        echo "Done: $(basename "$dataset")"
    else
        echo "FAILED: $(basename "$dataset")" >&2
    fi
done
```

### Apdorojimas su pasirinktiniais nustatymais

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

Galiojančių `--format` reikšmių yra lygiai keturios, ir jos turi tarpų — visada jas įrašykite kabutėse:

| `--format` reikšmė | Išvesties aplankas |
| --- | --- |
| `TIFF (16-bit)` *(numatyta)* | `tiff16` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer` priima `standard` (numatyta reikšmė) arba `texture-aware` (Chloros+).

### Automatinis apdorojimas naudojant „Cron“

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

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### Prieiga uždrausta

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### „setcap failed“ įdiegimo metu

`.deb` taiko `cap_net_bind_service` prie `/usr/lib/chloros/chloros-backend`, kad galėtų priskirti PTP prievadus 319/320 be root teisių. Jei diegimo metu trūko `libcap2-bin`, šis veiksmas praleidžiamas. Įdiekite jį ir iš naujo įdiekite paketą:

```bash
sudo apt install libcap2-bin
sudo apt reinstall chloros
```

### PTP nepaleidžiama / negali susieti 319 prievado

Patikrinkite, ar neprižiūrimų prievadų riba buvo sumažinta, ir, jei ne, pritaikykite ją dabartiniam paleidimui:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Tada patikrinkite „grandmaster“:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### „Nerasti „LATTICE“ kamerų tvarkyklių“

„Arena“ SDK vykdymo aplinka nėra nustatoma. Patikrinkite, ar yra ir ar atnaujinta paketo įrašoma įkėlimo konfigūracija:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
sudo ldconfig
ls /usr/lib/chloros/arena_runtime | head
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

Užkulisio žurnalai, susiję su nepavykusiu paleidimu, yra `~/.cache/chloros/logs/`.

### CUDA neaptikta

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

`chloros-cli selftest` vienoje eilutėje praneša tą patį: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Trūksta bendrųjų bibliotekų

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

### Lėtas paleidimas sistemose su SD kortele

Kiekvieną kartą paleidžiant programą, kompiliuoti binariniai failai išsipakuojami į laikiną katalogą. Jei egzistuoja `/mnt/ssd/tmp`, Chloros jį naudoja automatiškai; priešingu atveju nustatykite `TMPDIR` į greitą failų sistemą:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Chloros atnaujinimas Linux sistemoje

Komanda `update` veikia tik Linux/skirta tik „Jetson“. Ji patikrina versiją, paskelbtą atnaujinimų kanale, sukonfigūruotame `/etc/chloros/update.conf`, ir siūlo atsisiųsti bei įdiegti atitinkamą `.deb`:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux/„Jetson“ sistemoje komanda CLI taip pat kiekvieno paleidimo metu atlieka neblokuojantį atnaujinimų patikrinimą (rezultatas saugomas `~/.chloros/update_cache.json` talpykloje vieną valandą) ir, jei yra naujesnė versija, išveda pranešimą `Update available: vX.Y.Z`. Jūsų nustatymai ir projektai išlieka po atnaujinimo; po to reikės vėl prisijungti.

## Pašalinimas

```bash
sudo apt remove chloros
```

Pašalinus programą sustabdoma `chloros-backend.service`, atkuriamas numatytasis neprivilegijuotų prievadų ribinis skaičius (1024), pašalinama „bundled-exiftool“ simbolinė nuoroda ir „Arena“ įkėlėjo konfigūracija, taip pat išvalomi talpykloje saugomi prisijungimo duomenys. Jūsų projektai ir „`~/.chloros/`“ duomenų failai lieka nepakitę.

***

## Tolimesni veiksmai

* [„NVIDIA Jetson“ vadovas](nvidia-jetson-guide.md) — „Jetson“ specifinė optimizacija ir diegimas
* [CLI : Komandinė eilutė](../CLI.md) — „CLI“ vadovas
* [API : Python SDK](../api-python-sdk.md) — vadovas „SDK“
* [CLI nuoroda](../reference/cli-reference.md) ir [SDK nuoroda](../reference/sdk-reference.md) — išsamūs komandų /API sąrašai versijai 1.2.0
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — kaip Chloros prisitaiko prie jūsų aparatinės įrangos
