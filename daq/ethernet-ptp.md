# DAQ-E tinklo konfigūracija ir laiko sinchronizavimas

> Fizinė jutiklio tinklo konfigūracija – kabelių montavimas, PoE, IP adresų priskyrimas ir paties įrenginio tinklo nustatymai – aprašyta **[DAQ vartotojo vadove](https://mapir.gitbook.io/daq/daq-e/network-setup)**. Šiame puslapyje aptariama Chloros

pusė: prijungimas, laiko sinchronizavimas ir ką daryti, jei paieška neduoda rezultatų.

DAQ-E yra „DAQ“ šeimos narys, veikiantis per „Ethernet“ tinklą: maitinamas per PoE, aptinkamas per mDNS (paslauga `_daq-e._tcp`), o jo adresas nustatomas pagal kompiuterio vardą, sudarytą iš jutiklio ID — `daq-e-<6 hex>.local`, pvz., `daq-e-def330.local`. Šiame puslapyje aprašoma, kaip jis perduoda duomenis tinkle ir kaip dalyvauja PTP laiko sinchronizavime.

## Perdavimo režimai

| Režimas | Galinis taškas | Vartotojai | Pastabos |
| --- | --- | --- | --- |
| **Daugiaadresis** (numatyta) | UDP `239.10.10.10:5002` | Bet koks skaičius tame pačiame LAN tinkle gauna tą patį srautą | Kiekviena datagrama patikrinama pagal CRC-16/CCITT |
| **Neapdoroti duomenys** | TCP prievadas `5000` | Tiksliai vienas klientas (išskirtinis) | Suderinamas su DAQ-U baitų lygiu |

Chloros

pagal numatytuosius nustatymus naudoja daugiaadresinį perdavimą, o tai leidžia GUI, CLI

ir SDK

vienu metu stebėti vieną jutiklį.

## Tinklo reikalavimai

* **Tas pats transliacijos domenas.** Kompiuteris, kuriame veikia Chloros

, turi būti tame pačiame L2 tinklo segmente kaip ir jutiklis — mDNS atradimas neperžengia maršrutizatorių.
* **Ugniasienė.** Windows

sistemoje diegimo programa kiekviename tinklo profilyje prideda **„Chloros

Backend (UDP-In)“**ugniasienės taisyklę, kuri apima DAQ-E duomenis (UDP 5002), mDNS (UDP 5353) ir PTP (UDP 319/320), taip pat taisyklę**„Chloros

Backend (TCP-In)“**, apribotą vietiniu potinkliu, skirtą DAQ-E ryšiui su kompiuteriu programinės įrangos atnaujinimo metu. Linux

sistemoje „`.deb`“ atidaro UDP prievadus „`ufw`“ arba „`firewalld`“, jei ta ugniasienė yra aktyvi diegimo metu. Trečiosios šalies ugniasienėje arba vėliau įjungtoje ugniasienėje turi būti leidžiami tie patys prievadai.
* **PoE maitinimas, nėra būsenos šviesos diodo.** „DAQ-E“ neturi savo šviesos diodo — maitinimą patikrinkite per jungiklio arba įpurškimo prievado „link/PoE“ indikatorių ir po įjungimo palaukite keletą sekundžių, kol įrenginys įkels sistemą ir prisijungs prie tinklo.

## Prijungimas

**GUI:** Skirtukas „Šviesos jutikliai“ → „Prijungti jutiklį“ → Įrenginio tipas „DAQ-E (Ethernet)“. Paieška vyksta tik tol, kol ekrane rodomas prisijungimo dialogas (mDNS paieška ir ARP nuskaitymas Windows

sistemoje), kartojasi kas 15 sekundžių; mygtukas „Atnaujinti“ iškart atlieka pakartotinę paiešką. Aptikti jutikliai rodomi išskleidžiamajame meniu; automatiškai pasirenkamas pirmasis aptiktas jutiklis.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (veikiantis užkulisinis procesas):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

DAQ-E, jau esantis sąraše, atpažįstamas tiek pagal jo kompiuterio vardą, tiek pagal IP adresą: jei jį vėl prijungiate naudodami kitą adresą, bus grąžintas esamas įrašas sąraše, o ne atidarytas įrenginys antrą kartą.

### Kompiuteriai su keliais tinklo sąsajų moduliais (NIC) ir pirmasis prisijungimas po paleidimo

Kompiuteriuose, turinčiuose daugiau nei vieną aktyvią tinklo sąsają, **pirmasis** `pool-connect --eth` po paleidimo gali būti tuščias, net jei jutiklis veikia tinkamai — paieškos metu gali būti nepripažinta sąsaja, kurioje veikia jutiklis, kol ARP talpykla dar nėra užpildyta. Patikimas sprendimas – praleisti paiešką ir aiškiai nurodyti adresą:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host` priima mDNS kompiuterio vardą arba IP adresą, visada nukreipia į reikiamą jutiklį ir yra rekomenduojama forma skriptams bei instaliacijoms be grafinės sąsajos. Grafinėje sąsajoje naudokite prisijungimo dialogo lango mygtuką „Atnaujinti“ ir leiskite atlikti pakartotinį nuskaitymo ciklą.

## Įrenginio nustatymai ir programinė įranga

Pats jutiklis saugo savo tinklo nustatymus: DHCP (gamyklinis nustatymas, kuris, jei DHCP serveris neatsako, persijungia į 169.254.x.x vietinį adresą) arba statinį adresą. Jis taip pat saugo nustatymą, ar įjungus maitinimą pradėti duomenų srautą. Šiuos nustatymus galite keisti GUI (jutiklio nustatymų meniu, **Tinklas**:**Keisti…**,**Transliuoti įjungus maitinimą**,**Perkrauti jutiklį**; žr. [skirtuką „DAQ“](gui.md)), arba per CLI

ir SDK

, naudodami komandas `pool-set-network`, `pool-set-auto-stream` ir `pool-reboot` (`DAQSensorSession.set_network()`, `set_auto_stream()`, `reboot()`; žr. [CLI

žinyną](../reference/cli-reference.md#daq-e-device-settings)). Jutiklis iš naujo paleidžiamas, kad būtų pritaikyti tinklo pakeitimai, o Chloros

vėl jį prijungia. Chloros

veikiančio kompiuterio tinkle turi būti nustatytas statinis adresas: Chloros

atmeta adresą, kurio negalėjo pasiekti po paleidimo iš naujo. Jutiklio ID ir jo `daq-e-<id>.local` kompiuterio vardas niekada nesikeičia. Nauja 1.3.0 versijoje.

**Programinės įrangos atnaujinimai yra integruoti į grafinę vartotojo sąsają (GUI).** Kai prijungtas „DAQ-E“ naudoja senesnę programinę įrangą nei ta, kuri yra pridedama prie jūsų Chloros

versijos, jo jutiklio eilutėje rodomas gintarinis piktogramos žymeklis **„Update Available“**, o nustatymų lange su ratuko piktograma pasirodo <version>mygtukas</version>

„Update to <version>“. Atnaujinimas per tinklą atsisiunčiamas per maždaug 30 sekundžių; jutiklis automatiškai paleidžiamas iš naujo ir vėl prisijungia, o nutrauktas perdavimas nepažeidžia esamos programinės įrangos. Naudodamiesi CLI arba SDK, taip pat Linux sistemoje, kurioje nėra GUI, paleiskite komandą „`chloros-cli daq pool-update-firmware --sensor-id <id>`“ (`DAQSensorSession.update_firmware()`).

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## PTP laiko sinchronizavimas

„DAQ-E“ programinė įranga v1.2.0+ dalyvauja IEEE 1588 PTPv2 standarte kaip įprastas (tik pavaldusis) laikrodis. **Chloros pagrindinio kompiuterio užkulisinė sistema yra PTP „grandmaster“** — kiekvienas „DAQ-E“ įrenginys ir kiekviena „LATTICE“ kamera vietiniame tinkle (LAN) veikia kaip jo pavaldiniai 0 domeno ribose, išlaikydami visų įrenginių laiko žymes su ~1 ms paklaida. Būtent šis bendras laikrodis leidžia suderinti DAQ rodmenų laiko žymes su kameros ekspozicijomis (žr. [Įrašymas ir .daq formatas](recording.md)).

Patikrinkite sinchronizaciją iš CLI:

| Komanda | Rodo |
| --- | --- |
| `chloros-cli time-sync status` | Pagrindinio serverio būsena, BMCA prioritetai, laikrodžio identifikatorius ir `health`: `OK` arba `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` su `health_detail`, nurodant, kodėl jo paketai nepasiekia tinklo |
| `chloros-cli time-sync peers` | Visi aptikti pavaldiniai (DAQ-E jutikliai + LATTICE kameros) |
| `chloros-cli time-sync cameras` | Kiekvienos kameros PTP būklė (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | „Grandmaster“ proceso paleidimas iš naujo |

Grafinėje vartotojo sąsajoje (GUI) DAQ-E nustatymų lange rodomas realaus laiko **PTP sinchronizacijos** eilutė su dabartine jutiklio PTP būsena.**„DAQ-E“ su išjungtu PTP lieka išjungtas.** Įrenginys savo PTP įjungimo/išjungimo nustatymą saugo savo atmintyje, todėl įrenginys, kurio PTP buvo išjungtas, lieka išjungtas po pakartotinio prisijungimo ar maitinimo išjungimo ir įjungimo, o jo laiko žymos nėra sinchronizuojamos pagal PTP. `chloros-cli daq pool-list` pažymi tokį įrenginį (`!! PTP is switched OFF on this unit …`) ir išspausdina komandą, kuri išsprendžia šią problemą:

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

Chloros niekada pats iš naujo neįjungia įrenginio, nes išjungimas gali būti tyčinis.

Išsami informacija vartotojams, kuriems reikalingas griežtas suderinimas:

* Kiekvienas transliuojamas datagrama turi žymių lauką; **2-asis bitas nustatomas rėmeliuose, kurių laiko žymos yra sinchronizuotos pagal PTP**. Duomenų srautai, kuriems reikalingas griežtas kameros ir duomenų surinkimo įrenginio (DAQ) suderinimas, turėtų būti filtruojami pagal tą bitą.
* Prieš sinchronizuotą duomenų surinkimą įsitikinkite, kad jutiklis rodomas `chloros-cli time-sync peers` ir kad `chloros-cli daq pool-list` nepažymi jo PTP kaip išjungto. (MAPIR vidinė tiesioginė aparatinė įranga taip pat gali filtruoti įrašymą pagal PTP sinchronizaciją, naudodama `--wait-ptp` žymę, kuri laukia iki 15 s, kol jutiklis pasiekia SLAVE būseną; ši įranga nėra įtraukta į pateiktą CLI.)
* Kol PTP aktyviai veikia kaip vergas, jutiklis atmeta rankinius laikrodžio sinchronizavimo bandymus („PTP teikia laikrodį“). Tai numatyta pagal projektą — pasitikėkite PTP.

## Pastabos apie Linux

* **PTP prievadai be „root“ teisių.** PTP priskiria UDP 319/320. `.deb` įrašo `/etc/sysctl.d/60-chloros-ptp.conf` (`net.ipv4.ip_unprivileged_port_start = 319`), todėl užkulisiai prisijungia prie jų, veikdami kaip jūsų vartotojas. Nuo 1.3.0 versijos paketas nenustato jokių failų teisių (`setcap`) užpakalinėje programoje ir nereikia `libcap2-bin`; patys nepridėkite teisių, nes tuomet išpakuota užpakalinė programa nepaleis. Jei PTP negali priskirti 319 prievado, žr. [Linux diegimas](../linux/linux-installation.md#ptp-will-not-start--cannot-bind-port-319).

* **„Headless“ „Jetson“ / Linux kompiuteris:** paketas įdiegia užkulisio paslaugas, bet nė vienos iš jų neįjungia. Kad PTP (ir DAQ prieinamumas) veiktų nuolat, net jei niekas nevykdo komandos, įjunkite paslaugą savo vartotojo vardu:

  ```bash
  sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
  ```

  Jei to nepadarysite, PTP veiks tik tol, kol kas nors paleis foninę paslaugą — komandą „`chloros-cli`“ arba SDK skriptą. Žr. [Linux diegimą](../linux/linux-installation.md#always-on-ptp-for-headless-hosts), kur rasite informaciją apie root vartotoją `chloros-backend.service` ir kaip iš jo išsijungti.

## Trikčių šalinimas: „Nerasta DAQ-E įrenginių“

| Patikrinimas | Aprašymas |
| --- | --- |
| Maitinimas | Jutiklyje nedega LED lemputė — patikrinkite jungiklio / įpurškimo prievado PoE ir ryšio indikatorius; palaukite keletą sekundžių po įjungimo |
| Transliacijos domenas | Kompiuteris ir jutiklis yra tame pačiame L2 segmente; mDNS nenukreipia srauto |
| Užkarda | Windows: diegimo programos taisyklė **„Chloros Backend (UDP-In)“** leidžia DAQ-E srautą (UDP 5002), mDNS (5353) ir PTP (319/320) praeiti; trečiosios šalies užkarda turi būti sukonfigūruota taip pat. Linux: „`.deb`“ atidaro šiuos prievadus „`ufw`“ / „`firewalld`“ tik tuo atveju, jei ugniasienė buvo aktyvi diegimo metu — vėliau įjungus ugniasienę, juos reikia leisti patys |
| Kompiuteris su keliais tinklo adapteriais | Atradimo funkcija naršo mDNS su ARP atsargine galimybe; `--eth-host <ip-or-hostname>` praleidžia atradimo etapą ir prisijungia tiesiogiai |
| Pakartotinis nuskaitymas per GUI | Atradimo funkcija veikia tik tol, kol atidarytas prisijungimo dialogas; naudokite „Atnaujinti“ |</version>
