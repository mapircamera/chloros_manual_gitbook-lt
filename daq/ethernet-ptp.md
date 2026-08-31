# DAQ-E tinklo konfigūracija ir laiko sinchronizavimas

> Fizinė jutiklio tinklo konfigūracija – kabelių montavimas, PoE, IP adresų priskyrimas ir paties įrenginio tinklo nustatymai – aprašyta **[DAQ vartotojo vadove](https://mapir.gitbook.io/daq/daq-e/network-setup)**. Šiame puslapyje aptariama Chloros pusė: prijungimas, laiko sinchronizavimas ir ką daryti, jei paieška neduoda rezultatų.

DAQ-E yra DAQ šeimos narys, veikiantis Ethernet tinkle: maitinamas per PoE, aptinkamas per mDNS (paslauga `_daq-e._tcp`) ir pasiekiamas pagal hostname, sudarytą iš jo jutiklio ID — `daq-e-<6 hex>.local`, pvz., `daq-e-def330.local`. Šiame puslapyje aprašoma, kaip jis perduoda duomenis tinkle ir kaip dalyvauja PTP laiko sinchronizavime.

## Perdavimo režimai

| Režimas | Galinis taškas | Vartotojai | Pastabos |
| --- | --- | --- | --- |
| **Daugiataškis** (numatyta) | UDP `239.10.10.10:5002` | Bet koks skaičius tame pačiame LAN tinkle gauna tą patį srautą | Kiekviena datagrama patvirtinama CRC-16/CCITT |
| **Raw** | TCP prievadas `5000` | Tiksliai vienas klientas (išskirtinis) | Suderinamas su DAQ-U baitų lygiu |

Chloros pagal numatytuosius nustatymus naudoja daugiaadresinį perdavimą, todėl GUI, CLI ir SDK vienu metu gali stebėti vieną jutiklį.

## Tinklo reikalavimai

* **Tas pats transliacijos domenas.** Kompiuteris, kuriame veikia Chloros, turi būti tame pačiame L2 tinklo segmente kaip ir jutiklis — mDNS atradimas neperžengia maršrutizatorių.
* **„Windows“ užkardos užklausa: sutikite.** Kai „Chloros“ pirmą kartą susieja daugiaadresinius lizdus, „Windows Defender“ vieną kartą paprašo leidimo. Leidimas apima DAQ-E duomenis (UDP 5002), mDNS (UDP 5353) ir PTP (UDP 319/320). Linux atveju tai vyksta tyliai.
* **PoE maitinimas, nėra būsenos šviesos diodo.** „DAQ-E“ neturi savo šviesos diodo — maitinimą patikrinkite per jungiklio arba įpurškimo prievado „link/PoE“ indikatorių ir po įjungimo palaukite keletą sekundžių, kol įrenginys įkels sistemą ir prisijungs prie tinklo.

## Prijungimas

**GUI:** Skirtukas „Šviesos jutikliai“ → „Prijungti jutiklį“ → Įrenginio tipas „DAQ-E (Ethernet)“. Aptikimas vyksta tik tol, kol ekrane rodomas prisijungimo dialogas (mDNS paieška ir ARP nuskaitymas „Windows“), kartojamas kas 15 sekundžių; paspaudus mygtuką „Atnaujinti“ nuskaitymas atliekamas iš karto. Aptikti jutikliai rodomi išskleidžiamajame meniu; automatiškai pasirenkamas pirmasis aptiktas jutiklis.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (veikia foninis procesas):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

### Kompiuteriai su keliais tinklo sąsajų moduliais ir pirmasis prisijungimas po paleidimo

Kompiuteriuose su daugiau nei viena aktyvia tinklo sąsaja **pirmasis** `pool-connect --eth` po paleidimo gali būti tuščias, net jei jutiklis veikia tinkamai — paieškos metu gali būti nepripažinta sąsaja, kurioje veikia jutiklis, kol ARP talpykla dar nėra užpildyta. Patikimas sprendimas – praleisti paiešką ir aiškiai nurodyti adresą:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host` priima mDNS kompiuterio vardą arba IP adresą, visada nukreipia į reikiamą jutiklį ir yra rekomenduojama forma skriptams bei instaliacijoms be grafinės sąsajos. Grafinėje vartotojo sąsajoje naudokite prisijungimo dialogo lango mygtuką „Atnaujinti“ ir leiskite atlikti pakartotinį nuskaitymą.

## Įrenginio nustatymai ir programinė įranga

Pats jutiklis saugo tinklo nustatymus — statinį IP adresą arba DHCP + vietinį adresavimą, įrenginio pavadinimą, automatinį srauto paleidimą paleidžiant sistemą, OTA slaptažodį. Šie įrenginio nustatymai nėra pateikiami kaip komandos pateiktame „CLI“; jie valdomi per „Chloros“ grafinę vartotojo sąsają, kur jie rodomi, arba naudojantis „MAPIR“ palaikymu.

**Programinės įrangos atnaujinimai yra integruoti į grafinę vartotojo sąsają.**Kai prijungtas „DAQ-E“ naudoja programinę įrangą, senesnę nei vaizdas, pridedamas prie jūsų „Chloros“ versijos, jo jutiklių eilutėje rodomas gintarinis piktogramos mygtukas**„Galimas atnaujinimas“**, o nustatymų lange su ratuko piktograma pasirodo<version>

mygtukas</version> „Atnaujinti į<version>

“. Atnaujinimas per tinklą atsisiunčiamas per maždaug 30 sekundžių; jutiklis automatiškai paleidžiamas iš naujo ir vėl prisijungia, o nutrauktas perdavimas nekeičia esamos programinės įrangos versijos.

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## PTP laiko sinchronizavimas

„DAQ-E“ programinė įranga v1.2.0+ dalyvauja IEEE 1588 PTPv2 standarte kaip įprastas (tik pavaldusis) laikrodis. **Chloros pagrindinio kompiuterio užkulisiai yra PTP „grandmaster“** — kiekvienas DAQ-E ir kiekviena „LATTICE“ kamera vietiniame tinkle (LAN) veikia kaip jo pavaldiniai 0 domeno ribose, išlaikydami visų įrenginių laiko žymes su ~1 ms paklaida. Būtent šis bendras laikrodis leidžia suderinti DAQ rodmenų laiko žymes su kamerų ekspozicijomis (žr. [Įrašymas ir .daq formatas](recording.md)).

Patikrinkite sinchronizaciją iš CLI:

| Komanda | Rodo |
| --- | --- |
| `chloros-cli time-sync status` | Pagrindinio kompiuterio „grandmaster“ būsena, BMCA prioritetai, laikrodžio identifikatorius |
| `chloros-cli time-sync peers` | Visi aptikti pavaldiniai įrenginiai (DAQ-E jutikliai + „LATTICE“ kameros) |
| `chloros-cli time-sync cameras` | Kiekvienos kameros PTP būklė (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | Grandmaster proceso paleidimas iš naujo |

GUI aplinkoje DAQ-E nustatymų lange rodoma tiesioginė **PTP Sync** eilutė su dabartine jutiklio PTP būsena.

Išsami informacija vartotojams, kuriems reikalingas griežtas suderinimas:

* Kiekvienas perduodamas datagramas turi žymių lauką; **2-asis bitas nustatomas rėmeliuose, kurių laiko žyma yra sinchronizuota su PTP**. Duomenų srautai, kuriems reikalingas griežtas kameros ir DAQ suderinimas, turėtų būti filtruojami pagal šį bitą.
* Prieš sinchronizuotą duomenų surinkimą įsitikinkite, kad jutiklis rodomas sąraše „`chloros-cli time-sync peers`“. (MAPIR vidinis tiesioginis aparatinės įrangos įrankis taip pat gali filtruoti įrašymą pagal PTP sinchronizaciją naudodamas `--wait-ptp` žymę, kuri laukia iki 15 s, kol jutiklis pasieks SLAVE būseną; ta įranga nėra įtraukta į pristatytą CLI.)
* Kol PTP aktyviai veikia kaip pavaldusis, jutiklis atmeta rankinius laikrodžio sinčių siuntimus („PTP teikia laikrodžio signalą“). Tai numatyta konstrukcijoje — pasitikėkite PTP.

## „Linux“ pastabos

* **PTP diegimo metu reikalingas „`libcap2-bin`“.** „`.deb`“ postinst suteikia „`cap_net_bind_service=+ep`“ teisę „`/usr/lib/chloros/chloros-backend`“, kad jis galėtų priskirti PTP prievadus 319/320 be „root“ teisių. Jei trūksta „`libcap2-bin`“, šis žingsnis praleidžiamas ir PTP nepavyks paleisti. Sprendimas:

  ```bash
  sudo apt install libcap2-bin
  sudo apt reinstall chloros
  ```

* **„Headless“ Jetson / „Raspberry Pi“:** pirmą kartą įdiegiant generuojamas „systemd“ modulis „`chloros-backend.service`“, tačiau jis neįjungiamas. Kad PTP (ir DAQ prieinamumas) veiktų nuolat be grafinės vartotojo sąsajos:

  ```bash
  sudo systemctl enable --now chloros-backend.service
  ```

  Be jo PTP veikia tik tol, kol atidaryta „Chloros“ GUI.

## Problemų sprendimas: „Nerasta DAQ-E įrenginių“

| Patikrinimas | Aprašymas |
| --- | --- |
| Maitinimas | Jutiklyje nedega LED lemputė — patikrinkite jungiklio / įpurškimo prievado PoE ir ryšio indikatorius; palaukite kelias sekundes po įjungimo |
| Transliacijos sritis | Pagrindinis kompiuteris ir jutiklis yra tame pačiame L2 segmente; mDNS nenukreipia |
| Windows ugniasienė | Pirmą kartą paleidžiant sutikite su „Defender“ prašymu (UDP 5002, 5353, 319/320) |
| Kompiuteris su keliais tinklo adapteriais | Pirmasis aptikimas po paleidimo gali nepranešti apie jutiklį — prisijunkite naudodami `--eth-host <ip-or-hostname>` |
| Pakartotinis GUI nuskaitymas | Aptikimas vyksta tik tol, kol atidarytas prisijungimo dialogas; naudokite „Atnaujinti“ |</version>
