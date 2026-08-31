# Projekto nustatymai

Chloros programos šoninėje juostoje „Projekto nustatymai“ (<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

) galite konfigūruoti visus vaizdo apdorojimo, kalibravimo taškų aptikimo, multispektrinių indeksų skaičiavimo ir projekto eksporto parinktis. Šie nustatymai išsaugomi kartu su projektu ir gali būti įrašyti kaip šablonai, kuriuos galima pakartotinai naudoti įvairiuose projektuose.

## Kaip patekti į projekto nustatymus

Norėdami patekti į projekto nustatymus:

1. Atidarykite projektą programoje Chloros
2. Kairiajame šoniniame meniu spustelėkite skirtuką **Projekto nustatymai** „<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

“
3. Nustatymų skydelyje bus rodomos visos galimos konfigūracijos parinktys, suskirstytos pagal kategorijas

<!-- SCREENSHOT-NEEDED: Full Project Settings sidebar of a LATTICE project, scrolled so the Processing category is visible showing the per-product export checkboxes (Export sensor response, Export vignette corrected, Export debayered, Export preview, Export radiance, Export reflectance) and the Debayer method row. -->

{% hint style="info" %}
**Nustatymai, kurie priklauso nuo kitų nustatymų, yra paryškinti pilka spalva.** Kai viršesnis jungiklis neleidžia atlikti tam tikro nustatymo (pavyzdžiui, nuėmus žymę nuo *Atspindžio kalibravimas / baltos spalvos balansas* neįmanoma atlikti *Eksportuoti atspindį*), priklausomas valdiklis išjungiamas, o jo įrankio patarime nurodomas jungiklis, kurį reikia pakeisti.
{% endhint %}

***

## Ekranas

### Vaizdo miniatiūros skiriamoji geba

* **Tipas**: Išskleidžiamasis meniu
* **Parinktys**: `Default (512 px)`, `1024 px`, `2048 px`, `Full resolution`
* **Numatytasis**: Numatytasis (512 pikselių)
* **Aprašymas**: Skiriamoji geba (ilgiausias kraštas, pikseliais), kuria atvaizduojamos vaizdų tinklelio miniatiūros. Didesnės reikšmės atrodo ryškesnės, kai vaizdas padidinamas, tačiau įkeliama lėčiau ir sunaudoja daugiau atminties. Pilna skiriamoji geba atitinka originalaus vaizdo dydį.
* **Pastaba**: Tik rodymui — tai niekada neturi įtakos apdorojimui ar eksportuojamiems failams.***

## Taikinio aptikimas

Šie nustatymai kontroliuoja, kaip Chloros aptinka ir apdoroja kalibravimo taškus jūsų vaizduose. Abu parametrai veikia tik tada, kai įjungta **Atspindžio kalibravimas / baltos spalvos balansas** (kitu atveju jie yra išblukinti, nes taškų aptikimas visiškai praleidžiamas).

### Mažiausias kalibravimo mėginio plotas (px)

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 10 000 pikselių
* **Numatytasis**: 25 pikseliai
* **Aprašymas**: Nustato mažiausią plotą (pikseliais), reikalingą, kad aptiktas regionas būtų laikomas galiojančiu kalibravimo taško pavyzdžiu. Mažesnės reikšmės leis aptikti mažesnius tikslus, tačiau gali padidinti klaidingų teigiamų rezultatų skaičių. Didesnės reikšmės reikalauja didesnių ir aiškesnių tikslų sričių aptikimui.
* **Kada reguliuoti**:
  * Padidinkite, jei gaunate klaidingus aptikimus dėl mažų vaizdo artefaktų
  * Sumažinkite, jei jūsų kalibravimo tikslai vaizduose atrodo maži ir nėra aptinkami

### Minimalus tikslų grupuojimas (0–100)

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 100
* **Numatytasis**: 60
* **Aprašymas**: Nustato grupuojimo slenkstį, pagal kurį grupuojamos panašios spalvos sritys aptinkant kalibravimo tikslus. Didesnės reikšmės reikalauja, kad būtų sugrupuotos daugiau panašių spalvų, todėl taikiniai aptinkami konservatyviau. Mažesnės reikšmės leidžia didesnį spalvų skirtumą vienoje taikinio grupėje.
* **Kada reguliuoti**:
  * Padidinkite, jei kalibravimo taikiniai suskaidomi į kelis aptikimus
  * Sumažinkite, jei spalvų skirtumus turintys kalibravimo taikiniai nėra visiškai aptinkami

***

## Apdorojimas

Šie nustatymai kontroliuoja, kaip Chloros apdoroja ir kalibruoja jūsų vaizdus.

### Vigneto korekcija

* **Tipas**: Žymės langelis
* **Numatytasis**: Įjungta (pažymėta)
* **Aprašymas**: Taiko vinjetės korekciją, siekiant kompensuoti objektyvo sukeliamą vaizdo kraštų patamsėjimą. Vinjetė yra dažnas optinis reiškinys, kai dėl objektyvo savybių vaizdo kampai ir kraštai atrodo tamsesni nei centras.
* **Šalutinis poveikis**: Šis jungiklis taip pat leidžia pasirinkti, kokį *nekalibruotą atsarginį produktą* įrašo vykdymo ciklas (žr. toliau).

### Atspindžio kalibravimas / baltos spalvos balansas

* **Tipas**: Žymės langelis
* **Numatytasis**: Įjungta (pažymėta)
* **Aprašymas**: Įjungia atspindžio kalibravimą — remiantis aptiktais kadre esančiais kalibravimo taškais ir (arba) DAQ šviesos jutiklio žemyn nukreiptų spindulių duomenimis, priklausomai nuo kameros ir to, kas yra prieinama. Tai normalizuoja atspindžio vertes visame duomenų rinkinyje ir užtikrina nuoseklius matavimus nepriklausomai nuo apšvietimo sąlygų.
* **Kai išjungta**: Tikslo aptikimas visiškai praleidžiamas, ir**nė viena kamera negali sukurti atspindžio produkto** — nei Survey3, veikianti pagal tikslus, nei LATTICE, veikianti pagal DAQ. Susiję nustatymai (*Eksportuoti atspindžio koeficientą*, *Minimalus pakalibravimo intervalas* ir tikslų aptikimo slenksčiai) yra išblukinti.

### Nekalibruoti atsarginiai produktai: Eksportuoti jutiklio atsaką / Eksportuoti su koreguotu vinjetavimu

* **Tipas**: Dvi žymės langeliai
* **Numatytieji nustatymai**: Abu įjungti (pažymėti)
* **Aprašymas**: Kai kadro atspindžio kalibravimas neįmanomas (nerastas kalibravimo taikinys arba atspindžio kalibravimas išjungtas), jis įrašomas kaip *nekalibruotas atsarginis produktas*. **Kiekvienam kameros modeliui per vieną vaizdo įrašymo sesiją egzistuoja tik vienas iš dviejų atsarginių produktų**, pasirenkamas naudojant *Vignette correction* jungiklį:
  * **Įjungta**„Vignette correction“ → `Vignette_Corrected_Images/` (nustatoma pagal**„Export vignette corrected“**)
  * Vignette korekcija **išjungta**→ `Sensor_Response_Images/` (nustatoma pagal**Eksportuoti jutiklio atsaką**)
* Atsarginis variantas, kuris nenaudojamas, yra užtušuotas. Pašalinus žymę nuo naudojamo varianto, tas failas iš viso nebus įrašytas.

### „LATTICE“ eksporto produktai

Projektai, kuriuose yra „LATTICE“ įrašai, kiekvienas importuotas „LATTICE“ kadras vienu apdorojimo etapu išskaidomas į visus įjungtus **ir taikytinus**produktus. Šį išskaidymą valdo keturi žymimieji langeliai (visi pagal numatytuosius nustatymus**įjungti**):

| Nustatymas | Išvesties aplankas | Kas eksportuojama |
| --- | --- | --- |
| **Eksportuoti be „debayeringo“** | `Debayered_Images/` | Linijinis vaizdas be „debayeringo“. Taikoma RGB ir multispektrinėms kameroms. |
| **Eksportuoti peržiūrą** | `Preview_Images/` | Ekrano peržiūra. RGB = baltos spalvos balansas (DAQ šviesos šaltinis, jei yra, kitais atvejais – „gray-world“) + gama; daugiaspektrinė kamera = netikrų spalvų išplėtimas. |
| **Eksportuojamas spinduliavimas** | `Radiance_Images/` | „Float32“ spektrinis spinduliavimas, išreikštas W/m²/sr/nm. Tik daugiaspektrinis (M3C/M3M) — netaikoma RGB šablonams. Visada rašoma kaip 32 bitų TIFF, nepriklausomai nuo *Kalibruoto vaizdo formato* nustatymo. |
| **Eksporto atspindžio koeficientas**| `Reflectance_Calibrated_Images/` | „Uint16“ atspindžio koeficientas, mastelis nustatytas taip, kad**32768 = atspindžio koeficientas 1,0** (pažymėta kaip XMP `Chloros:PixelScale`). Tik multispektrinis, įrašomas, kai atitinkamas `.daq` žemyn nukreiptas įrašas (arba kokybės patikrinimą išlaikęs kadro vidinis taikinys) apima kadrą. |

* RGB pagrindinės kameros perduoda debayered + peržiūros duomenis; spinduliavimo intensyvumas ir atspindžio koeficientas jų atveju praleidžiami, nes netaikomi.
* Debayered/peržiūros duomenų bitų gylis priklauso nuo *Kalibruoto vaizdo formato* nustatymo; spinduliavimo intensyvumas visada yra float32.
* Survey3 apdorojimui šie keturi perjungikliai įtakos neturi.

Tie patys keturi perjungikliai egzistuoja be pavadinimo kaip „`chloros-cli process --debayered / --preview / --radiance / --reflectance`“ ir kaip atitinkami „SDK“ parametrai. Jie pakeitė senąjį „`--radiometric-output`“ žymeklį, kurio jau nebėra.

{% hint style="warning" %}
**Išjungus visus taikytinus produktus, apdorojimo ciklas baigiasi nesėkme.** Nuo 1.2.0 versijos apdorojimo ciklas, kurio metu buvo prašoma sukurti produktus, bet nebuvo įrašytas joks vaizdo produktas, praneša apie nesėkmę, o CLI baigia veikimą su nulinės vertės rezultatu, vietoj to, kad praneštų apie tylią sėkmę. Žurnale nurodytas produktas, kurio nepavyko įrašyti, ir priežastis. Tyčinis tik metaduomenų apdorojimo ciklas (nieko neprašyta) vis tiek laikomas sėkmingu.
{% endhint %}

### Atspindžio šaltinis (projekto nustatymas, nustatomas per CLI/SDK)

Projekte taip pat saugoma informacija, kokį **atspindžio etaloną** naudoja „LATTICE“ atspindžio produktas. Nustatymų skydelyje nėra specialaus valdiklio; reikšmė saugoma projekto konfigūracijoje kaip `Processing → "Target reflectance source"` ir nustatoma naudojant parametrą `chloros-cli process --reflectance-source {auto,target,daq}` arba „SDK parametru `reflectance_source`:

* **`auto`** (numatyta reikšmė): kokybės užtikrinimo (QA) reikalavimus atitinkantis rėmo vidinis kalibravimo taikinys tampa absoliučiu etalonu; jei taikinio nėra arba kokybės užtikrinimo patikrinimas nepavyksta, naudojamas DAQ žemyn nukreipto spinduliavimo padalijimas (ρ = πL/E).
* **`target`**: griežtas atspindžio koeficientas, nustatomas pagal tikslą — be DAQ pakeitimo.
* **`daq`**: atspindžio koeficientas, kuriam lemiamą reikšmę turi DAQ; kadrai esantys taikiniai nenaudojami kaip etalonas.

Išsaugota reikšmė atitinkama neatsižvelgiant į didžiųjų ir mažųjų raidžių skirtumą, o keletas rašybos variantų priimami kaip sinonimai: `target`, `target_image`, `empirical` ir `empirical_line` – visi reiškia **tikslas**; `daq`, `dls`, `light_sensor` ir `sensor` – visi reiškia**daq**. Viskas kita – įskaitant ir trūkstamą raktą – priskiriama**auto**.**Išmatuoti** tikslo skenavimai pagal vienetą ieškoma pagal tikslo vieneto serijinį numerį/QR, pvz., `<serial>.csv`, trijose vietose: kataloge, nurodytame su `--target-reflectance-dir` (išsaugotas kaip `Processing → "Target reflectance dir"`), pačiame projekte esančiame aplanke `target_reflectance/` ir kelyje, nurodytame aplinkos kintamajame `CHLOROS_TARGET_REFLECTANCE_DIR`. Jei to įrenginio matavimo duomenų nėra, vietoj to naudojama paskelbta nominalioji tikslinio modelio kreivė.

### „Debayer“ metodas

* **Tipas**: Išskleidžiamasis meniu
* **Parinktys**:
  * Standartinis (greitas, vidutinė kokybė)
  * Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė) \[Chloros+]
* **Numatytasis**: Standartinis (greitas, vidutinė kokybė)
* **Aprašymas**: Pasirenkamas demozėjimo algoritmas, naudojamas neapdorotiems Bayerio matricos jutiklio duomenims konvertuoti į spalvotus vaizdus. „Standartinis (greitas, vidutinė kokybė)“ metodas užtikrina optimalų apdorojimo greičio ir vaizdo kokybės balansą. „Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė)“ \[Chloros+] naudoja aukštos kokybės, kraštus atpažįstantį demosaicingo algoritmą, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą demosaicingo triukšmą. „Tekstūrą atpažįstančiam“ modeliui veikti reikalinga GPU atmintis (VRAM). Rekomenduojame jį naudoti, jei turite &gt;4 GB laisvos VRAM atminties, kad apdorojimas vyktų greičiau.
* **Kai eilutė yra išskleidžiamas meniu**: dviejų parinkčių išskleidžiamas meniu pasirodo tik tada, kai**abi**sąlygos tenkinamos — esate prisijungę su atitinkamu „Chloros+“ abonementu,**ir** projekte nėra „LATTICE“ užfiksuotų vaizdų. Priešingu atveju eilutė rodoma kaip paprastas tekstas „`Standard (Fast, Medium Quality)`“, be jokios pasirinkimo galimybės.
* **„LATTICE“ pastaba**: nėra „LATTICE“ duomenimis apmokyto „Texture Aware“ modelio, o apdorojimo grandinė priverčia taikyti standartinį demosaikavimą „LATTICE“ kadrams, nepriklausomai nuo išsaugotos vertės. Jei „LATTICE“ aplanką įtraukiate į projektą, kuriame jau buvo pasirinkta „Texture Aware“, „Chloros“ perrašo nustatymą atgal į „Standard“, o ne palieka pasenusią vertę „`project.json`“.

### Mažiausias pakartotinio kalibravimo intervalas

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 3 600 sekundžių
* **Numatytasis**: 0 sekundžių
* **Aprašymas**: Nustato minimalų laiko intervalą (sekundėmis) tarp kalibravimo taškų naudojimo. Nustačius 0, „Chloros“ naudos kiekvieną aptiktą kalibravimo tašką. Nustačius didesnę reikšmę, „Chloros“ naudos tik tuos kalibravimo taškus, kurie yra atskirti bent jau šiuo sekundžių, taip sutrumpindamas duomenų rinkinių, kuriuose dažnai fiksuojami kalibravimo taškai, apdorojimo laiką.
* **Kada reguliuoti**:
  * Nustatykite vertę 0, kad pasiektumėte maksimalų kalibravimo tikslumą, kai apšvietimo sąlygos kinta
  * Padidinkite (pvz., iki 60–300 sekundžių), kad apdorojimas vyktų greičiau, kai apšvietimas yra pastovus ir turite daug kalibravimo taškų vaizdų

### Šviesos jutiklio laiko juostos nuokrypis

* **Tipas**: Skaičius
* **Diapazonas**: nuo -12 iki +12 valandų
* **Numatytasis**: 0 valandų
* **Aprašymas**: Nurodo šviesos jutiklio duomenų laiko žymų laiko juostos nuokrypį (valandomis nuo UTC), naudojamą suderinant šviesos jutiklio įrašus su vaizdų užfiksavimo laiku. Naujausi `.daq` įrašai turi savo laiko juostos kilmės informaciją, todėl tai daugiausia reikalinga senesniems įrašams, užfiksuotiems vietos laiku.

### Taikyti PPK pataisas

* **Tipas**: Žymės langelis
* **Numatytasis nustatymas**: Išjungta (nepažymėta)
* **Aprašymas**: Įjungia galimybę naudoti PPK (Post-Processed Kinematic) korekcijas iš MAPIR DAQ įrašymo įrenginių, turinčių GPS (GNSS). Kai ši funkcija įjungta, „Chloros“ naudos visus .daq žurnalo failus, kuriuose yra ekspozicijos žymių duomenys jūsų projekto kataloge, ir pritaikys tikslias geolokacijos korekcijas jūsų vaizdams.
* **Reikalavimas**: jūsų projekto kataloge turi būti .daq žurnalo failas su ekspozicijos žymių įrašais
* **Kada įjungti**: Rekomenduojama visada įjungti PPK korekciją, jei jūsų .daq žurnalo faile yra ekspozicijos grįžtamojo ryšio įrašų.

### Ekspozicijos kontaktas 1

* **Tipas**: Išskleidžiamas meniu
* **Matomumas**: Matomas tik tada, kai įjungta funkcija „Taikyti PPK korekcijas“ IR yra ekspozicijos duomenų 1-ajam kontaktui
* **Parinktys**:
  * Projekte aptikti kamerų modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis**: Pasirenkamas automatiškai pagal projekto konfigūraciją
* **Aprašymas**: Priskiria konkrečią kamerą ekspozicijos kontaktui Nr. 1 PPK laiko sinchronizavimui. Ekspozicijos kontaktas fiksuoja tikslų laiką, kada suveikia kameros užraktas, o tai yra labai svarbu tiksliai nustatyti PPK geografinę vietą.
* **Automatinio pasirinkimo veikimas**:
  * Viena kamera + vienas kontaktas: kamera parenkama automatiškai
  * Viena kamera + du kontaktai: 1-asis kontaktas automatiškai priskiriamas kamerai
  * Kelios kameros: reikia pasirinkti rankiniu būdu

### 2-asis ekspozicijos kontaktas

* **Tipas**: pasirinkimas iš išskleidžiamojo sąrašo
* **Matomumas**: Matomas tik tada, kai įjungta funkcija „Taikyti PPK pataisas“ IR yra ekspozicijos duomenų 2-ajam kontaktui
* **Parinktys**:
  * Projekte aptikti kamerų modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis nustatymas**: Pasirenkama automatiškai pagal projekto konfigūraciją
* **Aprašymas**: Naudojant dviejų kamerų konfigūraciją, ekspozicijos kontaktui Nr. 2 priskiria konkrečią kamerą PPK laiko sinchronizavimui.
* **Automatinio pasirinkimo veikimas**:
  * Viena kamera + vienas kontaktas: kontaktui Nr. 2 automatiškai nustatoma parinktis „Nenaudoti“
  * Viena kamera + du kaiščiai: 2-asis kaištis automatiškai nustatomas į „Nenaudoti“
  * Kelios kameros: reikalingas rankinis pasirinkimas
* **Pastaba**: Tos pačios kameros negalima priskirti vienu metu tiek 1-ajam, tiek 2-ajam kaiščiui.***

## DAQ šviesos jutiklis

Šis skyrius rodomas projekto nustatymuose ir jame pateikiamas visų projekte esančių DAQ žemyn nukreiptų duomenų failų sąrašas — `.daq` įrašai ir DAQ-M `.csv` žemyn nukreiptos spinduliuotės žurnalai. Įrašai, padaryti skirtuke „Šviesos jutikliai“, automatiškai pridedami prie atidaryto projekto.

<!-- SCREENSHOT-NEEDED: Project Settings "DAQ Light Sensor" section of a project containing at least one .daq file, showing the "Cap override (all files)" dropdown and a per-file row with its resolved cap. -->

Kiekvienoje eilutėje nurodytas failas, jutiklio modelis, ir difuzoriaus dangtelio korekciją, kuri šiuo metu taikoma tam failui. Virš eilučių yra vienas visam projektui bendras valdymo elementas:

### Dangtelio perrašymas (visi failai)

* **Tipas**: Išskleidžiamasis meniu
* **Parinktys**: `Auto` ir difuzoriaus dangtelio korekcijos profiliai, galiojantys projekte esantiems jutiklių tipams
* **Numatytasis**: Automatinis
* **Išsaugota kaip**: `Processing → "DAQ cap id"` (numatyta reikšmė – `auto`)
* **Aprašymas**: `Auto` naudoja kiekviename faile užregistruotą dangos korekciją (jei nieko neužregistruota, laikoma, kad taikoma „Sunshine“ dangos korekcija — visi MAPIR duomenų surinkimo įrenginiai tiekiami su „Sunshine“ korektoriumi). Pasirinkus konkretų dangtelį, jis pakeičia**visus** projekte esančius žemyn nukreiptus failus: neapdoroti įrašai koreguojami naudojant jį, o įrašai, kuriuose jau yra dangtelis, perorientuojami (užregistruota korekcija panaikinama ir pritaikoma pasirinktoji).
* **Svarbu**: Pasirinkta dangtelio rūšis turi atitikti tą, kuri buvo fiziškai uždėta įrašymo metu. Nei jutiklis, nei programinė įranga negali aptikti fizinio dangtelio — netinkamas dangtelio identifikatorius neteisingai koreguoja spektrus.

Čia sąmoningai numatytas **vienas** visam projektui bendras valdymo elementas, o ne atskiri išskleidžiamieji meniu kiekvienam failui: šis nustatymas taikomas kiekvienam žemyn nukreiptam šaltiniui projekte.***

## Masyvo suderinimas

Šis skyrius rodomas **tik** tada, kai bent viename projekto vaizde yra modulio suderinimo transformacija, kurią LATTICE masyvai pažymi fiksavimo metu (XMP `Chloros:Alignment*` žymės). Čia rodoma, kiek vaizdų turi suderinimo žymes, kuri kamera yra etalonas (`REF` žymė), ir pateikiama vaizdų skaičiaus lentelė pagal kameras.

<!-- SCREENSHOT-NEEDED: Project Settings "Array Alignment" section for an imported LATTICE array capture set, showing the tagged-image count, the per-camera rows with the REF badge, and the three controls (Apply array alignment, Crop to common overlap, Resampling). -->

### Taikyti masyvo suderinimą

* **Tipas**: Varnelė
* **Numatytasis**: Įjungta (pažymėta)
* **Išsaugoma kaip**: `Processing → "Array alignment"`
* **Aprašymas**: Kiekvieną apdorotą produktą (debayering / peržiūra / spinduliavimas / atspindys / indeksas) iškraipo pagal masyvo bendrą etaloninę geometriją, naudodamas fotografavimo metu užfiksuotą transformaciją. Išjungta = eksportuojama pagal natūralią kiekvieno jutiklio geometriją.

### Apkarpyti iki bendro persidengimo

* **Tipas**: Varnelė (veikia tik tada, kai įjungta funkcija *Taikyti masyvo suderinimą*)
* **Numatytasis nustatymas**: Įjungta (pažymėta)
* **Išsaugoma kaip**: `Processing → "Array alignment crop"`
* **Aprašymas**: Apkarpo suderintus eksportus iki srities, kurią dalijasi visi kameros moduliai, todėl kiekviena juosta užima tą patį plotą. Išjungus išlaikomas visas jutiklio plotas (juodas užpildymas už šaltinio ribų).

### Perimatuavimas

* **Tipas**: Išskleidžiamas meniu (veikia tik tada, kai įjungta parinktis *Taikyti masyvo suderinimą*)
* **Parinktys**: `Bilinear (smooth, default)`, `Nearest (preserve exact values)`, `Cubic (sharpest)`
* **Numatytasis**: Bilinearinis
* **Išsaugoma kaip**: `Processing → "Array alignment interpolation"`
* **Aprašymas**: Interpoliacija, naudojama suderinimo deformacijai. Parinktis „Artimiausias“ išsaugo tikslias šaltinio reikšmes (be pikselių maišymo) griežtai radiometrinei analizei; parinktis „Bilinearinis“ geriausiai tinka žemėlapių sudarymui ir vizualiniam naudojimui.

Tos pačios trys parinktys be priesagos yra `chloros-cli process --array-alignment`, `--array-alignment-crop` ir `--array-alignment-interp {bilinear,nearest,cubic}`.

***

## Indeksas

Šie nustatymai leidžia konfigūruoti multispektrinius indeksus analizės ir vizualizavimo tikslais.

### Pridėti indeksą

* **Tipas**: Specialus indeksų konfigūracijos skydelis
* **Aprašymas**: Atidaro interaktyvų skydelį, kuriame galite pasirinkti ir konfigūruoti multispektrinius augmenijos indeksus (NDVI, NDRE, EVI, ir kt.), kuriuos reikia apskaičiuoti apdorojant vaizdus. Galite pridėti kelis indeksus, kiekvienam nustatydami atskirus vizualizavimo parametrus.
* **Galimi indeksai**: Grafinės vartotojo sąsajos išskleidžiamajame meniu yra**27** iš anksto apibrėžtos daugiaspektrinių indeksų formulės (žr. [Daugiaspektrinių indeksų formulės](multispectral-index-formulas.md), kur rasite išsamų sąrašą, įskaitant pavadinimus, kuriuos taip pat priima CLI/SDK `--indices` parinktis).
* **Funkcijos**:
  * Pasirinkite iš iš anksto nustatytų indeksų formulių
  * Perkelkite savo kameros filtro kanalus į formulės juostų lizdus
  * Nustatykite vizualizacijos spalvų gradientus (LUT – paieškos lentelės)
  * Nustatykite slenkstines vertes ir apkarpymo režimus
  * Sukurkite pasirinktines indeksų formules
* **Pastaba**: Indeksai neskaičiuojami vienos juostos „LATTICE M3M“ monokameroms — daugiajuostiniai indeksai vienoje juostoje nėra apibrėžti. Tai neturi įtakos „Survey3“ ir „LATTICE M3C“.

<!-- SCREENSHOT-NEEDED: Project Settings > Index section with one index added and expanded: the filter dropdown, the formula dropdown open showing preset names, the coloured channel circles above the rendered formula, and the "+ Add LUT" button below it. -->

Kiekvienas jūsų pridėtas indeksas pateikiamas kaip matematinė formulė, su spalvotu apskritimu kiekvienoje juostos vietoje: raudona = Red, žalia = Green, mėlyna = Blue, oranžinis = Orange, žydras = Cyan, violetinė = NIR, magenta = RE. Norėdami susieti formulę, perkelkite apskritimą iš eilutės virš formulės į lizdą; norėdami išvalyti susietą lizdą, dukart spustelėkite jį. Indeksas apskaičiuojamas tik tada, kai kiekviename formulės naudojamame lizde yra kanalas.

### Pasirinktinės formulės (Chloros+ funkcija)

* **Tipas**: Pasirinktinių formulių apibrėžimų masyvas
* **Prieinamumas**: Reikia prisijungti su atitinkamu Chloros+ abonementu.
* **Aprašymas**: Leidžia kurti ir išsaugoti pasirinktines multispektrinių indeksų formules, naudojant juostų skaičiavimus. Pasirinktinės formulės išsaugomos kartu su jūsų projekto nustatymais ir gali būti naudojamos taip pat kaip ir įdiegtieji indeksai.
* **Kaip sukurti**:
  1. Indekso konfigūracijos skydelyje atidarykite pasirinktinių formulių skaičiuoklę
  2. Parašykite formulę naudodami **juostų lizdų simbolius**, o ne juostų pavadinimus
  3. Išsaugokite formulę su aprašomuoju pavadinimu — tada ji pasirodys formulės išskleidžiamojo meniu apačioje, o jūs galėsite perkelti savo kameros kanalų apskritimus į jos lizdus lygiai taip pat, kaip ir naudojant įdiegtą išankstinį nustatymą
* **Formulės sintaksė**:
  * Juostų lizdai: `x`, `y`, `z`, `a`, `b`, `c` — šešios pozicijos, kurias priskiriate realiems kanalams, juos perkelkdami
  * Operatoriai: `+`, `-`, `*`, `/`, `^` ir `()` grupavimui
  * Funkcijos: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
* **Kodėl simboliai, o ne juostų pavadinimai**: formulė, užrašyta kaip `(y-x)/(y+x)`, veikia bet kurioje kameroje, nes „vilk-ir nuleidimo atitikimas nusprendžia, ar „`y`“ yra 850 nm „NIR“ iš „RGN“ filtro, ar 808 nm „NIR, priklausomai nuo OCN filtro. Įdiegtos išankstinės nustatymų parinktys saugomos tuo pačiu būdu — tikslią visų 27 simbolių formą rasite [Daugiaspektrinių indeksų formulėse](multispectral-index-formulas.md).
* **Kur jos veikia**: pasirinktinės formulės išsaugomos kartu su projekto nustatymais ir gali būti naudojamos tiek [Indekso/LUT bandymų aplinkoje](../image-viewer-gui/index-lut-sandbox.md), tiek apdorojimo metu. Jos**ne**priimamos CLI/SDK `--indices` pavadinimų sąraše, kuris tik išplečia 22 įdiegtų nustatymų pavadinimus.***

## Eksportavimas

Šie nustatymai kontroliuoja eksportuojamų apdorotų vaizdų formatą ir kokybę.

### Kalibruoto vaizdo formatas

* **Tipas**: Pasirinkimas iš išskleidžiamojo meniu
* **Parinktys**:
  * **TIFF (16 bitų)** – nesuspaustas 16 bitų TIFF formatas
  * **TIFF (32 bitai, procentais)** – 32 bitų slankiojo kablelio TIFF su atspindžio vertėmis procentais
  * **PNG (8 bitų)** - Suspaustas 8 bitų PNG formatas
  * **JPG (8 bitų)** - Suspaustas 8 bitų JPEG formatas
* **Numatytasis**: TIFF (16 bitų)
* **Aprašymas**: Pasirenkamas failo formatas, kuriuo bus išsaugoti apdoroti ir kalibruoti vaizdai. Eksportuoti failai patenka į atskirą, pagal formatą pavadintą pakatalogį kiekvienos kameros aplanke (`tiff16`, `tiff32`, `png8`, `jpg8`), po vieną `<Product>_Images/` aplanką kiekvienam produktui. Eksportuoti failai išlaiko šaltinio failo pavadinimą – produktą identifikuoja aplankas, o ne failo pavadinimo priesaga.
* **Rekomendacijos dėl formato**:
  * **TIFF (16 bitų)**: Rekomenduojamas mokslinėms analizėms ir profesionaliems darbo srautams. Užtikrina maksimalią duomenų kokybę be suspaudimo artefaktų. Geriausiai tinka multispektrinei analizei ir tolesniam apdorojimui GIS programinėje įrangoje.
  * **TIFF (32 bitai, procentais)**: Geriausiai tinka darbo procesams, kuriuose atspindžio vertės turi būti pateikiamos procentais (0–100 %). Užtikrina maksimalų radiometrinių matavimų tikslumą.
  * **PNG (8 bitų)**: Tinka peržiūrai internete ir bendrai vizualizacijai. Mažesni failų dydžiai su nesuspaudimu be nuostolių, tačiau sumažintas dinaminis diapazonas.
  * **JPG (8 bitų)**: Mažiausi failų dydžiai, geriausiai tinka tik peržiūrai ir rodymui internete. Naudojamas suspaudimas su praradimais, kuris netinka mokslinei analizei.
* **Pastaba**: „LATTICE“ spinduliavimas visada eksportuojamas kaip 32 bitų plaukiojančios dešimtainės skaičių sistemos TIFF, nepriklausomai nuo šio nustatymo.***

## Projekto šablono išsaugojimas

Ši funkcija leidžia išsaugoti esamus projekto nustatymus kaip pakartotinai naudojamą šabloną.

* **Tipas**: Teksto įvedimas + mygtukas „Išsaugoti“
* **Aprašymas**: Įveskite aprašomąjį pavadinimą savo nustatymų šablonui ir spustelėkite išsaugojimo piktogramą. Šablone bus išsaugoti visi dabartiniai jūsų projekto nustatymai (tikslo aptikimas, apdorojimo parinktys, indeksai ir eksporto formatas), kad juos būtų galima lengvai pakartotinai naudoti būsimuose projektuose. Šablonai saugomi aplanke „`Project Templates/`“, esančiame jūsų projekto išsaugojimo aplanke, ir juos taip pat galima pasirinkti arba eksportuoti iš pagrindinio meniu (*Pasirinkti šabloną* / *Išsaugoti šabloną* / *Eksportuoti šabloną*).
* **Naudojimo pavyzdžiai**:
  * Sukurkite šablonus skirtingoms kamerų sistemoms (RGB, multispektrinė, NIR)
  * Išsaugokite standartines konfigūracijas konkrečioms kultūrų rūšims ar analizės darbo eigoms
  * Dalinkitės vienodais nustatymais su visa komanda
* **Kaip naudoti**:
  1. Nustatykite visus norimus projekto parametrus
  2. Įveskite šablono pavadinimą (pvz., „RedEdge Survey3 NDVI Standartinis“)
  3. Spustelėkite išsaugojimo piktogramą
  4. Dabar šį šabloną galima įkelti kuriant naujus projektus

***

## Projekto aplanko išsaugojimas

Šis nustatymas nurodo, kur pagal numatytuosius nustatymus išsaugomi nauji projektai.

* **Tipas**: Katalogo kelio rodymas + mygtukas „Redaguoti“
* **Numatytasis (Windows)**: `C:\Users\[Username]\Chloros Projects`
* **Numatytasis (Linux)**: `~/Chloros Projects`
* **Aprašymas**: Rodo dabartinį numatytąjį katalogą, kuriame kuriami nauji Chloros projektai. Spustelėkite redagavimo piktogramą, kad pasirinkite kitą katalogą. Šis pakeitimas saugomas kaip viena teksto eilutė faile `~/.chloros/working_directory.txt` — Windows, t. y. `C:\Users\<Username>\.chloros\working_directory.txt`. Jei to failo nėra arba jame nurodytas nebeegzistuojantis kelias, Chloros grįžta prie aukščiau nurodyto numatytasis. CLI skaito ir rašo tą patį failą, todėl `chloros-cli` ir grafinė vartotojo sąsaja (GUI) visada sutaria dėl projektų saugojimo vietos.
* **Projekto šablonai** saugomi šio katalogo `Project Templates/` pakatalogyje.
* **Kada keisti**:
  * Nustatykite tinklo diską, jei dirbate komandoje
  * Perkelkite į diską su didesne saugojimo talpa, jei dirbate su dideliais duomenų rinkiniais
  * Tvarkykite projektus pagal metus, klientą arba projekto tipą skirtinguose aplankuose
* **Pastaba**: Šio parametro pakeitimas turi įtakos tik NAUJIEMS projektams. Esami projektai lieka savo pradinėse vietose.***

## Parametrų išsaugojimas

Chloros projektas yra **aplankas**. Visi projekto nustatymai išsaugomi jame esančiame `project.json`; prijungta įranga įsimenama kartu su juo `cameras.json` ir `sensors.json`, todėl vėl atidarius projektą, vėl prisijungia jo kameros ir šviesos jutikliai. Atidarius projektą iš naujo, visi nustatymai atkuriami lygiai taip, kaip juos palikote. Išsaugotus projektus taip pat galima valdyti be grafinės sąsajos naudojant „`chloros-cli project`“ arba „SDK“ failą „`open_project`“.

### Nustatymų hierarchija

Nustatymai taikomi tokia tvarka:

1. **Sistemos numatyti nustatymai** – įdiegtos numatytosios reikšmės, apibrėžtos Chloros
2. **Šablono nustatymai** – jei kurdami projektą įkeliate šabloną
3. **Išsaugoti projekto nustatymai** – su projekto failu išsaugoti nustatymai
4. **Rankiniai koregavimai** – bet kokie pakeitimai, kuriuos atliekate per dabartinę sesiją

### Nustatymai ir vaizdų apdorojimas

Apdorojimo nustatymai nuskaitomi, kai pradedamas apdorojimo ciklas. Nustatymo pakeitimas neturi atgalinio poveikio produktams, kurie jau yra diske – norėdami pritaikyti naujus nustatymus, apdorojimą reikia paleisti iš naujo. Kai kurie nustatymai visiškai neturi įtakos apdorojimui:

* Vaizdo miniatiūrų skiriamoji geba (tik rodymui)
* Išsaugoti projekto šabloną
* Išsaugoti projekto aplanką

***

## Konfigūracijos raktų žinynas

Automatizavimui (CLI `--config`, SDK `configure` arba tiesiogiai skaitant `project.json`), štai tikslieji raktai, esantys po `Project Settings`:

| Raktų kelias | Tipas | Numatytasis |
| --- | --- | --- |
| `Display → Image Thumbnail Resolution` | `"512" \| "1024" \| "2048" \| "full"` | `"512"` |
| `Target Detection → Minimum calibration sample area (px)` | skaičius 0-10000 | `25` |
| `Target Detection → Minimum Target Clustering (0-100)` | skaičius 0–100 | `60` |
| `Processing → Vignette correction` | bool | `true` |
| `Processing → Reflectance calibration / white balance` | bool | `true` |
| `Processing → Export sensor response` | bool | `true` |
| `Processing → Export vignette corrected` | bool | `true` |
| `Processing → Export debayered` | bool | `true` |
| `Processing → Export preview` | bool | `true` |
| `Processing → Export radiance` | bool | `true` |
| `Processing → Export reflectance` | bool | `true` |
| `Processing → Array alignment` | bool | `true` |
| `Processing → Array alignment crop` | bool | `true` |
| `Processing → Array alignment interpolation` | `"Bilinear" \| "Nearest" \| "Cubic"` | `"Bilinear"` |
| `Processing → Debayer method` | `"Standard (Fast, Medium Quality)" \| "Texture Aware (Slow, Highest Quality)"` | Standartas |
| `Processing → Minimum recalibration interval` | skaičius 0–3600 | `0` |
| `Processing → Light sensor timezone offset` | skaičius -12..12 | `0` |
| `Processing → Apply PPK corrections` | bool | `false` |
| `Processing → DAQ cap id` | ribojimo profilio ID arba `"auto"` | `"auto"` |
| `Processing → Target reflectance source` | `"auto" \| "target" \| "daq"` | `"auto"` |
| `Index → Add index` | indeksų konfigūracijų sąrašas | `[]` |
| `Export → Calibrated image format` | `"TIFF (16-bit)" \| "TIFF (32-bit, Percent)" \| "PNG (8-bit)" \| "JPG (8-bit)"` | `"TIFF (16-bit)"` |

Raktų `Array alignment` reikšmės įrašomos pirmą kartą, kai atvaizduojamas skyrius „Array Alignment“ arba juos nustato automatizavimo iškvietimas. Kol jų nėra, apdorojimo grandinė naudoja tas pačias vertes, kurios nurodytos aukščiau (`true`, `true`, bilinearinis), taigi projektas .json be jų veikia lygiai taip pat kaip ir tas, kuriame jie yra.

### Raktų, saugomų `project.json`, kurių negalima valdyti nustatymų skydelyje

Jie yra tame pačiame `Project Settings` medyje ir yra nuskaitomi apdorojimo metu, tačiau šoninėje juostoje nerasite jiems skirto valdiklio:

| Raktų kelias | Tipas | Numatytasis | Nustato |
| --- | --- | --- | --- |
| `Processing → LATTICE input level` | `"auto" \| "raw" \| "debayered" \| "processed"` | `"auto"` | `chloros-cli process --input-level`, SDK `input_level=`. Perrašo, kaip interpretuojami „LATTICE“ įvesties TIFF failai; `auto` iš kiekvieno failo`Chloros:ProcessingLevel` XMP žymą ir kanalų skaičių. Ignoruojama Survey3 `.raw` įrašams. Tai sąmoningai nėra GUI nustatymas — automatinis režimas tinka visais įprastais atvejais. |
| `Processing → Target reflectance dir` | kelio eilutė | `""` | `chloros-cli process --target-reflectance-dir`, arba projekto tikslas API |
| `Processing → Target reflectance config` | žodynas, suskirstytas pagal kameros serijos numerį | `{}` | Kadrinėje srityje esančio tikslo registravimas (režimas `fixed_block` / `fixed_strip` / `aruco`) |
| `Processing → DAQ-U log path` | kelio eilutė | `""` | SDK `process_folder(daq_log_path=…)`. Nurodo į `.daq` įrašą arba jų aplanką |
| `Target Detection → Minimum calibration target squares` | skaičius | `4` | Senasis numatytasis; be valdymo ir be CLI žymės |
| `UI → Grid thumbnail size` | skaičius | `160` | Pats vaizdų tinklelio miniatiūrų mastelio slankiklis |

Dvi peržiūros programos nuostatos saugomos **aukščiausiame lygmenyje `project.json`**, visiškai už `Project Settings` ribų, nes tai yra rodymo būsena, o ne apdorojimo nustatymai:

| Pagrindinis kelias | Tipas | Numatytasis | Nustato |
| --- | --- | --- | --- |
| `viewer_display → gsd_bin` | sveikasis skaičius 1–256 | `1` | Vaizdo skirtuko GSD (px) valdiklis — žr. [Vaizdo atidarymą visame ekrane](../image-viewer-gui/opening-an-image-full-screen.md) |

***

## Geriausia praktika

1. **Pradėkite nuo numatytųjų nustatymų**: Numatytieji nustatymai puikiai tinka daugumai MAPIR kamerų sistemų ir tipinių darbo eigų.
2. **Sukurkite šablonus**: Optimizavę nustatymus konkrečiam darbo procesui ar kamerai, išsaugokite juos kaip šabloną, kad užtikrintumėte nuoseklumą visuose projektuose.
3. **Išbandykite prieš pradedant visą apdorojimą**: Eksperimentuodami su naujais nustatymais, išbandykite juos su nedideliu vaizdų pavyzdžiu, prieš apdorodami visą duomenų rinkinį.
4. **Užfiksuokite savo nustatymus**: Naudokite aprašomuosius šablonų pavadinimus, nurodančius kamerų sistemą, apdorojimo tipą ir numatytą paskirtį (pvz., „Survey3\_RGB\_NDVI\_Žemės ūkio“).
5. **Eksporto formato pasirinkimas**: Pasirinkite eksporto formatą pagal galutinį naudojimo tikslą:
   * Mokslinė analizė → TIFF (16 bitų arba 32 bitų)
   * GIS apdorojimas → TIFF (16 bitų)
   * Greitas vizualizavimas → PNG (8 bitų)
   * Dalijimasis internete → JPG (8 bitų)

***

Daugiau informacijos apie daugiaspektrinius indeksus Chloros rasite puslapyje [Daugiaspektrinių indeksų formulės](multispectral-index-formulas.md).
