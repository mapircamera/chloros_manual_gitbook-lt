# Projekto nustatymai

<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

šoninėje juostoje esantys Chloros projekto nustatymai leidžia konfigūruoti visus vaizdo apdorojimo, kalibravimo taškų aptikimo, multispektrinių indeksų skaičiavimo ir projekto eksporto parinktis. Šie nustatymai išsaugomi kartu su projektu ir gali būti išsaugoti kaip šablonai, kuriuos galima pakartotinai naudoti įvairiuose projektuose.

## Kaip patekti į projekto nustatymus

Norėdami patekti į projekto nustatymus:

1. Atidarykite projektą Chloros


2. Spustelėkite kairėje šoninėje juostoje esantį skirtuką **Projekto nustatymai** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">


3. Nustatymų skydelyje bus rodomos visos galimos konfigūracijos parinktys, suskirstytos pagal kategorijas

<!-- SCREENSHOT-NEEDED: Full Project Settings sidebar of a LATTICE project, scrolled so the Processing category is visible showing the per-product export checkboxes (Export sensor response, Export vignette corrected, Export debayered, Export preview, Export radiance, Export reflectance) and the Debayer method row. -->



{% hint style="info" %}
**Nustatymai, kurie priklauso nuo kitų nustatymų, yra paryškinti pilka spalva.** Kai viršesnis parametras neleidžia nustatyti tam tikro parametro (pavyzdžiui, nuėmus žymę nuo *Atspindžio kalibravimas / baltos spalvos balansas* neįmanoma nustatyti *Eksportuoti atspindį*), priklausomas parametras yra išjungiamas, o jo paaiškinime nurodomas parametras, kurį reikia pakeisti.
{% endhint %}

***

## Ekranas

### Vaizdo miniatiūros skiriamoji geba

* **Tipas**: Išskleidžiamas meniu
* **Parinktys**: `Default (512 px)`, `1024 px`, `2048 px`, `Full resolution`
* **Numatytasis**: Numatytasis (512 pikselių)
* **Aprašymas**: Skiriamoji geba (ilgiausias kraštas, pikseliais), kuria atvaizduojamos vaizdų tinklelio miniatiūros. Didesnės reikšmės atrodo ryškiau, kai vaizdas padidinamas, tačiau įkeliama lėčiau ir sunaudoja daugiau atminties. Pilna skiriamoji geba atitinka originalaus vaizdo dydį.
* **Pastaba**: Tik rodymui — tai niekada neturi įtakos apdorojimui ar eksportuojamiems failams.

***

## Taikinio aptikimas

Šie nustatymai kontroliuoja, kaip Chloros aptinka ir apdoroja kalibravimo taikinius jūsų vaizduose. Abu jie aktyvūs tik tada, kai įjungta **Atspindžio kalibravimas / baltos spalvos balansas** (kitu atveju jie yra išblukinti, nes taikinio aptikimas visiškai praleidžiamas).

### Mažiausias kalibravimo mėginio plotas (px)

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 10 000 pikselių
* **Numatytasis**: 25 pikseliai
* **Aprašymas**: Nustato mažiausią plotą (pikseliais), reikalingą, kad aptiktas regionas būtų laikomas tinkamu kalibravimo taško pavyzdžiu. Mažesnės reikšmės leis aptikti mažesnius taškus, tačiau gali padidinti klaidingų teigiamų rezultatų skaičių. Didesnės reikšmės reikalauja didesnių ir aiškesnių taškų regionų aptikimui.
* **Kada reguliuoti**:
  * Padidinkite, jei gaunate klaidingus aptikimus dėl mažų vaizdo artefaktų
  * Sumažinkite, jei jūsų kalibravimo tikslai vaizduose atrodo maži ir nėra aptinkami

### Mažiausias tikslų grupuojimo slenkstis (0–100)

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 100
* **Numatytasis**: 60
* **Aprašymas**: Nustato grupavimo slenkstį, pagal kurį grupuojamos panašios spalvos sritys aptinkant kalibravimo taikinius. Didesnės reikšmės reikalauja, kad būtų sugrupuotos daugiau panašių spalvų, todėl taikinių aptikimas tampa konservatyvesnis. Mažesnės reikšmės leidžia didesnį spalvų skirtumą vienoje taikinių grupėje.
* **Kada reguliuoti**:
  * Padidinkite, jei kalibravimo taikiniai suskaidomi į kelis aptikimus
  * Sumažinkite, jei spalvų variacijų turintys kalibravimo taikiniai nėra visiškai aptinkami

***

## Apdorojimas

Šie nustatymai kontroliuoja, kaip Chloros apdoroja ir kalibruoja jūsų vaizdus.

### Vigneto korekcija

* **Tipas**: Žymės langelis
* **Numatytasis**: Įjungta (pažymėta)
* **Aprašymas**: Taiko vinjetės korekciją, siekiant kompensuoti objektyvo sukeliamą vaizdo kraštų patamsėjimą. Vinjetė – tai dažnas optinis reiškinys, kai dėl objektyvo savybių vaizdo kampai ir kraštai atrodo tamsesni nei centras.
* **Šalutinis poveikis**: Šis jungiklis taip pat leidžia pasirinkti, kokį *nekalibruotą atsarginį produktą* įrašo apdorojimo ciklas (žr. toliau).

### Atspindžio kalibravimas / baltos spalvos balansas

* **Tipas**: Varnelė
* **Numatytasis**: Įjungta (pažymėta)
* **Aprašymas**: Įjungia atspindžio kalibravimą — remiantis aptiktais kadre esančiais kalibravimo taškais ir (arba) DAQ šviesos jutiklio žemyn nukreiptų spindulių duomenimis, priklausomai nuo kameros ir to, kas yra prieinama. Tai normalizuoja atspindžio vertes visame duomenų rinkinyje ir užtikrina nuoseklius matavimus nepriklausomai nuo apšvietimo sąlygų.
* **Kai išjungta**: Tikslo aptikimas visiškai praleidžiamas, ir**nė viena kamera negali sukurti atspindžio produkto** — nei Survey3, veikianti pagal tikslus, nei „LATTICE DAQ“, veikianti pagal duomenų surinkimą. Priklausomi nustatymai (*Eksportuoti atspindį*, *Minimalus pakalibravimo intervalas* ir tikslo aptikimo slenksčiai) yra išblukinti.

### Nekalibruoti atsarginiai produktai: Eksportuoti jutiklio atsaką / Eksportuoti su koreguotu vinjetavimu

* **Tipas**: Dvi žymės langeliai
* **Numatytieji nustatymai**: Abu įjungti (pažymėti)
* **Aprašymas**: Kai kadro atspindžio kalibravimas neįmanomas (nerastas kalibravimo taikinys arba atspindžio kalibravimas išjungtas), jis įrašomas kaip *nekalibruotas atsarginis produktas*. **Kiekvienam kameros modeliui per vieną vaizdo įrašymo sesiją egzistuoja tik vienas iš dviejų atsarginių produktų**, pasirinktas naudojant *Vignette correction* jungiklį:
  * Vignette korekcija **įjungta**→ `Vignette_Corrected_Images/` (nustatoma pagal**Eksportuoti su vignette korekcija**)
  * Vignette korekcija **išjungta**→ `Sensor_Response_Images/` (nustatoma pagal**Eksportuoti jutiklio atsaką**)
* Rezervinis variantas, kuris nėra naudojamas, yra paryškintas pilka spalva. Nuėmus žymę nuo naudojamo varianto, tas failas iš viso nebus įrašytas.

### LATTICE eksporto produktai

Projektai, kuriuose yra LATTICE kadrų, kiekvienas importuotas LATTICE kadras vienu apdorojimo etapu išskaidomas į visus įjungtus **ir taikytinus**produktus. Šį paskirstymą valdo keturi žymimieji langeliai (visi pagal numatytuosius nustatymus**įjungti**):

| Nustatymas | Išvesties aplankas | Kas eksportuojama |
| --- | --- | --- |
| **Eksportuoti be „debayeringo“** | `Debayered_Images/` | Linijinis vaizdas be „debayeringo“: pašalintas kalibravimo paketo tamsos poslinkis, tada atliktas kraštų atpažinimu pagrįstas demozėjimas, be linijiškumo korekcijos, plokščio lauko ar spalvų atskyrimo — tas pats failas, kurį įrašas išsaugo debayeringo lygiu. Išsaugoma jutiklio natūrali orientacija (įrašyta XMP). Taikoma RGB ir multispektrinėms kameroms. |
| **Eksportuoti peržiūrą** | `Preview_Images/` | Ekrano peržiūra. RGB = baltos spalvos balansas (DAQ šviesos šaltinis, jei yra, kitaip – „gray-world“) + gama; daugiaspektrinė = dirbtinių spalvų išplėtimas. |
| **Eksportuoti spinduliavimą** | `Radiance_Images/` | „Float32“ spektrinis spinduliavimas, išreikštas W/m²/sr/nm. Tik daugiaspektrinėms (M3C/M3M) kameroms — netaikoma RGB pagrindiniams vaizdams. Visada įrašoma kaip 32 bitų TIFF, nepriklausomai nuo *Kalibruoto vaizdo formato* nustatymo. |
| **Eksportuoti atspindžio koeficientą**| `Reflectance_Calibrated_Images/` | „Uint16“ atspindžio koeficientas, mastelis toks, kad**32768 = atspindžio koeficientas 1,0** (pažymėta kaip XMP `Chloros:PixelScale`). Taikoma tik daugiaspektriniams vaizdams, įrašoma, kai atitinkamas `.daq` žemyn nukreiptas įrašas (arba kokybės patikrinimą išlaikęs kadro vidinis taikinys) apima kadrą. |

* RGB pagrindinės kameros perduoda debayeringą + peržiūrą; spinduliavimas/atspindžio koeficientas joms praleidžiami, nes netaikomi.
* Debayeringo/peržiūros bitų gylis priklauso nuo nustatymo *Kalibruotas vaizdo formatas*; spinduliavimas visada yra „float32“.
* Šie keturi perjungikliai neturi įtakos Survey3 apdorojimui.
* Multispektrinių M3C kamerų spinduliavimo ir atspindžio produktai taip pat gali įrašyti pikselio lygio **fiksavimo kaukę** į `clamp_masks/` aplanką šalia produktų aplankų, užfiksuodamos, kur juostos atskyrimo procesas apkarpė neigiamą vertę iki nulio. Žr. [Išvesties vaizdo formatai](../output-image-formats.md#clamp-masks).

Tie patys keturi perjungikliai egzistuoja be vartotojo sąsajos kaip „`chloros-cli process --debayered / --preview / --radiance / --reflectance`“. Jie pakeitė senąjį CLI „`--radiometric-output`“ žymeklį, kurio jau nebėra.

{% hint style="warning" %}
**Išjungus visus taikytinus produktus, apdorojimo ciklas baigiasi nesėkme.** Nuo 1.2.0 versijos apdorojimo ciklas, kuriam buvo užklausti produktai, bet nebuvo įrašytas joks vaizdo produktas, praneša apie nesėkmę, o CLI baigia veikimą su nelygiu nuliui rezultatu, vietoj to, kad tyliai praneštų apie sėkmę. Žurnale nurodomas produktas, kurio nepavyko įrašyti, ir priežastis. Tyčinis apdorojimo ciklas, skirtas tik metaduomenims (nieko neprašyta) vis tiek laikomas sėkmingu.
{% endhint %}

### Atšvaitumo šaltinis (projekto nustatymas, nustatomas per CLI)

Projekte taip pat saugoma informacija, kokį **atšvaitumo etaloną** naudoja LATTICE atšvaitumo produktas. Nustatymų skydelyje nėra specialaus valdiklio; reikšmė saugoma projekto konfigūracijoje kaip `Processing → "Target reflectance source"` ir nustatoma naudojant `chloros-cli process --reflectance-source {auto,target,daq}` (SDK parametras `configure()` neturi tam skirto raktinio žodžio):

* **`auto`** (numatyta reikšmė): kokybės užtikrinimo (QA) reikalavimus atitinkantis kalibravimo taikinys kadre tampa absoliučiu etalonu; jei taikinio nėra arba QA nepavyksta, grįžtama prie DAQ žemyn nukreipto spinduliavimo dalinio (ρ = πL/E). Tikslas, kuris išlaiko kokybės patikrinimą su žymekliu (nukritęs skydelis arba neveikianti juosta), nėra automatiškai teikiamas pirmenybė: tikslo, tikslo+DAQ ir tik DAQ grandinės bandomos pagal jų išmatuotą balą ant atidėtų skydelių.
* **`target`**: griežtas, tikslu grindžiamas atspindžio koeficientas — be DAQ pakeitimo.
* **`daq`**: DAQ autoritetingas atspindžio koeficientas; kadre esantys tikslai nenaudojami kaip etalonas.

Išsaugota reikšmė atitinkama neatsižvelgiant į didžiųjų ir mažųjų raidžių skirtumą, o keletas rašybos variantų priimami kaip sinonimai: `target`, `target_image`, `empirical` ir `empirical_line` – visi reiškia **tikslinį objektą**; `daq`, `dls`, `light_sensor` ir `sensor` visi reiškia**daq**. Viskas kita — įskaitant ir trūkstamą raktą — priskiriama**auto**.**Išmatuoti** tikslo skenavimai pagal vienetą ieškomi pagal tikslo vieneto serijinį numerį/QR kodą, pvz., `<serial>.csv`, trijose vietose: kataloge, nurodytame su `--target-reflectance-dir` (saugomame kaip `Processing → "Target reflectance dir"`), pačiame projekte esančiame `target_reflectance/` aplanke, ir kelyje, nurodytame aplinkos kintamajame `CHLOROS_TARGET_REFLECTANCE_DIR`. Jei to įrenginio išmatuotų skenavimų nėra, vietoj jų naudojama nominali paskelbta tikslinio modelio kreivė.

### „Debayer“ metodas

* **Tipas**: pasirinkimas iš išskleidžiamojo meniu
* **Parinktys**:
  * Standartinis (greitas, vidutinė kokybė)
  * Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė) \[Chloros+]
* **Numatytasis**: Standartinis (greitas, vidutinė kokybė)
* **Aprašymas**: Pasirenkamas demozėjimo algoritmas, naudojamas neapdorotiems „Bayer“ modelio jutiklio duomenims konvertuoti į spalvotus vaizdus. Metodas „Standartinis (greitas, vidutinė kokybė)“ užtikrina optimalų apdorojimo greičio ir vaizdo kokybės balansą. „Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė)“ \[Chloros+] naudoja aukštos kokybės, kraštus atpažįstantį demosaicingą, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą demosaicingo triukšmą. „Atsižvelgiantis į tekstūrą“ modelis veikimui reikalauja GPU atminties (VRAM). Rekomenduojame jį naudoti, jei turite >4 GB VRAM, kad apdorojimas vyktų greičiau.
* **Tik tuo atveju, jei eilutė yra išskleidžiamasis meniu**: dviejų variantų išskleidžiamasis meniu pasirodo tik tada, kai**abi** sąlygos yra teisingos — esate prisijungę su tinkamu „Chloros+“ abonementu, **ir** projekte nėra „LATTICE“ įrašų. Kitais atvejais eilutė rodoma kaip paprastas tekstas „`Standard (Fast, Medium Quality)`“, be jokios pasirinkimo galimybės.
* **„LATTICE“ pastaba**: Nėra „LATTICE“ apmokyto „Texture Aware“ modelio, o apdorojimo grandinė priverčia taikyti standartinį demozikavimą „LATTICE“ kadrams, nepriklausomai nuo išsaugotos vertės. Jei į projektą, kuriame jau buvo pasirinkta „Texture Aware“, įtraukiate „LATTICE“ aplanką, Chloros perrašo nustatymą atgal į „Standard“, o ne palieka pasenusią vertę „`project.json`.

### Mažiausias pakartotinio kalibravimo intervalas

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 3 600 sekundžių
* **Numatytasis**: 0 sekundžių
* **Aprašymas**: Nustato minimalų laiko intervalą (sekundėmis) tarp kalibravimo taškų naudojimo. Nustačius 0, Chloros naudos kiekvieną aptiktą kalibravimo tašką. Nustačius didesnę vertę, Chloros naudos tik tuos kalibravimo taškus, kurie yra atskirti bent jau šiuo sekundžių skaičiumi, taip sutrumpindamas duomenų rinkinių, kuriuose dažnai fiksuojami kalibravimo taškai, apdorojimo laiką.
* **Kada reguliuoti**:
  * Nustatykite 0, kad pasiektumėte maksimalų kalibravimo tikslumą, kai apšvietimo sąlygos kinta
  * Padidinkite (pvz., iki 60–300 sekundžių), kad apdorojimas vyktų greičiau, kai apšvietimas yra pastovus ir turite dažnai užfiksuotus kalibravimo taškų vaizdus

### Šviesos jutiklio laiko juostos nuokrypis

* **Tipas**: Skaičius
* **Diapazonas**: nuo -12 iki +12 valandų
* **Numatytasis**: 0 valandų
* **Aprašymas**: Nurodo šviesos jutiklio duomenų laiko žymų laiko juostos nuokrypį (valandomis nuo UTC), naudojamą suderinant šviesos jutiklio įrašus su vaizdų užfiksavimo laiku. Naujausi `.daq` įrašai turi savo laiko juostos kilmės informaciją, todėl tai daugiausia reikalinga senesniems įrašams, užfiksuotiems vietos laiku.

### Taikyti PPK pataisas

* **Tipas**: Varnelė
* **Numatytasis**: Išjungta (nepažymėta)
* **Aprašymas**: Įjungia galimybę naudoti PPK (Post-Processed Kinematic) korekcijas iš MAPIR DAQ įrašymo įrenginių, turinčių GPS (GNSS). Įjungus šią funkciją, Chloros naudos visus .daq žurnalo failus, kuriuose yra ekspozicijos pin duomenys, esančius jūsų projekto kataloge, ir pritaikys tikslias geolokacijos korekcijas jūsų vaizdams.
* **Reikalavimas**: jūsų projekto kataloge turi būti .daq žurnalo failas su ekspozicijos žymių įrašais
* **Kada įjungti**: rekomenduojama visada įjungti PPK korekciją, jei jūsų .daq žurnalo faile yra ekspozicijos grįžtamojo ryšio įrašai.

### Ekspozicijos kontaktas 1

* **Tipas**: Išskleidžiamas meniu
* **Matomumas**: Matomas tik tada, kai įjungta funkcija „Taikyti PPK korekcijas“ IR yra ekspozicijos duomenų apie 1-ąjį kontaktą
* **Parinktys**:
  * Projekte aptikti fotoaparatų modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis nustatymas**: Pasirenkama automatiškai pagal projekto konfigūraciją
* **Aprašymas**: Priskiria konkrečią kamerą ekspozicijos kontaktui Nr. 1 PPK laiko sinchronizavimui. Ekspozicijos kontaktas fiksuoja tikslų laiką, kada suveikia kameros užraktas, o tai yra labai svarbu tiksliai nustatyti PPK geografinę vietą.
* **Automatinio pasirinkimo veikimas**:
  * Viena kamera + vienas kontaktas: kamera parenkama automatiškai
  * Viena kamera + du kontaktai: 1-asis kontaktas automatiškai priskiriamas kamerai
  * Kelios kameros: reikia pasirinkti rankiniu būdu

### Ekspozicijos kontaktas Nr. 2

* **Tipas**: Pasirinkimas iš išskleidžiamojo meniu
* **Matomumas**: Matomas tik tada, kai įjungta funkcija „Taikyti PPK pataisas“ IR yra ekspozicijos duomenų kontaktui Nr. 2
* **Parinktys**:
  * Projekte aptikti kamerų modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis**: Pasirenkama automatiškai pagal projekto konfigūraciją
* **Aprašymas**: Naudojant dviejų kamerų konfigūraciją, ekspozicijos kontaktui Nr. 2 priskiria konkrečią kamerą PPK laiko sinchronizavimui.
* **Automatinio pasirinkimo veikimas**:
  * Viena kamera + vienas kontaktas: 2-asis kontaktas automatiškai nustatomas į „Nenaudoti“
  * Viena kamera + du kontaktai: 2-asis kontaktas automatiškai nustatomas į „Nenaudoti“
  * Kelios kameros: Reikalingas rankinis pasirinkimas
* **Pastaba**: tos pačios kameros negalima priskirti vienu metu ir 1-ajai, ir 2-ajai jungčiai.

***

## DAQ šviesos jutiklis

Šis skyrius rodomas „Projekto nustatymuose“ ir jame išvardyti visi projekte esantys DAQ žemyn nukreiptų spindulių failai — `.daq` įrašai ir DAQ-M `.csv` žemyn nukreiptų spindulių žurnalai. Įrašai, padaryti skirtuke „Šviesos jutikliai“, automatiškai pridedami prie atidaryto projekto.

<!-- SCREENSHOT-NEEDED: Project Settings "DAQ Light Sensor" section of a project containing at least one .daq file, showing the "Cap override (all files)" dropdown and a per-file row with its resolved cap. -->



Kiekvienoje eilutėje nurodytas failas, jutiklio modelis ir tam failui faktiškai taikoma difuzoriaus dangtelio korekcija. Virš eilučių yra vienas visam projektui bendras valdymo elementas:

### Dangtelio korekcijos perrašymas (visi failai)

* **Tipas**: išskleidžiamasis meniu
* **Parinktys**: `Auto` ir difuzoriaus dangtelio korekcijos profiliai, galiojantys projekte esantiems jutiklių tipams
* **Numatytasis**: „Auto“
* **Išsaugota kaip**: `Processing → "DAQ cap id"` (numatyta reikšmė `auto`)
* **Aprašymas**: `Auto` naudoja kiekviename faile užregistruotą dangtelį (jei nieko neužregistruota, daroma prielaida, kad tai „Sunshine“ dangtelis, tačiau DAQ-U, DAQ-M ir DAQ-E tiekiami be dangtelio, o Chloros negali matyti, kas yra uždėta, todėl apdorojimo metu rodomas įspėjimas dėl numanomo dangtelio: pasirinkite tą dangtelį, kuris buvo uždėtas). Pasirinkus konkretų dangtelį, jis pakeičia **visus** projekte esančius žemyn nukreiptus failus, išskyrus DAQ-E-S failus, kurių difuzorius yra kalibravimo dalis; projekte, kuriame yra tik DAQ-E-S, išskleidžiamasis meniu nerodomas: neapdoroti įrašai koreguojami naudojant jį, o įrašams, kuriuose jau yra taikytas dangtelis, nustatoma nauja atskaita (įrašyta korekcija atšaukiama ir pritaikoma pasirinktoji).
* **Svarbu**: Pasirinktas dangtelis turi atitikti tą, kuris buvo fiziškai uždėtas įrašymo metu. Nei jutiklis, nei programinė įranga negali aptikti fizinio dangtelio — netinkamas dangtelio ID klaidingai koreguoja spektrus.

Sąmoningai numatytas **vienas** visam projektui bendras nustatymas, o ne atskiri išskleidžiamieji meniu kiekvienam failui: šis nustatymas taikomas kiekvienam žemyn nukreiptam šaltiniui projekte.

***

## Masyvo suderinimas

Šis skyrius rodomas **tik** tada, kai bent viename projekto vaizde yra modulio su moduliu suderinimo transformacija, kurią LATTICE masyvai pažymi įrašymo metu (XMP `Chloros:Alignment*` žymos). Čia rodoma, kiek vaizdų turi suderinimo žymes, kuri kamera yra etalonas (`REF` žymė), taip pat pateikiama vaizdų skaičiaus lentelė pagal kameras.

<!-- SCREENSHOT-NEEDED: Project Settings "Array Alignment" section for an imported LATTICE array capture set, showing the tagged-image count, the per-camera rows with the REF badge, and the three controls (Apply array alignment, Crop to common overlap, Resampling). -->

### Taikyti masyvo suderinimą

* **Tipas**: Varnelė
* **Numatytasis**: Įjungta (pažymėta)
* **Išsaugoma kaip**: `Processing → "Array alignment"`
* **Aprašymas**: Kiekvieną apdorotą produktą (debayering / peržiūra / spinduliavimas / atspindys / indeksas) iškraipo pagal masyvo bendrą atskaitos geometriją, naudodamas fotografavimo metu užfiksuotą transformaciją. Išjungta = eksportuojama pagal kiekvieno jutiklio natūralią geometriją.

### Apkarpyti iki bendro persidengimo

* **Tipas**: Žymės langelis (veikia tik tada, kai įjungta funkcija *Taikyti masyvo suderinimą*)
* **Numatytasis nustatymas**: Įjungta (pažymėta)
* **Išsaugoma kaip**: `Processing → "Array alignment crop"`
* **Aprašymas**: Apkarpo suderintus eksportus iki srities, kurią dalijasi visi kameros moduliai, todėl kiekviena juosta užima tą patį plotą. Išjungus išlaikomas visas jutiklio plotas (juodas užpildymas už šaltinio ribų).

### Perdiskretizavimas

* **Tipas**: Išskleidžiamas meniu (veikia tik tada, kai įjungta parinktis *Taikyti masyvo suderinimą*)
* **Parinktys**: `Bilinear (smooth, default)`, `Nearest (preserve exact values)`, `Cubic (sharpest)`
* **Numatytasis**: Bilinearinis
* **Išsaugoma kaip**: `Processing → "Array alignment interpolation"`
* **Aprašymas**: Interpoliavimas, naudojamas suderinimo deformacijai. Parinktis „Artimiausias“ išsaugo tikslias šaltinio reikšmes (be pikselių maišymo) griežtai radiometrinei analizei; parinktis „Bilinearinis“ geriausiai tinka žemėlapių sudarymui ir vizualiniam naudojimui.

Tos pačios trys parinktys be priesagos yra `chloros-cli process --array-alignment`, `--array-alignment-crop` ir `--array-alignment-interp {bilinear,nearest,cubic}`.

***

## Indeksas

Šie nustatymai leidžia konfigūruoti multispektrinius indeksus analizės ir vizualizavimo tikslais.

### Pridėti indeksą

* **Tipas**: Specialus indeksų konfigūracijos skydelis
* **Aprašymas**: Atidaro interaktyvų skydelį, kuriame galite pasirinkti ir konfigūruoti multispektrinius augmenijos indeksus (NDVI, NDRE, EVI ir kt.), kurie bus apskaičiuojami vaizdo apdorojimo metu. Galite pridėti kelis indeksus, kiekvienam nustatydami atskirus vizualizavimo parametrus.
* **Galimi indeksai**: Grafinės vartotojo sąsajos išskleidžiamajame meniu yra**27** iš anksto apibrėžtos daugiaspektrinių indeksų formulės (visą sąrašą, įskaitant pavadinimus, kuriuos taip pat priima CLI/SDK `--indices` parinktis).
* **Funkcijos**:
  * Pasirinkite iš iš anksto apibrėžtų indeksų formulių
  * Perkelkite savo kameros filtro kanalus į formulės juostų lizdus
  * Nustatykite vizualizacijos spalvų gradientus (LUT – paieškos lentelės)
  * Nustatykite slenkstines vertes ir apribojimo režimus
  * Sukurkite pasirinktines indeksų formules
* **Pastaba**: Indeksai neskaičiuojami vienos juostos „LATTICE M3M“ monochromatinėms kameroms — daugiajuostiniai indeksai vienoje juostoje nėra apibrėžti. Tai neturi įtakos Survey3 ir „LATTICE M3C“ kameroms.

<!-- SCREENSHOT-NEEDED: Project Settings > Index section with one index added and expanded: the filter dropdown, the formula dropdown open showing preset names, the coloured channel circles above the rendered formula, and the "+ Add LUT" button below it. -->



Kiekvienas jūsų pridėtas indeksas pateikia savo formulę kaip matematinį išraišką, su spalvotu apskritimu kiekvienoje juostos lizdo vietoje: raudona = Red, žalia = Green, mėlyna = Blue, oranžinė = Orange, ciano = Cyan, violetinė = NIR, magenta = RE. Norėdami susieti, vilkite ratą iš eilutės virš formulės į lizdą; norėdami išvalyti susietą lizdą, dukart spustelėkite jį. Indeksas apskaičiuojamas tik tada, kai kiekviename lizde, kurį naudoja formulė, yra kanalas.

### Pasirinktinės formulės („Chloros+“ funkcija)

* **Tipas**: Pasirinktinių formulių apibrėžimų masyvas
* **Prieinamumas**: Reikia prisijungti su atitinkamu „Chloros+“ abonementu.
* **Aprašymas**: Leidžia kurti ir išsaugoti pasirinktines multispektrinių indeksų formules, naudojant juostų matematiką. Pasirinktinės formulės išsaugomos kartu su jūsų projekto nustatymais ir gali būti naudojamos taip pat kaip ir įdiegtieji indeksai.
* **Kaip sukurti**:
  1. Indekso konfigūracijos skydelyje atidarykite pasirinktinių formulių skaičiuoklę
  2. Parašykite formulę naudodami **juostų lizdų simbolius**, o ne juostų pavadinimus
  3. Išsaugokite formulę su aprašomuoju pavadinimu — tada ji pasirodys formulės išskleidžiamojo meniu apačioje, o jūs galėsite perkelti savo kameros kanalų apskritimus į jos lizdus lygiai taip pat, kaip ir naudojant įdiegtą išankstinį nustatymą
* **Formulės sintaksė**:
  * Juostų lizdai: `x`, `y`, `z`, `a`, `b`, `c` — šešios pozicijos, kurias priskiriate realiems kanalams vilkdami
  * Operatoriai: `+`, `-`, `*`, `/`, `^` ir `()`, skirti grupavimui
  * Funkcijos: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
* **Kodėl simboliai, o ne grupių pavadinimai**: formulė, užrašyta kaip `(y-x)/(y+x)`, veikia su bet kuria kamera, nes „vilk ir paleisk“ priskyrimas nusprendžia, ar `y` yra NIR filtras su 850 nm bangos ilgiu (RGN) ar NIR filtras su 808 nm bangos ilgiu (OCN). Įdiegtos išankstinės nustatymų parinktys saugomos tuo pačiu būdu — tikslią visų 27 simbolių formą rasite [Daugiaspektrinių indeksų formulėse](multispectral-index-formulas.md).
* **Kur jos veikia**: pasirinktinės formulės išsaugomos kartu su projekto nustatymais ir gali būti naudojamos tiek [Indekso/LUT bandymų aplinkoje](../image-viewer-gui/index-lut-sandbox.md), tiek apdorojimo metu. Jų**nepriima** CLI/SDK `--indices` vardų sąrašas, kuris išplečia tik 22 įdiegtų nustatymų pavadinimus.

***

## Eksportavimas

Šie nustatymai kontroliuoja eksportuojamų apdorotų vaizdų formatą ir kokybę.

### Kalibruoto vaizdo formatas

* **Tipas**: Išskleidžiamas meniu
* **Parinktys**:
  * **TIFF (16 bitų)** – Nesuspaustas 16 bitų TIFF formatas
  * **TIFF (8 bitai)** – nesuspaustas 8 bitų TIFF formatas
  * **TIFF (32 bitai, procentais)** – 32 bitų slankiojo kablelio TIFF su atspindžio vertėmis procentais
  * **PNG (8 bitai)** – suspaustas 8 bitų PNG formatas
  * **JPG (8 bitų)** – suspaustas 8 bitų JPEG formatas
* **Numatytasis**: TIFF (16 bitų); visiškai 8 bitų projekte rodomas TIFF (8 bitų) ir siūlomi tik 8 bitų formatai
* **Aprašymas**: Pasirenkamas failo formatas, kuriuo bus išsaugoti apdoroti ir kalibruoti vaizdai. Eksportuoti vaizdai išsaugomi atitinkamo formato pakatalogiuose, esančiuose kiekvienos kameros aplanke (`tiff16`, `tiff32`, `png8`, `jpg8`), kur kiekvienam produktui skiriamas po vieną „`<Product>_Images/`“ aplanką. Eksportuoti failai išlaiko šaltinio failo pavadinimą – produktą identifikuoja aplankas, o ne failo pavadinimo priesaga.
* **Rekomenduojami formatai**:
  * **TIFF (16 bitų)**: Rekomenduojamas mokslinei analizei ir profesionaliems darbo procesams. Užtikrina maksimalią duomenų kokybę be suspaudimo artefaktų. Geriausiai tinka multispektrinei analizei ir tolesniam apdorojimui GIS programinėje įrangoje.
  * **TIFF (32 bitai, procentais)**: Geriausiai tinka darbo procesams, kuriuose atspindžio vertės turi būti pateiktos procentais (0–100 %). Užtikrina maksimalų radiometrinių matavimų tikslumą.
  * **PNG (8 bitai)**: Tinka peržiūrai internete ir bendrai vizualizacijai. Mažesni failų dydžiai su nesuspaudimu be nuostolių, tačiau sumažintas dinaminis diapazonas.
  * **JPG (8 bitai)**: Mažiausi failų dydžiai, geriausiai tinka tik peržiūrai ir rodymui internete. Naudojamas suspaudimas su nuostoliais, kuris netinka mokslinei analizei.
* **Pastaba**: „LATTICE“ spinduliavimas visada eksportuojamas kaip 32 bitų plūduriuojančiojo kintamojo tipo TIFF, nepriklausomai nuo šio nustatymo.

***

## Projekto šablono išsaugojimas

Ši funkcija leidžia išsaugoti dabartinius projekto nustatymus kaip pakartotinai naudojamą šabloną.

* **Tipas**: Teksto įvedimas + mygtukas „Išsaugoti“
* **Aprašymas**: Įveskite aprašomąjį pavadinimą savo nustatymų šablonui ir spustelėkite išsaugojimo piktogramą. Šablone bus išsaugoti visi dabartiniai projekto nustatymai (tikslo aptikimas, apdorojimo parinktys, indeksai, ir eksporto formatą), kad juos būtų galima lengvai pakartotinai naudoti būsimuose projektuose. Šablonai saugomi aplanke „`Project Templates/`“, esančiame jūsų projekto išsaugojimo aplanke, taip pat juos galima pasirinkti arba eksportuoti iš pagrindinio meniu (*Pasirinkti šabloną* / *Išsaugoti šabloną* / *Eksportuoti šabloną*).
* **Naudojimo atvejai**:
  * Sukurkite šablonus skirtingoms kamerų sistemoms (RGB, multispektrinėms, NIR)
  * Išsaugokite standartines konfigūracijas konkrečioms pasėlių rūšims ar analizės darbo eigoms
  * Dalinkitės nuosekliais nustatymais su visa komanda
* **Kaip naudoti**:
  1. Nustatykite visus norimus projekto parametrus
  2. Įveskite šablono pavadinimą (pvz., „RedEdge Survey3 NDVI Standard“)
  3. Spustelėkite išsaugojimo piktogramą
  4. Dabar šabloną galima įkelti kuriant naujus projektus

***

## Projekto išsaugojimo aplankas

Šis nustatymas nurodo, kur pagal numatytuosius nustatymus išsaugomi nauji projektai.

* **Tipas**: Katalogo kelio rodymas + mygtukas „Redaguoti“
* **Numatytasis (Windows)**: „`Documents\MAPIR\Chloros Projects`“ jūsų aplanke „Dokumentai“ — paprastai „`C:\Users\[Username]\Documents\MAPIR\Chloros Projects`“, arba ten, kur „OneDrive“ ar klajojantis profilis perkėlė aplanką „Dokumentai“
* **Numatytasis (Linux)**: `~/Documents/MAPIR/Chloros Projects`
* **Aprašymas**: Rodo dabartinį numatytąjį katalogą, kuriame kuriami nauji Chloros projektai. Spustelėkite redagavimo piktogramą, kad pasirinkite kitą katalogą. Šis pakeitimas saugomas kaip viena teksto eilutė faile „`~/.chloros/working_directory.txt`“ — Windows sistemoje tai yra „`C:\Users\<Username>\.chloros\working_directory.txt`“. Jei to failo nėra arba jame nurodytas nebeegzistuojantis kelias, Chloros grįžta prie aukščiau nurodyto numatytojo. CLI skaito ir rašo tą patį failą, todėl „`chloros-cli`“ ir GUI visada sutaria dėl projektų buvimo vietos.
* **Projekto šablonai** saugomi šio katalogo `Project Templates/` pakatalogyje.
* **Kada keisti**:
  * Nustatykite tinklo diską, jei dirbate komandoje
  * Perkelkite į diską su didesne saugojimo vieta, jei dirbate su dideliais duomenų rinkiniais
  * Suskirstykite projektus pagal metus, klientą ar projekto tipą į skirtingus aplankus
* **Pastaba**: Šio parametro pakeitimas turi įtakos tik NAUJIEMS projektams. Esami projektai lieka savo pradinėse vietose.
* **Windows kelio ilgis**: jei Windows ilgi keliai nėra įjungti (diegimo programos parinktis **Įjungti Windows ilgus kelius**), projekto aplankas, kurio pavadinimas ilgesnis nei maždaug 130 simbolių, prieš apdorojimą bus atmestas, nes jo rezultatai viršytų „Windows “ 260 simbolių kelio ribą. Žr. [Apdorojimo pradžia](../processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).

***

## Nustatymų išsaugojimas

Chloros projektas yra **aplankas**. Visi projekto nustatymai išsaugomi jame esančiame `project.json`; kartu su jais prisijungusi įranga įsimenama failuose „`cameras.json`“ ir „`sensors.json`“, todėl vėl atidarius projektą, vėl prisijungia jo kameros ir šviesos jutikliai. Vėl atidarius projektą, visi nustatymai atkuriami tiksliai taip, kaip juos palikote. Išsaugotus projektus taip pat galima valdyti be grafinės sąsajos naudojant „`chloros-cli project`“ arba SDK „`open_project`“.

### Nustatymų hierarchija

Nustatymai taikomi tokia tvarka:

1. **Sistemos numatyti nustatymai** – Chloros apibrėžti įdiegti numatyti nustatymai 2. **Šablono nustatymai** – jei kurdami projektą įkeliate šabloną
3. **Išsaugoti projekto nustatymai** – su projekto failu išsaugoti nustatymai
4. **Rankiniai koregavimai** – bet kokie pakeitimai, kuriuos atliekate per dabartinę sesiją

### Nustatymai ir vaizdų apdorojimas

Apdorojimo nustatymai nuskaitomi, kai pradedamas apdorojimo ciklas. Nustatymo pakeitimas neturi atgalinio poveikio produktams, kurie jau yra diske – norėdami pritaikyti naujus nustatymus, apdorojimą reikia paleisti iš naujo. Kai kurie nustatymai visiškai neturi įtakos apdorojimui:

* Vaizdo miniatiūros skiriamoji geba (tik rodymui)
* Išsaugoti projekto šabloną
* Išsaugoti projekto aplanką

***

## Konfigūracijos raktų žinynas

Automatizavimui (CLI: `--config`, SDK: `configure` arba tiesioginiam `project.json` skaitymui) šie yra tikslūs raktai, esantys po `Project Settings`:

| Raktų kelias | Tipas | Numatytasis |
| --- | --- | --- |
| `Display → Image Thumbnail Resolution` | `"512" \| "1024" \| "2048" \| "full"` | `"512"` |
| `Target Detection → Minimum calibration sample area (px)` | skaičius 0–10000 | `25` |
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
| `Processing → Debayer method` | `"Standard (Fast, Medium Quality)" \| "Texture Aware (Slow, Highest Quality)"` | Standartinis |
| `Processing → Minimum recalibration interval` | skaičius 0-3600 | `0` |
| `Processing → Light sensor timezone offset` | skaičius -12..12 | `0` |
| `Processing → Apply PPK corrections` | bool | `false` |
| `Processing → DAQ cap id` | ribojimo profilio identifikatorius arba `"auto"` | `"auto"` |
| `Processing → Target reflectance source` | `"auto" \| "target" \| "daq"` | `"auto"` |
| `Index → Add index` | indeksų konfigūracijų sąrašas | `[]` |
| `Export → Calibrated image format` | `"TIFF (16-bit)" \| "TIFF (8-bit)" \| "TIFF (32-bit, Percent)" \| "PNG (8-bit)" \| "JPG (8-bit)"` | `"TIFF (16-bit)"` |

Raktų `Array alignment` reikšmės įrašomos pirmą kartą, kai atvaizduojamas skyrius „Array Alignment“ arba juos nustato automatizavimo iškvietimas. Kol jų nėra, apdorojimo grandinė naudoja tas pačias reikšmes, kurios nurodytos aukščiau (`true`, `true`, bilinearinis), todėl projektas „.json“ be jų veikia lygiai taip pat kaip ir tas, kuriame jie yra.

### Raktų, saugomų „`project.json`“, kuriuos neįmanoma valdyti nustatymų skydelyje

Jie yra tame pačiame `Project Settings` medyje ir yra nuskaitomi apdorojimo metu, tačiau šoninėje juostoje jų valdiklio nerastumėte:

| Raktų kelias | Tipas | Numatytasis | Nustato |
| --- | --- | --- | --- |
| `Processing → LATTICE input level` | `"auto" \| "raw" \| "debayered" \| "processed"` | `"auto"` | `chloros-cli process --input-level`, SDK `input_level=`. Pakeičia „LATTICE“ įvesties TIFF failų interpretavimą; `auto` nustato remdamasis kiekvieno failo `Chloros:ProcessingLevel` XMP žyma ir kanalų skaičiumi. Ignoruojama Survey3 `.raw` įrašams. Sąmoningai nėra GUI nustatymo — „auto“ yra teisingas pasirinkimas visais įprastais atvejais. |
| `Processing → Target reflectance dir` | kelio eilutė | `""` | `chloros-cli process --target-reflectance-dir`, arba projekto tikslinis API |
| `Processing → Target reflectance config` | žodynas, suskirstytas pagal kameros serijinį numerį | `{}` | Tikslo, esančio kadre, registravimas (režimas `fixed_block` / `fixed_strip` / `aruco`) |
| `Processing → DAQ-U log path` | kelio eilutė | `""` | SDK `process_folder(daq_log_path=…)`. Nurodo į `.daq` įrašą arba jų aplanką |
| `Target Detection → Minimum calibration target squares` | skaičius | `4` | Senoji numatytoji reikšmė; be valdymo ir be CLI žymės |
| `UI → Grid thumbnail size` | skaičius | `160` | Pats vaizdų tinklelio miniatiūrų mastelio slankiklis |

Dvi peržiūros nuostatos saugomos **aukščiausiame `project.json` lygyje**, visiškai už `Project Settings` ribų, nes jos yra susijusios su rodymo būsena, o ne apdorojimo nustatymais:

| Raktinis kelias | Tipas | Numatytasis | Nustato |
| --- | --- | --- | --- |
| `viewer_display → gsd_bin` | sveikasis skaičius 1–256 | `1` | Vaizdo skirtuko GSD (px) valdiklis — žr. [Vaizdo atidarymas visame ekrane](../image-viewer-gui/opening-an-image-full-screen.md) |

***

## Geriausia praktika

1. **Pradėkite nuo numatytųjų nustatymų**: Numatytieji nustatymai puikiai tinka daugumai MAPIR kamerų sistemų ir tipinių darbo eigų.
2. **Sukurkite šablonus**: Optimizavę nustatymus konkrečiam darbo srautui ar kamerai, išsaugokite juos kaip šabloną, kad užtikrintumėte nuoseklumą visuose projektuose.
3. **Išbandykite prieš pradedant visą apdorojimą**: Eksperimentuodami su naujais nustatymais, išbandykite juos su nedideliu vaizdų rinkiniu, prieš apdorodami visą duomenų rinkinį.
4. **Užfiksuokite savo nustatymus**: Naudokite aprašomuosius šablonų pavadinimus, nurodančius kamerų sistemą, apdorojimo tipą ir numatytą paskirtį (pvz., „Survey3\_RGB\_NDVI\_Agriculture“).
5. **Eksporto formato pasirinkimas**: Pasirinkite eksporto formatą atsižvelgdami į galutinę paskirtį:
   * Mokslinė analizė → „TIFF“ (16 bitų arba 32 bitų)
   * GIS apdorojimas → TIFF (16 bitų)
   * Greitas vizualizavimas → PNG (8 bitai)
   * Dalijimasis internete → „JPG“ (8 bitai)

***

Daugiau informacijos apie daugiaspektrinius indeksus Chloros programoje rasite puslapyje [Daugiaspektrinių indeksų formulės](multispectral-index-formulas.md).
