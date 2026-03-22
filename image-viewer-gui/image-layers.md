# Vaizdo sluoksniai

Išskleidžiamajame meniu „Vaizdo sluoksniai“ programoje „Chloros Image Viewer“ galite greitai perjungti skirtingas to paties vaizdo versijas – nuo originalių užfiksuotų vaizdų iki apdorotų atspindžio rezultatų ir apskaičiuotų indeksinių vaizdų.

## Kas yra vaizdo sluoksniai?

Chloros programoje **sluoksniai** reiškia skirtingus vaizdo rezultatus, kuriuos galima gauti iš vieno šaltinio vaizdo. Apdorojant vaizdus, Chloros sukuria keletą versijų:

* **Originalūs vaizdai** (JPG ir RAW failai iš jūsų fotoaparato)
* **Atstumo kalibruoti** rezultatai (jei buvo įjungta atstumo kalibracija)
* **Tiksliniai vaizdai** (jei vaizde yra kalibravimo tikslai)
* **Indeksiniai vaizdai** (NDVI, NDRE, GNDVI ir kt., jei buvo sukonfigūruoti indeksai)**Sluoksnių pasirinkimo išskleidžiamasis meniu**, esantis vaizdo peržiūros programos dešinėje viršutinėje dalyje, leidžia akimirksniu perjungti šias versijas neišeinant iš peržiūros programos.***

## Galimi sluoksnių tipai

### JPG

* Originalus JPG peržiūros vaizdas iš jūsų fotoaparato
* Visada prieinamas visiems vaizdams
* Neapdorotas, toks, koks užfiksuotas fotoaparatu
* Įkeliama ir rodoma greičiausiai

**Kada peržiūrėti:**

* Greita originalaus kadro peržiūra
* Vaizdo kompozicijos ir kadravimo patikrinimas
* Kadravimo kokybės patikrinimas prieš apdorojimą

### RAW (Originalus)

* Originalūs RAW jutiklio duomenys iš jūsų fotoaparato
* Debayered be jokio papildomo apdorojimo
* Didesnis bitų gylis nei JPG (paprastai 12 bitų arba 14 bitų jutiklio duomenys)

**Kada peržiūrėti:**

* Originalaus jutiklio duomenų kokybės tikrinimas
* Jutiklio problemų ar artefaktų tikrinimas
* Rezultatų prieš ir po apdorojimo palyginimas

### RAW (Tikslas)

* Rodomas tik tiems vaizdams, kuriuose nustatyta, kad yra kalibravimo tikslai
* Rodo originalų RAW vaizdą su aptiktu tikslu
* Naudojamas patikrinti, ar tikslo aptikimas buvo sėkmingas

**Kada peržiūrėti:**

* Patvirtinant, kad kalibravimo taikiniai buvo aptikti teisingai
* Tikrinant taikinio vaizdo kokybę
* Šalinant kalibravimo problemas

{% hint style="info" %}
**Taikinio sluoksnis**: Šis sluoksnis pasirodo išskleidžiamajame meniu tik tiems vaizdams, kuriuose yra kalibravimo taikiniai. Įprasti užfiksuoti vaizdai neturės šios parinkties.
{% endhint %}

### RAW (atspindžio koeficientas)

* Kalibruotas atspindžio koeficiento išvesties vaizdas
* Ištaisyta vinjetė (jei įjungta apdorojimo metu)
* Atspindžio koeficientas kalibruotas naudojant taškų duomenis (jei įjungta)
* Daugiajuostis TIFF su visais kameros kanalais
* Pikselių reikšmės atspindi atspindžio koeficiento procentinę dalį (naudojant procentinį režimą)
* Paruoštas redaguoti naudojant [Index/LUT Sandbox](index-lut-sandbox.md)

**Kada peržiūrėti:**

* Tikrinant kalibruotus rezultatus
* Tikrinant kalibravimo kokybę
* Tikrinant pikselių vertes dėl mokslinio tikslumo
* Lyginant su originalu, kad pamatytumėte kalibravimo efektus

{% hint style="success" %}
**Rekomenduojama**: Naudokite RAW (atspindžio) sluoksnį, kai tikrinate pikselių vertes moksliniams matavimams ir analizei.
{% endhint %}

### RAW (NDVI indeksas)... ir panašūs

* Apskaičiuotas augmenijos indekso vaizdas (šiuo pavyzdžiu – NDVI)
* Indekso pavadinimas keičiasi priklausomai nuo to, kuris indeksas buvo sukonfigūruotas apdorojimo metu
* Pavyzdžiai: RAW (NDVI indeksas), RAW (NDRE indeksas), RAW (GNDVI indeksas) ir kt.
* Vienos juostos pilkosios skalės vaizdas, rodantis indekso apskaičiavimo rezultatus
* Kiekvienam indeksui, sukonfigūruotam projekto nustatymuose, rodomas vienas sluoksnis

**Galimi indeksų pavadinimai:**

* RAW (NDVI indeksas)
* RAW (NDRE indeksas)
* RAW (GNDVI indeksas)
* RAW (OSAVI indeksas)
* RAW (EVI indeksas)
* RAW (SAVI indeksas)
* Ir daugelis kitų... (žr. [Daugiaspektrinių indeksų formulės](../project-settings/multispectral-index-formulas.md))

**Kada peržiūrėti:**

* Tikrinant indekso skaičiavimo rezultatus
* Tikrinant indekso verčių intervalus
* Nustatant dominančias sritis
* Tikrinant indekso vaizdus prieš naudojant GIS ar analizėje

***

## Sluoksnių pasirinkimo įrankio naudojimas

### Išskleidžiamojo meniu atidarymas

1. Atidarykite vaizdą visos ekrano režimu (spustelėkite bet kurią miniatiūrą vaizdo peržiūroje)
2. Suraskite **sluoksnių išskleidžiamąjį meniu** peržiūros dešiniame viršutiniame kampe
3. Išskleidžiamajame meniu rodomas šiuo metu pasirinktas sluoksnis (pvz., „JPG“)
4. Spustelėkite išskleidžiamąjį meniu, kad pamatytumėte visus galimus sluoksnius

### Sluoksnių perjungimas

1. Spustelėkite sluoksnių išskleidžiamąjį meniu, kad atidarytumėte sąrašą
2. Rodomi visi galimi dabartinio vaizdo sluoksniai
3. Spustelėkite bet kurio sluoksnio pavadinimą, kad pereitumėte prie tos versijos
4. Vaizdas atnaujinamas iš karto, kad būtų rodomas pasirinktas sluoksnis

**Greitas perjungimas:**

* Išskleidžiamasis meniu įsimena jūsų paskutinį pasirinkimą
* Pereidžiant prie kito vaizdo, Chloros bando rodyti to paties tipo sluoksnį
* Jei to sluoksnio nėra kitame vaizde, numatytasis sluoksnis yra JPG

### Sluoksnių prieinamumas

Ne visi sluoksniai yra prieinami kiekvienam vaizdui:

**Visada prieinami:*** ✅ JPG (kiekvienas vaizdas turi JPG peržiūrą)

**Prieinami su sąlygomis:**

* ⚠️ RAW (Originalus) – Tik jei vaizdas buvo užfiksuotas RAW arba RAW+JPG režimu
* ⚠️ RAW (Tikslas) – Tik jei vaizde yra aptikti kalibravimo tikslai
* ⚠️ RAW (atspindys) – tik po apdorojimo su įjungtu atspindžio kalibravimu
* ⚠️ RAW (\[Indeksas] indeksas) – tik po apdorojimo su sukonfigūruotais indeksais

***

## Sluoksnių išsaugojimas

### Perėjimas tarp vaizdų

Kai pereinate prie kito vaizdo (naudodami rodyklių klavišus arba spustelėdami miniatiūras):**Sluoksnio nustatymas išlieka:**

* Jei žiūrite „RAW (atspindys)“, kitas vaizdas rodo „RAW (atspindys)“ (jei yra)
* Jei žiūrite „RAW (NDVI indeksas)“, kitas vaizdas rodo „RAW (NDVI indeksas)“ (jei yra)
* Jei to paties sluoksnio nėra, numatytasis yra JPG

**Darbo eigos pavyzdys:**

1. Atidarykite 1 paveikslėlį, perjunkite į RAW (NDVI indeksas)
2. Paspauskite →, kad peržiūrėtumėte 2 paveikslėlį
3. 2 paveikslėlyje automatiškai rodomas RAW (NDVI indeksas) sluoksnis
4. Tęskite naršymą – visuose vaizduose rodomas NDVI sluoksnis
5. Labai efektyvu peržiūrint indekso rezultatus daugelyje vaizdų

***

## Įprasti darbo srautai

### Darbo srautas 1: Palyginimas prieš ir po

**Tikslas**: Palyginti originalų ir kalibruotą vaizdą

1. Atidarykite apdorotą vaizdą vaizdų peržiūroje
2. Išskleidžiamajame meniu pasirinkite **RAW (Originalus)**

3. Atkreipkite dėmesį į vinjetavimą ir nekalibruotas vertes
4. Išskleidžiamajame meniu pasirinkite **RAW (Atstumas)**

5. Palyginkite – vinjetavimas pašalintas, vertės kalibruotos

### Darbo eiga 2: Indekso peržiūra

**Tikslas**: Greitai peržiūrėti NDVI rezultatus visame duomenų rinkinyje

1. Atidarykite pirmąjį apdorotą vaizdą
2. Išskleidžiamajame meniu pasirinkite **RAW (NDVI indeksas)**

3. Naudokite → rodyklės klavišą, kad pereitumėte prie kito vaizdo
4. NDVI sluoksnis išlieka automatiškai
5. Tęskite per visus vaizdus, tikrindami NDVI modelius
6. Perjunkite į **RAW (NDRE indeksas)**, kad palygintumėte

### Darbo eiga 3: Tikslo patikrinimas

**Tikslas**: Patikrinti, ar visi tikslo vaizdai buvo aptikti teisingai

1. Pereikite prie tikslo vaizdo
2. Išskleidžiamajame meniu pasirinkite **RAW (Tikslas)**

3. Patikrinkite, ar kalibravimo tikslai yra aiškiai matomi ir aptikti
4. Pereikite prie kito tikslo vaizdo
5. Pakartokite patikrinimą visiems tikslams

### Darbo eiga 4: Pikselių verčių tikrinimas

**Tikslas**: Patikrinti atspindžio vertes dėl mokslinio tikslumo

1. Atidarykite apdorotą vaizdą
2. Pasirinkite **RAW (Atspindys)** sluoksnį
3. Įjunkite **Pikselių procentas** režimą (mygtukas viršutiniame dešiniajame įrankių juostoje)
4. Perkelkite žymeklį virš augmenijos plotų
5. Patikrinkite, ar pikselių vertės yra numatytuose intervaluose (30–70 % NIR, 5–15 % Red)
6. Patikrinkite, ar dirvožemio ir vandens plotų vertės yra tinkamos

***

## Pikselių verčių supratimas pagal sluoksnius

Skirtingi sluoksniai rodo skirtingus pikselių verčių intervalus:

### JPG sluoksnis

* **Intervalas**: 0–255 (8 bitai)
* **Reikšmė**: Rodomosios vertės, koreguotos pagal gama
* **Naudojimas**: Tik vizualiai apžiūrai, ne mokslinėms matavimams

### RAW (Originalus)

* **Intervalas**: 0–65535 (16 bitai)
* **Reikšmė**: Neapdoroti skaitmeniniai jutiklio duomenys
* **Naudojimas**: Jutiklio veikimo tikrinimui, nekalibruoti

### RAW (atspindžio)

* **Diapazonas**: 0–65 535 (16 bitų TIFF) arba 0,0–1,0 (32 bitų procentais)
* **Reikšmė**: Kalibruotas atspindžio procentas
* **Naudojimas**: Moksliniai matavimai ir analizė**16 bitų TIFF atveju:**Padalinkite iš 65 535, kad gautumėte atspindžio procentą**32 bitų procentų atveju:** Vertės tiesiogiai atspindi procentą (0,5 = 50 % atspindžio)

### RAW (indeksiniai vaizdai)

* **Diapazonas**: Skiriasi priklausomai nuo indekso (paprastai nuo -1,0 iki +1,0 normalizuotiems indeksams)
* **Reikšmė**: Indekso skaičiavimo rezultatas
* **Pavyzdžiai**:
  * NDVI: nuo -1 iki +1 (augmenija paprastai nuo 0,4 iki 0,9)
  * NDRE: nuo -1 iki +1 (streso nustatymas)
  * EVI: nuo 0 iki 1 (pagerinta augmenija)

***

## Patarimai ir geriausia praktika

### Efektyvus sluoksnių perjungimas

* **Klavišų kombinacijų naudojimas**: Nors sluoksniams nėra klavišų kombinacijų, navigacijos rodyklės (←/→) veikia visuose sluoksniuose
* **Nuoseklūs darbo srautai**: Pasirinkite vieną sluoksnį (pvz., NDVI) ir peržiūrėkite visą duomenų rinkinį prieš perjungdami į kitą
* **Greiti palyginimai**: Perjunkite tarp „Original“ ir „Reflectance“, kad patikrintumėte apdorojimo kokybę

### Veiklos svarstymai

* **JPG įkeliami greičiausiai**: Naudokite greitam naršymui per daugybę vaizdų
* **RAW sluoksniai įkeliami lėčiau**: Didesnė skiriamoji geba ir bitų gylis
* **Indekso sluoksniai**: Panašus greitis kaip ir atspindžio sluoksnių
* **Pirmasis įkėlimas yra lėčiausias**: Vėlesni to paties sluoksnio peržiūros yra talpinami į talpyklą ir yra greitesni

### Kokybės patikrinimas

* **Visada patikrinkite RAW (Original)**: Patikrinkite šaltinio duomenų kokybę prieš pasitikėdami apdorotais rezultatais
* **Palyginkite sluoksnius**: naudokite sluoksnių perjungimą, kad įsitikintumėte, jog apdorojimas vyko teisingai
* **Patikrinkite indeksų diapazonus**: naudokite „Pixel Percent“ režimą su indeksų sluoksniais, kad įsitikintumėte, jog vertės yra priimtinos***

## Problemų sprendimas

### Sluoksnis nėra prieinamas

**Problema**: laukiamas sluoksnis neatsiranda išskleidžiamajame meniu**Galimos priežastys:**

* Vaizdas nebuvo apdorotas (galimi tik JPG ir RAW (Original) formatai)
* Apdorojimo metu buvo išjungtas atspindžio kalibravimas
* Konkretus indeksas nebuvo nustatytas projekto nustatymuose
* Vaizdas yra tik taikinio vaizdas (taikinams indeksai nesukuriami)

**Sprendimai:**

1. Patikrinkite, ar vaizdas buvo apdorotas (patikrinkite išvesties aplanką, ar jame yra apdoroti failai)
2. Patikrinkite projekto nustatymus, kad įsitikintumėte, jog indeksai buvo nustatyti
3. Apdorokite iš naujo, įjungę norimus indeksus

### Rodomas neteisingas sluoksnis

**Problema**: Vaizdas atidaromas netikėtame sluoksnyje**Priežastis**: Perkelti ankstesnio vaizdo sluoksnio nustatymai, tačiau to sluoksnio dabartiniame vaizde nėra**Sprendimas**: Chloros automatiškai grįžta prie JPG, kai pageidaujamas sluoksnis nėra prieinamas – tai yra normalu

### Nematomi kalibravimo taikiniai

**Problema**: RAW (taikinio) sluoksnyje nerodomas taikinio aptikimas**Galimos priežastys:**

* Taikiniai nebuvo aptikti apdorojimo metu
* Vaizdas iš tikrųjų neturi taikinio
* Taikinio aptikimo nustatymai pernelyg griežti

**Sprendimai:**

1. Patikrinkite „Debug Log“ (Debug žurnalo) įrašus, ar nėra pranešimų „Target found“ (Taikinys rastas)
2. Patikrinkite, ar vaizde iš tiesų yra matomi kalibravimo taškai
3. Nustatykite taškų aptikimo parametrus projekto nustatymuose
4. Žiūrėkite [Taškų vaizdų pasirinkimas](../processing-images-gui/choosing-target-images.md)

***

## Susijusios funkcijos

### Vaizdo peržiūros įrankiai

Peržiūrėdami bet kurį sluoksnį, galite naudoti:

* **Mastelio valdymą**: Padidinkite vaizdą, kad galėtumėte apžiūrėti detales
* **Perkėlimą**: Spustelėkite ir vilkite, kad perkelti padidintą vaizdą
* **Pikselių verčių peržiūra**: Peržiūrėkite vertes kursoriaus vietoje
* **Navigacijos rodyklės**: Perkelkite tarp vaizdų išlaikydami sluoksnį
* **Pikselių procentų režimas**: Perjunkite tarp DN ir procentų rodymo

Žr. [Vaizdo atidarymas visame ekrane](opening-an-image-full-screen.md), kur rasite išsamią vaizdo peržiūros programos dokumentaciją.

### Indekso/LUT bandymų aplinka

Interaktyviam indekso testavimui ir vizualizavimui:

* **Indekso skaičiavimas realiuoju laiku**: Išbandykite įvairias indekso formules
* **LUT spalvų atvaizdavimas**: Taikykite spalvų gradientus pilkosios skalės indeksams
* **Vizualizacijų eksportavimas**: Išsaugokite spalvotus indekso vaizdus

Išsamią informaciją rasite [Indekso/LUT bandymų aplinkoje](index-lut-sandbox.md).

***

## Tolimesni veiksmai

Dabar, kai jau suprantate vaizdo sluoksnius:

* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) – išsamus „Image Viewer“ vadovas
* [**Indekso/LUT smėlio dėžė**](index-lut-sandbox.md) – interaktyvus indekso vizualizavimas
* [**Daugiaspektrinės indeksų formulės**](../project-settings/multispectral-index-formulas.md) – Galimų indeksų sąrašas
* [**Apdorojimo užbaigimas**](../processing-images-gui/finishing-the-processing.md) – Apdorotų rezultatų supratimas
