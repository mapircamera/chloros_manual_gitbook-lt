# CLI greitasis pradžios vadovas (pool-*)

Pristatytas „`chloros-cli`“ valdo DAQ jutiklius naudodamas **„`daq pool-*`“** komandų šeimą — tai „ploni“ HTTP klientai, kurie valdo jutiklį per Chloros užkulisio nuolatinį jutiklių rezervą. Backend valdo duomenų perdavimą, todėl GUI, CLI ir SDK skriptai visi naudoja vieną aktyvų identifikatorių, o ne konkuruoja dėl prievado. Viskas, ko reikia klientui, pasiekiama per „`pool-*`“: prisijungimas, duomenų srautas, kalibruotų „`.daq`“ failų įrašymas ir „cap“ profilių keitimas.

`pool-*` yra palaikoma DAQ sąsaja išleistose versijose. `chloros-cli daq --help` išvardija `pool-*` pakomandas, o `daq discover` / `daq list` atlieka tą patį nuskaitymą kaip ir `pool-discover`. Jei tiesioginės aparatinės įrangos DAQ pakomandos nėra, programa užsidaro, parodydama aiškią klaidą, kurioje nurodomas trūkstamas paketas ir nukreipiama atgal į „`pool-*`“ — niekas nesibaigia tyliai. (`pip install chloros-sdk` taip pat nepateikia komandų, skirtų tiesioginiam sąsajos su aparatine įranga valdymui.)

***

## Būtinos sąlygos

* **Chloros foninė programa turi veikti** — komandos „`pool-*`“ yra HTTP-klientai, o ne aparatinės įrangos tvarkyklės. Windows sistemoje paleiskite Chloros darbalaukio programą (ji paleidžia foninę programą). Linux/„Jetson“ sistemose be grafinės sąsajos įjunkite paslaugą savo vartotojo vardu: `sudo systemctl enable --now chloros-backend@$USER.service` (žr. [Linux diegimas](../linux/linux-installation.md#always-on-ptp-for-headless-hosts)).
* **„Chloros+“ (mokamas planas) prisijungimas**: pirmiausia paleiskite komandą `chloros-cli login`. Prieigos kontrolė vykdoma serverio pusėje — be prisijungimo komandos su `401 AUTH_REQUIRED` nepavyks; nemokamame („Iron“) lygyje jos nepavyks su `403 PLAN_UPGRADE_REQUIRED`.
* Komandos pagal numatytuosius nustatymus skirtos `http://127.0.0.1:5000`; `daq pool-*` šeimos komandos atsižvelgia į aplinkos kintamąjį `CHLOROS_BACKEND_URL`, jei jūsų užkulisinė sistema veikia kitur.

***

## Penkių minučių sesija

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)
#    (a DAQ-M's BLE MAC is not printed anywhere: find it with `chloros-cli daq pool-discover --only ble`)

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
| `daq pool-connect` | Išmanusis aptikimas: surasti bet kurį DAQ šiame kompiuteryje. |
| `daq pool-connect --port PORT` | DAQ-U per konkretų nuoseklųjį prievadą (pvz., `COM3`, `/dev/ttyUSB0`). |
| `daq pool-connect --ble` | DAQ-M per BLE, MAC adresas nustatytas automatiniu nuskaitymu. |
| `daq pool-connect --mac MAC` | DAQ-M su žinomu BLE MAC adresu (reiškia `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E su žinomu kompiuterio vardu arba IP adresu — **patikimas būdas**. |
| `daq pool-connect --eth` | DAQ-E su automatiniu aptikimu (mDNS, su ARP atsarginiu variantu). Žr. toliau pateiktą įspėjimą. |

Nustatymo žymės, visos neprivalomos:

| Žymė | Reikšmė |
| --- | --- |
| `--integration-time MS` / `-t MS` | Rankinis integravimo laikas milisekundėmis. |
| `--frame-avg N` / `-f N` | Kadrų skaičiaus vidurkis vienam pranešamam spektrui. |
| `--no-ae` | Išjungti automatinę ekspoziciją (AE įjungta pagal numatytuosius nustatymus). |
| `--no-stream` | Prisijungti nepradedant srauto (vėliau tęsti naudojant `pool-stream --start`). |
| `--cap-id CAP` | „Cap“ korekcijos profilis; numatytasis užpakalinės dalies nustatymas yra `sunshine_cosine`. Žr. [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

Prijungus DAQ-E, kuris jau yra pulke pagal kitą jo adresą — jo `daq-e-<id>.local` kompiuterio vardą arba IP adresą — grąžinamas esamas pulko įrašas, o ne atidaromas įrenginys antrą kartą.

{% hint style="warning" %}
**`--eth` automatinio aptikimo įspėjimas.** Daugialypio prisijungimo kompiuteryje (turinčiame daugiau nei vieną aktyvią tinklo sąsają) *pirmasis* `pool-connect --eth` paleidimo metu gali būti tuščias, net jei jutiklis veikia tinkamai — paieškos procesas gali nepranešti apie jutiklio sąsają, kol ARP talpykla yra tuščia. Jei `--eth` nieko neranda, pabandykite dar kartą arba visiškai praleiskite aptikimą naudodami `--eth-host <ip-or-hostname>`, kuris yra patikimas būdas kompiuteriuose su keliais tinklo sąsajomis. DAQ-E kompiuterio vardas yra `daq-e-<id>.local` (pvz., `daq-e-def330.local`); taip pat tinka ir jo paprastas IP adresas.
{% endhint %}

## `pool-discover` — nuskaitymas be prisijungimo

```bash
chloros-cli daq pool-discover                  # USB, BLE and Ethernet
chloros-cli daq pool-discover --only ble       # BLE only: this is how you find a DAQ-M's MAC
chloros-cli daq pool-discover --json           # machine-readable
```

Ieško jutiklių, prie kurių galėtumėte prisijungti, ir rodo adresą, kuriuo reikia prisijungti, neprisijungiant. `--only` priima kableliais atskirtą `usb,ble,eth` dalį; `--timeout SEC` nustato skenavimo trukmę kiekvienam perdavimo būdui (numatyta vertė – 5 s, ribojama nuo 1 iki 20 s). Jau atidaryti jutikliai rezervuare nėra rodomi sąraše (prisijungęs DAQ-M nustoja siųsti informaciją) — jiems naudokite `pool-list`. Išleistoje versijoje komandos „`chloros-cli daq discover`“ ir „`daq list`“ atlieka tą patį nuskaitymą.

## „`pool-list`“ — peržiūrėti, kas yra prijungta

Rodo visus jutiklius užkulisiniame pulke, įskaitant „`sensor_id`“, kurio reikia visoms kitoms komandoms:

| Modelis | „`sensor_id`“ forma | Pavyzdys |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5 oktetų su brūkšneliais | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

DAQ-E, kurio PTP funkcija buvo išjungta (nustatymas išsaugotas įrenginyje), `pool-list` žymimas kaip `!! PTP is switched OFF on this unit (saved in its flash): its timestamps are not PTP-synced.` ir pateikiama komanda, kuri ją vėl įjungia — žr. [`pool-enable-ptp`](#pool-enable-ptp-switch-a-daq-es-ptp-back-on).

## `pool-latest` — spektro rėmelių skaitymas

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

Grąžina naujausią kadrą arba naujausius `--recent N` kadrus; `--json` generuoja kompiuteriui suprantamą išvestį, skirtą skriptams. Kadrai yra radiometriškai kalibruotas spektrinis spinduliavimo intensyvumas (W/m²/nm) 135 taškų, 340–1010 nm tinklelyje, jau pritaikius jutiklio dangtelio profilį. Norint gauti kiekybinius spinduliavimo intensyvumo rodiklius, reikia apskaičiuoti bent 15 sekundžių kadrų vidurkį — tai prietaiso charakteristika, o ne defektas.

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
| `--duration SEC` / `-d SEC` | `0` | Įrašymo trukmė sekundėmis; `0` reiškia, kad įrašymas tęsiamas tol, kol įvedate komandą `--stop`. |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Išvesties katalogas. Naudojant šio kompiuterio užkurtį, santykinis kelias nustatomas pagal aplanką, kuriame buvo paleista komanda; naudojant nuotolinį užkurtį, kelias nustatomas **kompiuteryje, kuriame veikia užkurtis**. |
| `--device-name NAME` | — | Kartu su įrašu saugoma etiketė. |
| `--stop` | — | Sustabdyti vykstantį įrašymą. |
| `--confirm-after SEC` | `20` | Pradėjus veikti, laukti iki šio laiko, kol bus įrašyta pirmoji eilutė, ir garsiai įspėti (`!! WARNING: no rows written after …s. This recording is producing an EMPTY file.`), jei ji neatsiranda. `0` išjungia šią patikrą. |
| `--heartbeat SEC` | `30` | Naudojant `--duration`, kas SEC sekundžių išspausdinkite eilučių skaičių ir įspėkite, jei jis nustoja didėti. `0` išjungia šią funkciją. |

{% hint style="info" %}
Įrašymas vyksta užkulisiuose, todėl `.daq` failas patenka į **užkulisinio kompiuterio** failų sistemą — pagal numatytuosius nustatymus į `~/Documents/DAQ Live View/` ten, nebūtinai ten, kur paleidote CLI. Failų pavadinimuose nurodytas jutiklio ID ir laiko žyma.
{% endhint %}

## `pool-set-cap` — nurodykite uždėtą dangtelį

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

Dangtelio ID parenka gamyklos išmatuotą korekcijos profilį, taikomą kiekvienam spektrui, ir jis **privalo atitikti fiziškai ant jutiklio uždėtą dangtelį** — nei jutiklis, nei programinė įranga negali savarankiškai aptikti dangtelio, o pasirinkimas įrašomas į kiekvieną `.daq` failą. Visur numatytasis nustatymas yra `sunshine_cosine` (kiekvienas DAQ tiekiamas su įdėtu „Sunshine“ kosinuso korektoriaus dangteliu, kurio silpninimas pagal projektą yra ~12× — nedeklaruotas dangtelio pakeitimas spektrus koreguoja netiksliai maždaug tuo koeficientu).

| `--cap-id` | Galima naudoti su |
| --- | --- |
| `sunshine_cosine` (numatyta) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | Tik DAQ-U |
| `none` | Tik DAQ-E — žr. pastabą |

Prijungiant jutiklį, kurio dangtelio identifikatorius neatitinka nustatytų parametrų, atsiranda aiški klaida. `none` (DAQ-E) reiškia, kad dangtelis fiziškai nuimtas — vis dar taikomas gamyklinis geometrijos profilis DAQ-E įdubusiam stikliniam difuzoriui, taigi tai nėra neveikianti konfigūracija, o „nuogas“ DAQ-E yra laboratorinė konfigūracija, o ne palaikoma lauko konfigūracija. (Neuždengtas DAQ-U yra tikrai neuždengtas ir jam visiškai nereikia korekcijos profilio; DAQ-M naudojamas su „Sunshine“ dangteliu.)

## `pool-disconnect` — jutiklių atlaisvinimas

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

`pool-disconnect` atlaisvina jutiklį, nesvarbu, kas jį atidarė, taigi jis taip pat atlaisvina jutiklį, kurį avarijos ištiktas skriptas paliko rezervoare (pirmiausia užbaigiamas jame vykstantis įrašymas). Jutiklis, kurio nėra rezervo sąraše, neturi ko atlaisvinti: komanda išspausdina „`<sensor-id>: already released (not in the pool)`“ ir baigia veikimą „`0`“. Atlaisvinimas, kurį užkulisinė programa atmetė arba nepatvirtino, baigiasi „`1`“.

## `pool-enable-ptp` — vėl įjungti DAQ-E PTP

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

DAQ-E savo PTP įjungimo/išjungimo nustatymus saugo savo atmintyje, todėl įrenginys, kurio PTP buvo išjungtas, lieka išjungtas — ir jo laiko žymos nėra sinchronizuojamos pagal PTP — kol jis vėl nebus įjungtas. `pool-list` pažymi tokį įrenginį ir išspausdina šią komandą. Chloros niekada pats neįjungia įrenginio iš naujo, nes išjungimas gali būti tyčinis. Nauja funkcija versijoje 1.3.0.

## DAQ-E įrenginio nustatymai

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto
chloros-cli daq pool-set-auto-stream --sensor-id daq-e-def330 --on
chloros-cli daq pool-reboot --sensor-id daq-e-def330
chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

`pool-info` rodo jutiklio programinę įrangą (ir ar ši Chloros versija turi naujesnę), jo adresą ir kaip jis jį gavo, išsaugotus tinklo nustatymus, jei jie skiriasi nuo dabartinių, įjungimo srautą ir PTP. `pool-set-network` iš naujo paleidžia jutiklį, kad pakeitimai įsigaliotų, ir vėl prijungia jį su ankstesniais nustatymais; `--no-reboot` tik išsaugo nustatymus. Statinis adresas turi būti šio kompiuterio tinkle, kitaip Chloros jo nepriims; „`--force`“ tai ignoruoja, tačiau tik kompiuteriui, kuriam bus priskirtas naujas adresas arba kuris bus nukreiptas į jį. Perkrovimas neleidžiamas, kol jutiklis atlieka įrašymą. `pool-update-firmware` yra GUI komandos **„Update to \<version\>**“ atitikmuo komandinėje eilutėje ir būdas atnaujinti DAQ-E Linux sistemoje, kurioje nėra GUI. Nauja funkcija versijoje 1.3.0.***

## Komandų santrauka

| Komanda | Paskirtis |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Atidaryti jutiklį užkulisiniame pulke. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Ieškoti jutiklių nesijungiant (randa DAQ-M BLE MAC adresą). |
| `daq pool-list` | Rodo visus pulko jutiklius su jų `sensor_id`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N naujausi kalibruoti spektro kadrai. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Atnaujinti / pristabdyti srautą. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--confirm-after SEC] [--heartbeat SEC] [--stop]` | Pradėti / sustabdyti `.daq` įrašymą (backend pusėje). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Pakeisti viršutinės ribos korekcijos profilį vykdymo metu. |
| `daq pool-disconnect --sensor-id ID [--all]` | Atleisti vieną jutiklį arba visus. |
| `daq pool-enable-ptp --sensor-id ID` | Vėl įjungti PTP DAQ-E įrenginiui, kurio PTP yra išjungtas. |
| `daq pool-info --sensor-id ID [--json]` | DAQ-E įrenginio programinė įranga, adresas, išsaugoti tinklo nustatymai, srauto perdavimas įjungus maitinimą ir PTP. |
| `daq pool-set-network --sensor-id ID (--auto \| --static IP [--mask M] [--gateway G]) [--no-reboot] [--force]` | DHCP arba statinis adresas; iš naujo paleidžia ir iš naujo prijungia jutiklį. |
| `daq pool-set-auto-stream --sensor-id ID (--on \| --off)` | Transliacija įjungus maitinimą. |
| `daq pool-reboot --sensor-id ID` | Perkraukite DAQ-E ir vėl jį prijunkite. |
| `daq pool-update-firmware --sensor-id ID` | Įrašykite šios versijos DAQ-E programinę įrangą į senesnį jutiklį. |

***

## Pirmojo DAQ-E prisijungimo problemų sprendimas

1. DAQ-E neturi būsenos šviesos diodo — patikrinkite maitinimą per PoE/ryšio indikatorių ant komutatoriaus arba įpurškimo prievado ir palaukite keletą sekundžių po įjungimo, kol jis įkels sistemą ir prisijungs prie tinklo.
2. Serveryje turi būti **tas pats transliacijos domenas** kaip ir jutiklyje — mDNS neperkelia duomenų per maršrutizatorius.
3. Užkarda turi leisti praeiti mDNS (UDP 5353), DAQ-E duomenis (UDP 5002) ir PTP (UDP 319/320). Windows diegimo programa prideda šiam tikslui skirtą taisyklę, o Linux `.deb` atidaro jas per `ufw` / `firewalld`, kuris buvo aktyvus diegimo metu; trečiosios šalies ugniasienė arba vėliau įjungta ugniasienė turi leisti šiuos srautus.
4. Vis dar nėra atsakymo iš `--eth`? Naudokite `--eth-host` su įrenginio kompiuterio vardu (`daq-e-<id>.local`) arba IP adresu, taip praleidžiant paiešką.

***
{% hint style="info" %}
**Patarimas AI asistentams.** Kiekvienas šio vadovo puslapis pateikiamas kaip neapdorotas Markdown formatas — pridėkite `.md` prie puslapio mažosiomis raidėmis parašyto URLo (šis puslapis: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); kompiuteriui suprantamas indeksas yra `https://mapir.gitbook.io/chloros/llms.txt`. Norėdami gauti išsamią `chloros-cli daq` ir visų kitų komandų šeimų žymių lygio dokumentaciją, atsisiųskite [CLI žinyną](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); Python kelias yra `chloros_sdk.connect_daq_sensor()`, nurodytas [SDK nuorodoje](../reference/sdk-reference.md).
{% endhint %}
</version\>
