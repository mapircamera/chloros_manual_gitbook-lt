# Kameros nustatymai

Skirtukas **„Kameros“** yra „Chloros “ tiesioginio valdymo sąsaja, skirta „LATTICE“ kameroms: pagrindinė vaizdo srauto sritis, kurioje kiekviena prijungta kamera rodoma kaip tiesioginio vaizdo langelis, ir šoninė juosta, kuria galima perjungti tris puslapius – **kamerų sąrašą**,**nustatymų langą**(kiekvienos kameros, masyvo arba įrašymo nustatymai – po vieną), ir**indekso skaičiuoklė**. Šiame puslapyje aprašomi visi kameros sąrašo, kiekvienos kameros nustatymų lango ir masyvo nustatymų lango valdikliai. Įrašymo režimai, eksporto tipo pasirinkimas ir „Capture All“ srautas aprašyti gretimame puslapyje [Įrašymo nustatymai ir režimai](capture.md).

Skirtukas „Kameros“ pasirodo šoninėje juostoje, kai Chloros užkulisinė sistema yra pasirengusi. Visi žemiau esantys valdikliai bendrauja su vietine užkulisine sistema per `127.0.0.1:5000`; pakeitimai iš karto taikomi tiesioginiam kameros vaizdui, jei nenurodyta kitaip.

## Šiame puslapyje naudojami kamerų tipai

Valdymo elementai rodomi arba paslepiami priklausomai nuo to, kokio tipo kamera yra pasirinkta. Vadove visur naudojami šie terminai:

| Terminas | Reikšmė | Filtro kanalai |
| --- | --- | --- |
| **RGB kamera** | „LATTICE M3C“ su FRGB filtru (modelyje yra `-FRGB`) | Red / Green / Blue |
| **Bayer daugiaspektrinė** | „LATTICE M3C“ su FRGN, FOCN arba FNGB | FRGN: Red / Green / NIR · FOCN: Orange / Cyan / NIR · FNGB: NIR / Green / Blue |
| **Mono (M3M)** | „LATTICE M3M“ — vienas siaurajuostis filtras, viena kalibruota juosta | Viena juosta |
| **Masyvo narys** | Kamera, prijungta kaip sinchronizuoto masyvo dalis (bendras arba atskiras ekranas) | Pagal jos filtrą |

RGB kameroms taikomas fotometrinis apdorojimas (baltos spalvos balansas, spalvų profiliai, gama); daugiaspektrinėms ir mono kameroms taikoma radiometrinė grandinė, o fotometriniai parametrai praleidžiami. Masyvo nariai perduoda srauto lygio nustatymus (pikselių formatą, skiriamąją gebą, pikselių sujungimas, trigeris, kadrų dažnis) masyvui — šios eilutės tampa tik skaitymo režimu atskirų kamerų lange ir perkeliama į masyvo nustatymų langą.

## Pagrindinė vaizdo srauto sritis

<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->



Jei kameros neprijungtos, vaizdo srauto srityje rodomas pradinis ekranas **„Prijunkite kamerą, kad galėtumėte pradėti“**su dviem mygtukais:**Prijungti kamerą**(žalias, atidaro vienos kameros prijungimo dialogą) ir**Prijungti masyvą** (mėlynas, atidaro masyvo prijungimo dialogą). Patys prijungimo dialogai aprašyti skyriuje [Kamerų prijungimas](connecting.md); masyvo sąvokos (sinchronizavimas, lygiai, pralaidumas) – skyriuje [Daugiakameriniai masyvai](arrays.md). Atidarius išsaugotą projektą, kuriame yra kamerų, pradžios ekrane vietoj to rodomas sukasi ratas su užrašu „Iš naujo atidaroma N išsaugotų kamerų…“, kol Chloros atkuria srautus iš paskutinės sesijos.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Viršutinė juosta

| Valdymo elementas | Ką daro |
| --- | --- |
| **Perjungimas tarp peržiūros režimų**| Perjungia tarp**tinklelinio vaizdo**(visos plytelių langeliai) ir**sąrašo vaizdo**(visos eilutės užima visą plotį: kiekviena sujungta matrica kaip viena sudėtinė eilutė, kiekvienas**atskiros** matricos elementas kaip atskira eilutė su savo srautu ir kalibravimo taikinio lentele, o po jais – VIENA atskira kamera – aktyvioji). Pagalbinės užuominos: „Perjungti į tinklelio peržiūrą“ / „Perjungti į sąrašo peržiūrą“. |
| **Tinklelio fiksavimas**(spynelė) | Numatytasis nustatymas –**užfiksuotas** — plytelės nejudamos. Atrakinkite, kad galėtumėte vilkti ir perrikiuoti plyteles į bet kurią vietą (tarpai išlieka). Tinklelis automatiškai vėl užrakinamas kiekvieną kartą, kai prisijungia nauja kamera. Įrankio patarimai: „Atrakinėti tinklelį (įjungti plytelių vilkimą)“ / „Užrakinti tinklelį (įšaldyti plyteles vietoje)“. |
| **Transliacijos mastelio** slankiklis | Plytelių dydis – nuo 60 pikselių iki viso konteinerio pločio. Ląstelės išlaiko 4:3 kraštinių santykį. Jei langelio plotis mažesnis nei 200 px, pavadinimas ir kadrų per sekundę (fps) antraštės paslepiamos, kad plytelė atrodytų tvarkinga. |

### Transliacijos plytelės

Kiekviena kamera atkuria sudėtinę tiesioginę plytelių; kamera taip pat gali rodyti tris pilkos skalės **pagal kanalus suskirstytas** plyteles (žr. [Kanalų suskirstymas](#display-overlays-drawn-over-the-live-feed)), o matricos rodo sujungtą plytelių vaizdą. Aktyvioje plytelių vaizde matomas pasirinkimo žiedas kameros (arba matricos) spalva.

Užvedus pelę ant plytelių vaizdo, pasirodo uždarymo mygtukas **X**:

* Uždarius **sudėtinę** plytelių, kai jos kanalų padalijimai lieka rodomi, tiesiog paslėpiama sudėtinė plytelių.
* Uždarius **paskutinę matomą atskirą kameros plytelių**, ta kamera atjungiama.
* **Sudėtinio masyvo narių padalijimo plytelių niekada neatjungia** kameros — jos tik paslepiamos.

Atrišus tinklelį, bet kurią plytelių galite vilkti į bet kurią vietą; išdėstymas išsaugomas kartu su projektu.

## Šoninė juosta — kamerų sąrašas

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

Pirmoje šoninės juostos puslapyje pateikiamas visų prijungtų kamerų ir masyvų sąrašas:

* **Prijungti kamerą**(žalia) /**Prijungti masyvą** (mėlyna, nuskaitymo metu rodo „Aptinkama...“). Abi funkcijos išjungtos, kol atidarytas prijungimo dialogas.
* **Užfiksuoti viską** (raudona) — užfiksuoja visas sąraše esančias kameras, naudojant „Užfiksavimo nustatymuose“ pasirinktus eksporto tipus. Reikia atidaryto projekto. Išsamiai aprašyta skyriuje [Įrašymo nustatymai ir režimai](capture.md).
* **Įrašymo nustatymų ratukas** (šalia mygtuko „Įrašyti viską“) — atidaro [Įrašymo nustatymų langą](capture.md#the-capture-settings-pane). Neveikia, jei nėra projekto arba vyksta įrašymas.

### Kamerų eilutės

Kiekvienoje kamerų eilutėje rodomas spalvotu kodu pažymėtas rėmelis (kameros pasirinkta spalva), užrašas „CAM“ — su mėlyna **M**(pagrindinė) arba žalią**S** (pagalbinė) – ir ekrano pavadinimą. Numatytasis pavadinimas yra `LATTICE-MODEL (serial)`; jį galima pervardyti kameros nustatymų lange. Eilutės mygtukai:

| Mygtukas | Funkcija |
| --- | --- |
| **Akių simbolis**| Įjungti / išjungti matomumą. Paslėptos kameros išnyksta iš tinklelio ir**neįtraukiamos į „Capture All“**. |
| **Gear** | Atidaryti kiekvienos kameros nustatymų langą (kitas skyrius). |
| **Pause / Play**| Sustabdyti tiesioginį peržiūros vaizdą**tik ekrane** — duomenų surinkimas serveryje tęsiasi. Sustabdytos kameros negali įrašyti. |
| **X** | Atsijungti. Vartotojo sąsaja atnaujinama iš karto (geriausiu atveju); o pačiam užkulisiniam atjungimui užbaigti gali prireikti 10–30 s. |

### Masyvo eilutės

Masyvo eilutėje rodomas „ARRAY“ ženklas masyvo spalva, masyvo pavadinimas (jį galima pervardyti masyvo nustatymuose) ir **DAQ · įjungta/išjungta**mygtukas —**įjungta**, kai nustatytas masyvo lygio šviesos jutiklis *arba* bet kuris narys turi kameros jutiklį; jo paaiškinime nurodoma, kuris jutiklis perduoda kokius duomenis. Masyvo narių kameros išvardytos po juo su atitraukimu, kiekvienai skirta atskira eilutė. Masyvo eilutės mygtukai: **akis**(paslepia/rodo VISUS narius kartu),**ratas**(masyvo nustatymų langas),**X**(atjungia visą masyvą).

Šviesos jutiklio (DLS) būsena, naudojama masyvo eilutėse ir masyvo nustatymų lange, turi keturias būsenas:**išjungta**,**laukia**(dar nėra spektro),**aktyvus**(spektras gautas per paskutines 3 s) ir**pasenęs** — per 3 s nebuvo gautas naujas spektras, bet paskutinis rodmuo *vis dar naudojamas* (DAQ rodmenys fiksavimo kelyje niekada nepasensta).

Kol nepakeisite tvarkos, sąraše pirmiausia rodomi masyvai, tada atskiros kameros, o kameros (ir kiekvieno masyvo elementai) išdėstomi **kamerų tvarka**: M3C (Bayer) prieš M3M (mono); tarp M3C – FRGB, FRGN, FOCN, tada FNGB; M3M – pagal filtro bangos ilgį, pradedant nuo mažiausio. Tinklelio numatytasis išdėstymas atitinka tą pačią tvarką. Šoninėje juostoje galite perkelti atskiras kameras ir ištisus masyvų grupes viena pro kitą, kad pakeistumėte sąrašo tvarką; masyvo elementų atskirai perkelti negalima.

## Kameros nustatymų langas

Atidarykite spustelėdami **ratuką** kameros eilutėje. Langas pasislenka virš kamerų sąrašo.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->



**Antraštė**: kameros**spalvų pavyzdys**(spustelėkite, kad atidarytumėte integruotą spalvų pasirinkimo įrankį — nustato šoninės juostos rėmelio ir plytelių pasirinkimo žiedo spalvą),**pavadinimas**su pieštuku pažymėtu mygtuku**Pervardyti**(išsaugojus tuščią pavadinimą, grįžtama prie numatytojo `MODEL (serial)`) ir**×** uždarymui.

### Tiesioginis histogramas

Langelio viršuje yra tiesioginis šviesos histogramas, apskaičiuojamas iš JPEG peržiūros ~8 Hz dažniu. Vidurkis yra svertinis pagal „Bayer“ metodą — (R+2G+B)/4 — kad atitiktų pačios kameros automatinio ekspozicijos matavimo (AE) nustatymus.

* **Oranžinė (Orange) punktyrinė linija**= AE tikslas.**Pavelkite ją horizontaliai, kad pakeistumėte tikslą** — atleidus pelę siunčiamas vienas komandos signalas, o vilkdami perjungiate AE tikslo režimą į „Manual“ (Rankinis).
* **Žalia (Green) ištisinė linija** = faktinis vidutinis šviesos lygis (tai, ką šiuo metu užtikrina AE).
* **RGB mygtukas** (dešinėje viršuje): perjungia kiekvienos juostos perdengiamąsias histogramas, nuspalvintas pagal fotoaparato filtrą (pvz., naudojant FRGN: pilka NIR, žalia, raudona). Mono (M3M) fotoaparatuose mygtukas pažymėtas „MONO“ ir yra išjungtas — mono režimas visada rodo vienos juostos šviesos histogramą.
* X ašies žymės atitinka dabartinio pikselių formato jutiklio bitų gylį: 0..255, 0..1023, 0..4095 arba 0..65535.

### Kameros informacijos eilutės

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->



| Eilutė | Veikimas |
| --- | --- |
| **Modelis** | Tik skaitymo (pvz., `LATT-M3C-L87-FRGN`). |
| **Radiometrinis kalibravimas** | Žalia (Green) **„Aktyvi“**žymė su antrašte, kurioje nurodytas kalibravimo lygis, hash, kalibravimo data ir juostų sąrašas, įkeltas iš kameros kalibravimo paketo (žr. [Gamybinis radiometrinis kalibravimas](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)).**Paslėpta RGB kameroms** — jos turi fotometrinį baltos spalvos balanso kalibravimą, o ne spinduliavimo kalibravimą pagal atskirus juostos. |
| **Kalibravimo ataskaita**| Mygtukas**„Atsisiųsti“** — atidaro kameros serijinio numerio NIST kalibravimo sertifikatą PDF formatu jūsų operacinės sistemos peržiūroje. Jei sertifikatas dar nėra išsaugotas talpykloje, Chloros vietoj to rodo užuominą. |
| **Serijinis numeris** | Tik skaityti. |
| **Programinė įranga**| Rodo dabartinę versiją, tada nustato šiam modeliui prieinamą versiją (išsaugoma pagal modelį — N kamerų masyvas patikrina serverį vieną kartą). Būsenos: „Tikrinama…“ → mygtukas**„Atnaujinti į X“**→ „Atnaujinama…“ → „Atnaujinta iš A į B“ / „Nepavyko: …“ / „Praleista: …“ / žalia**„Atnaujinta“**. Atnaujinimo mygtuko paaiškinimas: „Gamyklinių nustatymų atstatymas + atnaujinimas + „UserSet1“ perprogramavimas. ~2–3 minutės; neatjunkite.“ |
| **IP** | Tik skaityti. |
| **Temperatūra** | Tik skaityti, atnaujinama kas 3 s. Tampa oranžinė, kai temperatūra ≥65 °C, ir raudona su ⚠, kai ≥75 °C. |
| **Kalibravimo taikinys** varnelė | Įjungia „ArUco“ atspindžio taikinio aptikimą su kiekvienam skydeliui skirta NDVI patvirtinimo lentele po tiesioginio vaizdo (sąrašo peržiūra) — žr. [Kalibravimo taikinio lentelę](#the-calibration-target-table). Veikia tik sesijos metu — visada atidaroma išjungta. |
| **Šviesos jutiklis** išskleidžiamasis meniu | Priskiria DAQ šviesos jutiklį (DAQ-E/M/U, iš skirtuko „Šviesos jutikliai“ sąrašo) prie šios kameros, kad būtų atliekama žemyn nukreiptos šviesos (DLS) apšvietimo korekcija ir prognozuojamas automatinis ekspozicijos nustatymas. „Pasirinkus „Nėra“ susiejimas panaikinamas. Jei nėra prijungtų jutiklių, išskleidžiamajame meniu rodomas pranešimas „(jutikliai neprijungti — atidarykite skirtuką „DAQ“)“. Susiejimas išsaugomas kartu su projektu. |

#### Kalibravimo taikinio lentelė

Lentelė yra **skirta tik patikrinimui** („tik patikrinimui — nekeičia kalibravimo“). Jos antraštėje nurodomas kameros serijos numeris, filtras, taikinio numeris ir žymės dydis pikseliais, o kiekvienam skydeliui skirta po vieną eilutę:

* **Plokštė**, po to – po vieną stulpelį kiekvienai juostai su plokštės verte – „Bayer“ kamera rodo tris juostas pagal pavadinimą ir bangos ilgį (pvz., FRGN: Red 660 nm / Green 550 nm / NIR 850 nm); **mono (M3M) kamera rodo vieną juostą** (pvz., `F750`) ir neturi NDVI stulpelių.
* **Sat** — prisotintų panelės pikselių dalis (gintarinė spalva nuo 1 %, raudona nuo 5 %).
* **NDVI Meas**,**NDVI Theo** (ekrano etaloninis atspindžio NDVI) ir **Δ**— tik „Bayer“ multispektrinėms kameroms. Mažas ženklas po „NDVI Meas“ nurodo, iš ko buvo apskaičiuotas išmatuotas NDVI:**ρ**(atspindys — yra šviesos jutiklio rodmenys),**L**(spinduliavimas — nėra šviesos jutiklio rodmenų) arba**DN**(neapdoroti kameros duomenys). Tik**ρ** NDVI galima lyginti su NDVI Theo: L ir DN atveju stulpelis Δ yra užblokuotas, o pastaboje nurodoma („Nėra šviesos jutiklio rodmenų: NDVI apskaičiuotas pagal spinduliavimą, todėl Δ nėra kalibravimo patikrinimas“ / „NDVI apskaičiuotas pagal neapdorotus kameros duomenis, todėl Δ nėra kalibravimo patikrinimas“). Naudojant ρ, Δ yra žalias, jei mažesnis nei 0,02, gintarinis – jei mažesnis nei 0,05, o kitais atvejais – raudonas.

<!-- SCREENSHOT-NEEDED: a 1.3.0 Calibration Target table under a live feed in list view — a Bayer camera's table showing the band columns, Sat, and "NDVI Meas ρ" / NDVI Theo / Δ; ideally next to a mono camera's single-band table with no NDVI columns. -->

### Ekspozicija ir stiprinimas

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Visi čia esantys skaitmeniniai įvesties laukeliai naudoja „laikyk ir pagreitink“ tipo slankiklius: bakstelėjimas = ±1, laikymas >1,5 s = ±10, laikymas >3 s = ±100. Vertė perduodama kamerai, kai atleidžiate slankiklį.

| Valdymas | Diapazonas / pasirinkimai | Numatytasis | Taikoma | Ką daro |
| --- | --- | --- | --- | --- |
| **Ekspozicija (us)**| Kameros realaus vaizdo min./maks. | Automatinis | Visi | Ekspozicijos trukmė mikrosekundėmis, su**Automatinis/Rankinis**perjungimo mygtuku. „Auto“ = nuolatinė kameros automatinė ekspozicija. Nustačius rankinę ekspoziciją,**jautrumas** lieka „Auto“ režime, jei toks buvo, ir galima naudoti visą kameros jautrumo diapazoną, kol nepakeisite jautrumo nustatymų arba nenustatysite „Rankinis“ režimo. |
| **Stiprinimas (dB)**| Kameros realaus laiko min./maks. (pvz., iki 48 dB) | Rankinis (išjungta) | Visi | Analoginis/skaitmeninis stiprinimas su atskiru**Automatinis/Rankinis** perjungikliu. |
| **AE tikslinis ryškumas**| 0–255 | 80, režimas**Automatinis**| Visi (redaguojama, kai įjungta AE arba automatinis stiprinimas) | Ryškumas, kurio siekia AE.**Auto**(numatyta) režimu histogramos pagrindu veikiantis valdiklis pats pasirenka tikslą, išlaikydamas ekspoziciją 60–75 % jutiklio maksimumo lygyje. Įvedus reikšmę arba patempus oranžinę histogramos liniją, režimas persijungia į**Rankinį**. |
| **AE išlyginimas** | 0,5–40, žingsnis 0,1 | 8,0 | Visi | AE slopinimas. Įrankio patarimas: „Mažesnė reikšmė = AE reaguoja greičiau (gali pulsuoti esant dideliam kadrų dažniui). Didesnė reikšmė = sklandesnis / lėtesnis.“ Reikšmės, gerokai mažesnės už numatytąją, gali sukelti AE pulsavimą ir destabilizuoti transliaciją esant dideliam kadrų dažniui; 8,0 yra stabili numatytoji reikšmė. |
| **AE domėjimosi sritis**| Įjungimo langelis + mygtukas**Nukreipti**| Išjungta | Visi | Kai įjungta, AE matuoja tik žalią punktyrinę sritį, o ne visą kadrą.**Tikslinimas** įjungia funkciją „spustelėk ir nustatyk“ tiesioginėje transliacijoje: spustelėjus sritis centruojama 30 % kadro vietoje; spustelėjus ir vilkdami nubrėžiate pasirinktinį stačiakampį (mažiausiai 5 % × 5 %). Funkcija „Aim“ išsijungia po vieno nustatymo. Sritis pritaikoma prie kameros natūralių koordinačių pagal bet kokį jūsų nustatytą pasukimą ar veidrodinį atspindį ir išsaugoma kartu su projektu. |
| **AE greičio reguliavimas** | 0,1–5, žingsnis 0,1 | 1,0 | Tik masyvo nariams | Kaip greitai automatinio AE tikslas seka scenos ryškumo pokyčius; esant 1,0×, pakartotinai tikrinama kas 2,5 s. |
| **Aukštų šviesų apsauga** | Griežta (1 %) / Įprasta (5 %) / Švelni (15 %) | Griežta | Kameros, kuriose galima nustatyti šį parametrą | Kokia kadro dalis gali būti apkarpyta iki baltos spalvos, kol AE patamsina vaizdą. |

{% hint style="info" %}
**Apšvietimo reikalavimai „Bayer“ multispektrinėms kameroms (RGN / OCN / NGB):** scenoje turi būti pakankamai šviesos visuose trijuose kanaluose, kitaip kalibravimas neveiks tinkamai — vieno jutiklio ekspozicija apima visus tris spektrus. Naudokite DAQ šviesos jutiklį šviesai matuoti arba perjunkite į visiškai monokromatinį režimą (M3M), kad kiekvienai juostai būtų nustatyta atskira ekspozicija. Jei fotografuojant šis reikalavimas pažeidžiamas, Chloros tai aptinka ir įspėja jus (pranešimas apie juostų atskyrimo apribojimą). Tiesioginiame vaizde kanalo padalijimo langelyje rodomas užrašas **„Mažo patikimumo juostos duomenys · N %“**, kai „unmix“ apribojo tą juostą 25 % ar daugiau jos pikselių.
{% endhint %}

{% hint style="info" %}
**Kaip automatinė ekspozicija naudoja ekspozicijos trukmę ir stiprinimą (1.3.0).** Kai stiprinimas nustatytas į „Auto“, Chloros išlaiko stiprinimą 0 dB lygyje, kol ekspozicija pasiekia ilgiausią leidžiamą vertę (AE viršutinę ribą, masyvo suaktyvinimo laikotarpį arba DAQ ekspozicijos ribą); tik tada stiprinimas padidėja. Masyve pirmenybė teikiama suaktyvinimo dažniui: automatinė ekspozicija užpildo laikotarpį, kurį nustato dažnis, o tada padidina stiprinimą, todėl tamsėjanti scena nebesumažina automatinio sužadinimo dažnio (1.2.0 versijoje taip buvo). **Rankinė**ekspozicija masyvo naryje, trunkanti ilgiau nei sužadinimo laikotarpis, sulėtina masyvo sužadinimą, kad jis jai atitiktų; automatinis dažnis atsinaujina, kai kiekvienas elementas, kuris jį sulėtino, vėl pereina į automatinę ekspoziciją. Kai**Ekspozicija**ir**AE tikslinis ryškumas** nustatyti į „Auto“, o DAQ šviesos jutiklis teikia rodmenis, kiekvienos kameros ekspozicija išlaikoma, kai tik automatinė ekspozicija stabilizuojasi, tol, kol išmatuotas šviesos lygis išlieka maždaug ±25 % ribose; didesnis, ilgalaikis šviesos pokytis, nustatymų perkrovimas (pavyzdžiui, ROI pakeitimas) arba jūsų pačių atliktas ekspozicijos pakeitimas vėl ją aktyvuoja.
{% endhint %}

{% hint style="info" %}
**Kaip veikia automatinė ekspozicija (1.3.0).**

* Rankinė ekspozicija palieka stiprinimą automatiniame režime, kol jo nepakeisite.
* Maži šviesūs taškai nepatamsina vaizdo: ignoruojamas atspindys, sudarantis iki 1 % kadro, o žinomų kalibravimo taikinių plokštėse – iki 0,5 % plokštės ploto.
* DAQ ekspozicijos riba taikoma tik tada, kai matomas kalibravimo taikinys; jei plokštės nematyti, kamera eksponuoja pagal vaizdą.
* Ekspozicija išlieka stabili esant pastoviam apšvietimui ir stabilizuojasi per kelias sekundes po didelių pokyčių. Apkarpymas (clip) nebeapriboja ekspozicijos visam laikui, o pakeitus domėjimosi sritį (ROI) ekspozicija vėl nustatoma.
* Ryškumo riba neviršija kameros, todėl sugrįžęs šviesos šaltinis nesukelia blyksnio kadre.
* Kamera, kurios automatinė ekspozicija nustoja reaguoti esant ryškiai saulei, išlaiko ekspozicijos lygį, atitinkantį esamą apšvietimą, kurį nuskaito šviesos jutiklis arba, jei jo nėra, pati kamera remdamasi savo kadrais, ir atleidžiama, kai apšvietimas pasikeičia. Tam netrukdo nei tamsus, nei uždengtas šviesos jutiklis.
{% endhint %}

### Pikselių formatas ir skiriamoji geba

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

**Masyvo nariai** rodo tik skaitymo režimu veikiančias eilutes „Dabartinis“ (formatas + PxA) ir „Binning“ su pastaba „Nustatyta masyvo nustatymuose“ — srauto paleidimas iš naujo viename naryje sutrikdytų sinchronizaciją, todėl šie parametrai valdomi [masyvo nustatymų lange](#array-settings-pane).**Atskiros kameros** turi:

| Valdymas | Pasirinkimai | Ką daro |
| --- | --- | --- |
| **Pikselių formatas** | 8 bitų / 10 bitų / 12 bitų / 16 bitų | Jutiklio bitų gylis; formato šeima priklauso nuo jutiklio. Numatytasis nustatymas yra 12 bitų, o projektas išsaugo jūsų nustatytą vertę, įskaitant 8 bitų, ir vėl prisijungia naudodamas ją. |
| **Skiriamoji geba** | Pilna / Pusė / Ketvirtis | Santykinai pagal dabartinį binningą: Pilna = 2048/N × 1536/N, kai binningas yra N×N. |
| **Pikselių sujungimas** | 1x1 (nėra) / 2x2 / 4x4 | Aparatinis N×N pikselių sujungimas — didesnės reikšmės sumažina skiriamąją gebą, bet padidina SNR ir kadrų dažnį. Pakeitus šį parametrą, srautas paleidžiamas iš naujo, o bet koks ROI nustatymas iš naujo pritaikomas prie naujo pilno matymo lauko. |
| **Dabartinis** | tik skaityti | Faktiniai galiojantys WxH ir (x, y) poslinkiai. |

### Tiesioginis peržiūros langas

Viskas šiame skyriuje yra **tik ekrano pusėje**— tai keičia tai, ką matote tiesioginiame sraute, o išsaugoti kadrai lieka linijiniai ir nepakeisti — su viena išimtimi:**Vignette** yra radiometrinis ir taip pat veikia eksportuojamus failus (kaip nurodyta žemiau).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->



| Valdymo elementas | Diapazonas / pasirinkimai | Numatytasis | Taikoma | Ką daro |
| --- | --- | --- | --- | --- |
| **Renderinimo skiriamoji geba** | 360p (greičiausia) / 480p / 720p / 1080p / Natūrali jutiklio skiriamoji geba (lėčiausia) | 720p | Visi | Aukštis, kuriame užkulisinė sistema vykdo radiometrinės peržiūros grandinę. Mažesnė reikšmė užtikrina didesnį kadrų dažnį, nekeičiant matymo lauko. |
| **Indeksas**| Žymės langelis „Įjungti“ + ratukas | Išjungta | Tik „Bayer“ multispektrinė,**ne** kombinuotojo masyvo nariai | Tiesioginė augmenijos indekso peržiūra. Paspaudus ratuką atsidaro bendrai naudojamas [Indekso skaičiuoklė](#index-calculator-pane), kurioje iš anksto įkelti kameros filtro natūralūs juostų nustatymai (pvz., `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`). Kiekviename peržiūros kadre apskaičiuojama pasirinktinė išraiška ir LUT (įjungta/išjungta, numatytas lygis 7 stopai, numatytas min. 0,2, numatytas maks. 1) apskaičiuojama kiekviename peržiūros kadre. Kombinuoto masyvo nariai paslepia šią eilutę — masyvas turi vieną bendrą indeksą. |
| **Baltojo balanso nustatymas** | Išjungta / Vienkartinis / Nuolatinis + pakartotinio fiksavimo mygtukas | Nuolatinis | Tik RGB | Baltos spalvos balansas realiuoju laiku. Atnaujinimo mygtukas iš naujo užfiksuoja baltos spalvos balansą iš dabartinio DLS spektro (išjungtas, kai režimas yra „Išjungta“). |
| **Gama** | Įjungta / Išjungta | Įjungta | Tik RGB | Rodo gamą (γ = 2,2 LUT) tiesioginiame peržiūros ekrane. Išsaugoti kadrai išlieka linijiniai. |
| **Triukšmo šalinimas** | Varnelė + stiprumas 0–100 | Išjungta / 50 | Visi (kiekvienai kamerai atskirai, net masyvuose) | Dvipusis filtras tiesioginiame peržiūros ekrane. Didesnis stiprumas = sklandesnis, bet švelnesnis detalumas. |
| **Ryškumas** | Varnelė + stiprumas 0–100 | Išjungta / 30 | Visi | Neaštraus kaukės taikymas tiesioginiame peržiūros lange, taikomas paskutinis. Gali sustiprinti triukšmą. Tik peržiūrai. |
| **Vignette**| Varnelė + stiprumas 0–100 | Išjungta / 0 | Visi | Rankinis likutinis vignetės pašalinimas (išryškina kampus), uždedamas ant masyvo „Smart Vignette“ įvertinimo.**Radiometrinis — veikia tiesioginį vaizdą IR eksportą**, skirtingai nei triukšmo pašalinimas ir ryškumas. |
| **Spalvų profilis** | „Raw“ / „Linear“ / „Natural“ / „Enhanced“ / Pasirinktinė temperatūra | „Natural“ | Tik RGB | Žr. toliau. |
| **Spalvų temperatūra** | 2000–10000 K, žingsnis 100 | 5500 K | Tik RGB, tik pasirinktinis temperatūros profilis | Nustato baltos spalvos balansą pagal fiksuotą koreliuotą spalvų temperatūrą (DLS įvestis ignoruojama). Paskutinė pasirinkta Kelvino vertė išsaugoma keičiant profilius. |
| **Šviesios sritys** | nuo −100 iki 0, žingsnis 1 | 0 (išjungta) | Tik RGB | Nauja funkcija versijoje 1.3.0. Patamsina ryškiausias, baltiausias vaizdo sritis (0 = nepakeista, −100 = stipriausias). Taikoma tiesioginiam peržiūros vaizdui **ir ekrano lygio (peržiūros) kadrams**, kuriuose įrašomas šis nustatymas (`Chloros:DisplayHighlights`); spinduliavimo ir atspindžio eksportams tai niekada neturi įtakos. Ši funkcija negali atkurti apkarpytų jutiklio duomenų — tam reikia sumažinti ekspoziciją. „Zebra“ perdanga atitinka patamsintą vaizdą. Išjungta naudojant „Raw“ spalvų profilį („Netaikoma naudojant „Raw“ spalvų profilį“). |
| **Sotumas** | 0–200 (100 = neutralus) | 100 | Tik RGB | HSV sotumas tiesioginiame peržiūros ekrane. |
| **Kontrastas** | 0–200 (100 = neutralus) | 100 | Tik RGB | Tiesinis kontrastas aplink vidutinę pilką spalvą tiesioginiame peržiūros lange. |
| **Veidrodinis H / Veidrodinis V** | Žymimieji langeliai | Išjungta | Visi | Apsukti peržiūrą horizontaliai / vertikaliai. |
| **Pasukimas**| 0° / 90° / 180° / 270° | 0° | Visi | Pasukti peržiūrą. Orientavimas taikomas peržiūros grandinės pabaigoje.**Išsaugoti kadrai (1.3.0):**atskiro fotoaparato „Peržiūra“, „Spinduliavimas“, „Atspindys“ ir „Indeksas“ produktai pasukami naudojant „Veidrodinis atspindys“ / „Pasukimas“, o**„Raw“ ir „Debayered“ išlieka kameros natūralioje orientacijoje**; kiekviename užfiksuotame faile šis nustatymas įrašomas į XMP, todėl vėliau apdorojant „raw“ failą jis taikomas taip pat.**Masyvo narių failai išlieka natūralūs** (nustatymas įrašomas, bet netaikomas), o masyvo sudėtiniai vaizdai jo ignoruoja. |

**Spalvų profilio semantika** (RGB kameros):

* **„Raw“** — visiškai apeina apdorojimo grandinę.
* **„Linear“** — tamsusis signalas + plokščias laukas + baltos spalvos balansas; be spalvų matricos, be gama.
* **Natūralus** *(numatyta)* — linijinis plius išmatuota spalvų korekcijos matrica ir prie scenos prisitaikanti tonų kreivė.
* **Patobulintas**— „Natūralus“ plius sodrumas ir CLAHE vietinis kontrastas. Papildomos išlaidos taikomos**tik tiesioginiam peržiūros režimui** — išsaugoti kadrai visada gauna pilną apdailą, nepriklausomai nuo profilio.
* **Pasirinktinė temperatūra** — „Natūralus“ su baltos spalvos balansu, nustatytu pagal jūsų pasirinktą Kelvino vertę.

{% hint style="warning" %}
Rinkus „Natūralus“, „Patobulintas“ ir „Pasirinktinė temperatūra“, lange rodomas tonų įspėjimas: kadrai pašviesinami atsižvelgiant į jų sceną, todėl išsaugoti *ekrano* vaizdai nėra palyginami kadrų atžvilgiu. **Eksportuokite spinduliavimą arba atspindį matavimams.**
{% endhint %}

### Ekrano perdangos (piešiamos ant tiesioginio vaizdo)

Jos veikia tik vartotojo sąsajoje — piešiamos ant vaizdo, niekada neliesdamos srauto ar įrašų.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Perdanga | Valdymo elementai | Numatytasis | Ką daro |
| --- | --- | --- | --- |
| **Zebra** | Varnelė + riba 200–255 | Išjungta / 250 | Magentos spalvos įstrižinės juostos ant apkarpytų pikselių. |
| **Kryžminis taškas** | Varnelė | Išjungta | Kadro centro žymė. |
| **Tinklelis** | Išjungta / 3 × 3 / 9 × 9 | Išjungta | Kompozicijos tinklelis. |
| **Histograma** | Varnelė + plotis 0,10–0,90 kadro | Išjungta / 0,25 | Histogramos juosta tiesioginiame sraute. |
| **Fokuso pikas** | Varnelė + slenkstis 20–200 + spalvų pavyzdys | Išjungta / 80 / `#ff5722` | Sobelio krašto paryškinimas fokusavimui. |
| **Kanalų padalijimai** | „Rodyti padalijimus (Red / Green / NIR)“ / mygtukas „Paslėpti padalijimus“ | Paslėpta | Prie sudėtinio vaizdo prideda tris nepriklausomus pilkosios skalės langelius kiekvienam kanalui (mygtuko pavadinimas atitinka kameros filtro kanalus). Kiekvieną padalijimo langelį galima perkelti, o jo spalva atitinka kameros rėmo spalvą. Nėra prieinama monochromatinėse kamerose. Išsaugoma kartu su projektu. |

### Taškinis matuoklis

* **Spustelėkite, kad paimtumėte mėginį**žymės langelis: spustelėkite tiesioginį vaizdą, kad paimtumėte vieno pikselio mėginį (jį pažymi kryžminis tinklelis), arba spustelėkite ir vilkite sritį, kad gautumėte pikselių vidurkį.**Išvalyti**ištrina mėginį ir tinklelį. Ši funkcija nesuderinama su AE-ROI**Nukreipimo** režimu.
* **Rodyti** išskleidžiamasis meniu:**Neapdoroti duomenys (bitų gylis)**— natūralūs skaitmeniniai skaičiai, atitinkantys jutiklio bitų gylį (pvz., 12 bitų → 0..4095) — arba**Ekranas (8 bitai)** (numatyta reikšmė). Kai aktyvus tiesioginis indeksas, „Display“ vietoj to rodo apskaičiuotą indekso vertę (pvz., NDVI).
* Rodymo skydelyje pateikiamos pikselių koordinatės, kadro dydis, pikselių formatas, bitų gylis ir kanalų lentelė (Chan / Value / %) su juostų pavadinimais ir bangų ilgiais; „Bayer“ žalios poros yra vidurkinamos; regionų mėginiai rodomi kaip „N px avg“.

Taškinio matuoklio būsena galioja tik sesijos metu.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Prognozuojama automatinė ekspozicija (valdoma DLS)

Šis skyrius rodomas tik tada, kai **prijungtas bent vienas DAQ šviesos jutiklis** — skaičiuoklei reikia realaus žemyn nukreipto spektro, kad ji galėtų veikti.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->



| Valdymas | Diapazonas | Numatytasis | Veikimas |
| --- | --- | --- | --- |
| **Įjungti** | Varnelė | Įjungta (autonominės kameros) | Uždarosios formos sprendiklis naudoja DLS spektrą ir kameros kalibravimo paketo skaliarus, kad ryškiausia juosta būtų arti prisotinimo, o tamsiausia juosta išliktų virš SNR ribos — vienas ekspozicijos įrašymas per kiekvieną sprendimą, be stabilizavimo ciklo. Sukurtas saulės energija maitinamiems laiko tarpo vaizdams, kur kiekvienas kadras turi būti teisingai eksponuotas. Sistemos branduolys tyliai pereina prie reaktyviosios automatinės ekspozicijos (AE), kai DLS rodmenys yra pasenę/trūksta arba kalibravimo paketas nėra įkeltas. |
| **Išlyginimas (α)** | 0,05–1,0, žingsnis 0,05 | 0,3 | Iš eilės einančių prognozuojamų sprendimų išlyginimas (kuo mažesnė reikšmė, tuo sklandesnis išlyginimas). |
| **Scenos atspindžio koeficientas**| Mygtukas**„Recalibrate ρ“** | — | Iš naujo įvertina scenos atspindžio koeficientą, kurį naudoja sprendiklis. |

{% hint style="info" %}
**Masyvo jungimas pagal numatytuosius nustatymus išjungia prognozuojamą AE** — masyvų atveju ekspoziciją valdo „Chloros “ išmanioji AE ir kameros pusėje esanti automatinė ekspozicija (su prisotinimo apsauga), o prognozuojamos AE vienos scenosatspindžio įvertinimas nėra saugus mišriose scenose. Čia galite jį vėl įjungti kiekvienai kamerai atskirai, jei konkrečiai norite DLS valdomos radiometrinės ekspozicijos.
{% endhint %}

**DAQ valdomas ekspozicijos ribinis dydis ir nuo krintančios šviesos priklausoma AE.** Neatsižvelgiant į aukščiau nurodytą žymės langelį, kai DAQ šviesos jutiklis priskiriamas RGB kamerai, Chloros apskaičiuoja — remiantis išmatuotu absoliučiu žemyn nukreiptu spinduliavimo intensyvumu — maksimalią ekspoziciją × stiprinimą, kuriuo 100 % atspindžio paviršius išlieka žemiau apkarpymo ribos, ir taiko ją kaip **ribą**automatinei ekspozicijai. Nuo 1.3.0 versijos riba taikoma tik tada, kai kalibravimo taikinys yra tos kameros matymo lauke (matomas kadre per paskutines 10 s); kai taikinio nėra matymo lauke, vietoj to veikia įprasta kameros automatinė ekspozicija su savo apsauga nuo peršvietimo, todėl likusi scenos dalis nėra išlaikoma tamsesnė nei reikia. Kol riba yra aktyvi, kamera veikia**pririšta prie krintančios šviesos**: ji veikia atvirosios grandinės režimu pagal krintančios šviesos matavimus, esant 0 dB stiprinimui — ekspozicija seka matuojamą šviesą, o ne scenos turinį. Kadangi viršutinė riba gali tik sutrumpinti ekspoziciją, ji pati negali sukelti vaizdo apkarpymo. Nuo 1.3.0 versijos viršutinė riba apskaičiuojama naudojant pačios kameros ekspozicijos konstantą iš jos kalibravimo paketo (parko vidutinė vertė taikoma tik tada, kai pakete jos nėra). Viršutinė riba automatiškai išjungiama — ir atnaujinama įprasta scenos automatinė ekspozicija — kai kadre nėra kalibravimo taikinio, trūksta DAQ rodmenų, jie yra pasenę (>30 s), tamsus arba jei ≥15 % kadro yra apkarpyta esant fiksuotai ekspozicijai (tai reiškia, kad jutiklis ir kamera mato skirtingą apšvietimą). Nėra GUI jungiklio; tai yra standartinis elgesys, kai RGB kamera yra susieta su DAQ.

### Duomenų surinkimas ir suaktyvinimas

<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

Masyvo nariai papildomai rodo tik skaitymo režimu veikiančias eilutes: **Vaidmuo**(Pagrindinis – mėlyna / Pavaldusis – žalia),**Sinchronizavimo linija**ir**Tarpusavio ryšiai**.

| Valdymas | Pasirinkimai | Numatytasis | Pastabos |
| --- | --- | --- | --- |
| **Triggerio režimas** | Išjungta / Įjungta | Įjungta | Masyvo elementams išjungta (masyvas valdo trigerio veikimą). |
| **Triggerio šaltinis** | Programinė įranga / Linija 0 (M8) / Linija 1 / Linija 2 | Linija 0 | Paslėpta, kai „Triggerio režimas“ yra išjungtas; išjungta masyvo nariams. Linija 0 yra M8 optiškai izoliuotas išorinis trigerio įėjimas. |
| **Kadrų dažnis**| Automatinis / Rankinis + vertė | Automatinis |**Automatinis**: kameros kadrų dažnio apribojimas išjungtas — ekspozicija nulemia kadrų skaičių per sekundę (fps), o langelyje rodomas tikrasis kadrų dažnis realiuoju laiku.**Rankinis**: kadrų dažnį ribojate slankikliu (nuo 1 iki maksimalaus, kurį leidžia pralaidumas), pradedant nuo dabartinio faktinio dažnio. Masyvo nariai mato tik skaitymo režimu rodomą „N kadrų per sekundę (tiesioginis)“ su užrašu „Nustatyta masyvo nustatymuose“. |

### Tinklas / Perdavimas

| Eilutė | Elgsena |
| --- | --- |
| **Paketo dydis**| 1500 (Standartinis) / 9000 (Jumbo) — numatytasis**Jumbo**. |
| **Pralaidumas** | Tik skaitymo režimu nustatoma ryšio pralaidumo riba, išreikšta MB/s. Kiekvieną kartą prisijungus ar atsijungus, serveris iš naujo paskirsto šį pralaidumą tarp visų prijungtų kamerų. |
| **Buferio tvarkymas** | Tik skaitymo režimu nustatomas buferio tvarkymo režimas. |

### Įrašymas

Langą užbaigia mygtukas **„Atidaryti įrašymo nustatymus…“**, kuris nukreipia į [Įrašymo nustatymų langą](capture.md#the-capture-settings-pane) (neveikia, kol nėra atidarytas projektas — „Sukurkite arba atidarykite projektą, kad būtų galima išsaugoti įrašus“). Jei kamera paslėpta arba sustabdyta, patarimas primena, kad prieš pradedant įrašyti reikia ją parodyti arba atnaujinti veikimą.

## Masyvo nustatymų langas

Atidarykite spustelėdami **ratuką**ant Masyvo eilutės. Antraštė: masyvo pavadinimas su pervardinimo pieštuku ir**×** uždarymui. Toliau esančios sekcijos, pažymėtos *tik sujungtos*, rodomos tik tada, kai masyvai sujungti sujungto rodymo režimu.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->



### Sinchronizavimas

Tik skaitymo režimo **Pagrindinis**,**Pavaldieji**ir**Sinchronizavimo linija** eilutės.

### Aplinkos šviesos jutiklis

Rodomas tiek sujungtiems, tiek atskiriems masyvams:

* **Šviesos jutiklis** išskleidžiamasis meniu — susieja vieną DAQ su visu masyvu. Pasirinkimas įsigalioja iš karto, perduodamas į kiekvienos kameros „Šviesos jutiklio“ išskleidžiamąjį meniu (vis tiek galite pakeisti nustatymus kiekvienai kamerai atskirai) ir pradeda siųsti spektrus į masyvą.
* Tiesioginė **Būsenos** eilutė: Išjungta · „Laukiama pirmojo spektro…“ · „Aktyvuota — visų masyvo kamerų apšvietimas pakoreguotas“ · „Per pastarąsias 3 s naujų spektrų nebuvo — vis dar naudojamas paskutinis rodmuo (nėra pasenusio laiko limito)…“.
* Pastaba lange: „Radiometrinė korekcija visam masyvui. Kiekvienos kameros nustatymai pakeičia šiuos.“

### Kalibravimo taikinys

Savo grupė, esanti iškart po aplinkos šviesos jutiklio, tiek sujungtiems, tiek atskiriems masyvams:

* Varnelė **Įjungti** — „Aptikti MAPIR ArUco taikinį ir patvirtinti NDVI, palyginus su skydelio atspindžio LUT“; valdo sujungto plytelių rinkinio taikinio perdangą ir patvirtinimo lenteles. **Atskirame** masyve įjungia kiekvieno elemento atskirą [Kalibravimo taikinio lentelę](#the-calibration-target-table) (po vieną kiekvienai kamerai sąrašo peržiūroje).

### Įrašymas — vienodi jutiklių nustatymai

Rodoma tiek sujungtiems, tiek atskiriems masyvams (grupės antraštė skamba „Įrašymas (vienodas visoms kameroms)“). Šie nustatymai vienodai taikomi kiekvienam nariui (pakeitimai atskirai kiekvienam nariui sutrikdytų sinchronizaciją). Redagavimai kaupiami ir taikomi kartu su mygtuku **Taikyti**— išskyrus perjungiklį**ROI įjungta / ROI išjungta**, kuris taikomas iš karto.

<!-- SCREENSHOT-NEEDED: array settings Capture section (1.3.0) — Pixel Format, Binning, Resolution preset, the ROI crop row with the "ROI on" toggle, the "Select" button, the "max WxH" hint and Reset button, the W/H/X/Y fields, the full-sensor ROI map below them with the orange crop box and its "W×H @ X,Y" label above the box, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons. -->

| Valdymas | Pasirinkimai / diapazonas | Ką daro |
| --- | --- | --- |
| **Pikselių formatas** | 8 bitų / 10 bitų / 12 bitų / 16 bitų | Vienodas bitų gylis visiems elementams; kiekviena kamera savo jutikliui naudoja atitinkamo gylio formatą. |
| **Binning** | 1x1 / 2x2 / 4x4 | Aparatinis pikselių sujungimas — išlaiko visą matymo lauką, tuo pačiu padidindamas signalo ir triukšmo santykį (SNR) bei kadrų dažnį. Pakeitus šį nustatymą, ROI laukai iš naujo nustatomi pagal naują visą matymo lauką. |
| **Skiriamoji geba** (iš anksto nustatyta) | Visa / Pusė / Ketvirtis | Priklauso nuo pikselių sujungimo; ROI laukai užpildomi centriniu kadravimu. |
| **ROI kadras (px)**|**ROI įjungta / ROI išjungta**perjungiklis, mygtukas**Pasirinkti**, laukai W / H / X / Y, viso jutiklio žemėlapis | Jutiklio kadras — žr. [ROI redaktorių](#the-roi-editor). Plotis ir aukštis priderinami prie 16 kartotinių (mažiausiai 64); nuokrypiai priderinami prie 4 kartotinių. „Maks. WxH“ užuomina rodo viršutinę ribą, o**Atstatyti** grąžina laukelius į visą matymo lauką. Redaguojant oranžinis apkarpymo langelis seka kiekvieną lauko pakeitimą žemėlapyje ir matricos plytelių srityje. |
| **Sukėlimo dažnis**| Perjungimas „Auto“ / „Manual“ + fps 0,5–10, žingsnis 0,5 |**Auto**(numatyta): sistema apskaičiuoja suaktyvinimo dažnį pagal skiriamąją gebą ir pralaidumą — įvesties laukelis išjungtas ir rodo apskaičiuotą vertę.**Rankinis**: užfiksuoja jūsų nustatytą vertę paspaudus „Taikyti“. Užfiksuotas dažnis, kurio naujas kadro dydis negali palaikyti, išlaikomas kaip greičiausias, kurį leidžia tas dydis, ir grįžta, kai kadro dydis vėl tai leidžia. |

Pastaba lange: „Formato / skiriamosios gebos pakeitimai trumpam iš naujo paleidžia visas kameras. Trigerio dažnis taikomas realiuoju laiku.“ Mygtukai **„Taikyti“ / „Atšaukti“** yra lango apačioje.

#### ROI redaktorius

Nauja funkcija versijoje 1.3.0. ROI apkarpymo eilutė ir žemiau esantis žemėlapis veikia kartu:

* **ROI įjungta / ROI išjungta**—**nedelsiant**įjungia arba išjungia jutiklio apkarpymą (kameros trumpam paleidžiamos iš naujo; matricos plytelių lange rodomas užrašas „Taikomi nustatymai…“).**Išjungta**nuskaito visą jutiklio vaizdą ir**įsimena jūsų apkarpymą**— laukeliuose jis lieka rodomas;**Įjungta** vėl jį taiko. Įjungus ROI be nustatyto apkarpymo, taikomas centrinis pusės dydžio apkarpymas („Apkarpymas nebuvo nustatytas, todėl buvo pritaikytas centrinis pusės dydžio apkarpymas. Pakoreguokite jį naudodami laukelius.“).
* **Pasirinkite**— nubrėžia apkarpymą žemėlapyje: vilkite per jį arba spustelėkite du priešingus kampus (klavišas „Esc“ atšaukia). Stačiakampis atsiduria laukeliuose W/H/X/Y, priderinamas pagal aukščiau nurodytus veiksmus; nieko netaikoma, kol nepaspausite**Taikyti**, todėl pirmiausia galite jį tiksliai sureguliuoti naudodami laukelių rodykles. Vienas stačiakampis už kiekvieną „Pasirinkti“ paspaudimą.
* **Žemėlapis**rodo kadravimą visame jutiklyje, todėl galite jį vėl išplėsti: „Visas jutiklis, tiesioginis vaizdas“, kol kameros nuskaito visą jutiklį, arba „Pilno kadro momentinė nuotrauka nuo {laiko}. Kameros nuskaito tik apkarpytą vaizdą“, kai vaizdas yra apkarpytas. Jei matrica visada transliavo tik apkarpytą vaizdą, žemėlapyje rodoma „Dar nėra pilno kadro vaizdo: ši matrica transliavo tik apkarpytą vaizdą.“ su mygtuku**Rodyti visą kadrą**, kuris išjungia ROI. Kadravimo etiketė „`W×H @ X,Y`“ žemėlapyje ir tiesioginės transliacijos plytelių lange yra už langelio ribų (virš jo arba žemiau, kai langelis siekia viršų).
* **Taikyti**patvirtina laukelius: apkarpytas vaizdas įjungia ROI; pilno kadro vaizdas (po**Atstatyti**) jį išjungia, tačiau ankstesnis apkarpytas vaizdas lieka įsimintas; išjungtas ROI, kurio laukeliuose vis dar rodomas įsimintas apkarpytas vaizdas, lieka išjungtas, kai pritaikote kitą pakeitimą (formatas, binningas, dažnis).
* **Suderintoje matricoje kiekvieno elemento apkarpymo langas nustatomas pagal suderinimą**, todėl kiekviena kamera kadruoja tą pačią scenos dalį. Matrica, apkarpyta prieš suderinimą, pirmiausia suderinama pagal visą jutiklį (apkarpymas pašalinamas, suderinimas atliktas, o apkarpymas atkurtas su langais pagal kiekvieną kamerą).
* Pakeitus kadravimą, automatinė ekspozicija iš naujo suderinama pagal naują kadravimą. Kadravimo metu nario ekspozicija vis dar gali veikti iki suaktyvinimo laikotarpio pabaigos.
* ROI įjungimo/išjungimo būsena ir įsimintas kadravimas išsaugomi kartu su projektu.

### Suderinimas (bendras registravimas)

Rodoma **tiek sujungtiems, tiek atskiriems masyvams** (1.2.0 versijoje tai buvo rodoma tik sujungtiems masyvams); grupės antraštėje rašoma „Alignment“ (Suderinimas).

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* **Likutis**: „RMS x.xx px“ — žalia, jei mažiau nei 1 px, gintarinė, jei mažiau nei 3 px, raudona kitais atvejais arba jei kuri nors kamera nesuveikė; „nėra profilio“ prieš pirmąjį sprendimą.
* Apibendrinimo eilutė: „✓ Visos kameros suderintos (N)“ / „⚠ p/N kamerų suderintos —  <serial (filter)="">nesėkmė“ / „Aktyvuotas kadravimas — Perkalibruokite, kad suderintumėte (naudojamas visas jutiklis)“ / „Laukiama, kol ekspozicija stabilizuosis…“ / „Taikomi nustatymai…“ (kameros paleidžiamos iš naujo) / „Matrica nustojo siųsti kadrus — iš naujo prijunkite matricą“.
* Lentelė pagal kameras: kamera (paskutiniai 4 serijos simboliai + filtras), reprojekcijos paklaida pikseliais su atitikimų skaičiumi („ref“ pagrindinei kamerai) ir sutapimo normalizuotos kryžminės koreliacijos balas, palygintas su 0,35 praeinimo riba.
* **Pakalibruoti suderinimą** mygtukas (prieš pirmąjį profilį rodomas užrašas „Kalibruoti suderinimą“) — pakartotinai atlieka bendrą registraciją su naujais kadrais.
* **„Automatiškai eksponuoti kameras suderinimui“** žymės langelis (pagal numatytuosius nustatymus pažymėtas) — laikinai pašviesina tamsias arba plokščias kameras (pirmiausia ekspozicija, tada stiprinimas), kad jos turėtų tekstūrą, kurią būtų galima suderinti, tada atkuria automatinę ekspoziciją.

Chloros automatiškai suderina kiekvieną masyvą po prisijungimo abiejuose rodymo režimuose, kai tik narių automatinė ekspozicija stabilizuojasi (bandoma iki 5 minučių); suderinamas ir sujungtas peržiūros vaizdas atidarant. Atskiras masyvas neturi sudėtinės plytelių, todėl ši sekcija tikrina suderinimo būseną ir pati paleidžia „Recalibrate“. Atlikite „Recalibrate“, jei pasikeitė fokusas arba scenos gylis. Suderinimas **pagal dizainą galioja tik sesijos metu** — jis niekada nėra išsaugomas profilyje, nes priklauso nuo tuo metu esančio atstumo iki scenos. Įrašus vis tiek galima eksportuoti su pikselių registracija (žr. [Suderinti eksportai](capture.md#per-array-controls)).

### Išmanusis vinjetės koregavimas

* Varnelė **„Įjungti korekciją“**— taiko kiekvienai kamerai atskirą vinjetės įvertinimą radiometrinei grandinei (tiesioginiam vaizdui**ir** eksportams).
* **Kalibruoti pagal dabartinį vaizdą**— pirmiausia nukreipkite matricos elementus į vienalytį taikinį (plokščias ekranas, siena arba dangus); kiekviena kamera išlyginama atskirai, o būsenos ataskaitoje nurodomas išlyginimo laimėjimas „n/N kamerų · −x,x %“. Paspaudus**Išvalyti** pašalinamas įvertinimas.
* Būklė: „Kalibruota“, „Nekalibruota“ arba „Iš dalies kalibruota — pakartokite“, kai tik kai kurių kamerų įvertinimai vis dar taikomi esant dabartiniam kadravimui (pavyzdžiui, po to, kai funkcija „Pakalibruoti“ perkelė kameros kadravimo langą) — vėl paleiskite „Kalibruoti“.
* Atlikite tikslią kiekvienos kameros korekciją naudodami kameros **Vignette** slankiklį [Tiesioginiame peržiūros režime](#live-preview).

### Tiesioginis peržiūros langas

* **Histogramos**žymės langelis + pločio slankiklis (0,10–0,90 kadro) — tiesioginiame sraute rodoma histogramos juosta kiekvieno nario plytelių srityje iš karto (įskaitant atskirtų kanalų plyteles), o [Histogramos perdanga](#display-overlays-drawn-over-the-live-feed), nustatyta visam masyvui. Ji rodoma kaip įjungta, kai kiekvienas narys jau ją turi.**Atskirame** masyve tai yra vienintelė eilutė grupėje.
* *Tik sujungus:* **Indeksas**: pažymėkite langelį + ratukas — atidaro bendrą [Indekso skaičiuoklę](#index-calculator-pane) su juostomis, nubrėžtomis iš**visų** narių kamerų. Eilutė išraiškos peržiūrai apačioje rodo dabartinę išraišką („Išraiška nenustatyta — atidarykite skaičiuoklę, kad ją sukurtumėte“), atnaujinamą kas sekundę.
* *Tik kombinuotame režime:* **Renderio skiriamoji geba**išskleidžiamasis meniu (tie patys nustatymai kaip ir kiekvienai kamerai atskirai, numatytasis 720p): tiesioginio vaizdo srauto aukštis**ir** išsaugoto sudėtinio vaizdo eksporto dydis. Pastaba lange: „Peržiūra + išsaugoto sudėtinio vaizdo dydis. Kiekvienos kameros vaizdai visada eksportuojami pilna skiriamąja geba.“

### Rodymo sluoksniai *(tik sujungus)** **Įjungti** žymės langelis (numatyta išjungta — pagrindinė kamera rodoma tiesiogiai; įjungta = sluoksniuotas kompozitas).
* **Išskleidžiamieji meniu**„Pirmame plane“**/**„Fone“**: kiekviena nario kamera (pagal pavadinimą) arba**„Indeksas“**. Kai „Pirmame plane“ pasirinkta „Indeksas“, pikseliai už LUT minimalių/maksimalių ribų rodo „Fono“ sluoksnį.

### Padalintas vaizdas *(tik sujungtas)*

**„Rodyti narių kameras“**— mygtukas**„Padalyti / Paslėpti kameras“**, kuris kiekvienos kameros tiesioginį vaizdą prideda kaip atskiras tinklelio plyteles šalia kompozicijos. Plytelės naudoja masyvo esamą kadrų buferį (nereikia papildomo kameros prijungimo). Tik tinklelinis vaizdas; išsaugoma pagal masyvą kartu su projektu.

### Galimybės

Tik skaitymo režimu veikiantis skydelis, atnaujinamas kas 5 s:

* **Lygio pavadinimas**: „Vienalaikis fiksavimas“ (žalia) · „Vienalaikis fiksavimas (FTD – pakopinis spinduliavimas)“ (žalia) · „Pakopinis fiksavimas (100 ms nuokrypis)“ (gintarinė) · „Konfigūracija per didelė“ (raudona).
* **Kadro būklė**: „x,xx % neužbaigta“ — žalia, jei mažiau nei 1 %, gintarinė, jei mažiau nei 5 %, raudona, jei 5 % ar daugiau.
* **Ryšio linija**: „NIC {mbps} Mbps – nuolatinis {MB/s} MB/s“.

Tai yra masyvo realaus laiko pralaidumo biudžetas. Dėl pagrindinių kadrų per sekundę (fps) ir tinklo modelio — bei to, ką reikia keisti, kai lygis tampa gintaro arba raudonos spalvos — žr. [Daugiakamerinius masyvus](arrays.md) ir [CLI žinyną](../reference/cli-reference.md).

<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->

## Langas „Indekso skaičiuoklė“

Trečiasis šoninės juostos puslapis, bendras tiek kameros indekso nustatymams, tiek jungtinio masyvo indekso nustatymams (vienu metu rodomas tik vienas – antraštėje rašoma „Indekso skaičiuoklė — <camera name="">“ arba „Indekso skaičiuoklė — <array name="">“). Jame rodomas juostų sąrašas (kameros filtronatūralieji juostų diapazonai arba visos juostos visose masyvo kamerose), dabartinį išraiškos šabloną ir LUT konfigūraciją (įjungta/išjungta, lygis – numatytasis 7 stopai, min. – numatytasis 0,2, maks. – numatytasis 1), taip pat tiesioginį indekso histogramą. Paspaudus **Taikyti** išraiška įgaliojama; LUT pakeitimai iš karto taikomi peržiūrai.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Kameros nustatymai ir masyvo valdomi nustatymai

Trumpas vadovas, kur kas yra, kai kamera yra masyvo narys:

| Masyvo valdomi (kameros lange tik skaitymo režimu) | Vis dar kameros nustatymai masyvo viduje |
| --- | --- |
| Pikselių formatas, skiriamoji geba, binningas | Automatinė ekspozicija (ekspozicija, stiprinimas, tikslas, išlyginimas, ROI) |
| Sužadinimo režimas/šaltinis, kadrų dažnis | Triukšmo mažinimas, ryškumas, vinjetė |
| | Orientacija (veidrodinis atspindys/pasukimas), ekrano antraštės (masyvo nustatymai → „Live Preview“ vienu metu nustato histogramą kiekvienam nariui), taškinis matuoklis |
| | Indeksas (atskirai rodomi masyvai), šviesos jutiklio susiejimas |

Kiti bendri elgesio ypatumai:

* **Kombinuotas vs atskiras rodymas** pasirenkamas jungiant masyvą: kombinuotas = viena suderinta sudėtinė plytele (elementai perduoda vaizdą tik per „Split View“); atskiras = kiekvienas elementas atkuria savo sinchronizuotą plytele. Kamera niekada nerodo ir atskiro vaizdo, ir masyvo plytelės.
* **Automatinis pakartotinis prisijungimas**: atidarius išsaugotą projektą atkuriamos jo kameros ir masyvai, o prieš atnaujinant srautus visi išsaugoti nustatymai iš naujo pritaikomi serveriui.
* **Įrašymo filtravimas**: paslėptos arba pristabdytos kameros neįtraukiamos į „Capture All“; masyvas visiškai blokuojamas tik tada, kai VISI nariai yra paslėpti arba pristabdyti. Žr. [Įrašymo nustatymai ir režimai](capture.md).

## Kaip išsaugomi nustatymai

Kameros skirtuko būsena išsaugoma **kartu su projektu**, o ne naršyklėje:

* Kiekvienas reaktyvusis pakeitimas sukuria kamerų ir masyvų momentinę kopiją projekto `cameras.json` (su 500 ms atsparumu atsiliepsniams). Tai apima kamerų pavadinimus ir spalvas, ekspozicijos/stiprinimo/AE nustatymus, pikselių formatą/skiriamąją gebą/binningą, sužadinimo dažnį, peržiūros nustatymus (atvaizdavimo skiriamąją gebą, triukšmo slopinimą, ryškumą, vinjetę, spalvų profilį, šviesias sritis, prisotinimą/kontrastą), orientaciją, perdangas, kanalų padalijimus, indekso konfigūraciją, prognozuojamojo AE nustatymus, AE ROI, matricos pavadinimus, rodymo režimą, matricos fiksavimo nustatymus (įskaitant ROI apkarpymą, ar ROI yra įjungta, ir įsimintą apkarpymą, kai ji išjungta), matricos „Wire Budget“ ir tinklelio bloką (vaizdo priartinimą, peržiūros režimą, tinklelio fiksavimą, rankinę plytelių tvarką, paslėptas kameras, uždarytas plyteles, aktyvią kamerą).
* Šviesos jutiklių priskyrimai išsaugomi projekto faile „`sensors.json`“.
* Vėl atidarius projektą, aparatinė įranga vėl prijungiama ir visi nustatymai vėl pritaikomi.
* **Jei projektas neatidarytas = tik sesija**: jei nėra projekto, uždarius Chloros niekas neišlieka.
* Tik sesijai, nepriklausomai nuo projekto: sustabdymo būsena, taškinio matuoklio mėginiai, žymės langelis „Kalibravimo taikinys“ kiekvienai kamerai (visada atidarytas išjungtas) ir matricos suderinimo profilis (pagal projektą perskaičiuojamas kiekvienai sesijai).
* Viena išimtis: **„Fiksavimo nustatymai“** eksporto pasirinkimai ir fiksavimo režimas išlieka kiekvienam projektui vietinėje programos saugykloje, o ne `cameras.json` — žr. [Fiksavimo nustatymai ir režimai](capture.md).</array></camera></serial>
