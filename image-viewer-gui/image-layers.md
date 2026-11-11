# Vaizdo sluoksniai

**Sluoksnių išskleidžiamasis meniu**, esantis vaizdo peržiūros lango dešinėje viršutinėje dalyje, leidžia perjungti visas peržiūrimą vaizdą sudarančias versijas – nuo pirminio užfiksuoto vaizdo iki kiekvieno apdoroto produkto ir apskaičiuotų indeksinių vaizdų – neišeinant iš peržiūros lango.

## Kas yra vaizdo sluoksniai?

Chloros sistemoje „sluoksnis“ yra vienas **produkto failas**, susietas su vienu šaltinio vaizdu. Importuojant gaunami šaltinio failai; apdorojimo metu kiekvienam apdorojimo ciklo metu sukurtam produktui pridedamas atskiras sluoksnis. Eksportuoti failai išlaiko šaltinio failo pavadinimą — produktą identifikuoja**aplankas**, o sluoksnio pavadinimas yra „Chloros “ suteikta to aplanko etiketė.

<!-- SCREENSHOT-NEEDED: Image Viewer full screen with the layer dropdown open on a processed LATTICE multispectral image, showing the full list: TIFF base, RAW (Original), RAW (Debayered), RAW (Preview), RAW (Radiance), RAW (Reflectance), and one RAW (NDVI Index) entry. -->

***

## Sluoksnių sąrašas

### Visada rodomi

| Sluoksnis | Kas tai yra |
| --- | --- |
| **JPG**(arba**PNG**/**TIFF**) | Pagrindinis failas, gautas kartu su užfiksuotu vaizdu. Survey3 importuoja „`.JPG`“ šalia kiekvieno „`.RAW`“; „LATTICE“ užfiksuoti vaizdai pateikia PNG arba TIFF peržiūros pavyzdį. Pažymėtas pagal tai, kas iš tikrųjų buvo importuota |
| **RAW (Originalus)** | Šaltinis – neapdorotas kadras, išskaidytas spalvų atkūrimui, be jokių korekcijų. Prieinamas nuo importavimo momento – nereikia jokio apdorojimo |

„LATTICE“ užfiksuotas vaizdas, kurio bazinis failas **yra** jo neapdorotas kadras, neturi atskiro bazinio įrašo: jį jau apima „`RAW (Original)`“.

### Survey3 apdorojimo produktai

| Sluoksnis | Įrašomas į | Egzistuoja, kai |
| --- | --- | --- |
| **RAW (Tikslas)** | — | Nustatyta, kad kadre yra kalibravimo tikslas |
| **RAW (Atspindžio koeficientas)** | `Reflectance_Calibrated_Images/` | Šiame kadre sėkmingai atliktas atspindžio koeficiento kalibravimas |
| **Ištaisyta vinjetė**| `Vignette_Corrected_Images/` | Kadrą nebuvo galima kalibruoti pagal atspindžio koeficientą**ir** buvo įjungta *vinjetės korekcija* |
| **Jutiklio atsakas**| `Sensor_Response_Images/` | Kadrą nebuvo galima kalibruoti pagal atspindžio koeficientą**ir** *vigneto korekcija* buvo išjungta |
| **Baltojo balanso nustatymas** | `White_Balanced_Images/` | Buvo sukurtas produktas su nustatytu baltuoju balansu |

{% hint style="info" %}
**Vignette korekcija ir jutiklio jautrumas yra alternatyvos, niekada nenaudojamos kartu.** Kiekvienam kameros modeliui per vieną apdorojimo ciklą sukuriamas tik vienas nekalibruotas atsarginis produktas, o *Vignette korekcijos* jungiklis pasirenka, kuris iš jų bus naudojamas. Žr. [Projekto nustatymus](../project-settings/project-settings.md).
{% endhint %}

### LATTICE lygiai

LATTICE vienu apdorojimo etapu išskirsto duomenis į šiuos lygius. Kurie iš jų egzistuoja, priklauso nuo projekto nustatymuose nurodytų eksporto perjungiklių kiekvienam produktui ir nuo to, kas taikoma konkrečiai kamerai.

| Sluoksnis | Įrašoma į | Taikoma |
| --- | --- | --- |
| **RAW (be bayerio)** | `Debayered_Images/` | RGB ir multispektrinis |
| **RAW (peržiūra)** | `Preview_Images/` | Multispektrinė (netikrų spalvų išplėtimas) |
| **Baltojo balanso** | `Preview_Images/` | RGB pagrindinės kameros — RGB peržiūra registruojama šiuo pavadinimu, kad atitiktų to paties pavadinimo Survey3 sluoksnį |
| **RAW (spinduliavimas)** | `Radiance_Images/` | Tik daugiaspektrinis |
| **RAW (atspindžio koeficientas)** | `Reflectance_Calibrated_Images/` | Tik daugiaspektrinė, ir tik tada, kai atitinkamas „`.daq`“ žemyn nukreiptas įrašas arba kokybės patikrinimą išlaikęs kadro viduje esantis taikinys užima visą kadrą |

RGB-pagrindinės kameros neturi radiometrijos pagal juostas, todėl spinduliavimas ir atspindys joms praleidžiami kaip **netaikytini** — tai nurodoma žurnale, o ne tyliai ignoruojama.

{% hint style="info" %}
**Neapdoroti ir debayeringo apdoroti sluoksniai suderinami su produktais (nauja funkcija versijoje 1.3.0).**LATTICE neapdoroti ir debayeringo apdoroti failai išsaugomi pagal jutiklio natūralią orientaciją, o XMP įraše nurodoma jiems priklausanti orientacija; kiti produktai išsaugomi jau pritaikius tą orientaciją. Peržiūros programa ir vaizdų tinklelis, rodydami vaizdus, perjungia**RAW (Original)**ir**RAW (Debayered)** pagal tą įrašą, todėl kadras nebeapsiverčia, kai perjungiate tarp produkto sluoksnio ir neapdoroto sluoksnio. Tai taikoma tik rodymui — failai niekada nėra perrašomi. Failai be orientacijos įrašo (užfiksuoti iki 1.3.0 versijos arba sukurti kita programine įranga) rodomi taip, kaip buvo išsaugoti. Žr. [Išvesties vaizdo formatai](../output-image-formats.md#orientation).
{% endhint %}

Fiksavimo kaukės (`*_clampmask.tif`, esančios `clamp_masks/` aplanke) nėra sluoksniai: Chloros jas praleidžia importuojant. Žr. [Išvesties vaizdo formatai](../output-image-formats.md#clamp-masks).

### Indekso, LUT ir „sandbox“ sluoksniai

| Sluoksnio tipas | Pavyzdys | Iš kur gaunamas |
| --- | --- | --- |
| **RAW (`<INDEX>` indeksas)** | `RAW (NDVI Index)` | Po vieną kiekvienam „Project Settings“ nustatytam indeksui, apskaičiuojamas apdorojimo metu |
| **`<INDEX>` LUT** | `NDVI LUT` | Indekso versija su spalvų atvaizdavimu |
| **Bandymų (`<Name>` `<Index\|LUT>` `<NNN>`)** | `Sandbox (NDVI LUT 003)` | Po vieną už kiekvieną [Indekso/LUT „Sandbox“](index-lut-sandbox.md) eksporto ciklo |

Jei tas pats indekso pavadinimas sukonfigūruotas daugiau nei vieną kartą su skirtingais nustatymais, antrasis ir vėlesnieji pavadinimuose gauna numerį (`RAW (NDVI2 Index)`), kad sluoksnius būtų galima atskirti.

***

## Sluoksnių pasirinkimo meniu naudojimas

1. Atidarykite vaizdą visame ekrane, spustelėdami miniatiūrą tinklelyje
2. Spustelėkite **sluoksnių išskleidžiamąjį meniu** peržiūros lango dešinėje viršutinėje dalyje
3. Pasirinkite sluoksnį — vaizdas atsinaujina iš karto

Išskleidžiamajame meniu pirmiausia rodomi **JPG, RAW (Original), RAW (Target), RAW (Reflectance)**, būtent tokia tvarka, o po jų išvardijami visi kiti sluoksniai pagal produktų registravimo tvarką.

### Sluoksnių prioritetas naršant

Paspausdami **←**/**→** pereisite prie kito vaizdo ir sistema stengsis išlaikyti tą patį sluoksnį:

1. **Pirmiausia tiksli atitiktis** — jei kitas vaizdas turi to paties pavadinimo sluoksnį, jis bus parinktas. Būtent tai leidžia jums likti sluoksnyje „`RAW (NDVI Index)`“, peržiūrint visą rinkinį
2. **Tada atitikimas pagal tipą** — indeksinis sluoksnis ieško bet kurio indeksinio sluoksnio, LUT — bet kurio LUT, atspindžio — atspindžio, tikslinio — tikslinio, originalaus — originalaus, bazinio — bazinio
3. **Tada, tik eksporto sluoksniams** — pavadinimas išsaugomas net jei sluoksnių sąrašas dar nespėjo atsinaujinti, nes failas jau egzistuoja diske. Būtent tai leidžia peržiūrėti produktus, kol vykdymo procesas juos dar rašo
4. **Kitais atvejais** — pirmasis pasiekiamas sluoksnis, kuris paprastai yra bazinis vaizdas

Projekte esantys „`.daq`“ ir „`.csv`“ „sidecar“ failai praleidžiami naršant rodyklių klavišais, todėl peržiūrint vaizdus niekada nepatenkama į šviesos jutiklio įrašą.

Padidinimas ir perėjimas taip pat taikomi visose nuotraukose, todėl to paties lauko pozicijos palyginimas „prieš“ ir „po“ tampa paprastas.

***

## Pikselių verčių supratimas pagal sluoksnį

[Kursoriaus verčių skydelyje](opening-an-image-full-screen.md#cursor-values) rodomos tikrosios kiekvieno kanalo vertės po jūsų kursoriumi, to sluoksnio saugojimo vienetais. Jo stulpeliai keičiasi priklausomai nuo sluoksnio:

| Sluoksnis | Rodomas vienetas | Pastabos |
| --- | --- | --- |
| Bazinis (JPG / PNG / TIFF peržiūra) | DN, 0–255 | Ekrano reikšmės, koreguotos pagal RGB gama. Tik vizualiam patikrinimui |
| RAW (originalas) | DN | Neapdoroti skaitmeniniai jutiklio duomenys. Histogramos ašis nurodo gylį: 255 (8 bitai), 4095 (12 bitai) arba 65535 (16 bitai) |
| RAW (be bayerio) | DN | Linijinis, be ekrano ištempimo |
| RAW (peržiūra) / su baltos spalvos balansu | DN | Ekrano vaizdas — ištemptas arba su gama korekcija. Neskirtas matavimui |
| RAW (spinduliavimas) | **W/m²/sr/nm** | „Float32“ fizinis spinduliavimas. Nėra DN stulpelio |
| RAW (atspindys) | DN **ir %** | Procentas apskaičiuotas pagal to failo skalę — žr. žemiau |
| Indekso / LUT / „sandbox“ eksportai | Indekso vertė arba RGB komponentai | Vienkanalis indeksinis failas pateikia indekso vertę; spalvų atvaizdavimo LUT failas pateikia Red /Green /Blue komponentus |

### Atspindžio koeficientas: skalė nustatoma kiekvienam failui atskirai

{% hint style="warning" %}
**„Padalinti iš 65 535“ teisinga tik Survey3 atveju.** „LATTICE“ atspindžio koeficientas saugomas kitokiu masteliu, o šių dviejų daliklių sumaišymas yra dažniausias būdas gauti atspindžio koeficiento vertes, kurios yra lygiai pusė to, kokios turėtų būti.
{% endhint %}

| Šaltinis | DN, lygus atspindžio koeficientui 1,0 | Identifikuojamas pagal |
| --- | --- | --- |
| **LATTICE**(M3C / M3M) |**32768** | XMP žymė „`Chloros:PixelScale=32768`“, įrašyta į kiekvieną eksportuojamą „LATTICE“ atspindžio duomenų rinkinį. Dvigubas rezervas reiškia, kad ρ, didesnis nei 1,0, gali būti atvaizduojamas, o ne apkarpomas |
| **Survey3**| **65535** | Nėra Chloros XMP mastelio žymės — Survey3 kalibravimas įrašo ρ × dtype-max ir apkarpo vertę ties 1,0 |

GIS ir skriptams: iš failo nuskaitykite `Chloros:PixelScale` ir padalinkite iš jo. Jei žymės nėra, failas yra Survey3 masto (65535). Peržiūros programa, indekso/LUT bandymo aplinka ir indekso eksportas visos nustato mastelį tuo pačiu būdu, todėl skaičius, kurį matote prie žymeklio, yra tas pats skaičius, kurį naudojo indekso skaičiavimai.

Be to, atsižvelgiant į formatą, duomenys saugomi taip:

* **TIFF (32 bitai, procentais)** saugo DN / 65535 kaip plūduriuojančiojo kablelio skaičių
* **PNG (8 bitai)**ir**„JPG“ (8 bitai)** saugo DN × 255 / 65535
* **8 bitų DN, gautas iš 8 bitų įrašo**, arba atspindžio failas, sukurtas iki versijos 1.3.0, apribojamas iki 0–255 ir neturi mastelio žymės. Šiame skydelyje tiems failams rodomas tik DN, be procentų stulpelio

### Indekso verčių intervalai

| Indeksų grupė | Tipinis intervalas | Rodmenys |
| --- | --- | --- |
| Normalizuotas skirtumas (NDVI, GNDVI, NDRE, ENDVI…) | nuo −1 iki +1 | Sveika augmenija paprastai 0,4–0,9; plikas dirvožemis – artimas 0; vanduo – neigiamas |
| Prisitaikytas prie dirvožemio (SAVI, OSAVI, MSAVI2…) | maždaug nuo −1 iki +1,5 | Rodmenys panašūs į NDVI, bet slopinamas dirvožemio fonas |
| Santykis (GRVI, GCI, MSR, CIRE…) | neribotas virš | Santykiai auga be ribų, kai vardiklio juosta artėja prie nulio |
| EVI / LAI | nuo 0 iki ~1, nuo 0 iki ~3,5 | Debesys ir kiti prisotinti pikseliai išstumia abu rodiklius už ribų — pirmiausia juos užmaskuokite |

Tikslias kiekvieno iš anksto nustatyto rodiklio skaičiavimo formules rasite [Daugiaspektrinių indeksų formulėse](../project-settings/multispectral-index-formulas.md).

***

## Įprasti darbo srautai

### Palyginimas „prieš“ ir „po“

1. Pasirinkite **RAW (Original)** ir atkreipkite dėmesį į vinjetavimą bei nekalibruotas reikšmes
2. Perjunkite į **RAW (atspindžio)**

3. Palyginkite — vinjetavimas pašalintas, reikšmės kalibruotos. Padidinimas ir peržiūra išlieka, todėl matote tą patį plotą

### Peržiūrėkite vieną indeksą visame rinkinyje

1. Atidarykite pirmąjį apdorotą vaizdą ir pasirinkite indekso sluoksnį
2. Pakartotinai spauskite **→** — indekso sluoksnis seka paskui jus nuo vaizdo prie vaizdo
3. Peržiūrėdami stebėkite šoninės juostos histogramą: kadrą, kurio pasiskirstymas staigiai pakinta, verta apžiūrėti atidžiau

### Kalibravimo taškų patikrinimas

1. Pasirinkite **RAW (Tikslas)** tikslo kadre
2. Patikrinkite, ar tikslas aiškiai matomas ir aptiktas
3. Pereikite prie kito tikslo kadro — tikslo sluoksnis seka paskui

### Patikrinkite atspindžio verčių tikslumą

1. Pasirinkite **RAW (Atspindys)**

2. Perskaitykite stulpelį**%** skydelyje „Cursor Values“ (Kursoriaus reikšmės) — jis jau yra tinkamai pritaikytas tam failui
3. Patikrinkite, ar reikšmės atitinka žinomų medžiagų savybes kadre: sveika augmenija pasižymi aukštais NIR ir žemais raudonos spalvos atspindžio rodikliais; kalibravimo taško atspindžio rodiklis turėtų būti artimas paskelbtam

***

## Problemų sprendimas

### Lauke, kurį tikėjausi rasti, nėra reikiamo sluoksnio

**Galimos priežastys**

* Vaizdas nebuvo apdorotas — yra tik bazinis ir `RAW (Original)` sluoksniai
* Projekto nustatymuose nepažymėtas produkto eksporto jungiklis
* Produktas netaikomas tai kamerai (spinduliavimas ir atspindžio koeficientas RGB pagrindiniame kadre; bet koks indeksas vienos juostos M3M monokamerai)
* Atspindžio kalibravimui nebuvo su kuo dirbti — trūksta `.daq` žemyn nukreipto spinduliavimo aprėpties ir kokybės užtikrinimo reikalavimus atitinkančio taikinio kadre — todėl kadras grįžo prie „Vignette Corrected“ arba „Sensor Response“

**Ką daryti**

1. Patikrinkite vykdymo žurnalą: Chloros nurodo, kada nebuvo įmanoma eksportuoti prašomo produkto ir kodėl
2. Patikrinkite kiekvieno produkto eksporto parinktis [Projekto nustatymuose](../project-settings/project-settings.md)
3. Patikrinkite, ar produkto aplankas yra projekto išvesties medžio struktūroje
4. Pakartotinai apdorokite, įjungus šį produktą

### Sluoksnių sąrašas atrodo pasenęs

Chloros pakartotinai nuskaito projekto produktų aplankus vykstant apdorojimui ir ištaiso trūkstamus sluoksnių registravimus remdamasis tuo, kas iš tikrųjų yra diske, todėl sluoksnis, kurio eksportavimas baigtas, paprastai pats atsiranda apklausos metu. Perėjimas nuo vaizdo į kitą langą ir atgal priverčia atlikti naują apdorojimą.

### Atspindžio vertės atrodo dvigubai mažesnės nei turėtų būti

Beveik neabejotinai dalinate LATTICE failą iš 65535. Naudokite `Chloros:PixelScale` (32768) arba peržiūrėkite stulpelį **%**, kuriame šis koeficientas jau pritaikytas.

### Indekso sluoksnis yra, bet vaizdas tuščias

Indeksui reikalingos juostos, kurių jūsų sluoksnyje nėra — pavyzdžiui, indeksas, skaitantis trečiąjį kanalą, pritaikytas vieno ar dviejų kanalų failui. Perjunkite į daugiakanalį sluoksnį (atspindžio arba be „bayeringo“) arba pasirinkite indeksą, atitinkantį kameros filtrą.

***

## Tolimesni veiksmai

* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) — žymeklio rodmenys, histograma ir GSD valdymas
* [**Indeksų/LUT bandymų aplinka**](index-lut-sandbox.md) — interaktyvus indeksų vizualizavimas ir eksportavimas
* [**Daugiaspektrinių indeksų formulės**](../project-settings/multispectral-index-formulas.md) — indeksų žinynas
* [**Apdorojimo užbaigimas**](../processing-images-gui/finishing-the-processing.md) — išvesties aplankų medis, į kurį nukreipia šie sluoksniai
