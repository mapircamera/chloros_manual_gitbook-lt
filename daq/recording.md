# Įrašymas ir .daq formatas

`.daq` failas yra „Chloros“ šviesos jutiklio įrašymo formatas: kalibruotų spektrinių kadrų **SQLite duomenų bazė**, gauta iš vieno DAQ jutiklio. Įrašykite vieną per fiksavimo sesiją, ir atspindžio apdorojimo grandinė vėliau galės kiekvieną vaizdą padalyti iš tuo pačiu metu išmatuoto žemyn nukreipto spinduliavimo intensyvumo.

## Kas yra .daq faile

| Savybė | Vertė |
| --- | --- |
| Konteineris | „SQLite“ duomenų bazė, po vieną failą kiekvienam jutikliui ir kiekvienam įrašui |
| Failo pavadinimas | Apima **jutiklio ID**ir**laiko žymą**, pvz., `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Spektras vienam kadrui | 135 taškai, 340–1010 nm 5 nm žingsniais, taip pat CIE XYZ tristimuli |
| Vienetai | Kalibruotas spektrinis spinduliavimo intensyvumas, **W/m²/nm** (taikomas gamyklinis kalibravimo rinkinys + dangtelio profilis) |
| Įrašyti metaduomenys | Jutiklio ID (raktas, skirtas gauti to įrenginio gamyklinį kalibravimą) ir galiojantis dangtelio profilis — žr. [Dangtelio profiliai ir kalibruotas diapazonas](caps-and-range.md) |

Formatas yra identiškas DAQ-U, DAQ-M ir DAQ-E įrenginiams, todėl tolesniam apdorojimui nesvarbu, kuris įrenginys jį įrašė.

Kalibruotam įrašymui reikalingas jutiklio gamyklinis kalibravimo rinkinys. DAQ-U ir DAQ-M atveju serveris atsisiunčia paketą iš MAPIR debesies pagal jutiklio ID (jei to padaryti nepavyksta, įrašymas atmetamas); DAQ-E įrenginiai yra išimtis, nes jie kalibravimo duomenis saugo pačiame įrenginyje.

## Įrašymas per GUI

Įrašymui per GUI reikia **atidaryto projekto** (kitu atveju įrašymo mygtukai yra išjungti):

* **Įrašyti viską / Sustabdyti viską** — viršuje, šviesos jutiklių šoninėje juostoje; vienu metu paleidžia arba sustabdo `.daq` įrašymą visuose prijungtuose jutikliuose.
* **Įrašyti / Sustabdyti įrašymą** — kiekvienam jutikliui atskirai, nustatymų lange su ratuko piktogramos. Įrašymo metu jutiklio realaus laiko informacijos eilutėse rodomas raudonas „REC“ indikatorius.

Failai įrašomi į „`<project>/light_sensor/`“, o kai įrašymas sustabdomas — paspaudus „Sustabdyti“, „Viską sustabdyti“ arba atjungus įrašantį jutiklį — užbaigtas „`.daq`“ failas **automatiškai pridedamas prie atidaryto projekto**. Jis atsiranda projekto failų sąraše be jokio rankinio pridėjimo veiksmo, jau paruoštas atspindžio apdorojimui.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->

## Įrašymas iš „CLI“

CLI įrašo per užkulisinės programos jutiklių grupę (užkulisinė programa turi veikti — šios komandos yra lengvi HTTP klientai):

```bash
# Connect the sensor into the backend pool
chloros-cli daq pool-connect --eth-host daq-e-def330.local

# Record for 150 seconds, with a human-friendly device label
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
    -o ./out --device-name "rooftop-A"

# Or run open-ended and stop explicitly
chloros-cli daq pool-record --sensor-id daq-e-def330            # --duration defaults to 0 = run until --stop
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

Gauti `--sensor-id` vertę iš `chloros-cli daq pool-list`. Dvi verta žinoti numatytosios reikšmės:

| Parinktis | Numatytasis |
| --- | --- |
| `--duration` | `0` — įrašyti iki `pool-record --stop` |
| `--output` / `-o` | `~/Documents/DAQ Live View/` **galinio serverio** failų sistemoje, o ne CLI |

Išvesties katalogo skirtumas svarbus, kai CLI nukreipia į backendą kitame kompiuteryje: failas patenka ten, kur veikia backendas.

## Įrašymas iš Python

`DAQSensorSession` (grąžinamas `chloros_sdk.connect_daq_sensor()`) pateikia tą patį sujungtą įrašą: `record_start(output_dir=None, device_name=None)` grąžina failo kelią, o `record_stop()` grąžina `{path, rows}`. Visą sesiją API rasite [SDK žinyne](../reference/sdk-reference.md). SDK tiesioginės aparatinės įrangos klasės (tik įdiegus kompiuteryje) įrašus pagal numatytuosius nustatymus rašo į `~/Documents/DAQ/`; išleistose versijose palaikomas aukščiau nurodytas bendras kelias.

## .daq failo naudojimas apdorojimo metu

Norint apskaičiuoti atspindį iš vaizdų, Chloros reikia, kad kiekvienai ekspozicijai būtų priskirtas atitinkamas žemyn nukreiptas spinduliavimo intensyvumas:

* **Laikykite `.daq` kartu su vaizdais.**Apdorojimo metu apdorojimo grandinė automatiškai nustato**laiko žymos atitinkantį spinduliavimą žemyn** iš įrašyto `.daq` failo (bet kurio DAQ modelio) — arba iš DAQ-M natūralaus `.csv` —, esančio kartu su vaizdais. GUI įrašai šį reikalavimą atitinka automatiškai, nes jie įtraukiami į projektą iš karto po to, kai baigiasi jų įrašymas.
* **Kalibravimas atsisiunčiamas pagal poreikį.** Jei kameros ar DAQ gamyklinio kalibravimo rinkinys dar nėra išsaugotas vietinėje talpykloje, Chloros jį automatiškai atsisiunčia iš MAPIR debesies pirmojo naudojimo metu (reikia vienkartinio prisijungimo prie interneto; išsaugoma `~/.chloros/`).
* **Tiesioginiai įrašai sukuria savo „sidecar“ failą.** Kiekvienam tiesiogiai užfiksuotam atspindžio kadrui faktiškai panaudotas DAQ rodmuo išsaugomas kaip „`.daq`“ „sidecar“ failas šalia vaizdo medžiagos, todėl įrašą vėliau galima apdoroti iš naujo be originalaus įrašo.

## Spinduliavimo intensyvumo išgavimas

Apdorojant projektą taip pat eksportuojami visi jame esantys šviesos jutiklio įrašai į
`Light Sensor/` aplanką šalia vaizdų produktų. Tam **nereikia** vaizdų:
atskirai skraidęs šviesos jutiklis yra pilnas įrašas, o aplankas, kuriame yra tik `.daq`
failai, yra tinkamas įvesties šaltinis. Vykdymo ataskaitoje nurodoma, kiek šviesos jutiklio produktų buvo įrašyta.

| Produktas | Kas tai yra |
| --- | --- |
| `<name>_calibrated.daq` | Pakartotinai apdorojamas archyvas, kurio schema atitinka tiesioginio įrašo schemą, dabar nurodantis kalibravimo paketą, kurio pagrindu jis buvo sukurtas. Pakartotinai importuojant jį, jis **nėra** kalibruojamas antrą kartą. |
| `<name>_calibrated.csv` | Spektrinė spinduliuotės intensyvumas W/m²/nm, pateiktas pagal paties jutiklio bangų ilgio tinklelį, po vieną eilutę kiekvienam matavimui, taip pat fotometrinės stulpeliai: bendra galia, fotopinis ir skotopinis liuksas, PPFD su mėlynos/žalios/raudonos spalvų pasiskirstymu ir didžiausias bangos ilgis. |

DAQ-U arba DAQ-M, kurio kalibravimo rinkinio negalima atsisiųsti — esate neprisijungę prie interneto arba
to jutiklio kalibravimo duomenų nėra failuose — yra **praleidžiamas nurodant priežastį**, niekada neišrašomas
kaip „kalibruotas“ failas, kuriame saugomi neapdoroti skaičiavimo duomenys. Prisijunkite prie interneto ir paleiskite iš naujo. DAQ-E
turi savo kalibravimą, todėl jo reikia tik tada, kai įrenginys nėra prijungtas ir
vietiniame kešyje nieko nėra.

### DAQ-A: neapdoroti skaičiai ir kodėl tai yra teisingas sprendimas

**DAQ-A** buvo sukurtas anksčiau nei serijinių kalibravimo rinkinių sistema, todėl neturi rinkinio, kurį
reikėtų atsisiųsti. Tai nėra aplaidumas: DAQ-A kalibruojamas lauke pagal
atspindžio taikinį, o kalibravimui pagal taikinį reikalingas tik jutiklio *santykinis*
atsakas — o tai yra būtent neapdoroti skaičiai. Chloros šiandien kalibruojasi naudodamas juos.

Taigi DAQ-A įrašas eksportuojamas, bet su kitu pavadinimu:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq
    └── <name>_raw.csv
```

`_raw`, o ne `_calibrated` — tai kitoks failo pavadinimas, o ne žymė pačiame faile,
nes šis teiginys turi išlikti, kai failas siunčiamas el. paštu kaip paprastas pavadinimas. `.csv`
antraštėje nurodyta `raw spectral sensor counts (NOT irradiance)` ir įspėjama, kad reikšmės yra
palyginamos **tame pačiame** faile, o ne tarp skirtingų jutiklių. Stulpeliai, kurie turi reikšmę tik
tikrajam spinduliavimo intensyvumui — bendra galia, liuksai, PPFD — paliekami tušti, o ne
apskaičiuojami pagal skaičiavimus.

Senesniuose DAQ-A-SD įrašuose (schema v1.01 / v1.02) įrašomas tik failo įrašymo laikas, o ne
kiekvieno matavimo laiko žyma. Chloros nesuderins vaizdų su jais — kadro susiejimas su
įrašymo laiku būtų neteisingas, nors iš pirmo žvilgsnio atrodytų teisingai — tačiau eksportas juos nuskaito be problemų, o
CSV nurodo, pagal kurį laikrodį jis veikia.

Išsamią informaciją apie atspindžio matavimus – vieno jutiklio su kamera ir dviejų jutiklių (aplinkos/objekto) – rasite [Atspindžio matavimo darbo eigoje](reflectance.md).
