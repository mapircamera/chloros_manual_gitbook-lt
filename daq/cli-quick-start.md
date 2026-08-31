# CLI Greitasis pradžios vadovas (pool-*)

Pristatytas `chloros-cli` valdo DAQ jutiklius naudodamas **`daq pool-*`** komandų šeimą — ploni HTTP klientai, kurie valdo jutiklį per Chloros užkulisio nuolatinį jutiklių fondą. Už transportą atsakingas užkulisinis modulis, todėl grafinė vartotojo sąsaja, skriptai „CLI“ ir „SDK“ visi naudoja tą patį aktyvų identifikatorių, o ne konkuruoja dėl prievado. Viskas, ko reikia klientui, pasiekiama per „`pool-*`“: prisijungimas, duomenų srautas, kalibruotų „`.daq`“ failų įrašymas ir „cap“ profilių keitimas.

`pool-*` taip pat yra **vienintelis** DAQ paviršius išleistose versijose. `chloros-cli daq --help` išvardija `pool-*` pakomandas, o tiesioginės aparatinės įrangos DAQ pakomandos paleidimas išleistoje versijoje baigiasi aiškia klaida, kurioje nurodomas trūkstamas paketas ir nukreipiama atgal į `pool-*` — niekas nesibaigia tyliai. (Komandos, skirtos tiesioginiam sąsajos su aparatine įranga valdymui, veikia tik iš MAPIR šaltinio versijos; `pip install chloros-sdk` jų taip pat nepateikia.)

***

## Būtinos sąlygos

* **Turi veikti „Chloros“ foninė programa** — „`pool-*`“ komandos yra „HTTP“ klientai, o ne aparatinės įrangos tvarkyklės. „Windows“ paleiskite „Chloros“ darbalaukio programą (ji paleidžia foninę programą). „Linux/Jetson“ be monitoriaus įjunkite paslaugą: „`sudo systemctl enable --now chloros-backend.service`“.
* **Prisijungimas prie „Chloros+“ (mokamo lygio)**: pirmiausia paleiskite „`chloros-cli login`“. Prieigos kontrolė vykdoma serverio pusėje — be prisijungimo komandos su `401 AUTH_REQUIRED` nepavyksta; nemokamame („Iron“) pakete jos nepavyksta su `403 PLAN_UPGRADE_REQUIRED`.
* Komandos pagal numatytuosius nustatymus skirtos `http://127.0.0.1:5000`; `daq pool-*` šeima atsižvelgia į aplinkos kintamąjį `CHLOROS_BACKEND_URL`, jei jūsų užkulisiai veikia kitur.

***

## Penkių minučių sesija

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)

# 2. List the pool — this shows the sensor_id used by every command below
chloros-cli daq pool-list

# 3. Read the most recent calibrated spectrum frame (add --json for scripting)
chloros-cli daq pool-latest --sensor-id daq-e-def330 --json

# 4. Record a calibrated .daq file for 60 seconds
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 60 \
  --device-name "field-A"

# 5. Release the sensor when done
chloros-cli daq pool-disconnect --sensor-id daq-e-def330
```

***

## `pool-connect` — atidaryti jutiklį iš rezervo

| Variantas | Reikšmė |
| --- | --- |
| `daq pool-connect` | Išmanusis aptikimas: surasti bet kurį DAQ šioje mašinoje. |
| `daq pool-connect --port PORT` | „DAQ-U“ per konkretų nuoseklųjį prievadą (pvz., `COM3`, `/dev/ttyUSB0`). |
| `daq pool-connect --ble` | DAQ-M per BLE, MAC adresas nustatytas automatiniu nuskaitymu. |
| `daq pool-connect --mac MAC` | DAQ-M su žinomu BLE MAC adresu (tai reiškia `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E su žinomu kompiuterio vardu arba IP adresu — **patikimas būdas**. |
| `daq pool-connect --eth` | DAQ-E su automatiniu aptikimu (mDNS, su ARP atsarginiu variantu). Žr. toliau pateiktą įspėjimą. |

Nustatymo žymės, visos neprivalomos:

| Žymė | Reikšmė |
| --- | --- |
| `--integration-time MS` / `-t MS` | Rankinis integravimo laikas milisekundėmis. |
| `--frame-avg N` / `-f N` | Kadrų skaičius, kurio vidurkis skaičiuojamas pagal pranešamą spektrą. |
| `--no-ae` | Išjungti automatinę ekspoziciją (AE pagal numatytuosius nustatymus įjungta). |
| `--no-stream` | Prisijungti nepradedant srauto (vėliau tęsti naudojant `pool-stream --start`). |
| `--cap-id CAP` | „Cap“ korekcijos profilis; numatytasis užpakalinės dalies nustatymas yra `sunshine_cosine`. Žr. [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

{% hint style="warning" %}
**`--eth` automatinio aptikimo įspėjimas.** Daugialypio prisijungimo kompiuteryje (turinčiame daugiau nei vieną aktyvią tinklo sąsają) *pirmasis* `pool-connect --eth` paleidimo metu gali būti tuščias, net jei jutiklis veikia tinkamai — paieškos procesas gali nepranešti apie jutiklio sąsają, kol ARP talpykla yra tuščia. Jei `--eth` nieko neranda, pabandykite dar kartą arba visiškai praleiskite aptikimą naudodami `--eth-host <ip-or-hostname>` – tai patikimas būdas kompiuteriuose su keliais tinklo sąsajų adresais. DAQ-E kompiuterio vardas yra `daq-e-<id>.local` (pvz., `daq-e-def330.local`); taip pat tinka ir jo paprastas IP adresas.
{% endhint %}

## `pool-list` — peržiūrėkite, kas yra prijungta

Rodo visus jutiklius užkulisiniame pulke, įskaitant `sensor_id`, kurio reikia visoms kitoms komandoms:

| Modelis | `sensor_id` forma | Pavyzdys |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5 oktetų su brūkšneliais | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

## `pool-latest` — spektro rėmelių skaitymas

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

Grąžina naujausią kadrą arba naujausius `--recent N` kadrus; `--json` generuoja kompiuteriui suprantamą išvestį, skirtą skriptams. Kadrai yra radiometriškai kalibruotas spektrinis spinduliavimo intensyvumas (W/m²/nm) 135 taškų, 340–1010 nm tinklelyje, jau pritaikius jutiklio dangtelio profilį. Norint gauti kiekybinius spinduliavimo intensyvumo rodiklius, reikia apskaičiuoti bent 15 sekundžių kadrų vidurkį — tai prietaiso charakteristika, o ne gedimas.

## `pool-stream` — srauto sustabdymas arba atnaujinimas

```bash
chloros-cli daq pool-stream --sensor-id daq-e-def330 --stop    # pause
chloros-cli daq pool-stream --sensor-id daq-e-def330 --start   # resume
```

## `pool-record` — įrašyti `.daq` failą

```bash
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
  --output ~/Documents/spectra --device-name "rooftop-A"
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

| Žymė | Numatytasis | Reikšmė |
| --- | --- | --- |
| `--duration SEC` / `-d SEC` | `0` | Įrašymo trukmė sekundėmis; `0` reiškia, kad įrašas turi vykti tol, kol bus pateiktas `--stop`. |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Išvesties katalogas, nustatomas **kompiuteryje, kuriame veikia užkulisinė programa**. |
| `--device-name NAME` | — | Kartu su įrašu saugoma etiketė. |
| `--stop` | — | Sustabdyti vykstantį įrašymą. |

{% hint style="info" %}
Įrašymas vyksta serveryje, todėl `.daq` failas patenka į **backend kompiuterio** failų sistemą — pagal numatytuosius nustatymus ten, kur yra `~/Documents/DAQ Live View/`, nebūtinai ten, kur paleidote CLI. Failų pavadinimuose nurodytas jutiklio ID ir laiko žyma.
{% endhint %}

## `pool-set-cap` — nurodykite uždėtą dangtelį

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

Dangtelio ID parenka gamyklos išmatuotą korekcijos profilį, taikomą kiekvienam spektrui, ir jis **privalo atitikti fiziškai ant jutiklio uždėtą dangtelį** — nei jutiklis, nei programinė įranga negali savarankiškai aptikti dangtelio, o pasirinkimas įrašomas į kiekvieną „`.daq`“ failą. Visur numatytasis nustatymas yra `sunshine_cosine` (kiekvienas DAQ tiekiamas su įdėtu „Sunshine“ kosinusinio koregavimo dangteliu, kurio silpninimas pagal projektą yra ~12× — nedeklaruotas dangtelio pakeitimas spektrus koreguoja netiksliai maždaug tuo koeficientu).

| `--cap-id` | Prieinama |
| --- | --- |
| `sunshine_cosine` (numatyta) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | Tik DAQ-U |
| `none` | Tik DAQ-E — žr. pastabą |

Prijungiant jutiklį, kurio dangtelio identifikatorius neatitinka nustatytų parametrų, atsiranda aiški klaida. `none` (DAQ-E) reiškia, kad dangtelis yra fiziškai nuimtas — vis tiek taikomas gamyklinis geometrinis profilis DAQ-E įdubusiam stikliniam difuzoriui, taigi tai nėra neveikianti konfigūracija, o „nuogas“ DAQ-E yra laboratorinė konfigūracija, o ne palaikoma lauko konfigūracija. (Neuždengtas DAQ-U yra tikrai neuždengtas ir jam visiškai nereikia korekcijos profilio; DAQ-M naudojamas su „Sunshine“ dangteliu.)

## `pool-disconnect` — jutiklių atlaisvinimas

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

***

## Komandų santrauka

| Komanda | Paskirtis |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Atidaryti jutiklį užpakalinės dalies pulke. |
| `daq pool-list` | Rodyti visus baseino jutiklius su jų `sensor_id`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N naujausių kalibruotų spektro kadrų. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Tęsti / pristabdyti srautą. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--stop]` | Pradėti / sustabdyti `.daq` įrašymą (backend pusėje). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Pakeisti viršutinės ribos korekcijos profilį vykdymo metu. |
| `daq pool-disconnect --sensor-id ID [--all]` | Atjungti vieną jutiklį arba visus. |

***

## Pirmojo „DAQ-E“ prisijungimo problemų sprendimas

1. „DAQ-E“ neturi būsenos šviesos diodo — patikrinkite maitinimą per PoE/ryšio indikatorių komutatoriuje arba įrenginio prievade ir palaukite keletą sekundžių po įjungimo, kol įrenginys įkels sistemą ir prisijungs prie tinklo.
2. Galinis kompiuteris turi būti **tame pačiame transliacijos domene** kaip ir jutiklis — mDNS neperkelia duomenų per maršrutizatorius.
3. Windows įrenginyje pirmą kartą paleidžiant priimkite „Defender“ ugniasienės užklausą (mDNS UDP 5353, DAQ-E duomenys UDP 5002, PTP UDP 319/320).
4. Vis dar nėra jokio signalo iš „`--eth`“? Naudokite „`--eth-host`“ su įrenginio kompiuterio vardu (`daq-e-<id>.local`) arba IP adresu – tai patikimas būdas, ypač kompiuteriuose su keliais IP adresais.

***{% hint style="info" %}**Patarimas AI asistentams.** Kiekvienas šio vadovo puslapis pateikiamas kaip neapdorotas „Markdown“ formatas — pridėkite `.md` prie puslapio mažosiomis raidėmis rašomo slugo URL (šis puslapis: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); kompiuteriui suprantamas indeksas yra `https://mapir.gitbook.io/chloros/llms.txt`. Norėdami gauti išsamią `chloros-cli daq` ir visų kitų komandų šeimų žymių lygio dokumentaciją, atsisiųskite [CLI nuorodą](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); Python kelias yra `chloros_sdk.connect_daq_sensor()` [SDK nuorodoje](../reference/sdk-reference.md).
{% endhint %}
