# DAQ šviesos jutikliai

> **Ieškote informacijos apie įrangą?**Patys jutikliai – modeliai, montavimas, dangteliai, jungtys, maitinimas ir „SCANNER“ programa – aprašyti**[DAQ vartotojo vadove](https://mapir.gitbook.io/daq)**. Šiame skyriuje aptariamas jų naudojimas programoje Chloros. „

MAPIR“ **DAQ** šviesos jutikliai matuoja aplinkos šviesą kaip radiometriškai kalibruotus spektrus. Programoje Chloros jie atlieka dvi funkcijas:

* **Savarankiškas spektrinis prietaisas** — realaus laiko spektro diagramos, kolorimetriniai duomenys ir `.daq` įrašai, kuriuos galima gauti iš [skirtuke „Šviesos jutikliai“](gui.md), [CLI](cli-quick-start.md) arba Python SDK.
* **Žemyn nukreipto spinduliavimo šaltinis atspindžio apskaičiavimui** — apdorojimo metu Chloros interpoliuoja jūsų `.daq` rodmenis pagal kiekvieno kadro ekspozicijos laiko žymą ir naudoja išmatuotą žemyn nukreiptą šviesą, kad konvertuotų kameros spinduliavimą į atspindžio koeficientą (`--reflectance-source daq`); kalibruotoms juostoms nereikalingas vaizdo kadre esantis panelis.

<!-- SCREENSHOT-NEEDED: product photo of the DAQ-U, DAQ-M, and DAQ-E units side by side, each with its Sunshine cosine-corrector cap fitted (request from hardware team — no repo asset exists) -->

***

## Trys modeliai, vienas duomenų formatas

| Modelis | Duomenų perdavimas | Atradimas |
| --- | --- | --- |
| **DAQ-U** | USB (serijinis) | nuskaitymas per serijinį prievadą |
| **DAQ-M** | „Bluetooth Low Energy“ | BLE nuskaitymas pagal pavadinimą |
| **DAQ-E** | „Ethernet“ (IPv4, maitinimas per PoE) | mDNS `_daq-e._tcp` (pagrindinio kompiuterio vardas `daq-e-<id>.local`) |**DAQ-E-S (nauja versija 1.3.0).** DAQ-E su nuolatiniu difuzoriumi ir pakreipimo jutikliu. Chloros jį nurodo kaip `DAQ-E-S` ir valdo lygiai taip pat kaip DAQ-E, išskyrus tai, kad nereikia atlikti viršutinės ribos korekcijos, o kiekvienas rodmuo atspindi prietaiso pakreipimą, pasvirimą ir polinkį. Žr. [DAQ-E-S: fiksuotas difuzorius ir pasvirimas](caps-and-range.md#daq-e-s-fixed-diffuser-and-tilt).

Visi trys prietaisai naudoja tą patį duomenų perdavimo protokolą ir pateikia identiškus duomenis:

* **135 taškų spektras nuo 340 iki 1010 nm 5 nm žingsniais**, taip pat CIE XYZ tristimulių vertės kiekviename kadre.
* **Radiometriškai kalibruotas spektrinis spinduliavimo intensyvumas W/m²/nm** — prieš duomenims pasiekiant jus, taikomas kiekvieno įrenginio gamyklinis kalibravimo rinkinys (kartu su aktyviu dangtelio korekcijos profiliu).
* Tas pats **`.daq` įrašymo formatas** (SQLite failas). Tolimesnis apdorojimas yra identiškas, nepriklausomai nuo to, kuris perdavimo būdas sukūrė failą.

Perdavimo protokolai (USB serijinis, BLE, mDNS/zeroconf) yra integruoti į Chloros užkurtį — nereikia nieko įdiegti, kad galėtumėte bendrauti su bet kuriuo iš trijų modelių per GUI arba CLI komandas „`pool-*`“.

***

## Kalibruotas diapazonas: pranešamas 340–1010 nm, kalibruotas ~374–974 nm

Jutiklis praneša apie visą 340–1010 nm tinklelį, tačiau NIST standartais atsekamas radiometrinis stiprinimas apima maždaug **374–974 nm**. Chloros atmeta absoliučiojo atspindžio padalijimą bet kuriam kameros diapazonui, kurio mažiau nei pusė spektrinio svorio patenka į tą kalibruotą intervalą; praleistas diapazonas nurodomas su praleidimo priežastimi `dls-uncalibrated-band-<nm>`.

Iš visų tiekiamų „LATTICE“ filtrų modelių tai taikoma tik **F988**:

F988 atspindžio koeficientas kalibruojamas naudojant atspindžio plokštelę, esančią vaizdo kadre: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros taiko jūsų naujausią plokštelės užfiksuotą vertę ir ją išlaiko tarp plokštelės matavimų.

Jei „F988“ užfiksavimas apdorojamas turint tik DAQ duomenis, Chloros atmeta DAQ pagrįstą atspindžio koeficientą tam diapazonui, nurodydamas praleidimo priežastį `dls-uncalibrated-band-988` — [atspindžio plokštės darbo eiga](../calibration-targets.md) yra palaikomas būdas, skirtas F988.

***

## Jutiklių ID

Kiekvienas DAQ praneša stabilų jutiklio ID. Jo forma skiriasi priklausomai nuo modelio:

| Modelis | ID forma | Pavyzdys |
| --- | --- | --- |
| DAQ-U | 5 oktetų su brūkšneliais | `CB-7C-A8-2E-5F` |
| DAQ-M | 5 oktetų su brūkšneliais | `CB-74-02-30-6B` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

Jutiklio identifikatorius yra:

* įrašytas į kiekvieną jo įrašomą `.daq` failą,
* raktas, kurį Chloros naudoja, kad gautų to įrenginio gamyklinį kalibravimo paketą,
* vertė, kurią perduodate „`--sensor-id`“ per CLI komandas „`pool-*`“, ir
* „DAQ-E“ atveju – taip pat jo mDNS hosto vardas (`daq-e-def330.local`) – vertė, kurią priima „`--eth-host`“.

***

## Gamyklinis kalibravimas ir debesija

Kiekvienas DAQ įrenginys yra atskirai kalibruojamas gamykloje naudojant NIST atsekamą radiometrinę grandinę, o Chloros įkelia kiekvieno įrenginio kalibravimo paketą, susietą su jo jutiklio ID. Kiekvieno įrenginio kalibravimo ataskaitą (PDF) galima atsisiųsti iš jutiklio nustatymų [skirtuke „Šviesos jutikliai“](gui.md).

{% hint style="warning" %}
**„DAQ-U“ ir „DAQ-M“ kalibravimui reikalinga prieiga prie debesies.**Nei vienas iš šių modelių nieko nesaugo savo atmintyje: jų gamykliniai kalibravimo rinkiniai saugomi MAPIR debesyje ir gaunami pagal jutiklio ID (vėliau išsaugomi vietinėje talpykloje). Chloros reikia interneto ryšio, kad galėtų perduoti kalibruotus W/m²/nm duomenis iš „DAQ-U“ arba „DAQ-M“.**„DAQ-E“ yra išimtis** — jis kalibravimo duomenis saugo pačiame įrenginyje.
{% endhint %}
<!-- PRE-PUBLISH-CHECK: LAUNCH item 3 (DAQ-M end-to-end connect smoke) was still unverified as of 2026-08-16 — re-confirm the DAQ-M cloud-calibration flow on the release build before publishing this page. -->

***

## Kur saugomi įrašai

| Paviršius | Numatytasis „`.daq`“ saugojimo vieta |
| --- | --- |
| Vartotojo sąsaja — skirtukas „Šviesos jutikliai“ | „`<project folder>/light_sensor/`“ (baigti įrašai automatiškai pridedami prie projekto) |
| CLI — `daq pool-record` | `~/Documents/DAQ Live View/` kompiuteryje, kuriame veikia užkulisinė programa (santykinis `-o` nustatomas pagal aplanką, kuriame paleidote komandą, kai užkulisinė programa veikia tame pačiame kompiuteryje) |

Kiekvienas `.daq` failo pavadinimas apima jutiklio ID ir laiko žymą.

***

## Šiame skyriuje

* [**Chloros skirtukas „DAQ“**](gui.md) — išsamus GUI apžvalga: kiekvieno modelio prijungimas, nustatymai kiekvienam jutikliui, spektro diagramos, spalvų matavimo duomenys realiuoju laiku, dviejų jutiklių atspindžio matavimai ir įrašymas.
* [**CLI greitasis pradžios vadovas (pool-\*)**](cli-quick-start.md) — DAQ jutiklių valdymas iš „`chloros-cli daq pool-*`“, palaikomas komandinės eilutės kelias.
* [**Ribų profiliai ir kalibruotas diapazonas**](caps-and-range.md) — kokios ribos yra kiekvienam modeliui, kaip jas deklaruoti ir išsamus aprašymas apie kalibruotą spektrinį diapazoną.
* [**Įrašymas ir .daq formatas**](recording.md) — `.daq` SQLite formatas ir įrašymo darbo eigos.
* [**DAQ-E tinklas ir laiko sinchronizavimas**](ethernet-ptp.md) — DAQ-E perdavimo režimai ir PTP laiko sinchronizavimas.
* [**Atspindžio darbo eigos**](reflectance.md) — DAQ žemyn nukreiptų duomenų naudojimas atspindžio apskaičiavimui.
* Išsamią dokumentaciją apie parametrų žymes rasite [CLI žinyne](../reference/cli-reference.md) (skyriuje `chloros-cli daq`) ir [SDK žinyne](../reference/sdk-reference.md) (`chloros_sdk.connect_daq_sensor()`), kurios abi parengtos taip, kad jas galėtų tiesiogiai naudoti AI asistentai.
