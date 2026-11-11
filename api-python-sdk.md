# API: Python SDK

{% hint style="info" %}
**Ieškote išsamios API specifikacijos?** Šis puslapis yra praktinis vadovas. Visos viešos klasės, metodai, tikslios parašai ir pavyzdžiai, kuriuos galima nukopijuoti ir įklijuoti, yra pateikti [SDK nuorodoje](reference/sdk-reference.md), kuri yra optimizuota dirbant su AI asistentais.**Dirbate su AI asistentu?** Įklijuokite šį URL į pokalbio langą, kad jis turėtų visą, naujausią Chloros 1.3.0 API:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Kiekvienas šio vadovo puslapis yra prieinamas kaip neapdorotas „Markdown“ failas, kurio pavadinimas susideda iš mažųjų raidžių slugo + `.md`, o visas vadovas yra indeksuotas adresu `https://mapir.gitbook.io/chloros/llms.txt`.
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` „PyPI“ svetainėje) iš Python valdo visas darbalaukio programos funkcijas: vaizdų apdorojimą partijomis, „LATTICE“ kameros ir matricos valdymą realiuoju laiku, DAQ šviesos jutiklių sesijas ir išsaugotų projektų automatizavimą. Tai plonas sluoksnis, uždengiantis tą patį vietinį užkurtį, kurį naudoja GUI ir CLI (HTTP, `127.0.0.1:5000`), todėl veikimas visose trijose sąsajose yra identiškas.

## Įdiegimas

SDK’as pateikiamas kartu su Chloros darbalaukio paketu, kuris taip pat suteikia apdorojimo užkurtį ir aparatinės įrangos vykdymo aplinką, ir visada atitinka jo versiją.

**1 žingsnis — Įdiekite Chloros.** Windows: paleiskite darbalaukio diegimo programą (numatytoji vieta – `C:\Program Files\Chloros\`) iš [Atsisiuntimo](download.md) puslapio. Linux: įdiekite paketą „`.deb`“ naudodami „`sudo apt install ./chloros_1.3.0_<arch>.deb`“ ([Linux diegimas](linux/linux-installation.md)).**2 žingsnis — Patikrinkite SDK** (Python 3.7+). Paprastai nieko nereikia diegti:

* **Windows:** diegimo programa įdiegia pridedamą SDK „wheel“ failą į jūsų sistemos Python (jei Python nėra įdiegtas, šis žingsnis praleidžiamas).
* **Linux:** `.deb` įdiegia SDK, skirtą sistemos `python3` versijai, į `/usr/lib/python3/dist-packages`. Paketas rekomenduoja šiuos reikalavimus: `python3-numpy` ir `python3-requests`; `apt install` juos įtraukia, o `dpkg -i` – ne.
* **„virtualenv“** nemato sistemos paketų; į jį įdiekite komplekte esantį „wheel“ failą: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` Linux sistemoje arba `chloros_sdk-*.whl`, esantį Windows diegimo aplankoje esančiame „`sdk`“ aplanke (pagal numatytuosius nustatymus – `C:\Program Files\Chloros\sdk\`).

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # Windows: py -c "…"
```

Kompiuteryje, kuriame nėra Chloros paketo, kurio SDK valdo kito kompiuterio užkurtį (žr. [Ką gaunate, įdiegę tik per „pip“](#what-a-pip-only-install-gets)), įdiekite jį iš PyPI: `pip install chloros-sdk`. PyPI atnaujinamas išleidžiant naujas versijas, todėl jame visada yra naujausia stabili versija.

**3 žingsnis — Prisijunkite vieną kartą kiekviename kompiuteryje:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Prisijungimo duomenys išsaugomi `~/.chloros/` (abiejose platformose). Windows sistemoje taip pat galite prisijungti per darbalaukio programėlės skirtuką „Vartotojas“ <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">. SDK reikalauja mokamo „Chloros+“ plano — žr. [Licencijos reikalavimus](#license-requirement) žemiau.

| Reikalavimas | Išsami informacija |
| --- | --- |
| **Įdiegtas Chloros** | Windows: darbalaukio diegimo programa; Linux: `.deb` paketas (apima užkulisinį binarinį failą ir SDK) |
| **Python** | 3.7 ar naujesnė versija (sukurta ir išbandyta su 3.10) |
| **Operacinė sistema** | Windows 10/11 (64 bitų), „Ubuntu“ 22.04 LTS ar naujesnė versija arba „NVIDIA Jetson“ (JetPack 6) |
| **Licencija** | Aktyvus „Chloros+“ prisijungimas, bet kuris mokamas planas („Copper“ ar aukštesnis) |

## 60 sekundžių pergalė

Vienu iškvietimu sukuriamas projektas, importuojamas aplankas, konfigūruojamas apdorojimas ir paleidžiamas apdorojimo procesas — jei foninis procesas dar neveikia, jis paleidžiamas automatiškai:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)
```

(Linux sistemoje naudokite Linux kelius: `/home/user/drone_images/flight001`. SDK veikia vienodai abiejose platformose.)

Apdorojate „LATTICE“ įrašų aplanką? Naudokite „LATTICE“ suderinamą apgaubą — ji taiko tinkamus numatytuosius nustatymus (be skydelio tikslo aptikimo, standartinis „debayer“):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — visiškas apdorojimo grandinės valdymas

Jei reikia atlikti daugiau nei vienos eilutės komandą, naudokite `ChlorosLocal`. Ji naudoja jau veikiantį užkurtį (darbalaukio programos) arba pirmą kartą naudojant paleidžia įdiegtąjį (`auto_start_backend=True`; Windows sistemoje iš Chloros diegimo aplanko „`backend`“ aplanko, pagal numatytuosius nustatymus — „`C:\Program Files\Chloros\backend\`“), sukuria ir konfigūruoja projektus, stebi vykdymo eigą ir pateikia vykdymo pabaigos ataskaitą.

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

{% hint style="info" %}
Palikite numatytąjį „`http://127.0.0.1:5000`“, o ne pakeiskite jį į „`localhost`“ — Windows sistemoje `localhost` pirmiausia išsprendžiamas kaip `::1` ir kiekvienam užklausimui, siunčiamam į tik IPv4 palaikantį užkulisinį modulį, sugaišta apie 2 sekundes.
{% endhint %}

Naudokite jį kaip konteksto tvarkyklę. Išėjus iš bloko, baigiamas šio egzemplioriaus naudojimasis užpakaline sistema, o ne pati užpakalinė sistema: ji toliau veikia kitam skriptui ir savaime sustabdo veiklą po 30 minučių nenaudojimo. „`shutdown_backend()`“ sustabdo vieną šio egzemplioriaus paleistą procesą.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` priima šiuos raktinius žodžius: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation` ir `custom_settings`. Pagrindinės reikšmės:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (8-bit)"            # 8-bit TIFF; what all-8-bit images get for a deeper format
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

LATTICE specifiniai reguliatoriai (`input_level`, `radiometric_output`, `array_alignment*` šeima) yra aprašyti su išsamiomis verčių lentelėmis [SDK nuorodoje](reference/sdk-reference.md#supported-values).

### Vykdymo eigos stebėjimas

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Vykdymo pabaigos santraukos skaitymas — ir tuščių vykdymų aptikimas

Užbaigus vykdymą, `process()` prideda užkulisio apdorojimo santrauką kaip `result["summary"]`. Kiekvienas įrašas `summary["hints"]` yra pilnas sakinys, paaiškinantis viską, kas verta dėmesio — pavyzdžiui, kodėl vykdymo metu nebuvo gauta jokių rezultatų — be to, kiekviena užuomina taip pat iš naujo pateikiama kaip Python `UserWarning`, todėl tušti vykdymai diagnozuojami automatiškai, net jei niekada nepatikrinate žodyno:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` nesukelia išimties, kai vykdymo metu nesukuriama jokių vaizdų.** Tai vienintelė vieta, kurioje SDK ir CLI sąmoningai skiriasi: `chloros-cli process` traktuoja situaciją „buvo užklausti produktai, bet nė vienas nebuvo įrašytas“ kaip nesėkmę ir baigia veikimą su nelygiu nuliui rezultatu, tuo tarpu SDK grįžta įprastai ir apie šią būklę praneša per `summary` / užuominas. Jei jūsų procesų grandinė turėtų sustoti, kai vykdymo rezultatas yra tuščias, patikrinkite tai patys — peržiūrėkite `summary` (arba suskaičiuokite failus projekto aplanke), o ne pasikliaukite išimtimi.
{% endhint %}

## „Smart Connect“ — veikianti įranga

Trys pagalbiniai procesai atidaro nuolatines sesijas užkulisinės sistemos įrangos pulke — tame pačiame pulke, kurį naudoja CLI, kiti skriptai ir GUI kameros skirtukai, todėl SDK skriptai gali veikti kartu su jais nesivaržydami dėl serijinių prievadų ar tinklo pralaidumo. Visi trys automatiškai paleidžia vietinį užkulisinį procesą, jei toks neveikia. GUI DAQ skydelis nėra šio rezervo dalis: prieš atidarydami šviesos jutiklį iš skripto, uždarykite jį GUI.

### Viena „LATTICE“ kamera — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

{% hint style="info" %}
**Santykiniai keliai nukreipia į vietą, kurioje vykdomas jūsų skriptas (pakeista versijoje 1.3.0).** Naudojant vietinį užkurtį, santykinis `output_dir` (įskaitant numatytąjį `"output"`) — ir kiti keliai, kuriuos atidaro užkurtis (`burst_dir`, `profile_path`, `save_path`, `mask_path`) — nustatomi pagal jūsų skripto darbo katalogą, todėl `cam.capture("output/")` rašo į `<current folder>/output/`. Versijoje 1.2.0 įdiegtas užpakalinis modulis juos išsprendė savo įdiegimo aplanke ir grąžino klaidą „Prieiga uždrausta“. Naudojant nuotolinį užpakalinį modulį, keliai siunčiami taip, kaip įvesti.
{% endhint %}

### Sinchronizuotas masyvas — `connect_array`

`connect_array` yra rekomenduojamas pradinis taškas daugiakamerinėms sistemoms. Jis vykdo tą patį „smart-prep“ srautą kaip ir GUI: tinklo analizę, automatinį sinchronizavimo lygio pasirinkimą, PTP laiko sinchronizavimą, pikselių formato pasirinkimą kiekvienai kamerai, AE sėjos nustatymą ir GPIO trigerio įjungimą. **Pirmoji serijinė kamera yra pagrindinė** (ji siunčia aparatinio trigerio impulsą); likusios yra pavaldžios.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Pridėkite `smart=True` prie bet kurio masyvo įrašymo, kad prieš suaktyvinant lauktumėte, kol automatinė ekspozicija nusistovės visose kamerose.

Nauja 1.3.0 versijoje: `ArraySession` taip pat tiesiogiai pateikia sinchronizuotus kadrus, nerašydamas failų:

* „`arr.grab()`“ grąžina po vieną kadrą iš kiekvienos kameros, gautus iš vieno sužadinimo, kaip „numpy“ masyvus („`{serial: array}`“); `.group_id` yra bendras suaktyvinimas, o `.errors` nurodo bet kurią neįtrauktą kamerą.
* `arr.frame_stream(fps=5.0)` pateikia vieną tokį rinkinį per kiekvieną ciklą, skirtą tiesioginiam apdorojimui jūsų pačių apdorojimo grandinėje.
* `arr.continuous("output/")` paleidžia pačios užkulisinės sistemos nuolatinį fiksavimą (GUI „Nuolatinis“ režimas) ir grąžina `ContinuousHandle` su `status()`, `wait()` ir `stop()` — naudokite jį vietoj fiksavimo ciklo, kai reikia, kad kiekvienas sužadinimo signalas būtų išsaugotas dideliu dažniu.

Apie fiksavimo režimus („Vienkartinis“ / „Nuolatinis“ / „Intervalinis“ / „Greičiausias“), įrašymo įrenginius, „burst-to-video“ ir masyvo suderinimą skaitykite [SDK žinyne](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### DAQ šviesos jutiklis — `connect_daq_sensor`

Jei argumentų nenurodyta, `connect_daq_sensor()` automatiškai nustato perdavimo būdą (pirmenybė: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Kiekviename kadre perduodami 135 taškų `spectrum` (W/m²/nm, kai kalibruota), `is_saturated` žymė ir CIE `x`, `y`, `z`. Norėdami priskirti konkretų jutiklį ar perdavimo būdą, perduokite vieną aiškų nurodymą:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Atkreipkite dėmesį, kad viršutinės ribos koregavimo profiliai (`cap_id`) **nėra** SDK reguliatorius — juos pasirinkite naudodami `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap`.

### Išsaugoti projektai — `open_project`

Išsaugotas Chloros projektas išlaiko prijungtą įrangą (`cameras.json` + `sensors.json` kartu su `project.json`), o „`chloros_sdk.open_project(path)`“ gali iš karto vėl prijungti savo kameras, matricas ir šviesos jutiklius bei valdyti įrašymą pagal įrenginio pavadinimą. Šviesos jutikliai vėl prisijungia per veikiantį Chloros užkulisinį modulį, išlaikydami savo išsaugotus nustatymus ir ribą. Žr. [Projekto automatizavimas](reference/sdk-reference.md#project-automation--chlorosproject) nuorodoje.

## Ką gauna tik „pip“ įdiegimas

Prieš naudodami aparatinės įrangos paviršius, patikrinkite modulio lygio prieinamumo žymes:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

Kompiuteryje, kuriame yra **tik** „`pip install chloros-sdk`“ ir nėra Chloros darbalaukio paketo:

* `ChlorosLocal`, `process_folder` ir `process_lattice_capture` **neveikia** — jiems reikalingas užkulisinis binarinis failas, kuris pateikiamas darbalaukio diegimo programoje.
* „Smart-connect“ pagalbinės programos (`connect_camera`, `connect_array`, `connect_daq_sensor`) yra grynai HTTP klientai, todėl jos veikia su kito kompiuterio užkulisiniu komponentu — tačiau pateikti užpakaliniai moduliai prisijungia tik prie kilpos, todėl prievadą turite nukreipti patys (pvz., `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) ir perduoti `backend_url="http://127.0.0.1:5000"` kartu su `auto_start_backend=False`. Žr. [Nuotolinio užpakalinio serverio režimas](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Tiesiogiai su aparatine įranga susijusios LATTICE klasės (`LatticeCamera`, `CameraPool`, …) importuojamos, tačiau joms reikalinga „Arena“ SDK-vykdymo aplinka iš darbalaukio paketo — be jos `CAMERA_AVAILABLE` yra `False`.
* DAQ jutikliai valdomi tik per (tuneliuotą) užkurtį — `connect_daq_sensor()` arba projekto `proj.sensors`; užkurtis atidaro jutiklį savo kompiuteryje.

## AI asistentai — `chloros-mcp`

SDK&#x27;e yra `chloros-mcp` – MCP serveris, leidžiantis „Claude Desktop“, „Claude Code“, „Cursor“ ir kitiems MCP klientams valdyti Chloros: apdoroti vaizdus ir valdyti „LATTICE“ kameras, matricas bei DAQ šviesos jutiklius. Jo įrankiai apima visas šio SDK funkcijas. Jis įdiegiamas kartu su SDK, todėl „Claude Code“ nustatymui pakanka vienos eilutės:

```bash
claude mcp add chloros -- chloros-mcp
```

Kiti klientai: [Chloros naudojimas su AI asistentais](ai-assistants.md). Parinktys ir visi įrankiai: [MCP serveris (AI asistentai)](reference/sdk-reference.md#mcp-server-ai-assistants).

## Licencijos reikalavimai

Norint naudotis SDK, reikia turėti aktyvų „Chloros+“ prisijungimą bet kuriame mokamame lygyje — **„Copper“ ar aukštesniame**(„Copper“ / „Bronze“ / „Silver“ / „Gold“); nemokamas „Iron“ planas nesuteikia prieigos prie SDK / CLI. Šis reikalavimas taikomas**serverio pusėje**: kiekvienas SDK užklausimas turi būti susijęs su aktyvia sesija ir mokamu planu, kitaip serveris grąžina klaidą `403` / `PLAN_UPGRADE_REQUIRED` (kuris pagal `ChlorosLocal` generuojamas kaip `ChlorosLicenseError`, o pagal `connect_*` pagalbines funkcijas – kaip `ChlorosConnectError`). Atsijungęs skambintojas gauna `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — pakartotinis `chloros-cli login` paleidimas išsprendžia pirmąjį atvejį, bet ne antrąjį.

Naudojimas neprisijungus veikia plano atidėjimo laikotarpiu: pakopa nuskaitoma iš serverio patvirtinimo talpyklos (5 minutės) arba iš pasirašytos, prie įrenginio priskirtos licencijos talpyklos (30 dienų mėnesiniams planams; iki prenumeratos galiojimo pabaigos metiniams planams). Pasibaigus atidėjimo laikotarpiui, planas pakeičiamas į nemokamą, o prieiga prie SDK sustabdoma, kol kompiuteris bent kartą prisijungs prie serverio. `chloros-cli status` lieka pasiekiamas nemokamoje pakopoje, todėl priežastis visada matoma. Žr. [„Chloros+“ prisijungimas](chloros+-login.md).

## Išimtys

Apsaugokite bazinę klasę, kad būtų tvarkomi „visi Chloros veikimo sutrikimai“:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

Visos duomenų srauto išimtys (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) kyla iš `ChlorosError`. Vienas niuansas: `ChlorosConnectError` — sukeliamas tik `connect_camera` / `connect_array` / `connect_daq_sensor` — kyla iš paprastojo `Exception`, **ne** iš `ChlorosError`, todėl `except ChlorosError` jo neaptiks. Pilna hierarchija pateikta [SDK žinyne](reference/sdk-reference.md#exceptions).

## Taip pat žr.

* [SDK žinynas](reference/sdk-reference.md) — išsamus API sąsajos aprašymas, optimizuotas AI asistentams.
* [CLI žinynas](reference/cli-reference.md) — kiekviena CLI pakomanda atitinka SDK iškvietą.
* [Chloros naudojimas su AI asistentais](ai-assistants.md) — „`chloros-mcp`“ nustatymo ir komandų eilutės pavyzdžiai.
* [Atsisiųsti](download.md) — diegimo programos Windows ir Linux sistemoms.
