# CLI: Komandinė eilutė

> **Išsami nuoroda:**[CLI nuoroda](reference/cli-reference.md) aprašo**visus kiekvienos pakomandos parametrus** ir yra pritaikyta dirbtinio intelekto asistentams — įklijuokite jos URL-ą į savo asistentą ir paprašykite veikiančios komandos: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **Patarimas dirbant su AI įrankiais:** bet kurį šio vadovo puslapį galima peržiūrėti kaip neapdorotą „Markdown“ failą, prie jo URLo pridėjus `.md` (pvz., `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), o „`https://mapir.gitbook.io/chloros/llms.txt`“ indeksuoja visą vadovą, kad jį galėtų naudoti didelio kalbos modelio (LLM) sistemos.

> **Norite, kad jūsų asistentas pats paleistų Chloros?** „`chloros-mcp`“ – tai MCP serveris, įdiegtas su Python SDK, kuris veikia su „Claude Desktop“, „Claude Code“, „Cursor“ ir kitais MCP klientais: žr. [Chloros naudojimas su AI asistentais](ai-assistants.md).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.3.0 build so the version line reads "Chloros CLI 1.3.0" -->


## Kas yra CLI


`chloros-cli` yra komandinės eilutės sąsaja su tuo pačiu apdorojimo varikliu, kurį naudoja Chloros darbalaukio programa. Tai yra HTTP klientas, veikiantis per Chloros užkurtį (vietinį serverį „`127.0.0.1:5000`“) — dauguma komandų užkurtį paleidžia automatiškai, todėl scenarijui pakanka vieno „`chloros-cli process …`“ iškvietimo. Tada užkulisiai toliau veikia, laukdami kitos komandos: jis sustos, kai bus įvykdytas „`chloros-cli logout`“ (nebent programa būtų atidaryta arba vyktų vykdymas ar kompiliavimas), kai bus įvykdytas „`chloros-cli backend stop`“, arba savaime po 30 minučių, jei nebus kliento, vykdymo ar prijungto įrenginio. Tai rodo komanda „`chloros-cli backend status`“.

Ji veikia **Windows 10/11 (x64)**ir**Linux (x86_64 bei „NVIDIA Jetson“ arm64 su „JetPack 6“)** sistemose, bet kuriame terminale, nereikalaujant grafinės vartotojo sąsajos. Patikrinkite įdiegimą naudodami:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

Komandų grupės iš pirmo žvilgsnio:

* **Apdorojimas ir paskyra** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 kalbos — žr. [Palaikomos kalbos](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (tik Linux / „Jetson“)
* **Veikianti įranga** — `lattice` (LATTICE kameros valdymas, daugiau nei 45 papildomų komandų), `daq pool-*` (DAQ šviesos jutikliai), `time-sync` (PTP)
* **Automatizavimas** — `project` (išsaugoto Chloros projekto vykdymas be grafinės sąsajos, įskaitant YAML duomenų surinkimo receptus)

Vertos žinoti globalios parinktys: `--port N` (užpakalinės dalies prievadas, numatytasis `5000`), `-v/--verbose`, `--restart` (priverstinis užpakalinės dalies paleidimas iš naujo), `--backend-exe PATH`. Visą sąrašą rasite [CLI žinyne](reference/cli-reference.md).

***

## Įdiegimas

CLI **yra įtraukta į Chloros diegimo programą** visose platformose — atskirai CLI atsisiųsti negalima. Diegimo programą galite atsisiųsti iš [Atsisiuntimo](download.md) puslapio.

### Windows


Diegimo programa įdiegia CLI į:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

ir vieną kartą prideda tą aplanką prie jūsų sistemos `PATH` (atnaujinimas taip pat pašalina ankstesnių diegimo programų paliktus dubliuojamus įrašus) — po įdiegimo **atidarykite naują terminalą**, kad būtų aptiktas atnaujintas `PATH`. Įdiegimo programa taip pat įdeda paleidimo scenarijus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) į įdiegimo šakninį katalogą bei**Chloros CLI** nuorodą „Pradžios“ meniu, kurios kiekviena atidaro terminalą su parengtu naudoti „`chloros-cli`“.

### Linux


Įdiekite savo architektūrai skirtą „`.deb`“ naudodami „`apt`“ (o ne „`dpkg -i`“, kuris praleidžia Python SDK rekomenduojamus reikalavimus):

```bash
# Linux x86_64
sudo apt install ./chloros_1.3.0_amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
```

Tai įdiegia `chloros-cli` į `/usr/bin/chloros-cli` (jau yra `PATH`), o užkulisinė programa ir CLI-programos išpakuojamos į `/usr/lib/chloros/backend/` ir `/usr/lib/chloros/cli/` (`/usr/lib/chloros/chloros-backend` yra paleidimo programa, kuri paleidžia foninę programą), „Arena SDK “ vykdymo aplinka, reikalinga foninei programai „LATTICE“ kameroms, ir „Python SDK “ sistemai `python3`. Išsamią informaciją rasite skyriuje [Linux diegimas](linux/linux-installation.md).

### Patikrinkite

```bash
chloros-cli --version    # "Chloros CLI 1.3.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Prisijungimas ir licencijavimas

Prieigai prie CLI (ir Python SDK) reikalingas **mokamas „Chloros+“ planas**— jį turi bet kuris mokamas lygis; nemokamas lygis jo neturi. Ribos taikomos**serverio pusėje** per užkurtį, o ne per CLI vykdomąjį failą: prisijungusio vartotojo skambutis atmetamas su kodu `401 AUTH_REQUIRED`, o prisijungusio vartotojo užklausa nemokamame plane atmetama su kodu `403 PLAN_UPGRADE_REQUIRED`, nepriklausomai nuo to, ar ji siunčiama iš `chloros-cli`, SDK ar savarankiškai sukurto HTTP kliento. Atnaujinkite [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).

Prisijunkite **vieną kartą iš kiekvieno kompiuterio**:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.3.0 build showing the license tier line -->


{% hint style="warning" %}
**Slaptažodžiai su specialiais simboliais**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` yra iškraipomas komandų eilutės aplinkos (CLI, tai aptinka gavus 401 klaidą ir automatiškai bando iš naujo, tačiau naudojant viengubas kabutes šios problemos galima visiškai išvengti).
{% endhint %}

Sesija išsaugoma `~/.chloros/user_session.json` ir toliau veikia neprisijungus per plano atidėjimo laikotarpį (30 dienų mėnesiniams planams, iki galiojimo pabaigos – metiniams). `chloros-cli status` veikia net ir be mokamo plano, todėl atmetimo priežastis visada matoma.

{% hint style="danger" %}
**Planuojate vykdyti užduotis be vartotojo sąsajos? Pirmiausia prisijunkite.**Komandos, paleidžiančios foninius procesus (`process`, `status`, `export-status`, …) vykdoma**be išsaugotos sesijos**nesibaigia greitai — ji pereina į interaktyvią `Email:` / `Password:` eilutę stdin. Todėl automatinė „cron“ užduotis arba CI etapas**užstrigs laukdamas įvesties**. Prieš planuojant bet kokius veiksmus, kompiuteryje vieną kartą paleiskite komandą `chloros-cli login EMAIL 'PASSWORD'`.
{% endhint %}

***

## Pirmasis apdorojimo paleidimas

Nukreipkite „`process`“ į užfiksuotų duomenų aplanką — programa automatiškai aptiks Survey3 („`.raw`“ + „`.jpg`“), „LATTICE“ (`.tif`/`.tiff`), „`.dng`“ arba jų derinį:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

Vykdymo eiga transliuojama tiesiogiai pagal kiekvieną procesų srautą (aptikimas, analizė, apdorojimas, eksportavimas), o sėkmingas vykdymas baigiamas pranešimu, kiek vaizdų produktų buvo įrašyta: `Image products written: N`, po kurio eina `(plus M clamp masks)`, jei vykdymo metu taip pat buvo įrašytos [fiksavimo kaukės](output-image-formats.md#clamp-masks) (jos neskaičiuojamos kaip produktai).

<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N (plus M clamp masks)" summary line -->


Jei Chloros užkulisinė programa jau veikia (pavyzdžiui, darbalaukio programos), „`process`“ ją naudoja ir joje atidaro savo projektą. Ji atsisako veikti, kol programa yra atidaryta projekte, kurį atidarė pati programa: vykdykite procesą iš programos arba uždarykite tą projektą. Jei ankstesnis vykdymas dar nebaigtas, prieš atmetant laukiama iki 15 sekundžių. „`--restart`“ priverčia paleisti naują backendą. Linux sistemoje „systemd“ paslauga paleistas foninis procesas niekada nėra paleidžiamas iš naujo: `process` naudoja jį tokį, koks yra, o tais atvejais, kai kitaip jį paleistų iš naujo (`--restart` arba importas, kuris nieko nerado), jis išveda pranešimą `sudo systemctl restart <unit>` ir užsidaro `1`.

### Kur patenka išvesties duomenys

`process` rašo į **projekto aplanką**, o ne į jūsų įvesties aplanką:

* Jei nenurodyta `-o`: projektas sukuriama jūsų numatytame projektų aplanke (bendrai naudojamame su GUI; jį tvarkykite naudodami `get-project-folder` / `set-project-folder`; jei nenustatyta, `Documents/MAPIR/Chloros Projects` jūsų vartotojo aplanke), pavadintas pagal `-n/--project-name` arba laiko žymą (`YYYYMMDD_HHMMSS`), jei nenurodyta.
* Naudojant `-o PATH`: tas aplankas **yra** projekto aplankas. Jei jame jau yra `project.json`, vietoj jo perrašymo sukuriama `_1`/`_2`… su priesaga, o ne perrašomas. Santykinis `PATH` nustatomas atsižvelgiant į aplanką, iš kurio paleidžiate `chloros-cli`.

{% hint style="info" %}
**Santykiniai keliai (pakeista versijoje 1.3.0).** Kiekvienas kelias, kurį atidaro užkulisinė programa — `-o`/`--output` bet kokioje komandoje (įskaitant numatytąją `output` fiksavimo komandų), `--input`, `--burst-dir`, `--profile`, `--save` ir `--mask` — nustatomas pagal aplanką, iš kurio paleidžiate „`chloros-cli`“, taigi „`-o captures/`“ rašo į „`<current folder>/captures/`“. Versijoje 1.2.0 užkulisinė sistema juos nustatydavo savo įdiegimo aplanke, dėl ko kildavo klaida „Prieiga uždrausta“. Naudojant nuotolinę užkulisinę sistemą (`CHLOROS_BACKEND_URL`), keliai siunčiami taip, kaip įvesti, ir nustatomi toje mašinoje.
{% endhint %}

{% hint style="warning" %}
**Windows: naudokite trumpą projekto aplanką, nebent būtų įjungti ilgi keliai.** Kai Windows ilgų kelių funkcija išjungta (tai yra Windows numatytasis nustatymas), joks failo kelias negali būti ilgesnis nei 260 simbolių, o produktai turi būti ne daugiau kaip apie 130 simbolių žemiau projekto aplanko. `process` tai patikrina prieš paleidimą: per gilus projekto aplankas atmetamas su pranešimu **„Projekto aplankas yra per gilus šiam kompiuteriui“**, nurodant ilgiausią tinkamą projekto aplanko pavadinimą ir kaip įjungti ilgus kelius — nieko neapdorojama. Naudokite trumpesnį „`-o`“ (arba „`set-project-folder`“) arba įjunkite Windows ilgus kelius (diegimo programos parinktis **„Įjungti Windows ilgus kelius“**, pažymėta pagal numatytuosius nustatymus) ir iš naujo paleiskite Chloros.
{% endhint %}

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

„LATTICE“ kameros aplankas yra „`LATT-<sensor>-<lens>-F<filter>`“ (atitinkantis nuotraukos EXIF duomenis „`Model`“) ir „`<model>_<filter>`“ (pvz., `Survey3N_RGN`) – Survey3. Formato aplankai seka taip: `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` arba `tiff32`, skirtas `TIFF (32-bit, Percent)`.

Daugiaspektrinės M3C kameros atveju formato aplanke, šalia produktų aplankų, taip pat gali atsirasti aplankas „`clamp_masks/`“ (pavyzdžiui, „`tiff16/clamp_masks/Reflectance_Calibrated_Images/`“ ir „`tiff32/clamp_masks/Radiance_Images/`“). Jame saugomos spinduliavimo ir atspindžio produktų kaukės pagal kiekvieną pikselį, kuriose juostos atskyrimo procesas neigiamą vertę apribojo iki nulio — žr. [Apribojimo kaukės](output-image-formats.md#clamp-masks). Joks produkto aplankas niekada neapima kaukės.

{% hint style="info" %}
**Kiekvienas eksportuotas produktas išlaiko ŠALTINIO failo pavadinimą.**`capture_..._raw.tif` spinduliavimo eksportas vis dar vadinasi `capture_..._raw.tif` — jis tiesiog yra `tiff32/Radiance_Images/` kataloge.**Aplankas identifikuoja produktą, o ne failo pavadinimą**, todėl naudokite globą katalogui, o ne priesufiksui „`*radiance*`“.
{% endhint %}

### Parinktys, kurias iš tikrųjų naudosite

| Parinktis | Numatytasis | Ką daro |
| --- | --- | --- |
| `-o, --output PATH` | numatytasis projekto aplankas | Projekto aplanko vieta (žr. aukščiau). |
| `-n, --project-name NAME` | laiko žyma | Projekto pavadinimas. |
| `--format FMT` | `TIFF (16-bit)` | Vienas iš `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros nepadidina skiriamosios gebos: visuose 8 bitų vaizduose gilesnis formatas įrašomas kaip „`TIFF (8-bit)`“, ir tai nurodoma vieną kartą. |
| `--indices NAME [NAME ...]` | nėra | Eksportuotini augmenijos indeksai (žr. [Augmenijos indeksai](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = neuroninis debayeris, lėtesnis, aukščiausia kokybė („Chloros+“, reikalingas CUDA GPU: jei jo nėra, naudojamas `standard`; neleidžiama naudoti su „LATTICE“ įrašais, kuriems visada taikomas `standard`). |
| `--vignette / --no-vignette` | įjungta | Vignette korekcija. |
| `--reflectance / --no-reflectance` | įjungta | Atspindžio kalibravimas; „LATTICE“ atveju tai taip pat yra atspindžio produkto įjungimo/išjungimo jungiklis. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Priversti naudoti „LATTICE“ TIFF failų apdorojimo grandinės įėjimo tašką. |

Dėl visko kito — taikinio aptikimo nustatymų, PPK, ekspozicijos žymių, masyvo suderinimo žymių — žr. [CLI žinyno skyrių „`process`“](reference/cli-reference.md).

***

## Eksportuojamų duomenų pasirinkimas (LATTICE produktai)

LATTICE apdorojimas vienu praėjimu išskirstomas į **visus taikytinus produktus**. Keturi kiekvienam produktui skirti perjungikliai**pagal numatytuosius nustatymus yra įjungti**; naudokite formą `--no-`, jei norite išjungti vieną iš jų:

| Jungiklis | Produktas |
| --- | --- |
| `--debayered` | Linijinis demozėjimas → `Debayered_Images/`: pašalinamas kalibravimo paketo tamsos poslinkis, tada atliekamas kraštų atpažinimu pagrįstas demozėjimas be linijiškumo korekcijos, plokščio lauko ar atskyrimo — tas pats failas, kurį įrašymo programa išsaugo savo `debayered` lygyje. Išsaugoma jutiklio natūrali orientacija (įrašyta XMP). |
| `--preview` | Peržiūros rodymas (baltos spalvos balansas + gama; dirbtinių spalvų išplėtimas multispektriniams vaizdams) → `Preview_Images/` |
| `--radiance` | „float32“ spinduliavimas, W/m²/sr/nm → `Radiance_Images/` (visada `tiff32/`) |
| `--reflectance` | uint16 atspindžio koeficientas, suderinamas su „Pix4D“ → `Reflectance_Calibrated_Images/` |

Tik RGB-pagrindinės kameros visada perduoda debayeringą + peržiūrą — spinduliavimas/atspindžio koeficientas pagal juostą nėra reikšmingas plačiajuosčio dažnio jutikliui, todėl šie perjungikliai jiems neveikia. Survey3 `.raw` ignoruoja perjungiklius ir laikosi standartinio atspindžio koeficiento/tikslinio kelio.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (numatyta reikšmė `auto`) pasirenka atspindžio etaloną: `auto` nustato kokybės užtikrinimo (QA) reikalavimus atitinkantį kadre esantį [kalibravimo taikinį](calibration-targets.md) kaip absoliučią atskaitą ir, kai nėra taikinio, grįžta prie DAQ šviesos jutiklio žemyn nukreipto srauto padalijimo (ρ = π·L/E), kai nėra jokio taikinio (taikinys, kuris atitinka kokybės užtikrinimo reikalavimus su žymekliu, konkuruoja su DAQ pagal išmatuotą rezultatą); `target` veikia griežtai (be DAQ pakeitimo); `daq` remiasi DAQ duomenimis. Vienetinius išmatuotus taikinio skenus galima pateikti naudojant `--target-reflectance-dir`.

{% hint style="info" %}
**Atspindžio pikselių skaitymas:**DN reikšmė ρ = 1,0 yra**kiekvienam šaltiniui** — LATTICE failai XMP žymėje įrašo `Chloros:PixelScale=32768`; Survey3 failai naudoja 65535 ir neturi žymės „`Chloros:PixelScale`“ (tačiau juose yra kitų apdorojimo žymių, pvz., „`Chloros:*`“). Skaitykite žymą ir dalykite iš jos, o ne laikykite ją pastovia. Išsamiau apie tai ir apie vieną sąmoningai numatytą kraštutinį atvejį be mastelio rasite [CLI žinyne](reference/cli-reference.md).
{% endhint %}

**Apdorojimas visada prasideda nuo `raw`.** Išvestiniai produktai (eksportuoti be bayerio, spinduliavimo ar atspindžio duomenys) niekada nėra grąžinami atgal į apdorojimo grandinę — jų pakartotinis importavimas ir apdorojimas dvigubai pritaikytų kalibravimo skaičiavimus, todėl Chloros juos praleidžia ir apie tai informuoja. `--input-level` yra specialiai numatytas atsarginis variantas, kai tikrai reikia priverstinai nustatyti įėjimo tašką.

***

## Kai apdorojimas nepavyksta

Nuo 1.2.0 versijos `process` aiškiai rodo nesėkmę, o ne „sėkmingai“ baigia, nieko neparodydamas:

* Vykdymas, kuris **paprašė produktų, bet nė vieno neįrašė**— tik `project.json` ir `calibration_data.json` — išspausdina `Processing finished but wrote no image products.` ir**baigiasi nelygiu nuliui rezultatu**, todėl skriptai gali tai aptikti. Įprastos priežastys: įvesties aplankas nebuvo atpažintas kaip užfiksuotas vaizdas (patikrinkite išdėstymą ir `--input-level`), arba kiekvienas prašytas produktas buvo netaikytinas toms kameroms (pvz., prašoma spinduliavimo/atspindžio iš kamerų, turinčių tik RGB).
* **Sąmoningas vykdymas tik su metaduomenimis** (visi produktai išjungti, be `--indices`) vis tiek laikomas sėkmingu — tuščias vaizdo išvesties rezultatas šiuo atveju yra teisingas.
* Vykdykite procesą iš naujo su `--verbose` ir patikrinkite vidinio modulio žurnalą, ieškodami eilučių `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`, kurios paaiškina, kodėl buvo praleistos tam tikros kameros.
* Windows sistemoje, kai išjungti ilgi keliai, projektų aplankas, kurio gylis viršija 260 simbolių kelio ribą, atmetamas **prieš** pradedant apdorojimą, o naudojamas ilgiausias tinkamas projektų aplankas (žr. [Kur patenka išvesties failai](#where-the-outputs-land)).
* Įrašymo komandos (`lattice capture`, `lattice array-capture` ir kitos įrašymo komandų grupės) nurodo kiekvieną failą, kurio nepavyko įrašyti, ir baigia veikimą su kodu `1`; pakartotiniai įrašymai sustabdomi, kai diskas užpildomas.

Išeitimo kodai: `0` – sėkmė · `1` – bendro pobūdžio klaida · `2` – argumento klaida · `130` – nutraukta paspaudus Ctrl+C.

***

## Augmenijos indeksai

Vykdykite komandą `--indices` su vienu ar keliais iš anksto nustatytais pavadinimais; kiekvienas indeksas bus išsaugotas atskiroje `<INDEX>_Index_Images/` aplankoje:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI GNDVI OSAVI
```

22 iš anksto nustatyti pavadinimai, kuriuos priima `process --indices`:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

Į projektą įtraukiamas pavadinimas, kurio jame dar nėra. Pavadinimas, kuris nėra iš anksto nustatytas arba kurio kameros negali sukurti, praleidžiamas, o vartotojui parodomas įspėjimas su jo pavadinimu. Jei CLI’ negali perskaityti dabartinio projekto indeksų sąrašo, ji palieka sąrašą nepakeistą ir rodo įspėjimą, o ne pakeičia jį.

{% hint style="warning" %}
**Yra trys indeksų sąrašai — jų nesupainiokite.**GUI meniu „Projekto nustatymai“ išskleidžiamajame sąraše yra 27 formulės (pridedami `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — šios penkios formulės skirtos tik GUI ir**netaikomos** `--indices`). Komanda „live/offline“ `lattice index --preset` naudoja savo atskirą 22 išankstinių nustatymų sąrašą. Formulės ir juostų skaičiavimai aprašyti [Daugiaspektrinių indeksų formulėse](project-settings/multispectral-index-formulas.md).
{% endhint %}

***

## DAQ šviesos jutikliai: trumpas apžvalga

`daq pool-*` šeima valdo MAPIR DAQ spektrinius jutiklius (DAQ-U per USB, DAQ-M per BLE, DAQ-E per Ethernet) per užkulisio nuolatinį rezervą — GUI, CLI ir SDK visi naudoja vieną tiesioginį identifikatorių. **`pool-*` yra palaikomas DAQ kelias pateiktoje CLI versijoje**; kitos `daq` pakomandos, į kurias galite pamatyti nuorodų, yra tik MAPIR vidinis šaltinis, o išeinant iš jų rodomas aiškus klaidos pranešimas, nukreipiantis jus į `pool-*`.

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

`pool-record` be `--duration` veikia iki `pool-record --stop`; numatytoji išvesties katalogas yra `~/Documents/DAQ Live View/` **galinio modulio kompiuteryje**. Kondensatoriaus korekcijos profilis pasirenkamas prisijungimo metu (`--cap-id`, galinio serverio numatytasis – `sunshine_cosine`) ir gali būti keičiamas realiuoju laiku naudojant `pool-set-cap` — viršutinės ribos profiliai ir jutiklio kalibruotas diapazonas aprašyti šio vadovo skyriuose apie duomenų surinkimą (DAQ).

{% hint style="warning" %}
**„DAQ-E“ kompiuteryje su keliais tinklo adapteriais:** pirmasis „`pool-connect --eth`“ automatinis aptikimas po paleidimo gali nepavykti net ir esant veikiančiam jutikliui. „`--eth-host <ip-or-hostname>`“ yra patikimesnis variantas — naudokite jį, kai aptikimas neduoda rezultatų.
{% endhint %}

***

## „LATTICE“ kameros, PTP ir projektų automatizavimas

`lattice` šeima (daugiau nei 45 papildomų komandų) apima visą „LATTICE“ kamerų veikimą nuo pradžios iki pabaigos: aptikimą, pavienius kadrų įrašymus, nuolat sinchronizuotus masyvus su GUI „smart-prep“ prisijungimo srautu, tiesioginį peržiūrėjimą naršyklėje, suderinimą, indeksų skaičiavimus ir kompiuterio tinklo plokščių diagnostiką. Pavyzdys:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Be to: `chloros-cli time-sync` pateikia ataskaitą apie „PTP grandmaster“, kurį vykdo Chloros pagrindinis kompiuteris (LATTICE kameros ir DAQ-E jutikliai veikia kaip jo pavaldiniai, kad būtų užtikrinti įrenginių tarpusavio laiko žymos), o „`chloros-cli project`“ atidaro išsaugotą Chloros projektą ir valdo jo kameras, matricas bei jutiklius be grafinės sąsajos — įskaitant scenarijais pagrįstus YAML duomenų surinkimo receptus.

Šios trys šeimos („`lattice`“, `project`, `daq pool-*`) taip pat yra vienintelės, kurios palaiko `CHLOROS_BACKEND_URL` komandą, skirtą **nuotolinio** užpakalinio modulio valdymui; pagrindinės komandos visada skirtos vietiniam kompiuteriui.

Išsamūs naudojimo aprašymai pateikiami šio vadovo skyriuose, skirtuose „LATTICE“; visi parametrai nurodyti [CLI žinyne](reference/cli-reference.md).

***

## Problemų sprendimas: 5 dažniausios

| Simptomas | Sprendimas |
| --- | --- |
| `Login required` arba suplanuota užduotis įstrigo prie `Email:` eilutės | Vieną kartą paleiskite `chloros-cli login EMAIL 'PASSWORD'` šiame kompiuteryje — komandos be išsaugotos sesijos eilutės bus vykdomos interaktyviai, o ne iškart baigsis nesėkme. |
| `backend unreachable` | Paleiskite Chloros darbalaukio programą arba patys paleiskite foninę programą: `"C:\Program Files\Chloros\backend\chloros-backend.exe"` Windows sistemoje (`backend` aplankas jūsų diegimo aplanke; jos nėra `PATH`), `chloros-backend` Linux sistemoje. `chloros-cli backend status` rodo, ar kuri nors iš jų veikia; `lattice`/`project`/`daq pool-*` niekada jo nepaleidžia. Jei nukreipiate juos į nuotolinį backendą, patikrinkite `CHLOROS_BACKEND_URL`. |
| Masyvo prisijungimas užblokuotas: `FRAMES WILL DROP` / `Reduce ROI to enable` | Pagrindinio kompiuterio tinklo plokštės priėmimo žiedas atstatytas į numatytuosius nustatymus — pagrindinė priežastis, dėl kurios anksčiau veikusi įranga atsisako prisijungti, paprastai po tinklo plokštės tvarkyklės atnaujinimo. Paleiskite komandą `chloros-cli lattice network --fix` iš **privilegijuoto** terminalo (arba nustatykite `ReceiveBufferLen=256`, `PendingReceives=64`); žr. nuorodą *Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas*. |
| `daq` pakomanda baigia veikimą: „reikalingas pilnas DAQ paketas…“ | Naudokite komandas „`daq pool-*`“, kurios valdo tą patį jutiklį per užkurtį: prisijungimas, srautas, įrašymas, ribojimo parinktis, o „DAQ-E“ atveju – jo tinklo nustatymai, perkrovimas ir programinės įrangos atnaujinimai (arba „`chloros_sdk.connect_daq_sensor()`“ iš Python). |
| „Jetson“ prieš atidarydamas didelius aplankus rodo įspėjimą apie keitimąsi | Pridėkite failais pagrįstą keitimąsi — CLI rodo tikslias komandas „`fallocate`“ ir „`swapon`“, kurias reikia paleisti. |

***

## Pagalba

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Kiekvienas žymeklis, kiekviena pakomanda:** [CLI žinynas](reference/cli-reference.md)
* **Python atitikmuo:** [Python SDK](api-python-sdk.md) ir [SDK žinynas](reference/sdk-reference.md)
* **Pagalba:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
