# Įrašymas ir .daq formatas

`.daq` failas yra „Chloros “ šviesos jutiklio įrašymo formatas: **SQLite duomenų bazė**, kurioje saugomi kalibruoti spektriniai kadrai, gauti iš vieno DAQ jutiklio. Įrašykite vieną failą per fiksavimo sesiją, ir atspindžio apdorojimo grandinė vėliau galės kiekvieną vaizdą padalyti iš tuo pačiu metu išmatuoto žemyn nukreipto spinduliavimo intensyvumo.

## Kas yra .daq faile

| Savybė | Vertė |
| --- | --- |
| Konteineris | „SQLite“ duomenų bazė, po vieną failą kiekvienam jutikliui ir kiekvienam įrašui |
| Failo pavadinimas | Apima **jutiklio ID**ir**laiko žymą**, pvz., `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Spektras viename kadre | 135 taškai, 340–1010 nm 5 nm žingsniais, taip pat CIE XYZ tristimuli |
| Vienetai | Kalibruotas spektrinis spinduliavimo intensyvumas, **W/m²/nm** (taikomas gamyklinis kalibravimo rinkinys + dangtelio profilis) |
| Įrašyti metaduomenys | Jutiklio ID (raktas, leidžiantis gauti to įrenginio gamyklinį kalibravimą) ir galiojantis dangtelio profilis — žr. [Dangtelio profiliai ir kalibruotas diapazonas](caps-and-range.md) |

Formatas yra identiškas DAQ-U, DAQ-M, DAQ-E ir DAQ-E-S įrenginiams, todėl tolesniam apdorojimui nesvarbu, kuris įrenginys jį įrašė. DAQ-E-S įraše šalia kiekvieno spektro taip pat saugomi įrenginio pasvirimo, posvyrio ir pakreipimo kampai.

**Įrašai išlieka net ir nutraukus maitinimą (1.3.0).**Tiesioginis įrašas rašomas naudojant žurnalą ir atliekant visišką disko sinchronizavimą po kiekvieno įrašymo, todėl maitinimo nutraukimas ar proceso nutraukimas praranda tik paskutines 3 sekundes, o ne visą failą. Įrašas, sustabdytas rašymo viduryje, gali palikti failą „`<name>.daq-journal`“ šalia failo „`.daq`“:**išsaugokite ir nukopijuokite juos abu kartu.** Chloros atkuria žurnalą pirmą kartą, kai nuskaito failą. Užimtas diskas vėluoja failo įrašymą, bet niekada – jutiklio rodmenų perdavimą.

**„DAQ-E“, kuris nustoja perduoti duomenis, yra vėl prijungiamas (1.3.0).** Kai DAQ-E duomenų srautas nutyla (buvo išjungtas ir vėl įjungtas maitinimo jungiklis arba nutrūko ryšys), serveris jį vėl prijungia su ankstesniais nustatymais, o įrašymas tęsiamas tame pačiame „`.daq`“ faile, kuriame pažymėta pertrauka.

Kalibruotam įrašymui reikalingas gamintojo pateiktas jutiklio kalibravimo rinkinys. DAQ-U ir DAQ-M atveju serveris atsisiunčia rinkinį iš „MAPIR “ debesies pagal jutiklio ID (jei to padaryti nepavyksta, įrašymas atmetamas); DAQ-E įrenginiai yra išimtis, nes jie saugo kalibravimo duomenis pačiame įrenginyje.

## Įrašymas per GUI

Įrašymui per GUI reikia **atidaryto projekto** (kitu atveju įrašymo mygtukai yra išjungti):

* **Įrašyti viską / Sustabdyti viską** — viršuje, šviesos jutiklių šoninėje juostoje; vienu metu pradeda arba sustabdo `.daq` įrašymą visuose prijungtuose jutikliuose.
* **Įrašyti / Sustabdyti įrašymą** — kiekvienam jutikliui atskirai, nustatymų lange su ratuko piktograma. Įrašymo metu jutiklio realaus laiko informacijos eilutėse rodomas raudonas „REC“ indikatorius.

Failai įrašomi į „`<project>/light_sensor/`“, o kai įrašymas sustabdomas — paspaudus „Sustabdyti“, „Sustabdyti viską“ arba atjungus įrašantį jutiklį — užbaigtas „`.daq`“ failas **automatiškai pridedamas prie atidaryto projekto**. Jis atsiranda projekto failų sąraše be jokio rankinio pridėjimo veiksmo, jau paruoštas atspindžio apdorojimui.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->



## Įrašymas iš CLI



CLI įrašo per užkulisio jutiklių grupę (užkulisys turi veikti — šios komandos yra paprasti HTTP klientai):

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

Gauti `--sensor-id` reikšmę iš `chloros-cli daq pool-list`. Dvi verta žinoti numatytosios reikšmės:

| Parinktis | Numatytasis |
| --- | --- |
| `--duration` | `0` — įrašyti iki `pool-record --stop` |
| `--output` / `-o` | `~/Documents/DAQ Live View/` **backend'o** failų sistemoje, o ne CLI |

Išvesties katalogo skirtumas svarbus, kai CLI-as nukreipia į backend'ą kitame kompiuteryje: failas patenka ten, kur veikia backend'as. Jei backend yra tame pačiame kompiuteryje, santykinis `-o` (pavyzdžiui, aukščiau nurodytas `./out`) nustatomas pagal aplanką, kuriame buvo paleista komanda.

## Įrašymas iš Python

`DAQSensorSession` (grąžinamas `chloros_sdk.connect_daq_sensor()`) pateikia tą patį sujungtą įrašą: `record_start(output_dir=None, device_name=None)` grąžina failo kelią, o `record_stop()` grąžina `{path, rows}`. Visą sesijos API aprašymą rasite [SDK žinyne](../reference/sdk-reference.md). SDK tiesioginio sąsajos su aparatine įranga klasės (tik stalinėms versijoms) įrašus pagal numatytuosius nustatymus rašo į „`~/Documents/DAQ/`“; išleistose versijose palaikomas aukščiau nurodytas sujungtas kelias.

## .daq failo naudojimas apdorojimo metu

Norint iš vaizdų apskaičiuoti atspindžio koeficientą, Chloros reikia kiekvienai ekspozicijai pritaikyto žemyn nukreipto spinduliavimo intensyvumo:

* **Laikykite `.daq` kartu su vaizdais.**Apdorojimo metu apdorojimo grandinė automatiškai nustato**laiko žymos atitikmenį turinčią žemyn nukreiptą spinduliuotę** iš įrašyto `.daq` failo (bet kurio DAQ modelio) — arba iš DAQ-M natūralaus `.csv` — esančio šalia vaizdų. GUI įrašai šį reikalavimą atitinka automatiškai, nes jie į projektą įtraukiami iškart po įrašymo pabaigos.
* **Kalibravimas gaunamas pagal poreikį.** Jei kameros ar DAQ gamyklinio kalibravimo rinkinys dar nėra išsaugotas vietinėje talpykloje, Chloros jį automatiškai atsisiunčia iš „MAPIR “ debesies pirmojo naudojimo metu (reikia vienkartinio prisijungimo prie interneto; išsaugoma kaip `~/.chloros/`).
* **Tiesioginiai įrašai sukuria savo „sidecar“ failą.** Kiekvienam tiesiogiai užfiksuotam atspindžio kadrui faktiškai panaudotas DAQ rodmuo išsaugomas kaip `.daq` failas įrašų `daq/` aplanke, todėl įrašą vėliau galima apdoroti iš naujo be originalaus įrašo.
* **Įrašai iš „`chloros_scripts`“ įrašymo įrenginių (1.3.0).**Viešai prieinamų [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) įrašymo įrenginių sukurtas „`.daq`“ failas nurodomas kaip jį sukūrusio DAQ modelio pavadinimas, o jo ribą galima nustatyti**Projekto nustatymai → DAQ šviesos jutiklis**, kaip ir bet kurio kito įrašo. Šie įrašymo įrenginiai įrašo neapdorotus jutiklio skaičiavimo duomenis; Chloros pagal serijinį numerį gauna gamyklinius jutiklio kalibravimo parametrus ir juos taiko.
* **Kam priklauso šis rodmuo (1.3.0).**Šalia kiekvieno įrašo `.daq` nurodoma `.daq.cameras.json`, nurodanti kamerą (-as), kurios kadrus apibūdina šis rodmuo, o apdorojimas jį susieja**tik su tų kamerų vaizdais**. Todėl atskira kamera, neturinti savo šviesos jutiklio, nebeperima kitos kameros rodmenų – jai reikia priskirti jutiklį arba įrašyti savo. Įrašas, padarytas pačiu DAQ (GUI „Record“, `daq pool-record`), ir bet kuris `.daq` be to failo (įskaitant senesnius įrašus), vis dar tinka kiekvienai kamerai.

## Šviesos intensyvumo išgavimas

Apdorojant projektą taip pat eksportuojami visi jame esantys šviesos jutiklio įrašai į
`Light Sensor/` aplanką šalia vaizdo produktų. Tam **nereikia** vaizdų:
atskirai įrašytas šviesos jutiklis yra pilnas įrašas, o aplankas, kuriame yra tik `.daq`
failai, yra tinkamas įvesties šaltinis. Projekto, kuriame yra tik šviesos jutiklių įrašai, sąrašas ir
apdorojimas vyksta tiek programėlėje, tiek CLI, o jie lieka sąraše ir po apdorojimo. Apdorojimo ataskaitoje
nurodoma, kiek šviesos jutiklių produktų buvo įrašyta.

| Produktas | Kas tai yra |
| --- | --- |
| `<name>_calibrated.daq` | Pakartotinai apdorojamas archyvas, kurio schema atitinka tiesioginio įrašo schemą, dabar nurodantis kalibravimo paketą, kurio pagrindu jis buvo sukurtas. Pakartotinai importavus jį, jis **nėra** kalibruojamas antrą kartą. |
| `<name>_calibrated.csv` | Spektrinis spinduliavimo intensyvumas W/m²/nm, pateiktas pagal paties jutiklio bangų ilgių tinklelį, po vieną eilutę kiekvienam matavimui, taip pat fotometrinės stulpeliai: bendra galia, fotopinis ir skotopinis liuksas, PPFD su mėlynos/žalios/raudonos spalvų pasiskirstymu ir didžiausias bangos ilgis. |

DAQ-U arba DAQ-M, kurio kalibravimo rinkinio negalima atsisiųsti — esate neprisijungę prie interneto arba
to jutiklio kalibravimo duomenų nėra failuose — yra **praleidžiamas nurodant priežastį**, niekada neišrašomas
kaip „kalibruotas“ failas, kuriame saugomi neapdoroti skaičiai. Prisijunkite prie interneto ir paleiskite iš naujo. DAQ-E
turi savo kalibravimą, todėl jo reikia tik tada, kai įrenginys nėra prijungtas ir
vietiniame atminties talpykloje nieko nėra.

### DAQ-A: neapdoroti skaičiai ir kodėl tai yra teisingas sprendimas

**DAQ-A** buvo sukurtas anksčiau nei serijinių kalibravimo rinkinių sistema, todėl neturi rinkinio, kurį
reikėtų atsisiųsti. Tai nėra aplaidumas: „DAQ-A“ kalibruojamas lauke pagal
atspindžio taikinį, o taikinio pagrindu atliekamam kalibravimui reikalingas tik jutiklio *santykinis*
atsakas — o tai yra būtent neapdoroti skaičiai. Chloros juos naudoja kalibravimui iki šiol.

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
palyginamos **tame pačiame** faile, o ne tarp skirtingų jutiklių. Stulpeliai, kurie turi reikšmę
tik tikrajam spinduliavimo intensyvumui — bendra galia, liuksai, PPFD — paliekami tušti, o ne
apskaičiuojami pagal skaičiavimus.

Senesniuose DAQ-A-SD įrašuose (schema v1.01 / v1.02) įrašomas tik failo įrašymo laikas, o ne
kiekvieno matavimo laiko žyma. Chloros nesuderins vaizdų su jais — kadro susiejimas su
įrašymo laiku būtų neteisingas, nors iš pirmo žvilgsnio atrodytų teisingai — tačiau eksportuojant jie nuskaitomi tinkamai, o
CSV faile nurodoma, kuriam laikrodžiui jie priskiriami.

Išsamią informaciją apie atspindžio matavimus — vieno jutiklio su kamera ir dviejų jutiklių (aplinkos/objekto) — rasite [Atspindžio matavimo darbo eigose](reflectance.md).
