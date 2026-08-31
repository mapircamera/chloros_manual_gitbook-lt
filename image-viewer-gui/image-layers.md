# Vaizdo sluoksniai

**Sluoksnių išskleidžiamasis meniu**, esantis vaizdo peržiūros programos dešiniame viršutiniame kampe, leidžia perjungti visas peržiūrimos nuotraukos versijas – nuo pirminio kadro iki kiekvieno apdoroto produkto ir apskaičiuotų indeksinių vaizdų – neišeinant iš peržiūros programos.

## Kas yra vaizdo sluoksniai?

„Sluoksnis“ programoje „Chloros“ yra vienas **produkto failas**, susietas su vienu šaltinio vaizdu. Importuojant gaunami šaltinio failai; apdorojimo metu kiekvienam proceso metu sukurtam produktui pridedamas atskiras sluoksnis. Eksportuoti failai išlaiko šaltinio failo pavadinimą — būtent**aplankas** identifikuoja produktą, o sluoksnio pavadinimas yra Chloros žymė tam aplankui.

<!-- SCREENSHOT-NEEDED: Image Viewer full screen with the layer dropdown open on a processed LATTICE multispectral image, showing the full list: TIFF base, RAW (Original), RAW (Debayered), RAW (Preview), RAW (Radiance), RAW (Reflectance), and one RAW (NDVI Index) entry. -->

***

## Sluoksnių sąrašas

### Visada esantys

| Sluoksnis | Kas tai yra |
| --- | --- |
| **JPG**(arba**PNG**/**TIFF**) | Pagrindinis failas, gautas kartu su įrašymu. „Survey3“ importuoja „`.JPG`“ šalia kiekvieno „`.RAW`“; „LATTICE“ įrašai pateikia „PNG“ arba „TIFF“ peržiūros vaizdą. Pažymėtas pagal tai, kas iš tikrųjų buvo importuota |
| **RAW (Originalus)** | Šaltinis – neapdorotas kadras, kuriam pašalintas „bayeringas“ rodymui, netaikant jokių korekcijų. Prieinamas nuo importavimo momento — nereikia jokio apdorojimo |

„LATTICE“ užfiksuotas vaizdas, kurio bazinis failas **yra** neapdorotas kadras, neturi atskiro bazinio įrašo: jį jau apima `RAW (Original)`.

### Survey3 apdorojimo produktai

| Sluoksnis | Įrašyta į | Egzistuoja, kai |
| --- | --- | --- |
| **RAW (Tikslas)** | — | Nustatyta, kad kadre yra kalibravimo tikslas |
| **RAW (Atspindžio koeficientas)** | `Reflectance_Calibrated_Images/` | Šiame kadre sėkmingai atliktas atspindžio koeficiento kalibravimas |
| **Ištaisyta vinjetė**| `Vignette_Corrected_Images/` | Kadrą nebuvo galima kalibruoti pagal atspindį**ir** buvo įjungta *vinjetės korekcija* |
| **Jutiklio atsakas**| `Sensor_Response_Images/` | Kadrą nepavyko kalibruoti pagal atspindžio koeficientą**ir** *vigneto korekcija* buvo išjungta |
| **Baltojo balanso nustatymas** | `White_Balanced_Images/` | Buvo sukurtas produktas su nustatytu baltuoju balansu |

{% hint style="info" %}
**Vignette korekcija ir jutiklio jautrumas yra alternatyvos, niekada nenaudokite abiejų kartu.** Kiekvienam kameros modeliui per vieną apdorojimo ciklą sukuriamas tik vienas nekalibruotas atsarginis produktas, o *Vignette korekcijos* jungiklis pasirenka, kuris iš jų bus naudojamas. Žr. [Projekto nustatymus](../project-settings/project-settings.md).
{% endhint %}

### LATTICE lygiai

LATTICE vienu apdorojimo etapu išskirsto duomenis į šiuos lygius. Kurie iš jų egzistuoja, priklauso nuo projekto nustatymuose nurodytų eksporto perjungiklių kiekvienam produktui ir nuo to, kas taikoma konkrečiai kamerai.

| Sluoksnis | Įrašoma į | Taikoma |
| --- | --- | --- |
| **RAW (Debayered)** | `Debayered_Images/` | RGB ir multispektrinė |
| **RAW (peržiūra)** | `Preview_Images/` | Daugiaspektriniai (netikrų spalvų išplėtimas) |
| **Baltojo balanso** | `Preview_Images/` | RGB pagrindinės kameros — RGB peržiūra užregistruota šiuo pavadinimu, kad sutaptų su to paties pavadinimo Survey3 sluoksniu |
| **RAW (spinduliavimas)** | `Radiance_Images/` | Tik daugiaspektrinė |
| **RAW (atspindys)** | `Reflectance_Calibrated_Images/` | Tik multispektrinė, ir tik tada, kai atitinkamas `.daq` žemyn nukreiptas įrašas arba kokybės patikrinimą išlaikęs kadre esantis taikinys užima kadrą |

RGB pagrindinės kameros neturi radiometrijos pagal juostas, todėl spinduliavimo ir atspindžio duomenys jų atveju praleidžiami kaip **netaikytini** — apie tai nurodoma žurnale, o ne tyliai praleidžiama.

### Indekso, LUT ir „sandbox“ sluoksniai

| Sluoksnio modelis | Pavyzdys | Iš kur jis paimtas |
| --- | --- | --- |
| **RAW (`<INDEX>` indeksas)** | `RAW (NDVI Index)` | Po vieną kiekvienam indekso, sukonfigūruotam projekto nustatymuose, apskaičiuojamas apdorojimo metu |
| **`<INDEX>` LUT** | `NDVI LUT` | Indekso versija su pritaikytomis spalvomis |
| **Bandymų aplinka (`<Name>` `<Index\|LUT>` `<NNN>`)** | `Sandbox (NDVI LUT 003)` | Po vieną už kiekvieną [indekso/LUT „Sandbox“](index-lut-sandbox.md) eksporto ciklą |

Jei tas pats indekso pavadinimas sukonfigūruotas daugiau nei vieną kartą su skirtingais nustatymais, antrajam ir vėlesniems pavadinimuose pridedamas skaičius (`RAW (NDVI2 Index)`), kad sluoksnius būtų galima atskirti.

***

## Sluoksnių pasirinkimo funkcijos naudojimas

1. Atidarykite vaizdą visame ekrane, spustelėdami miniatiūrą tinklelyje
2. Spustelėkite **sluoksnių išskleidžiamąjį meniu** peržiūros lango dešinėje viršutinėje dalyje
3. Pasirinkite sluoksnį — vaizdas atsinaujina iš karto

Išskleidžiamajame meniu pirmiausia pateikiami **JPG, RAW (Original), RAW (Target), RAW (Reflectance)**, būtent tokia tvarka, o po jų išvardijami visi kiti sluoksniai pagal produktų registravimo tvarką.

### Sluoksnių prioritetas naršant

Paspausdami **←**/**→** pereisite prie kito vaizdo ir sistema stengsis išlaikyti tą patį sluoksnį:

1. **Pirmiausia tiksli atitiktis** — jei kitas vaizdas turi to paties pavadinimo sluoksnį, jis bus parinktas. Būtent tai leidžia jums likti sluoksnyje „`RAW (NDVI Index)`“, kol peržiūrite visą rinkinį
2. **Tada atitikimas pagal tipą** — indeksinis sluoksnis ieško bet kurio indeksinio sluoksnio, LUT — bet kurio LUT, atspindžio — atspindžio, tikslinio — tikslinio, originalo — originalo, bazinio — bazinio
3. **Tada, tik eksporto sluoksnių atveju** — pavadinimas išsaugomas net jei sluoksnių sąrašas dar nepasivijo, nes failas jau egzistuoja diske. Būtent tai leidžia peržiūrėti produktus, kol vykdymo procesas juos dar rašo
4. **Kitais atvejais** — pirmasis pasiekiamas sluoksnis, kuris paprastai yra bazinis vaizdas

Projekto „`.daq`“ ir „`.csv`“ papildomi failai praleidžiami naršant rodyklių klavišais, todėl peržiūrint vaizdus niekada nepatenkama į šviesos jutiklio įrašą.

Padidinimas ir perėjimas taip pat taikomi visoms nuotraukoms, todėl tų pačių lauko vietų palyginimas prieš ir po tampa paprastas.

***

## Pikselių verčių supratimas pagal sluoksnius

[Kursoriaus verčių skydelyje](opening-an-image-full-screen.md#cursor-values) rodomos tikrosios kiekvieno kanalo vertės po jūsų kursoriumi, to sluoksnio saugojimo vienetais. Jo stulpeliai keičiasi priklausomai nuo sluoksnio:

| Sluoksnis | Rodomas vienetas | Pastabos |
| --- | --- | --- |
| „Base“ (JPG / PNG / TIFF peržiūra) | DN, 0–255 | Rodomos reikšmės, koreguotos pagal gama RGB. Tik vizualiam patikrinimui |
| RAW (Originalus) | DN | Neapdoroti jutiklio skaitmeniniai duomenys. Histogramos ašis nurodo gylį: 255 (8 bitai), 4095 (12 bitai) arba 65535 (16 bitai) |
| RAW (Debayered) | DN | Linijinis, be ekrano ištempimo |
| RAW (Peržiūra) / Balansuota balta | DN | Rodomas rezultatas — ištemptas arba su koreguota gama. Neskirtas matavimui |
| RAW (Spinduliavimas) | **W/m²/sr/nm** | „Float32“ fizinis spinduliavimas. Nėra DN stulpelio |
| RAW (atspindys) | DN **ir %** | Procentinė vertė apskaičiuota pagal to failo skalę — žr. toliau |
| Indekso / LUT / „sandbox“ eksportai | Indekso vertė arba RGB komponentai | Vienkanalis indeksinis failas pateikia indekso vertę; spalvų atvaizdavimo LUT faile pateikiami Red/Green/Blue komponentai |

### Atspindžio koeficientas: skalė nurodyta kiekvienam failui atskirai

{% hint style="warning" %}
**„Padalinti iš 65 535“ teisinga tik Survey3 atveju.** LATTICE atspindžio koeficientas saugomas kitokiu masteliu, o šių dviejų daliklių sumaišymas yra dažniausias būdas gauti atspindžio koeficiento vertes, kurios yra lygiai perpus mažesnės nei turėtų būti.
{% endhint %}

| Šaltinis | DN, kurio vertė lygi atspindžio koeficientui 1,0 | Identifikuojamas pagal |
| --- | --- | --- |
| **LATTICE**(M3C / M3M) |**32768** | XMP žymė „`Chloros:PixelScale=32768`“, įrašyta į kiekvieną eksportuotą LATTICE atspindžio duomenų rinkinį. 2× atsarga reiškia, kad ρ, didesnis nei 1,0, gali būti atvaizduojamas, o ne apkarpomas |
| **Survey3**|**65535** | Jei nėra XMP masto žymės „Chloros“, kalibravimas „Survey3“ įrašo ρ × dtype-max ir apriboja vertę iki 1,0 |

GIS ir skriptų kūrimui: iš failo nuskaitykite `Chloros:PixelScale` ir padalinkite iš jo. Jei žymės nėra, failas yra Survey3 masto (65535). Peržiūros programa, indekso/LUT bandymų aplinka ir indekso eksportas mastelį apskaičiuoja vienodu būdu, todėl skaičius, kurį matote žymeklio vietoje, yra tas pats skaičius, kurį naudojo indekso skaičiavimai.

Be to, atsižvelgiant į formatą, duomenys saugomi taip:

* **TIFF (32 bitai, procentais)** saugo DN / 65535 kaip plūduriuojančiojo kablelio skaičių
* **PNG (8 bitai)**ir**JPG (8 bitai)** saugo DN × 255 / 65535
* **8 bitų TIFF eksportas iš 8 bitų šaltinio įrašo** yra apribojamas iki 0–255, o ne perskaičiuojamas, ir sąmoningai neturi mastelio žymės. Šiame skydelyje tik tiems failams rodomas DN, be procentų stulpelio

### Indekso verčių intervalai

| Indekso grupė | Tipinis intervalas | Vertė |
| --- | --- | --- |
| Normalizuotas skirtumas (NDVI, GNDVI, NDRE, ENDVI…) | nuo −1 iki +1 | Sveika augmenija paprastai 0,4–0,9; plikas dirvožemis – apie 0; vanduo – neigiamas |
| Prisitaikęs prie dirvožemio (SAVI, OSAVI, MSAVI2…) | apytikriai nuo −1 iki +1,5 | Panašus rodmuo kaip NDVI, bet pašalintas dirvožemio fonas |
| Santykis (GRVI, GCI, MSR, CIRE…) | neribotas viršuje | Santykiai auga be ribų, kai vardiklio juosta artėja prie nulio |
| EVI / LAI | nuo 0 iki ~1, nuo 0 iki ~3,5 | Debesys ir kiti prisotinti pikseliai išstumia abu rodiklius už ribų — pirmiausia juos užmaskuokite |

Tikslias kiekvieno iš anksto nustatyto parametrų derinio formules rasite [Daugiaspektrinių indeksų formulėse](../project-settings/multispectral-index-formulas.md).

***

## Įprasti darbo srautai

### Palyginimas prieš ir po

1. Pasirinkite **RAW (Original)** ir atkreipkite dėmesį į vinjetavimą bei nekalibruotas reikšmes
2. Perjunkite į **RAW (Reflectance)**

3. Palyginkite — vinjetavimas pašalintas, reikšmės kalibruotos. Padidinimas ir peržiūra išlieka, todėl matote tą patį plotą

### Vieno indekso peržiūra visame rinkinyje

1. Atidarykite pirmąjį apdorotą vaizdą ir pasirinkite indekso sluoksnį
2. Pakartotinai spauskite **→** — indekso sluoksnis seka paskui jus nuo vaizdo prie vaizdo
3. Peržiūrėdami stebėkite šoninės juostos histogramą: kadrą, kurio pasiskirstymas staigiai šoktelėja, verta apžiūrėti atidžiau

### Kalibravimo taškų patikrinimas

1. Pasirinkite **RAW (Target)** ant tikslinio kadro
2. Patikrinkite, ar tikslas aiškiai matomas ir aptiktas
3. Pereikite prie kito tikslinio kadro — tikslo sluoksnis seka kartu

### Patikrinkite atspindžio verčių tikslumą

1. Pasirinkite **RAW (Reflectance)**

2. Perskaitykite**%** stulpelį skydelyje „Cursor Values“ — jis jau yra tinkamai pritaikytas šiam failui
3. Atlikite patikrinimą, lygindami su žinomomis medžiagomis kadre: sveika augmenija pasižymi aukštais NIR rodikliais ir žemais raudonos spalvos rodikliais; kalibravimo taško atspindžio koeficientas turėtų būti artimas paskelbtam

***

## Problemų sprendimas

### Lauke, kurio tikėjausi, nėra išskleidžiamajame meniu

**Galimos priežastys**

* Vaizdas nebuvo apdorotas — yra tik bazinis sluoksnis ir `RAW (Original)`
* Projekto nustatymuose nepažymėtas produkto eksporto jungiklis
* Produktas netaikomas tai kamerai (spinduliavimas ir atspindžio koeficientas RGB pagrindinėje kameroje; bet koks indeksas vienos juostos M3M monochromatinėje kameroje)
* Atspindžio kalibravimui nebuvo su kuo dirbti — nebuvo `.daq` žemyn nukreipto spinduliavimo aprėpties ir nebuvo kokybės patikrinimą išlaikiusio taikinio kadre — todėl kadras buvo grąžintas į „Vignette Corrected“ arba „Sensor Response“

**Ką daryti**

1. Patikrinkite vykdymo žurnalą: Chloros nurodo, kada nebuvo įmanoma eksportuoti prašomo produkto ir kodėl
2. Patikrinkite eksporto perjungiklius kiekvienam produktui atskirai [Projekto nustatymuose](../project-settings/project-settings.md)
3. Patikrinkite, ar produkto aplankas yra projekto išvesties medžio struktūroje
4. Pakartotinai apdorokite įjungus produktą

### Sluoksnių sąrašas atrodo pasenęs

Chloros, vykstant apdorojimui, iš naujo nuskaito projekto produktų aplankus ir ištaiso trūkstamus sluoksnių registravimus remdamasis tuo, kas iš tikrųjų yra diske, todėl sluoksnis, kurio eksportavimas baigtas, paprastai atsiranda savaime per apklausą. Perėjimas nuo vaizdo į kitą langą ir atgal priverčia atlikti naują apdorojimą.

### Atspindžio vertės atrodo dvigubai mažesnės nei turėtų būti

Beveik neabejotinai dalinate „LATTICE“ failą iš 65535. Naudokite `Chloros:PixelScale` (32768) arba peržiūrėkite stulpelį **%**, kuriame šis koeficientas jau yra pritaikytas.

### Indekso sluoksnis egzistuoja, bet vaizdas tuščias

Indeksui reikalingos juostos, kurių jūsų sluoksnyje nėra — pavyzdžiui, indeksas, skaitantis trečiąjį kanalą, pritaikytas vieno ar dviejų kanalų failui. Perjunkite į daugiakanalį sluoksnį (atspindžio arba be „bayeringo“) arba pasirinkite indeksą, atitinkantį kameros filtrą.

***

## Tolimesni veiksmai

* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) — žymeklio rodmenys, histograma ir GSD valdymas
* [**Indeksų/LUT bandymų erdvė**](index-lut-sandbox.md) — interaktyvus indeksų vizualizavimas ir eksportavimas
* [**Daugiaspektrinių indeksų formulės**](../project-settings/multispectral-index-formulas.md) — indeksų nuorodos
* [**Apdorojimo užbaigimas**](../processing-images-gui/finishing-the-processing.md) — išvesties aplankų medis, į kurį nukreipia šie sluoksniai
