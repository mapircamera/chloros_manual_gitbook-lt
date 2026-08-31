# DAQ šviesos jutikliai

> **Ieškote informacijos apie įrangą?**Informacija apie pačius jutiklius – modelius, montavimą, dangtelius, jungtis, maitinimą ir programėlę „SCANNER“ – pateikta**[DAQ vartotojo vadove](https://mapir.gitbook.io/daq)**. Šiame skyriuje aprašomas jų naudojimas, pradedant nuo Chloros.

MAPIR **DAQ** šviesos jutikliai matuoja aplinkos šviesą kaip radiometriškai kalibruotus spektrus. Chloros jie atlieka dvi funkcijas:

* **Savarankiškas spektrinis prietaisas** — realaus laiko spektro diagramos, kolorimetriniai duomenys ir `.daq` įrašai, kuriuos visus galima rasti [„Šviesos jutikliai“ skirtuke](gui.md), [CLI](cli-quick-start.md) arba Python SDK.
* **Žemyn nukreipto spinduliavimo šaltinis atspindžio nustatymui** — apdorojimo metu Chloros interpoliuoja jūsų `.daq` rodmenis į kiekvieno kadroekspozicijos laiko žymę ir, remdamasis išmatuotu žemyn nukreiptu šviesos srautu, konvertuoja kameros spinduliavimą į atspindžio koeficientą (`--reflectance-source daq`); kalibruotoms juostoms nereikalingas scenos viduje esantis skydelis.

<!-- SCREENSHOT-NEEDED: product photo of the DAQ-U, DAQ-M, and DAQ-E units side by side, each with its Sunshine cosine-corrector cap fitted (request from hardware team — no repo asset exists) -->

***

## Trys modeliai, vienas duomenų formatas

| Modelis | Duomenų perdavimas | Atradimas |
| --- | --- | --- |
| **DAQ-U** | USB (serijinis) | serijinio prievado nuskaitymas |
| **DAQ-M** | „Bluetooth Low Energy“ | BLE nuskaitymas pagal pavadinimą |
| **DAQ-E** | „Ethernet“ (IPv4, maitinimas per PoE) | mDNS `_daq-e._tcp` (pavadinimas `daq-e-<id>.local`) |

Visi trys naudoja tą patį ryšio protokolą ir pateikia identiškus duomenis:

* **135 taškų spektras nuo 340 iki 1010 nm 5 nm žingsniais**, taip pat CIE XYZ tristimulių vertės kiekviename kadre.
* **Radiometriškai kalibruotas spektrinis spinduliavimo intensyvumas W/m²/nm** — prieš duomenims pasiekiant jus, taikomas kiekvieno įrenginio gamyklinis kalibravimo rinkinys (kartu su aktyviu dangtelio korekcijos profiliu).
* Tas pats **`.daq` įrašymo formatas** (SQLite failas). Tolesnis apdorojimas yra identiškas, nepriklausomai nuo to, kuris perdavimo būdas sukūrė failą.

Transporto stekai (USB serijinis, BLE, mDNS/zeroconf) yra įtraukti į „Chloros“ užkurtį — nereikia nieko įdiegti, kad galėtumėte bendrauti su bet kuriuo iš trijų modelių per grafinę vartotojo sąsają arba naudojant „CLI“ komandas „`pool-*`“.

***

## Kalibruotas diapazonas: pranešamas 340–1010 nm, kalibruotas ~374–974 nm

Jutiklis praneša apie visą 340–1010 nm tinklelį, tačiau NIST standartais atsekamas radiometrinis stiprinimas apima maždaug **374–974 nm**. Chloros atmeta absoliučiojo atspindžio dalijimą bet kuriam kameros juostos diapazonui, kurio mažiau nei pusė spektrinio svorio patenka į tą kalibruotą intervalą; praleista juosta pranešama nurodant praleidimo priežastį `dls-uncalibrated-band-<nm>`.

Iš visų tiekiamų „LATTICE“ filtrų modelių tai taikoma tik **F988**:

F988 atspindžio koeficientas kalibruojamas naudojant atspindžio plokštelę, įmontuotą į vaizdo sceną: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros taiko jūsų naujausią plokštelės užfiksuotą duomenų rinkinį ir išlaiko jį tarp plokštelės matavimų.

Jei F988 užfiksavimas apdorojamas turint tik DAQ duomenis, Chloros atmeta DAQ pagrįstą atspindžio koeficientą tam dažnių juostos intervalui, nurodydama praleidimo priežastį `dls-uncalibrated-band-988` — [atspindžio plokštės darbo eiga](../calibration-targets.md) yra F988 palaikomas būdas.

***

## Jutiklių ID

Kiekvienas DAQ praneša stabilų jutiklio ID. Jo forma skiriasi priklausomai nuo modelio:

| Modelis | ID forma | Pavyzdys |
| --- | --- | --- |
| DAQ-U | 5 oktetų su brūkšneliais | `CB-7C-A8-2E-5F` |
| DAQ-M | 5 oktetų su brūkšneliais | `CB-74-02-30-6B` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

Jutiklio identifikatorius yra:

* įrašytas į kiekvieną `.daq` failą, kurį jis įrašo,
* raktas, kurį Chloros naudoja, kad gautų to įrenginio gamyklinį kalibravimo paketą,
* vertė, kurią perduodate `--sensor-id` komandose CLI ir `pool-*`, ir
* DAQ-E atveju – taip pat jo mDNS hosto vardą (`daq-e-def330.local`) – vertę, kurią priima `--eth-host`.

***

## Gamyklinis kalibravimas ir debesija

Kiekvienas DAQ įrenginys yra individualiai kalibruojamas gamykloje naudojant NIST atsekamą radiometrinę grandinę, o Chloros įkelia kiekvieno įrenginio kalibravimo paketą, susietą su jo jutiklio ID. Kiekvieno įrenginio kalibravimo ataskaitą (PDF) galima atsisiųsti iš jutiklio nustatymų [skirtuke „Šviesos jutikliai“](gui.md).

{% hint style="warning" %}
**„DAQ-U“ ir „DAQ-M“ kalibravimui reikalinga prieiga prie debesies.**Nei vienas iš šių modelių nieko nesaugo savo atmintyje: jų gamykliniai kalibravimo rinkiniai saugomi „MAPIR“ debesyje ir gaunami pagal jutiklio ID (vėliau išsaugomi vietinėje talpykloje). Chloros reikia interneto ryšio, kad galėtų perduoti kalibruotus W/m²/nm duomenis iš DAQ-U arba DAQ-M.**Išimtis yra DAQ-E** — jis kalibravimą saugo pačiame įrenginyje.

<!-- PRE-PUBLISH-CHECK: LAUNCH item 3 (DAQ-M end-to-end connect smoke) was still unverified as of 2026-08-16 — re-confirm the DAQ-M cloud-calibration flow on the release build before publishing this page. -->

{% endhint %}***

## Kur saugomi įrašai

| Paviršius | Numatytasis `.daq` saugojimo vieta |
| --- | --- |
| Vartotojo sąsaja — skirtukas „Šviesos jutikliai“ | `<project folder>/light_sensor/` (baigti įrašai automatiškai pridedami prie projekto) |
| CLI — `daq pool-record` | `~/Documents/DAQ Live View/` kompiuteryje, kuriame veikia serveris |

Kiekvieno `.daq` failo pavadinime yra nurodytas jutiklio ID ir laiko žyma.

***

## Šiame skyriuje

* [**Skirtukas „DAQ“ Chloros**](gui.md) — išsamus GUI apžvalga: kiekvieno modelio prijungimas, nustatymai kiekvienam jutikliui, spektro diagramos, kolorimetriniai duomenys realiuoju laiku, dviejų jutiklių atspindžio koeficientas ir įrašymas.
* [**CLI „Greitasis pradžios vadovas“ (pool-\*)**](cli-quick-start.md) — DAQ jutiklių valdymas iš `chloros-cli daq pool-*`, palaikomas komandinės eilutės kelias.
* [**Ribų profiliai ir kalibruotas diapazonas**](caps-and-range.md) — kokios ribos yra kiekvienam modeliui, kaip jas deklaruoti ir išsamiai aprašytas kalibruotas spektrinis diapazonas.
* [**Įrašymas ir .daq formatas**](recording.md) — `.daq` SQLite formatas ir įrašymo darbo eigos.
* [**DAQ-E tinklas ir laiko sinchronizavimas**](ethernet-ptp.md) — DAQ-E perdavimo režimai ir PTP laiko sinchronizavimas.
* [**Atspindžio darbo srautai**](reflectance.md) — DAQ žemyn nukreiptų spindulių duomenų naudojimas atspindžio apskaičiavimui.
* Išsamią dokumentaciją apie žymių lygį rasite [CLI nuorodoje](../reference/cli-reference.md) (skyriuje `chloros-cli daq`) ir [SDK nuorodoje](../reference/sdk-reference.md) (`chloros_sdk.connect_daq_sensor()`), kurios abi parengtos taip, kad jas galėtų tiesiogiai naudoti AI asistentai.
