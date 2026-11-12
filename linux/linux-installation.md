# Linux diegimas

Chloros Linux sistemai platinamas kaip `.deb` paketai, kurie įdiegia CLI, užkulisinį serverį ir Python SDK (sistemai `python3` taip pat pridedamas versijai atitinkantis „wheel“ failas, skirtas „virtualenvs“).

Paketų failų pavadinimuose nurodyta versija ir architektūra: „`chloros_1.3.0_amd64.deb`“ – x86_64, o „`chloros_1.3.0_arm64_jp6.deb`“ – „arm64“ (NVIDIA Jetson, JetPack 6). Toliau pateiktose komandose pakeiskite failo pavadinimą į tą, kurį iš tikrųjų atsisiuntėte.

{% hint style="info" %}
**Nauja 1.3.0 versijoje: programos pateikiamos neišpakuotos.** Užkulisinė dalis ir CLI įdiegiamos kaip aplankai, `/usr/lib/chloros/backend/` ir `/usr/lib/chloros/cli/`, o ne kaip atskiri savaime išsiskleidžiantys failai. Jie nebeišsiskleidžia į laikiną aplanką kiekvieną kartą paleidžiant, todėl paleidžiasi daug greičiau (x86_64 architektūroje užkulisinė dalis anksčiau kiekvieną kartą paleidžiant išsiskleisdavo apie 100 sekundžių) ir nebereikia kelių GB laisvos vietos kataloge „`/tmp`“. „amd64“ versijos „`.deb`“ dydis taip pat yra maždaug perpus mažesnis nei anksčiau.
{% endhint %}

***

## Linux amd64 (x86_64)

### Sistemos reikalavimai

| Reikalavimas | Minimalus | Rekomenduojamas |
| --- | --- | --- |
| **Distribucija** | „Ubuntu“ 22.04 LTS+ / „Debian“ 12+ | „Ubuntu“ 24.04 LTS |
| **Procesorius** | x86_64 (Intel/AMD) | Intel Core i7 ar geresnis |
| **Atmintis (RAM)** | 8 GB | 16 GB ar daugiau |
| **Vaizdo plokštė** | Nereikalinga (apdorojama procesoriumi) | „NVIDIA“ vaizdo plokštė su 4 GB ar daugiau VRAM (12 GB ar daugiau atrakina `GPU_PARALLEL`, 7 GB ar daugiau užtikrina, kad „Texture Aware“ neveiktų vieno vaizdo kelyje) |
| **Saugykla** | 2 GB laisvos vietos | SSD su 10 GB ar daugiau laisvos vietos |
| **Python** | Python 3.7 ar naujesnė versija (SDK) | Python 3.10 ar naujesnė versija |

> **„Ubuntu 20.04“ ir „Debian 11“ nepalaikomi.** „`.deb`“ priklausomybių sąrašas
> sudarytas remiantis tuo, su kuo iš tikrųjų susiejamas Chloros užpakalinis modulis, ir tai apima
> „`libc6`“, naujesnį už „glibc 2.31“, kurį pateikia „focal“ ir „bullseye“, todėl „`apt`“ atsisako
> įdiegti iš karto, o ne leidžia, kad vėliau įdiegimas žlugtų vykdymo metu.

### Įdiegimas

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
```

{% hint style="info" %}
**Naudokite „`apt install ./…`“, o ne „`dpkg -i`“.** „`apt`“ išsprendžia paketo priklausomybes ir taip pat įdiegia tai, ką rekomenduoja: Python SDK reikalavimus, `python3-numpy` ir `python3-requests`. `dpkg -i` neįdiegia nė vieno iš jų. CLI ir užkulisiai yra savarankiški ir veikia be jų, tačiau `import chloros_sdk` jų reikia; diegimo išvestyje nurodomi visi trūkstami elementai. Palikite `./`, kad `apt` įdiegs šį failą, o ne ieškos to paties pavadinimo paketo. Paketas taip pat deklaruoja `libgl1`, kurį įkelia pridedamas „OpenCV“, o minimaliame serverio atvaizde jo gali trūkti.
{% endhint %}

Patikrinkite diegimą:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo apt install ./chloros_1.3.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block ("chloros_sdk 1.3.0 for python3" and the "In a virtualenv: pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl" line), any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend@<user>.service" hint -->

***

## Linux arm64 (NVIDIA Jetson)

### Sistemos reikalavimai

| Reikalavimas | Minimalus | Rekomenduojamas |
| --- | --- | --- |
| **Platforma** | „NVIDIA Jetson“ su „JetPack 6“ | „Jetson Orin NX“ 16 GB arba „AGX Orin“ |
| **„JetPack“** | „JetPack“ 6.x | Naujausia „JetPack“ 6 versija |
| **Atmintis (RAM)** | 8 GB (bendra GPU/CPU) | 16 GB+ bendros atminties (12 GB+ yra riba lygiagrečiam apdorojimui) |
| **Saugykla** | 2 GB laisvos vietos | NVMe SSD su 10 GB+ laisvos vietos |
| **Python** | Python 3.7+ (SDK) | Python 3.10+ |

### Įdiegimas

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

Toks pats išdėstymas kaip ir amd64 `.deb` (įtrauktas Python SDK, priėmimo buferiai ir užkulisinės paslaugos), skirtas „Jetson Orin“ / „Orin NX“ / „Orin Nano“ su **„JetPack 6“** (L4T R36). Jo „PyTorch“ yra „JetPack 6“ CUDA versija, skirta „Orin“, todėl GPU apdorojimas, įskaitant „Texture Aware“ debayerį, vyksta „Jetson“ GPU. Paketas pats neapima CUDA: jis naudoja CUDA ir cuDNN bibliotekas, kurias įdiegia „JetPack 6“ (`sudo apt install nvidia-jetpack` atvaizde, kuriame jų nėra). Jei CUDA GPU negalima naudoti, apdorojimas vyksta CPU, o „`--debayer texture-aware`“ naudoja standartinį „debayer“ (užkulisiai užregistruoja vieną „`[DEBAYER] … needs a CUDA GPU …`“ eilutę, paaiškinančią priežastį). Informaciją apie „Jetson“ atmintį, šiluminius parametrus ir veikimą lauke rasite [„NVIDIA Jetson“ vadove](nvidia-jetson-guide.md).

***

## Python SDK diegimas (visoms Linux versijoms)**„`.deb`“ jūsų vardu įdiegia SDK.** Abu paketai (amd64 ir „Jetson“) įdiegia jį sistemai `python3`, todėl `import chloros_sdk` veikia iškart po įdiegimo, be „pip“ ir be tinklo. Jo paketai (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` ir projekto įkėlėjai) patenka į `/usr/lib/python3/dist-packages`, priklausantį paketui: atnaujinimas juos pakeičia, o pašalinus paketą – ir jie pašalinami. Versija visada atitinka jūsų įdiegtą CLI ir užkurtį.

SDK reikalavimai, `python3-numpy` ir `python3-requests`, yra **rekomenduojami, o ne privalomi**: `sudo apt install ./chloros_<version>_<arch>.deb` juos įdiegia, o `dpkg -i` – ne. Įdiegimo išvestyje nurodoma SDK versija, kurią importuoja `python3`, bet kokie trūkstami reikalavimai, ir visus `chloros_sdk` elementus kitur `python3` kelyje, kurie užgožia paketo kopiją (pavyzdžiui, anksčiau įdiegtą su `pip install --user`).**„virtualenv“ aplinkoje**, kuri nemato sistemos paketų, į ją įdiekite pridedamą „wheel“ failą (visiškai neprisijungus prie interneto):

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

**Iš PyPI** — tik kompiuteriui, kuriame neįdiegtas Chloros paketas, kurio SDK valdo kito kompiuterio užkurtį (žr. [Nuotolinio užkurtio režimas](../reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

Papildomos galimybės, skirtos diegimui naudojant „pip“ (virtualioje aplinkoje arba iš PyPI):

| Papildoma galimybė | Komanda | Prideda |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py`, skirtas tiesioginiam pažangos transliavimui |
| `camera` | `pip install chloros-sdk[camera]` | `bleak`, skirtas BLE (DAQ-M) perdavimui |

Patikrinkite SDK:

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
Python SDK bendrauja su „`.deb`“ įdiegtu užkulisiu per vietinį HTTP API (`http://127.0.0.1:5000`). `ChlorosLocal` ir prisijungimo pagalbinės programos jį paleidžia automatiškai, kai reikia; išsaugoto projekto `connect_all()` to niekada nedaro, todėl kompiuteryje be monitoriaus paleiskite užkurtį kaip [paslaugą](#always-on-ptp-for-headless-hosts). Visada naudokite tiesioginį IPv4 adresą, o ne `localhost` — `localhost` gali būti išspręstas kaip `::1` ir užtrukti maždaug dvi sekundes vienam užklausimui.
{% endhint %}

***

## Pirminis nustatymas

### 1. Prisijunkite

Prieigai prie CLI ir SDK reikalingas mokamas „Chloros+“ lygis (**Copper** ar aukštesnis), kuris užtikrinamas serverio pusėje: atsijungęs skambintojas gauna adresą `401 AUTH_REQUIRED`, o nemokamo lygio (Iron) skambintojas gauna adresą `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

Prisijungimo duomenys saugomi `~/.chloros/user_session.json`.

{% hint style="info" %}
**Atnaujinus versiją jūsų prisijungimo duomenys išlieka; pašalinus versiją jie ištrinami.** Įdiegus naujesnę „`.deb`“ versiją ant esamos instaliacijos (įskaitant per „`chloros-cli update`“), „`~/.chloros/user_session.json`“ ir išsaugota licencija išlieka, todėl CLI / SDK užduotys be grafinės sąsajos toliau veikia. Pašalinus paketą (`sudo apt remove chloros`), ištrinami visų kompiuterio vartotojų išsaugoti prisijungimo duomenys ir licencijos talpyklos turinys, todėl naujai diegiant programą vėl reikės `chloros-cli login`.
{% endhint %}

### 2. Patikrinkite savo licencijos būseną

```bash
chloros-cli status
```

`chloros-cli status` veikia bet kuriame pakete (įskaitant nemokamą), todėl visada galite pamatyti, kodėl prieiga yra arba nėra prieinama.

### 3. Vykdykite sistemos diagnostiką

```bash
chloros-cli selftest
```

Septyni patikrinimai atliekami eilės tvarka, o komanda baigiasi nulinės vertės kodu, jei bent vienas iš jų nepavyksta:

| # | Patikrinimas | Ką patvirtina |
| --- | --- | --- |
| 1 | **Versija** | Komandinė eilutė (CLI) praneša savo versiją (`v1.3.0`). |
| 2 | **Laisvas prievadas** | Prievadas 5000 yra laisvas, *arba* jau atsakė veikiantis Chloros užpakalinis modulis (tai laikoma sėkme). |
| 3 | **Užpakalinio modulio paleidimas** | Paleidžiamas užpakalinio modulio vykdomasis failas. |
| 4 | **API testas (`/api/test`)** | „Backend“ atsako `status: ok`. |
| 5 | **Sistemos informacija** | Išspausdina „`GPU: <name>, CUDA: <bool>, PyTorch: <version>`“ iš „`/api/system-info`“. |
| 6 | **Triukšmo šalinimo modeliai** | Randa „`*.pth.enc`“ modelius (Linux sistemoje: `/usr/lib/chloros/models`). |
| 7 | **CUDA + triukšmo šalinimo įrankis**| „Texture Aware“ iš tiesų veikia — reikalinga CUDA**ir** bent vienas modelio failas. |

Vykdymas baigiasi su `N/7 checks passed`, išvardijant visas nesėkmes pagal pavadinimus.

### 4. Apdorokite savo pirmąjį duomenų rinkinį

```bash
chloros-cli process ~/datasets/flight001
```

***

## Failai ir katalogai

### Vartotojui priskirti

Chloros saugo savo prisijungimo duomenis ir CLI konfigūraciją viename tarpplatforminiame kataloge, **`~/.chloros/`** (Windows sistemoje – `%USERPROFILE%\.chloros\`). Dvi Linux sistemoms būdingos talpyklos laikosi XDG konvencijų — jos naudoja katalogus „`XDG_CONFIG_HOME`“ / „`XDG_CACHE_HOME`“, jei jie yra nustatyti.

| Kelias | Paskirtis |
| --- | --- |
| `~/.chloros/user_session.json` | Prisijungimo sesijos talpyklė, sukurta `chloros-cli login` (išsaugoma atnaujinant paketą; išvaloma pašalinus paketą) |
| `~/.chloros/working_directory.txt` | Numatytasis projekto aplanko perrašymas (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | Komandinės eilutės (CLI) kalbos nustatymas (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Kalbos nustatymas, bendras su Windows grafine vartotojo sąsaja — čia nustatymas „`language`“ turi pirmenybę prieš „`cli_language.json`“ |
| `~/.chloros/update_cache.json` | Vienos valandos talpyklos duomenys, skirti Linux/„Jetson“ paleidimo atnaujinimų patikrinimui |
| `~/.chloros/backend.log` | Užkulisio žurnalas, kai užkulisio programa buvo paleista per CLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Į talpyklą įrašyti kiekvienos kameros „LATTICE“ kalibravimo paketai, susieti pagal serijos numerį ir paketo hash |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | Pasirenkami vartotojo nustatymai, pakeičiantys DAQ „cap“ korekcijos profilius |
| `~/.config/chloros/system_config.json` | „Dynamic Compute Adaptation“ įrenginio profilio talpyklos duomenys — ištrinkite juos, kad būtų priverstinai atliktas naujas įrenginio aptikimas |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Užkulisinio serverio žurnalai, po vieną failą kiekvienam paleidimui |
| `~/Documents/MAPIR/Chloros Projects/` | Numatytasis projekto aplankas, kai nenustatytas joks perrašymas |

### Visos sistemos

| Kelias | Paskirtis |
| --- | --- |
| `/usr/bin/chloros-cli` | Apgaubimo scenarijus — nustato `LD_LIBRARY_PATH` įtrauktoms natūralioms bibliotekoms (ir `TMPDIR` į `/mnt/ssd/tmp`, jei tas aplankas egzistuoja), tada paleidžia tikrąjį binarinį failą |
| `/usr/bin/chloros-backend` | Apgaubimo scenarijus — tas pats, skirtas užpakalinės dalies programai, kurią jis pažymi kaip įdiegtą gamybinę versiją |
| `/usr/lib/chloros/cli/`, `/usr/lib/chloros/backend/` | Sukompiliuota CLI ir užkulisinė dalis, įdiegiamos išpakuotos (nauja 1.3.0 versijoje); programos yra `cli/chloros-cli` ir `backend/chloros-backend` |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Dviejų eilučių paleidimo scenarijai, kurie paleidžia aukščiau nurodytas tikrąsias programas — laikomi šiuose keliuose, nes SDK, CLI ir senesni scenarijai jų čia ieško |
| `/usr/lib/chloros/arena_runtime/` | „Arena“ SDK-vykdymo aplinka, reikalinga „LATTICE“ kameroms |
| `/usr/lib/chloros/models/*.pth.enc` | Šifruoti triukšmo šalinimo modeliai, kuriuos naudoja „Texture Aware“ debayeris |
| `/usr/lib/python3/dist-packages/chloros_sdk/` (ir `lattice_sdk`, `lattice_cam`, `arena_api` – projekto įkėlėjai) | Python SDK, įdiegtas sistemai `python3` ir priklausantis paketui |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK „wheel“, atitinkantis būtent šią versiją, skirtas „virtualenv“ |
| `/usr/lib/chloros/exiftool` | Įtrauktas „ExifTool“ paleidimo įrankis (simbolinė nuoroda į `/usr/local/bin/exiftool` nustatoma tik tuo atveju, jei sistemoje nėra „exiftool“). Nuo 1.3.0 versijos „ExifTool“ skriptas pateikiamas su savo „Perl“ biblioteka, esančia `/usr/lib/chloros/exiftool-dist/`, todėl jis veikia be sistemos `libimage-exiftool-perl` paketo |
| `/etc/chloros/update.conf` | „Update-channel“ konfigūracija, kurią nuskaito `chloros-cli update` |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Nustato `net.ipv4.ip_unprivileged_port_start = 319`, kad užkulisinė programa galėtų priskirti PTP prievadus be „root“ teisių |
| `/etc/sysctl.d/60-chloros-gige.conf` | Padidina branduolio lizdų priėmimo buferius (`net.core.rmem_max`, `net.core.rmem_default`) iki 32 MB, skirtus „LATTICE“ kamerų srautams, ir pritaiko juos diegimo metu (jei vertė jau yra nustatyta didesnė, ji paliekama nepakeista). Pagal branduolio numatytuosius nustatymus kameros paketai yra atmesti branduolyje, o masyvas praranda kadrus ir kadrų dažnį; `chloros-cli lattice network` praneša apie buferius, kai jų lygis yra žemas. Žr. [Linux kompiuteriai — lizdų priėmimo buferiai](../reference/cli-reference.md#linux-hosts--socket-receive-buffers) |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Nukreipia dinaminį įkėlėją į `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Suteikia prisijungusiam vartotojui prieigą prie DAQ-U USB serijinio tilto (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend@.service` | Įjungia nuolat veikiančią užkulisinę paslaugą, kuri paleidžia užkulisinę dalį kaip jūsų nurodytą vartotoją (įdiegta, **neįjungta**) — norėdami ją įjungti, žr. [Always-On PTP](#always-on-ptp-for-headless-hosts) |
| `/lib/systemd/system/chloros-backend.service` | Ta pati paslauga, vykdanti foninę programą kaip **root**(įdiegta,**neįjungta**), išlaikoma tam, kad kompiuteris, kuriame ji įjungta, toliau veiktų |
| `/var/log/chloros/` | Išsamus „root“ paslaugos žurnalas, kurį gali skaityti grupė `adm` (ištrinamas, kai paketas pašalinamas) |
| `/usr/share/applications/chloros-cli.desktop` | „Chloros CLI“ meniu punktas, atveriantis terminalą |

**Ugniasienė (nauja nuo 1.3.0 versijos).** Jei diegimo metu veikia `ufw` arba `firewalld`, paketas atidaro UDP prievadus `5002` (DAQ-E duomenys), `5353` (mDNS atradimas), `319` ir `320` (PTP), o pašalinus paketą šios taisyklės yra pašalinamos. Jei ugniasienė yra įdiegta, bet neveikia, ji nieko neprideda ir parodo prievadus, kuriuos reikės leisti, jei vėliau ją įjungsite. Ji neatidaro jokių TCP prievadų: DAQ-E programinės įrangos atnaujinimas, kurio metu jutiklis per TCP prisijungia prie šio kompiuterio, kad surinktų vaizdą, baigiasi nesėkme su kodu `… device never connected back on TCP <port> …`, jei aktyvi ugniasienė blokuoja tą ryšį.

## Vidinės programos vykdomojo failo vieta

CLI ir SDK automatiškai aptinka vidinę programą:

| Komponentas | Kelias |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Backend | `/usr/lib/chloros/chloros-backend` (paleidimo programa, skirta `/usr/lib/chloros/backend/chloros-backend`) |

Pakeiskite backend kelio nustatymą naudodami „`--backend-exe`“ CLI žymę arba „`backend_exe`“ SDK konstruktoriaus parametrą, o prievadą – naudojant `--port` (numatyta reikšmė – `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` nukreipia **`lattice`**,**`project`**ir**`daq pool-*`** komandų šeimas nuotolinėje serveryje. Pagrindinės komandos (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) sąmoningai jį ignoruoja ir visada nukreipia į `http://127.0.0.1:<port>`.
{% endhint %}

***

## „LATTICE“ kameros ir DAQ šviesos jutikliai Linux sistemoje

Visos „live-hardware“ komandų grupės veikia Linux sistemoje (amd64 ir „Jetson“):

* **`chloros-cli lattice`** — aptinka, prijungia, konfigūruoja ir fiksuoja vaizdą iš „LATTICE“ kamerų bei sinchronizuotų masyvų. „`.deb`“ apima jiems reikalingą „Arena“ SDK-vykdymo aplinką ir ją registruoja dinaminėje įkrovimo sistemoje.
* **`chloros-cli daq pool-*`** — prijungti DAQ-U/M/E šviesos jutiklius per užkulisinį pulą, transliuoti kalibruotus spektrus, įrašyti `.daq` failus ir keisti „cap“ profilius, o DAQ-E atveju – keisti jo tinklo nustatymus, iš naujo paleisti jį ir atnaujinti jo programinę įrangą. `chloros-cli daq discover` nuskaito jutiklius, kuriuos galite prijungti, o `chloros-cli daq --help` pateikia `pool-*` pakomandų sąrašą.
* **`chloros-cli project`** — valdyti išsaugotą projektą (jo kameras, jutiklius ir apdorojimo nustatymus) be grafinės sąsajos.
* **`chloros-cli time-sync`** — patikrinti PTP „grandmaster“, kurį Chloros užkulisinė programa naudoja „LATTICE“ kameroms ir „DAQ-E“ jutikliams.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` yra privaloma komandai `pool-latest`, `pool-stream`, `pool-record` ir `pool-set-cap`; `pool-list` rodo šiuo metu rezervuotoje grupėje esančius ID.

{% hint style="info" %}
**`--eth-host` praleidžia paiešką.** `pool-connect --eth` suranda „DAQ-E“ per mDNS, naudodamas ARP atsarginį variantą, kuris veikia iš tuščios ARP talpyklos; perduodant jutiklio IP adresą arba kompiuterio vardą su `--eth-host`, prisijungiama prie jo tiesiogiai.
{% endhint %}

**DAQ-U serijinės teisės** tvarkomos pagal įdiegtą „udev“ taisyklę (`uaccess` + grupė `dialout`). Jei jau prijungtas jutiklis lieka nepasiekiamas, perkraukite taisykles arba prijunkite jį iš naujo:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Visą komandų rinkinį rasite [CLI žinyne](../CLI.md).

### Nuolat veikiantis PTP be monitoriaus kompiuteriams

Paketas įdiegia dvi „systemd“ paslaugas užkulisiniam procesui, tačiau nė viena iš jų nėra įjungta. „Jetson“ įrenginyje be monitoriaus arba serveryje, kuriame PTP laiko sinchronizavimas turi veikti nepertraukiamai „DAQ-E“ jutikliams ir „LATTICE“ kameroms, arba kur SDK skriptai jungia išsaugotus projektus (`connect_all()` niekada nepaleidžia užpakalinės dalies), įjunkite paslaugą **savo vartotojo vardu**:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` paleidžia backendą kaip tą vartotoją: jo įrašyti duomenys priklauso tam vartotojui, išsamus jo žurnalas yra to vartotojo `~/.cache/chloros/logs`, o jis naudoja to vartotojo `chloros-cli login` (`~/.chloros`), todėl pirmiausia vieną kartą prisijunkite kaip tas vartotojas. Įdiegus paketą, bus išspausdinta ši komanda su įrašytu jūsų vartotojo vardu.

Be paslaugos foninis procesas (ir kartu su juo PTP grandmasteris) veikia tik tol, kol jį paleidžia kažkas — komanda „`chloros-cli`“ arba SDK-skriptas.

Įrenginys susieja foninį procesą su aplinkos nustatymais „`127.0.0.1:5000` (`CHLOROS_HOST` / `CHLOROS_PORT` aplinkos nustatymai modulyje; perrašykite naudojant `sudo systemctl edit chloros-backend@.service`) ir, įvykus gedimui, paleidžia jį iš naujo po 5 sekundžių.

**„Root“ paslauga.**`chloros-backend.service` paleidžia užkurtį kaip**„root“**. Ji vis dar įdiegta, kad kompiuteris, kuriame ji įjungta, toliau veiktų, tačiau viskas, ką įrašo „root“ užkulisiai, priklauso „root“, todėl vartotojas negali ištrinti savo pačių įrašų; jos žurnalas yra `/var/log/chloros`, kurį gali skaityti `adm` grupė. Norėdami perjungti į savo vartotoją:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

**Paslaugos foninis procesas yra bendrai naudojamas, jo niekada nestabdo klientas.** „`chloros-cli`“ ir SDK skriptai jį naudoja taip, kaip randa: „`process`“ veikia jame, įkeldamas savo projektą vietoj paskutiniojo. Ten, kur CLI, kitais atvejais, paleistų foninę sistemą iš naujo (`--restart` arba `process`, kurio importas nieko nerado), jis nurodo modulio pavadinimą, išveda „`sudo systemctl restart <unit>`“ ir uždaro „`1`“, nes tokiu būdu sustabdytas foninis procesas liktų išjungtas. Paleiskite jį iš naujo patys naudodami „`sudo systemctl restart chloros-backend@$USER.service`“. Paslaugos užkulisiai niekada savaime nesustoja, kai neveikia, o „`chloros-cli backend stop`“ tai atmeta, nurodydama komandą „`systemctl stop`“.**Kaip PTP gauna savo prievadus.** PTP naudoja UDP 319/320, abu žemiau įprastos 1024 privilegijuotų prievadų ribos. Paketo komanda „`postinst`“ įrašo „`/etc/sysctl.d/60-chloros-ptp.conf`“ su „`net.ipv4.ip_unprivileged_port_start = 319`“, o tai leidžia foniniam procesui juos susieti, kai jis veikia jūsų vartotojo vardu. Nuo 1.3.0 versijos paketas nenustato jokių failų teisių (`setcap`) užpakalinėje dalyje, o `libcap2-bin` nebėra priklausomybė: išpakuotoje užpakalinėje dalyje failų teisės sutrukdytų jai paleisti, todėl patys jų nepridėkite.

***

## „Bash“ skriptų pavyzdžiai

{% hint style="info" %}
**Skriptams pritaikyti išėjimo kodai.**`chloros-cli process` sėkmingai užbaigia `0`, o**nesėkmės atveju grąžina nelygų nuliui kodą — įskaitant vykdymą, kurio metu buvo užklausti vaizdo produktai, bet nė vienas nebuvo įrašytas** (jis išveda `Processing finished but wrote no image products.` ir nurodo projekto aplanką bei įprastas priežastis). Sėkmingi vykdymai praneša, kiek vaizdų produktų buvo įrašyta (`Image products written: N`, plius `(plus M clamp masks)`, kai buvo įrašytos kaukės). Išeitimo kodai: `0` – sėkmė, `1` – nesėkmė, `2` – argumento klaida, `130` – nutrauktas.
{% endhint %}

### Kelių duomenų rinkinių apdorojimas

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
    --indices NDVI GNDVI OSAVI \
    --debayer texture-aware \
    --no-vignette
```

Galiojančių `--format` verčių yra lygiai keturios, ir jos turi tarpų — visada įrašykite jas kabutėse:

| `--format` vertė | Išvesties aplankas |
| --- | --- |
| `TIFF (16-bit)` *(numatyta)* | `tiff16` |
| `TIFF (8-bit)` | `tiff8` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer` priima `standard` (numatyta reikšmė) arba `texture-aware` („Chloros+“).

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
    indices=["NDVI", "OSAVI"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## Problemų sprendimas

### Po įdiegimo nerandama CLI

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### Atsisakyta suteikti leidimą

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend /usr/lib/chloros/backend/chloros-backend
```

### Backend nepaleidžiamas po failų teisių pridėjimo

Negalima paleisti `setcap` backend'e. Nuo 1.3.0 versijos paketas nenustato jokių failų galimybių, o PTP prievadai 319/320 nustatomi pagal `sysctl` parametrą, aprašytą skyriuje [Kaip PTP gauna savo prievadus](#always-on-ptp-for-headless-hosts). Failo galimybė perkelia išpakuotą užkurtį į saugaus vykdymo režimą, kuriame jis negali rasti savo bibliotekų, todėl nepaleidžiamas. Jei pridėjote tokią galimybę, pašalinkite ją:

```bash
sudo setcap -r /usr/lib/chloros/backend/chloros-backend
```

### PTP nepaleidžiamas / negali prisijungti prie 319 prievado

Patikrinkite, ar buvo sumažintas neprivilegijuotų prievadų ribinis skaičius, ir, jei ne, vėl jį pritaikykite dabartiniam paleidimui:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Tada patikrinkite „grandmaster“:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### `lattice` komandos baigiasi nesėkme su pranešimu „LATTICE kameros tvarkyklės nerastos“

Backend negalėjo įkelti „LATTICE“ kameros vykdymo aplinkos, su kuria ji buvo įdiegta. Visos „`lattice`“ komandos vykdomos backend’e; pati CLI neturi jokių kameros tvarkyklių. Iš naujo įdiekite Chloros (`sudo apt install --reinstall ./chloros_1.3.0_<arch>.deb`): `.deb` įdiegia vykdymo aplinką šalia užkulisio, į `/usr/lib/chloros/arena_runtime`, ir užregistruoja ją dinaminio įkėlimo programoje. Norėdami patikrinti tą registraciją:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
ls /usr/lib/chloros/arena_runtime | head
```

### Nepavyko paleisti užpakalinės dalies

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

Failo „`~/.cache/chloros/logs/`“ yra užpakalinės dalies žurnalai, susiję su nepavykusiu paleidimu. Jei 5000 uostas priklauso užpakalinės dalies paslaugai (`systemctl status chloros-backend@$USER.service`), paleiskite paslaugą iš naujo, o ne nutraukite jos procesą.

### CUDA neaptikta

```bash
# Check NVIDIA driver installation (x86_64)
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check the L4T release (JetPack 6 is R36) and that JetPack's CUDA libraries are installed
cat /etc/nv_tegra_release
sudo apt install nvidia-jetpack
```

`chloros-cli selftest` tą patį praneša vienoje eilutėje: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Trūksta bendrųjų bibliotekų

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries (the program itself, not the launcher script)
ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"
```

### Lėtas paleidimas sistemose su SD kortelėmis

Nuo 1.3.0 versijos užkulisinė paslauga ir CLI yra įdiegiamos neišpakuotos, todėl jos nebėra išpakuojamos į laikiną katalogą kiekvieno paleidimo metu. Laikinieji failai automatiškai perkeliami į `/mnt/ssd/tmp`, jei tas katalogas egzistuoja; priešingu atveju galite nurodyti, kad jie būtų saugomi greitoje failų sistemoje:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Chloros atnaujinimas Linux

Komanda „`update`“ skirta tik Linux/Jetson. Ji patikrina versiją, paskelbtą atnaujinimų kanale, sukonfigūruotame „`/etc/chloros/update.conf`“, ir siūlo atsisiųsti bei įdiegti atitinkamą „`.deb`“:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux/„Jetson“ sistemoje CLI taip pat kiekvieną kartą paleidžiant atlieka neblokuojantį atnaujinimų patikrinimą (rezultatas saugomas vieną valandą `~/.chloros/update_cache.json`) ir, jei yra naujesnė versija, išveda pranešimą `Update available: vX.Y.Z`. Jūsų nustatymai, projektai ir prisijungimo duomenys išlieka po atnaujinimo.

Atnaujinant paketą, visos foninės paslaugos sustabdomos diegimo metu, todėl veikiantis skriptas praranda prieigą prie kamerų ir jutiklių, o po to vėl paleidžia kiekvieną iš jų, kuri yra įjungta (ta, kuri buvo paleista rankiniu būdu, bet niekada nebuvo įjungta, lieka sustabdyta, kaip ir po perkrovimo).

## Pašalinimas

```bash
sudo apt remove chloros
```

Pašalinimas sustabdo ir išjungia visas fonines paslaugas, atkuria numatytąjį neprivilegijuotų prievadų ribą (1024), pašalina priėmimo buferio nustatymų failą (veikiančios reikšmės išlieka iki perkrovimo), „bundled-exiftool“ simbolinę nuorodą, „Arena“ įkėlėjo konfigūraciją, paketo pridėtas ugniasienės taisykles ir jo įdiegtą Python SDK, taip pat išvalo talpyklos prisijungimo duomenis. Jūsų projektai ir `~/.chloros/` duomenų failai lieka nepaliesti. `sudo apt purge chloros` taip pat ištrina `/var/log/chloros`.

***

## Tolimesni veiksmai

* [„NVIDIA Jetson“ vadovas](nvidia-jetson-guide.md) — „Jetson“ specifinė optimizacija ir diegimas
* [CLI: Komandinė eilutė](../CLI.md) — CLI vadovas
* [API: Python SDK](../api-python-sdk.md) — SDK vadovas
* [CLI nuoroda](../reference/cli-reference.md) ir [SDK nuoroda](../reference/sdk-reference.md) — išsamūs komandų ir API sąrašai versijai 1.3.0
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — kaip Chloros prisitaiko prie jūsų aparatinės įrangos
