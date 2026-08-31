# Kameros nustatymai

Skirtukas **„Kameros“**yra „Chloros“ tiesioginio valdymo pultas, skirtas „LATTICE“ kameroms: pagrindinė vaizdo srauto sritis, kurioje kiekviena prijungta kamera rodoma kaip tiesioginis langelis, ir šoninė juosta, kurią galima perkelti tarp trijų puslapių –**kamerų sąrašo**,**nustatymų skydelio**(nustatymai pagal kamerą, masyvą arba įrašymo nustatymai — po vieną), ir**indekso skaičiuoklė**. Šiame puslapyje aprašomi visi valdikliai, esantys kamerų sąraše, nustatymų skydelyje pagal kamerą ir masyvo nustatymų skydelyje. Įrašymo režimai, eksporto tipo pasirinkimas ir „Capture All“ (Įrašyti viską) procesas aprašyti gretimame puslapyje [Įrašymo nustatymai ir režimai](capture.md).

Skirtukas „Kameros“ pasirodo šoninėje juostoje, kai Chloros užkulisinė sistema yra paruošta. Visi žemiau esantys valdikliai bendrauja su vietine užkulisine sistema per `127.0.0.1:5000`; pakeitimai iš karto taikomi tiesioginiam kameros vaizdui, jei nenurodyta kitaip.

## Šiame puslapyje naudojami kamerų tipai

Valdymo elementai rodomi arba paslepiami priklausomai nuo to, kokio tipo kamera yra pasirinkta. Vadove visur vartojami šie terminai:

| Terminas | Reikšmė | Filtrų kanalai |
| --- | --- | --- |
| **RGB kamera** | „LATTICE M3C“ su FRGB filtru (modelyje yra `-FRGB`) | Red / Green / Blue |
| **Bayer multispektrinė** | „LATTICE M3C“ su „FRGN“, „FOCN“ arba „FNGB“ | „FRGN“: Red / Green / NIR · „FOCN“: Orange / Cyan / NIR · FNGB: NIR / Green / Blue |
| **Mono (M3M)** | LATTICE M3M — vienas siaurajuostis filtras, viena kalibruota juosta | Viena juosta |
| **Matricos narys** | Kamera, prijungta kaip sinchronizuotos matricos dalis (kombinuotas arba atskiras ekranas) | Pagal savo filtrą |

RGB kameros atlieka fotometrinį apdorojimą (baltos spalvos balansą, spalvų profilius, gama); daugiaspektrinėms ir mono kameroms taikoma radiometrinė grandinė, o fotometriniai nustatymai praleidžiami. Masyvo nariai perduoda srauto lygio nustatymus (pikselių formatą, skiriamąją gebą, pikselių sujungimą, sužadinimą, kadrų dažnį) į masyvą — šios eilutės tampa tik skaitymo režimu atskirų kamerų lange ir perkeliama į masyvo nustatymų langą.

## Pagrindinė vaizdo srauto sritis

<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->

Kai nėra prijungtų kamerų, vaizdo srauto srityje rodomas pradinis ekranas **„Prijunkite kamerą, kad galėtumėte pradėti“**su dviem mygtukais:**„Prijungti kamerą“**(žalias, atidaro vienos kameros prijungimo dialogą) ir**„Prijungti masyvą“** (mėlynas, atidaro masyvo prijungimo dialogą). Patys prijungimo dialogai aprašyti skyriuje [Kamerų prijungimas](connecting.md); masyvo sąvokos (sinchronizavimas, lygiai, pralaidumas) – [Daugiakameros masyvai](arrays.md). Atidarius išsaugotą projektą, kuriame yra kamerų, pradžios ekrane vietoj to rodomas sukasi ratukas su užrašu „Iš naujo atidaroma N išsaugotų kamerų…“, o Chloros atkuria srautus iš paskutinės sesijos.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Viršutinė juosta

| Valdymo elementas | Ką daro |
| --- | --- |
| **Perjungimas tarp peržiūros režimų**| Perjungia tarp**tinklelinio vaizdo**(visos plytelių formos langeliai) ir**sąrašo vaizdo** (matricos užima visą viršutinę dalį, po jomis – VIENA aktyvi kamera). Įrankio patarimai: „Perjungti į tinklelinį vaizdą“ / „Perjungti į sąrašo vaizdą“. |
| **Tinklelio fiksavimas**(spynelė) | Numatytasis nustatymas**užfiksuotas** — plytelės nejudamos. Atrakinkite, kad galėtumėte perkelti plyteles į bet kurią vietą (tarpai išlieka). Tinklelis automatiškai vėl užfiksuojamas kiekvieną kartą, kai prisijungia nauja kamera. Pagalbinės užuominos: „Atrakinėti tinklelį (įjungti plytelių vilkimą)“ / „Užrakinti tinklelį (įšaldyti plyteles vietoje)“. |
| **Srauto mastelio** slankiklis | Plytelių dydis – nuo 60 pikselių iki viso konteinerio pločio. Ląstelės išlaiko 4:3 kraštinių santykį. Jei langelio plotis mažesnis nei 200 pikselių, pavadinimas ir kadrų dažnio (fps) antraštės paslepiamos, kad langelis atrodytų tvarkingas. |

### Srauto langeliai

Kiekviena kamera atkuria sudėtinį tiesioginį langelį; kamera taip pat gali rodyti tris pilkos skalės **pagal kanalus suskirstytus** langelius (žr. [Kanalų suskirstymas](#display-overlays-drawn-over-the-live-feed)), o matricos rodo sujungtą plytelių vaizdą. Aktyvioje plytelių vaizde matomas pasirinkimo žiedas kameros (arba matricos) spalva.

Užvedus pelę ant plytelių vaizdo, pasirodo uždarymo mygtukas **X**:

* Uždarius **sudėtinį** langelį, kai jo kanalų padalijimai lieka rodomi, tiesiog paslėpiamas sudėtinis vaizdas.
* Uždarius **paskutinį matomą atskiros kameros langelį**, ta kamera atjungiama.
* **Sudėtinės kamerų grupės narių padalijimo langeliai niekada neatjungia** kameros — jie tik paslepia vaizdą.

Atrišus tinklelį, bet kurią plytelių galite perkelti į bet kurią vietą; išdėstymas išsaugomas kartu su projektu.

## Šoninė juosta — kamerų sąrašas

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

Pirmoje šoninės juostos puslapyje pateikiamas visų prijungtų kamerų ir masyvų sąrašas:

* **Prijungti kamerą**(žalia) /**Prijungti masyvą** (mėlyna, skenavimo metu rodo „Aptinka....“). Abu mygtukai neveikia, kol atidarytas prisijungimo dialogas.
* **Įrašyti viską** (raudonas) — įrašo vaizdą iš visų sąraše esančių kamerų, taikant „Įrašymo nustatymuose“ pasirinktus eksporto tipus. Reikia atidaryto projekto. Išsamiai aprašyta skyriuje [Įrašymo nustatymai ir režimai](capture.md).
* **„Įrašymo nustatymų“ ratukas** (šalia mygtuko „Įrašyti viską“) — atidaro [„Įrašymo nustatymų“ langą](capture.md#the-capture-settings-pane). Neveikia, jei nėra projekto arba vyksta įrašymas.

### Kamerų eilutės

Kiekvienoje kameros eilutėje rodoma spalvotu kodu pažymėta riba (kameros pasirinktinė spalva), užrašas „CAM“ — su mėlyna **M**(pagrindinė) arba žalią**S** (pagalbinė) – rodo masyvo narių vaidmenį – bei ekrano pavadinimą. Numatytasis pavadinimas yra `LATTICE-MODEL (serial)`; jį galima pervardyti kamerų nustatymų lange. Eilutės mygtukai:

| Mygtukas | Poveikis |
| --- | --- |
| **Akių simbolis**| Įjungti / išjungti matomumą. Paslėptos kameros išnyksta iš tinklelio ir yra**neįtraukiamos į „Capture All“**. |
| **Ratukas** | Atidaryti kiekvienos kameros nustatymų langą (kitas skyrius). |
| **Pauzė / Grojimas**| Sustabdyti tiesioginį peržiūros vaizdą**tik ekrane** — vaizdo įrašymas serveryje tęsiasi. Pauzuotos kameros negali įrašyti. |
| **X** | Atsijungti. Vartotojo sąsaja atnaujinama iškart (optimistinis scenarijus); pats atsijungimas fone gali užtrukti 10–30 s. |

### Masyvo eilutės

Masyvo eilutėje rodomas „ARRAY“ ženklas masyvo spalva, masyvo pavadinimas (jį galima pervardyti masyvo nustatymuose) ir **DAQ · įjungta/išjungta**mygtukas —**įjungta**, kai nustatytas masyvo lygio šviesos jutiklis *arba* bet kuris narys turi kameros jutiklį; jo paaiškinime nurodoma, kuris jutiklis perduoda kokią informaciją. Masyvo narių kameros išvardytos po juo, atitrauktos į atskiras eilutes. Masyvo eilutės mygtukai: **akis**(paslepia/parodo VISUS narius kartu),**ratas**(masyvo nustatymų langas),**X**(atsijungia visą masyvą).

Šviesos jutiklio (DLS) būsena, naudojama masyvo eilutėse ir masyvo nustatymų lange, turi keturias būsenas:**išjungta**,**laukiama**(dar nėra spektro),**aktyvus**(spektras gautas per paskutines 3 s) ir**pasenęs** — per 3 s naujo spektro negauta, bet paskutinis rodmuo *vis dar naudojamas* (DAQ rodmenys fiksavimo kelyje niekada nepasensta).

Šoninėje juostoje galite perkelti atskiras kameras ir visus masyvo grupes viena pro kitą, kad pakeistumėte sąrašo tvarką; masyvo narių atskirai perkelti negalima.

## Nustatymų langelis kiekvienai kamerai

Atidarykite spustelėdami **ratuką** kameros eilutėje. Langas pasislenka virš kamerų sąrašo.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->

**Antraštė**: kameros**spalvų pavyzdys**(spustelėkite, kad atidarytumėte įprastą spalvų pasirinkimo langą — nustato šoninės juostos apvado ir plytelių pasirinkimo žiedo spalvą),**pavadinimas**su pieštuku**Pervardyti**(išsaugojus tuščią pavadinimą, grįžtama prie numatytojo `MODEL (serial)`) ir**×** uždarymui.

### Tiesioginis histogramas

Langelio viršuje yra tiesioginis šviesos histogramas, apskaičiuojamas iš JPEG peržiūros ~8 Hz dažniu. Vidurkis yra svertinis pagal Bayer metodą — (R+2G+B)/4 — kad atitiktų pačios kameros AE matavimą.

* **Orange punktyrinė linija**= AE tikslas.**Vilkite ją horizontaliai, kad pakeistumėte tikslą** — atleidus pelę siunčiamas vienas komandos signalas, o vilkdami perjungiate AE tikslo režimą į „Rankinį“.
* **Green ištisinė linija** = faktinė vidutinė šviesos vertė (kurią šiuo metu užtikrina AE).
* **RGB mygtukas** (dešinėje viršuje): perjungia atskirų juostų perdengiamus histogramus, nuspalvintus pagal fotoaparato filtrą (pvz., FRGN režime: pilka NIR, žalia, raudona). Mono (M3M) kamerose mygtukas pažymėtas „MONO“ ir yra išjungtas — mono režimu visada rodomas vienos juostos šviesos histogramos vaizdas.
* X ašies etiketės atitinka dabartinio pikselių formato jutiklio bitų gylį: 0..255, 0..1023, 0..4095 arba 0..65535.

### Kameros informacijos eilutės

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->

| Eilutė | Veikimas |
| --- | --- |
| **Modelis** | Tik skaitymo (pvz., `LATT-M3C-L87-FRGN`). |
| **Radiometrinis kalibravimas**| Green**„Aktyvus“**ženkliukas su antrašte, rodančia kalibravimo lygį, hash kodą, kalibravimo datą ir juostų sąrašą, įkeltą iš kameros kalibravimo paketo (žr. [Gamyklinis radiometrinis kalibravimas](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)).**Paslėpta RGB kamerose** — jose atliekama fotometrinė baltos spalvos balanso kalibravimas, o ne spinduliavimo stiprumo kalibravimas pagal juostas. |
| **Kalibravimo ataskaita**|**Atsisiųsti** mygtukas — atidaro kameros serijinio numerio NIST kalibravimo sertifikatą PDF formatu jūsų operacinės sistemos peržiūroje. Jei sertifikatas dar nėra išsaugotas talpykloje, Chloros vietoj to rodo užuominą. |
| **Serijinis numeris** | Tik skaityti. |
| **Programinė įranga**| Rodo dabartinę versiją, tada nustato šiam modeliui prieinamą versiją (išsaugoma pagal modelį — N kamerų masyvas patikrina serverį vieną kartą). Rodo: „Tikrinama…“ → mygtukas**„Atnaujinti į X“**→ „Atnaujinama…“ → „Atnaujinta iš A į B“ / „Nepavyko: …“ / „Praleista: …“ / žalia**„Atnaujinta“**. Atnaujinimo mygtuko paaiškinimas: „Gamyklinių nustatymų atkūrimas + programinės įrangos įrašymas + „UserSet1“ perprogramavimas. ~2–3 minutės; neatjunkite.“ |
| **IP** | Tik skaitymo režimas. |
| **Temperatūra** | Tik skaitymo režimas, atnaujinama kas 3 s. Tampa oranžinė, kai temperatūra ≥65 °C, o raudona su ⚠ ženklu, kai ≥75 °C. |
| **Kalibravimo taikinys** žymės langelis | Įjungia „ArUco“ atspindžio taikinio aptikimą su kiekvienam skydeliui skirta NDVI patvirtinimo lentele po tiesioginio vaizdo (sąrašo peržiūra). Veikia tik sesijos metu — visada atidaryta išjungta. |
| **Šviesos jutiklis** išskleidžiamasis meniu | Susieja DAQ šviesos jutiklį (DAQ-E/M/U, iš skirtuko „Šviesos jutikliai“ sąrašo) su šia kamera, kad būtų atliekama žemyn nukreiptos šviesos (DLS) apšvietimo korekcija ir prognozuojama automatinė ekspozicija. Pasirinkus „Nėra“ susiejimas panaikinamas. Jei jutikliai neprijungti, išskleidžiamajame meniu rodomas pranešimas „(jutikliai neprijungti — atidarykite DAQ skirtuką)“. Susiejimas išsaugomas kartu su projektu. |

### Ekspozicija ir jautrumas

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Visi čia esantys skaitmeniniai įvesties laukeliai naudoja „laikyk ir pagreitink“ sukimo ratukus: bakstelėjimas = ±1, laikymas &gt;1,5 s = ±10, laikymas &gt;3 s = ±100. Vertė siunčiama į kamerą, kai atleidžiate.

| Valdymas | Diapazonas / pasirinkimai | Numatytasis | Taikoma | Ką daro |
| --- | --- | --- | --- | --- |
| **Ekspozicija (us)**| Kameros realaus laiko min./maks. | Automatinis | Visi | Ekspozicijos trukmė mikrosekundėmis, su**Automatinis/Rankinis** perjungikliu. Automatinis = nuolatinė kameros pusės automatinė ekspozicija. |
| **Jautrumas (dB)**| Kameros realaus laiko mažiausia/didžiausia vertė (pvz., iki 48 dB) | Rankinis (išjungta) | Visi | Analoginis/skaitmeninis jautrumas su atskiru**Automatinis/Rankinis** perjungikliu. |
| **AE tikslinis ryškumas**| 0–255 | 80, režimas**Automatinis**| Visi (redaguojama, kai įjungta AE arba automatinis stiprinimas) | Ryškumas, kurio siekia AE.**Automatiniame**režime (numatyta reikšmė) histogramos pagrindu veikiantis valdiklis pats parenka tikslą, išlaikydamas ekspoziciją 60–75 % jutiklio maksimumo lygyje. Įvedus reikšmę arba vilkdami histogramos oranžinę liniją, perjungiate į**Rankinį** režimą. |
| **AE išlyginimas** | 0,5–40, žingsnis 0,1 | 8,0 | Visi | AE slopinimas. Įrankio patarimas: „Mažesnė vertė = AE reaguoja greičiau (gali pulsuoti esant dideliam kadrų dažniui). Didesnė reikšmė = sklandesnis / lėtesnis.“ Reikšmės, gerokai mažesnės už numatytąją, gali sukelti AE pulsavimą ir destabilizuoti transliaciją esant dideliam kadrų dažniui; 8,0 yra stabili numatytoji reikšmė. |
| **AE domėjimosi sritis**| Žymės langelis „Įjungti“ + mygtukas**Nukreipti**| Išjungta | Visi | Kai įjungta, AE matuoja tik žalią punktyrinę sritį, o ne visą kadrą.**Tikslas** įjungia funkciją „paspaudimu nustatyti vietą“ tiesioginiame transliavimo vaizde: paspaudus sritis centruojama 30 % kadro vietoje; paspaudus ir vilkdami nubrėžiate pasirinktinį stačiakampį (mažiausiai 5 % × 5 %). Funkcija „Tikslas“ išsijungia po vieno nustatymo. Sritis atvaizduojama atgal į kameros natūralias koordinates pagal bet kokį jūsų nustatytą pasukimą ar veidrodinį atspindį ir išsaugoma kartu su projektu. |
| **AE greičio reguliavimas** | 0,1–5, žingsnis 0,1 | 1,0 | Tik masyvo nariams | Kaip greitai automatinis AE tikslas seka scenos ryškumo pokyčius; esant 1,0×, patikrinimas atliekamas kas 2,5 s. |
| **Šviesių sričių apsauga** | Griežta (1 %) / Įprasta (5 %) / Švelni (15 %) | Griežta | Kameros, kuriose šis parametras yra nustatomas | Kiek kadro ploto gali būti apkarpyta iki baltos spalvos, kol AE patamsins vaizdą. |

{% hint style="info" %}
**Šviesos reikalavimas „Bayer“ multispektrinėms kameroms (RGN / OCN / NGB):** scenoje turi būti pakankamai šviesos visuose trijuose kanaluose, kitaip kalibravimas neveiks tinkamai — vieno jutiklio ekspozicija apima visus tris spektrus. Naudokite DAQ šviesos jutiklį šviesos matavimui arba perjunkite į visiškai monokromatinį režimą (M3M) režimą, kad kiekvienai juostai būtų skirta atskira ekspozicija. Jei fotografuojant šis reikalavimas pažeidžiamas, „Chloros“ tai aptinka ir įspėja jus (pranešimas „unmix-clamp“).
{% endhint %}

### Pikselių formatas ir skiriamoji geba**Masyvo

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

nariai** rodo tik skaitymo režimu veikiančias eilutes „Dabartinis“ (formatas + WxH) ir „Binning“ su pastaba „Nustatyta masyvo nustatymuose“ — srauto paleidimas iš naujo viename elemente sutrikdytų sinchronizaciją, todėl šie parametrai valdomi [masyvo nustatymų lange](#array-settings-pane).**Atskiros kameros** gauna:

| Valdymas | Pasirinkimai | Ką tai daro |
| --- | --- | --- |
| **Pikselių formatas** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Jutiklio pikselių formatas (bitų gylis). |
| **Skiriamoji geba** | Pilna / Pusė / Ketvirtis | Santykinė, atsižvelgiant į esamą pikselių sujungimą: Pilna = 2048/N × 1536/N, kai taikomas N×N pikselių sujungimas. |
| **Pikselių sujungimas** | 1x1 (nėra) / 2x2 / 4x4 | Aparatinis N×N pikselių sujungimas — didesnės reikšmės sumažina skiriamąją gebą, bet padidina signalo ir triukšmo santykį (SNR) bei kadrų dažnį. Pakeitus šią reikšmę, srautas paleidžiamas iš naujo, o bet koks domėjimosi sritis (ROI) nustatoma pagal naują visą matymo lauką. |
| **Dabartinis** | tik skaityti | Faktiniai galiojantys WxH ir (x, y) poslinkiai. |

### Tiesioginis peržiūros langas

Viskas šiame skyriuje yra **tik ekrano pusėje**— tai keičia tai, ką matote tiesioginiame sraute, o išsaugoti kadrai išlieka linijiniai ir nepakeisti — su viena išimtimi:**Vignette** yra radiometrinis ir taip pat veikia eksportuojamus failus (paaiškinta žemiau).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->

| Valdymas | Diapazonas / pasirinkimai | Numatytasis | Taikoma | Ką daro |
| --- | --- | --- | --- | --- |
| **Renderinimo skiriamoji geba** | 360p (greičiausia) / 480p / 720p / 1080p / Natūrali jutiklio skiriamoji geba (lėčiausia) | 720p | Visiems | Aukštis, kuriame užkulisinė sistema vykdo radiometrinę peržiūros grandinę. Mažesnė reikšmė užtikrina didesnį kadrų dažnį nekeičiant matymo lauko. |
| **Indeksas**| Žymės langelis „Įjungti“ + ratukas | Išjungta | Tik „Bayer“ multispektrinė kamera,**ne** kombinuotos matricos kameros | Tiesioginė augmenijos indekso peržiūra. Paspaudus ratuką atsidaro bendrai naudojamas [Indekso skaičiuoklė](#index-calculator-pane), kuriame iš anksto įkelti kameros filtrų natūralieji juostos (pvz., `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`). Kiekviename peržiūros kadre apskaičiuojama pasirinktinė išraiška ir LUT (įjungta/išjungta, numatytas lygis 3, numatytas minimumas 0,2, numatytas maksimumas 1). Kombinuotų matricos narių atveju ši eilutė paslėpta — matrica turi vieną bendrą indeksą. |
| **Baltojo balanso nustatymas** | Išjungta / Vienkartinis / Nuolatinis + pakartotinio fiksavimo mygtukas | Nuolatinis | Tik RGB | Baltojo balanso nustatymas realiuoju laiku. Atnaujinimo mygtukas pakartotinai fiksuoja baltojo balanso nustatymą iš dabartinio DLS spektro (išjungta, kai režimas yra „Išjungta“). |
| **Gama** | Įjungta / Išjungta | Įjungta | Tik RGB | Rodo gamą (γ = 2,2 LUT) tiesioginiame peržiūros ekrane. Išsaugoti kadrai išlieka linijiniai. |
| **Triukšmo mažinimas** | Varnelė + stiprumas 0–100 | Išjungta / 50 | Visi (kiekvienai kamerai atskirai, net ir masyvuose) | Dvipusis filtras tiesioginiame peržiūros lange. Didesnis stiprumas = sklandesnis, bet švelnesnis vaizdas. |
| **Ryškumas** | Varnelė + stiprumas 0–100 | Išjungta / 30 | Visi | Neaštraus kaukės taikymas tiesioginiame peržiūros ekrane, taikomas paskutinis. Gali sustiprinti triukšmą. Tik peržiūrai. |
| **Vignette**| Varnelė + stiprumas 0–100 | Išjungta / 0 | Visi | Rankinis likutinis vignette pašalinimas (išryškina kampus), uždedamas ant masyvo „Smart Vignette“ įvertinimo.**Radiometrinis — veikia tiesioginį peržiūros vaizdą IR eksportą**, skirtingai nei „Denoise“ / „Sharpness“. |
| **Spalvų profilis** | „Raw“ / „Linear“ / „Natural“ / „Enhanced“ / „Custom Temperature“ | „Natural“ | Tik RGB | Žr. žemiau. |
| **Spalvų temperatūra** | 2000–10000 K, žingsnis 100 | 5500 K | RGB, tik pasirinktos temperatūros profilis | Nustato baltos spalvos balansą pagal fiksuotą koreliuotą spalvų temperatūrą (DLS įvestis ignoruojama). Paskutinė pasirinkta Kelvino vertė išsaugoma keičiant profilius. |
| **Sotumas** | 0–200 (100 = neutralus) | 100 | Tik RGB | HSV sodrumas tiesioginiame peržiūros lange. |
| **Kontrastas** | 0–200 (100 = neutralus) | 100 | Tik RGB | Tiesinis kontrastas aplink vidutinę pilką spalvą tiesioginiame peržiūros lange. |
| **Veidrodinis H / Veidrodinis V** | Žymimieji langeliai | Išjungta | Visi | Apsukti peržiūrą horizontaliai / vertikaliai. |
| **Pasukimas**| 0° / 90° / 180° / 270° | 0° | Visi | Pasukti peržiūrą. Orientacija taikoma galutinėje peržiūros grandinės dalyje —**išsaugoti kadrai išlieka kameros natūralioje orientacijoje**, o sudėtiniai vaizdai jos neatsižvelgia. |**Spalvų profilio semantika** (RGB kameros):

* **Raw** — visiškai apeiti apdorojimo grandinę.
* **Linijinis** — tamsus signalas + lygus laukas + baltos spalvos balansas; be spalvų matricos, be gama.
* **Natūralus** *(numatyta)* — linijinis su išmatuota spalvų korekcijos matrica ir prie scenos prisitaikančia tonų kreive.
* **Patobulinta**— „Natūrali“ su gyvybingumu ir CLAHE vietiniu kontrastu. Papildoma kaina taikoma**tik tiesioginiam peržiūros režimui** — išsaugoti kadrai visada gauna pilną apdailą, nepriklausomai nuo profilio.
* **Pasirinktinė temperatūra** — „Natūrali“ su baltos spalvos balansu, pririštu prie jūsų pasirinktos Kelvino vertės.

{% hint style="warning" %}
Rinkus „Natūralus“, „Patobulintas“ ir „Pasirinktinė temperatūra“, lange rodoma pastaba apie toną: kadrai pašviesinami atsižvelgiant į jų sceną, todėl išsaugoti *ekrano* vaizdai nėra palyginami kadras su kadru. **Eksportuokite spinduliavimą arba atspindį matavimams.**
{% endhint %}

### Ekrano perdangos (nupieštos ant tiesioginio vaizdo)

Jos veikia tik vartotojo sąsajoje — nupieštos ant vaizdo, niekada nepaliesdamos srauto ar kadrų.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Perdanga | Valdymo elementai | Numatytasis | Ką daro |
| --- | --- | --- | --- |
| **Zebra** | Varnelė + riba 200–255 | Išjungta / 250 | Magentos spalvos įstrižinės juostos ant apkarpytų pikselių. |
| **Kryžminis taškas** | Varnelė | Išjungta | Kadrų centro žymė. |
| **Tinklelis** | Išjungta / 3 × 3 / 9 × 9 | Išjungta | Kompozicijos tinklelis. |
| **Histograma** | Varnelė + plotis 0,10–0,90 kadro | Išjungta / 0,25 | Histogramos juosta vaizdo sraute. |
| **Fokuso viršūnės** | Varnelė + riba 20–200 + spalvų pavyzdys | Išjungta / 80 / `#ff5722` | Sobelio krašto paryškinimas fokusavimui. |
| **Kanalų padalijimas** | „Rodyti padalijimus (Red / Green / NIR)“ / Mygtukas „Paslėpti padalijimus“ | Paslėpta | Prie sudėtinio vaizdo prideda tris nepriklausomus pilkosios skalės langelius kiekvienam kanalui (mygtuko pavadinimas atitinka kameros filtro kanalus). Kiekvieną padalijimo langelį galima vilkti, o jo spalva atitinka kameros rėmo spalvą. Nėra prieinama monokrominėse kamerose. Išsaugoma su projektu. |

### Taškinis matuoklis

* **„Spustelėkite, kad paimtumėte mėginį“**žymės langelis: spustelėkite tiesioginį vaizdą, kad paimtumėte vieno pikselio mėginį (jį pažymi kryžminis tinklelis), arba spustelėkite ir vilkite sritį, kad gautumėte pikselių vidurkį.**„Išvalyti“**ištrina mėginį ir tinklelį. Ši funkcija nesuderinama su AE-ROI**„Aim“** režimu.
* Išskleidžiamasis meniu **Rodyti**:**Neapdoroti duomenys (bitų gylis)**— natūralūs skaitmeniniai duomenys, atitinkantys jutiklio bitų gylį (pvz., 12 bitų → 0..4095) — arba**Ekranas (8 bitai)** (numatyta reikšmė). Kai aktyvus tiesioginis indeksas, „Display“ vietoj to rodo apskaičiuotą indekso vertę (pvz., NDVI).
* Rodymo skydelyje pateikiamos pikselių koordinatės, kadro dydis, pikselių formatas, bitų gylis ir kanalų lentelė (Chan / Value / %) su juostų pavadinimais ir bangų ilgiais; „Bayer“ žalios poros yra vidurkinamos; regionų mėginiai rodo „N px avg“.

Taškinio matuoklio būsena galioja tik sesijos metu.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Prognozuojama automatinė ekspozicija (valdoma DLS)

Šis skyrius rodomas tik tada, kai **prijungtas bent vienas DAQ šviesos jutiklis** — sprendiklis jam valdyti reikia tiesioginio žemyn nukreipto spektro.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->

| Valdymas | Diapazonas | Numatytasis | Ką daro |
| --- | --- | --- | --- |
| **Įjungti** | Varnelė | Įjungta (atskiros kameros) | Uždarosios formos sprendiklis naudoja DLS spektrą ir kameros kalibravimo paketo skaliarinius dydžius, kad ryškiausia juosta būtų arti prisotinimo, o silpniausia juosta išliktų virš SNR ribos — vienas ekspozicijos įrašymas per kiekvieną sprendimą, be stabilizavimo ciklo. Sukurtas saulėslaiko raidos fotografavimui, kai kiekvienas kadras turi būti teisingai eksponuotas. Sistema tyliai grįžta prie reaktyviosios automatinės ekspozicijos (AE), kai DLS rodmenys yra pasenę ar trūksta, arba kai kalibravimo paketas nėra įkeltas. |
| **Išlyginimas (α)** | 0,05–1,0, žingsnis 0,05 | 0,3 | Iš eilės einančių prognozuojamų sprendimų išlyginimas (kuo mažesnė reikšmė, tuo sklandesnis). |
| **Scenos atspindžio koeficientas**|**Pakalibruoti ρ** mygtukas | — | Iš naujo įvertina scenos atspindžio koeficientą, kurį naudoja sprendiklis. |

{% hint style="info" %}
**Masyvo jungimas pagal numatytuosius nustatymus išjungia prognozuojamą AE** — masyvų atveju ekspoziciją reguliuoja Chloros „išmanioji AE“ ir kameros automatinė ekspozicija (su prisotinimo apsauga), o prognozuojamos AEvienintelis scenos atspindžio įvertinimas nėra patikimas mišriose scenose. Čia galite jį vėl įjungti kiekvienai kamerai atskirai, jei konkrečiai norite DLS valdomos radiometrinės ekspozicijos.
{% endhint %}

**DAQ valdomos ekspozicijos riba ir nuo krintančios šviesos priklausoma AE.**Neatsižvelgiant į aukščiau nurodytą žymės langelį, kai DAQ šviesos jutiklis priskiriamas RGB kamerai, Chloros apskaičiuoja — remiantis išmatuotu absoliučiu žemyn nukreiptu spinduliavimo— maksimalią ekspoziciją × stiprinimą, kuriuo 100 % atspindžio paviršius išlieka žemiau apkarpymo ribos, ir taiko ją kaip**ribą**automatinei ekspozicijai. Kol riba aktyvi, kamera veikia**pririšta prie krintančios šviesos**: ji veikia atvirojo kontūro režimu, ekspoziciją nustatydama pagal išmatuotą krintančią šviesą, o stiprinimą nustatydama 0 dB — ekspozicija seka išmatuotą šviesą, o ne vaizdo turinį. Kadangi viršutinė riba gali tik sutrumpinti ekspoziciją, ji pati negali sukelti klipavimo. Riba automatiškai išjungiama — ir atnaujinama įprasta scenos automatinė ekspozicija — kai trūksta DAQ rodmenų, jie pasenę (&gt;30 s) arba tamsūs, arba jei ≥15 % kadro yra apkarpyta esant fiksuotai ekspozicijai (tai reiškia, kad jutiklis ir kamera mato skirtingą apšvietimą). GUI jungiklio nėra; tai yra standartinis elgesys, kai „RGB“ kamera yra susieta su DAQ.

### Duomenų surinkimo ir suaktyvinimo

<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

masyvo nariai papildomai rodo tik skaitymo režimu esančias eilutes **Vaidmuo**(Pagrindinis – mėlyna / Pavaldusis – žalia),**Sinchronizavimo linija**ir**Tarpusavio ryšiai**.

| Valdymas | Pasirinkimai | Numatytasis | Pastabos |
| --- | --- | --- | --- |
| **Triggerio režimas** | Išjungta / Įjungta | Įjungta | Masyvo nariams išjungta (masyvas valdo trigerių veikimą). |
| **Triggerio šaltinis** | Programinė įranga / Line0 (M8) / Line1 / Line2 | Line0 | Paslėpta, kai „Triggerio režimas“ yra išjungtas; neveikia masyvo nariams. Linija 0 yra M8 optiškai izoliuotas išorinis suaktyvinimo įėjimas. |
| **Kadrų dažnis**| Automatinis / Rankinis + vertė | Automatinis |**Automatinis**: kameros kadrų dažnio riba išjungta — ekspozicija nulemia kadrų skaičių per sekundę (fps), o langelyje rodomas tikrasis kadrų dažnis realiuoju laiku.**Rankinis**: kadrų dažnį ribojate slankikliu (nuo 1 iki maksimalaus, kurį leidžia pralaidumas), pradedant nuo dabartinio faktinio dažnio. Masyvo nariai mato tik skaitymo režimu rodomą „N kadrų per sekundę (gyvai)“ su užrašu „Nustatyta masyvo nustatymuose“. |

### Tinklas / Perdavimas

| Eilutė | Veikimas |
| --- | --- |
| **Paketo dydis**| 1500 (Standartinis) / 9000 (Jumbo) — numatytasis**Jumbo**. |
| **Pralaidumas** | Tik skaitymo režimu nustatoma jungties pralaidumo riba, išreikšta MB/s. Kiekvieną kartą prisijungus ar atsijungus, serveris iš naujo paskirsto šį pralaidumą tarp visų prijungtų kamerų. |
| **Buferio tvarkymas** | Tik skaitymo režimu nustatomas buferio tvarkymo režimas. |

### Įrašymas

Langą užbaigia mygtukas **„Atidaryti įrašymo nustatymus…“**, kuris nukreipia į [Įrašymo nustatymų langą](capture.md#the-capture-settings-pane) (neveikia, kol nėra atidarytas projektas — „Sukurkite arba atidarykite projektą, kad būtų galima išsaugoti įrašus“). Jei kamera paslėpta arba sustabdyta, patarimas primena, kad prieš pradedant įrašyti reikia ją vėl parodyti arba atnaujinti veikimą.

## Masyvo nustatymų langas

Atidarykite paspaudę **ratuką**ant eilutės „ARRAY“. Antraštė: masyvo pavadinimas su pieštuku, skirtu pervardyti, ir**×**, skirtas uždaryti. Toliau esančios sekcijos, pažymėtos *tik jungtiniai*, rodomos tik tada, kai masyvai sujungti jungtinio rodymo režimu.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->

### Sinchronizavimas

Tik skaitymo režimo **Pagrindinis**,**Pavaldiniai**ir**Sinchronizavimo linija** eilutės.

### Aplinkos šviesos jutiklis

Rodoma tiek sujungtiems, tiek atskiriems masyvams:

* Varnelė **Kalibravimo taikinys** — „Aptikti MAPIR ArUco taikinį ir patvirtinti NDVI atitiktį skydelio atspindžio LUT“; valdo sujungto bloko taikinio perdangą ir patvirtinimo lentelę.
* Išskleidžiamasis meniu **Šviesos jutiklis** — susieja vieną DAQ su visu masyvu. Pasirinkimas įsigalioja iš karto, perduodamas į kiekvienos masyvo kameros išskleidžiamąjį meniu „Šviesos jutiklis“ (vis dar galite pakeisti nustatymus kiekvienai kamerai atskirai) ir pradeda siųsti spektrus į masyvą.
* Tiesioginė **Būsenos** eilutė: Išjungta · „Laukiama pirmojo spektro…“ · „Veikia — visų masyvo kamerų apšvietimas pakoreguotas“ · „Per paskutines 3 s naujo spektro nebuvo — vis dar naudojamas paskutinis rodmuo (seno duomenų laiko limito nėra)…“.
* Pastaba lange: „Radiometrinė korekcija visam masyvui. Atskiri kamerų nustatymai pakeičia šiuos.“

### Įrašymas — vienodi jutiklio nustatymai *(taikomi tik kartu)*

Šie nustatymai vienodai taikomi kiekvienam masyvo nariui (atskiri kiekvieno nario pakeitimai sutrikdytų sinchronizaciją). Pakeitimai kaupiami ir taikomi kartu.

<!-- SCREENSHOT-NEEDED: array settings Capture section — Pixel Format, Binning, Resolution preset, the ROI crop W/H/X/Y fields with the "max WxH" hint and Reset button, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons; ideally with the live orange crop-preview box visible on the array tile. -->

| Valdymas | Pasirinkimai / diapazonas | Ką daro |
| --- | --- | --- |
| **Pikselių formatas** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Vienodas jutiklio formatas visiems elementams. |
| **Pikselių sujungimas** | 1x1 / 2x2 / 4x4 | Aparatinis pikselių sujungimas — išlaiko visą matymo lauką, tuo pačiu padidindamas signalo ir triukšmo santykį (SNR) bei kadrų dažnį. Pakeitus šį parametrą, ROI laukai iš naujo nustatomi pagal naują visą matymo lauką. |
| **Skiriamoji geba** (iš anksto nustatyta) | Pilna / Pusė / Ketvirtis | Priklauso nuo pikselių sujungimo; užpildo ROI laukelius su centruotu kadravimu. |
| **ROI kadras (px)**| W / H / X / Y skaitmeniniai laukai | Jutiklio kadras. Plotis ir aukštis pritaikomi prie 16 kartotinių (mažiausiai 64); poslinkiai pritaikomi prie 4 kartotinių. Užuomina „maks. WxH“ rodo viršutinę ribą, o mygtukas**Atstatyti** grąžina į visą matymo lauką. Redaguojant ant matricos plytelės nupiešiamas oranžinis iškirpimo peržiūros langelis (įskaitant viso jutiklio schemą, kai iškirpimas plečiamas į išorę). |
| **Sukėlimo dažnis**| Automatinis / Rankinis perjungimas + kadrų per sekundę (fps) 0,5–10, žingsnis 0,5 |**Automatinis**(numatyta): sistema apskaičiuoja sukėlimo dažnį pagal skiriamąją gebą ir pralaidumą — įvesties laukelis išjungtas ir rodo apskaičiuotą vertę.**Rankinis**: nustato jūsų nurodytą vertę paspaudus „Taikyti“. |

Pastaba lange: „Formato / skiriamosios gebos pakeitimai trumpam iš naujo paleidžia visas kameras. Trigerio dažnis taikomas iš karto.“ **Taikyti / Atšaukti** mygtukai yra lango apačioje.

### Suderinimas (bendras registravimas) *(tik kombinuotas)*

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* **Likutis**: „RMS x,xx px“ — žalia, jei mažiau nei 1 px, gintarinė, jei mažiau nei 3 px, raudona kitais atvejais arba jei kuri nors kamera nesuveikė; „nėra profilio“ prieš pirmąjį sprendimą.
* Apibendrinimo eilutė: „✓ Visos kameros suderintos (N)“ / „⚠ p/N kamerų suderintos —  <serial (filter)="">nesėkmė“ / „Aktyvuotas apkarpymas — Kalibruokite iš naujo, kad suderintumėte (naudojamas visas jutiklis)“ / „Laukiama, kol ekspozicija stabilizuosis…“.
* Lentelė pagal kiekvieną kamerą: kamera (paskutiniai 4 serijos numerio skaitmenys + filtras), reprojekcijos paklaida px su atitikimų skaičiumi („ref“ pagrindinei kamerai) ir persidengimo normalizuotos kryžminės koreliacijos balas, palygintas su 0,35 praeinimo slenksčiu.
* **„Pakalibruoti suderinimą“** mygtukas (prieš pirmąjį profilį rodomas užrašas „Kalibruoti suderinimą“) — iš naujo atlieka bendrą registravimą su naujais kadrais.
* **„Automatiškai eksponuoti kameras suderinimui“** žymės langelis (pagal numatytuosius nustatymus pažymėtas) — laikinai pašviesina tamsias arba plokščias kameras (pirmiausia ekspozicija, tada stiprinimas), kad jos turėtų tekstūrą, kurią būtų galima suderinti, tada atkuria automatinę ekspoziciją.

Sujungtas peržiūros vaizdas automatiškai suderinamas atidarius; pakalibruojamas, jei pasikeitė fokusavimas arba scenos gylis. Suderinimas **pagal dizainą galioja tik sesijos metu** — jis niekada nėra išsaugomas profilyje, nes priklauso nuo tuo metu esančio scenos atstumo. Įrašus vis tiek galima eksportuoti su pikseliniu suderinimu (žr. [Suderinti eksportai](capture.md#per-array-controls)).

### Pažangus vinjetavimas

* **Įjungti korekciją**žymės langelis — taiko kiekvienai kamerai apskaičiuotą vinjetavimo įvertį radiometrinei grandinei (tiesioginiam vaizdui**ir** eksportams).
* **Kalibruoti pagal dabartinį vaizdą**— pirmiausia nukreipkite kamerų masyvą į vienalytį taikinį (plokščią ekraną, sieną arba dangų); kiekviena kamera išlyginama atskirai, o būsenos ataskaitoje rodomas išlyginimo laimėjimas „n/N kamerų · −x,x %“.****Išvalyti** pašalina įvertinimą.
* Atlikite tikslinį koregavimą kiekvienai kamerai naudodami kiekvienos kameros **Vignette** slankiklį [Tiesioginėje peržiūroje](#live-preview).

### Tiesioginė peržiūra *(tik sujungta)** **Indeksas**: pažymėkite žymės langelį + ratuką — atsidarys bendras [Indekso skaičiuoklė](#index-calculator-pane) su juostomis, nubrėžtomis iš**visų** kamerų. Eilutė išraiškos peržiūrai apačioje rodo dabartinę išraišką („Išraiška nenustatyta — atidarykite skaičiuoklę, kad ją sukurtumėte“), atnaujinamą kas sekundę.
* **Renderio skiriamoji geba**išskleidžiamasis meniu (tie patys nustatymai kaip ir kiekvienai kamerai atskirai, numatytasis 720p): tiesioginio peržiūros srauto aukštis**ir** išsaugoto kompozicijos eksporto dydis. Pastaba lange: „Peržiūros + išsaugoto kompozicinio vaizdo dydis. Kiekvienos kameros vaizdai visada eksportuojami pilna raiška.“

### Rodymo sluoksniai *(tik sujungti)** Žymės langelis **Įjungti** (numatyta išjungta — pagrindinė kamera rodoma tiesiogiai; įjungta = sluoksniuota kompozicija).
* **Pirmame plane**/**Fone**išskleidžiamieji meniu: kiekviena nario kamera (pagal pavadinimą) arba**Indeksas**. Kai „Pirmame plane“ pasirinkta „Indeksas“, pikseliai už LUT minimalių ir maksimalių ribų rodo „Fono“ sluoksnį.

### Padalintas vaizdas *(tik sujungtas)*

**„„Rodyti kameras“**— mygtukas**„Padalyti / Paslėpti kameras“**, kuris prideda kiekvienos kameros tiesioginį vaizdą kaip atskiras tinklelio langelius šalia kompozicijos. Langeliai naudoja masyvo esamą kadrų buferį (nėra papildomo kameros prijungimo). Tik tinklelinis vaizdas; išsaugoma pagal masyvą kartu su projektu.

### Galimybės

Tik skaitymo režimu veikiantis skydelis, atnaujinamas kas 5 s:

* **Lygio etiketė**: „Vienalaikis fiksavimas“ (žalia) · „Vienalaikis fiksavimas (FTD-pakopinis išsiuntimas)“ (žalia) · „Pakopinis fiksavimas (100 ms nuokrypis)“ (gintarinė) · „Konfigūracija per didelė“ (raudona).
* **Kadro būklė**: „x,xx % neužbaigta“ — žalia, kai mažiau nei 1 %, gintarinė, kai mažiau nei 5 %, raudona, kai 5 % ar daugiau.
* **Ryšio linija**: „NIC {mbps} Mbps – nuolatinis {MB/s} MB/s“.

Tai yra masyvo realaus laiko pralaidumo biudžetas. Dėl pagrindinių kadrų per sekundę (fps) ir tinklo modelio — bei to, ką reikia keisti, kai lygis tampa gintaro arba raudonos spalvos — žr. [Daugiakameriniai masyvai](arrays.md) ir [CLI nuorodoje](../reference/cli-reference.md).



<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->## Langas „Indekso skaičiuoklė“

Trečiasis šoninės juostos puslapis, bendras kameros indekso nustatymams ir jungtinio masyvo indekso nustatymams (po vieną – antraštėje rašoma „Indekso skaičiuoklė — <camera name="">“ arba „Indekso skaičiuoklė —<array name="">

“). Jame pateikiamas juostų sąrašas (kameros filtro natūralios juostos arba visos masyvo narių juostos), dabartinis išraiškos formulė ir LUT konfigūracija (įjungta/išjungta, lygis – numatytasis 3, min. – numatytasis 0,2, maks. – numatytasis 1), taip pat tiesioginė indekso histograma. **„Taikyti“** patvirtina išraišką; LUT pakeitimai iš karto pritaikomi peržiūroje.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Kameros ir masyvo valdomi nustatymai

Trumpas vadovas, kur kas yra, kai kamera yra masyvo narys:

| Masyvo valdomi (kameros lange tik skaitymo režimu) | Vis dar kameros viduje, esant masyve |
| --- | --- |
| Pikselių formatas, skiriamoji geba, pikselių sujungimas | Automatinė ekspozicija (ekspozicija, stiprinimas, tikslinė vertė, išlyginimas, ROI) |
| Suaktyvinimo režimas/šaltinis, kadrų dažnis | Triukšmo šalinimas, ryškumas, vinjetė |
| | Orientacija (veidrodinis atspindys/pasukimas), ekrano antraštės, taškinis eksponometras |
| | Indeksas (atskirų ekranų masyvai), šviesos jutiklio susiejimas |

Kiti bendri elgesio ypatumai:

* **Kombinuotas ir atskiras rodymas** pasirenkamas jungiant masyvą: kombinuotas = viena suderinta sudėtinė plytele (nariai perduoda vaizdą tik per „Split View“); atskiras = kiekvienas narys atkuria savo sinchronizuotą plytele. Kamera niekada nerodo ir atskiro vaizdo, ir masyvo plytelės.
* **Automatinis pakartotinis prisijungimas**: atidarius išsaugotą projektą atkuriamos jo kameros ir matricos, o prieš atnaujinant srautus visi išsaugoti nustatymai iš naujo pritaikomi užkulisiuose.
* **Įrašymo filtravimas**: paslėptos arba pristabdytos kameros neįtraukiamos į „Capture All“; matrica visiškai blokuojama tik tada, kai VISOS jos dalys yra paslėptos arba pristabdytos. Žr. [Įrašymo nustatymai ir režimai](capture.md).

## Kaip išsaugomi nustatymai

Kameros skirtuko būsena išsaugoma **kartu su projektu**, o ne naršyklėje:

* Kiekvienas reaktyvusis pakeitimas užfiksuoja kamerų ir masyvų būseną projekto `cameras.json` (su 500 ms atsiliepimo trukme). Tai apima kamerų pavadinimus ir spalvas, ekspozicijos/stiprinimo/AE nustatymus, pikselių formatą/skiriamąją gebą/binningą, suaktyvinimo dažnį, peržiūros nustatymus (atvaizdavimo skiriamąją gebą, triukšmo šalinimą, ryškumą, vinjetę, spalvų profilį, sodrumą ir kontrastą), orientaciją, perdangas, kanalų padalijimus, indekso konfigūraciją, prognozuojamosios AE nustatymus, AE ROI, matricos pavadinimus, rodymo režimą, matricos fiksavimo nustatymus (įskaitant ROI apkarpymo padėtį) ir tinklelio bloką (transliacijos priartinimą, peržiūros režimą, tinklelio fiksavimą, rankinę plytelių tvarką, paslėptas kameras, uždarytas plyteles, aktyvią kamerą).
* Šviesos jutiklių priskyrimai išsaugomi projekto `sensors.json`.
* Vėl atidarius projektą, įranga vėl prijungiama ir visi nustatymai vėl pritaikomi.
* **Jei projektas neatidarytas = tik sesija**: jei nėra projekto, uždarius Chloros niekas neišsaugoma.
* Tik sesijai nepriklausomai nuo projekto: sustabdymo būsena, taškinio matuoklio mėginiai, žymės langelis „Kalibravimo taikinys“ kiekvienai kamerai (pagal numatymą visada atidarytas) ir matricos suderinimo profilis (pagal projektą perskaičiuojamas kiekvienai sesijai).
* Viena išimtis: **„Užfiksavimo nustatymai“** eksporto pasirinkimai ir užfiksavimo režimas išsaugomi pagal projektą vietinėje programos saugykloje, o ne `cameras.json` — žr. [Užfiksavimo nustatymai ir režimai](capture.md).</array></camera></serial>
