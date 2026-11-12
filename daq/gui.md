# Chloros skirtukas „DAQ“

Skirtukas „DAQ“ — Chloros šoninėje juostoje pažymėtas kaip **Šviesos jutikliai** — yra [DAQ-U, DAQ-M ir DAQ-E šviesos jutiklių](README.md) valdymo sąsaja realiuoju laiku: prijunkite jutiklius per bet kurį duomenų perdavimo protokolą, stebėkite kalibruotus spektrus realiuoju laiku, apskaičiuokite atspindžio koeficientą iš jutiklių poros ir įrašykite `.daq` failus tiesiai į savo projektą.

Šis skirtukas tampa prieinamas, kai tik baigiasi Chloros užkulisio paleidimas. Skirtuko diagramos atnaujinamos per „Chloros“ DAQ paslaugą, naudojant tiesioginį ryšį, kuris, jei nutrūksta, automatiškai atsinaujina (2–10 s pertrauka); kol paslauga nepasiekiama, jutiklio būsenos eilutėje rodomas užrašas **„No Server“**.

Išdėstymas susideda iš **jutiklių šoninės juostos**(po vieną eilutę kiekvienam prijungtam jutikliui) ir**diagramų srities** (po vieną diagramos langelį kiekvienam jutikliui arba grupei).

<!-- SCREENSHOT-NEEDED: full DAQ (Light Sensors) tab in list view with one DAQ-E connected — sensor sidebar on the left (Connect Sensor + Record All buttons, one sensor row), spectrum chart with rainbow fill in the main area, live data table below the chart -->

***

## Jutiklio prijungimas

Spustelėkite **„Prijungti jutiklį“** šoninės juostos viršuje. Pagrindinėje srityje atsidaro prijungimo dialogo langas (arba kaip užklotas, jei pridedamas kitas jutiklis – tokiu atveju pasirodo mygtukas „Atšaukti“).

| Valdymas | Veikimas |
| --- | --- |
| **Įrenginio tipas** | `DAQ-U (USB)` (numatyta reikšmė), `DAQ-M (Bluetooth)` arba `DAQ-E (Ethernet)`. Pakeitus tipą, vėl pradedamas naujai pasirinktos perdavimo rūšies nuskaitymas. |
| **Prievadas / BLE įrenginys / Prieglobos vardas / IP** | Rodo aptiktus įrenginius kaip „`device - description`“; automatiškai pasirenkamas pirmasis įrašas, atpažintas kaip jutiklis. Skenavimo metu rodomas `Scanning...` (USB), `Scanning (N)...` su 8 sekundžių atgaline skaičiavimo juosta (BLE) arba `Discovering ethernet sensors (N)...` su 5 sekundžių atgaline skaičiavimo juosta (Ethernet). Tušti rezultatai rodomi kaip „`No ports`“ / „`No BLE devices`“ / „`No ethernet sensors found`“. |
| **↻ Atnaujinti** | Nedelsiant pakartotinai nuskaito pasirinktą perdavimo būdą (išjungta BLE/Ethernet nuskaitymo metu). |
| **Prijungti jutiklį** | Įjungiama, kai pasirenkamas įrenginys; kol užmezgamas ryšys, pavadinimas pakeičiamas į „`Connecting...`“. |

Paieška vyksta tik **kol ekrane rodomas prisijungimo dialogas** ir kartojasi kas 15 sekundžių tik pasirinktam perdavimo būdui — vien tik atidarius skirtuką nuskaitymas nevyksta. Jei nepavyksta prisijungti, dialoge rodomas pranešimas: *„Prisijungti nepavyko. Pabandykite atjungti ir vėl prijungti jutiklį, tada vėl spustelėkite „Prisijungti“.“*

Šoninė juosta atsidaro automatiškai, kai prisijungia pirmasis jutiklis.

{% hint style="info" %}
**„DAQ-E“ nerodomas?**„DAQ-E“ neturi būsenos šviesos diodo — patikrinkite PoE/ryšio indikatorių komutatoriuje arba įterpimo prievade, į kurį jis yra prijungtas, ir po įjungimo palaukite keletą sekundžių, kol jis įsikraus. Chloros įrenginys turi būti tame pačiame transliacijos domene (mDNS neperžengia maršrutizatorių). Diegimo programos**Chloros užkardos (UDP-In)** taisyklė leidžia mDNS (UDP 5353), DAQ-E duomenis (UDP 5002) ir PTP (UDP 319/320); trečiosios šalies ugniasienėje taip pat turi būti leidžiami tie patys prievadai. Du DAQ-E įrenginiai viename LAN tinkle aptinkami atskirai, kiekvienas su savo „`daq-e-<id>.local`“ kompiuterio vardu.
{% endhint %}

<figure><img src="../.gitbook/assets/v120-daq-device-type.png" alt=""><figcaption>Įrenginio tipas siūlo DAQ-U (USB), DAQ-M (Bluetooth) ir DAQ-E (Ethernet)</figcaption></figure>

***

## Jutiklių šoninė juosta

Kiekvienam prijungtam jutikliui skiriama viena eilutė (plius viena eilutė kiekvienai „Ambient+Object“ grupei). Eilutes galima perkelti ir pertvarkyti, o jų tvarka taip pat keičia diagramų plytelių išdėstymą. Spustelėkite eilutę, kad tas jutiklis ar grupė taptų aktyvia diagrama sąrašo peržiūroje.

| Elementas | Reikšmė |
| --- | --- |
| Spalvota kairioji riba | Jutiklio diagramos spalva. |
| Transporto ženkliukas | `DAQ-U` / `DAQ-M` / `DAQ-E` arba žalia `REF` žymė, skirta „Ambient+Object“ atspindžio grupei. |
| Įrenginio pavadinimas | Pagal numatytuosius nustatymus – jutiklio serijos numeris (jo pastovus identifikatorius kalibravimui, `.daq` failų pavadinimams ir importo atitikimui); pasirinktiniai pavadinimai išlieka kiekviename projekte. |
| **Kalibruota** piktograma (žalia) | Rodoma, kai įkeltas gamintojo kalibravimo rinkinys, t. y. spektrai yra tikslūs W/m²/nm. |
| **Galimas atnaujinimas** piktograma (gintarinė, tik DAQ-E) | Veikianti programinė įranga yra senesnė nei vaizdas, pridedamas prie šios Chloros versijos. Atnaujinimo metu rodoma tiesioginė pažanga (`Flashing… N%`, `Restarting sensor…`, tada `Updated X → Y` arba `Failed`). |
| Akis | Įjungia arba išjungia šio jutiklio matomumą diagramoje. |
| Ratukas | Atidaro atskirų jutiklių nustatymų langą (žemiau). |
| ✕ (raudonas) | Atjungia jutiklį arba pašalina „Ambient+Object“ grupę. |

Virš eilučių yra du mygtukai:

* **Prijungti jutiklį** — atidaro prijungimo dialogą (veikimo metu pervadinamas į „`Connecting...`“).
* **Įrašyti viską / Sustabdyti viską**— pradeda arba sustabdo „`.daq`“ įrašymą**kiekviename**prijungtame jutiklyje. Reikalingas bent vienas jutiklis**ir atidarytas projektas** (patarimas: „Atidarykite projektą, kad galėtumėte įrašyti“); kol vyksta bet koks įrašymas, mygtukas tampa raudonas.

Tuščioje būsenoje rodomas užrašas „Jutiklių neprijungta“.

<!-- SCREENSHOT-NEEDED: sensor sidebar with three rows — a DAQ-E showing both the green Calibrated pill and the amber Update Available pill, a DAQ-U row, and a green REF group row — plus the Connect Sensor and Record All buttons -->

***

## Kiekvieno jutiklio nustatymai (dantukų meniu)

Atidarykite paspaudę ratuko piktogramą jutiklio eilutėje. Turinys eilės tvarka:

* **Informacinės eilutės** — Įrenginio tipas (DAQ-U/M/E), Ryšys (`Serial (USB)` / `Bluetooth` / `Ethernet`), prievadas (COM prievadas, BLE adresas arba pagrindinis kompiuteris) ir serijinis numeris.
* **Kalibravimo ataskaita: atsisiųsti** — atsisiunčia šio įrenginio NIST atsekamą kalibravimo sertifikatą (PDF) ir atidaro jį jūsų PDF peržiūroje. Funkcija prieinama, kai žinomas serijinis numeris; sertifikatas įrašomas į talpyklą pirmojo prisijungimo metu.
* **Įrenginio pavadinimas** — spustelėkite pieštuką, kad pakeistumėte pavadinimą; išlieka kiekvienam projektui.
* **Grafikos linijos spalva** — spalvų pavyzdžiai; išlieka kiekvienam projektui.
* **Integracijos laikas (ms)**— slankiklis + skaičius,**1–500 ms**, numatytasis**32 ms**. Neveikia, kai AE yra įjungta.
* **Kadrų vidurkis**— slankiklis + skaičius,**1–50 kadrų**, numatytasis**20**.
* **AE: ĮJUNGTA/IŠJUNGTA**— automatinės ekspozicijos perjungimas; prisijungus**numatyta ĮJUNGTA**. Išjunkite, jei norite integracijos laiką nustatyti rankiniu būdu.
* **Sustabdyti transliaciją / Pradėti transliaciją** — sustabdykite arba atnaujinkite tiesioginę transliaciją.
* **Įrašyti / Sustabdyti įrašymą** — įrašymas pagal kiekvieną jutiklį `.daq` (reikia atidaryto projekto).
* **Cap** — „cap“ korekcijos profilis (kitas skyrius).
* **Tiesioginės informacijos eilutės** — integracijos laikas (ms), kadrų dažnis (FPS), mėginiai, įrašymas (raudona `REC` arba `Off`) ir būsena (`Streaming` / `Paused` / `SATURATED` / `No Server`).

### Tik DAQ-E: tinklo, programinės įrangos ir PTP eilutės

* **Pagrindinio kompiuterio vardas / IP** — dabartinis įrenginio adresas.
* **Programinė įranga** — dabartinė programinės įrangos versija, taip pat veiksmų langelis:<version\>

mygtukas</version\>

**Atnaujinti į \<version\>** pasirodo, kai ši Chloros versija apima naujesnį „DAQ-E“ programinės įrangos atvaizdą. Atnaujinimas per tinklą įvyksta per maždaug 30 sekundžių; jutiklis automatiškai paleidžiamas iš naujo ir vėl prisijungia, o nutrauktas perdavimas palieka dabartinę programinę įrangą nepakitusią. Atnaujinimo eiga rodoma realiuoju laiku (`Flashing… N%` → `Restarting sensor…` → `Updated X → Y`), o langelyje rodomas `Up to date`, kai atnaujinimas vyksta.
* **PTP sinchronizavimas** — tiesioginė PTP būsena (grįžta prie `unknown`). DAQ-E programinė įranga v1.2.0+ dalyvauja IEEE 1588 PTPv2 standarte tik kaip pavaldusis laikrodis; Chloros pagrindinio kompiuterio užkulisiai yra PTP „grandmaster“, o kiekvienas DAQ-E ir „LATTICE“ fotoaparatas vietiniame tinkle (LAN) veikia kaip jo pavaldinys 0 domeno ribose, išlaikydamas laiko žymes su maždaug 1 ms tikslumu.
* **Tinklas**— adresas, kuriuo veikia įrenginys, ir kaip jis jį gavo (DHCP, „link-local“ arba statinis), taip pat išsaugotas nustatymas, kuris taikomas po perkrovimo.**Keisti…**atidaro formą:**Automatinis (DHCP)**arba**Statinis IP**su IP adresu, potinklio kauke ir pasirinktiniu šliuzu.**Taikyti ir paleisti iš naujo** išsaugo nustatymus, iš naujo paleidžia jutiklį ir vėl jį prijungia. Statinis adresas, esantis už šio kompiuterio tinklo ribų, atmetamas, nes Chloros po perkrovimo negalėtų pasiekti jutiklio; tuomet pasirodo langelis, leidžiantis tai padaryti vis tiek, jei kompiuteris bus peradresuotas arba nukreiptas į tą tinklą. **Transliuoti įjungus maitinimą**nustato, ar jutiklis pradės transliuoti iškart, kai bus įjungtas maitinimas, dar prieš prisijungiant bet kuriai programinei įrangai.**„Restart sensor“** (Perkrauti jutiklį) – perkrauna jutiklį ir vėl prijungia jį su esamais nustatymais. Perkrovimas neleidžiamas, kol jutiklis atlieka įrašymą. Nauja funkcija versijoje 1.3.0.

„Ambient+Object“ grupės atveju dantukų meniu rodomi tik grupės šaltinio jutikliai, įrenginio pavadinimas ir grafiko linijos spalva.

<!-- SCREENSHOT-NEEDED: per-sensor settings modal for a DAQ-E — info rows, Calibration Report Download, Hostname/IP + Firmware row with an "Update to <ver>" button, PTP Sync row, Integration Time / Frame Average sliders, AE ON toggle, and the Cap dropdown all visible (scrolled composite acceptable) -->



### Dangtelio pasirinkimas

Išskleidžiamajame meniu **Dangtelis** nurodoma Chloros, koks fizinis dangtelis uždėtas ant jutiklio difuzoriaus, ir kiekvienam spektrui pritaikomas to dangtelio gamykliniu būdu išmatuotas korekcijos profilis. Pasirinkimai priklauso nuo modelio:

| Modelis | Dangtelio pasirinkimai |
| --- | --- |
| DAQ-U | Nėra (nuogas jutiklis), FOV 15°, FOV 30°, FOV 45°, FOV 60°, FOV 90°, „Sunshine“ (kosinusinis korektorius) |
| DAQ-M | Nėra (atviras jutiklis), „Sunshine“ (kosinusinis korektorius) |
| DAQ-E | Nėra (atviras jutiklis), FOV 15°, FOV 45°, FOV 90°, „Sunshine“ (kosinusinis korektorius) |

**Kiekvieno modelio numatytasis nustatymas yra „Sunshine“ (kosinusinis korektorius)** — MAPIR kiekvieną DAQ pristato su įmontuotu „Sunshine“ dangteliu, ir tai yra standartinė lauko konfigūracija: 180° pusrutulio matymo kampas su kosinusine paklaida ≤ ±4 % iki 60° ir ≤ ±4,5 % iki 70° (nerekomenduojama, kai saulės aukštis mažesnis nei ~15°), silpninimas pagal konstrukciją (~12×). Jūsų pasirinkimas išlieka projekte.

{% hint style="warning" %}
**Dangtelio pasirinkimas turi atitikti fiziškai uždėtą dangtelį.**Nei jutiklis, nei programinė įranga negali nustatyti, koks dangtelis yra uždėtas. Pasirinkimas lemia tiek realaus laiko korekciją, tiek žymą, įrašomą į kiekvieną `.daq` failą — atsižvelgiant į „Sunshine“ dangtelio ~12× silpninimą, nedeklaruotas dangtelio pakeitimas spektrus koreguoja netiksliai maždaug tuo koeficientu. (Nuimant ir vėl uždedant tą patį dangtelį, paklaida siekia apie 1,5 %.) Pasirinkite**„None“ (nuogas jutiklis)** tik tada, kai dangtelis fiziškai nuimtas; „DAQ-E“ atveju pasirinkus „None“ vis tiek taikomas gamyklinis geometrijos profilis įdubusiam stikliniam difuzoriui – tai nėra neveikianti funkcija – o „nuogas“ „DAQ-E“ yra laboratorinė konfigūracija, o ne palaikoma lauko konfigūracija.
{% endhint %}

{% hint style="info" %}
Atnaujinimas iš ankstesnės versijos: naršyklės pusėje esantis 1.1.0 versijos perjungiklis „Sunshine Diffuser Installed“ (Įdiegtas „Sunshine“ difuzorius) buvo pašalintas. Dabar dangtelio tvarkymas vykdomas pagal šį kiekvienam jutikliui skirtą dangtelio profilį, taikomą serverio pusėje.
{% endhint %}

***

## Diagramų sritis

Viršutinėje fiksuotoje juostoje yra **perjungiklis „sąrašas ⇄ tinklelis“**ir slankiklis**„Diagramos mastelis“** (plytelių dydis 200–2000 pikselių). Jei yra daugiau nei viena diagramų grupė, rodinys automatiškai persijungia į tinklelio vaizdą, o jei yra viena ar mažiau grupių – grįžta į sąrašo vaizdą. Peržiūros režimas ir diagramos dydis išlieka tokie patys kiekvienam projektui.

Kiekvieno jutiklio **spektro diagrama** rodo:

* **X ašis** — bangos ilgis (nm). Jutiklio tinklelis yra 340–1010 nm su 5 nm žingsniu (135 taškai), o rodymui interpoliuojamas iki 1 nm.
* **Y ašis**— kalibruotam jutikliui (žalia**Kalibruota**piktograma),**Galia (W/m²/nm)**— radiometriškai kalibruotas spektrinis spinduliavimo intensyvumas visais trimis matavimo vienetais — su automatiniu SI priešdėliu (m/µ/n), parinktu pagal didžiausią vertę. Jutiklis, kurio kalibravimo rinkinys neįkeltas, rodo neapdorotus matavimo duomenis kaip**Intensyvumą (skaičių)**, nesuskalūtu. Kai diagramoje sutampa abu tipai, galia rodoma kairėje ašyje, o skaičiai – dešinėje ašyje.
* Vienos kreivės apačioje – vaivorykštinis spektrinis užpildymas; keli jutikliai vienoje diagramoje sutampa kaip spalvotos linijos su prislopintu užpildymu.
* **Pele užvedus**— vertikalus žymeklis su bangos ilgiu ir kiekvieno jutiklio verte (W/m²/nm kalibruotam jutikliui, kitais atvejais — bendras skaičius);**patempkite**, kad priartintumėte (priartinus pasirodo sumažinimo mygtukas).
* Mygtukas **+** (tik tinklelinio vaizdo režime) – skirtas jutikliui pridėti prie šio grafiko arba sukurti grupę (žr. žemiau).
* Įrenginio pavadinimas išcentruotas viršuje, o kol gaunamas pirmasis kadras, rodomas sukamasis ratukas.

**Perkrovimas** pačiame grafike nepažymėtas: perkrautas jutiklis rodo raudoną `SATURATED` būsenos tekstą ir raudoną `Saturated: Yes` eilutę tiesioginių duomenų lentelėje. Norėdami jį išvalyti, sumažinkite integravimo laiką arba vėl įjunkite AE.

<!-- SCREENSHOT-NEEDED: grid view with at least two chart tiles visible, the Chart Zoom slider and list/grid toggle in the top bar, and the "+" add-sensor button visible on one tile -->



***

## Tiesioginių duomenų lentelė (sąrašo peržiūra)

Po diagramos sąrašo peržiūros režimu, atnaujinama kas 500 ms:

* **Visi modeliai**: Šviesos spalvos pavyzdys (sRGB pagal CIE XYZ), Pasotintas (Taip/Ne), CIE 1931 X/Y/Z, Chromatinumas x/y, CIE u′/v′, CCT (K), CRI (Ra), dominuojantis bangos ilgis (nm), didžiausias bangos ilgis (nm), sužadinimo grynumas, Duv, CIE L\*/a\*/b\* ir Munsell H/V/C.
* **Tik kalibruoti jutikliai**(bet kuris iš DAQ-U / DAQ-M / DAQ-E, kai įkeltas gamyklinis kalibravimo rinkinys — tai rodo žalia žymė**Kalibruotas** jutiklio eilutėje): Bendra galia (W/m²), fotopinis liuksas (lx), skotopinis liuksas (lx), S/P santykis, PPFD ir PPFD Red /Green /Blue (µmol/m²/s), taip pat opiniai spinduliavimo stiprumai — S-konas, melanopinis, rodopinis, M-konas, L-konas (visi W/m²).

<!-- SCREENSHOT-NEEDED: list view live data table for a DAQ-E showing both the colorimetric rows and the power-calibrated rows (Total Power, Photopic/Scotopic Lux, PPFD, opic irradiances) -->

***

## Atspindžio grupės (Aplinkos + objekto)

Du sujungti jutikliai gali būti sujungti į realaus laiko atspindžio rodinį — be kameros:

1. Tinklelinio vaizdo režime spustelėkite **+**ant diagramos plytelių ir pasirinkite**Sujungti aplinkos + objekto**.
2. Pasirinkite **Aplinkos šviesos šaltinio**jutiklį ir**Objekto skaitytuvo**jutiklį (du skirtingus jutiklius), tada spustelėkite**Sukurti**.

Chloros apskaičiuoja R(λ) = objektas(λ) / aplinka(λ) kiekvienam bangos ilgiui iš dviejų tiesioginių srautų (0, kai aplinka ≤ 0). Grupės pavadinimas priklauso nuo jutiklių kalibravimo klasės:

* Abu jutikliai kalibruoti (paketas įkeltas) → **„Matomas atspindžio koeficientas“**.
* Jei bent vienas jutiklis nekalibruotas → **„Santykinis atspindžio koeficientas“**.

Grupė šoninėje juostoje rodoma kaip žalia eilutė „`REF`“, taip pat turi savo diagramą (vaivorykštės spalvos užpildymas, verčių rodymas iki 4 skaičių po kablelio, mastelio keitimas vilkdami).

Meniu **+**taip pat siūlo parinktį**Pridėti naują jutiklį** su trimis išdėstymo variantais: *Sujungti naują jutiklį* (pridėti prie šio diagramos), *Perkelti esamą jutiklį čia* arba *Peržiūrėti naują jutiklį* (atskiras diagramos langas).

<!-- SCREENSHOT-NEEDED: the "+" add-sensor overlay open on a chart tile showing the menu (Add New Sensor / Combine Ambient + Object / Cancel), and the Ambient + Object sub-dialog with its two sensor selects -->



### Augmenijos indekso lentelė

Sąrašo peržiūroje po atspindžio grupės diagramos pateikiama augmenijos indekso lentelė, apskaičiuota remiantis realiuoju atspindžiu juostų centruose **mėlyna 450 / žalia 550 / raudona 670 / NIR 800 nm** (reikšmės iki 4 skaičių po kablelio, `---`, kai neįmanoma apskaičiuoti; užveskite pelę ant indekso pavadinimo, kad pamatytumėte jo pilną pavadinimą):

* **Visada rodoma** (nepriklausoma nuo masto, bet koks jutiklių derinys): NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR.
* **Tik tada, kai abu jutikliai yra kalibruoti pagal galią** (įkelti abu paketai): EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI.

<!-- SCREENSHOT-NEEDED: an Ambient+Object reflectance group in list view — reflectance chart labeled "Apparent Reflectance" with the vegetation index table below it showing live NDVI etc. -->

***

## `.daq` failų įrašymas

* Įrašymui reikalingas **atviras projektas** — priešingu atveju tiek „Įrašyti viską“ (šoninėje juostoje), tiek atskirų jutiklių „Įrašyti“ mygtukai yra išjungti.
* Failai įrašomi į **`<project folder>/light_sensor/`**; failų pavadinimuose nurodytas jutiklio ID ir laiko žyma, o įrašymo metu išsaugomas įrenginio pavadinimas.
* Kai įrašymas sustabdomas („Stop“, „Stop All“ arba atsijungus įrašymo metu), užbaigtas `.daq` failas **automatiškai pridedamas prie atidaryto projekto** — jis atsiranda projekto failų sąraše be jokio rankinio pridėjimo, paruoštas naudoti kaip žemyn nukreiptų spindulių duomenys [atspindžio apdorojimui](README.md).
* Įrašymo metu nustatymų lango tiesioginėse eilutėse rodomas raudonas `REC` indikatorius.

Norint gauti kiekybinius spinduliavimo stiprio rodiklius, reikia apskaičiuoti bent 15 sekundžių duomenų vidurkį — tai prietaiso charakteristika, o ne gedimas.

<!-- SCREENSHOT-NEEDED: recording in progress — sidebar Stop All button in its red state and the settings modal live rows showing Recording: REC -->



***

## Daugialypės jutiklių išdėstymo schemos ir projekto išsaugojimas

* Sujunkite kelis jutiklius viename grafike (bendros ašys), išlaikykite atskirus grafikus (automatinis tinklelio išdėstymas), perkelkite jutiklius iš vieno grafiko į kitą, vilkdami pertvarkykite eiles/plyteles ir paslėpkite atskirus jutiklius naudodami akies mygtuką.
* Kiekvienam projektui Chloros išsaugo: įrenginių pavadinimus, grafikų spalvas, diagramos dydį, peržiūros režimą ir kiekvieno jutiklio nustatymus (integravimo laiką, kadrų vidurkavimą, AE būseną, dangtelio pasirinkimą).
* **Atidarius projektą iš naujo, jo jutikliai automatiškai vėl prisijungia** pagal adresą – COM prievadą „DAQ-U“ atveju, BLE įrenginį „DAQ-M“ atveju, mDNS hosto vardą DAQ-E atveju (nustatomas net jei įrenginio IP adresas pasikeitė) — ir vėl pritaiko kiekvieno jutiklio išsaugotą „cap“ profilį, kadrų vidurkavimą, AE būseną ir rankinį integracijos laiką.

***

## Kameros suporavimas (DLS)

Nereikia nieko suporuoti. Skirtingai nuo dronų DLS darbo eigos, kurioje šviesos jutiklis su kamera susiejamas iš anksto, Chloros suderina DAQ duomenis su vaizdais vėlesniame etape: importavimo/apdorojimo metu „`.daq`“ rodmenys interpoliuojami pagal kiekvieno kadro ekspozicijos laiko žymą. Įrašykite naudodami bet kurį prijungtą jutiklį (`.daq` automatiškai patenka į projektą), o atspindžio apdorojimas pagal laiką suranda reikiamus rodmenis — kaip naudojami žemyn nukreipti duomenys, žr. [DAQ šviesos jutikliai](README.md). **Tiesioginių**LATTICE įrašų atveju vietoj to pasirinkite jutiklį skirtuke „Kameros“ esančiame išskleidžiamajame meniu**Šviesos jutiklis**; tuomet kiekvienas įrašas išsaugo naudotą rodmenį, susietą tik su to įrašo kameromis (žr. [Atspindžio darbo eigos](reflectance.md)).</version\>
