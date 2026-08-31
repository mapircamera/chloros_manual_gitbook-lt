# „CLI“: Komandinė eilutė

> **Išsami nuoroda:**[CLI Nuoroda](reference/cli-reference.md) aprašo**visus kiekvienos pakomandos parametrus** ir yra pritaikyta dirbtinio intelekto asistentams — įklijuokite jos URL į savo asistentą ir paprašykite veikiančios komandos: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **Patarimas dirbant su AI įrankiais:** bet kurį šio vadovo puslapį galima gauti kaip neapdorotą „Markdown“ failą, pridėjus `.md` prie jo URL (pvz., `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), o „`https://mapir.gitbook.io/chloros/llms.txt`“ indeksuoja visą vadovą, kad jį galėtų apdoroti didelės kalbos modelis (LLM).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.2.0 build so the version line reads "Chloros CLI 1.2.0" -->


## Kas yra „CLI
“

„`chloros-cli`“ yra komandinės eilutės sąsaja su tuo pačiu apdorojimo varikliu, kurį naudoja „Chloros
“ darbalaukio programa. Tai yra „HTTP
“ plonas klientas, veikiantis per „Chloros
“ užkurtį (vietinį serverį, esantį `127.0.0.1:5000`) — dauguma komandų užkurtį paleidžia automatiškai, todėl scenarijui pakanka vieno `chloros-cli process …` iškvietimo.

Ji veikia **„Windows
“ 10/11 (x64)**ir**„Linux
“ (x86_64 bei „NVIDIA Jetson“ arm64 su „JetPack 6“)**, bet kuriame terminale, nereikalaujant grafinės vartotojo sąsajos. Patikrinkite įdiegimą taip:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```

Komandų grupės iš pirmo žvilgsnio:

* **Apdorojimas ir paskyra** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 kalbos — žr. [Palaikomos kalbos](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (tik „Linux
“ / „Jetson“)
* **Veikianti įranga** — `lattice` (LATTICE kameros valdymas, daugiau nei 45 pakomandos), `daq pool-*` (DAQ šviesos jutikliai), `time-sync` (PTP)
* **Automatizavimas** — `project` (išsaugoto „Chloros
“ projekto vykdymas be vartotojo sąsajos, įskaitant YAML fiksavimo receptus)

Vertos žinoti globalios parinktys: `--port N` (užpakalinio modulio prievadas, numatytasis `5000`), `-v/--verbose`, `--restart` (priverstinis užpakalinės dalies paleidimas iš naujo), `--backend-exe PATH`. Išsamų sąrašą rasite [CLI
nuorodoje](reference/cli-reference.md).

***

## Įdiegimas

„CLI
“ **yra įtrauktas į „Chloros
“ diegimo programą** visose platformose — atskiro „CLI
“ failo atsisiųsti negalima. Diegimo programą galite atsisiųsti iš [Atsisiuntimo](download.md) puslapio.

### „Windows
“

Diegimo programa įdiegia „CLI
“ į:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

ir prideda tą aplanką prie jūsų sistemos `PATH` — po įdiegimo **atidarykite naują terminalą**, kad būtų aptiktas atnaujintas `PATH`. Diegimo programa taip pat įdiegia paleidimo scenarijus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) į diegimo šakninį katalogą bei**Chloros
CLI
** nuorodą „Pradžios“ meniu, kurios kiekviena atidaro terminalą su parengtu naudoti `chloros-cli`.

###Linux


Įdiekite savo architektūrai skirtą `.deb`:

```bash
# Linux x86_64
sudo dpkg -i chloros-amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

Tai įdiegia „`chloros-cli`“ į „`/usr/bin/chloros-cli`“ (jau yra `PATH`) ir užkurtį į `/usr/lib/chloros/chloros-backend`, kartu su „Arena“SDK
vykdymo aplinka, reikalinga „LATTICE“ kameroms. Išsamią informaciją rasite [Linux
diegimo instrukcijoje](linux/linux-installation.md).

### Patikrinkite

```bash
chloros-cli --version    # "Chloros CLI 1.2.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Prisijungimas ir licencijavimas

CLI
(irPython
SDK
) prieigai reikalingas **mokamas „Chloros
+“ planas**— jį turi bet kuris mokamas paketas; nemokamas paketas jo neturi. Šis apribojimas taikomas**serverio pusėje** per užkulisinę sistemą, o ne per „CLI
“ programą: prisijungusio vartotojo skambutis atmetamas su kodu `401 AUTH_REQUIRED`, o prisijungusio vartotojo užklausa nemokamoje pakopoje atmetama su kodu `403 PLAN_UPGRADE_REQUIRED`, nepriklausomai nuo to, ar ji siunčiama iš `chloros-cli`,SDK
, ar savarankiškai sukurto „HTTP
“ kliento. Atnaujinkite adresu [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).

Prisijunkite **vieną kartą kiekviename kompiuteryje**:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.2.0 build showing the license tier line -->


{% hint style="warning" %}
**Slaptažodžiai su specialiais simboliais**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` yra iškraipomas apvalkalo (CLI
as tai aptinka pagal 401 klaidą ir automatiškai bando iš naujo, tačiau naudojant viengubas kabutes šios problemos visiškai išvengiama).
{% endhint %}

Sesija išsaugoma talpykloje kaip `~/.chloros/user_session.json` ir veikia neprisijungus prie interneto plano atidėjimo laikotarpiu (30 dienų mėnesiniams planams, iki galiojimo pabaigos metiniams). `chloros-cli status` veikia net ir be mokamo plano, todėl atmetimo priežastis visada matoma.

{% hint style="danger" %}
**Planuojate vykdyti „headless“ užduotis? Pirmiausia prisijunkite.**Komandos, paleidžiančios užkulisines užduotis (`process`, `status`, `export-status`, …) vykdymas**be išsaugotos sesijos**nesibaigia greitai — ji pereina į interaktyvią `Email:` / `Password:` eilutę stdin. Todėl automatinė „cron“ užduotis arba CI etapas**užstrigs laukdamas įvesties**. Prieš planuojant bet kokius veiksmus, kompiuteryje vieną kartą paleiskite komandą `chloros-cli login EMAIL 'PASSWORD'`.
{% endhint %}

***

## Pirmasis apdorojimo paleidimas

Nukreipkite „`process`“ į įrašų aplanką — programa automatiškai aptiks „Survey3
“ (`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng` arba jų derinį:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

Vykdymo eiga transliuojama tiesiogiai pagal kiekvieną proceso srautą (aptikimas, analizė, apdorojimas, eksportavimas), o sėkmingai užbaigtas vykdymas baigiamas pranešimu, kiek vaizdų produktų buvo įrašyta (`Image products written: N`).



<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N" summary line -->
### Kur patenka rezultatai

`process` įrašo į **projekto aplanką**, o ne į jūsų įvesties aplanką:

* Jei nenurodyta `-o`: projektas sukuriama jūsų numatytame projektų aplanke (bendrai naudojamame su GUI; jį tvarkykite naudodami `get-project-folder` / `set-project-folder`, atsarginis variantas – `~/Chloros Projects`), pavadintas pagal `-n/--project-name` arba laiko žymą (`YYYYMMDD_HHMMSS`), jei pavadinimas nenurodytas.
* Naudojant `-o PATH`: tas aplankas **yra** projekto aplankas. Jei jame jau yra `project.json`, vietoj perrašymo sukuriama seserinė aplankas su priesaga `_1`/`_2`…

Projekte produktai sugrupuoti **pagal kamerą, o po to – pagal failo formatą**:

```
<project>/
├── project.json
├── calibration_data.json
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

„LATTICE“ kameros aplankas yra „`LATT-<sensor>-<lens>-F<filter>`“ (atitinkantis įrašo EXIF „`Model`“) ir „`<model>_<filter>`“ (pvz., `Survey3N_RGN`) skirta „Survey3
“. Formato aplankas seka taip: `--format`; `tiff16`, `tiff8`, `png8`, `jpg8` arba `tiff32`, kai nurodytas `TIFF (32-bit, Percent)`.

{% hint style="info" %}
**Kiekvienas eksportuotas produktas išlaiko ŠALTINIO failo pavadinimą.**`capture_..._raw.tif` spinduliavimo eksportas vis dar vadinasi `capture_..._raw.tif` — jis tiesiog yra `tiff32/Radiance_Images/` aplanke.**Produktą identifikuoja aplankas, o ne failo pavadinimas**, todėl naudokite glob-sintaksę ieškant katalogo, o ne `*radiance*` priesagos.
{% endhint %}

### Parinktys, kurias iš tikrųjų naudosite

| Žymė | Numatytasis | Ką daro |
| --- | --- | --- |
| `-o, --output PATH` | numatytasis projekto aplankas | Projekto aplanko vieta (žr. aukščiau). |
| `-n, --project-name NAME` | laiko žyma | Projekto pavadinimas. |
| `--format FMT` | `TIFF (16-bit)` | Vienas iš `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | nėra | Eksportuotini augmenijos indeksai (žr. [Augmenijos indeksai](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = neuroninis debayeris, lėtesnis, aukščiausia kokybė (Chloros
ir naujesnė versija, NVIDIA GPU). |
| `--vignette / --no-vignette` | įjungta | Vignette korekcija. |
| `--reflectance / --no-reflectance` | įjungta | Atspindžio kalibravimas; „LATTICE“ atveju tai taip pat yra atspindžio produkto perjungiklis. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Priverstinis apdorojimo grandinės pradžios taško nustatymas „LATTICE“ TIFF failams. |

Dėl visko kito — taikinio aptikimo derinimo, PPK, ekspozicijos žymių, masyvo suderinimo žymių — žr. [„CLI
“ nuorodų skyriaus dalį „`process`“](reference/cli-reference.md).

***

## Eksportuojamų duomenų pasirinkimas (LATTICE produktai)

LATTICE apdorojimas vienu praėjimu išskirstomas į **visus taikytinus produktus**. Keturi kiekvienam produktui skirti perjungikliai**pagal numatytuosius nustatymus yra įjungti**; naudokite formą `--no-`, jei norite išjungti vieną iš jų:

| Perjungiklis | Produktas |
| --- | --- |
| `--debayered` | Linijinis demozėjimas → `Debayered_Images/` |
| `--preview` | Peržiūros rodymas (baltos spalvos balansas + gama; neteisingų spalvų ištempimas multispektriniams vaizdams) → `Preview_Images/` |
| `--radiance` | „float32“ spinduliavimas, W/m²/sr/nm → `Radiance_Images/` (visada `tiff32/`) |
| `--reflectance` | uint16 atspindžio koeficientas, pritaikytas „Pix4D“ → `Reflectance_Calibrated_Images/` |

RGB
pagrindinės kameros visada perduoda tik debayered + peržiūros duomenis — spinduliavimas/atspindys pagal juostas nėra reikšmingas plačiajuosčio dažnio jutikliui, todėl šie perjungikliai jiems neveikia.Survey3
`.raw` ignoruoja perjungiklius ir seka standartinį atspindžio/tikslo kelią.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (numatyta reikšmė `auto`) pasirenka atspindžio etaloną: `auto` sukuria kokybės patikrinimą atitinkantį kadre esantį [kalibravimo taikinį](calibration-targets.md) nustato absoliučią atskaitą ir, kai nėra taikinio, grįžta prie DAQ šviesos jutiklio žemyn nukreipto srauto padalijimo (ρ = π·L/E); `target` veikia griežtai (be DAQ pakeitimo); `daq` remiasi DAQ duomenimis. Vienetais išmatuoti taikinio skenavimo duomenys gali būti pateikiami naudojant `--target-reflectance-dir`.

{% hint style="info" %}
**Atspindžio pikselių skaitymas:**DN reikšmė ρ = 1,0 yra**vienam šaltiniui** — „LATTICE“ failai įrašo žymą „`Chloros:PixelScale=32768`“ į XMP; „Survey3
“ failai naudoja 65535 (ir neturi „`Chloros:*`“ žymių). Skaitykite žymą ir dalykite iš jos, o ne laikykite ją pastoviąja reikšme. Išsamiau apie tai ir apie vieną sąmoningai numatytą kraštutinį atvejį be mastelio rasite [CLI
nuorodoje](reference/cli-reference.md).
{% endhint %}

**Apdorojimas visada prasideda nuo `raw`.** Išvestiniai produktai (debayeringo, spinduliavimo ar atspindžio eksportai) niekada nėra grąžinami atgal į apdorojimo grandinę — jų pakartotinis importavimas ir apdorojimas dvigubai pritaikytų kalibravimo skaičiavimus, todėl „Chloros
“ juos praleidžia ir apie tai praneša. `--input-level` yra specialiai numatyta išeitis, kai tikrai reikia priverstinai nustatyti įėjimo tašką.

***

## Kai apdorojimas nepavyksta

Nuo 1.2.0 versijos `process` aiškiai rodo nesėkmę, o ne „sėkmingą“ vykdymą be jokių rezultatų:

* Vykdymas, kuris **paprašė produktų, bet nė vieno neįrašė**— tik `project.json` ir `calibration_data.json` — išveda `Processing finished but wrote no image products.` ir**baigiasi ne nuliniu rezultatu**, todėl skriptai gali tai aptikti. Įprastos priežastys: įvesties aplankas nebuvo atpažintas kaip įrašymas (patikrinkite išdėstymą ir „`--input-level`“), arba kiekvienas prašytas produktas buvo netaikytinas toms kameroms (pvz., prašoma spinduliavimo/atspindžio iš kamerų, turinčių tik „RGB
“).
* **Sąmoningas vykdymas tik su metaduomenimis** (visi produktai išjungti, be `--indices`) vis tiek laikomas sėkmingu — tuščias vaizdo išvesties rezultatas šiuo atveju yra teisingas.
* Pakartokite vykdymą su „`--verbose`“ ir patikrinkite vidinio modulio žurnale eilutes „`[LATTICE-EXPORT]`“ / „`[EXPORT-CHECK]`“, kuriose paaiškinami praleidimai pagal kameras.

Išeitimo kodai: `0` – sėkmė · `1` – bendro pobūdžio klaida · `2` – argumento klaida · `130` – nutraukta paspaudus Ctrl+C.

***

## Augmenijos indeksai

Paleiskite „`--indices`“ su vienu ar keliais iš anksto nustatytais pavadinimais; kiekvienas indeksas patenka į savo atskirą „`<INDEX>_Index_Images/`“ aplanką:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI NDRE GNDVI
```

22 iš anksto nustatyti pavadinimai, kuriuos priima `process --indices`:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

{% hint style="warning" %}
**Yra trys rodyklių sąrašai — jų nesupainiokite.**GUI meniu „Project Settings“ (Projekto nustatymai) išskleidžiamajame sąraše yra 27 formulės (pridedamos `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — šios penkios formulės skirtos tik GUI ir**netaikomos** `--indices`). Komanda „live/offline“ `lattice index --preset` naudoja savo atskirą 22 išankstinių nustatymų sąrašą. Formulės ir juostų skaičiavimai aprašyti [Daugiaspektrinių indeksų formulėse](project-settings/multispectral-index-formulas.md).
{% endhint %}

***

## DAQ šviesos jutikliai: trumpas apžvalga

`daq pool-*` šeima valdo „MAPIR
“ DAQ spektrinius jutiklius (DAQ-U per USB, DAQ-M per BLE, DAQ-E per Ethernet) per užkulisio nuolatinį rezervą — GUI, „CLI
“ ir „SDK
“ visi naudoja vieną tiesioginį identifikatorių. **`pool-*` yra palaikomas DAQ kelias pateiktame „CLI
“ pakete**; kitos `daq` pakomandos, į kurias galite pamatyti nuorodas, yra tik „MAPIR
“ vidinis šaltinis, o išeinant rodomas aiškus klaidos pranešimas, nukreipiantis jus į `pool-*`.

```bash
# 1. Open a pooled session (pick the line matching your sensor)
chloros-cli daq pool-connect                              # smart-detect
chloros-cli daq pool-connect --port COM3                  # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF      # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local   # DAQ-E by hostname (reliable)

# 2. List pooled sensors and their ids
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 3. Read the latest calibrated spectrum (W/m²/nm)
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 4. Record a calibrated .daq file for 60 s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 5. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

`pool-record` be `--duration` veikia iki `pool-record --stop`; numatytoji išvesties katalogas yra `~/Documents/DAQ Live View/` **užpakalinio serverio kompiuteryje**. Aplinkos korekcijos profilis pasirenkamas prisijungimo metu (`--cap-id`, galinio serverio numatytasis – `sunshine_cosine`) ir gali būti keičiamas realiuoju laiku naudojant `pool-set-cap` — viršutinės ribos profiliai ir jutiklio kalibruotas diapazonas aptariami šio vadovo skyriuose, skirtuose duomenų surinkimui (DAQ).

{% hint style="warning" %}
**„DAQ-E“ kompiuteryje su keliais tinklo plokštėmis:** pirmasis „`pool-connect --eth`“ automatinis aptikimas po paleidimo gali nepavykti net ir esant veikiančiam jutikliui. „`--eth-host <ip-or-hostname>`“ yra patikimesnis variantas — naudokite jį, kai aptikimas neduoda rezultatų.
{% endhint %}

***

## „LATTICE“ kameros, PTP ir projektų automatizavimas

`lattice` šeima (daugiau nei 45 pakomandų) apima visą „LATTICE“ kamerų veikimą nuo pradžios iki pabaigos: aptikimą, pavienius kadrų fiksavimus, nuolat sinchronizuotus masyvus su GUI „smart-prep“ prisijungimo srautu, tiesioginį peržiūrėjimą naršyklėje, suderinimą, indeksų skaičiavimus ir pagrindinio kompiuterio tinklo plokščių diagnostiką. Pavyzdys:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Be to: `chloros-cli time-sync` pateikia ataskaitą apie „PTP grandmaster“, kurį vykdo „Chloros
“ pagrindinis kompiuteris (LATTICE kameros ir DAQ-E jutikliai veikia kaip jo „slave“ įrenginiai, siekdami užtikrinti laiko žymes tarp įrenginių), o „`chloros-cli project`“ atidaro išsaugotą „Chloros
“ projektą ir valdo jo kameras, matricas bei jutiklius be grafinės sąsajos — įskaitant scenarijais pagrįstus YAML duomenų surinkimo receptus.

Šios trys šeimos (`lattice`, `project`, `daq pool-*`) taip pat yra vienintelės, kurios palaiko `CHLOROS_BACKEND_URL` komandą, skirtą valdyti **nuotolinį** užpakalinį procesorių; pagrindinės komandos visada skirtos vietiniam kompiuteriui.

Išsamūs aprašymai pateikiami šio vadovo skyriuose, skirtuose LATTICE; visi parametrai yra [CLI
nuorodoje](reference/cli-reference.md).

***

## Problemų sprendimas: 5 dažniausios

| Simptomas | Sprendimas |
| --- | --- |
| `Login required` arba suplanuota užduotis įstrigo prie `Email:` eilutės | Vieną kartą paleiskite `chloros-cli login EMAIL 'PASSWORD'` šiame kompiuteryje — komandos be išsaugotos sesijos bus vykdomos interaktyviai, o ne iškart baigsis klaida. |
| `backend unreachable` | Paleiskite „Chloros
“ darbalaukio programą arba tiesiogiai paleiskite foninę programą (`chloros-backend`). Jei nukreipiate `lattice`/`project`/`daq pool-*` į nuotolinį užpakalinį procesą, patikrinkite `CHLOROS_BACKEND_URL`. |
| Blokuotas prisijungimas prie masyvo: `FRAMES WILL DROP` / `Reduce ROI to enable` | Hostingo tinklo plokštės (NIC) priėmimo žiedo parametrai atkurti į numatytuosius — pagrindinė priežastis, dėl kurios anksčiau veikusi įranga atsisako prisijungti, paprastai po tinklo plokštės tvarkyklės atnaujinimo. Paleiskite komandą `chloros-cli lattice network --fix` iš **aukštesnių teisių** terminalo (arba nustatykite `ReceiveBufferLen=256`, `PendingReceives=64`); žr. vadovo skyrių *Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas*. |
| `daq` pakomanda baigia veikimą: „reikia pilno DAQ paketo…“ | Tai tikėtina išsiųstose versijose — kompiliuotas „CLI
“ pateikia tik `daq pool-*` šeimos komandas, kurios apima prisijungimą, srauto perdavimą, įrašymą ir kapiliaro pasirinkimą. Naudokite „`pool-*`“ (arba „`chloros_sdk.connect_daq_sensor()`“ iš „Python
“). |
| „Jetson“ prieš atidarant didelius aplankus rodo persijungimo įspėjimą | Pridėkite failais pagrįstą persijungimą — „CLI
“ pateikia tikslias „`fallocate`“ / „`swapon`“ komandas, kurias reikia paleisti. |

***

## Pagalba

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Kiekvienas žymeklis, kiekviena pakomanda:** [CLI
Nuoroda](reference/cli-reference.md)
* **„Python
“ atitikmuo:** [Python
SDK
](api-python-sdk.md) ir [SDK
Nuoroda](reference/sdk-reference.md)
* **Palaikymas:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
