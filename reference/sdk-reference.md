# Chloros Python SDK Nuoroda

**Versija:**

1.2.0**Sukurta:**2026-07-29 19:19 ·**Atnaujinta:** 2026-08-30**Paketas:** `chloros-sdk` (PyPI)**Skirta:** Optimizuota dideliems kalbos modeliams (LLM); suprantama žmogui.**Apimtis:** Visos viešos klasės, funkcijos ir pagalbinės funkcijos, kurias pateikia `import chloros_sdk`, su pavyzdžiais, kuriuos galima nukopijuoti ir įklijuoti, apimantys vaizdų apdorojimą, vienos kameros valdymą, sinchronizuotus masyvus, DAQ jutiklius ir projekto automatizavimą.

Jei jums reikia tik svarbiausių dalykų, pereikite prie:
- [Įdiegimas ir greitasis pradžios vadovas](#installation)
- [„Smart-Connect“ LATTICE kamerų masyvams](#smart-connect-for-lattice-cameras)
- [DAQ jutiklių sesijos](#daq-sensor-sessions)
- [Projekto automatizavimas](#project-automation--chlorosproject)
- [„Smart-AE“ / „Smart-Capture“](#smart-ae--smart-capture)

---

## Architektūra per 60 sekundžių

„SDK“ yra plonas „Python“ sluoksnis, veikiantis virš „Chloros“ užkulisio (tas pats „Flask“ serveris, kurį naudoja darbalaukio GUI ir „CLI“). Automatizavimui importuokite „`chloros_sdk`“ ir iškvieskite aukšto lygio metodus; viduje kiekvienas iškvietimas tampa „HTTP“ užklausimu į vietinį užpakalinį procesą 5000-ajame prievade — „`http://127.0.0.1:5000/api/...`“ (sąmoningaityčia ne `localhost`, kuris pirmiausia nukreipia į `::1` adresu Windows ir kainuoja ~2 s už kiekvieną užklausą, kai naudojamas tik IPv4 palaikantis backend). Backend valdo aparatinės įrangos fondą — kameras, DAQ jutiklius, suderinimo profilius, kadrų buferius — todėl „SDK“ skriptai gali veikti kartu su GUI, nesivaržydami dėl serijinių prievadų ar tinklo plokštės pralaidumo.

Naudosite tris sąsajas:

1. **`ChlorosLocal` + nemokamos funkcijos** (`process_folder`, `process_lattice_capture`) — vaizdo apdorojimo srautas. Vienu „Python“ iškvietimu apdorokite visą aplanką, atlikdami kalibravimą, „debayer“ ir indeksų eksportą.
2. **„Smart-connect“ tvarkyklės** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Atidarykite nuolatinę užkulisinę sesiją realaus laiko aparatūrai. Tas pats „smart-prep“ srautas kaip ir GUI: tinklo zondas, automatinis lygmens pasirinkimas, PTP, AE sėjos nustatymas, GPIO trigerio konfigūracija.
3. **`ChlorosProject` / `open_project`** — Įkelia išsaugotą projektą (aplanką su `cameras.json` + `sensors.json` + `project.json`), vienu metu prijungti viską ir vykdyti įrašymus naudojant pavadintus identifikatorius.

1 ir 2 sąsajos **automatiškai paleidžia vietinį backendą**, jei jis dar neklauso (tas pats komplekte esantis binarinis failas, kurį paleidžia GUI / „CLI“) — taigi paprastas skriptas veikia iš naujos komandų eilutės, be to, kad jums nereikėtų iš anksto paleisti užkulisio. Norėdami atsisakyti šios funkcijos, perduokite `auto_start_backend=False` (pvz., kai nukreipiate į nuotolinį užkulį, kuris niekada nėra paleidžiamas). Žr. [Užkulisio automatinis paleidimas](#backend-auto-start). „Surface 3“ elgiasi kitaip: „`open_project()`“ nepriima „`auto_start_backend`“ parametro, o „`connect_all()`“ niekada nepaleidžia užkulisinio proceso — jis vieną kartą patikrina „`http://127.0.0.1:5000` tik vieną kartą, o jei niekas neatsako, tyliai grįžta prie tiesioginio (be užkulisiobe užpakalinės dalies) `lattice_sdk` įrenginio valdymą. Tik `proj.process()` ir `stream(..., overlays=True)` lėtapusiškai sukuria `ChlorosLocal()` (kuris paleidžiamas automatiškai).

Visi trys reikalauja autentifikavimo: vieną kartą paleiskite „`chloros-cli login`“ kompiuteryje, arba prisijunkite per darbalaukio grafinę sąsają. „SDK“ iškvietimai be galiojančios sesijos sukelia „`ChlorosAuthenticationError`“ klaidą.

Reikalavimai:
- „Python“ 3.7+ (kaip nurodyta pakete; sukurta ir išbandyta su 3.10 versija)
- Vietoje įdiegta „Chloros“ darbalaukio versija (pagrindinis vykdomasis failas pateikiamas diegimo programoje)
- Aktyvi „Chloros“ ar naujesnės versijos paskyra. „SDK“ / „CLI“ paslaugoms taikomas **„Copper“**lygis ar aukštesnis (Copper / Bronze / Silver / Gold); nemokamas**„Iron“**lygis neturi prieigos prie „SDK“ / „CLI“. Tai užtikrinama**serverio pusėje**: kiekvienas užklausimas su žymėmis „SDK“ / „CLI“ turi turėti tiek aktyvią sesiją, tiek apmokėtą planą, kitaip serveris grąžina klaidą „`403`“ su „`error_code: PLAN_UPGRADE_REQUIRED`“ (`ChlorosLocal`, o pagalbinės funkcijos rodo kaip „`ChlorosConnectError`“ ir „`connect_*`“). Atsijungęs vartotojas gauna klaidos kodus „`401`“ / „`AUTH_REQUIRED`“ (`ChlorosAuthenticationError`) — šie du kodai skiriasi, nes pakartotinai paleidus `chloros-cli login` ištaisomas pirmasis, o antrasis neištaisomas.
- Naudojimas neprisijungus palaikomas plano atidėjimo laikotarpiu: lygis nuskaitomas iš serverio patvirtinimo talpyklos (5 min.) arba iš pasirašytos, prie kompiuterio pririštos licencijos talpyklos (30 dienų mėnesiniams planams, iki prenumeratos galiojimo pabaigos metiniams planams). Pasibaigus šiam atidėjimo laikotarpiui, planas perjungiamas į nemokamą, o prieiga prie SDK / CLI sustabdoma, kol kompiuteris bent kartą prisijungs prie serverio. `chloros-cli status` (`GET /api/license-status`) lieka pasiekiamas nemokamoje pakopoje, todėl priežastis yra matoma — tai vienintelis „SDK“ / „CLI“ maršrutas, kuriam netaikomas pakopos apribojimas.
- „Windows“ 10/11 64 bitų, **„Ubuntu 22.04 LTS“ ar naujesnė versija**arba „Jetson“ (JetPack 6). „Ubuntu 20.04**nėra** palaikoma: „`.deb`“ priklausomybės kyla iš to, su kuo susietas užpakalinis modulis, įskaitant „`libc6 (>= 2.34)`“, o „focal“ pateikia „glibc 2.31“.

---

## Įdiegimas

„Python“ SDK yra plonas „Python“ sluoksnis, veikiantis ant „Chloros“ užkulisio. Visiems darbams, išskyrus keletą vien tik duomenų surinkimo (DAQ) darbo eigų, reikia **vietoje įdiegto „Chloros“ darbalaukio paketo** (Windows diegimo programa arba Linux `.deb`) — būtent jis suteikia užkulisinį binarinį failą, „Arena“ SDK vykdymo aplinką „LATTICE“ kameroms ir kalibravimo rinkinius.

Naujausi atsisiuntimai: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### 1 žingsnis — Įdiekite „Chloros“ platformos paketą

#### „Windows“ (.exe)

1. Atsisiųskite „`Chloros-Setup-x.y.z.exe`“ iš atsisiuntimo puslapio.
2. Paleiskite diegimo programą ir sekite vedlio nurodymus. Numatytasis diegimo kelias yra „`C:\Program Files\MAPIR\Chloros\`“.
3. Bent kartą paleiskite „Chloros“ ir prisijunkite naudodami savo „Chloros+“ paskyrą.

#### „Linux“ amd64 (.deb)

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

#### „Linux“ arm64 — „Jetson“ (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

### 2 žingsnis — Įdiekite „Python“ SDK

**„Chloros“ diegimo programa pateikia atitinkamą „SDK“ ratą.** Kiekviena „Windows“ diegimo programa ir „Linux“ .deb failas į diską įrašo „`chloros_sdk-X.Y.Z-py3-none-any.whl`“, kuris tiksliai atitinka GUI / „CLI“ / užkulisinės dalies versiją. Jums nereikia sekti „PyPI“, kad viskas būtų sinchronizuota.

#### „Windows“

Diegimo programa automatiškai paleidžia „`pip install` su komplekte esančiu „wheel“ failu, naudodamas jūsų sistemos „Python“ (pirmenybė teikiama „`py.exe`“ paleidimo programai, o jei ji neveikia – naudojama „`python -m pip`“). Jokių veiksmų atlikti nereikia — sėkmingai įdiegus, „`import chloros_sdk`“ veikia jūsų „Python“ aplinkoje. Jei kompiuteryje nėra „Python“, diegimo programa tyliai praleidžia šį žingsnį, o GUI ir „CLI“ toliau veikia.

#### „Linux“ (.deb)

.deb failas įdiegia „wheel“ į `/usr/lib/chloros/sdk/`. `postinst` išspausdina tikslią komandą — PEP 668 distribucijos pagal numatytuosius nustatymus atsisako globalaus „pip“ rašymo, todėl mes neįdiegiame automatiškai:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

„Air-gapped“ „Jetson“ diegimams tai veikia visiškai neprisijungus prie interneto — „wheel“ jau yra diske.

#### Viešasis PyPI

Tik „pip“ naudojantiems kompiuteriams (neįdiegta „Chloros“ darbalaukio paketo versija; „remote-backend“ arba tik DAQ darbo srautai):

```bash
pip install chloros-sdk
```

PyPI atnaujinamas išleidžiant naujas diegimo versijas, todėl paskelbtas „wheel“ atitinka naujausią stabilią versiją. Kūrimo versijos (pvz., `1.1.4.dev1`) platinamos tik per pridedamą diegimo „wheel“.

#### Patikrinkite

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Reikalingas „Chloros“ prenumerata.** Visi „SDK“ iškvietimai reikalauja aktyvios „Chloros“ prisijungimo informacijos. Vieną kartą kiekviename kompiuteryje paleiskite „`chloros-cli login user@example.com 'YourPassword'`“; prisijungimo duomenys išsaugomi „`~/.chloros/`“.

### Ar man reikalingas darbalaukio paketas?

Vien „pip“ paketo daugumai darbo eigų **nepakanka**. Štai ko reikia kiekvienam „SDK“ paviršiui:

| „SDK“ paviršius | Reikia darbalaukio paketo? | Kodėl |
| --- | --- | --- |
| „`ChlorosLocal`“, „`process_folder`“, „`process_lattice_capture`“ | **Taip** | „Auto“– paleidžia užkulisinį binarinį failą `/usr/lib/chloros/chloros-backend` (Linux) arba `C:\Program Files\MAPIR\Chloros\…` (Windows). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Taip**(vietinis)**/ Ne**(nuotolinis) | Grynieji „HTTP“ klientai per užkulisį. Vietinis užkulisys → reikalingas darbalaukio paketas. Nuotolinis užkulisys → `backend_url=`**per tunelį** (žr. „Nuotolinio užkulisio režimas“ — pridedami užkulisiai pririšami tik prie kilpos). |
| `ChlorosProject` / `open_project` | **Taip** | Per backend&#x27;ą valdo išsaugotus projektus. |
| Tiesioginės LATTICE klasės (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Taip** | Reikalinga „Arena“ „SDK“ gimtoji vykdymo aplinka, kuri yra pateikiama su darbalaukio paketu. Kitais atvejais „`CAMERA_AVAILABLE`“ importuojant tampa „`False`“. |
| Tiesioginės DAQ klasės (`DAQUSensor`, `DAQMSensor`, `DAQESensor`, `SensorFleet`, `discover_all`) | **Ne** | Grynasis „Python“ per „pyserial“/„bleak“/„zeroconf“. Tik „pip“ aplinka gali valdyti duomenų surinkimo įrenginius (DAQ) nuo pradžios iki pabaigos. |

### Nuotolinio užpakalinio modulio režimas (tik „pip“ kompiuteris, per tunelį)

> **Pristatytas užpakalinis modulis nėra pasiekiamas per LAN.** Gamybinė
> versijos pririša tik kilpos grįžtąjį ryšį (abi kilpos grįžtamojo ryšio šeimos) ir kategoriškai atmeta
> vienintelį nekilpos grįžtamojo ryšio režimą (`CHLOROS_CLOUD_MODE`), todėl
> `backend_url="http://<lan-ip>:5000"` **negali veikti su įdiegtu
> „Chloros“** — šis modelis visada veikė tik su „source/dev“
> užpakaliniu serveriu. Norėdami valdyti backendą kitame kompiuteryje, patys persiųskite jo kilpos
> prievadą ir nukreipkite „SDK“ į tunelį:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"   # the tunnel endpoint

chloros_sdk.connect_camera("213800234", backend_url=BACKEND)
chloros_sdk.connect_array(serials, backend_url=BACKEND)
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local", backend_url=BACKEND)
```

Kompiuteriai be monitoriaus / CI / robotų kompiuteriai gali turėti vieną kompiuterį su pilna darbalaukio instaliacija kaip „Chloros“ serverį, o visur kitur naudoti `pip install chloros-sdk` — tačiau duomenų perdavimas tarp jų vyksta per aukščiau minėtą vartotojo sukonfigūruotą tunelį, o ne tiesioginiu LAN „URL“.

> **Žinomas apribojimas — `ChlorosLocal` nepalaiko tik „pip“.** „`ChlorosLocal(backend_url=BACKEND)`“ šiuo metu savo konstruktoriuje išsprendžia vietinį užpakalinio proceso binarinį failą *prieš* tikrindamas „URL“, ir sukelia „`ChlorosBackendError`“ („Chlorosinis užpakalinis procesas nerastas…“), kai nėra įdiegtas joks darbalaukio paketas — net ir turint pasiekiamą nuotolinį užpakalinį procesą. Tik aukščiau nurodytas „smart-connect“ paviršius (`connect_camera` / `connect_array` / `connect_daq_sensor`, taip pat `analyze_array_network` ir `list_*` / `discover_*` pagalbiniai moduliai) veikia iš kompiuterio, kuriame įdiegtas tik „pip“.

### Tik DAQ darbo eiga (kompiuteris, kuriame įdiegtas tik „pip“)

Jei jums reikalingi tik DAQ jutikliai ir nenaudojate „LATTICE“ kamerų ar vaizdo apdorojimo, „pip“ paketas yra savarankiškas:

```bash
pip install chloros-sdk
```

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

sensor = DAQUSensor(port="/dev/ttyUSB0")
sensor.connect()
sensor.start_streaming()
```

Nereikia jokio užkulisinio modulio, jokio .deb failo, jokio prisijungimo per Chloros+ norint dirbti tiesiogiai su aparatine DAQ įranga.

---

## Greitasis pradžios vadovas

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## Aukščiausio lygio „API“ indeksas

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()

# === Project lifecycle ===
chloros_sdk.open_project(path)                    # → ChlorosProject
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk / daq_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics
chloros_sdk.DAQUSensor, DAQMSensor, DAQESensor, SensorFleet, discover_all

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.DAQ_AVAILABLE        # True iff daq_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True iff ChlorosProject deps available
```

---

## Vaizdų apdorojimas — `ChlorosLocal`

Pagrindinė duomenų apdorojimo klasė. Pirmą kartą naudojant paleidžia užkurtį, sukuria ir konfigūruoja projektus, stebi vykdymo eigą, grąžina apibendrinimus po vykdymo.

### Konstruktorius

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

### Metodai

| Metodas | Aprašymas |
| --- | --- |
| `create_project(project_name, camera=None)` | Sukuria naują projektą (pasirinkus galima naudoti kameros šabloną, pvz., `"Survey3N_RGN"`). |
| `import_images(folder_path, recursive=False)` | Importuoja RAW/TIF/JPG/DNG vaizdus **ir `.daq` šviesos jutiklio įrašus**. Grąžina `count` (vaizdus) ir `scan_count` (įrašus). Įspėja tik tuo atveju, jei aplanke nėra nei vienų, nei kitų. |
| `export_light_sensor(daq=True, csv=True)` | Įrašo kalibruotus `.daq` + `.csv` duomenis už kiekvieną projekto šviesos jutiklio įrašą į `<project>/Light Sensor/`. Žr. [Šviesos jutiklio įrašai](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | Nustatykite apdorojimo parametrus. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Paleiskite apdorojimo grandinę. Grąžina „`{"status": "complete", "async": False}`“, taip pat raktą „`summary`“, jei jį pateikia užkulisinė sistema — žr. [Vykdymo pabaigos santrauką ir patarimus](#post-run-summary--hints). |
| `get_config()` / `get_status()` / `status()` | Patikrinti užkulisio būseną. |
| `logout()` | Išvalyti talpykloje saugomus prisijungimo duomenis. |
| `shutdown_backend()` | Nutraukti užkulisio veiklą (jei paleista naudojant „SDK“ -start). |
| `discover_cameras()` | Aptikti „LATTICE“ kameras **per šio egzemplioriaus užkurtį** (`/api/camera/discover`). Grąžina žodynų sąrašą (`serial`, `model`, `ip`, …) — tokios pačios struktūros, kokią mato GUI/CLI. Tuščias sąrašas, jei nerasta jokių kamerų arba backend nepasiekiamas. |
| `camera_capture(output_dir, format="tiff", **settings)` | Užfiksuoja vieną kadrą**per užkurtį**(automatiškai paleidžiamą šiuo identifikatoriumi), kad jis būtų paruoštas taip pat, kaip GUI / CLI (numatyta 12 bitų, pakartotinis rezervo naudojimas, įterpti kalibravimo metaduomenys). Nustatykite tikslą naudodami `serial=` arba `device_index=`; perduokite `exposure`/`gain`/`pixel_format`/`preset` kaip `**settings`. Grąžina senosios versijos metaduomenų žodyną (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Sukuria perklotų vaizdų peržiūros kadrus iš sujungtų kamerų — lengvas MJPEG klientas per užkulisio `/api/camera/<serial>/stream-annotated` maršrutą (zebra / tinklelis / kryžminis taškas / histograma / piko rodymas / taškas, nubrėžtas serverio pusėje). `decode=True` pateikia BGR masyvus; `False` pateikia neapdorotus „JPEG“ baitus. Taip pat pasiekiamas pagal projektą kaip `ChlorosProject.stream(overlays=True)`. |

Naudokite kaip konteksto tvarkyklę, užtikrinančią išvalymą:

```python
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

### Šviesos jutiklių įrašai — kalibruoti `.daq` + `.csv`

DAQ-U / DAQ-M / DAQ-E duomenys gali būti įrašomi **be** jų kalibravimo paketo. Tai reiškia,
ką viešai prieinami [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
įrašymo įrenginiai (`record_daq.py`) daro pagal numatytuosius nustatymus: jie įrašo neapdorotus jutiklio rodmenis ir pažymi
failą taip, kad „Chloros“ paimtų to jutikliogamybinę kalibraciją **pagal serijos numerį** — pirmiausia iš vietinės talpyklos,
tada iš „MAPIR“ debesies — ir pritaiko ją importuojant.

„Chloros“ rezultatą išrašo atgal kaip du produktus vienam įrašui, po
`<project>/Light Sensor/`:

| Produktas | Kas tai yra |
| --- | --- |
| `<name>_calibrated.daq` | Pakartotinai apdorojamas archyvas — ta pati schema kaip ir tiesioginiame įraše, dabar nurodant paketą, kuris jį sukūrė. Pakartotinis jo importavimas **ne** kalibruos jo antrą kartą. |
| `<name>_calibrated.csv` | Spektrinis spinduliavimo intensyvumas W/m²/nm pagal paties jutiklio bangų ilgių tinklelį, po vieną eilutę kiekvienam matavimui, taip pat fotometrinės kolonos (bendroji galia, fotopinis/skotopinis liuksas, PPFD ir jo pasiskirstymas pagal mėlyną, žalią bei raudoną spalvas, didžiausio intensyvumo bangos ilgis). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Tik jutikliai be ryšio bloko (DAQ-A).** Neapdoroti spektriniai jutiklio skaičiai — *ne* spinduliavimo intensyvumas. Žr. toliau. |

`process()` atlieka šį eksportą kaip vieną iš savo etapų. Jam **nereikia** vaizdų:
atskirai skraidantis šviesos jutiklis yra puikus darbo srautas, o toks projektas iš esmės neturi
jokių vaizdų.

**DAQ-A įrašai eksportuojami kaip neapdoroti skaičiai.** DAQ-A serija yra senesnė už serijinius
rinkinius, todėl neturi jokio rinkinio, kurį reikėtų atsisiųsti — vietoje to ji kalibruojama lauke pagal
atspindžio taikinį, todėl rinkinio jai niekada nereikėjo. Šie įrašai eksportuojami
su `_raw` šaknimi, o ne su `_calibrated`: tai kitoks failo pavadinimas, o ne žymė
failo viduje, nes informacija turi išlikti, kai ji siunčiama el. paštu kaip paprastas pavadinimas.
`.csv` antraštė nurodo „`raw spectral sensor counts (NOT irradiance)`“ ir įspėja, kad
reikšmės yra palyginamos **tame pačiame** faile — būtent tam, kam jas naudoja kalibravimas pagal etaloną
— o ne tarp skirtingų jutiklių. Nuo galios priklausančios fotometrinės kolonos (bendroji galia,
fotopinis/skotopinis liuksas, PPFD) grąžinamos kaip **NULL**, o ne integruojamos iš skaičiavimų.

DAQ-U / DAQ-M / DAQ-E, kurių rinkinio tiesiog nepavyko atsisiųsti, vis tiek **praleidžiami**,
o ne įrašomi neapdoroti: ten rinkinys egzistuoja, todėl „iš naujo prisijungti ir apdoroti“ yra tikras patarimas.

Senosios **v1.01 / v1.02** įrašai (juos rašo DAQ-A-SD) neturi kiekvieno skaitymo epochos,
tik failo įrašymo laiką. Vaizdo ↔ žemyn nukreipto srauto atitikimo programa vis dar juos atmeta —
kadangi kadrą suderinti su įrašymo laiku būtų nematoma klaida — tačiau eksportuotojas juos nuskaito, o
„CSV“ išspausdina `clock=daq_created_on`, todėl produktas nurodo, kokiu laikrodžiu jis veikia.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

Įrašas, kurio kalibravimo rinkinio negalima atsisiųsti (neprisijungus prie interneto arba jei jutiklio
kalibravimo duomenų faile nėra), pranešamas kaip `skipped` **nurodant priežastį**. Jis niekada
nėra išrašomas kaip „kalibruotas“ failas, kuriame saugomi neapdoroti skaičiai — prisijunkite prie interneto ir
vykdykite iš naujo, ir eksportavimas bus užbaigtas.

### Vykdymo pažangos atgaliniai skambučiai

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Apibendrinimas ir patarimai po vykdymo

Užbaigus, `process()` atsisiunčia `GET /api/processing-summary` ir prideda jo turinį kaip `result["summary"]`. Atsisiuntimas vykdomas pagal galimybes ir niekada neužblokuoja sėkmingo grįžimo — jei santrauka nėra prieinama, `process()` grįžta prie paprastos `{"status": "complete", "async": False}` formos. Kiekvienas įrašas `summary["hints"]` — pilni sakiniai su siūlomais pataisymais, pvz.,pvz., kodėl vykdymo rezultatas buvo nulinis — taip pat iš naujo išsiunčiamas kaip „Python“ `UserWarning`, taigi vykdymai su nuliniais rezultatais yra savaiminiai, net jei niekada nepatikrinate žodyno:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` yra kompiuteriui suprantama pusė:

| Raktas | Ką skaičiuoja |
| --- | --- |
| `models` | Kameros grupės vykdymo metu. |
| `images_in_groups` | Šaltinio vaizdai tose grupėse. |
| `targets_found` | Aptikti atspindžio taikiniai. |
| `images_calibrated` | Vaizdai, kuriais buvo kalibruotas vykdymo ciklas. |
| `exported_files` | **Vaizdo produkto failai, kuriuos sukūrė vykdymo ciklas.** |
| `daq_recordings_exported` / `daq_recordings_skipped` | Šviesos jutiklio įrašai, tyčia suskaičiuoti atskirai — jie yra iš kito etapo ir egzistuoja bandymams, kuriuose vaizdų visai nėra, todėl juos įtraukus, atrodytų, kad bandymas, kuriame buvo naudojamas tik duomenų surinkimas (DAQ), eksportavo vaizdus. |

Kartu su jais: `summary["output_dirs"]` (kiekvienas katalogas, į kurį buvo įrašyta),
`summary["light_sensor_export"]`, `summary["stopped"]` (žymimas kaip „true“, kai vartotojas nutraukė
vykdymą, todėl daliniai skaičiai nereiškia, kad užbaigtas vykdymas buvo nepakankamai produktyvus) ir
`summary["groups"]` (paskirstymas pagal grupes).

`exported_files` įrašomas vamzdyne **rašymo metu**, o ne vėliau nuskaitomas iš
projekto vaizdo objektų. Lygiagretaus apdorojimo ir GPU strategijos kuria savo vaizdo
objektus (GPU keliuose – darbininkų subprocesuose), todėl senasis nuskaitymas pranešdavo
`0 file(s) written` kiekvienam tokiam vykdymui ir tada išsiųsdavo nulinį-exports užuominą — vykdymuose,
kuriuose viskas veikė tinkamai. Jei kuriate skriptą pagal šį skaičių, dabar sėkmingas lygiagretusis vykdymas
praneša apie nulinį skaičių.

„Light-sensor“ praleidimai praneša priežastį, kurią skaitytuvas faktiškai nustatė kiekvienam failui —
neskaitoma schema, trūkstamas rinkinys, rašymo klaida — **deduplikuota**, taigi dvidešimt failų,
praleistų dėl vienos priežasties, traktuojami kaip viena priežastis, o ne kaip dvidešimt jos pasikartojimų.

> **`process()` neatsiranda, kai vykdymo metu nesukuriami jokie vaizdai.** Tai vienintelis atvejis, kai „SDK“ ir
> „CLI“ sąmoningai skiriasi: `chloros-cli process` traktuoja „buvo užsakyti produktai, bet nė vienas nebuvo
> įrašytas“ kaip nesėkmę ir baigia veikimą su nelygiu nuliui rezultatu, tuo tarpu „SDK“ grįžta įprastai ir apie
> šią būklę praneša per `summary` / „hints“. Jei jūsų apdorojimo grandinė turėtų sustoti tuščio vykdymo atveju, patikrinkite tai
> patys — patikrinkite „`summary`“ (arba suskaičiuokite failus projekto aplanke), o ne pasikliaukite
> išimties nebuvimu. Įprastos priežastys yra įvesties aplankas, kuris nebuvo atpažintas kaip
> įrašymo šaltinis, ir produktai, praleisti kaip netinkami esamoms kameroms (pvz., spinduliavimas iš kamerų, turinčių tik „RGB“
> funkciją).

### Patogios funkcijos

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

### Palaikomos reikšmės

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as already-debayered BGR
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### Radiometriniai išvesties duomenys (LATTICE multispektrinis apdorojimo procesas)

`process` apdorojimo proceso LATTICE multispektrinis (M3C/M3M) eksporto lygis — `reflectance` (numatyta reikšmė), `radiance`, `sensor-response` arba `all` (kiekvienas taikytinas režimas kiekvienam vaizdui) — atitinka projekto apdorojimo nustatymą **„Radiometrinė išvestis“**. `configure()` turi tam skirtą raktinį žodį:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

Išplėstinis išeities variantas — projekto `"Radiometric output"` rakto įrašymas per `custom_settings` — vis dar veikia, tačiau atminkite, kad jis pakeičia visą nustatymų bloką (žr. toliau pateiktą įspėjimą):

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Radiometric output": "radiance"},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (numatytoji reikšmė) padalina kameros spinduliavimą iš **laiko žymos atitinkančio DAQ žemyn nukreipto srauto**, kuris automatiškai nustatomas iš įrašyto `.daq` (DAQ-U/M/E)**arba DAQ-M natyvią `.csv`**, esančio kartu su vaizdais; bet koks vietoje trūkstamas kameros ar DAQ kalibravimo rinkinys pirmą kartą naudojant yra**automatiškai atsisiunčiamas iš AWS**. „CLI“ tai pateikia kaip kiekvieno tipo produkto perjungimo mygtukus `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.

> `custom_settings` **pakeičia** visą apskaičiuotų nustatymų bloką (jis apeina `configure()`&#x27;kitus raktinius žodžius ir numatytą patikrinimą). Naudodami jį, įtraukite visus `Project Settings` raktus, kurie jums svarbūs, kaip parodyta pavyzdyje aukščiau.

---

## „Smart-Connect“ „LATTICE“ kameroms

Nuolatinės užkulisinės sesijos, skirtos tiesioginiam aparatūros valdymui. Naudojami tie patys galiniai taškai, kuriuos naudoja GUI, todėl elgsena yra identiška visose platformose: SDK / CLI / GUI.

### Viena kamera — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

#### `connect_camera()` parašas

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

#### `CameraSession` metodai

| Metodas | Aprašymas |
| --- | --- |
| `read_nodes(names, enum_names=(), timeout=30.0)` | Skaito GenICam mazgus; grąžina `{nodes, errors, enums, device}`. |
| `set_settings(**kwargs)` | Rašo mazgus pagal patogų pavadinimą (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, settings=None, timeout=None)` | Užfiksuoja **vieną** kadrą. Grąžina vieno elemento sąrašą, sudarytą iš kadrų metaduomenų žodynų. (Serijinis / kelių kadrų fiksavimas buvo pašalintas — jei reikia serijos, cikle iškvieskite `capture()`.) |
| `disconnect()` | Atleidžia iš rezervo. Nevykdo jokios operacijos, jei prisijungta prie jau atidarytos sesijos. |

`capture()` eksporto valdymas (tas pats modelis kaip masyvo + GUI):

- `processing` / `levels` — `processing="all"` išsaugo visus taikytinus eksporto tipus; `levels=["raw","radiance"]` išsaugo tik tuos (pakeičia `processing`). Norint naudoti užpakalinės dalies numatytąsias reikšmes, praleiskite abu parametrus.
- `force_daq=True` — išsaugokite priskirtą DAQ/DLS rodmenį kaip `.daq` papildomą failą net ir tik neapdorotų duomenų įrašymo atveju, kad vėliau kadrą būtų galima perdirbti į atspindžio koeficientą/indeksą. Nevykdo jokios operacijos, jei nėra susietas joks DAQ.

### Sinchronizuotas masyvas — `ArraySession` (Smart-Prep)

`connect_array` yra **rekomenduojamas pradinis taškas** daugiakamerinėms konfigūracijoms. Jis viduje vykdo visą GUI „Smart-Prep“ eigą:

1. **Tinklo analizė** (`/api/camera/array/recommend`) — nustato didžiausią kadrų dydį, kuris tinka „sim-emit“ lygiui be kadrų praradimo.
2. **Automatinis lygio pasirinkimas** — `sim-capture-sim-emit`, jei ryšio linija tai gali išlaikyti; priešingu atveju — `sim-capture-ftd-stagger` arba `slip-emit-and-capture`.
3. **Automatinis sumažinimas**— tyliai sumažina kadrų dydį / padidina binningą, kai laidai negali išlaikyti reikalaujamos skiriamosios gebos.**Ši apsauga neapima bendro perviršinio užsakymo**: per daug kamerų laidams negalima išspręsti sumažinant kadrus — žr. [Perviršinis užsakymas](#over-subscription-the-per-cam-floor).
4. **PTP įjungtas**pagal numatytuosius nustatymus — skirtingų kamerų laiko žymos sinchronizuojamos pagal vieną bendrą laikrodį iki**~1 ms**. Vienalaikis eksponavimas užtikrinamas M8 aparatinės įrangos trigeriu (**&lt; 100 µs** tarp modulių), o ne PTP: PTP suderina *laiko žymes*, o ne ekspozicijas.
5. **Automatinis pikselių formato pasirinkimas kiekvienai kamerai** — RGB kameros → `BayerRG8`, multispektrinės → `BayerRG12`.
6. **AE pradinės reikšmės nustatymas** — užfiksuoja kiekvienos kameros dabartinę AE būseną, kad prisijungus ekspozicija nebūtų iš naujo nustatoma veikimo metu.
7. **GPIO trigerio konfigūracija** — „`connect_array`“ įjungia kiekvieną kamerą („`TriggerMode=On`“, „`TriggerSource=Line2`“), kad pagrindinio įrenginio impulsas valdytų pavaldžiuosius per M8 kabelį. Tai yra: viena kamera, įjungta naudojant `LatticeCamera`, veikia savarankiškai.

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

#### `connect_array()` parašas

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # master trigger fire rate (auto if None)
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=180.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`force_tier` reikšmės:
- `"sim-capture-sim-emit"` — tikrasis sinchroniškumas (visos kameros suveikia tuo pačiu laikrodžio impulsu).
- `"sim-capture-ftd-stagger"` — lankstus laiko srities išskirstymas (kameros siunčia šiek tiek nesutampančiais laikais, todėl paketai laiduose išdėstomi nuosekliai).
- `"slip-emit-and-capture"` — nuoseklus duomenų surinkimas pagal kiekvieną kamerą (be laiko sinchronizacijos; vienintelė galimybė, kai nė vienas rėmo dydis netinka simuliacijai).

`wire_ceiling_mbps` perrašo **kompiuterio nuolatinį laidinio ryšio pralaidumo limitą** MB/s — vienintelį
skaičių, nuo kurio priklauso visos matricos paskirstymas. Palikite nustatymą `None`, kad būtų naudojama automatiškai nustatyta
vertę. Sumažinkite jį, kai masyvas praneša apie GVSP sugadintus rėmelius: automatinė vertė apskaičiuojama
pagal tinklo plokštės nurodytą ryšio greitį, kuris pervertina USB adapterių, siaurų PCIe juostų ir
apkrautų bendrų tinklų pajėgumą — o šis pervertinimas pasireiškia sugadintais kadrais, o ne
akivaizdžiai lėtu ryšiu. Ši vertė išsaugoma projekto masyvo fiksavimo bloke, todėl
atidarius iš naujo arba vėliau nustatant „`connect_array`“, ji atkuriama kaip ir bet kuris kitas masyvo nustatymas.
Žr. [Masyvo būklė](#array-health--which-subsystem-is-losing-frames).

#### Per didelis pasirašymas (minimalus limitas vienai kamerai)

„Sim-emit“ tempas kiekvienai kamerai skiria dalį susidūrimams atsparaus laidinio pralaidumo biudžeto, kurio minimalus limitas yra **8 MB/s vienai kamerai**(`per_cam_floor_bps`). Kai `N × floor` viršija susidūrimų saugumo viršutinę ribą, masyvas**perviršija laidų pajėgumą**— gedimo režimas yra GVSP paketų praradimas, o ne mažesnis kadrų dažnis — ir nėra jokio sprendimo, susijusio su kadrų dydžiu:**agregavimo ir ROI funkcijos mažina baitų skaičių kadre, o ne reguliuojamus baitus per sekundę**, kuriuos lygina agregacinė patikra. Praktinės maksimalios ribos, esant 1 GbE kompiuteriui:**6 kameros su 1500 MTU, 9 su „jumbo“ kadrais** (`max_cams_collision_safe` analizės atsakyme nurodo jūsų laidinio ryšio ribą). Sprendimai: mažiau kamerų, „jumbo“ kadrai visame maršrute arba greitesnis tinklo adapteris.

- Atsakymai `analyze_array_network()` ir `/api/camera/array/connect` apima `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe` ir `per_cam_floor_bps`. Kai `oversubscribed` yra „true“, projekcija **nustato fps laukelius į nulį** (`achievable_fps_max` / `fps_bright` / `fps_dark`), o ne pateikia klaidinančio lėto, bet veikiančio greičio.
- `POST /api/camera/array/connect` priima `pin_resolution` kūno parametrą (**tik HTTP — ne SDK kwarg**; `connect_array`jo neatskleidžia). Prisegimas pašalina „binning walk-down“ saugos tinklą, todėl per daug užregistruotas prisijungimas su nustatytu `pin_resolution` yra**kategoriškai atmestas** su klaida, nurodančia visus sprendimo būdus. Be prisegimo, ryšys tęsiamas su „walk-down“, tačiau rodomas įspėjimas, kad sumažinimas negali išvalyti agregato.
- Aplinkkelis bandymams: nustatykite `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` užpakalinės dalies aplinkoje, kad atmetimas būtų sumažintas iki garsaus įspėjimo — vis tiek prisijungsite ir sutiksite su paketų praradimu.

#### Masyvo būklė — kuri posistemė praranda rėmelius

`GET /api/camera/array/<array_id>/capability` perneša aktyvų `health` bloką
prijungtame masyve, kuris iš naujo įvertinamas **10 sekundžių** slenkamajame lange. Jis suskirsto rėmelių praradimą
į dvi priežastis, kurioms reikia priešingų sprendimų, vietoj vieno „nepilno“ rodiklio, kuris
nenurodo nė vienos iš jų:

| Laukelis | Ką tai reiškia | Kurioje posistemyje |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (pagal serijos numerį) | Rėmelis **atėjo, bet buvo struktūriškai sugadintas**— GVSP paketo praradimas. |**Tinklas**: laidų pralaidumas, tempas, NIC RX žiedas, MTU |
| `never_arrived_rate_pct` (pagal serijos numerį) | Kadrą **visai negavome**— kamera nesuveikė arba nieko iš jos neišsiuntė. |**Sukėlėjas / sinchronizacija**: M8 kabelis, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Blogiausias kiekvienos kameros rodiklis. | — |
| `per_cam_rate_pct` | Bendras neišsamus rodiklis vienai kamerai (abi priežastys kartu). | — |
| `stable_for_seconds` | Kiek laiko kiekviena kamera išliko žemiau 0,01 %. | — |

Kartu su `health`, toje pačioje ataskaitoje pateikiamas skaičius, kuriuo visam paskirstymui trūksta:

| Laukelis | Ką reiškia |
| --- | --- |
| `wire_ceiling_mbps` | Galiojantis nuolatinis kompiuterio laidinio ryšio pralaidumo limitas, MB/s. |
| `wire_ceiling_source` | Šio skaičiaus kilmė, žodžiais — pvz., `USB-capped 200 MB/s (was theoretical 1062; …)` arba `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, kai jį nustatė `wire_ceiling_mbps=` jį nustatė. |
| `nic_is_usb` | `true`, skirtas USB Ethernet adapteriui. |

Šiam galiniam taškui nėra SDK apvalkalo — skaitykite tiesiogiai:

```python
import requests, chloros_sdk

arr = chloros_sdk.attach_array(["213800234", "214000533"])
h = requests.get(
    f"http://127.0.0.1:5000/api/camera/array/{arr.array_id}/capability",
    timeout=10).json()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    arr.disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Skaitymas:** nelygus nuliui `gvsp_corrupt_rate_pct`, kai `never_arrived_rate_pct` yra lygus 0, reiškia,
kad trigeris ir kabelio sinchronizacija veikia puikiai, o 100 % praradimų tenka tinklo keliui — sumažinkite
`wire_ceiling_mbps` ir prisijunkite iš naujo. Priešingas modelis rodo, kad problema yra sinchronizavimo kabelyje arba
sukėlimo linijoje.

> **`target_fps` nėra veiksnys, lemiantis sugadintus rėmelius.** GevSCPD tempas nustatomas vieną kartą
> prisijungus, todėl sumažinus trigerio dažnį keičiasi darbo ciklas, o ne
> vienalaikio siuntimo sprogstamojo perdavimo dažnis. Išmatuotas 5 kartų paklausos sumažinimas nedavė jokio pagerėjimo, o
> sumažinus laidinio ryšio ribą nuo 240 iki 200 MB/s, to paties įrenginio sugadintų rėmų procentas sumažėjo nuo 10,4 % sugadintų rėmelių iki
> 0,00 %.

> **TRI032S programinėje įrangoje nėra automatinio srauto mažinimo funkcijos.**Veikiantis masyvas negali
> pats išspręsti šios problemos; atjunkite ir vėl prijunkite, kad prisijungimo laiko planavimo programa iš naujo suplanuotų pagal
> naują ribą.

**USB Ethernet adapterio greitis yra ribojamas iki 200 MB/s** zondo, nepriklausomai nuo jo
techninių duomenų: efektyvumo lentelė, kuri paverčia ryšio greitį pastoviu skaičiumi, yra
pagrįsta PCIe, o USB tinklo plokštė skelbia savo Ethernet ryšio greitį, tačiau yra ribojama
USB magistralės ir jos tvarkyklės. Ribojimas yra absoliutus, o ne dalinis — USB 1 GbE adapteris
pasiekia ~80 MB/s ir jam tai neturi įtakos.

#### `ArraySession` metodai

| Metodas | Aprašymas |
| --- | --- |
| `status(timeout=10.0)` | „Live“ `{fps, ptp, frame_count, last_error, …}`. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, smart=False, timeout=300.0)` | Viena sinchronizuota įrašymo grupė. Grąžina `CaptureResult` (kadrų žodynų sąrašą + `.skipped`). Eksporto valdymas aprašytas žemiau. |
| `capture(..., smart=True)` | **Išmanusis fiksavimas** — laukia, kol AE stabilizuosis visose kamerose, tada suaktyvina fiksavimą. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, timeout=120.0)` | Greičiausias fiksavimas: tik neapdoroti duomenys + priskirtas DAQ rodmuo (+ nemokamas sujungtas indeksas). Atitinka GUI mygtuką „Fastest Capture“. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Vienkartinis / Nuolatinis / Intervalinis vienoje ribotoje kilpoje. Grąžina `list[CaptureResult]`.**Reikalingas `count` ir (arba) `duration_s`**, kad procesas būtų nutrauktas (SDKyje nėra „Ctrl+C“). |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, timeout=30.0)` | Pradėti tiesioginio sujungtovaizdo įrašą į vaizdo įrašą/GIF → `RecorderHandle`. Vienas kompozicinis įrašymo įrenginys vienam masyvui. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, timeout=30.0)` | Pradėti didelio kadrų dažnio neapdorotų „Bayer“ serijos kadrų seriją → `RecorderHandle`. Atlikti pakartotinį apdorojimą neprisijungus naudojant `build_video()`. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Neprisijungus pakartotinai apdoroti išsaugotą neapdorotų kadrų seriją į kalibruotą vaizdo įrašą (-us). Blokuoja, kol užduotis bus atlikta (`wait=True`) ir grąžina `{outputs, errors, combined}`. |
| `build_video_status(job_id, timeout=15.0)` | Patikrinti neprisijungus vykdomą kūrimo užduotį: `{running, result, error, burst_dir}`. |
| `disconnect()` | Išlaisvina visą masyvą. |

`capture()` eksporto valdymas (tas pats galinis taškas, kurį naudoja GUI / „CLI“):

- `processing` / `levels` — `processing="all"` (arba `levels=["raw","radiance",…]`) išsaugo kiekvieną taikytiną eksporto tipą kiekvienai kamerai; viena `processing` reikšmė išsaugo tik tą lygį.
- `aligned=True` — kiekvieno elemento ne žaliavinio eksporto iškraipymas pagal masyvo [suderinto profilio](#array-alignment) (suregistruotas); neapdoroti duomenys lieka neišlyginti, bet transformacija nurodoma metaduomenyse. Jei masyvas neturi profilio, grįžtama prie neišlygintos versijos (su įspėjimu, rodomu rezultato `alignment` reikšmėje).
- `render_index=False` — praleidžia kiekvienos kameros augmenijos indekso perdangą; pagal numatytuosius nustatymus ji atvaizduojama ten, kur sukonfigūruota.
- `force_daq=True` — išsaugo priskirtą DAQ/DLS rodmenį kaip `.daq` papildomą failą, net jei nė vienam pasirinktam lygiui to nereikia.

**„TIFF“ suspaudimas (tik „HTTP“ reguliatorius):**`ArraySession.capture()` nesiunčia `compression` rakto, todėl taikomas užkulisio numatytasis nustatymas — `POST /api/camera/array/capture` nuskaito `compression` kūno parametrą, `"deflate"` (be nuostolių „zlib“ L1 + horizontalusis prognozuotojas, ~4,1 MB vienam pilnos raiškos kadrui). `"none"` rašo nesuspaustą (~6,3 MB/kadras) su**~5 kartus greitesniu rašymu** — abu yra be nuostolių ir importuojant skaitomi identiškai. „SDK“ nepateikia jokių „kwarg“ parametrų; išeitis yra „`chloros-cli lattice array-capture --compression none`“ arba neapdorotas „HTTP“. „DEFLATE“ taip pat laiko „Python“ GIL, todėl suspaustų duomenų rašymas nėra lygiagrečiai vykdomas per kiekvienos kameros rašymo sriegius — norint užtikrinti nepertraukiamą 8 kamerų pilnos raiškos įrašymą jutiklio dažniu, reikia naudoti `compression: "none"`. Išsamiau: [CLI Nuoroda → array-capture](cli-reference.md).**Eksporto perrašymai pagal narius (tik HTTP):**tas pats galinis taškas taip pat priima `exclude_serials` (sąrašas — pašalinti elementus iš išsaugoto rinkinio; masyvas vis tiek suveikia kaip viena sinchronizuota grupė, o pašalinti elementai grąžinami `excluded`), `serial_levels` (`{serial: [level tokens]}` per-kameros lygio perrašymai) ir `serial_index` (`{serial: bool}` per-kameros indeksų perdengimo perrašymai). Tai yra GUI atitikmenų kūno parametrai ir**dar ne „SDK“ argumentai**; nariai, kurių nėra žemėlapiuose, grįžta prie visam masyvui taikomų `levels` / `render_index`.

##### Praleistų kamerų tikrinimas — `CaptureResult.skipped`

`ArraySession.capture()` grąžina `CaptureResult`, kuris yra `list` pogrupis: iterjuos, indeksuokite, `len()` — visi esami šablonai toliau veikia. Naujas kodas gali patikrinti `.skipped` atributą, kad pamatytų, kurios kameros buvo išskirtos ir kodėl. Dažniausias atvejis – „RGB“ kameros mišriojo filtro masyve, kai prašoma „`processing="radiance"`“ arba „`"reflectance"`“ – spinduliavimas pagal „Bayer“ sistemą plačiajuosčiam jutikliui neturi prasmės, todėl apdorojimo modulis praleidžia tas kameras, o ne generuoja nesąmones.

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

Priežasties žymės atitinka šį šabloną: `<level>-not-applicable-to-rgb-cam` (po vieną įrašą kiekvienam praleistam lygiui, kiekvienas su `level`). Atspindžio koeficientui būdingi praleidimai yra `reflectance-skipped-no-fresh-dls` (nėra naujų žemyn nukreiptų matavimų), `reflectance-skipped-bound-daq-unavailable (…)` (nepavyko pasiekti prijungto DAQ) ir `dls-uncalibrated-band-<nm>` — juosta daugiausia yra už DAQ šviesos jutiklio radiometriškai kalibruoto diapazono ribų (~374–974 nm), todėl atmetamas absoliutus atspindžio pagrindu nustatytas DAQ padalijimas, o kadras grįžta prie jutiklio atsako. Tarp parduodamų SKU tik F988 sukelia šią situaciją; šiai kamerai palaikomas darbo srautas yra atspindžio plokštės darbo srautas.

`processing` lygiai:

| Lygis | Išvestis |
| --- | --- |
| `"raw"` | Vienkanalis „Bayer“ (mono kameros: viena juosta) tiesiai iš jutiklio. |
| `"debayered"` *(numatyta SDK)* | 3 kanalų BGR per bilinearinį demozėjimą (mono kameros: 1 kanalas pilkosios skalės). |
| `"radiance"` | „float32“ W/m²/sr/nm per visą radiometrinę grandinę. Tik multispektrinis režimas — „RGB“ kameros praleidžiamos. |
| `"reflectance"` | „uint16“ 0,.32768 (Pix4D-ready); reikalauja tiesioginio DAQ suporavimo absoliučiam etalonui. Tik daugiaspektrinė. |
| `"display"` | Visa grandinė atitinka GUI peržiūrą (CCM + WB + gama pagal kamerosprofilį). |
| `"all"` | **Vienas failas kiekvienam taikytinam lygiui** kiekvienai kamerai (atitinka GUI „Capture All“ / „CLI“ numatytąjį nustatymą). Grąžintas `CaptureResult` tada turi po vieną kadro žodyną kiekvienam `(cam, level)`, su lygiu kiekviename žodyne; netaikytini lygiai rodomi `.skipped`. DAQ rodmuo, naudojamas bet kuriam atspindžio rėmelio, yra išsaugomas kaip `.daq` papildomas duomenų rinkinys. |

> **Pastaba — numatytasis nustatymas skiriasi nuo „CLI“.** `ArraySession.capture()` numatytasis nustatymas yra `processing="debayered"`; komandos `chloros-cli lattice array-capture` numatytasis nustatymas yra `processing="all"`. Perduokite `processing="all"` aiškiai iš „SDK“, kad atitiktų „CLI“ /GUI daugialypio lygio išsaugojimą.

### Įrašymo režimai ir įrašymo įrenginiai

Matricos paviršius atspindi GUI įrašymo skydelį: vienkartinio, nepertraukiamo, intervalinio ir greičiausio užrakto režimai, taip pat du įrašymo įrenginiai (tiesioginis sudėtinis vaizdas ir neapdoroti serijiniai kadrai → apdorojimas neprisijungus).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Combined-index video/GIF recorder (needs the combined live view streaming)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**yra „SDK“ nepertraukiamo / intervalinio įrašymo ciklas. Kadangi nėra `Ctrl+C`, kuris leistų jį nutraukti iš skripto, jūs**privalote** praeiti pro `count` ir (arba) `duration_s` (ciklas sustos, kai bus pasiektas bet kuris iš jų). `interval_s` skaičiuojamas nuo kiekvieno praėjimo pradžios (atitinka GUI). Likusieji „kwargs“ praeina tiesiai į `capture()`.
- **`record`** yra *stebėjimo lygio*: jis fiksuoja tiesioginį sujungto indekso kompozitą, kaip-rodomas, todėl, kad kadrai būtų įrašomi, turi būti atidarytas sujungtas srautas. Vienas kompozicinis įrašymo įrenginys vienam masyvui (sukelia išimtį, jei jau veikia kitas).
- **`burst` → `build_video`** yra *analizės lygio*: `burst` įrašo neapdorotus kadrus + kiekvieno kadro manifestą + po vieną `.daq` už kiekvieną atskirą DLS rodmenį pagal `<output>/bursts/<base>/` įrašymo ciklo maksimaliu greičiu (be grandinės, be „exiftool“, be tiesioginio peržiūros režimo). `build_video` suderina kiekvieno kadro laiką su artimiausiu `.daq` ir iš naujo paleidžia importo grandinės spinduliavimo/atspindžio/indekso grandinę. `products` yra `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` sąrašas (numatyta reikšmė: sujungtas indeksas). `burst().stop()` taip pat automatiškai paleidžia „best-effort“ sujungto indekso sudarymą, kurio rezultatas grąžinamas kaip `build_job` sustabdymo rezultate.

#### `RecorderHandle`

Grąžinamas `ArraySession.record()` ir `ArraySession.burst()`. Naudokite jį kaip konteksto tvarkyklę, kad procesas būtų automatiškai sustabdytas išėjus iš srities, arba valdykite jį rankiniu būdu.

| Elementas | Aprašymas |
| --- | --- |
| `job_id` | Užkulisinės užduoties identifikatorius (str). |
| `kind` | `"composite"` (iš `record`) arba `"raw"` (iš `burst`). |
| `start_stats` | Žodynas, grąžintas `start` iškvietimo metu. |
| `result` | `None` vykdymo metu; galutinis sustabdymo rezultatų žodynas, kai procesas sustabdytas. |
| `stats(timeout=10.0)` | Tiesioginė užduoties statistika (įrašytų kadrų skaičius, pasiektas kadrų per sekundę skaičius, praėjęs laikas). |
| `stop(timeout=60.0)` | Sustabdo įrašymo įrenginį; grąžina ir išsaugo galutinį rezultatą. Idempotentinis (antrasis iškvietimas grąžina į talpyklą įrašytą rezultatą). |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

### Prisijungimas prie jau prijungto masyvo — `attach_array`

Jei masyvas jau veikia (jį atidarė GUI arba ankstesnė „SDK“ sesija iškvietė `connect_array`), vietoj pakartotinio prisijungimo naudokite `attach_array`, kad gautumėte jo identifikatorių. `connect_array` tokioje situacijoje visada grąžina klaidą „Kamera  <sn>jau yra masyve<id>“, nes „POST“ užklausos siuntimas naudojant „`/array/connect`“ nariui, esančiam pulke, nėra idempotentinis; `attach_array` nuskaito `/api/camera/array/list` ir atitinka pagal „array_id“ arba serijinius numerius.

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

Šablonas: „SDK“ skriptai, veikiantys kartu su darbalaukio GUI, turėtų pirmiausia bandyti `attach_array`, o jei baseine dar nėra masyvo, pereiti prie `connect_array`.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Svarbu — „context-manager“ uždarymas IŠ TIKRŲJŲ nutraukia ryšį.**„`ArraySession.disconnect()`“ visada siunčia „POST“ užklausą į „`/array/disconnect`“; nėra tokio „prijungtas, bet nepriklausantis“ apsaugos mechanizmo, koks yra `CameraSession` / `DAQSensorSession` atveju. Jei naudojatės ištekliu kartu su GUI ir nenorite išardyti masyvo išeinant iš srities,**nenaudokite `with` bloko** — išsaugokite identifikatorių įprastame kintamajame ir praleiskite aiškų `disconnect()`:
>
> ```python
> arr = chloros_sdk.attach_array(serials)
> arr.capture("output/", processing="reflectance")
> # … script ends; array stays up for the GUI
> ```

### Tinklo analizės pagalbinė priemonė

Naudinga prieš atidarant masyvą — leidžia numatyti, ar jūsų siūlomi nustatymai tiks:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    print(f"Shrink to {r['out_width']}x{r['out_height']} binning={r['binning']}")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

`status` yra vienas iš `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (kitu atveju – `error`). `auto_capped_fps` reiškia, kad prašoma skiriamoji geba tinka RX žiedui tik esant ribotam suaktyvinimo dažniui — išlaikykite skiriamąją gebą ir perduokite `target_fps=result["recommended"]["recommended_target_fps"]` į `connect_array` (žr. [6 pavyzdį](#6-capability-probe-before-connecting-a-4-cam-array)).

**Kaip skaityti projekciją** (tas pats modelis kaip GUI skydelyje „Array Settings“):

- **Serija (`frame_bytes_total`) sumuojama pagal kiekvieną kamerą, atsižvelgiant į kiekvienos kameros tikrąjį pikselių formatą.**Mono**M3M**kameros transliuoja Mono12 (2 B/px) nepriklausomai nuo to, kokį `pixel_format` perduodate, taigi 4 kamerų pilnos raiškos kadras yra**~25 MB** su trimis „mono“ kameromis, o ne ~12,6 MB, kaip būtų galima manyti, darant prielaidą, kad visos kameros yra 8 bitų. Serveryje kiekvienos kameros formatas nustatomas pagal jos modelį.
- **Pralaidumas (`burst_fits_nic_ring`) atsižvelgia į duomenų išsiuntimo greitį**, o ne į visą duomenų srautą palyginti su žiedu: „sim-emit“ tinka, kai pagrindinis kompiuteris ištuština RX žiedą greičiau, nei kameros jį užpildo. 10G pagrindinis kompiuteris + 1 GbE kameros**priima** pilnos raiškos duomenis net tada, kai duomenų srautas viršija žiedo talpą; 1 GbE pagrindinis kompiuteris blokuoja (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` yra konservatyvus serijinio duomenų išgavimo ribinis dydis** — `max(readout+emit, N×emit)`, kai kiekvienos kameros duomenų siuntimas apribotas iki 1 GbE kameros ryšio greičio, nepriklausomai nuo ekspozicijos. Pvz., ~2,8 kadrų per sekundę 4kameros pilnos raiškos 12 bitų masyvui (atitinka vykdymo metu išmatuotus ~2,7–3,0). Pilnas modelis: [CLI Nuoroda → Masyvo kadrų per sekundę ir serijos modelis](cli-reference.md#array-fps--burst-model).
- **Perteklinis užsakymas (`oversubscribed: true`) reiškia, kad N × minimalus kadrų skaičius vienai kamerai viršija susidūrimo-saugumo ribą** — fps laukai (`achievable_fps_max` / `fps_bright` / `fps_dark`) rodo 0, o automatinis susiaurinimas/grupavimas šio problemos neišsprendžia (jos sumažina baitų skaičių kadre, o ne reguliuojamą baitų skaičių per sekundę). Šią problemą galima išspręsti naudojant mažesnį kamerų skaičių, „jumbo“ rėmus arba greitesnį tinklo sąsajos modulį (NIC); `max_cams_collision_safe` praneša apie viršutinę ribą (6 pilnos raiškos kameros 1 GbE tinkle su 1500 MTU, 9 – naudojant „jumbo“ rėmus). Atsakyme taip pat pateikiami kodai `aggregate_demand_bps`, `collision_safe_ceiling_bps` ir `per_cam_floor_bps` (8 MB/s). Žr. [Per didelis pasirašymas](#over-subscription-the-per-cam-floor).

### Atradimas ir sąrašas

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

---

## „Smart-AE“ / „Smart-Capture

„LATTICE“ matricos, vos tik prijungtos, fone nuolat atlieka automatinį ekspozicijos nustatymą (AE), tačiau naujai nukreiptam vaizdui sukonverguoti reikia šiek tiek laiko. **„Smart-capture“** yra patogi funkcija: ji tikrina kiekvienos kameros ekspoziciją, laukia, kol matrica stabilizuosis visame lange, ir tada paleidžia fotografavimą. Tai GUIfunkcija: darbalaukio programos mygtukas „smart“ capture iššaukia tą patį užkulisinį galinį tašką.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

Naudojant `ChlorosProject` (kitas skyrius), atsiranda daugiau nustatymų:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

„Smart-AE“ politika pagal numatytuosius nustatymus yra konservatyvi. Sugriežtinkite `exposure_tolerance_pct`, jei atliekate reiklų radiometrinį darbą; sušvelninkite, jei scenos keičiasi greitai ir jums reikia tik „pakankamai artimo“ rezultato.

---

## DAQ jutiklių sesijos

Nuolatinis užpakalinės sistemos rezervas spektriniams jutikliams (DAQ-U per USB, DAQ-M per BLE, DAQ-E per Ethernet). Atkartoja kameros veikimą: išmanusis aptikimas, pulko pakartotinis naudojimas, idempotentinis prijungimas.

### Išmanusis aptikimas (be konfigūracijos)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

Prioritetas: Ethernet → BLE → USB. Perduokite bet kurią aiškią užuominą, kad užfiksuotumėte perdavimo būdą.

### Užfiksuotas perdavimo būdas

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

### `DAQSensorSession` metodai

| Metodas | Aprašymas |
| --- | --- |
| `status(timeout=10.0)` | Duomenų bazės įrašo santrauka (transliacijos/įrašymo būsena, bangų ilgio diapazonas, kalibravimo SHA, integravimo laikas, frame_avg, AE būsena). |
| `latest(n=1, timeout=10.0)` | Grąžina iki N naujausių spektro kadrų. |
| `stream_start()` / `stream_stop()` | Tęsti / pristabdyti srautą (rankenėlė lieka atidaryta). |
| `record_start(output_dir=None, device_name=None)` | Pradeda įrašyti .daq failą. Grąžina failo kelią. Neveikia su DAQ-U/M be AWS kalibravimo paketo (DAQ-E yra išimtis). |
| `record_stop()` | Sustabdo įrašymą. Grąžina `{path, rows}`. |
| `disconnect()` | Atleidžia iš rezervo. Nėra veiksmo, jei rankenos yra prijungtos, bet nepriklauso vartotojui. |

> **Kapaciteto koregavimo profiliai (`cap_id`) nėra „SDK“ reguliatorius.** `connect_daq_sensor()` / `DAQSensorSession` neatskleidžia jokio `cap_id` parametro ar `set_cap` metodą. Pasirinkite laivyno ribos koregavimo profilį per „CLI“ (`chloros-cli daq pool-connect --cap-id …` / `chloros-cli daq pool-set-cap …`) arba backend’o `/api/daq` „HTTP“ maršrutus (`/api/daq/connect` ir `/api/daq/<id>/cap-id` priima `cap_id`).

### Atradimas — adreso, su kuriuo prisijungti, paieška

`discover_daq_sensors()` nuskaito USB / BLE / ETH, ieškodamas jutiklių, kuriuos *galėtumėte* atidaryti. Tai yra DAQ atitikmuo `discover_lattice_cameras()`, ir vienintelis būdas gauti **DAQ-M BLE MAC** — DAQ-E turi įrenginio vardą, o DAQ-U — COM prievadą, tačiau MAC adresas nėra nei išspausdintas ant įrenginio, nei nurodytas operacinėje sistemoje.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               None   {'manufacturer': 'Intel'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| Laukas | Aprašymas |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | COM prievadas / BLE MAC / kompiuterio vardas — perduoti į `connect_daq_sensor` kaip `port=` / `mac=` / `eth_host=`. |
| `display` | Žmogui suprantama etiketė. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E`, arba `None`, jei prievadas negali būti atpažintas (USB serijiniai adapteriai be zondo yra neatskiriami, todėl nežinomi prievadai rodomi, o ne paslėpiami). |
| `extra` | Duomenys pagal perdavimo būdą (BLE skelbiamas pavadinimas, USB gamintojas, DAQ-E IP/FW/…). Tuščios reikšmės praleidžiamos. |

| Parametras | Numatytasis | Aprašymas |
| --- | --- | --- |
| `transports` | visi trys | Seką (arba CSV eilutė), ribojanti nuskaitymą. Verta nurodyti, jei žinote, ko norite — BLE yra lėčiausia grandis. |
| `scan_timeout` | 5 | Skenavimo langas kiekvienam perdavimo būdui sekundėmis; užkulisiai riboja iki 1–20. |
| `timeout` | 60,0 | „HTTP“ viršutinė riba visam iškvietimui (kaip ir kitur „SDK). |
| `auto_start_backend` | `True` | Sukuria vietinį užpakalinį modulį, jei jo dar neveikia. Niekada nesukuria nuotolinio `backend_url`. |

> **Jau atidaryti jutikliai rezervuote nerodomi.** Prijungtas BLE įrenginys nustoja siųsti skelbimus, o atidaryto COM prievado negalima patikrinti, todėl atradimo funkcija pateikia sąrašą to, kas *prieinama prisijungimui*. Tuoj pat po to, kai ką nors prijungėte, tikėtinas tuščias rezultatas — naudokite `list_daq_sensors()` tiems įrenginiams, kuriuos jau turite. Transportai, kurių nuskaitymas negali vykti (neįdiegta „bleak“ / „zeroconf“), yra praleidžiami, o ne rodomi kaip klaida, todėl kompiuteris be „Bluetooth“ vis tiek gauna atsakymus dėl USB ir ETH.

### Sąrašas

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Bendras veikimas su GUI / „CLI“

Jei GUI jau turi atidarytą jutiklį, iškvietus „`connect_daq_sensor(port="COM3")`“ iš „Python“, grąžinamas identifikatorius, pažymėtas „`already_connected=True`“. Tuomet sesijos „`disconnect()`“ yra neveikiantis, todėl jūsų „SDK“ scenarijus, uždarant programą, neištrins jutiklio iš GUI.

### Tiesioginės aparatinės įrangos klasės (be užpakalinės dalies)

`daq_sdk` yra pakartotinai eksportuojamas per `chloros_sdk`, todėl jūs taip pat galite valdyti jutiklius nuo pradžios iki pabaigos proceso metu be užpakalinės dalies:

> **Prieinamumas:**„`daq_sdk`“ pateikiamas su „Chloros“ darbalaukio diegimu,**ne** su PyPI paketu — `pip install chloros-sdk` suteikia jums `lattice_sdk`, bet palieka `chloros_sdk.DAQ_AVAILABLE == False`. Prieš naudodami šias klases, patikrinkite tą žymę; kompiuteryje, kuriame veikia tik pip, valdykite jutiklį per [`connect_daq_sensor()`](#daq-sensor-sessions) , kuriam nereikia jokių vietinių perdavimo bibliotekų.

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

# Discovery
for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

# Direct DAQ-U
sensor = DAQUSensor(port="COM3")
sensor.connect()
sensor.start_streaming()
# ... use sensor.add_spectrum_callback(...) ...
sensor.stop()
```

Jei norite bendrai valdyti jutiklį su GUI, rinkitės „smart-connect“ kelią (`connect_daq_sensor`); tiesiogines klases naudokite be grafinės sąsajos skriptams, kurie išimtinai valdo jutiklį.

---

## Projekto automatizavimas — `ChlorosProject`

Išsaugotas „Chloros“ projektas yra aplankas, kuriame yra `cameras.json` + `sensors.json` + `project.json`. `open_project` įkelia manifestą, o `connect_all` prijungia visus išsaugotus įrenginius su jų išsaugotais nustatymais — tokia pati aparatinės įrangos būsena, kokią sukurtų grafinė vartotojo sąsaja.

### Minimalus pavyzdys

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Read a DAQ
spectrum = proj.sensors["Sky"].read()

# Trigger every device simultaneously
proj.capture_all("./out")

proj.disconnect_all()
```

Arba kaip konteksto tvarkyklė:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` metodai

| Metodas | Aprašymas |
| --- | --- |
| `connect_all(cameras=True, arrays=True, sensors=True, verbose=False, align=None)` | Aptinka ir prijungia kiekvieną išsaugotą įrenginį. Grąžina prisijungimo ataskaitą pagal klases. Naudoja veikiantį užkurtį, jei toks klausosi `127.0.0.1:5000`; priešingu atveju tyliai pereina prie tiesioginio (be užkurtio) `lattice_sdk` įrenginių valdymo — jis niekada nesukuria užkurtio. |
| `disconnect_all()` | Viską nutraukti. |
| `capture_all(output_dir=".")` | Po vieną kadrą iš kiekvienos kameros + masyvo + spektro iš kiekvieno jutiklio. |
| `stream(camera, overlays=False, fps=10.0)` | Generatoriaus našumasBGR `numpy` kadrus iš nurodytos kameros (arba masyvo). `overlays=False` yra tiesioginė `lattice_sdk` kadrų paėmimo kilpa (matricos generuoja `{serial: frame}` žodynus). `overlays=True` nukreipia per `ChlorosLocal.camera_stream()` → į galinio įrenginio `/api/camera/<serial>/stream-annotated` MJPEG srautą, o kameros išsaugotas `ui.overlay` blokas perduodamas kaip užklausos parametrai. Reikalingas užpakalinės dalies režimas ir **autonominė kamera**: tiesioginio režimo kamera sukelia `RuntimeError` (užpakalinė dalis negali paimti kameros, kurią valdo šis procesas), o masyvas sukelia `NotImplementedError` (sukuria kompozicinius perdengimus pagal kiekvieną kamerą — transliuoja elementą pagal pavadinimą). Vienkartinis ekvivalentas: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Atlikti suderinimą kiekviename šiuo metu prijungtame masyve. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Vykdyti kalibravimo / indeksavimo procesą projekto vaizduose (apima `ChlorosLocal.process`; šie keturi yra **vieninteliai** priimtini raktiniai argumentai — `indices=` ir pan. sukelia klaidą `TypeError`; indeksus nustatykite per `ChlorosLocal.configure()`). Lėtai sukuria `ChlorosLocal()`, kuris automatiškai paleidžia užkurtį. |

Atributai:
- `proj.cameras` — `Dict[str, CameraHandle]`, indeksuotas pagal pavadinimą IR serijos numerį.
- `proj.arrays` — `Dict[str, ArrayHandle]`, indeksuojamas pagal pavadinimą IR array_id.
- `proj.sensors` — `Dict[str, SensorHandle]`, indeksuojamas pagal pavadinimą IR slot_id.
- `proj.config` — `project.json["config"]` žodynas.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**Apdorojimo lygiai.** `capture()`, `grab()` ir `frame_stream()` visi naudoja tą patį `processing`
žymeklį, o grandinė yra kumuliatyvi — kiekvienas lygis vykdo viską, kas yra aukščiau jo:

| Lygis | Išvestis | Pastabos |
| --- | --- | --- |
| `raw` | 1 kanalas „Bayer“, jutiklio natūralus | Nėra demozėjimo. Šiame lygyje perdangos nėra prieinamos. |
| `debayered` | 3 kanalai BGR (**numatyta**) | Bilinearinis demozėjimas. Vienintelis lygis, veikiantis be „backend“ režimo. |
| `radiance` | float32, W/m²/sr/nm | Pilna radiometrinė grandinė: demosaikas + 3×3 atskyrimas (multispektrinis) + DSNU + plokščio lauko korekcija + NIST skalė, išskaičiuojant ekspoziciją × stiprinimą, kad reikšmės būtų absoliučios. |
| `reflectance` | uint16, 32768 = 1,0 | Spinduliavimas, padalytas iš žemyn nukreipto spinduliavimo intensyvumo (ρ = π·L/E). Reikalingas DLS/DAQ rodmuo — žr. pastabą žemiau. |
| `display` | 8 bitų, panašus į sRGB | GUIekvivalentiškas atvaizdavimas: CCM + baltos spalvos balansas + gama per kameros aktyvų spalvų profilį. |

Visi parametrai, išskyrus `debayered`, reikalauja „backend“ režimo; tiesioginio režimo kamera sukelia
`NotImplementedError`. `reflectance` reikalauja tinkamo žemyn nukreipto rodmens — kadro pabaiga automatiškai perkelia
sukauptą DAQ į kameros DLS lizdą, tačiau jei DAQ nėra susietas, grandinė atmeta
atspindžio išėjimą ir sąžiningai pažymi pažeminimą grąžinamuose metaduomenyse, o ne tyliai
grąžina prastesnį produktą.

> **Atspindžio DN skalė — jos nekoduokite kietai.** LATTICE atspindys naudoja `32768` = ρ 1,0 ir pažymi
> XMP `Chloros:PixelScale=32768`; „Survey3“ atspindžio koeficientas naudoja `65535` = ρ 1.0 ir neturi
> `Chloros:*` žymių. Perskaitykite žymą ir padalinkite iš jos. Ji apibrėžta uint16 srityje, todėl išlieka
> `32768` kiekvienam formatui, kuris keičia mastelį (16 bitų „TIFF“, 8 bitų „PNG“ /JPG, 32 bitų procentais) — pirmiausia normalizuokite
> išsaugotą duomenų tipą pirmiausia normalizuokite atgal į „uint16“ (×257 iš 8 bitų, ×65535 iš „float“). Vienintelė išimtis:
> 8 bitų šaltinio įrašas, užrašytas kaip 8 bitų „TIFF“, yra *apkarpytas*, o ne perskalintas, todėl jam netaikomas joks mastelis
> — „Chloros“ tokiu atveju visiškai praleidžia `PixelScale` ir „MicaSense“ tuplą. Trūkstamą
> žymą LATTICE atspindžio faile traktuokite kaip „nėra galiojančio mastelio“, o ne kaip numatytąjį.

> **EXIF duomenys perkeliami į eksportą.** `process()` nukopijuoja šaltinio įrašo GPS bloką
> **ir jo ExifIFD** į kiekvieną produktą, todėl eksportuojami failai turi `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` ir `CameraSerialNumber`, taip pat
> georeferencavimą. `FocalLength` yra duomenys, pagal kuriuos „Pix4D“ apskaičiuoja žemės paviršiaus taškų atstumą — be jo
> rekonstrukcija atliekama pagal visiškai neteisingą mastelį (vienu išmatuotu atveju 411 m plotas
> virto 47,8 km plotu). Kopija sąmoningai nėra `-all:all`: IFD0 struktūrinės žymos sutrikdo
> „LATTICE“ išvestį, o `ExifImageWidth`/`Height` yra pašalinti, nes jie apibūdina šaltinio
> fiksavimą, o ne eksportuotą rasterį.

Fiksavimo etapo papildomos žymės (taikomos radiometriniams lygiams — `radiance`, `reflectance`, `display`):

| Žymė | Numatytasis | Reikšmė |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + lygio lauko korekcija + 3x3 atskyrimas + NIST radiometrinė skalė. |
| `apply_white_balance` | `True` | WB LUT. DLS, kai prie kameros prijungtas duomenų surinkimo įrenginys (DAQ). |
| `apply_index` | `False` | Augmenijos indekso vertinimas. |
| `index_expression` | `None` | Formulės perrašymas. Jei laukas nėra tuščias → indeksas įjungiamas automatiškai. |
| `annotated` | `False` | GUI dekoracijų (zebra/tinklelis/piko rodymas) perdengimas. Netaikoma `raw`. |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **Grąžinamojo tipo tipas yra `CapturePathMap`, o ne `Dict[str, str]`.**
> `chloros_sdk.CapturePathMap` yra `Dict[str, Union[str, List[str]]]`: vieno lygio
> `processing` kiekvienai serijai suteikia po vieną kelią, o daugialygis (`"all"` arba
> aiškus `levels` sąrašas) suteikia jam **eilišką sąrašą** visų produktų, išsaugotų tai
> kamerai. Tiesioginis sujungtas kompozitas, jei toks būtų transliuojamas, patenka po papildomu
> raktu `"combined"`, o ne po serijiniu numeriu. Kodas, kuris daroma prielaida, kad „`str`“ neveikia su
> sąrašo forma, nors tipų tikrintuvas tam neprieštarauja — anotacija nurodė `Dict[str, str]`
> kurį laiką po sąrašo formos išleidimo, todėl ir egzistuoja šis aliasas. Normalizuokite
> kai norite plokščios formos:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

### Masyvų suderinimas

`ArrayHandle` atskleidžia visą suderinimo paviršių. Profiliai pagal numatytuosius nustatymus galioja tik sesijos metu — norėdami juos išsaugoti, aiškiai iškvieskite `export_alignment()`.

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### Suderinimas prisijungimo metu

`connect_all(align=...)` gali automatiškai suderinti kiekvieną masyvą prisijungimo metu:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

Jei nenurodyta, grįžtama prie `project.json["config"]["auto_align_on_connect"]`.

### `SensorHandle`

```python
spectrum = proj.sensors["Sky"].read()
# (spectrum_list, is_saturated, integration_time, x, y, z) — matches the
# daq_sdk add_spectrum_callback signature.
```

---

## Tiesioginė aparatinė įranga (be užkulisio)

Jei norite visiškai nepriklausyti nuo užkulisio (CI, „headless“ robotai, įterptinė įranga), importuokite „`lattice_sdk`“ ir „`daq_sdk`“ tiesiogiai — abu jie yra pakartotinai eksportuojami per „`chloros_sdk`“. Įspėjimas dėl `CAMERA_AVAILABLE` / `DAQ_AVAILABLE`: `lattice_sdk` yra PyPI pakete (tačiau jam reikalinga „Arena“ SDK vykdymo aplinka), o `daq_sdk` pateikiamas tik su darbalaukio versija.

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### Nustatymai ir paleidimas

Trys iš keturių nustatymų **freeveikimo**: kamera eksponuoja nuolat, o
`capture()` grąžina kitą kadrą. `triggered` yra išimtis — jis parengia
kamerą laukti aparatinės ribos 2-oje eilutėje, todėl nieko nefiksuoja, kol ji nepasiekia.

| Nustatymas | Trigeris | Naudokite, kai |
| --- | --- | --- |
| `default` | laisvas veikimas | bendras naudojimas |
| `high_speed` | laisvas veikimas | 8 bitai, 60 kadrų per sekundę riba, trumpa ekspozicija |
| `high_quality` | laisvas veikimas | 12 bitų, be kadrų per sekundę apribojimo — įprastas pasirinkimas fotografuojant |
| `triggered` | **įjungta, 2-oji linija** | kamera prijungta M8 sinchronizavimo kabeliu, o ją suaktyvina kitas įrenginys |

Jei pasirinksite „`triggered`“ (arba patys nustatysite „`trigger_mode="On"`“), kai 2-oji linija
nėra suaktyvinta, kiekvienas „`capture()` pasieks laiko limitą — teisingai, nes jūs paprašėte,
kad kamera palauktų. „SDK“ paaiškina, kodėl taip atsitinka; žr.
[SC_ERR_TIMEOUT fotografavimo metu](#direct-hardware-backend-free).

> **Pastaba — „GVSP probe“ / `SC_ERR_TIMEOUT -1011` pranešimai prisijungiant nėra klaidos.**&gt; Prisijungiant „SDK“ bando susitarti dėl**jumbo rėmelių** (9000 baitų GVSP paketus), siekdamas didesnio pralaidumo. Tiesioginiame „taškas-taškas“ tinklo plokštės ryšyje (pvz., vietinio ryšio `169.254.x.x` adresas) tinklas paprastai negali perduoti „jumbo“ rėmų, todėl šis bandymas baigiasi laiko limitu ir į žurnalą įrašomos tokios eilutės:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Tai yra **numatyta atsarginė priemonė**: „SDK“ automatiškai grįžta prie standartinių 1500 baitų paketų, o kamera toliau prisijungia kaip įprasta (toliau einančios eilutės „`[chunk-enable …]`“ yra įprastos prisijungimo sekos dalis). Duomenų fiksavimas vis dar veikia.
>
> Šį patikrinimą galite praleisti, bet **tai ne tik žurnalo įrašų slopintuvas — jis išjungia „jumbo“ rėmus.** Kamera atsako į „Don&#x27;t-Fragment“ pingus tik iki 1500 baitų, nesvarbu, koks geras būtų jūsų tinklas, todėl vien ping testu niekada nepavyks aptikti „jumbo“; šis patikrinimas yra vienintelis būdas tai padaryti. Išjunkite jį, ir kamera bet kuriame tinkle visada siųs standartinius 1500 baitų paketus:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Tai verta daryti tik tinkle, apie kurį *žinote*, kad jis nepalaiko „jumbo“ paketų, nes tai sutaupo maždaug vieną sekundę prisijungimo laiko kiekvienai kamerai. Kadangi tai yra realus, o ne tik kosmetinis kompromisas, „SDK“ dabar tai nurodo, kai šią funkciją naudojate:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Nepalikite šio nustatymo, nebent turite priežastį.** Palikus įjungtą, kiekvieno prisijungimo metu iš naujo matuojamas jūsų turimas tinklas: prijunkite prie „jumbo“ paketus palaikančio komutatoriaus, ir kitą kartą prisijungus „jumbo“ paketai bus aptikti automatiškai, nereikės nieko konfigūruoti ir nereikės perkrauti sistemos.
>
> Jei *norite* „jumbo“ pralaidumo, įjunkite „jumbo“ nuo galo iki galo (NIC MTU 9000 + komutatorius, kuris juos perduoda), arba nustatykite jį naudodami `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000`, kai žinote, kad ryšio linija jį palaiko — tačiau geriau rinkitės komandą `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …`, o ne nustatykite jį visam laikui, nes fiksuotas dydis praleidžia tikrinimą ir nustoja prisitaikyti prie priešais esančio tinklo. **Kiekvienas** įrenginys kelyje turi perduoti „jumbo“ paketus — įskaitant bet kokį PoE skirstytuvą ar įterptuvą, nes tai dažniausiai yra priežastis, dėl kurios kitaip „jumbo“ paketus palaikanti konfigūracija negali jų perduoti.

> **`SC_ERR_TIMEOUT -1011`, pasirodantis `capture()` / `grab*()` metu, yra kita problema — tai tikra klaida.**&gt; Pirmiau pateikta pastaba susijusi tik su klaida „`-1011`“, užregistruota**prisijungimo laiko zondo**. Ta pati klaida, užfiksuota**fiksavimo** metu, reiškia, kad kamera prisijungė sėkmingai, bet nesiunčia jokių vaizdų:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> Tai išduoda kamera, kurios *valdymo* kanalas veikia tinkamai — aptikimas veikia, nustatymai ir `[chunk-enable …]` įrašai sėkmingi — tačiau *kiekvienas* kadras viršija laiko limitą.
>
> **Dažniausiai priežastis yra ta, kad kamera yra nustatytas veikti pagal aparatinį trigerį.** Esant klaidoms `trigger_mode="On"` ir `trigger_source="Line2"`, kamera nieko nesiunčia, kol M8 sinchronizavimo kabelyje neatsiranda elektrinis impulsas. Jei šią liniją valdančio kabelio nėra, kiekvienas kadrų paėmimas laukia amžinai. Kamera nėra sugedusi, o tinklas veikia gerai — ji daro būtent tai, kas jai nurodyta.
>
> Kodai „`CameraSettings()`“ ir „`default`“ / „`high_speed`“ / `high_quality` nustatymuose įjungtas laisvasis režimas, o įjungus įrašymą, kurio laikas baigėsi, rodomas paaiškinimas, o ne tik „`-1011`“. `PRESETS["triggered"]` pagal numatytą veikimą įjungia „Line2“.
>
> Norint priversti bet kurią kamerą veikti laisvuoju režimu:
>
> ```python
> settings = PRESETS["high_quality"]
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> Jei su `trigger_mode="Off"` vis tiek pasibaigia laiko limitas, kamera tikrai neteikia duomenų — atsiųskite mums žurnalą ir `ip link show`.

#### Spalvų profiliai (tiesioginis peržiūros vaizdas „RGB“) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` parenka ekrano spalvų profilį **tiesioginiam peržiūros vaizdui** „RGB“ kamerose (daugiaspektrinės kameros ignoruoja šį nustatymą):

| Profilis | Reikšmė |
| --- | --- |
| `raw` | Visiškai apeiti radiometrinę grandinę. |
| `linear` | DSNU + „flat“ + baltos balanso nustatymas, be CCM, be gama. |
| `natural` | Linijinis + išmatuotas CCM + sRGB gama, tik su paprastu apdorojimu (spalvų išlyginimas + šviesių sričių desaturacija) — realistiškas numatytasis nustatymas. |
| `enhanced` | `natural` su pilna „hub-parity“ apdaila (spalvų apvalių pašalinimas, gyvumas, CLAHE vietinis kontrastas). Turtingesnė išvaizda, tačiau **apdailos kaina vienam kadrui yra maždaug dvigubai didesnė**, todėl mažesnis tiesioginis kadrų dažnis. |
| `custom_temp` | `natural`, bet baltos spalvos balansas (WB) pririštas prie `custom_cct_k` Kelvino (DLS ignoruojamas; apribotas iki 2000–10000 K užkulisiuosepusėje). |

Profilis yra **tik tiesioginiam peržiūrėjimui skirtas** greičio/vaizdo reguliatorius: išsaugoti kadrai visada gauna pilną, turtingą apdailą, nepriklausomai nuo pasirinkto profilio, todėl pasirinkus `natural`, siekiant sutaupyti kadrų generavimo laiko, į diską įrašomos medžiagos kokybė nesumažėja. Nežinomas profilis padidina `ValueError`; kai „chloros“ užpakalinė dalis yra pasiekiama, pakeitimas taip pat yra siunčiamas POST metodu į ją, todėl kitas peržiūros kadras tai atspindi (tiesioginio „SDK“ naudotojai be užpakalinės dalies vis tiek gauna nustatymų pakeitimus).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Mono (M3M) kameros ir `Calibration`

Mono **M3M** kamera (`M3M-<lens>-F<wavelength>`) yra vienos juostos: viena pilkosios skalės plokštuma, be „Bayer“ mozaikos, be 3×3 spektrinio persipynimo matricos. `Calibration` ją atpažįsta ir pateikia `is_mono` žymę. Atspindžio koeficientas vis dar taikomas kaip radiometrinis žemėlapis pagal kiekvieną juostą (atskyrimas yra tapatybės matrica), tačiau daugiajuostiniai skaičiavimai naudojant vieną kamerą duoda prasmingus rezultatus, o ne beprasmiškus:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

Norint sudaryti augmenijos indeksą naudojant monochromatinę įrangą, sujunkite kelias skirtingų bangų ilgių M3M kameras į suderintą daugiajuostinį rinkinį (žr. [Masyvo suderinimas](#array-alignment)) ir apskaičiuokite indeksą visam šiam rinkiniui, o ne vienai kamerai.

DAQ tiesioginis režimas:

```python
from chloros_sdk import (
    DAQUSensor, DAQMSensor, DAQESensor,
    SensorFleet, discover_all, DiscoveredSensor,
    apply_sensor_settings, SensorSettings,
)

for d in discover_all(timeout=3.0):
    print(d)

sensor = DAQUSensor(port="COM3")
sensor.connect()
apply_sensor_settings(sensor, settings={"integration_time_ms": 64, "frame_avg": 20})
sensor.start_streaming()
# ... sensor.add_spectrum_callback(your_callback) ...
sensor.stop()
```

> **`apply_sensor_settings` priimtini raktų kodai**— būtent `integration_time_ms`, `frame_avg`, `ae_enabled`, `sunshine_diffuser_installed` (DAQ-E; neberekomenduojama, vietoj jos rekomenduojama naudoti `cap_id`), `filter_model` (DAQ-M) ir `cap_id` (visi DAQ tipai; `None`/`""`/`"none"` = tik jutiklis, be dangtelio korekcijos). Nežinomi raktiniai žodžiai**tyliai ignoruojami** — pvz., `{"integration_time": 64}` nieko neveikia (turėtų būti `integration_time_ms`). Grąžina `{"applied": [...], "errors": {...}}` ir niekada nesukelia išimties.

`chloros_sdk` pakartotinai eksportuoja tik aukščiau naudojamą pagrindinį paviršių. Visas viešasis `daq_sdk` „API“ (22 pavadinimai) prideda šiuos elementus — importuokite juos tiesiogiai iš `daq_sdk`:

```python
from daq_sdk import (
    DAQULogger, DAQMLogger, DAQELogger,     # rotating-file recorders (the ones the GUI uses)
    ConnectResult, FleetRecordResult,       # SensorFleet result types
    discover_all_detailed, build_sensor,    # detailed discovery + build-by-descriptor
    scan_eth_devices, DaqEControl,          # DAQ-E Ethernet scan + control channel
    scan_ble_devices, detect_ble_device, list_ble_devices,   # DAQ-M BLE discovery
    detect_port, list_serial_ports,         # DAQ-U serial-port discovery
    TcpSerial,                              # serial-over-TCP transport shim
)
```

---

## Išimtys

Pagaukite bazinę klasę, kad galėtumėte tvarkyti „bet kokią klaidą, susijusią su „Chloros““:

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

> `ChlorosAuthenticationError` ir `ChlorosConfigurationError` eksportuojami aukščiausiame lygyje kartu su kitais; juos taip pat galima importuoti iš `chloros_sdk.exceptions`, kaip parodyta.

Hierarchija:

```

ChlorosError
├── ChlorosBackendError           (backend failed to start / unreachable)
├── ChlorosConnectionError        (HTTP transport failure)
├── ChlorosLicenseError           (subscription / tier gate)
├── ChlorosAuthenticationError    (login required)
├── ChlorosConfigurationError     (bad configure() / open_project() inputs)
└── ChlorosProcessingError        (pipeline failed)

ChlorosConnectError                (raised by connect_camera / connect_array /
                                    connect_daq_sensor only — derives from
                                    plain Exception, NOT from ChlorosError,
                                    so `except ChlorosError` will not catch it)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## Pavyzdžiai nuo pradžios iki pabaigos

### 1. Aplankų apdorojimas su pasirinktiniu pažangos juostele

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. Tiesioginis LATTICE masyvas → atspindžio koeficientas + DAQ etalonas

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. Projekto pagrįsta duomenų surinkimo kampanija

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. Daugiakamerinis kadrų srautas → „NumPy“ apdorojimo grandinė

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. „Headless“ tiesioginis įrangos (be užkulisio) įrašymo scenarijus

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. Galimybių patikrinimas prieš prijungiant 4 kamerų masyvą

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["out_width"], height=r["out_height"],
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Įrašymo recepto ekvivalentas (grynasis „Python“)

„CLI“ recepto DSL turi tiesioginį „Python“ ekvivalentą:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply
    # (CameraHandle has no direct apply method; use the underlying lattice_sdk
    #  helper or the backend's /api/camera/<sn>/apply-settings via requests)
    # For most cases just use cam.cam.set_exposure(...) in direct mode or
    # the GUI's saved settings via project.connect_all().

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## Automatinis backend paleidimas

„Smart-connect“ įėjimo taškai — `connect_camera`, `connect_array`, `connect_daq_sensor`, ir `discover_lattice_cameras` — yra „HTTP“ ploni klientai, kurie daro prielaidą, kad užkulisiai klausosi prievado `127.0.0.1:5000` (numatytoji „“ sąsajos „URL“). Kai GUI arba „CLI“ jau veikia, vienas iš jų veikia. Jei paleidžiama tik iš skripto, jo gali nebūti — todėl šios funkcijos **automatiškai paleidžia pridedamą serverio dvejainį failą** (be lango, taip pat kaip tai daro „`ChlorosLocal`“) prieš pirmąjį jų iškvietimą, tada laukia iki „`backend_startup_timeout`“, kol jis paleidžiamas.

Taisyklės:

- **Visada paleidžiamas tik vietinis „URL“.** Tinkamas yra `backend_url`, nukreipiantis į `localhost` / `127.0.0.1` / `[::1]`; bet kuris kitas kompiuteris laikomas kieno nors kitokompiuteris ir niekada nebus paleistas.
- **Fono procesas paliekamas veikti, kad būtų galima jį pakartotinai naudoti** (taip pat kaip ir „CLI“) — jūsų skriptui pasibaigus, jis nėra automatiškai išjungiamas. Pakartotinai paleidus skriptą, vėl naudojamas veikiantis fono procesas.
- **Atsisakykite naudojimo su `auto_start_backend=False`** bet kuriame iš šių iškvietimų (pvz., kai nurodote nuotolinį backendą arba patys valdote backendo gyvavimo ciklą).

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

Jei komplekte esančio binarinio failorasti arba paleisti, vėlesnis „HTTP“ iškvietimas sukelia veiksmingą, **platformai pritaikytą** `ChlorosConnectError` klaidą, o ne paprastą ryšio atmetimo žinutę — „Windows“ sistemoje ji nukreipia į darbalaukio programą arba komandą `chloros-cli`; „Linux“ sistemoje (be GUI) jis nukreipia į komandą „`chloros-cli`“ arba „`.deb`“.

---

## Aplinka ir antraštės

„SDK“ kiekvieną „HTTP“ iškvietą iš užkulisinės sistemos pažymi žyma „`X-Chloros-Client: sdk`“. Užkulisinė sistema taiko „SDK“ / „CLI“ licencijavimo taisykles (reikia prisijungti **ir** turėti mokamą „Chloros+“ planą) , o ne GUI nemokamo lygio keliu. Tai nustatoma automatiškai importavimo metu — jums nereikia nieko daryti.

`http://localhost` ir `http://127.0.0.1` aptinkami kaip vietinis backend. Kreipimaisi į kitus serverius (pvz., jūsų pačių analitikos paslauga) lieka nepakitę.

Pakeiskite užkulisinį serverį „URL“, nurodydami „`backend_url=`“ (arba `api_url=` vietoj `ChlorosLocal`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(`backend_url` be kilpos pasiekia tik „source/dev“ užkurtį — pristatytieji užkurtiai pririšami tik prie kilpos; žr. „Remote-Backend Mode, kad sužinotumėte apie tunelio modelį.)

---

## Versijos ir suderinamumas

- „SDK“ versija pateikiama kaip `chloros_sdk.__version__`.
- „SDK“ pritaiko elgseną prie komplekte esančios galinio modulio versijos. Senesnio „SDK“ derinimas su naujesniu galiniu moduliu paprastai veikia (į priekį suderinami galiniai taškai), tačiau naujesnio „SDK“ derinimas su senesniu galiniu moduliu gali sukelti „`404`“ klaidas naujuose galiniuose taškuose — atnaujinkite darbalaukio programą, kad ji atitiktų.
- „Smart-connect“ sąsaja (`connect_camera` / `connect_array` / `connect_daq_sensor`) ir tinklo analizės galinis taškas grąžina stabilias „JSON“ schemas; nauji laukai yra papildomi.

---

## Trikčių šalinimo patarimai

- **`ChlorosAuthenticationError: Login required`** → Šiame kompiuteryje vieną kartą paleiskite „`chloros-cli login EMAIL PASSWORD`“ arba prisijunkite per „Chloros“ darbalaukio programą.
- **`ChlorosConnectError: No Chloros backend is running …`** → „Smart-connect“ iškvietimai automatiškai paleidžia vietinį užkulisinį procesą, todėl ši žinutė pasirodo tik tada, kai negalima rasti arba paleisti pridedamo binarinio failo (pvz.pvz., kompiuteryje, kuriame naudojamas tik „pip“, bet nėra darbalaukio paketo). Pranešimas priklauso nuo platformos: „Windows“ sistemoje atidarykite darbalaukio programą arba paleiskite bet kurią komandą „`chloros-cli`“; „Linux“ sistemoje paleiskite komandą „`chloros-cli`“ (grafinės sąsajos nėra) arba įdiekite „`.deb`“. Jei naudojate nuotolinį užkulisinį serverį, perduokite „`backend_url=`“ (ir „`auto_start_backend=False`“).
- **`CAMERA_AVAILABLE == False`** importuojant → nepavyko įkelti `lattice_sdk` (paprastai nėra įdiegtos „Arena“ SDK vykdymo DLL). Paviršius be kameros vis dar veikia.
- **„Array connect“ grąžina mažesnę nei natūralią skiriamąją gebą**→ Užkulisio „smart-prep“ funkcija automatiškai sumažina kadro dydį, kad jis tilptų į laidą. Naudokite `analyze_array_network()`, kad sužinotumėte priežastį, tada atnaujinkite ryšį, sutikite su sumažinimu arba perduokite `force_tier="slip-emit-and-capture"`, kad būtų atliekamas nuoseklusis įrašymas. Sumažinimo saugumo-tinklas**neapima** bendro perviršinio paskirstymo (`oversubscribed: true`, fps laukai 0): per didelio kamerų skaičiaus, palyginti su laidu, negalima išspręsti naudojant kadrų sujungimą (binning) arROI – sumažinkite kamerų skaičių, įjunkite „jumbo“ rėmelius arba pereikite prie greitesnio tinklo adapterio (žr. [Per didelis prenumeratų skaičius](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` praneša, kad tinklo plokštės (NIC) priėmimo žiedas yra labai mažas (~0,26 MB) / jungimo vartai rodo „FRAMES WILL DROP“** → Pagrindinio kompiuterio tinklo plokštės priėmimo žiedas yra nustatytas pagal numatytuosius parametrus (dažnai po tinklo plokštės tvarkyklės atnaujinimo nustatomas į 32). Naudojant „Realtek“ USB 10GbE adapterį, nustatykite `ReceiveBufferLen=256` ir `PendingReceives=64` (padidintus), tada perkraukite užkulisinę sistemą, kad ji iš naujo-perskaitytų žiedą. Išsami procedūra: [CLI Nuoroda → Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Pagrindinis kompiuteris užstringa perkraunant ar išjungiant, vėliau atsiranda WMI `Invalid class` klaidos / tinklo plokštė neįsijungia** → Pasenęs USB 10GbE tvarkyklė sukelia klaidą `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atnaujinkite adapterio tvarkyklę iki naujausios versijos (≥ 2026) ir iš naujo-pritaikykite priėmimo žiedo nustatymus. Žr. [„CLI“ nuorodą → Pagrindinio kompiuterio NIC nustatymas ir optimizavimas](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Atsispindėjimas atmestas** → Veikiantis DAQ turi būti susietas su kamera (arba matrica), kad būtų galima matuoti atspindžio koeficientą absoliučia skale. Susiekite per GUI arba naudokite `processing="radiance"` (W/m²/sr/nm), kuriam nereikia porinio jutiklio.
- **`smart=True` fiksavimas trunka ilgiau nei tikėtasi** → AE konvergencija priklauso nuo scenos dinamikos; jei norite greitesnio (mažiau stabilaus) suveikimo, sugriežtinkite „`exposure_tolerance_pct`“ arba sutrumpinkite „`stability_window_s`“.

---

## Taip pat žr.

- [„CLI“ nuoroda](cli-reference.md) — kiekviena „CLI“ pakomanda atitinka „SDK“ iškvietą.
- [DAQ jutiklių vadovas](../daq/README.md) — konkretiems jutikliams skirti laidų sujungimo, kalibravimo ir įrašymo reikalavimai.
- Internetinė dokumentacija: `https://mapir.gitbook.io/chloros/api-python-sdk`</id></sn>
