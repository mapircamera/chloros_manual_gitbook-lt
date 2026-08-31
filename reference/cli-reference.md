# „Chloros“ „CLI“ žinynas

**Versija:**

1.2.0**Sukurta:**2026-07-29 19:19 ·**Atnaujinta:** 2026-08-30**Skaitytojai:** Optimizuota dideliems kalbos modeliams (LLM); suprantama žmonėms.**Apimtis:** Visos vartotojui skirtos `chloros-cli` pakomandos su parinktimis ir pavyzdžiais, kuriuos galima nukopijuoti ir įklijuoti.

Šis dokumentas yra išsamus `chloros-cli` komandinės eilutės įrankio, kuris pateikiamas kartu su „MAPIR“ Chloros, vadovas. Jis sąmoningai parengtas išsamiai, kad kad LLM (arba žmogus) galėtų sudaryti bet kurį palaikomą darbo srautą iš žemiau pateiktų sąrašų, neperžiūrėdamas šaltinio kodo.

Jei jums reikia tik svarbiausių dalykų, pereikite prie:
- [Penkių minučių greitasis pradžios vadovas](#five-minute-quickstart)
- [„LATTICE“ kameros pirmojo prisijungimo darbo eiga](#lattice-camera-first-connect-workflow)
- [DAQ jutiklio pirmojo prisijungimo darbo eiga](#daq-sensor-first-connect-workflow)
- [„Smart-AE“ / Smart-Capture](#smart-ae--smart-capture)
- [Įrašymo režimai, įrašymo įrenginiai ir neprisijungus atliekamas pakartotinis apdorojimas](#capture-modes-recorders--offline-reprocess)

---

## Konvencijos

- Visų komandų priešdėlis yra `chloros-cli`. „Windows“ sistemoje binarinis failas yra `chloros-cli.exe`; „Linux“ / „Jetson“ sistemose – `chloros-cli`.
- Pasirinktiniai argumentai nurodomi kaip „`--flag`“. Privalomi poziciniai argumentai nurodomi be skliaustelių.
- Jei nurodyta numatytoji reikšmė, nepateikus žymės, naudojama ta reikšmė.
- „CLI“ yra „HTTP“ plonas klientas, veikiantis per „Chloros“ užkurtį (Flask serveris, esantis `127.0.0.1:5000`). Užkurtis automatiškai paleidžiamas daugumos komandų. `CHLOROS_BACKEND_URL=<url>` nukreipia į **`lattice`**,**`project`**ir**`daq pool-*`** komandų šeimas nukreipia į nuotolinį backendą — pagrindines komandas (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) sąmoningai fiksuoja `http://127.0.0.1:<port>` ir ją ignoruoja (IPv4 literatas išvengia Windows&#x27; `localhost`→`::1` ~2 s baudą už kiekvieną užklausą). Žr. [Aplinkos kintamieji](#environment-variables).
- Visiems „SDK“ / „CLI“ iškvietimams reikalingas prisijungimas su „Chloros“ paskyra (vykdykite „`chloros-cli login`“ vieną kartą kiekviename kompiuteryje; įrašoma į talpyklą „`~/.chloros/`“).
- Pavyzdžiuose naudojami „Linux“ keliai; „Windows“ pakeiskite „`/home/user/...`“ į „`C:/Users/.../...`“.

---

## Aukščiausio lygio apžvalga

```
chloros-cli [global options] COMMAND [command options]
```

### Bendrosios parinktys

| Parinktis | Aprašymas |
| --- | --- |
| `--backend-exe PATH` | Perrašyti automatiškai aptiktą užkulisio vykdomąjį failą. |
| `--port N` | Užkulisio „HTTP“ prievadas (numatyta reikšmė: `5000`). |
| `-v, --verbose` | Įjungti išsamų išvesties režimą. |
| `--restart` | Priverstinis užpakalinės dalies paleidimas iš naujo (uždaro visus veikiančius `backend_server.py` procesus). |
| `--version` | Rodyti versiją (`Chloros CLI 1.2.0`). |
| `--help` | Rodyti aukščiausio lygio pagalbą. |

### Komandų rodyklė

| Komanda | Paskirtis |
| --- | --- |
| [`process`](#chloros-cli-process) | Apdoroti „Survey3“ arba „LATTICE“ užfiksuotų duomenų aplanką nuo pradžios iki pabaigos. |
| [`login`](#chloros-cli-login) | Autentifikuoti šį kompiuterį naudojant „Chloros“ paskyrą. |
| [`logout`](#chloros-cli-logout) | Išvalyti talpyklos duomenis. |
| [`status`](#chloros-cli-status) | Rodyti dabartinę licencijos / autentifikavimo būseną. |
| [`export-status`](#chloros-cli-export-status) | „Live Thread-4“ eksporto pažanga vykdant `process` komandą. |
| [`language`](#chloros-cli-language) | Nustatyti arba peržiūrėti „CLI“ rodymo kalbą (palaikomos 38 kalbos). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Numatytasis projekto aplankas (bendras su GUI). |
| [`update`](#chloros-cli-update) | Patikrinti ir įdiegti „CLI“ atnaujinimus (Linux /Jetson). |
| [`selftest`](#chloros-cli-selftest) | Sistemos diagnostika + greitieji testai. |
| [`time-sync`](#chloros-cli-time-sync) | PTP „grandmaster“ būsena / valdymas. |
| [`lattice`](#chloros-cli-lattice) | „LATTICE“ kameros valdymas ir vaizdo fiksavimas (daugiau nei 45 papildomos komandos). |
| [`daq`](#chloros-cli-daq) | DAQ spektrinių jutiklių valdymas (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Atidaryti ir paleisti išsaugotą „Chloros“ projektą (kameros + DAQ). |

---

## Įdiegimas

`chloros-cli` yra įtrauktas į „Chloros“ darbalaukio diegimo programą visose palaikomose platformose — atskiro „CLI“ failo atsisiųsti nereikia. Įdiegus platformos paketą, „`chloros-cli`“ bus pridėtas prie jūsų „`PATH`“ kartu su darbalaukio programa ir jos valdomu užkulisiniu vykdomuoju failu.

Naujausi atsisiuntimai: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Įdiegimo programa taip pat pateikia patogius paleidimo scenarijus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`), kurie atidaro paruoštą naudoti „CLI“ aplinką; jie aprašyti [„CLI“ vartotojo vadove](../CLI.md) ir čia nekartojami.

### „Windows“ (.exe)

1. Atsisiųskite „Windows“ diegimo programą iš atsisiuntimo puslapio.
2. Paleiskite `Chloros-Setup-x.y.z.exe` ir sekite vedlio nurodymus. Numatytasis diegimo kelias yra `C:\Program Files\Chloros\` („CLI“ įdiegiamas į katalogą „`C:\Program Files\Chloros\cli\`“, kurį diegimo programa įtraukia į PATH).
3. Atidarykite naują terminalą („`cmd.exe`“, „PowerShell“ arba „Windows“ terminalą), kad būtų pasirinktas atnaujintas „`PATH`“ .

```powershell
chloros-cli --version
```

Diegimo programa automatiškai įtraukia `chloros-cli.exe` į jūsų sistemos `PATH` aplinką ir prideda „Arena“ SDK vykdymo aplinką, reikalingą „LATTICE“ kameroms.

### „Linux“ amd64 (.deb)

Skirta „Ubuntu 22.04 LTS“ ar naujesnėms versijoms / „Debian“ pagrįstoms x86_64 darbo stotims.

> **„Ubuntu 20.04“ nepalaikoma.** Paketo priklausomybių sąrašas sudaromas remiantis
> tuo, su kuo iš tikrųjų susiejamas pagrindinis modulis, ir tai įskaitant `libc6 (>= 2.34)`;
> „focal“ pateikia „glibc 2.31“. `apt` atsisako įdiegti, užuot leidęs, kad įdiegimas žlugtų
> vykdymo metu.

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
```

.deb įdiegia:
- `chloros-cli` į `/usr/bin/chloros-cli`
- Sukompiliuotą backend į `/usr/lib/chloros/chloros-backend`
- „Arena“ „SDK“ vykdymo aplinką (skirtą „LATTICE“ kameroms)
- Triukšmo šalinimo modelius, kalibravimo rinkinius ir atnaujinimo kanalo konfigūraciją

### „Linux“ arm64 — „Jetson“ (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
```

Toks pat išdėstymas kaip ir „amd64 .deb“ faile, su „CUDA“ versija, pritaikyta „Jetson Orin“ / „Orin NX“ / „Orin Nano“.

### Autentiškumo patvirtinimas vieną kartą kiekviename įrenginyje

Kiekvienoje platformoje reikia vienkartinio prisijungimo prie Chloros+ prisijungimą, kad veiktų „SDK“ / „CLI“ iškvietimai:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Prisijungimo duomenys išsaugomi `~/.chloros/user_session.json`.

### Įdiegimo patikrinimas

```bash
chloros-cli --version           # prints "Chloros CLI 1.2.0"
chloros-cli selftest            # full 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

> **Reikalinga „Chloros“ prenumerata.**„CLI“ reikalauja aktyvaus „Chloros“ plano.**„Copper“**yra pradinis „Chloros“ lygis — kiekvienas mokamas „Chloros“ lygis turi prieigą prie „CLI“ / „SDK“; tik nemokamas**„Iron“** lygis jos neturi. (Plano-id žemėlapis: `0`=Iron/free, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) Atnaujinkite [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing).
>
> Šis apribojimas taikomas ne tik „CLI“, bet ir serverio pusėje: užklausos su žymėmis „SDK“ / „CLI“, pateiktos be apmokėto plano, atmetamos su kodu „`403 PLAN_UPGRADE_REQUIRED`“, nepriklausomai nuo to, ar jos siunčiamos iš „`chloros-cli`“, „Python“ SDK, arba iš rankomis sukurto „HTTP“ kliento. Atsijungęs skambintojas vietoj to gauna klaidos kodą `401 AUTH_REQUIRED`. Prieiga veikia neprisijungus plano atidėjimo laikotarpiu (30 dienų per mėnesį, iki metinio plano galiojimo pabaigos) ir nutraukiamas pasibaigus šiam laikotarpiui; „`chloros-cli status`“ toliau veikia, kad būtų matoma priežastis (tai vienintelis maršrutas SDK / CLI, kuriam netaikomas pakopų apribojimas — „`GET /api/license-status`“).

---

## Penkių minučių greitasis pradžios vadovas

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI NDRE GNDVI

# 3. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 4. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 5. Read a spectrum from a connected DAQ-U
chloros-cli daq pool-connect --port COM3
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

---

## `chloros-cli process`

Apdorokite vaizdų aplanką per visą „Chloros“ procesą (tikslo aptikimas → kalibravimas → vinjetė → atspindžio koeficientas → indekso eksportavimas).

### Apžvalga

```
chloros-cli process INPUT [OPTIONS]
```

### Poziciniai argumentai

| Argumentas | Aprašymas |
| --- | --- |
| `INPUT` | Kelias į įvesties aplanką, kuriame yra `.raw + .jpg` (Survey3), `.tif/.tiff` (LATTICE) arba `.dng` failus. |

### Bendrosios parinktys

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `-o, --output PATH` | naujas aplankas su laiko žyma pagal numatytąjį projekto kelią (`~/Chloros Projects`, jei nenustatyta kitaip) | Projekto aplankas, kurį reikia sukurti arba pakartotinai naudoti. Jei aplanke jau yra `project.json`, vietoj jo perrašymo bus sukurtas lygiavertis aplankas „`_1`“/„`_2`“. |
| `-n, --project-name NAME` | auto (laiko žyma) | Projekto pavadinimas. |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` naudoja „Chloros“ + neuroninį debayerį; lėtesnis, bet užtikrina aukštesnę kokybę. |
| `--vignette / --no-vignette` | `--vignette` | Vignette korekcija. |
| `--reflectance / --no-reflectance` | `--reflectance` | Atspindžio kalibravimas (naudojamas skydelio taikinys, jei rastas, NIST kalibravimas pagal serijos numerį „LATTICE“). „LATTICE“ multispektrinėms nuotraukoms tai taip pat veikia kaip atspindžio **sandaugos** perjungiklis — žr. [Per-produkto eksporto perjungikliai](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | išjungta | Taikyti PPK GNSS korekcijas iš „sidecar“ failų. |
| `--exposure-pin-1 MODEL` | išjungta | Fiksuoti „Survey3“ dviejų kamerų įrangos „pin-1“ modelį. |
| `--exposure-pin-2 MODEL` | išjungta | Fiksuoti „pin-2“ modelį. |
| `--recal-interval SECONDS` | 0 | Priversti pakartotinai vykdyti kalibravimo skaičiavimus kas N sekundžių nuo įrašymo pradžios. |
| `--timezone-offset HOURS` | vietinis | Pakeisti laiko juostos nuokrypį, įtrauktą į išvesties metaduomenis. |
| `--format FORMAT` | `TIFF (16-bit)` | Vienas iš `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | nėra | Augmenijos indeksai (`NDVI`, `NDRE`, `GNDVI`, `EVI`, `SAVI`, `OSAVI`, `CIG`, …). |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Priversti naudoti „LATTICE TIFF“ failų apdorojimo grandinės pradžios tašką (failams „Survey3.raw“ tai neturi įtakos). Taip pat išimtis, leidžianti apdoroti įrašus su **be „raw“** apdorojimą visiškai praleisti — žr. [Kaip atrodo įrašų aplankas](#kaip-atrodo-užfiksuotų-duomenų-aplankas). |
| `--debayered / --no-debayered` | įjungta | Išvesti linijinį debayeringo rezultatą (`Debayered_Images`). Žr. [Eksporto perjungikliai pagal produktą](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | įjungta | Išsiunčia ekrano peržiūrą (`Preview_Images`): „RGB“ = baltos spalvos balansas (DAQšviesos šaltinis, jei yra, kitaip – pilkosios aplinkos) + gama; multispec = netikrų spalvų ištempimas. |
| `--radiance / --no-radiance` | įjungta | Siųsti „float32“ spinduliavimą (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | LATTICE atspindžio produkto etalonas: `auto` = kokybės patikrinimą išlaikęs kadre esantis taikinys yra absoliutus etalonas, atsarginis variantas – DAQ žemyn nukreiptas spinduliavimas (ρ = π·L/E); `target` = griežtas (be DAQ pakeitimo); `daq` = DAQ yra autoritetingas. Žr. [Eksporto perjungikliai pagal produktą](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | nėra | Kiekvieno vieneto **išmatuotų** tikslo atspindžio skenų katalogas (`<serial>.csv`); nesėkmės atveju grįžtama prie nominalių T3/T4P spektrų. |
| `--array-alignment / --no-array-alignment` | įjungta | LATTICE masyvai: kiekvienam apdorotam produktui (debayeringas / peržiūra / spinduliavimo / atspindžio / indekso). Atvaizdams be žymių – neveikia. |
| `--array-alignment-crop / --no-array-alignment-crop` | apkarpymas | Apkarpykite suderintus eksportus iki masyvo bendrossutapimo sritį, kad visi moduliai užimtų tą patį plotą; `--no-…` išlaiko visą jutiklio plotą (juodas užpildymas už šaltinio ribų). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Perdiskretizavimas suderinimui. `nearest` išsaugo tikslius šaltinio DN (be radiometrinių verčių maišymo tarp pikselių). |

### Tikslo aptikimo parinktys

| Žymė | Aprašymas |
| --- | --- |
| `--min-target-size PIXELS` | Mažiausias detektoriaus plokštelės-tikslo dydis (px). |
| `--target-clustering 0-100` | Klasterizavimo jautrumas. |
| `--target / --targets` | Laikyti įvesties aplanką kaip skirtą tik tikslams (praleisti apžvalgos aptikimą). |

### Pavyzdžiai

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI NDRE GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a custom output location with a project name
chloros-cli process "C:/input" -o "C:/output" -n "Field_A_2026-05-26"
```

### Eksporto perjungikliai pagal produktą (LATTICE multispektrinis)

LATTICE apdorojimas vienu praėjimu išskirstomas į **visus taikytinus produktus**. Keturi perjungikliai pagal tipą — `--debayered`, `--preview`, `--radiance`, `--reflectance` — visi yra**pagal numatytuosius nustatymus įjungti**; naudokite formą `--no-<type>`, jei norite išjungti vieną iš jų. „RGB“ pagrindinės kameros visada pateikia tik debayeringo apdorotus duomenis ir peržiūrą (be spinduliavimo/atspindžio pagal juostas), todėl `--radiance`/`--reflectance` jiems neveikia. Perjungikliai ignoruojami „Survey3“ `.raw` (kuris seka standartinį atspindžio / tikslo kelią). *(Senasis `--radiometric-output {reflectance,radiance,sensor-response}` žymeklis buvo **pašalintas** ir pakeistas šiais perjungikliais; `sensor-response` lygio nebėra.)*

| Produktas | Išvestis | Reikalingas DAQ žemyn nukreiptas srautas? |
| --- | --- | --- |
| `--debayered` | Linijinis demozėjimas (`Debayered_Images`). | Ne. |
| `--preview` | Peržiūros rodymas (`Preview_Images`): „RGB“ = WB + gama; „multispec“ = netikrų spalvų ištempimas. | Ne. |
| `--radiance` | „float32“ W/m²/sr/nm iš pilnos radiometrinės grandinės (`Radiance_Images`). | Nr. |
| `--reflectance` | uint16 atspindžio koeficientas ρ (`32768` = 1,0), suderinamas su „Pix4D“. | **Taip**, nebent jį fiksuoja kokybės užtikrinimo reikalavimus atitinkantis kadre esantis taškas (žr. žemiau). |

`--reflectance-source` pasirenka atspindžio etaloną:**`auto`**(numatyta reikšmė) nustato kokybės užtikrinimo reikalavimus atitinkantį kadre esantį taikinį kaip**absoliutų etaloną**— taikiniu pririštos empirinių linijų grandinės yra kryžmiškai vertinamos pagal išsaugotusišimamose plokštėse, o matuotas laimėtojas pritaikomas — grįžtama prie DAQ žemyn nukreipto padalijimo (ρ = π·L/E), kai nėra tikslo arba QA nepavyksta;**`target`**yra griežtas (be DAQ pakeitimo);**`daq`**pasirenka DAQ autoritetingą elgesį. Tikslo geometrija (ArUco / fiksuotas ROI / juostelė) gaunama iš projekto taikinio konfigūracijos; `--target-reflectance-dir DIR` saugo kiekvieno vieneto**išmatuotus** skenavimus (`<serial>.csv`), surastus pagal tikslinio vieneto serijinį numerį / QR kodą, o kaip atsarginį variantą naudoja nominalius T3/T4P spektrus.

DAQ atspindžio kelias automatiškai nustato **laiko žymos atitikmenį turinčią žemyn nukreiptą spinduliuotę**iš įrašyto**`.daq`**(DAQ-U/M/E)**arba DAQ-M natyvią `.csv`**, rastą kartu su vaizdais. Jei kameros ar DAQ kalibravimo paketas nėra išsaugotas vietinėje talpykloje, apdorojimo grandinė**jį automatiškai atsisiunčia iš AWS** pirmą kartą naudojant (reikia vienkartinio prisijungimo prie interneto; išsaugoma kaip `~/.chloros/`).

#### Atspindžio pikselių skaitymas (Pix4D / Metashape / jūsų pačių skriptai)

Atspindžio koeficientas saugomas kaip sveikasis skaičius DN, o **DN, reiškiantis ρ = 1,0, priklauso nuo šaltinio kameros**:

| Šaltinis | ρ = 1,0 yra | Kaip nustatyti |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezervas iki ρ 2,0) | Failą pažymėta XMP `Chloros:PixelScale=32768`. |
| Survey3 | `65535` (apribota iki ρ 1,0) | Nėra `Chloros:*` XMP žymių — šis nebuvimas *ir yra* signalas. |

**Perskaitykite `Chloros:PixelScale` ir padalinkite iš jo** užduotų manyti, kad tai konstanta. Žymė apibrėžta „uint16“ srityje, todėl ji išlieka `32768` visose išvesties formatuose, kuriuose atliekamas perskalavimas — `TIFF (16-bit)`, `PNG (8-bit)`, `JPG (8-bit)` ir `TIFF (32-bit, Percent)` – visi jie yra savaime apibūdinami (pirmiausia normalizuokite saugomą duomenų tipą atgal į „uint16“: ×257 iš 8 bitų, ×65535 iš „float“).

> **Vienas atvejis pagal projektą neturi mastelio.** Kai 8 bitų šaltinio įrašas (BayerRG8) įrašomas kaip 8 bitų „TIFF“, apdorojimo grandinė *apriboja* reikšmes iki 0..255, o ne perskaičiuoja mastelį, todėl kiekviena reikšmė, didesnė už ρ≈0,008, išlyginama iki 255, o failą neapibūdina joks mastelis. „Chloros“ sąmoningai praleidžia tiek „`Chloros:PixelScale`“, tiek „`MicaSense:RadiometricCalibration`“ tuplą ir užregistruoja priežastį. **Jei LATTICE atspindžio faile nėra žymės, nedarykite prielaidos apie mastelį — eksportuokite iš naujo 16 bitų arba 32 bitų formatu**, o ne dalykite pikselius, kurių niekada nebuvo galima padalyti.

#### Į eksportą perkelti EXIF duomenys

`process` nukopijuoja šaltinio įrašo **GPS bloką ir jo ExifIFD** į kiekvieną produktą, todėl
eksportuojant perkeliami `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` ir
`CameraSerialNumber` kartu su georeferencijavimu.

**`FocalLength` nėra neprivalomas fotogrametrijai.** „Pix4D“ apskaičiuoja žemės mėginio atstumą pagal
fokalinį nuotolį ir aukštį; jei žymės nėra, programa naudoja labai netikslią skalę. Vieno
49 kadrų skrydžio virš apelsinų giraitės metu dėl trūkstamos žymos 411 m × 160 m plotas virto atkurtu
47,8 km × 13 km plotu – 455 MP ortofotografija, kurioje daugiausia buvo „nodata“ duomenų, o tai vėliau buvo traktuojama kaip mozaikos sudarymo problema ir
„BigTIFF“ problema, kol kas nors patikrino GSD. Jei jūsų ortofotografija gaunama neįtikėtinu
masteliu, pirmiausia paleiskite „`exiftool -FocalLength`“ ant eksportuoto produkto.

Kopija sąmoningai **nėra** „`-all:all`“: IFD0 struktūrinės žymos sugadina „LATTICE“ išvestį, kai
yra kopijuojamos, o „`ExifImageWidth`“ / „`ExifImageHeight`“ yra atmesti, nes jie apibūdina
*šaltinio* užfiksavimą — eksportas, kurio dydis kada nors buvo pakeistas, priešingu atveju turėtų matmenis
, prieštaraujančius pačiam rasteriui. XMP rašomas tiesiogiai, o ne kopijuojamas, nes „ExifTool“
atmeta tos pačios iškvietos XMP žymes, kai kopijuojamas XMP blokas (dėl to būtų prarastos „MAPIR“
kalibravimo žymos).

### Kur išsaugomi rezultatai

Produktai įrašomi **projekto aplanke, sugrupuoti pagal fotoaparatą, o po to – pagal failo formatą**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Fotoaparato aplankas yra „`LATT-<sensor>-<lens>-F<filter>`“ modelio „LATTICE“ atveju (atitinka nuotraukos EXIF
„`Model`“) ir „`<model>_<filter>`“ modelio „Survey3“ atveju — šie du fotoaparatai turi tą patį jutiklį ir filtrą, tačiau skiriasi
objektyvu, turi atskiras medžių struktūras, nes skiriasi vinjetavimas, matymo laukas ir iškraipymas. Formato
Aplankas tęsiasi taip: `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` arba `tiff32`, jei
`TIFF (32-bit, Percent)`.

> **Kiekvienas eksportuotas produktas išlaiko ŠALTINIO failo pavadinimą.** „Radiance“ eksportas iš
> `capture_…_raw.tif` vis dar vadinamas `capture_…_raw.tif` — jis tiesiog saugomas
> `tiff32/Radiance_Images/`. **Produktą identifikuoja aplankas, o ne failo pavadinimas**, todėl ieškant
> pagal `*radiance*.tif` nieko nerandama; vietoj to reikia ieškoti pagal aplanką.

### Šviesos jutiklio įrašai — kalibruoti `.daq` + `.csv`

`process` taip pat tvarko `.daq` įrašus jūsų įvesties aplanke, ir tam **ne**
reikia jokių vaizdų: atskirai skraidantis DAQ-U / DAQ-M / DAQ-E yra pilnas
įrašas, o aplankas, kuriame yra tik `.daq` failai, yra tinkamas įvesties šaltinis.

DAQ įrašai gali būti daromi **be** jo kalibravimo — būtent taip viešai prieinami
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) įrašymo įrenginiai
(`record_daq.py`) veikia pagal numatytuosius nustatymus: jie įrašo neapdorotus jutiklio skaičiavimus ir pažymi failą taip, kad
Chloros gautų tą jutiklio gamyklinį kalibravimą **pagal serijos numerį** (pirmiausia iš vietinės talpyklos,
tada iš „MAPIR“ debesies) ir jį pritaiko. `process` rezultatą vėl įrašo atgal:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` kiekvienam matavimui skiria vieną eilutę: UTC laiko žymą, integracijos laiką, bendrą galią,
fotopinį/skotopinį liuksą, PPFD (ir jo pasiskirstymą pagal mėlyną, žalią bei raudoną spalvas), didžiausią bangos ilgį, o po to –
visą spektrą pagal paties jutiklio bangų ilgių tinklelį. `.daq`importuoja be
pakartotinės kalibracijos.

Sėkmingai atlikus operaciją, ataskaitoje nurodomas kodas `Light-sensor products written: N (calibrated .daq + .csv)`.
Skliaustuose nurodoma, kas iš tikrųjų buvo įrašyta, taigi ten rašoma
`(RAW COUNTS — this sensor has no calibration bundle)`, jei jutiklis be paketo, ir
`(N calibrated, M raw counts)`, jei aplankas turi abu. Pačios sistemos
antraštės „`[DAQ-EXPORT]`“ ir „`[RUN-SUMMARY]`“ formuluojamos tuo pačiu būdu — nė viena iš
iš šių trijų negali vadinti neapdorotų eksportuotų duomenų kalibruotais.

DAQ-U / DAQ-M / DAQ-E įrašas, kurio kalibravimo rinkinio negalima gauti — esate
neprisijungę prie interneto arba to jutiklio kalibravimo duomenų nėra faile — yra **praleidžiamas nurodant priežastį** eilutėje
`[DAQ-EXPORT]`, niekada neišrašomas kaip „kalibruotas“ failas, kuriame saugomi neapdoroti skaičiai.
Prisijunkite prie interneto ir paleiskite iš naujo. Priežastis yra ta, kurią skaitytuvas faktiškai
nustatė tam failui (neskaitoma schema, nėra rinkinio, rašymo klaida), o vykdymo
apibendrinime išvardytos **atskiros** priežastys — dvidešimt failų, praleistų dėl vienos priežasties, pateikiami kaip viena
priežastis, o ne kaip dvidešimt jos pasikartojimų.

#### DAQ-A įrašų eksportavimas kaip neapdorotų skaičių

**DAQ-A** šeima yra senesnė už serijinių paketų sistemą ir neturi kalibravimo paketo,
kurį reikėtų atsisiųsti — vietoj to ji kalibruojama lauke pagal atspindžio taikinį, todėl
jos niekada nereikėjo. Atmetus tuos įrašus, nebeliko jokio būdo gauti jų
duomenis, todėl jie eksportuojami su **kitu pavadinimu**:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Kitoks failo pavadinimas, o ne žymė pačiame faile, nes duomenys turi išlikti
siunčiami el. paštu kaip paprastas pavadinimas. `.csv` antraštėje nurodyta
`raw spectral sensor counts (NOT irradiance)` ir įspėjama, kad reikšmės yra palyginamos
**pačiame** faile — būtent tam jas ir naudoja tiksliniu kalibravimu pagrįstas metodas — o
ne tarp skirtingų jutiklių. Nuo galios priklausančios fotometrinės stulpeliai (bendroji galia, fotopinis ir
skotopinis liuksas, PPFD) įrašomos kaip **NULL**, o ne integruojamos iš skaičiavimų, o bandymo
santraukoje nurodyta `RAW COUNTS`, todėl į žurnalą „eksportuoti“ duomenys negali būti traktuojami kaip spinduliavimo intensyvumas.

Senosios versijos **v1.01 / v1.02** įrašai (juos rašo DAQ-A-SD) neturi kiekvieno matavimo epochos,
tik failo įrašymo laiką. Vaizdo ↔ žemyn nukreipto spinduliavimo atitikimo programa vis dar jų nepriima — atitikimas
kadrą su įrašymo laiku būtų nepastebimai klaidingas — tačiau eksportuotojas juos nuskaito, o
„CSV“ išspausdina „`clock=daq_created_on`“, todėl produktas nurodo, pagal kokį laikrodį jis veikia.

### Pastabos

- „`process`“ automatiškai nustato, ar jūsų aplankas yra „Survey3“, „LATTICE“ ar mišrus.
- Vykdymo eiga perduodama per „Server-Sent Events“; „CLI“ rodo realaus laiko kiekvieno sriegio vykdymo eigą („Detecting“, „Analyzing“, „Processing“, „Exporting“).
- „Linux“ / „Jetson“ atveju „CLI“ patikrina keitimo atmintį ir gali įspėti prieš apdorojant didelius aplankus. Tekstūrą atpažįstantis „debayer“ taip pat automatiškai taiko GPU dažnio ribą mažos galios „Jetson“ įrenginiuose („Nano“, „Orin Nano“).
- Sėkmingai užbaigus vykdymą, ataskaitoje nurodoma, kiek vaizdų produktų buvo įrašyta (`Image products written: N`).

#### Vykdymas, kurio metu neįrašoma jokių vaizdų, laikomas nesėkmingu

Jei paprašėte produktų, o vykdymo metu nebuvo įrašyta **nė vieno** — tik `project.json` ir
`calibration_data.json` — „`process`“ tai traktuoja kaip nesėkmę: išveda
`Processing finished but wrote no image products.` ir **baigia veikimą su nelygiu nuliui rezultatu**, todėl skriptas gali
tai aptikti. Pranešime nurodytas projekto aplankas ir įprastos priežastys:

- įvesties aplankas nebuvo atpažintas kaip užfiksavimas (patikrinkite išdėstymą ir „`--input-level`“), arba
- visi prašyti produktai buvo praleisti kaip netaikytini toms kameroms (pvz., prašant
  spinduliavimo/atspindžio iš „RGB“ kameros).

Vykdykite dar kartą su `--verbose` ir patikrinkite užkulisio žurnalą, ieškodami eilučių `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`,
kurios paaiškina praleidimus pagal kameras, kurie kitaip nepatenka į „CLI“ išvestį.

Sąmoningas vykdymas tik su metaduomenimis — visi produktai išjungti ir be `--indices` — vis tiek yra
**sėkmingas**, nes tuščias vaizdo išvesties rezultatas šiuo atveju yra teisingas.

Taip pat ir **tik šviesos jutiklio veikimas**: `.daq` įrašų aplanke nėra vaizdų, kuriuos būtų galima eksportuoti
, o vykdymas vertinamas pagal kalibruotus „`.daq`“ / „`.csv`“, kuriuos jis užrašė vietoj to.

---

## `chloros-cli login`

Autentifikuokite šį kompiuterį naudodami „Chloros+“ debesies paskyrą. Prisijungimo duomenys saugiai išsaugomi `~/.chloros/user_session.json`.

```
chloros-cli login EMAIL PASSWORD
```

### Pavyzdžiai

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **„PowerShell“ `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (pašalinant dalį slaptažodžio arba dubliuojant jo dalis). Gavus 401 klaidą, „CLI“ automatiškai bando iš naujo, pridėdamas `$$`, tada – pašalinęs dubliuojamą slaptažodžio dalį; jei pakartotinis bandymas pavyksta, sistema jus prisijungia ir parodo teisingą viengubų kabučių sintaksę, kurią reikia naudoti kitą kartą.

> **Naudojimas be grafinės sąsajos / su skriptais: nesant sesijos išsaugojimo atmintyje, pasirodo interaktyvi komandų eilutė, o ne greita klaida.** Bet kuri komanda, paleidžianti foninį procesą (`process`, `status`, `export-status`, `time-sync`, …), vykdoma be išsaugotos licencijos ar sesijos, prieš tęsiant veikimą stdin kanale rodo interaktyvią `Email:` / `Password:` eilutę. Todėl automatizuota užduotis be išsaugotos sesijos užstrigs, laukdama įvesties — prieš planuojant užduotis be vartotojo sąsajos, kiekviename kompiuteryje vieną kartą paleiskite komandą `chloros-cli login EMAIL PASSWORD`.

---

## `chloros-cli logout`

Išvalo išsaugotą sesiją ir priverčia iš naujo prisijungti kitą kartą, kai programa bus paleista.

```bash
chloros-cli logout
```

---

## `chloros-cli status`

Rodo dabartinį licencijos lygį („Iron“/„Copper“/„Bronze“/„Silver“/„Gold“), autentišką vartotoją ir įrenginių susiejimų skaičių.

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

Tikrina „Thread-4“ eksporto eigą realiuoju laiku. Saugu iškviesti **tuo metu, kai** iš kitos komandų eilutės vykdomas `process`.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

Nustato „CLI“ ekrano kalbą (palaikomos 38 kalbos, įskaitant CJK, RTL ir indų kalbas). Senesnėse konsolėse, kurios negali atvaizduoti rašmenų, sklandžiai perjungiama į anglų kalbą.

```
chloros-cli language [LANG_CODE] [--list]
```

### Pavyzdžiai

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## Projekto aplanko komandos

Šios komandos valdo numatytąją projekto aplanko vietą (bendrą su GUI).

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

---

## `chloros-cli update` „

Linux“ / Tik „Jetson“. Patikrina `version_url` iš `/etc/chloros/update.conf` ir siūlo atsisiųsti bei įdiegti atitinkamą `.deb`.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

„Linux“ / „Jetson“ sistemoje „CLI“ taip pat **kiekvieną kartą paleidžiant atlieka automatinį atnaujinimų patikrinimą** (neblokuojantis, niekada neuždelsta komandos vykdymo): perskaito „`/etc/chloros/update.conf`“, rezultatą 1 valandą išsaugo „`~/.chloros/update_cache.json`“ talpykloje ir išveda „`Update available: vX.Y.Z / Run: chloros-cli update`, jei yra naujesnė versija. Bet kokios klaidos atveju ir esant „Windows“ tyliai praleidžiama.

---

## `chloros-cli selftest`

Atlieka 7 etapų bandymą: versija, prievado prieinamumas, užpakalinės dalies paleidimas, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), triukšmo šalinimo modelio buvimas, CUDA ir triukšmo šalinimo parengtis.

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

PTP „grandmaster“ būsena ir valdymas. „Chloros“ serveris vykdo PTP „grandmaster“ funkciją; „LATTICE“ kameros ir „DAQ-E“ įrenginiai veikia kaip jo pavaldiniai, kad būtų užtikrinti įrenginių tarpusavio laiko žymos.

| Pakomanda | Aprašymas |
| --- | --- |
| `status` | Rodyti pagrindinio serverio būseną, BMCA prioritetus, laikrodžio identifikatorių. |
| `peers` | Rodyti per „Delay_Req“ aptiktus pavaldinius įrenginius (kameras ir DAQ-E jutiklius). |
| `cameras` | Kiekvienos kameros PTP būklė (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | „Grandmaster“ proceso paleidimas iš naujo. |
| `set-priority --priority1 N --priority2 N` | Apeiti BMCA prioritetus. |

### Pavyzdžiai

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

„LATTICE“ kameros valdymas. Kiekviena pakomanda nukreipiama per „Chloros“ užkurtį; užkurtis valdo kamerų grupę, todėl vėlesni „CLI“ iškvietimai pakartotinai naudoja tą patį atvirą identifikatorių.

### Bendrosios parinktys (bendros daugumai pakomandų)

| Žymė | Aprašymas |
| --- | --- |
| `-d, --device N` | Kameros indeksas (numatyta reikšmė: 0). |
| `-s, --serial SN` | Konkretus serijos numeris; pakeičia `--device`. |
| `--serials SN1,SN2,…` | Kableliais atskirti serijiniai numeriai, skirti darbui su keliomis kameromis. |
| `--all` | Veikti su kiekviena aptikta kamera. |
| `--exposure US` | Ekspozicijos trukmė mikrosekundėmis. |
| `--gain DB` | Stiprinimas dB. |
| `--pixel-format FMT` | pvz., `BayerRG8`, `BayerRG12`. |
| `--width N` / `--height N` | Vaizdo matmenys. |
| `--preset {default,high_quality,high_speed,triggered}` | Taikyti nustatymų šabloną. Visi veikia laisvuoju režimu, išskyrus `triggered`, kuris įjungia kamerą, reaguojančią į 2-osios linijos aparatinį signalą — jei ši linija nėra aktyvuota, kamera lauks neribotą laiką, o ne fiksuos vaizdą. |
| `-o, --output DIR` | Išvesties katalogas (numatyta: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | GVSP paketo dydis. `auto` atlieka ICMP+GVSP patikrinimus; `jumbo` = 9000; `standard` = 1500. |

### „LATTICE“ kameros pirmojo prisijungimo darbo eiga

```bash
# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Auto-shrink frame size to fit the wire
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### Papildomų komandų žinynas

#### Atradimas ir informacija

| Papildoma komanda | Paskirtis |
| --- | --- |
| `lattice info` | Pateikti prijungtų kamerų sąrašą (gamintojas, modelis, serijos numeris, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Analizuoti pagrindinę sistemą, siekiant nustatyti optimalų kameros konfigūravimą. `--no-discover` praleidžia kameros aptikimą (greitesnis, analizuojama tik pagal tinklo plokštę). |
| `lattice network [--fix] [--estimate] [--cameras N]` | Patikrinti / pataisyti tinklo plokštės nustatymus; įvertinti pralaidumą / kadrų skaičių per sekundę (FPS). |
| `lattice network-analysis --master SN --slaves SN1,SN2,… [--width N] [--height N] [--pixel-format FMT] [--binning N] [--force-tier TIER] [--backend-url URL] [--json]` | „Stable-schema“ užkulisio tinklo galimybės + masyvo rekomendacijos (grąžina `status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `auto_capped_fps` išlaiko prašomą skiriamąją gebą, bet apriboja tikslinius kadrų per sekundę skaičių — perskaitykite `recommended.recommended_target_fps` ir perduokite jį kaip prisijungimo tikslą; laikykite tai sėkme, o ne klaida. |
| `lattice analyze-array [--models M1,M2,…] [--binning N] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--json]` | „Kas būtų, jeigu“ analizė neatidarant kamerų. **`--n-active` yra bendras tinkle esančių kamerų skaičius, o ne tik šio masyvo**— padidinkite jį, kai atskiros kameros transliuoja vienu metu arba kai tinklo pralaidumo biudžetas apskaičiuojamas remiantis poreikiu, kuriame jų skaičius yra per mažas (numatyta reikšmė: `len(--models)`). Visada išspausdina suvestines eilutes `Wire budget:` (reikalaujamas MB/s palyginti su susidūrimų saugumo riba) ir `Max cameras:` bei pažymi `** OVER-SUBSCRIBED**`, kai masyvas viršija laidų pajėgumą — žr. [Masyvo fps ir srauto modelis](#array-fps--burst-model). |
| `lattice gpu` | Rodyti GPU būseną. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Patikrinti arba atnaujinti kameros programinę įrangą. Vietinis `.fwa` pasirinkimas yra užfiksuotas: failas `firmware/<MODEL_PREFIX>/`, atitinkantis kompiliacijos `MIN_FIRMWARE_VERSION`, yra įrašomas, jei yra (aukščiausia versija naudojama tik kaip atsarginė), todėl naujesnis gamintojo atvaizdas, laikomas diske, neveikia, kol tas užfiksavimas nėra — sąmoningai naujesnės versijos pasiekia įrenginius per pasirašytą AWS manifestą, kuris yra teikiamas pirmenybė, jei yra naujesnis. |
| `lattice presets [--apply NAME]` | Rodyti arba taikyti kameros išankstinius nustatymus. |
| `lattice status` | Kameros būsenos rodymas realiuoju laiku. |

#### Įrašymas

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice capture [--format tiff\|png\|jpg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--force-daq]` | Vienas kadras. **Pagal numatytuosius nustatymus išsaugoma kiekviena eksporto rūšis** (`--processing all`); žr. [Įrašymo eksporto lygiai](#capture-export-levels-the-all-default). `--levels` išsaugo nurodytą dalinį rinkinį (pakeičia `--processing`); `--force-daq` įrašo priskirtą DAQ rodmenį kaip `.daq` priedą net ir eksportuojant tik neapdorotus duomenis. `--jpeg-quality` = „JPEG“ kokybė 1–100 (pagal numatytuosius nustatymus 95). |
| `lattice continuous [--format tiff\|png\|jpg] [--jpeg-quality N] [--queue-depth N]` | Transliuoti į diską, kol bus paspaustas „Ctrl+C“. |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Naršyklėje veikiantis tiesioginis MJPEG peržiūros langas. `--ae-damping` nustato automatinės ekspozicijos slopinimą (0,4–100). |

#### Jutiklio nustatymas

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice configure [--get N1 N2…] [--set N=V N=V…] [--dump] [--json]` | Skaityti / rašyti bet kurį „GenICam“ mazgą. |
| `lattice exposure [--auto] [--auto-once] [--off] [--set US] [--brightness N] [--damping F] [--upper-limit US]` | Ekspozicija ir automatinė ekspozicija (AE). |
| `lattice gain [--auto] [--off] [--set DB]` | Stiprinimas ir automatinis stiprinimas. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N] [--binning-mode Sum\|Average]` | Jutiklio ROI ir pikselių sujungimas. |
| `lattice format [--set FMT] [--list]` | Pikselių formatas. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Aparatinis/programinis trigeris. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (be žymių = vienkartinis baltos spalvos balansavimas) | Baltos spalvos balansavimo operacijos. RGB /Tik „Bayer“ kameroms; neveikia (praleidžiama) mono M3M. |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct K] [--get]` | „RGB“ spalvų apdorojimo grandinė. `natural` (numatyta) yra pigesnis tiesioginis apdorojimas; `enhanced` prideda spalvų kontūrų pašalinimą + gyvybingumą + CLAHE vietinį kontrastą, kad būtų pasiektas pilnas „hub-parity“ vaizdas, kainuojantis maždaug 2 kartus daugiau užkadrą, todėl mažesnis **tiesioginis** kadrų dažnis — išsaugoti kadrai bet kuriuo atveju visada gauna pilną apdorojimą. Tik „RGB“ / „Bayer“ kameros; praleidžiama naudojant „mono M3M“. |
| `lattice color [--saturation N] [--contrast N] [--reset] [--get]` | Ekrano sodrumas/kontrastas („RGB“ filtro kameros). Praleidžiama naudojant mono M3M. |
| `lattice filter [--set NAME] [--list]` | Nustatyti kameros filtro modelį (`RGN-IMX265`, `OCN`, `NGB`, …). |
| `lattice power [--sleep]` | Patikrinti maitinimo / šiluminius mazgus; įjungti / išjungti mažos galios budėjimo režimą. |

#### Kalibravimas ir jutikliai

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Kalibruoti pagal atspindžio taikinį. |
| `lattice dls [--connect] [--spectrum] [--irradiance] [--mac MAC] [--filter NAME] [--json]` | Įmontuotos žemyn nukreiptosšviesos jutiklio komandos. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Taikyti vinjetės korekciją esamiems vaizdams. |

#### Daugiakamerinis režimas (trumpalaikės sesijos)

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice multi-info` | Išvardyti visas kameras su sinchronizavimo vaidmenimis. |
| `lattice multi-capture [--format FMT] [--jpeg-quality N] [--processing LEVEL]` | Po vieną sinchronizuotą kadrą iš kiekvienos kameros. **Pagal numatytuosius nustatymus išsaugo visus eksporto tipus**, kai prijungtas nuolatinis masyvas; trumpalaikio be masyvo atsarginio varianto atveju atliekamas tik**debayeringas** (likusiems kadrams pirmiausia paleiskite komandą `array-connect`). |
| `lattice multi-stream [--fps F] [--count N] [--format FMT] [--jpeg-quality N]` | Sinchronizuotų kadrų srautas (trumpalaikis). |
| `lattice multi-test [--count N]` | GPIO sinchronizavimo laiko testas. |
| `lattice multi-detect [--line LINE] [--json]` | Automatinis GPIO pagrindinio/pavaldžiojo įrenginio laidų nustatymas. |

#### Suderinti

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice align-calibrate [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N]` — taip pat detektoriaus/suderinimo reguliatoriai `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, RANSAC reguliatoriai `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, kelių kadrų sujungimas `[--averaging mean\|median\|inlier_weighted]`, geometriniai apribojimai `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, erdviniai apribojimai `[--roi X0,Y0,X1,Y1] [--mask PATH]` ir kiekvieno pavaldinio perrašymai `[--per-cam-override SN:KEY=VALUE]` (pakartojama) | Apskaičiuoti suderinimo profilį iš veikiančių kamerų. `--prefilter` numatytasis nustatymas yra `gradient` (kraštų žemėlapis; atitinka GUI/masyvo suderinimo įrankį — kraštai išlieka visose spektrinėse juostose). `--matcher flann` pasiteisina, kai bruožų skaičius viršija ~5000; `--averaging median` yra atsparus vienam netinkamam įrašui, `inlier_weighted` sveria pagal atitikimų skaičių; `--lock-scale` projektuoja į artimiausią pasukimą (be mastelio), `--lock-axis` nulinąja reikšme nustato vieną transliacijos komponentą; `--mask` taikomas kiekvienai kamerai (naudokite `--per-cam-override`, jei norite nustatyti parametrus kiekvienaikameros nustatymams, pvz., `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` atsisako išsaugoti kalibravimą, kurio reprojekcijos RMS viršija ribą. |
| `lattice align-apply --profile PATH [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N]` | Užfiksuoti vieną suderintą daugiajuostinįjuostų kadrą. `--bit-depth` pagal numatytuosius nustatymus pritaiko prie kameros; `--no-crop` išlaiko visą kadrą (užpildo juodai); `--interpolation` (numatyta reikšmė `linear`) ir `--border-mode`/`--border-value` (numatyta reikšmė `constant`/0) valdo procesoriaus (CPU) deformaciją — vaizdo plokštės (GPU) kelias bet kuriuo atveju yra bilinearinis. |
| `lattice align-stream --profile PATH [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N]` | Srauto suderinti daugiabandžiai kadrai (tie patys „warp“ reguliatoriai kaip ir `align-apply`). |
| `lattice align-info --profile PATH [--json]` | Rodo profilio detales. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Keičia sluoksnių tvarką. |

#### Indeksas / Augmenijos skaičiavimai

```bash
# Offline: compute NDVI from an aligned multi-band TIFF
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: discover array, calibrate alignment, capture, compute index, in one go
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --save-multiband -o output/
```

Pilnas žymių rinkinys: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI / NDRE / EVI / SAVI / GNDVI /…), `--formula EXPR`, `--channel SYM=BAND` (pakartojama), `--capture-level raw|debayered|radiance|reflectance|unknown` (perrašo šaltinio „TIFF“ įrašytą įrašymo lygį; numatytasis: nuskaitoma iš „TIFF“ metaduomenų), `--output PATH`, `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. Taip pat taikomi `--live` taip pat taikomi išlyginimo iškraipymo reguliatoriai: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`.

> **`--channel` simboliai yra jautrūs didžiosioms ir mažosioms raidėms.** Simbolių pusė turi tiksliai atitikti išankstinio nustatymo kanalų pavadinimus (išankstiniuose nustatymuose naudojamos mažosios raidės, pvz., „NDVI“ = `red`, `nir` — patikrinkite `--list-presets`), o dažnių juostos dalis turi sutapti su dažnių juostos pavadinimu suderintame sąraše (arba būti dažnių juostos indeksu, skaičiuojamu nuo 0, neprisijungus prie interneto). „`--channel red=Red_660 --channel nir=NIR_850`“ veikia; „`--channel RED=660`“ sukelia klaidą „`channel_map missing entries`“.

#### Nuolatiniai ryšiai („Smart-Prep“, GUI ekvivalentas)

Šios komandos išlaiko kameras atidarytas užkulisiniame pulke per visus „CLI“ iškvietimus.

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Pridėti vieną kamerą į grupę (viena kamera, be masyvo). |
| `lattice cam-disconnect [--serial SN] [--all]` | Atleisti. |
| `lattice cam-list` | Rodyti pulke esančias kameras. |
| **`lattice array-connect`**|**Prijungti nuolatinį sinchronizuotą masyvą (REKOMENDUOJAMAS pradinis taškas).** Vykdo visą GUI „smart-prep“ eigą. |
| `lattice array-disconnect [--array-id ID] [--all]` | Atleisti masyvą. |
| `lattice array-list` | Rodyti prijungtų masyvų sąrašą. |
| `lattice array-status [--array-id ID]` | Tiesioginis kadrų dažnis (fps), PTP, paskutinė klaida. |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--index\|--no-index] [--force-daq] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Vienas sinchronizuotas kadrų rinkinys iš tiesioginio masyvo — Vienkartinis / Nuolatinis / Intervalinis / Greičiausias. **Numatytasis nustatymas – `all`** (vienas failas pagal atitinkamą eksporto tipą kiekvienai kamerai). Praleistos kameros (pvz., „RGB“ neįtrauktos į spinduliavimo / atspindžio matavimus) nurodomos su `Skipped: SN:<serial> (<reason>)`; atspindžiui naudojamas DAQ rodmuo išsaugomas kartu ir nurodomas su `DAQ: <path>`. Žr. [Fiksavimo režimai, įrašymo įrenginiai ir neprisijungus atkūrimas)“ (#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only]` | Įrašykite tiesioginį sujungto indekso vaizdą į vaizdo įrašą / GIF (stebėjimo kokybės; reikia, kad sujungtas srautas būtų atidarytas). |
| `lattice array-burst [--duration S] [--max-frames N] [--build] [--products …]` | Serija neapdorotų „Bayer“ vaizdų su dideliu kadrų dažniu (skirta analizei; perdirbama neprisijungus). |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Išsaugotą neapdorotų kadrų seriją perdirbti į kalibruotą (-us) vaizdo įrašą (-us). |

##### `array-connect` Parinktys

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automatiškai aptikti visas „LATTICE“ kameras (reikia ≥2) | Pirmasis serijinis numeris yra „MASTER“. Jei nenurodyta, aptikimas filtruojamas pagal „LATTICE“ (`TRI032*`) modelius ir sujungiamos visos jos. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinchronizavimo linija. |
| `--target-fps F` | auto | Pagrindinio įrenginio trigerio aktyvacijos dažnis. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Aplenkti lygio pasirinkimo funkciją. |
| `--wire-ceiling-mbps MB_PER_S` | nustatoma automatiškai | **Pagrindinio kompiuterio nuolatinis laidinio ryšio pralaidumas, išreikštas MB/s — skaičius, nuo kurio priklauso visos matricos paskirstymas.** Sumažinkite jį, kai masyvas praneša apie GVSP sugadintus rėmelius: automatinė reikšmė apskaičiuojama pagal tinklo plokštės (NIC) skelbiamą ryšio greitį, kuris pervertina USB adapterių, siaurų PCIe juostų ir užimtų bendrų tinklų pajėgumą. Ši reikšmė išsaugoma projekto masyvo fiksavimo bloke, todėl ją atkuria pakartotinis atidarymas / „CLI“ / „SDK“ prisijungimas. Žr. [Masyvo būklė](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | auto | Aparatinis duomenų grupinimas. |
| `--no-recommend` | išjungta | Praleisti tinklo analizės etapą. |
| `--no-ptp` | išjungta | Išjungti PTP (tuomet skirtingų kamerų laiko žymos **nėra** palyginamos). |

### „Smart-AE“ / „Smart-Capture

„LATTICE“ matricos, vos tik prijungtos, fone nuolat vykdo automatinį ekspozicijos nustatymą (AE), tačiau naujai nukreiptai scenai reikia šiek tiek laiko, kol ekspozicija stabilizuojasi. `array-capture --smart` yra **patogus sprendimas**: jis laukia, kol AE stabilizuosis visose matricos kamerose, ir tada paleidžia fiksavimą. Naudokite jį, kai sesijos metu keičiate sceną.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Nustatymų stabilizavimo politika pagal numatytuosius parametrus yra konservatyvi: 5 s laiko limitas, 1,5 s stabilumo langas, ±5 % ekspozicijos nuokrypio tolerancija. Jei norite, kad automatizavimo sistema veiktų kitaip, nustatykite parametrus per „SDK“ (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`).

### Įrašymo eksporto lygiai (numatytasis `all` numatytasis)

Nuo šios versijos `lattice capture`, `lattice multi-capture` ir `lattice array-capture` **numatyta reikšmė yra `--processing all`** — vienas išsaugotas failas kiekvienam eksporto tipui , kuris taikomas kiekvienai kamerai, atitinkantis GUI funkcijos „Užfiksuoti viską“ veikimą. Lygiai yra tokie:

| Lygis | Išvestis | Taikoma |
| --- | --- | --- |
| `raw` | Vienkanalis „Bayer“ (mono kameros: viena juosta) tiesiai iš jutiklio. | Visos kameros. |
| `debayered` | 3 kanalų BGR demozajavimas (mono kameros: 1 kanalo pilkosios skalės). | Visos kameros. |
| `radiance` | „float32“ W/m²/sr/nm per visą radiometrinę grandinę. | Tik multispektrinės (M3C/M3M) — **neapskaičiuojama „RGB“ filtruojančioms kameroms**. |
| `reflectance` | uint16 ρ (`32768` = 1,0), suderinama su „Pix4D“. | Tik daugiaspektrinė, ir **tik tada, kai priskirtas DAQ + kamera kalibruota**; kitais atvejais praleidžiama. |
| `preview` / `display` | Pilna GUI peržiūros grandinė (CCM + WB + gama pagal kameros profilį). `lattice capture` pavadina šį `preview`; `array-capture`/`multi-capture` naudoja `display`. | Visos kameros. |

Nurodykite vieną lygį, kad būtų išsaugotas tik tas vienas (`--processing debayered`). Kai prašote `all`, lygiai, kurie netaikomi konkrečiai kamerai, yra praleidžiami (ir apie juos pranešama), o ne rodomos klaidos — neprijungta arba nekalibruota kamera vis tiek gauna `raw` / `debayered` / `preview`.

Bet kuriam atspindžio kadrui faktinis DAQ žemyn nukreiptas matavimas įrašomas į **`.daq`** priedinį failą šalia vaizdo (kad įrašą vėliau būtų galima apdoroti iš naujo) ir pateikiamas `DAQ:` eilutėje.

### Kaip atrodo užfiksuotų duomenų aplankas

Kiekvienas eksporto tipas patenka į savo **atskirą pakatalogį**, esantį po `-o`, todėl daugiapakopio užfiksavimo atveju tipai niekada nesimaišo:

```
output/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
└── *.daq          the downwelling reading matched to the capture
```

`<ts>` yra užfiksavimo laiko žyma, o `<serial>` – kameros serijos numeris, taigi viena sinchronizuota grupė turi bendrą
laiko žymą visose kamerose. **Atkreipkite dėmesį į vieną asimetriją:** `display` lygis saugomas aplanke,
pavadintame `preview/`, o pačių failų pavadinimuose išlieka `_display` — aplankas ir priesaga skiriasi
tik šiam lygiui. Nežinomi lygiai perkeliami į aplanką, pavadintą jų pačių pavadinimu, o jei pakatalogio
sukurti nepavyksta, failas įrašomas į išvesties šaknį, o ne prarandamas.

**„Captures“ aplanko pakartotinis apdorojimas:**nukreipkite `chloros-cli process` į**„Captures“ šaknį**
(`output/`). `process` paprastai importuoja tik jūsų nurodytą aplanką, tačiau kai tame aplanke nėra
vaizdų, o yra pakatalogiai, jis automatiškai nusileidžia žemyn — taigi pagrindinio katalogo lygio pakatalogiai ir pats
pagrindinis katalogas `.daq` surenkami vienu kartu. Kiekvienas užfiksuotų vaizdų lygis importuojamas kaip vienas vaizdas, o
kiti lygiai prieinami kaip režimai, o ne kaip po vieną vaizdą kiekvienam lygiui.

Taip pat veikia ir tiesioginis **lygio pakatalogio** pavadinimo nurodymas (pvz., `output/raw/`) taip pat veikia. Tai padarius, šaknis
`.daq` lieka nepanaudota, todėl, kai iš naujo gaunate radiometrinį
produktą iš `raw/` — priešingu atveju laiko žymos atitikimas neturės su kuo susieti.

**Apdorojimas visada prasideda nuo `raw`.** Kiekviename įraše neapdorotas kadras yra apdorojimo grandinės šaltinis;
`debayered`, `radiance`, `reflectance` ir `preview` pateikiami kaip peržiūros režimai, tačiau niekada nėra grąžinami
atgal per apdorojimo grandinę. Pakartotinai apdorojant išvestinį produktą, būtų iš naujo pritaikyti vinjetės, CCM ir
spinduliavimo skaičiavimai , kurios jau yra įtrauktos į jo pikselius, todėl „Chloros“ atsisako, o ne
atlieka dvigubą apdorojimą. Dvi pasekmės, kurias verta žinoti:

- Renderiai „`index/`“ ir „`composite/`“ **niekada** neapdorojami. Tai yra išvesties failai, o ne įrašai —
  „NDVI“ LUT renderis neturi jokios prasmingos spinduliavimo interpretacijos.
- Įrašų aplankas, eksportuotas **be** „`raw`“ (pvz., „`array-capture --processing reflectance`“), neturi
  jokio teisėto apdorojimo grandinės šaltinio. Šie įrašai importuojami ir rodomi įprastai, tačiau `process` juos praleidžia
  juos praleidžia ir apie tai praneša:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Jei jums tikrai reikia perduoti išvestinį produktą — „hub“ sesiją, užfiksuotą su
  įjungtu `demosaic`, arba seną aplanką — `--input-level {raw,debayered,processed}` priverčia naudoti įėjimo
  tašką ir panaikina praleidimą. Šis žymeklis yra sąmoningai numatytas išeities variantas; `auto` (numatytoji reikšmė)
  niekada neapdoroja įrašo, kuris neturi neapdorotų duomenų.

### Praleisti įrašai mišrių filtrų masyvuose

Kai viename masyve sumaišote „RGB“ ir multispektrines kameras, `array-capture --processing radiance` (arba `reflectance`) išsaugo multispektrinius kadrus ir **praleidžia** „RGB“ kameras — spinduliavimas vienam „Bayer“ elementui nėra reikšmingas plačiajuosčio dažnio jutikliui. „CLI“ aiškiai išspausdina kiekvieną išsaugotą failą (kartu su jo eksporto lygiu), kiekvieną užrašytą „`.daq`“ ir kiekvieną praleidimą, todėl failų skaičius nėra netikėtas:

```
  Saved: output/sync_…_SN213800234.tif [reflectance] (SN:213800234, fid:1)
  Saved: output/sync_…_SN214000533.tif [reflectance] (SN:214000533, fid:1)
  Saved: output/sync_…_SN214701288.tif [reflectance] (SN:214701288, fid:1)
  DAQ:   output/sync_…_daq-e-54b5e0.daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

Pralikimo priežasties žymos atitinka šabloną `<level>-not-applicable-to-rgb-cam`. Atspindžio matavimai taip pat gali būti praleisti su `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, o su `dls-uncalibrated-band-<nm>`, kai juosta daugiausia yra už DAQ šviesos jutiklio radiometriškai kalibruoto diapazono ribų (~374–974 nm) — iš siunčiamų SKU tik F988, kurio palaikomas procesas yra atspindžio skydelio darbo eiga.

Naudokite `--processing debayered` (arba `display`), kad įtrauktumėte visas kameras, nepriklausomai nuo filtro tipo, arba numatytąjį `all`, kad vienu metu gautumėte visus taikytinus lygius kiekvienai kamerai.

---

## Įrašymo režimai, įrašymo įrenginiai ir neprisijungus atliekamas perdirbimas

Visi jie veikia **nuolatinėje matricoje** (pirmiausia paleiskite `array-connect`). Jie atspindi GUI įrašymo skydelį.

### `array-capture` režimai

`array-capture` yra viena komanda su keturiais užrakto režimais ir eksporto perjungimo parinktimis:

| Režimas | Žymė | Veikimas |
| --- | --- | --- |
| **Vienkartinis** *(numatyta)* | (nėra) | Viena sinchronizuota fiksavimo grupė, po to uždaroma. |
| **Nuolatinis** | `--continuous` | Vienas po kito vykdomi ciklai, kol bus pasiektas `Ctrl+C`, `--count N` arba `--duration S`. |
| **Intervalas** | `--interval S` | Vienas praėjimas kas `S` sekundžių (skaičiuojant nuo kiekvieno praėjimo pradžios), tos pačios ribos. |
| **Greičiausias** | `--fastest` | Tik neapdoroti duomenys + priskirtas DAQ rodmuo + sujungtasindekso kompozicija; praleidžia spinduliavimo/atspindžio/ekrano skaičiavimus, kad kadras būtų pateiktas greitai. Tai reiškia `--processing raw --force-daq`. Vėliau iš naujo apdorokite išsaugotus `.daq` duomenis į kalibruotus produktus. |

Eksporto perjungikliai (derinamisu bet kuriuo režimu; visi naudoja tą pačią GUI / „SDK“ galinį tašką):

| Žymė | Poveikis |
| --- | --- |
| `--processing LEVEL` | Vienas eksporto lygis arba `all` (numatyta reikšmė). |
| `--levels L1,L2,…` | Konkretus eksporto tipų pogrupis (pvz., `raw,radiance,reflectance`); **pakeičia `--processing`**. |
| `--aligned` / `--no-aligned` | Kiekvieno elemento ne žaliavinio eksporto transformavimas pagal masyvo [suderinto profilio](#išlyginimą) (suderinta). Neapdoroti duomenys lieka neišlyginti, bet transformacija nurodoma metaduomenyse. Jei masyvas neturi profilio, grįžtama prie neišlyginto varianto (su įspėjimu) , jei masyvas neturi profilio. |
| `--index` / `--no-index` | Išsaugoti / praleisti kiekvienos kameros augmenijos indekso perdangą, jei ji yra sukonfigūruota. Numatytasis nustatymas: atvaizduoti. |
| `--force-daq` | Išsaugoti priskirtą DAQ/DLS rodmenį kaip `.daq` „sidecar“ failą, net jei nė vienam pasirinktam lygiui to nereikia (pvz., tik neapdorotų duomenų įrašymas), kad kadrus būtų galima vėliau apdoroti atspindžio / indekso atžvilgiu neprisijungus. |
| `--smart` | Prieš paleidžiant laukti, kol AE nusistovės visose kamerose (žr. [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | „TIFF“ pikselių suspaudimas. `deflate` (numatyta) = be nuostolių „zlib L1“ + horizontalusis prognozuotojas, ~4,1 MB vienam pilnos raiškos kadrui; `none` = nesuspaustas, ~5 kartus greitesnis įrašymas, ~6,3 MB vienam kadrui — naudokite siekiant maksimalaus nuolatinio greičio, kai leidžia disko talpa. Abu variantai yra be nuostolių ir importuojant atkuriami identiškai. |

> **Vieno rašymo „TIFF“ + pastovaus greičio modelis.**Įrašai įrašomi per**vieną**„tifffile“ praėjimą, per kurį perduodami pikseliai + XMP + IFD0 gamintojas/modelis (išmatuota pilnos raiškos „Mono12“: 36 ms suspaustas / 6,5 ms nesuspaustas, palyginti su ~148 ms senajam „parašyti, tada perrašyti su ExifTool“ metodui); vienintelis likęs „ExifTool“ darbas (EXIF sub-IFD patobulinimas) vyksta asinchroniniame fono procese, o kadras yra baigtas ir paruoštas importavimui net jei tas procesas niekada nevyksta. Atkreipkite dėmesį, kad DEFLATE suspaudimas užima „Python“ GIL, todėl suspaustų duomenų rašymas**nėra**lygiagrečiai vykdomi atskiruose kiekvienos kameros rašymo srautuose — nuolatinis 8 kamerų pilnos raiškos įrašymas jutiklio dažniu (~10,4 kadrų per sekundę) reikalauja `--compression none`**ir** NVMeklasės disko (~500 MB/s nuolatinio rašymo greičio). Tas pats parametras `POST /api/camera/array/capture` modelyje vadinamas `compression`.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --aligned --no-index -o out/
```

### `array-record` — kombinuoto indekso vaizdo įrašas/GIF (stebėjimo kokybės)

Įrašo viską, ką rodo **tiesioginis kombinuotoindekso vaizdas** rodo į `.avi` (ir pasirinktinai į `.gif`). Kadangi jis gauna duomenis iš tiesioginio kompozicinio vaizdo, kombinuotas srautas turi būti atidarytas (pvz., masyvas peržiūrimas GUI), kad kadrai būtų įrašomi. Jis kas 2 s tikrina pažangą ir sustoja pasiekęs `--duration`, `Ctrl+C` arba kai įrašymo įrenginys pats baigia veikti.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/
```

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--array-id ID` | tik masyvas | Tikslinis masyvas (neįrašykite, jei prijungtas tik vienas). |
| `-o, --output DIR` | `output` | Išvesties katalogas (vietinis). |
| `--fps F` | `10` | Įrašymo kadrų dažnis. |
| `--duration S` | iki Ctrl+C | Automatinis sustabdymas po `S` sekundžių. |
| `--gif` | išjungta | Taip pat įrašyti animuotą GIF. |
| `--gif-only` | išjungta | Įrašyti tik GIF (be `.avi`). |

### `array-burst` — „raw-Bayer“ didelio kadrų dažnio serija (analizės kokybės)

Tiesiogiai skaito „grab loop“ sinchronizuotos grupės buferį — **nereikia kalibravimo grandinės, „exiftool“ ar tiesioginio vaizdo peržiūros** — todėl veikia visu fotoaparato kadravimo greičiu. Įrašo neapdorotus kadrus + kiekvieno kadro manifestą + po vieną `.daq` už kiekvieną atskirą DLS rodmenį pagal `<output>/bursts/<base>/`. Perapdorokite neprisijungus (kita komanda) arba perduokite „`--build`“, kad tai būtų atlikta iškart sustabdžius.

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--array-id ID` | tik masyvas | Tikslinis masyvas. |
| `-o, --output DIR` | `output` | Išvesties katalogas (duomenų srautas patenka į `<DIR>/bursts/<base>/`). |
| `--duration S` | iki Ctrl+C | Automatinis sustabdymas po `S` sekundžių. |
| `--max-frames N` | neribotas | Automatinis sustabdymas po `N` neapdorotų kadrų. |
| `--build` | išjungta | Sustabdžius, iškart iš naujo apdoroti seriją (taip pat kaip `array-build-video`). |
| `--products …` | `combined:index` | Naudojant `--build`: kurį (-ius) vaizdo įrašą (-us) sukurti (žr. toliau). |
| `--fps F` | `10` | Naudojant `--build`: išvestinio vaizdo kadrų dažnis (fps). |
| `--save-tiffs` | išjungta | Naudojant `--build`: taip pat išsaugotikiekvieną kadrą kalibruotus TIFF failus. |
| `--gif` | išjungta | Kartu su `--build`: taip pat įrašyti animuotus GIF failus. |

### `array-build-video` — neprisijungus pakartotinai apdoroti išsaugotą seriją

Kiekvieną neapdorotą kadrą laiko atžvilgiu suderina su artimiausiu išsaugotu `.daq` rodmeniu ir perduoda jį per **tą pačią spinduliavimo / atspindžio / indekso grandinę kaip ir importo procesą**, sukuriant vieną ar daugiau vaizdo įrašų.

`--products` yra kableliais atskirtas `kind:level` elementų, kur `kind` ∈ `per_cam` | `combined` ir `level` ∈ `radiance` | `reflectance` | `index`. Paprastas `level` (be `kind:`) numatytasis nustatymas yra `per_cam`. Numatytasis nustatymas yra `combined:index`.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--burst-dir DIR` | (privaloma) | Kelias į „burst“ aplanką (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` sąrašas, kaip nurodyta aukščiau. |
| `--fps F` | `10` | Išvesties vaizdo kadrų dažnis (fps). |
| `--save-tiffs` | išjungta | Taip pat išsaugoti kalibruotus TIFF failus pagal kiekvieną kadrą kartu su vaizdo įrašais. |
| `--gif` | išjungta | Taip pat įrašyti animuotus GIF. |

> **Pasirinkite tinkamą įrašymo įrenginį.** `array-record` yra *stebėjimo lygio* — jis fiksuoja tiesioginį kompozicinį vaizdą taip, kaip jis rodomas, ir reikalauja, kad srautas būtų atidarytas. `array-burst` → `array-build-video` yra *analizės klasės* — jis saugo neapdorotus jutiklio duomenis pilnu greičiu ir vėliau atkuria kalibruotus spinduliavimo/atspindžio/indekso vaizdo įrašus, nereikalaujant tiesioginio vaizdo.

### Mono (M3M) vienos juostos kameros

**M3M**serija yra „Bayer“**M3C**modelių mono versija: viena siaurajuostė trukdžių filtras kiekvienoje kameroje (`M3M-<lens>-F<wavelength>`, pvz., `M3M-L87-F685`), todėl jutiklis pateikia**vieną pilkosios skalės juostą** be „Bayer“ mozaikos. Nėra ko demozikuoti, nėra kanalų tarpusavio trukdžių, kuriuos reikėtų atskirti, ir nereikia nustatyti baltos spalvos balanso – visas „RGB“ spalvų apdorojimo procesas tiesiog netaikomas.

Ką tai reiškia „CLI“ atveju:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**aptinka monochromatinę kamerą ir**praleidžia ją, pateikdami vienos eilutės pranešimą** užduotų beprasmiškų nustatymų. Jie vis dar veikia normaliai su „RGB“ / „Bayer M3C“ kamera toje pačioje sesijoje.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** vis dar veikia — spinduliavimo ir atspindžio radiometriniai žemėlapiai yra *pagal juostą* ir puikiai apibrėžti vienai juostai. Mono kadrai turi **tapatybės** jutiklio atsako matricą (be 3×3 atskyrimo), todėl kalibravimo skaičiavimai jų nepaliečia.
- **Viena mono kamera negali sukurti augmenijos indekso.**NDVI / NDRE /ir pan. reikia mažiausiai dviejų juostų (pvz., Red + NIR). Norint gauti indeksą iš monokromatinės įrangos, nukreipkite**keletą** M3M kamerų į skirtingus bangos ilgius, suderinkite jas į vieną daugiajuostį rinkinį ir apskaičiuokite *to* indeksą:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` simboliai turi **tiksliai** atitikti išankstinio nustatymo kanalų pavadinimus (skiriamos didžiosios ir mažosios raidės; NDVI yra mažosiomis raidėmis `red`,`nir` — žr. `--list-presets`), o juostos pusės pavadinimas nurodo juostą suderintame steke (neprisijungusiam režimui taip pat priimtini nuo 0 prasidedantys juostų indeksai, pvz., `--channel red=0 --channel nir=1`).

Visą steką skiriantis žymeklis yra modelio eilutėje esantis žodis „`M3M`“ (jis niekada neatsiranda eilutėje „`M3C` eilutėje), GUI/SDKyje rodomas kaip `is_mono`.

---

## Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas (LATTICE masyvai)

LATTICE kameros transliuoja GVSP per pagrindinio kompiuterio Ethernet adapterį, todėl daugiakamerių masyvų atveju adapterio **vairuotojas**ir**priėmimo žiedo dydis** yra tokie pat svarbūs kaip ir ryšio sparta. Neteisingi nustatymai pasirodo kaip „`FRAMES WILL DROP`“ / „`Reduce ROI to enable`“ vartai masyvo nustatymų skydelyje (ir „`lattice network-analysis`“ / „SDK“ „`analyze_array_network()`), net jei pačios kameros veikia tinkamai.

### USB 10GbE adapteriai — Realtek RTL8157 („Realtek USB 10GbE Family Controller“)

| Elementas | Reikalinga reikšmė | Kodėl tai svarbu |
| --- | --- | --- |
| **Vairuotojo versija**|**≥ v10.67 (2026 m. sausio mėn.)**, INF `rtump64x64sta.inf` | Senoji**2016 m.**vairuotojo versija (v10.65, `rtump64x64.inf`) netinkamai tvarko išjungimą ir klaidų patikrinimus su**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**išjungiant, perkraunant ar perjungiant į miego režimą. Perėjimas užstringa (laiko limitas ~5 min.), vartotojas priverstinai išjungia maitinimą, o pasikartojantys netvarkingi išjungimai**sugadina WMI saugyklą**(PowerShell ir kiti įrankiai pradeda strigti dėl `Invalid class`) bei**užblokuoja USB steką** kitą kartą paleidus kompiuterį (tinklo plokštė neįsijungia; USB įrenginiai nustoja būti atpažįstami). Atnaujinkite iš realtek.com (arba raktelio gamintojo) prieš bandydami iš naujo paleisti kompiuterį. |
| **Priėmimo buferiai**— raktinis žodis `ReceiveBufferLen` |**256**(maksimalus tvarkyklės nustatymas) | Tinklo plokštės RX žiedas. Vairuotojo numatytasis nustatymas**32**palieka tik ~0,26 MB naudingo žiedo — pernelyg mažai daugia-kameros serijiniam perdavimui — todėl masyvo skydelis praneša apie `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` ir blokuoja jungtis. Esant vertei**256**, žiedas yra didelis (**~13,5 MB, išmatuota laboratorijos 10GbE serveryje**), suteikiantis RX srautui realią atsargą daugiakameriniams GVSP srautams. (Ar tam tikra konfigūracija iš tiesų *užmezga ryšį*, nusprendžiama pagal du patikrinimus — **drain-aware**priėmimo patikrinimas ir**bendro perviršinio užsakymo** patikrinimas — o ne paprastas serijos ir žiedo palyginimas; žr. [Masyvo fps ir serijos modelis](#array-fps--burst-model).) |
| **Priėmimo URB**— raktinis žodis `PendingReceives` |**64** (maks.) | Siunčiami USB užklausų blokai; didinkite kartu su priėmimo buferiais, kad būtų galima sugerti srautus. |
| **Jumbo rėmelis** — raktinis žodis `*JumboPacket` | **9014** | Reikalingas 9000 baitų GVSP paketams (6 kartus mažiau paketų rėmelyje nei 1500). |

> ⚠️ **Atnaujinus tinklo plokštės tvarkyklę, šios išplėstinės savybės GRĄŽINAMOS į numatytuosius nustatymus.**Atnaujinus arba pakeitus tinklo plokštės tvarkyklę,**iš naujo pritaikykite** `ReceiveBufferLen=256` ir `PendingReceives=64`, kitaip masyvo skydelis vėl užsiblokuos, net jei „aparatūroje niekas nepasikeitė“.“ Tai yra pagrindinė priežastis, dėl kurios anksčiau veikusi įranga staiga atsisako prisijungti.

Taikykite iš **administratoriaus teisių turinčios** „PowerShell“ aplinkos (pakeiskite savo adapterio pavadinimą, pvz., `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` tinka USB 10GbE adapteriams.** Dabar programa nustato adapterio tipą ir nustato teisingą „receive-ring“ raktinį žodį: `*ReceiveBuffers`→2048, skirtą PCIe tinklo plokštėms (Intel I219 ir pan.) arba „`ReceiveBufferLen`→256“ + „`PendingReceives`→64“ „Realtek“ **USB** 10GbE valdikliui (kuris nepateikia „`*ReceiveBuffers`“). Tikslinės vertės ribojamos pagal kiekvieno tvarkyklės nurodytą maksimumą (`NumericParameterMaxValue`), todėl niekada nerašoma reikšmė, kuri yra už ribų. Vykdykite iš **administratoriaus teisių** turinčio terminalo; kaip ir bet koks registru pagrįstas optimizavimas, pakeitimai įsigalioja po adapterio perkrovimo arba kompiuterio perkrovimo. Anksčiau minėtos rankinės `Set-NetAdapterAdvancedProperty` komandos lieka puiki alternatyva — jos taikomos iš karto (iš naujo priskiria adapterį) be perkrovimo.

### Tinklo pagrindai (visi LATTICE ryšiai)

- **Adresavimas:** ryšio vietinis `169.254.0.0/16` (GigE Vision LLA). Kompiuteris naudoja statinį adresą `169.254.x.x/16`; kameros ir DAQ-E pačios prisiskiria adresus tame pačiame diapazone. DHCP ar šliuzo nereikia.
- **Paketo dydis:**geriau rinktis „jumbo“ (9000), bet leiskite automatiniam tikrinimui jį nustatyti — jis iš naujo matuoja kiekvieną kartą prisijungus ir jau atsižvelgia į kameros 1500-baitų ICMP ribą per GVSP tyrimą, todėl nustato „jumbo“ dydį ten, kur laidinis ryšys iš tiesų jį perduoda. Nustatykite fiksuotą vertę su `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` tik tada, kai žinote geriau nei tyrimo funkcija, ir teikite pirmenybę komandai-komandą, o ne nuolatinį nustatymą: nustatymas apeina tikrinimą, taigi jei maršrutas iš tikrųjų negali perduoti 9000,**kiekvienas** duomenų surinkimas baigsis laiko limito pasiekimu su `SC_ERR_TIMEOUT -1011` (žr. [Aplinkos kintamieji](#environment-variables)).
- **RX žiedas keičia dydį priklausomai nuo `ReceiveBufferLen`:**esant numatytam `32` dydžiui, naudingas žiedas yra ~0,26 MB (per mažas bet kokiam daugiakameriniam duomenų srautui); esant maksimaliam `256` dydžiui, jis yra didelis (~13,5 MB, išmatuota laboratorijos 10GbE serveryje), suteikiant realią atsargą. Ar konfigūracija bus prijungta, nusprendžiama pagal išteklių naudojimą atsižvelgiančią priėmimo patikrą**ir** toliau pateiktą suvestinę perviršinio užsakymo patikrą — ne pagal grynąjį serijos ir žiedo dydžių palyginimą.

### „Array“ kadrų per sekundę (fps) ir serijos perdavimo modelis

Kaip skaityti „Array“ nustatymų skydelį (ir `lattice analyze-array` / „SDK“ `analyze_array_network`):

- **Serijos duomenys sumuojami pagal kiekvieną kamerą, atsižvelgiant į jos tikrąjį pikselių formatą.**Mono**M3M**kameros transliuoja**Mono12 (2 B/px)**;**M3C**Bayer kameros perduoda 8 arba 12 bitų srautą (TRI032S tyliai siunčia „BayerRG12“, net jei prašoma „BayerRG8“). Taigi 4 kamerų pilnas-rezoliucijos kadras yra**~12,6 MB, jei visos kameros yra 8 bitų, bet ~25 MB, jei naudojamos trys 12 bitų mono kameros**. Projekcija nustato kiekvienos kameros formatą pagal jos modelį (identiteto talpyklą), todėl duomenų srautas atitinka tai, ką iš tikrųjų perduoda laidas — o ne vienodą „BayerRG8“ prielaidą.
- **USB Ethernet adapterio greitis ribojamas iki 200 MB/s, nepriklausomai nuo jo techninių duomenų.** Efektyvumo lentelė, kuri paverčia ryšio greitį nuolatiniu skaičiumi, yra sudaryta remiantis PCIe; USB tinklo plokštė nurodo savo *Ethernet* ryšio greitį, tačiau ją riboja USB magistralė ir jos tvarkyklė. USB 10GbE raktas anksčiau pasiekdavo ~1063 MB/s „nuolatinį“ greitį — skaičių, kuris niekada nebuvo patikrintas — o dėl to susidariusio greičio svyravimo buvo sugadinta 6–18 % kadrų , nors vis tiek rodydavo tinkamą tikslinių kadrų per sekundę skaičių. USB jungiamieji tinklo adapteriai dabar yra apriboti iki **200 MB/s** kaip absoliutus (ribą nustato magistralė, todėl ji nepriklauso nuo techninių charakteristikų; USB 1 GbE adapteris pasiekia ~80 MB/s ir jam tai neturi įtakos). `wire_ceiling_source` pajėgumų įraše tai nurodo žodžiais, o `nic_is_usb` tai pažymi. Bet kuriuo atveju šį apribojimą galima perrašyti naudojant `--wire-ceiling-mbps`.
- **Praleidžiamumas priklauso nuo išteklių naudojimo, o ne nuo viso srauto palyginimo su žiedu.** Vienu metu perduodamas srautas turi tilpti tik į *laikinąjį užlaikytą duomenų kiekį* = `max(0, Σ per-cam arrival − host drain) × emit_window`, o ne visam srautui. Greito pagrindinio kompiuterio / lėto kameros struktūroje (**PCIe**10G pagrindinis kompiuteris + 4× 1 GbE kameros: atvykimas ≈ 320 MB/s, išsiuntimas ≈ 1063 MB/s) pagrindinis kompiuteris duomenis išsiunčia greičiau, nei kameros juos pripildo, užlaikymas ≈ 0, todėl pilnos raiškos simuliacijos siuntimas**leidžiamas**, nors 25 MB duomenų srautas viršija 13,5 MB žiedo ribą. Jei tas pačias keturias kameras prijungti prie**USB**10GbE adapterio, išsiuntimo greitis bus 200 MB/s, o ne 1063 — duomenų priėmimo greitis jį viršija, o praradimai pasireiškia kaip sugadinti kadrai, o ne kaip mažesnis kadrų dažnis. 1 GbE kompiuteryje kamerų 31,25 MB/s DLThr riba lemia, kad duomenų srautas viršija išsiuntimo greitį → sistema teisingai**blokuoja**(šios klasės blokavimui sumažinkite ROI arba naudokite binningą ≥ 2). Leidimas yra vienas iš**dviejų** jungiamųjų vartų — kitas yra toliau pateikta suvestinė perviršinio užsakymo patikra.
- **Prognozuojamas kadrų per sekundę skaičius (fps) yra konservatyvi serijinio išgavimo riba.**Šiuo metu kompiuterio duomenų paėmimo ciklas iš kiekvienos kameros buferio duomenis ištraukia**serijiniu būdu**(~po vieną išsiuntimo langą kiekvienai kamerai), todėl ciklas ribojamas `max(readout+emit, N × emit)`, o išsiuntimas kiekvienai kamerai apribotas kameros**prieigos jungtimi**(1 GbE ≈ 80 MB/s), o ne pagrindinio kompiuterio aukštynine jungtimi. 4 kamerų pilnos raiškos 12 bitų masyvo atveju tai sudaro**~2,8 kadrų per sekundę**, o išmatuotas ~2,7–3,0 kadrų per sekundę rodiklis sąmoningai**nepriklauso nuo ekspozicijos trukmės**, todėl prastai apšviestose scenose faktinis kadrų skaičius gali šiek tiek nukristi žemiau viršutinės ribos, kai ekspozicija pailgėja. Serijinis duomenų išgavimas yra tikrasis kadrų per sekundę ribotuvas; jo lygiagretinimas padidintų viršutinę ribą iki vieno kameros duomenų perdavimo greičio.
- **Bendras perviršinis užsakymas yra rimta jungimosi kliūtis.**Pralaidumo paskirstymas kiekvienai kamerai ribojamas iki**8 MB/s**(`ARRAY_PER_CAM_FLOOR_BPS`), taigi, kai pasiekiamas šis minimumas, bendras poreikis (`per_cam × N`) gali viršyti**susidūrimų saugumo ribą**(`sustained × sim_emit_factor`). Praktiniai maksimalūs ribiniai dydžiai 1 GbE tinkle:**6 kameros su 1500 MTU, 9 – su „jumbo“**. Ši riba priklauso tik nuo laidinio ryšio ir apatinės ribos – ji yra**nepriklausoma nuo rėmelio dydžio**, todėl**duomenų sugrupavimas ir mažesnės ROI sritys NEpadeda** (jos sumažina baitų skaičių viename *rėmelyje*, o ne GevSCPD tempu perduodamų baitų skaičių per *sekundę*); vienintelės išeitys – mažiau kamerų, „jumbo“ rėmeliai nuo pradžios iki galo arba greitesnis tinklo adapteris. Simptomas būtų GVSP paketų praradimas, o ne sklandus kadrų per sekundę (fps) sumažėjimas, todėl `analyze-array` nustato pasiekiamų kadrų per sekundę (fps) skaičių į nulį ir rodo `**OVER-SUBSCRIBED**`, o `array-connect` – su fiksuota skiriamąja geba **atsisako prisijungti** (kitu atveju „walk-down“ suskirsto kadrus į mažesnes grupes, o tai taip pat neišsprendžia šios klasės blokavimo). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` sumažina atsisakymo lygį iki garsaus įspėjimo, skirto testavimo darbams — žr. [Aplinkos kintamieji](#environment-variables).

### Masyvo būklė — kuri posistemė praranda kadrus

Prijungto masyvo `GET /api/camera/array/<array_id>/capability` turi aktyvų
`health` bloką, kuris peržiūrimas kas **10 sekundžių** laiko tarpu. Jis suskirsto kadrų praradimą
į dvi priežastis, kurioms reikia priešingų sprendimų, o ne praneša apie vieną „nepilną“
rodiklį, kuris nenurodo nė vienos iš jų:

| Laukelis | Ką tai reiškia | Kurioji posistemė |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (pagal serijinį numerį) | Kadras **atėjo, bet buvo struktūriškai sugadintas**— GVSP paketų praradimas. |**Tinklas**: laidų pralaidumas, tempas, NIC RX žiedas, MTU |
| `never_arrived_rate_pct` (pagal serijos numerį) | Kadras **visai nepasiekė**— kamera nesuveikė arba iš jos nieko neišėjo. |**Sukėlėjas / sinchronizacija**: M8 kabelis, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Blogiausias kiekvienos kameros duomenų perdavimo greitis. | — |
| `per_cam_rate_pct` | Bendras neišsamus rodiklis vienam fotoaparatui (abi priežastys kartu). | — |
| `stable_for_seconds` | Kiek laiko kiekvienas fotoaparatas buvo žemiau 0,01 %. | — |

Jei rodiklis viršija 5 %, užkulisinė sistema įrašo eilutę „`[array-health <id>] WARN`“, nurodydama padalijimą —
pirmojo pažeidimo, pasikeitus rimtumo lygiui, kartą per minutę, kol problema tęsiasi, ir vieną kartą, kai
ji išsprendžiama. Sugedusi pusė išspausdina įrašą „`[gvsp-corrupt <SN>]`“ po pirmojo atvejo kiekvienai kamerai ir
priežasties, tada apibendrinimą kas 60 s. Kiekvienas įvertinimas vis tiek patenka į užpakalinės sistemos žurnalo failą;
skaitikliai juda su kiekvienu buferiu, nepriklausomai nuo to, kas išspausdinama.

Tame pačiame įraše nurodomas skaičius, nuo kurio priklauso visas paskirstymas:

| Laukas | Ką reiškia |
| --- | --- |
| `wire_ceiling_mbps` | Galiojantis nuolatinis pagrindinio kompiuterio pralaidumo limitas, MB/s. |
| `wire_ceiling_source` | Iš kur gautas šis skaičius, žodžiais — pvz., `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` arba `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, kai `--wire-ceiling-mbps` (arba GUI lauke **Wire Budget** lauke) jį nustato. |
| `nic_is_usb` | `true` USB Ethernet adapteriui — žr. aukščiau nurodytą 200 MB/s ribą. |

**Kaip interpretuoti:** nelygi nuliui reikšmė `gvsp_corrupt_rate_pct`, kai `never_arrived_rate_pct` yra 0,
reiškia, kad sužadinimas ir kabelio sinchronizacija yra nepriekaištingi, o 100 % nuostolių tenka tinklo
kelyje — sumažinkite `--wire-ceiling-mbps` ir prisijunkite iš naujo. Atvirkštinis modelis rodo, kad problema yra
sinchronizavimo kabelyje arba suaktyvinimo linijoje.

> **`--target-fps` nėra veiksnys, lemiantis sugadintus rėmus.** GevSCPD tempas nustatomas
> vieną kartą prisijungus, todėl sumažinus trigerio dažnį keičiasi darbo ciklas, o ne
> vienalaikio siuntimo serijos dažnis. Išmatuotas 5 kartų sumažintas poreikis nepagerino situacijos;
> sumažinus laidą ribą nuo 240 iki 200 MB/s sumažino to paties įrenginio sugadintų kadrų skaičių nuo 10,4 %
> iki 0,00 %.

> **TRI032S programinėje įrangoje nėra automatinio srauto sumažinimo funkcijos.** Veikiantis masyvas
> negali pats išspręsti šios problemos; atjunkite ir vėl prijunkite, kad prisijungimo laiko pasirinkimo funkcija galėtų
> iš naujo suplanuoti veikimą pagal naują ribą.

### Simptomas → sprendimas

| Simptomas („Masyvo nustatymai“ / „Prijungimas“ / `analyze_array_network`) | Priežastis | Sprendimas |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` nustatytas į 32 (paprastai po tvarkyklės atnaujinimo) | Nustatykite `ReceiveBufferLen`→256, `PendingReceives`→64; vėl atidarykite skydelį (perkraukite foninę programą, jei ji įrašė į talpyklą seną žiedo dydį) |
| Įkrovos/išjungimo procesas užstringa; vėliau `Invalid class` WMI klaidos, tinklo plokštė neįsijungia, trūksta USB įrenginių | Senas 2016 m. „Realtek“ USB 10GbE tvarkyklė → BSOD `0x9F` → priverstinis išjungimas | Atnaujinkite adapterio tvarkyklę iki ≥ v10.67 (2026), tada iš naujo pritaikykite aukščiau nurodytus priėmimožiedo nustatymus, nurodytus aukščiau |
| Prijungimas pavyksta, bet grąžina mažesnę nei natūralią skiriamąją gebą | „Smart-prep“ automatiškai sumažino rėmelį, kad jis tilptų į laidą | Atnaujinkite ryšį / sutikite su sumažinimu / `--force-tier slip-emit-and-capture` |
| Masyvas praneša apie tinkamą tikslų kadrų per sekundę skaičių, bet pateikia tik jo dalį; `health.gvsp_corrupt_rate_pct` nelygus nuliui, `never_arrived_rate_pct` 0 | Kompiuterio numanomas laidų pralaidumo rezervas yra pervertintas, palyginti su tuo, ką jis iš tikrųjų gali išlaikyti (būdinga USB Ethernet adapteriams, siaurai PCIe juostai arba bendrai naudojamai struktūrai) | Prijunkite iš naujo nustatydami mažesnę `--wire-ceiling-mbps` reikšmę ir dar kartą patikrinkite būklės bloką. **Ne** `--target-fps` — „GevSCPD“ tempas nustatomas prisijungimo metu |
| Kamerų trūksta paskelbtose grupėse; `health.never_arrived_rate_pct` nelygus-nulis, `gvsp_corrupt_rate_pct` 0 | Trigerio / sinchronizavimo kelias — kameros nesuveikia, tai nėra tinklo problema | Patikrinkite M8 sinchronizavimo kabelį ir `--line`; įsitikinkite, kad kiekvienas narys yra įjungtas (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` viršytas `analyze-array`, arba atsisakymas prisijungti su fiksuotu skiriamuoju gebėjimu (`array over-subscribes the wire`) | Bendras poreikis vienai kamerai (8 MB/s riba × N kamerų) viršija susidūrimams saugią linijos ribą — 6 kameros pilna raiška 1 GbE tinkle su 1500 MTU, 9 su „jumbo“ rėmeliais | Mažiau kamerų, „jumbo“ rėmeliai nuo pradžios iki galo arba greitesnis tinklo adapteris. **ROI/binning NEPADĖS** (ribinė vertė nepriklauso nuo rėmelio dydžio). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` perrašoma bandymų stende (priimamas paketų praradimas) |

---

## `chloros-cli daq`

Spektrinio jutiklio komandos. Dvi klasės:
- **`pool-*`**— „HTTP“ ploni klientai, valdančios jutiklį per užpakalinės dalies nuolatinį rezervą.**Tai yra palaikomas būdas ir vienintelis, esantis išleistame „CLI“.** Užpakalinė dalis valdo perdavimą, todėl GUI, „CLI“ ir „SDK“ skriptai visi dalijasi vienu aktyviu identifikatoriumi, o ne konkuruoti dėl nuosekliojo prievado.
- **Visa kita**(`test`, `record`, `live`, `stream`, `connect`, `info`, `net`, `ota`, `sample-rate`, `calibrate`, `serve`, `ws`, `udp`, `mqtt`, `reflectance`, `login`, `logout`, `status`) — tiesioginė prieiga prie aparatinės įrangos, išsamiai aprašyta toliau. Jiems reikalingas „`daq`“ „Python“ paketas, kuris**nėra įtrauktas į jokį pateiktą artefaktą**: kompiliuotas „CLI“ jo neįtraukia (`scripts/Build-CLI.ps1` nustato `--nofollow-import-to=daq`, o transportai `pyserial` / `bleak` / `zeroconf` su juo), o PyPI SDK pakete jo taip pat nėra. Jie veikia tik iš šaltinio kopijos, todėl laikykite juos „MAPIR“ vidiniu kūrimo keliu, o ne kažkuo, ko reikėtų siekti.
- **`discover` / `list`** apima abu atvejus: tai yra tiesioginės aparatinės įrangos komandos iš šaltinio kopijos, tačiau išleistoje versijoje jos grįžta prie `pool-discover`, o užkulisiai atlieka skenavimą. Taigi skenavimas veikia visur — tai svarbu, nes tai vienintelis būdas sužinoti DAQ-M įrenginio BLE MAC adresą.

> **`chloros-cli daq --help`** (ir `-h` / `help`) išvardija `pool-*` pakomandas — pagalba sąmoningai nukreipiama į „pool“ klientą, kad atspindėtų komandas, kurios iš tikrųjų vykdomos. Jei išsiųstoje versijoje paleisite tiesioginę aparatinės įrangos pakomandą, ji bus nutraukta su aiškia klaida, nurodant trūkstamą paketą ir nukreipiant jus atgal į `pool-*`; niekas nesibaigia tyliai. (`discover` / `list` yra išimtis — jos nukreipia į `pool-discover` ir tiesiog veikia.)
>
> **Viskas, ko reikia klientui, pasiekiama per `pool-*`** — prisijungti, transliuoti, įrašyti kalibruotus „`.daq`“ failus ir keisti kondensatorių profilius. DAQ taip pat galima valdyti iš „Python“ naudojant „`chloros_sdk.connect_daq_sensor()`“, kuris naudoja tą patį bendrą kelią.

### DAQ jutiklio pirmojo prisijungimo darbo eiga

```bash
# 1. Smart-detect any DAQ on this machine (Ethernet → BLE → USB precedence)
chloros-cli daq connect

# 2. Detailed scan: every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U COM port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq discover                      # or: daq pool-discover
chloros-cli daq discover --only ble           # BLE only
chloros-cli daq discover --json               # machine-readable

# 3. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect           # smart-detect
chloros-cli daq pool-connect --port COM3                       # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 4. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 5. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 6. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 7. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### `pool-*` nuoroda

| Pakomanda | Paskirtis |
| --- | --- |
| `daq pool-connect` (smart-detect) | Atidaryti jutiklį užkulisiniame pulke. |
| `daq pool-connect --port PORT` | DAQ-U per konkretų nuoseklųjį prievadą. |
| `daq pool-connect --ble` | DAQ-M per BLE, automatinis MAC adresų nuskaitymas. |
| `daq pool-connect --mac MAC` | DAQ-M per BLE žinomu MAC (reiškia `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E per Ethernet su žinomu kompiuteriu. |
| `daq pool-connect --eth` | DAQ-E per Ethernet, kompiuteris aptiktas automatiškai (mDNS + ARP atsarginis variantas; veikia su tuščia ARP talpykla adresuose Windows ir Linux). |
| `daq pool-connect --integration-time MS --frame-avg N --no-ae` | Nustatyti integracijos langą / AE būseną. |
| `daq pool-connect --no-stream` | Prisijungti, bet dar nepradėti transliacijos (tęsti su `pool-stream --start`). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | „Cap“ korekcijos profilis. Numatytasis nustatymas serveryje yra `sunshine_cosine`. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Nuskaityti kiekvieną perdavimo kanalą, ieškant jutiklių, prie kurių galima prisijungti, neprisijungiant. **Taip galima rasti DAQ-M BLE MAC adresą.** `daq discover` / `daq list` išsiųstose versijose automatiškai nukreipia čia. Jau atidaryti sensoriai rezervo sąraše nerodomi — prijungtas DAQ-M nustoja siųsti skelbimus — todėl tiems naudokite `pool-list`. |
| `daq pool-list` | Rodyti visus jutiklius užkulisiniame sąraše. |
| `daq pool-disconnect --sensor-id ID [--all]` | Atleisti. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N naujausi spektro kadrai. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Tęsti / pristabdyti srautą. |
| `daq pool-record --sensor-id ID [--duration SEC] [--output DIR] [--device-name NAME] [--stop]` | Pradėti / sustabdyti .daq įrašymą. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Pakeisti viršutinės ribos korekcijos profilį vykdymo metu. |

### Tiesioginės aparatinės įrangos pakomandos (tik šaltinio išsikėlimui — nėra išleistose versijose)

> Pateikta išsamumo dėlei. Joms reikalingas „Python“ paketas „`daq`“ bei „`pyserial`“ / „`bleak`“ / „`zeroconf`“, kurių nė viena nėra įtrauktos į kompiliuotą „CLI“ arba „PyPI“ SDK — jos veikia tik iš MAPIR šaltinio kopijos. **Jei naudojate išleistą „Chloros“ versiją, vietoj to naudokite aukščiau nurodytas `pool-*` komandas**; jos apima prisijungimą, srautą, įrašymą ir kameros pasirinkimą.

```bash
chloros-cli daq test --port COM3                           # Verify connection
chloros-cli daq connect --eth                              # Smart-detect over ETH
chloros-cli daq info --eth-host daq-e-xxx.local            # Device summary as JSON
chloros-cli daq discover --only usb,ble --timeout 5        # Scan local interfaces
chloros-cli daq list                                       # Alias of discover
# ^ discover/list are the exception in this section: in a shipped build they
#   fall back to `pool-discover` (the backend does the scan), so they work
#   without a source checkout. The only difference is that the fallback needs
#   the Chloros backend running, as all pool-* commands do.

# Streaming JSON Lines to stdout (pipeable)
chloros-cli daq stream --port COM3 --format jsonl --photometrics

# Record to .daq for 60 seconds
chloros-cli daq record --port COM3 --duration 60 -o ~/Documents/spectra/

# Live spectrum visualization in a window
chloros-cli daq live --port COM3 --record

# Dual-sensor reflectance (ambient + object) → JSON Lines
chloros-cli daq reflectance \
  --ambient-eth-host daq-e-field.local \
  --object-eth-host daq-e-canopy.local \
  --record -o ~/Documents/reflectance/

# Convenience: pick integration_time + frame_avg for a target rate
chloros-cli daq sample-rate --port COM3 --target-hz 5

# Calibration profile management
chloros-cli daq calibrate --port COM3 --list
chloros-cli daq calibrate --port COM3 --set field_calibration_2026_05

# DAQ-E network config (mDNS auto-discovers the host)
chloros-cli daq net --eth-host daq-e-xxx.local set-ip --mode static --ip 192.168.2.20
chloros-cli daq net --eth-host daq-e-xxx.local set-name "sky-sensor"
chloros-cli daq net --eth-host daq-e-xxx.local set-ptp --enabled true --domain 0
chloros-cli daq net --eth-host daq-e-xxx.local set-auto-stream true          # auto-stream on boot
chloros-cli daq net --eth-host daq-e-xxx.local set-require-signature         # require factory-signed cal (fw v1.6.0+; refused while the held cal is unsigned)
chloros-cli daq net --eth-host daq-e-xxx.local set-time                      # push host clock (refused when PTP SLAVE)
chloros-cli daq net --eth-host daq-e-xxx.local set-auth-token --current "" --new "s3cret"   # control-channel auth ("" new = disable)
chloros-cli daq net --eth-host daq-e-xxx.local set-ota-password "newpass"    # change OTA password (min 4 chars)
chloros-cli daq net --eth-host daq-e-xxx.local factory-reset                 # clear all NVS settings and reboot
chloros-cli daq net --eth-host daq-e-xxx.local reboot

# OTA firmware update
chloros-cli daq ota --eth-host daq-e-xxx.local \
  --firmware daq_e_1.21.bin --password mapir-daq-e

# Bridge spectra to other protocols
chloros-cli daq serve --port COM3 --tcp-port 9000           # TCP JSON-lines
chloros-cli daq ws    --port COM3 --ws-port 9001            # WebSocket
chloros-cli daq udp   --port COM3 --udp-port 9002           # UDP broadcast
chloros-cli daq mqtt  --port COM3 --broker mqtt.example.com --topic daq/spectrum
```

---

## `chloros-cli project`

Atidaryti, prisijungti prie ir valdyti išsaugotą „Chloros“ projektą (aplanką su „`cameras.json`“ + „`sensors.json`“ + „`project.json`“). Viskas nukreipiama per užkulisinę sistemą, todėl GUI ir „CLI“ sukuria identišką įrangos būseną.

### Papildomų komandų žinynas

| Papildoma komanda | Paskirtis |
| --- | --- |
| `project open PATH` | Atspausdinti projekto įrenginių sąrašą (kameros, masyvai, jutikliai). |
| `project devices PATH [--reconnect]` | Rodyti sąrašą arba pakartotinai paleisti paiešką. |
| `project connect PATH [--cameras-only] [--sensors-only]` | Prijungti kiekvieną išsaugotą kamerą / masyvą / jutiklį. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | Vienkartinis kadrų užfiksavimas iš nurodytos kameros ar masyvo. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | N kadrų serija iš nurodytos kameros ar masyvo (`-n/--count` numatytasis 5; `-i/--interval` sekundės tarp kadrų, numatytasis 0). Matavimų grupės serijos pašalina pasikartojančių sinchronizuotų grupių dubliavimus (pasenimo stebėjimo mechanizmas), todėl dalinai cikliškai veikianti matavimų grupė negali grąžinti N vieno kadro kopijų; išspausdina kiekvienos iteracijos rezultatus. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--poll-interval S]` | Srauto perkėlimas į diską per užkulisinę užduotį. `--poll-interval` = sekundės tarp `/stats` apklausų (numatyta reikšmė 2,0). |
| `project sensor read PATH NAME [--json]` | Naujausias spektro kadras. |
| `project sensor log PATH NAME --seconds SEC [-o DIR] [--device-name NAME]` | Įrašyti .daq failą. |
| `project run PATH RECIPE.yaml` | Vykdyti YAML/JSON duomenų surinkimo receptą. `--dry-run` atlieka patikrinimą nevykdydamas. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Apskaičiuoti masyvo suderinimą — žr. [žemiau pateiktą žymių lentelę](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Išspausdinti dabartinį suderinimo profilį. |
| `project align clear PATH NAME` | Ištrinti talpyklos profilį. |
| `project align tweak PATH NAME --serial SN --dx N --dy N --rotation-deg N --scale N` | Pakeisti vieno pavaldinio transformaciją. |
| `project align export PATH NAME --to FILE` | Išsaugoti profilį į „JSON“. |
| `project align import PATH NAME --from FILE [--no-validate]` | Įkelti išsaugotą profilį. |

#### `project align calibrate` parinktys

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Suderinimo metodas. **Šios rašybos skiriasi nuo `lattice align-calibrate`**, kuri priima trumpąsias formas `orb` / `akaze` / `phase`; šios dvi komandos nėra tarpusavyje keičiamos naudojant šį parametrą. |
| `--model {translation, rigid, affine, homography}` | `affine` | Pritaikyti modelį. |
| `--frames N` | `1` | Sinchronizuoti kadrų momentiniai vaizdai su vidurkiu. |
| `--reference SN` | pagrindinė | Etaloninės kameros serijos numeris; visi kiti elementai yra deformuojami pagal ją. |
| `--max-features N` | `5000` | ORB savybiųskaičiaus riba. |
| `--ratio-threshold F` | `0.75` | Lowe&#x27;o santykio testas. |
| `--ransac-threshold-px F` | `3.0` | RANSAC vidinių taškų slenkstis. |
| `--min-matches N` | `15` | **Kokybės slenkstis** — atmesti sprendimą, jei atitikimų skaičius mažesnis už šį vidinių taškų skaičių. |
| `--max-reproj-err-px F` | `4.0` | **Kokybės riba** — atmesti sprendimą, jei RMS reprojekcijos paklaida didesnė už šią vertę. |
| `--checkerboard RxC` | — | Plokštės geometrija, skirta `--method checkerboard`, pvz., `9x6`. |
| `--name PROFILE` | tuščias | Profilio pavadinimas, įterptas į išsaugotą „JSON“. **Ne masyvo pavadinimas** — tai yra pozicinis `NAME`. |

Dėl šių dviejų kokybės patikrinimų kalibravimas gali sėkmingai išspręsti užduotį, bet vis tiek
atsisakyti išsaugoti: profilis, kuris neatitinka vieno iš jų, tyliai neteisingai registruotų kiekvieną
vėlesnį užfiksavimą, todėl jis atmetamas, o ne išsaugomas.

### Pavyzdžiai

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### Recepto DSL

`project run RECIPE.yaml` priima YAML arba „JSON“ failą, aprašantį veiksmų seką:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        exposure_auto: "Off"
        gain: 6.0
        gain_auto: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

Palaikomi veiksmai: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. Veiksmas `burst` priima `name` (privaloma), `count` (numatyta reikšmė – 5), `interval` (sekundės, numatytasis 0), `output`, `format` ir `settings` (tokie patys nustatymai kiekvienai kamerai kaip ir `apply`); serijinių kadrų sekcijos naudoja tą patį naujai sinchronizuotą grupės stebėjimo mechanizmą kaip ir `project burst`.

Paleiskite:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

---

## Aplinkos kintamieji

| Kintamasis | Poveikis |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Perrašo užkulisinį „URL“ (numatyta reikšmė – `http://127.0.0.1:5000`) — **taikoma tik komandų šeimoms `lattice`, `project` ir `daq pool-*` komandų šeimoms.** Pagrindinės komandos (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) priskiria komandą `http://127.0.0.1:<port>` ir ignoruoja šį kintamąjį (IPv4 literatas apeina „Windows“ `localhost`→`::1` ~2 s baudą už kiekvieną užklausą), todėl jie visada nukreipiami į vietinį kompiuterį. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | `1` sumažina masyvo per didelio užimtumo prisijungimo atmetimo (bendras paklausos kiekis vienam CAM &gt; susidūrimų saugumo ribą su `pin_resolution`) į garsų įspėjimą ir tęsimą, priimdamas GVSP paketų praradimą. Skirta tik bandymams — žr. [Masyvo fps ir srauto modelis](#array-fps--burst-model). |
| `CHLOROS_CLI_MODE` | Nustato pats „CLI“; nurodo užpakalinės dalies sistemai įjungti lygiagretų apdorojimą. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` praleidžia GVSP atsarginį zondą (tik ICMP rezultatai). **Tai išjungia „jumbo“, o ne tik sumažina žurnalo įrašų skaičių** — kamera atsako į DF pingus tik iki 1500 kiekvienu maršrutu, todėl tik šis patikrinimas gali aptikti „jumbo“ paketus. Sutaupo ~1 s vienai kamerai vienam prisijungimui; kainuoja ~1,45× laidinio ryšio ribą, jei tinklas *galėtų* perduoti „jumbo“ paketus. „SDK“ įspėja, kai jį nustatote. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | Nustato GVSP paketo dydį į N baitų; visiškai praleidžia tikrinimą. Geriau naudoti nustatymą pagal komandą (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`), nei nustatyti jį visam laikui: fiksuotas dydis neleidžia prisitaikyti prie priešais esančio tinklo, o 9000 fiksavimas kelyje, kuris negali perduoti „jumbo“, priverčia **kiekvieną** duomenų surinkimą su `SC_ERR_TIMEOUT -1011`. |
| `TMPDIR` (Linux) | Perrašyti „Nuitka“ vieno failo išgavimo katalogą. „CLI“ automatiškai naudoja „`/mnt/ssd/tmp`“, jei toks yra. |

---

## Išėjimo kodai

| Kodas | Reikšmė |
| --- | --- |
| `0` | Sėkmė. |
| `1` | Bendro pobūdžio klaida (dauguma pakomandų klaidų). |
| `2` | Argumentų klaida. |
| `130` | Nutraukta spaudus Ctrl+C. |

---

## Trikčių šalinimo patarimai

- **„Reikia prisijungti“** → Šiame kompiuteryje vieną kartą paleiskite „`chloros-cli login EMAIL PASSWORD`“.
- **„nepasiekiamas užpakalinis modulis“** → Paleiskite „Chloros“ darbalaukio programą arba tiesiogiai paleiskite užpakalinės sistemos vykdomąjį failą (`chloros-backend`), arba, jei jungiatės nuotoliniu būdu, patikrinkite `CHLOROS_BACKEND_URL`.
- **`lattice` komandos nepavyksta dėl pranešimo „LATTICE kameros tvarkyklės nerastos“** → „Arena“ SDK vykdymo aplinka nėra įdiegta; „CLI“ pateikiamas su `win32api`, įtrauktu į paketą Windows, tačiau C vykdymo aplinka yra GUI diegimo programos dalis.
- **„Array connect“ / „Array Settings“ rodo „FRAMES WILL DROP“ arba „Reduce ROI to enable“** → Pagrindinio kompiuterio tinklo plokštės (NIC) priėmimo žiedas yra per mažas (paprastai po tinklo plokštės tvarkyklės atnaujinimo nustatomas į 32). Žr. [Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](#host-nic-setup--tuning-lattice-arrays) — nustatykite `ReceiveBufferLen=256`, `PendingReceives=64`.
- **Kompiuteris užstringa paleidžiant iš naujo / išjungiant, tada WMI rodo `Invalid class` / tinklo plokštė neįjungiama / trūksta USB įrenginių** → Pasenęs USB 10GbE adapterio tvarkyklės versija sukelia `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atnaujinkite adapterio tvarkyklę — žr. [Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](#host-nic-setup--tuning-lattice-arrays).
- **„Jetson“ keitimo įspėjimas** → Pridėkite failąpalaikomą keitimo sritį; „CLI“ išspausdina tikslias `fallocate` / `swapon` komandas.
- **Trūksta tiesioginių DAQ komandų** → Kaip tikėtasi: pateiktame `chloros-cli` sąraše sąmoningai neįtrauktas `daq` paketą, todėl yra tik `pool-*` (PyPI SDK jo taip pat neturi). Naudokite `pool-*`, kuris valdo tą patį jutiklį per užkurtį, arba `chloros_sdk.connect_daq_sensor()` iš „Python“.

---

## Taip pat žr.

- [„Python“ „SDK“ žinynas](sdk-reference.md) — programinis kiekvienos „CLI“ komandos atitikmuo.
- [DAQ jutiklių vadovas](../daq/README.md) — konkrečių jutiklių prijungimas ir kalibravimas.
- Dokumentai internete: `https://mapir.gitbook.io/chloros/cli`
