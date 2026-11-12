# Projekto nustatymų konfigūravimas

Prieš apdorojant vaizdus, svarbu sukonfigūruoti projekto nustatymus taip, kad jie atitiktų jūsų darbo eigos reikalavimus. Skirtuke „Projekto nustatymai“ <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> galima visapusiškai valdyti kalibravimą, apdorojimo parinktis, multispektrinius indeksus ir eksporto formatus.

## Kaip pasiekti projekto nustatymus

1. Atidarykite savo projektą programoje Chloros
2. Kairiajame šoniniame meniu spustelėkite piktogramą **„Projekto nustatymai“** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">
3. Skydelyje „Projekto nustatymai“ rodomos visos konfigūracijos parinktys

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption><p>Skirtukas „Projekto nustatymai“ — rodymas, taikinio aptikimas ir apdorojimas</p></figcaption></figure>

{% hint style="info" %}
**Nustatymai išsaugomi automatiškai** kartu su projektu. Atidarius projektą iš naujo, visi nustatymai atkuriami.
{% endhint %}

***

## Greitas nustatymas dažniausiai naudojamiems darbo srautams

### Numatytieji nustatymai (rekomenduojami daugumai vartotojų)

Numatytieji nustatymai puikiai tinka tipiniams Survey3 ir „LATTICE“ darbo srautams:

* ✅ **Vignette korekcija**: Įjungta
* ✅ **Atspindžio kalibravimas / baltos spalvos balansas**: Įjungta (naudojami MAPIR taikiniai ir (arba) DAQ šviesos jutiklio duomenys)
* ✅ **„Debayer“ metodas**: Standartinis (greitas, vidutinė kokybė)
* ✅ **Eksporto formatas**: TIFF (16 bitų) (TIFF (8 bitų) projektams, kuriuose visi duomenys yra 8 bitų)
* ✅ **Visi eksporto produktai**: Įjungta (LATTICE automatiškai išskirsto duomenis į „Debayer“, „Peržiūra“, „Spinduliavimas“ ir „Atspindžio koeficientas“)

Tiesiog importuokite savo vaizdus ir pradėkite apdorojimą naudodami šiuos numatytuosius nustatymus.

***

## Projekto nustatymų apžvalga

Projekto nustatymų skydelis suskirstytas į toliau nurodytus skyrius. Du papildomi skyriai — **DAQ šviesos jutiklis**ir**Matricos suderinimas** — pasirodo automatiškai, jei jūsų projekte yra atitinkami failai. Išsamią dokumentaciją rasite [Projekto nustatymai](../project-settings/project-settings.md).

### Rodymas

* **Vaizdų miniatiūrų skiriamoji geba**: Vaizdų tinklelio miniatiūrų skiriamoji geba. Parinktys:**Numatytasis (512 px)**,**1024 px**,**2048 px**,**Pilna skiriamoji geba**. Skirta tik rodymui – niekada neturi įtakos apdorojimui. Didesnės reikšmės atrodo ryškesnės priartinus vaizdą, tačiau įkeliama lėčiau.

### Tikslo aptikimas

Nustato, kaip Chloros atpažįsta kalibravimo tikslus jūsų vaizduose.

**Pagrindiniai nustatymai:**

* **Mažiausias kalibravimo mėginio plotas (px)**: Dydžio riba tikslo aptikimui (numatyta:**25**, diapazonas 0–10000)
* **Minimalus tikslų grupuojimo koeficientas (0–100)**: panašumo riba, reikalinga tikslų sričių grupavimui (numatyta reikšmė:**60**)**Kada reguliuoti:**

* Padidinkite mėginio plotą, jei gaunami klaidingi aptikimai
* Sumažinkite, jei tikslai neaptinkami
* Reguliuokite grupavimą, jei tikslai suskaidomi į kelis aptikimus

{% hint style="info" %}
Šie nustatymai yra išblukinti, kai išjungta funkcija **Atspindžio kalibravimas / baltos spalvos balansas** — kai ji išjungta, tikslų aptikimas visiškai neveikia.
{% endhint %}

### Apdorojimas

Pagrindiniai vaizdo apdorojimo ir kalibravimo parametrai.

**Pagrindiniai nustatymai:**

* **Vignette korekcija**: Kompensuoja objektyvo tamsėjimą kraštuose ✅ Rekomenduojama
* **Atspindžio kalibravimas / baltos spalvos balansas**: Kalibruoja vaizdus naudojant aptiktus taikinius (Survey3) ir (arba) DAQ šviesos jutiklio duomenis („LATTICE“) ✅ Rekomenduojama
* **„Debayer“ metodas**: algoritmas, skirtas RAW failų konvertavimui į 3 kanalų multispektrinius
* **Minimalus pakartotinio kalibravimo intervalas**: minimalus laikas sekundėmis tarp kalibravimo taškų naudojimo (numatyta reikšmė:**0** = naudoti visus, diapazonas 0–3600)**Nekalibruoti atsarginiai produktai:**Kai kadro atspindžio kalibravimas neįmanomas (nėra taikinio arba kalibravimas išjungtas), jis eksportuojamas kaip vienas iš dviejų atsarginių produktų —**kiekvienam vykdymo ciklui egzistuoja tik vienas iš šios poros**, pasirinktas pagal „Vignette“ korekcijos jungiklį:

* **Eksportuoti jutiklio atsaką**: įrašo `Sensor_Response_Images` — naudojama, kai „Vignette“ korekcija yra**išjungta**
* **Eksportuoti su vignette korekcija**: įrašo `Vignette_Corrected_Images` — naudojama, kai vignette korekcija yra**įjungta**Neaktyvus žymimasis langelis yra išblukintas. Pašalinus žymę iš aktyviojo langelio, tas failas iš viso nebus įrašomas.**LATTICE eksporto produktai** (rodomi kiekvienam projektui; jie taikomi LATTICE užfiksuotiems vaizdams):

* **Eksportuoti be „debayeringo“**: linijinis vaizdas be mozaikos (`Debayered_Images`): pašalintas kalibravimo paketo tamsos poslinkis, tada pašalinta mozaika, be plokščio lauko ar atskyrimo — tas pats failas, kurį įrašas išsaugo be mozaikos lygiu — jutiklio natūralioje orientacijoje. Taikoma RGB ir multispektriniams moduliams.
* **Eksportuoti peržiūrą**: ekrano peržiūra (`Preview_Images`). RGB = baltos spalvos balansas (DAQ šviesos šaltinis, jei yra, kitaip – „gray-world“) + gama; multispektrinis = dirbtinių spalvų išplėtimas.
* **Eksportuoti spinduliavimą**: „float32“ spektrinis spinduliavimas (`Radiance_Images`, W/m²/sr/nm). Tik daugiaspektriniams moduliams — netaikoma RGB pagrindiniams failams.
* **Eksportuoti atspindžio koeficientą**: „uint16“ atspindžio koeficientas (`Reflectance_Calibrated_Images`, DN 32768 = ρ 1,0), kai kadrą dengia „`.daq`“ žemyn nukreiptas matavimas arba kadre esantis taikinys. Tik daugiaspektriniai moduliai.

Visi keturi yra **įjungti pagal numatytuosius nustatymus**— vienas importuotas „LATTICE“ neapdorotas kadras vienu apdorojimo etapu išskirstomas į visus įjungtus ir taikytinus produktus. Žymės langelis**Eksportuoti atspindžio koeficientą** yra išblukęs, kai atspindžio kalibravimas yra išjungtas. Nustatymai, kurių įjungimas neįmanomas dėl viršesnio lygio jungiklio, visada yra išblukinti, o šalia jų rodomas įrankio patarimas, nurodantis, kurį jungiklį reikia pakeisti.

**Išplėstiniai nustatymai:**

* **Šviesos jutiklio laiko juostos poslinkis**: Valandos nuo UTC, skirtos šviesos jutiklio laiko suderinimui (pagal numatytuosius nustatymus: 0, diapazonas nuo −12 iki +12)
* **Taikyti PPK pataisas**: Naudoja GPS/ekspozicijos kontaktų duomenis iš `.daq` failų (numatyta: išjungta)
* **Ekspozicijos kontaktas 1/2**: Priskiria kameras ekspozicijos kontaktams dviejų kamerų konfigūracijose

{% hint style="info" %}
**„LATTICE“ įvesties lygis nustatomas automatiškai.** „LATTICE“ užfiksuoti kadrai savo apdorojimo lygį perneša XMP metaduomenyse, o apdorojimas visada prasideda nuo neapdoroto kadro – vartotojo sąsajoje (GUI) nereikia nieko konfigūruoti. (CLI žymė `--input-level` skirta pažengusiems vartotojams, norintiems perrašyti nustatymus, kai prarandami įrašų metaduomenys; žr. [CLI žinyną](../reference/cli-reference.md).)
{% endhint %}

### „Debayer“ metodas

Šiuo metu Chloros siūlome 2 „debayering“ metodus:

#### Standartinis (greitas, vidutinė kokybė)

Standartinis „debayer“ apdoroja greitai, tačiau rodo „debayering“ spalvų triukšmą, dėl ko vaizdai tampa mažiau tikslūs ir triukšmingesni.

#### Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė) \[Tik „Chloros+“]

Metodas „Atsižvelgiantis į tekstūrą“ naudoja aukštos kokybės, kraštus atpažįstantį debayeringą, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayeringo triukšmą. Šiam modeliui veikti reikalinga GPU atmintis (VRAM): turint **7 GB ar daugiau VRAM**, jis gali apdoroti kelis vaizdus vienu metu; turint mažiau nei 7 GB, jis apdoroja po vieną vaizdą (žymiai lėčiau). Žr. [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md).

{% hint style="info" %}
**„LATTICE“ užfiksuotuose vaizduose visada naudojamas standartinis demosaic.** „LATTICE“ nėra apmokyto „Texture Aware“ modelio, todėl ši parinktis nesiūloma „LATTICE“ vaizdams — toje pačioje projekto dalyje esantys Survey3 vaizdai vis dar gali ją naudoti.
{% endhint %}

### Indeksai (daugiaspektriniai indeksai)

Nustatykite, kokius augmenijos indeksus skaičiuoti ir eksportuoti. Grafinės vartotojo sąsajos išskleidžiamajame meniu siūloma **27 iš anksto apibrėžtų indeksų formulių**.**Kaip pridėti indeksus:**

1. Spustelėkite mygtuką**„Pridėti indeksą“**

2. Išskleidžiamajame meniu pasirinkite indeksą (NDVI, NDRE, GNDVI ir kt.)
3. Nustatykite vizualizavimo parametrus (LUT spalvas, verčių intervalus)
4. Prireikus pridėkite kelis indeksus

**Populiarūs indeksai:**

* **NDVI**: Bendra augmenijos būklė (dažniausiai naudojamas)
* **NDRE**: Ankstyvas streso nustatymas naudojant RedEdge
* **GNDVI**: jautrus chlorofilo koncentracijai
* **OSAVI**: gerai veikia, kai dirvožemis matomas
* **EVI**: regionai su dideliu lapų ploto indeksu (LAI)

**Pasirinktinės formulės:**

* Sukurkite pasirinktines multispektrinių indeksų formules, taikydami juostų skaičiavimus visiems vaizdo kanalams
* Išsaugokite pasirinktines formules, kad galėtumėte jas naudoti pakartotinai
* Pasirinktinės formulės yra „Chloros+“ funkcija; jų prieinamumas priklauso nuo jūsų plano lygio

Visus galimus indeksus ir formules — įskaitant tai, kurie pavadinimai yra tik grafinėje vartotojo sąsajoje (GUI), o kurie taip pat veikia CLI ir SDK — rasite [Daugiaspektrinių indeksų formulėse](../project-settings/multispectral-index-formulas.md).

### Eksportavimas

Nustato išvesties failo formatą.

**Galimi formatai**(nustatymas:**Kalibruoto vaizdo formatas**, numatytasis**TIFF (16 bitų)**):

* **TIFF (16 bitų)**: Rekomenduojamas GIS ir mokslinėms analizėms
* **TIFF (8 bitai)**: 8 bitų TIFF
* **TIFF (32 bitai, procentais)**: Skaičiai su slankiuoju kableliu
* **PNG (8 bitai)**: Suspaudimas be nuostolių, skirtas vizualizavimui
* **JPG (8 bitai)**: Mažiausi failai, suspaudimas su nuostoliais

Sąraše nurodyta, kokius vaizdus gali talpinti projekto vaizdai: Chloros nepadidina bitų gylio. Visiems 8 bitų vaizdams siūlomi tik TIFF (8 bitų), PNG (8 bitų) ir JPG (8 bitų) formatai, o numatytasis formatas yra TIFF (8 bitų). Mišriame projekte kiekvienas įrašas eksportuojamas su savo bitų gilumu arba mažesniu. „Radiance“ išlieka 32 bitų plaukiojančiojo kablelio.

Išvesties failai įrašomi projekto aplanke, sugrupuoti pagal kamerą ir formatą: `<project>/<camera>/<format>/<Product>_Images/`. „Radiance“ **visada** įrašomas kaip „float32“ į aplanką „`tiff32`“, nepriklausomai nuo šio nustatymo. Eksportuoti failai išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas. Eksportuojant „LATTICE“ multispektrinius duomenis, taip pat galima įrašyti pikselių ribojimo kaukes į aplanką „`clamp_masks/`“, esantį šalia produktų aplankų. Visą išvesties medį rasite skyriuje [Apdorojimo užbaigimas](finishing-the-processing.md).

{% hint style="warning" %}
**Atspindžio koeficientų reikšmių skaitymas**: DN reikšmė, reiškianti ρ = 1,0, priklauso nuo šaltinio kameros — „LATTICE“ naudoja 32768 (pažymėta kaip XMP `Chloros:PixelScale`), Survey3 naudoja 65535. Verčiau perskaitykite žymą, o ne laikykitės prielaidos, kad vertė yra pastovi. Žr. [Išvesties vaizdo formatai](../output-image-formats.md).
{% endhint %}

### DAQ šviesos jutiklis

Šiame skyriuje išvardyti visi jūsų projekte esantys DAQ žemyn nukreiptų spindulių failai (`.daq` / `.csv`), po vieną eilutę kiekvienam failui, nurodant jutiklio modelį, failo pavadinimą ir difuzoriaus **dangtelio** korekcija, taikoma tam failui.

* **Dangtelio perrašymas (visi failai)**: vienas išskleidžiamas meniu, galiojantis visam projektui.**„Auto“** (numatyta) naudoja kiekvieno failo užregistruotą ribą — jei nieko neužregistruota, daroma prielaida, kad buvo saulėta, tačiau „DAQ-U“, „DAQ-M“ ir „DAQ-E“ tiekiami be ribos, o Chloros negali matyti, kas buvo pritaikyta, todėl apdorojimo metu rodomas įspėjimas dėl numanomos ribos: pasirinkite ribą, kuri buvo pritaikyta. Pasirinkus ribą, ji pakeičia visus failus, išskyrus DAQ-E-S failus, kurių difuzorius yra kalibravimo dalis: neapdoroti įrašai koreguojami naudojant jį, o įrašai, kuriuose jau yra nustatyta riba, perkalibruojami (užregistruota korekcija atšaukiama, taikoma pasirinkta riba).
* Eilutėse rodomi įspėjimai, kai įrašytas dangtelis buvo laikomas numatytuoju, o ne operatoriaus patvirtintu, ir kai pasirinktam dangteliui nėra profilio tam įrenginio modeliui (to failo atveju perrašymas atmetamas).

„Šviesos jutikliai“ skirtuke atlikti DAQ įrašai automatiškai pridedami prie atidaryto projekto, o importuoti `.daq` / `.csv` failai čia pasirodo iškart po to, kai jie pridedami.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p>Apatiniai projekto nustatymai — indeksas, eksporto formatas, DAQ šviesos jutiklių skyrius ir projekto šablono / aplanko valdikliai</p></figcaption></figure>### Masyvo suderinimas

Šis skyrius rodomas **tik** tada, kai bent viename projekto vaizde yra modulio su moduliu suderinimo transformacija, kurią LATTICE masyvai pažymi fiksavimo metu (`Chloros:Alignment*` XMP). Čia rodoma, kiek vaizdų turi žymes ir kuri kamera yra etalonas, naudojant šiuos valdiklius:

* **Taikyti masyvo suderinimą** (numatyta: įjungta): kiekvieną apdorotą produktą (debayering / peržiūra / spinduliavimas / atspindys / indeksas) iškraipyti pagal bendrą masyvo etaloninę geometriją. Išjungta = eksportuoti pagal natūralią jutiklio geometriją.
* **Apkarpyti iki bendro persidengimo** (numatyta: įjungta): apkarpykite suderintus eksportus iki srities, kurią dalijasi visi moduliai, kad kiekviena juosta turėtų tą patį užimamą plotą. Išjungus išlaikomas visas jutiklio vaizdas (juodas užpildymas už šaltinio ribų).
* **Perimatuavimas**:**Bilinearinis (lygus, numatytasis)**,**Artimiausias (išsaugo tikslias vertes)**— be pikselių tarpusavio maišymo, skirta griežtai radiometrinei analizei — arba**Kubinis (aštriausias)**.

***

## Nustatymų išsaugojimas ir įkėlimas

### Projekto šablono išsaugojimas

Sukurkite pakartotinai naudojamus šablonus, kad darbo eiga būtų nuosekli:

1. Nustatykite visus norimus parametrus skydelyje „Projekto nustatymai“
2. Nuslinkite į apačioje esantį skyrių **„Išsaugoti projekto šabloną“**

3. Įveskite apibūdinantį šablono pavadinimą (pvz., „Survey3N\_RGN\_Agriculture“)
4. Spustelėkite išsaugojimo piktogramą

**Privalumai:**

* Taikykite identiškus nustatymus keliuose projektuose
* Dalinkitės konfigūracijomis su komandos nariais
* Užtikrinkite nuoseklumą kartojamuose tyrimuose

### Šablono įkėlimas į naują projektą

Kuriant naują projektą:

1. Pagrindiniame meniu pasirinkite **„Naujas projektas“**

2. Pasirinkite projekto šabloną pasirinktiniame šablonų pasirinkimo lange
3. Visi šablono nustatymai bus pritaikyti automatiškai

### Darbo katalogas

**„Darbo katalogas“** nustatymas nurodo, kur pagal numatytuosius nustatymus kuriamos naujos projektai:

* **Numatytasis katalogas**: `Documents\MAPIR\Chloros Projects` jūsų Windows aplanke „Dokumentai“ (paprastai `C:\Users\[Username]\Documents\MAPIR\Chloros Projects`; jis seka „Dokumentų“ aplanką, jei „OneDrive“ arba klajojantis profilis jį perkelia)
* **Pakeisti vietą**: spustelėkite redagavimo piktogramą ir pasirinkite naują aplanką
* **Bendrinama su CLI**: `chloros-cli` naudoja tą patį numatytąjį projekto aplanko nustatymą
* **Windows sistemoje naudokite trumpus kelius**: jei Windows ilgųjų kelių funkcija neįjungta, projekto aplankas, kurio pavadinimas ilgesnis nei maždaug 130 simbolių, prieš apdorojimą bus atmestas — žr. [Apdorojimo pradžia](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer)
* **Kada keisti**:
  * Tinklo diskas komandiniam bendradarbiavimui
  * Kitas diskas su didesne saugojimo talpa
  * Tvarkinga aplankų struktūra pagal metus / klientą

***

## PPK (Post-Processed Kinematic) nustatymas

Jei naudojate MAPIR DAQ įrašymo įrenginius su GPS tiksliai geografinei vietai nustatyti:

### Būtinos sąlygos

* MAPIR DAQ su GPS (GNSS) moduliu
* .daq žurnalo failas su ekspozicijos kontaktų įrašais
* Kamera, prijungta prie DAQ ekspozicijos kontaktų fiksavimo sesijos metu

### Konfigūravimo žingsniai

1. Įdėkite .daq žurnalo failą į savo projekto aplanką
2. Projekto nustatymuose pažymėkite langelį **„Taikyti PPK pataisas“**

3. Jei reikia, nustatykite**„Šviesos jutiklio laiko juostos nuokrypį“** (numatyta reikšmė: 0, atitinkanti UTC)
4. Priskirkite kameras ekspozicijos kontaktams:
   * **Viena kamera**: automatiškai priskiriama 1 kontaktui
   * **Dvi kameros**: kiekvieną kamerą rankiniu būdu priskirkite atitinkamam kontaktui

**Ekspozicijos kontaktų priskyrimas:**

* **Ekspozicijos kontaktas 1**: išskleidžiamajame meniu pasirinkite kameros modelį
* **Ekspozicijos kontaktas 2**: pasirinkite antrą kamerą arba „Nenaudoti“
* Viena ir ta pati kamera negali būti priskirta abiem kontaktams

{% hint style="warning" %}
**Svarbu**: Ekspozicijos kontaktiniai kaiščiai turi būti teisingai priskirti atitinkamoms kameroms. Neteisingas priskyrimas lems klaidingus geografinės vietos duomenis.
{% endhint %}

***

## Išplėstiniai scenarijai

### Daugiakameriniai projektai

Apdorojant vaizdus iš kelių MAPIR kamerų viename projekte:

1. Chloros automatiškai atpažįsta kiekvieną kameros modelį (Survey3 ir „LATTICE“)
2. Kiekvienai kamerai priskiriami atitinkami apdorojimo profiliai, o kiekvienai kamerai sukuriama atskira išvesties aplankų struktūra
3. PPK: Rankiniu būdu priskirkite kiekvieną Survey3 kamerą teisingam ekspozicijos kontaktui
4. Visos kameros naudoja tą patį eksporto formatą ir indeksus

**Pavyzdžiai**: Survey3W RGN + Survey3N OCN dviejų kamerų įranga arba „LATTICE“ matrica, kurioje RGB pagrindinė kamera derinama su siaurajuosčio spektro moduliais

### Laiko tarpo arba kelių datų tyrimai

Atliekant pakartotinius to paties ploto tyrimus per tam tikrą laikotarpį:

1. Sukurkite šabloną su standartiniais nustatymais
2. Kiekvieną sesiją naudokite nuoseklią kalibravimo taškų konfigūraciją
3. Kiekvieną datą apdorokite kaip atskirą projektą
4. Naudokite identiškus nustatymus, kad gautumėte palyginamus rezultatus
5. Eksportuokite tuo pačiu formatu, kad būtų galima atlikti laiko analizę

### Didelės duomenų rinkmenų apimtys

Projektams su daugybe vaizdų (500+):

* Apsvarstykite galimybę suskirstyti į mažesnius projektus pagal datą arba teritoriją
* Naudokite „Chloros+“ lygiagretų apdorojimą, kad gautumėte greitesnius rezultatus
* Apsvarstykite galimybę naudoti CLI arba API, siekiant automatizuoti paketinį apdorojimą
* Nustatykite minimalų pakartotinio kalibravimo intervalą, kad sutrumpintumėte taikinio aptikimo laiką

***

## Nustatymų patikrinimas

Prieš pradėdami apdorojimą, peržiūrėkite šiuos pagrindinius nustatymus:

* [ ] Kameros modelis teisingai atpažintas failų naršyklėje
* [ ] Įjungta vinjetės korekcija
* [ ] Įjungtas atspindžio kalibravimas
* [ ] Survey3 atveju: importuotas ir patikrintas bent vienas kalibravimo taikinio vaizdas; „LATTICE“ atveju: yra taikinys ir (arba) „`.daq`“ žemyn nukreiptas įrašas
* [ ] Pridėti norimi multispektriniai indeksai
* [ ] Eksporto formatas, tinkamas jūsų darbo eigai
* [ ] Nustatyti PPK parametrai (jei naudojate .daq failą su ekspozicijos įvykiais)

***

## Tolimesni veiksmai

Kai nustatymas bus atliktas:

1. **Pažymėkite kalibravimo taikinio vaizdus** – žr. [Taikinio vaizdų pasirinkimas](choosing-target-images.md)
2. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)
3. **Stebėkite apdorojimo eigą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)

Išsamią informaciją apie visus galimus nustatymus rasite [Projekto nustatymai](../project-settings/project-settings.md) žinyno dokumentacijoje.
