# Dangtelių profiliai ir kalibruotas diapazonas

> Patys dangteliai – su kuriuo jutikliu jie tiekiami, kaip montuojami ir kokios yra jų optinės savybės – aprašyti **[DAQ vartotojo vadove](https://mapir.gitbook.io/daq)**. Šiame puslapyje aprašoma, kaip *nurodyti* įmontuotą dangtelį Chloros, nes būtent tai užtikrina teisingą korekciją.

Kiekvieno DAQ šviesos jutiklio gamyklinis radiometrinis kalibravimas apibūdina *grynąjį* jutiklį. Fizinis dangtelis, uždėtas ant difuzoriaus, keičia tai, kokią šviesą surenka jutiklis, todėl Chloros kalibravimo rinkiniui taiko gamykliniu būdu išmatuotą **dangtelio korekcijos profilį**. Teisingo dangtelio deklaravimas yra dalis kalibruotų duomenų gavimo proceso — šiame puslapyje aprašoma, kokie dangteliai yra kiekvienam modeliui, kaip juos deklaruoti ir koks iš tikrųjų yra jutiklio kalibruotas spektrinis diapazonas.

## Dangtelių prieinamumas pagal modelį

| Dangtelio profilis (`cap_id`) | Fizinis dangtelis | DAQ-U | DAQ-M | DAQ-E |
| --- | --- | --- | --- | --- |
| `sunshine_cosine` | „Sunshine“ kosinuso korektoriaus dangtelis (**nustatytas pagal numatytuosius parametrus kiekvienam modeliui**) | Taip | Taip | Taip |
| `fov_15` / `fov_45` / `fov_90` | FOV ribojantys kūgiai (15° / 45° / 90°) | Taip | — | Taip |
| `fov_30` / `fov_60` | Regos lauko ribojimo kūgiai (30° / 60°) | Taip | — | — |
| `none` | Be dangtelio | — | — | Taip |

Pastabos apie konkrečius modelius:

* **DAQ-M turi vieną dangtelio profilį: `sunshine_cosine`.** „Bare-plus-Sunshine-cap“ yra jo produkto apibrėžimas, o „bare“ DAQ-M nereikia jokio geometrinio profilio.
* **„Bare“ DAQ-U yra tikrasis „bare“** — jam visiškai nereikia jokio geometrinio profilio, todėl jam nėra profilio `none`.
* **`none` ant DAQ-E nėra neveikiantis profilis.** DAQ-E įdubęs, stiklu padengtas difuzorius turi savo tikrą geometrinę korekciją, todėl „be dangtelio“ šiam modeliui pats savaime yra išmatuotas profilis.
* **Golas DAQ-E negali matuoti tiesioginių saulės spindulių jokiu aukščiu** — „Sunshine“ dangtelis yra lauko konfigūracija. Neplanuokite lauko darbų su golu DAQ-E.

GUI nustatymuose, skirtuose kiekvienam jutikliui atskirai (dantytojo piktograma skirtuke „Šviesos jutikliai“), išskleidžiamajame meniu **„Dangtelis“** DAQ-U ir DAQ-M modeliuose taip pat siūloma parinktis „Nėra (neuždengtas jutiklis)“ — šiuose dviejuose modeliuose „neuždengtas“ tiesiog reiškia, kad, kaip nurodyta aukščiau, dangtelio korekcija netaikoma. Pasirinkite šią parinktį tik tada, kai dangtelis yra fiziškai nuimtas.

## Dangtelio deklaravimas — ir kodėl tai svarbu

**Deklaruotas kodas `cap_id` turi atitikti dangtelį, kuris fiziškai yra uždėtas ant jutiklio.** Nei jutiklis, nei programinė įranga negali aptikti uždėto dangtelio. Šis nurodymas lemia du dalykus:

1. **Tiesioginę korekciją**, taikomą kiekvienam spektrui.
2. **Dangtelio žymą, įrašomą į kiekvieną `.daq` įrašą**, kuria remiasi tolesnis atspindžio apdorojimas.

„Sunshine“ dangtelis **pagal konstrukciją**silpnina signalą maždaug**12 kartų**, todėl įrašant su neteisingai deklaruotu dangteliu spektrai yra netiksliai masteliuojami maždaug tuo pačiu koeficientu. Dangtelio pakeitimus deklaruokite nedelsiant.

### Dangtelio nustatymas

GUI: skirtukas „Šviesos jutikliai“ → ratuko piktograma jutiklio eilutėje → išskleidžiamasis meniu **„Dangtelis“**. Kiekvieno modelio numatytasis nustatymas yra „`sunshine_cosine`“ (visi DAQ jutikliai tiekiami su įdiegtu kosinuso korektoriumi), ir šis pasirinkimas išlieka visam projekto laikotarpiui.

<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->

„CLI“ (turi veikti užkulisinė programa):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI sintaksiškai priima visą `cap_id` sąrašą (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); kiekvienas profilis patikrinamas pagal jutiklio modelį prisijungimo metu, todėl jei kondensatoriaus identifikatorius yra neprieinamas (pavyzdžiui, tik „E“ identifikatorius „DAQ-U“ įrenginyje), atsiranda aiški klaida, o ne neteisingas koregavimas. Jei nieko neperduodama, numatytasis užkulisio nustatymas yra `sunshine_cosine`.

Python SDK pastaba: `cap_id` **nėra** SDK reguliatorius — `connect_daq_sensor()` / `DAQSensorSession` neatskleidžia jokio kondensatoriaus parametro. Pasirinkite ribą naudodami aukščiau nurodytas „CLI“ komandas arba GUI išskleidžiamąjį meniu; žr. [„SDK“ nuorodą](../reference/sdk-reference.md).

Išplėstinė informacija: profiliai pateikiami „Chloros“ diegimo pakete, esančiame „`daq/cap_profiles/<u|m|e>/<cap_id>.json`“, ir juos galima perrašyti kiekvienam vartotojui atskirai „`~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json`“.

Neatsižvelgiant į ribas, jutikliai, kurie niekada nebuvo pakalibruoti, automatiškai gauna nedidelį, remiantis visos įrangos duomenimis nustatytą tamsos nuokrypio patikslinimą — vartotojo veiksmų nereikia.

## „Sunshine“ ribos veikimas (konfigūracija lauke)

Skaičiai, kuriais galite remtis kurdami procedūras:

| Savybė | Vertė |
| --- | --- |
| Matymo laukas | 180° pusrutulinis |
| Kosinusinės reakcijos paklaida | ≤ ±4 % iki 60° kritimo kampo; ≤ ±4,5 % iki 70° |
| Žemos saulės riba | Nerekomenduojama, kai saulės aukštis mažesnis nei ~15° |
| Silpnėjimas | ~12× (pagal konstrukciją) |
| Kepurėlės pakartotinio uždėjimo pakartojamumas | ≈ 1,5 % |
| Kiekybinis spinduliavimo intensyvumas | Vidutiniai **≥ 15 s** rodmenys (prietaiso charakteristika, ne defektas) |

Bet kokiam kiekybiniam spinduliavimo intensyvumo skaičiui — įskaitant atspindžio rodiklius — naudokite ne vieno kadro, o bent 15 sekundžių rodmenų vidurkį.

## Kalibruotas spektrinis diapazonas

| Savybė | Vertė |
| --- | --- |
| Spektrinis diskretizavimas | 340–1010 nm 5 nm žingsniais (135 taškai) |
| Radiometriškai kalibruotas diapazonas | **~374–974 nm** (nustatoma programoje) |

Jutiklis pateikia visą 340–1010 nm tinklelį, tačiau NIST standartams atitinkantis radiometrinis jautrumas apima ~374–974 nm diapazoną. Chloros **atmeta absoliučiojo atspindžio padalijimą** bet kuriam kameros juostos diapazonui, kurio mažiau nei pusė spektrinio svorio patenka į tą intervalą, ir pateikia praleidimo priežastį `dls-uncalibrated-band-<nm>`, o ne sukuria nekalibruotą rezultatą. Iš parduodamų kamerų modelių tik F988 filtras nepatenka į šį intervalą; jo atveju vietoj to naudojama atspindžio plokštės darbo eiga — žr. [Atspindžio darbo eigos](reflectance.md).

Apie jutiklių modelius, perdavimo būdus ir jutiklių ID žr. [DAQ apžvalgą](README.md). Apie tai, kaip apdorojimo metu naudojamas „cap“ žymėjimas, žr. [Įrašymas ir .daq formatas](recording.md).
