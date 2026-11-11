# Jutiklių korpusų profiliai ir kalibruotas matavimo diapazonas

> Patys dangteliai — kuris dangtelis tiekiamas su kuriuo jutikliu, kaip jie montuojami ir kokios yra jų optinės savybės — aprašyti **[DAQ vartotojo vadove](https://mapir.gitbook.io/daq)**. Šiame puslapyje aptariama, kaip *nurodyti* Chloros
sumontuotą dangtelį, nes būtent tai užtikrina teisingą korekciją.

Kiekvieno DAQ šviesos jutiklio gamyklinis radiometrinis kalibravimas apibūdina *grynąjį* jutiklį. Fizinis dangtelis, uždėtas ant difuzoriaus, keičia tai, kokią šviesą surenka jutiklis, todėl Chloros
kalibravimo paketui taiko gamykliniu būdu išmatuotą **dangtelio korekcijos profilį**. Teisingo dangtelio deklaravimas yra dalis kalibruotų duomenų gavimo proceso — šiame puslapyje aprašoma, kokie dangteliai yra kiekvienam modeliui, kaip juos deklaruoti ir koks iš tikrųjų yra jutiklio kalibruotas spektrinis diapazonas.

## Dangtelių prieinamumas pagal modelį

| Dangtelio profilis (`cap_id`) | Fizinis dangtelis | DAQ-U | DAQ-M | DAQ-E |
| --- | --- | --- | --- | --- |
| `sunshine_cosine` | „Sunshine“ kosinuso korektoriaus dangtelis (**numatytasis kiekvienam modeliui**) | Taip | Taip | Taip |
| `fov_15` / `fov_45` / `fov_90` | FOV ribojantys kūgiai (15° / 45° / 90°) | Taip | — | Taip |
| `fov_30` / `fov_60` | Regos lauko ribojimo kūgiai (30° / 60°) | Taip | — | — |
| `none` | Be uždengimo | — | — | Taip |

Pastabos apie konkrečius modelius:

* **DAQ-M turi vieną uždengimo profilį: `sunshine_cosine`.** „Bare-plus-Sunshine-cap“ yra jo produkto apibrėžimas, o „bare“ DAQ-M nereikia jokio geometrinio profilio.
* **„Bare“ DAQ-U yra tikrasis „bare“** — jam visiškai nereikia jokio geometrinio profilio, todėl jam nėra `none` profilio.
* **`none` ant DAQ-E nėra neveikiantis profilis.** DAQ-E įdubęs, stiklu padengtas difuzorius turi savo tikrą geometrinę korekciją, todėl „be dangtelio“ šiam modeliui savaime yra išmatuotas profilis.
* **Golas DAQ-E negali matuoti tiesioginių saulės spindulių bet kokiame aukštyje** — „Sunshine“ dangtelis yra lauko konfigūracija. Neplanuokite lauko darbų su golu DAQ-E.

GUI nustatymuose, skirtuose kiekvienam jutikliui atskirai (dantukų piktograma skirtuke „Šviesos jutikliai“), išskleidžiamajame meniu **„Dangtelis“** DAQ-U ir DAQ-M modeliuose taip pat siūloma parinktis „Nėra (neuždengtas jutiklis)“ — šiuose dviejuose modeliuose „neuždengtas“ tiesiog reiškia, kad, kaip nurodyta aukščiau, dangtelio korekcija netaikoma. Pasirinkite šią parinktį tik tada, kai dangtelis yra fiziškai nuimtas.

## Dangtelio deklaravimas — ir kodėl tai svarbu

**Deklaruotas kodas `cap_id` turi atitikti dangtelį, kuris fiziškai yra uždėtas ant jutiklio.** Nei jutiklis, nei programinė įranga negali aptikti uždėto dangtelio. Šis nurodymas lemia du dalykus:

1. **Tiesioginę korekciją**, taikomą kiekvienam spektrui.
2. **Dangtelio žymą, įrašomą į kiekvieną `.daq` įrašą**, kuria remiasi tolesnis atspindžio apdorojimas.

„Sunshine“ dangtelis **pagal konstrukciją**silpnina signalą maždaug**12 kartų**, todėl įrašant su neteisingai deklaruotu dangteliu spektrai bus netiksliai masteliuojami maždaug šiuo koeficientu. Dangtelio pakeitimus deklaruokite nedelsiant.

### Dangtelio nustatymas

GUI: skirtukas „Šviesos jutikliai“ → ratuko piktograma jutiklio eilutėje → išskleidžiamasis meniu **„Dangtelis“**. Kiekvieno modelio numatytasis nustatymas yra „`sunshine_cosine`“ – tai prielaida (DAQ-U, DAQ-M ir DAQ-E tiekiami be dangtelio, o įraše nurodoma, kad dangtelis buvo numatytas, kol jūs jo nepasirinkote), ir šis pasirinkimas išlieka visam projektui.



CLI<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->
(turi veikti foninė programa):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI
sintaksiškai priima visą `cap_id` sąrašą (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); kiekvienas profilis prisijungimo metu patikrinamas pagal jutiklio modelį, todėl jei nurodytas neprieinamas ribos identifikatorius (pavyzdžiui, tik „E“ identifikatorius naudojant DAQ-U), sistema pateikia aiškią klaidą, o ne atlieka neteisingą koregavimą. Jei nieko nepateikiama, foninio proceso numatytasis nustatymas yra `sunshine_cosine`.

Python
SDK
: prisijungimo metu pavadinkite jungtį naudodami `connect_daq_sensor(cap_id="sunshine_cosine")` arba pakeiskite ją vykdymo metu naudodami `DAQSensorSession.set_cap("fov_45")`; žr. [SDK
žinyną](../reference/sdk-reference.md#daq-sensor-sessions).

Išplėstinė informacija: profiliai pateikiami Chloros
diegimo pakete kaip `daq/cap_profiles/<u|m|e>/<cap_id>.json` ir gali būti perrašyti kiekvienam vartotojui atskirai kaip `~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json`.

Be ribų, jutikliai, kurie niekada nebuvo pakalibruoti, automatiškai gauna nedidelį, iš įrenginių parko gautą tamsos nuokrypio patikslinimą — vartotojo veiksmų nereikia.

## DAQ-E-S: fiksuotas difuzorius ir pakreipimas

**DAQ-E-S** yra „DAQ-E“ su dviem skirtumais, kurie svarbūs Chloros
sistemai. Viskas kita (aptikimas, prisijungimas, PTP, įrašymas, įrenginio nustatymai) veikia lygiai taip pat kaip ir „DAQ-E“ atveju.

* **Jo difuzorius yra nuolatinis, todėl nereikia jokios dangtelio korekcijos.** Difuzorius buvo pritvirtintas prie įrenginio, kai buvo atliekamas jo gamyklinis kalibravimas, todėl kalibravimas jau rodo tikrąjį žemyn nukreiptą spinduliavimą. Chloros
jam netaiko jokio ribos profilio, kad ir ko būtų prašoma: jam pavadinta riba ignoruojama su pranešimu, jo įrašuose nurodoma, kad riba buvo nustatyta pagal modelį, o įspėjimas apie numanomą ribą jam niekada nepasireiškia. Meniu „Project Settings“ → „DAQ Light Sensor“ jo eilutėje nerodomas joks ribojimas, o visam projektui taikomas ribojimo perrašymas jam netaikomas. Netaikykite ribojimo jo duomenims rankiniu būdu: „Sunshine“ korekcija šiuo atveju rodytų maždaug 11 kartų didesnę vertę.
* **Jame įmontuotas 3 ašių akselerometras.** Difuzoriaus kosinusinė charakteristika galioja tik tada, kai jis yra horizontalioje padėtyje, todėl pakreiptas jutiklis rodo neteisingą spinduliavimo intensyvumą be jokių kitų požymių. Todėl kiekvienas „DAQ-E-S“ rodmuo apima jo pakreipimą, pasvirimą ir polinkį, o iš vieno iš jų įrašytas „`.daq`“ juos saugo šalia kiekvieno spektro (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, …), todėl galite atskirti debesį nuo pakrypusio laikiklio.

Apie kiekvieną padėties lauką ir tai, kaip pranešama apie padėties srauto gedimą, žr. [„DAQ-E-S“ CLI
žinyne](../reference/cli-reference.md#daq-e-s--fixed-diffuser-and-tilt-sensor).

## „Sunshine cap“ veikimas (konfigūracija lauke)

Skaičiai, kuriais galite remtis kurdami procedūras:

| Savybė | Vertė |
| --- | --- |
| Regėjimo laukas | 180° pusrutulinis |
| Kosinusinės reakcijos paklaida | ≤ ±4 % iki 60° kritimo kampo; ≤ ±4,5 % iki 70° |
| Žemos saulės riba | Nerekomenduojama, kai saulės aukštis mažesnis nei ~15° |
| Silpnėjimas | ~12× (pagal projektą) |
| Dangtelio pakartotinio montavimo pakartojamumas | ≈ 1,5 % |
| Kiekybinis spinduliavimo intensyvumas | Vidutiniai **≥ 15 s** rodmenys (prietaiso charakteristika, ne defektas) |

Bet kokiam kiekybiniam spinduliavimo intensyvumo skaičiui — įskaitant atspindžio rodiklius — naudokite ne vieno kadro, o bent 15 sekundžių rodmenų vidurkį.

## Kalibruotas spektrinis diapazonas

| Savybė | Vertė |
| --- | --- |
| Spektrinis diskretizavimas | 340–1010 nm 5 nm žingsniais (135 taškai) |
| Radiometriškai kalibruotas diapazonas | **~374–974 nm** (nustatoma programine įranga) |

Jutiklis pateikia visą 340–1010 nm tinklelį, tačiau NIST standartais atsekamas radiometrinis stiprinimas apima ~374–974 nm. Chloros
**atmeta absoliučiojo atspindžio padalijimą** bet kuriam kameros diapazonui, kurio mažiau nei pusė spektrinio svorio patenka į tą intervalą, ir pateikia praleidimo priežastį `dls-uncalibrated-band-<nm>`, o ne sukuria nekalibruotą rezultatą. Iš visų parduodamų kamerų modelių tik F988 filtras nepatenka į šį diapazoną; jo atveju vietoj to naudojama atspindžio plokštės darbo eiga — žr. [Atspindžio darbo eigos](reflectance.md).

Apie jutiklių modelius, perdavimo būdus ir jutiklių ID žr. [DAQ apžvalgą](README.md). Apie tai, kaip apdorojimo metu naudojamas „cap“ žymėjimas, žr. [Įrašymas ir .daq formatas](recording.md).
