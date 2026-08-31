# API : Python SDK

{% hint style="info" %}
**Ieškote išsamios informacijos apie API?** Šis puslapis yra praktinis vadovas. Visos viešos klasės, metodai, tikslios parašai ir pavyzdžiai, kuriuos galima nukopijuoti ir įklijuoti, yra [SDK nuorodoje](reference/sdk-reference.md), kuri yra pritaikyta dirbti su AI asistentais.**Dirbate su AI asistentu?** Įklijuokite šį URL į pokalbio langą, kad jis turėtų visą, naujausią Chloros 1.2.0 API:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Kiekvienas šio vadovo puslapis yra prieinamas kaip neapdorotas „Markdown“ failas, kurio pavadinimas susideda iš mažosiomis raidėmis parašyto slugo + `.md`, o visas vadovas yra indeksuotas adresu `https://mapir.gitbook.io/chloros/llms.txt`.
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` „PyPI“ svetainėje) valdo visas darbalaukio programos funkcijas, pradedant nuo Python: paketinį vaizdų apdorojimą, tiesioginį „LATTICE“ kameros ir matricos valdymą, DAQ šviesos jutiklių sesijas bei išsaugotų projektų automatizavimą. Tai plonas sluoksnis, uždedamas ant to paties vietinio užpakalinio modulio, kurį naudoja GUI ir „CLI“ (HTTP, esantis „`127.0.0.1:5000`“), todėl veikimas visose trijose aplinkose yra identiškas.

## Įdiegimas

Įdiegimas vyksta dviem etapais: pirmiausia įdiegiamas Chloros darbalaukio paketas (jis suteikia apdorojimo pagrindą ir aparatinės įrangos vykdymo aplinką), o po to – Python paketas.

**1 etapas — Įdiekite Chloros.** Windows: paleiskite darbalaukio diegimo programą (numatytoji vieta – `C:\Program Files\MAPIR\Chloros\`) iš [Atsisiuntimo](download.md) puslapio. Linux: įdiekite paketą „`.deb`“ ([Linux įdiegimas](linux/linux-installation.md)).**2 žingsnis — Įdiekite „SDK“** (Python 3.7+):

```bash
pip install chloros-sdk
```

Jums netgi gali neprireikti „pip“: kiekvienas diegimo paketas turi atitinkamą „SDK“ „wheel“. „Windows“ diegimo programa jį automatiškai įdiegia į jūsų sistemos „Python“; Linux `.deb` įdiegia jį į `/usr/lib/chloros/sdk/` ir išveda tikslią `pip install --user` komandą. PyPI atnaujinamas išleidžiant naujas versijas, todėl `pip install chloros-sdk` atitinka naujausią stabilią versiją.

**3 žingsnis — Prisijunkite vieną kartą kiekviename kompiuteryje:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Prisijungimo duomenys išsaugomi `~/.chloros/` (abiejose platformose). Windows galite prisijungti taip pat naudodami darbalaukio programėlės skirtuką „Vartotojas“ (<img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">). SDK reikalauja mokamo Chloros+ plano — žr. [Licencijos reikalavimus](#license-requirement) žemiau.

| Reikalavimas | Išsami informacija |
| --- | --- |
| **Įdiegta „Chloros“** | „Windows“: darbalaukio diegimo programa; Linux: `.deb` paketas (teikia užkulisinį binarinį failą) |
| **Python** | 3.7 ar naujesnė versija (sukurta ir išbandyta su 3.10) |
| **Operacinė sistema** | Windows 10/11 64 bitų, „Ubuntu 22.04 LTS“ ar naujesnė versija, arba „NVIDIA Jetson“ (JetPack 6) |
| **Licencija** | Aktyvi „Chloros+“ prisijungimo paskyra, bet koks mokamas planas („Copper“ ar aukštesnis) |

## 60 sekundžių pergalė

Vienas iškvietimas sukuria projektą, importuoja aplanką, sukonfigūruoja apdorojimą ir paleidžia duomenų srautą — automatiškai paleidžiant užkulisinį procesą, jei jis dar neveikia:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)
```

(Naudojant Linux, naudokite Linux kelius: `/home/user/drone_images/flight001`. SDK veikia vienodai abiejose platformose.)

Apdorojate „LATTICE“ užfiksuotų vaizdų aplanką? Naudokite „LATTICE“ suderinamą apvalkalą — jis taiko tinkamus numatytuosius nustatymus (be skydelio tikslo nustatymo, standartinis debayeris):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — visiškas apdorojimo grandinės valdymas

Jei reikia daugiau nei vienos eilutės komandos, naudokite `ChlorosLocal`. Jis paleidžia užkurtį pirmą kartą naudojant (`auto_start_backend=True`), sukuria ir konfigūruoja projektus, stebi vykdymo eigą ir pateikia apibendrinimą po užduoties įvykdymo.

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
Naudokite numatytąjį `http://127.0.0.1:5000`, o ne pakeiskite jį `localhost` — Windows atveju `localhost` pirmiausia išsprendžiamas kaip `::1` ir kainuoja ~2 sekundes už kiekvieną užklausą, kai naudojamas tik IPv4 palaikantis užpakalinis serveris.
{% endhint %}

Naudokite jį kaip konteksto tvarkyklę, kad būtų užtikrintas išvalymas:

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` priima šiuos raktinius žodžius: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation` ir `custom_settings`. Pagrindinės reikšmės:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

„LATTICE“ būdingi reguliatoriai (`input_level`, `radiometric_output`, `array_alignment*` šeima) yra aprašytos kartu su išsamiomis verčių lentelėmis [SDK nuorodoje](reference/sdk-reference.md#supported-values).

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

Užbaigus vykdymą, `process()` prideda užkulisio apdorojimo santrauką kaip `result["summary"]`. Kiekvienas įrašas `summary["hints"]` yra pilnas sakinys, paaiškinantis viską, kas verta dėmesio — pavyzdžiui, kodėl vykdymo metu nebuvo gauta jokių rezultatų — ir kiekviena užuomina taip pat pakartotinai išsiunčiama kaip Python `UserWarning`, todėl tušti vykdymai yra savaime diagnozuojami, net jei niekada nepatikrinate žodyno:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` neatsiranda, kai vykdymo metu nesukuriami jokie vaizdai.** Tai vienintelė vieta, kurioje SDK ir CLI sąmoningai skiriasi: `chloros-cli process` traktuoja situaciją „buvo prašoma produktų, bet nė vienas nebuvo įrašytas“ kaip nesėkmę ir baigia veikimą su nelygiu nuliui rezultatu, tuo tarpu SDK grįžta įprastai ir apie šią sąlygą praneša per `summary` / užuominas. Jei jūsų procesų grandinė turėtų sustoti esant tuščiam vykdymui, patikrinkite tai patys – peržiūrėkite `summary` (arba suskaičiuokite failus projekto aplanke), o ne pasikliaukite išimtimi.
{% endhint %}

## „Smart Connect“ — veikianti aparatinė įranga

Trys pagalbinės programos atidaro nuolatines sesijas užkulisinės sistemos aparatinės įrangos rezervoje — toje pačioje rezervoje, kurią naudoja GUI, todėl „SDK“ skriptai veikia kartu su darbalaukio programa, nesivaržydami dėl nuosekliųjų prievadų ar tinklo pralaidumo. Visos trys automatiškai paleidžia vietinę užkulisinę sistemą, jei nė viena neveikia.

### Viena „LATTICE“ kamera — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

### Sinchronizuotas masyvas — `connect_array`

`connect_array` yra rekomenduojamas pradinis taškas daugiakamerinėms sistemoms. Jis vykdo tą patį išmaniojo paruošimo procesą kaip ir grafinė vartotojo sąsaja: tinklo analizę, automatinį sinchronizavimo lygmens pasirinkimą, PTP laiko sinchronizavimą, pikselių formato pasirinkimą kiekvienai kamerai, AE sėjos nustatymą ir GPIO trigerio įjungimą. **Pirmoji serijinė kamera yra pagrindinė** (ji siunčia aparatinio trigerio impulsą); likusios yra pavaldžiosios.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Pridėkite `smart=True` prie bet kokio masyvo įrašymo, kad prieš suveikiant būtų palaukta, kol automatinė ekspozicija visose kamerose stabilizuosis. Dėl fiksavimo režimų („Vienkartinis“ / „Nuolatinis“ / „Intervalinis“ / „Greičiausias“), įrašymo įrenginių, „burst-to-video“ ir masyvo suderinimo žr. [SDK nuorodą](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### DAQ šviesos jutiklis — `connect_daq_sensor`

Jei argumentų nepateikiama, „`connect_daq_sensor()`“ automatiškai nustato perdavimo būdą (pirmenybė: „Ethernet“ → „BLE“ → „USB“):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Kiekviename rėmelyje perduodami 135 taškų `spectrum` (W/m²/nm, kai kalibruota), `is_saturated` žymė ir CIE `x`, `y`, `z`. Norint priskirti konkretų jutiklį ar perdavimo protokolą — tai patikimas pasirinkimas kompiuteriuose su keliais tinklo sąsajų įrenginiais, kur „Ethernet“ automatinis aptikimas pirmuoju bandymu gali nepranešti apie veikiantį „DAQ-E“ — reikia perduoti vieną aiškų nurodymą:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Atkreipkite dėmesį, kad didžiųjų raidžių koregavimo profiliai (`cap_id`) **nėra** SDK reguliatorius — vietoj to pasirinkite juos naudodami `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap`.

### Išsaugoti projektai — `open_project`

Išsaugotas Chloros projektas išsaugo prijungtą įrangą (`cameras.json` + `sensors.json` kartu su `project.json`), o „`chloros_sdk.open_project(path)`“ gali iš karto vėl prijungti viską ir vykdyti įrašymus pagal įrenginio pavadinimą. Žr. [Projekto automatizavimas](reference/sdk-reference.md#project-automation--chlorosproject) nuorodoje.

## Ką gaunate įdiegę tik per „pip“

Prieš naudodami aparatinės įrangos paviršius, patikrinkite modulio lygio prieinamumo žymes:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)       # True iff daq_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

Kompiuteryje, kuriame yra **tik** `pip install chloros-sdk` ir nėra Chloros darbalaukio paketo:

* `ChlorosLocal`, `process_folder` ir `process_lattice_capture` **neveikia** — jiems reikalingas užkulisinis binarinis failas, kuris pateikiamas darbalaukio diegimo programoje.
* „Smart-connect“ pagalbinės programos (`connect_camera`, `connect_array`, `connect_daq_sensor`) yra gryni „HTTP“ klientai, todėl jie veikia su kito kompiuterio užkurtu serveriu — tačiau pateikti serveriai susiejami tik su kilpos adresais, todėl prievadą turite nukreipti patys (pvz., `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) ir perduoti „`backend_url="http://127.0.0.1:5000"`“ kartu su „`auto_start_backend=False`“. Žr. [Nuotolinio užpakalinio serverio režimas](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Tiesiogiai su aparatine įranga susijusios LATTICE klasės (`LatticeCamera`, `CameraPool`, …) importuojamos, tačiau joms reikalinga „Arena“ SDK vykdymo aplinka iš darbalaukio paketo — be jos `CAMERA_AVAILABLE` yra `False`.
* `daq_sdk` (tiesioginės DAQ klasės) pateikiamos su darbalaukio diegimu, o ne su PyPI paketu, todėl „`DAQ_AVAILABLE`“ yra „`False`“ kompiuteryje, kuriame naudojamas tik „pip“ — vietoj to valdykite DAQ jutiklius per „`connect_daq_sensor()`“, prisijungus prie (tuneliuoto) užpakalinio serverio.

## Licencijos reikalavimai

Norint naudotis „SDK“, reikia turėti aktyvų „Chloros+“ prisijungimą bet kuriame mokamame lygyje — **„Copper“ ar aukštesniame**(„Copper“ / „Bronze“ / „Silver“ / „Gold“); nemokamame „Iron“ lygyje nėra prieigos prie SDK/CLI. Reikalavimų vykdymas vyksta**serverio pusėje**: kiekvienas SDK užklausimas turi turėti tiek aktyvią sesiją, tiek mokamą planą, kitaip serveris grąžina `403` / `PLAN_UPGRADE_REQUIRED` (kurį `ChlorosLocal` sukelia kaip `ChlorosLicenseError`, o pagalbinės funkcijos – kaip `ChlorosConnectError`). Atsijungęs skambintojas gauna `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — pakartotinis `chloros-cli login` paleidimas išsprendžia pirmąjį atvejį, bet ne antrąjį.

Naudojimas neprisijungus veikia plano atidėjimo laikotarpiu: lygis nuskaitomas iš serverio patvirtinimo talpyklos (5 minutės) arba pasirašytos, prie kompiuterio priskirtos licencijos talpyklos (30 dienų mėnesiniams planams; iki prenumeratos galiojimo pabaigos metiniams planams). Pasibaigus atidėjimo laikotarpiui, planas pereina į nemokamą lygį, o prieiga pagal „SDK“ sustabdoma, kol kompiuteris bent kartą prisijungs prie serverio. „`chloros-cli status`“ lieka pasiekiamas nemokamame lygyje, todėl priežastis visada matoma. Žr. [Chloros+ Prisijungimas](chloros+-login.md).

## Išimtys

Naudokite bazinę klasę, kad būtų tvarkomi „visi Chloros sutrikimai“:

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

Visos vamzdynų išimtys (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) kyla iš `ChlorosError`. Vienas niuansas: `ChlorosConnectError` — sukeltas tik `connect_camera` / `connect_array` / `connect_daq_sensor` — kyla iš paprastojo `Exception`, **ne** iš `ChlorosError`, todėl `except ChlorosError` jo neaptiks. Pilna hierarchija pateikta [SDK nuorodoje](reference/sdk-reference.md#exceptions).

## Taip pat žr.

* [SDK nuoroda](reference/sdk-reference.md) — išsamus „API“ paviršius, optimizuotas dirbtinio intelekto asistentams.
* [CLI nuoroda](reference/cli-reference.md) — kiekviena CLI pakomanda atitinka SDK iškvietą.
* [Atsisiųsti](download.md) — diegimo programos, skirtos Windows ir Linux.
