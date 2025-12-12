# Vaizdo sluoksniai

Chloros vaizdo peržiūros programos išskleidžiamajame meniu „Vaizdo sluoksniai“ galite greitai perjungti skirtingas to paties vaizdo versijas – nuo originalių nuotraukų iki apdorotų atspindžio rezultatų ir apskaičiuotų indeksų vaizdų.

## Kas yra vaizdo sluoksniai?

Chloros programoje **sluoksniai** reiškia skirtingus vaizdo išvesties variantus, kurie yra prieinami vienam šaltinio vaizdui. Apdorojant vaizdus, Chloros sukuria keletą versijų:

* **Originalūs vaizdai** (JPG ir RAW failai iš jūsų fotoaparato)
* **Atšvaitos kalibruoti** rezultatai (jei buvo įjungtas atšvaitos kalibravimas)
* **Tiksliniai vaizdai** (jei vaizde yra kalibravimo tikslai)
* **Indeksų vaizdai** (NDVI, NDRE, GNDVI ir kt., jei buvo sukonfigūruoti indeksai)

**Sluoksnių pasirinkimo išskleidžiamasis meniu**, esantis viršutiniame dešiniajame vaizdo peržiūros lango kampe, leidžia greitai pereiti iš vienos versijos į kitą neišeinant iš peržiūros lango.

***

## Galimi sluoksnių tipai

### JPG

* Originalus JPG peržiūros vaizdas iš jūsų fotoaparato
* Visada prieinamas visiems vaizdams
* Neapdorotas, kaip užfiksuotas fotoaparatu
* Greičiausiai įkeliama ir rodomas

**Kada peržiūrėti:**

* Greitas originalaus užfiksuoto vaizdo peržiūrėjimas
* Vaizdo kompozicijos ir kadravimo patikrinimas
* Užfiksuoto vaizdo kokybės patikrinimas prieš apdorojimą

### RAW (originalus)

* Originalūs RAW jutiklio duomenys iš jūsų fotoaparato
* Be post-processing, be post-processing
* Didesnis bitų gylis nei JPG (paprastai 12 bitų arba 14 bitų jutiklio duomenys)

**Kada peržiūrėti:**

* Tikrinant originalius jutiklio duomenų kokybę
* Tikrinant jutiklio problemas ar artefaktus
* Lyginant prieš ir po apdorojimo rezultatus

### RAW (tikslas)

* Rodomas tik vaizdams, kuriuose yra kalibravimo tikslai
* Rodo originalų RAW vaizdą su aptiktu tikslu
* Naudojamas tikslų aptikimo sėkmei patikrinti

**Kada peržiūrėti:**

* Kalibravimo tikslų teisingo aptikimo patvirtinimui.
* Tikslo vaizdo kokybės patikrinimui.
* Kalibravimo problemų sprendimui.

{% hint style=&quot;info&quot; %}
**Tikslo sluoksnis**: Šis sluoksnis rodomas tik išskleidžiamajame meniu vaizdams, kuriuose yra kalibravimo tikslai. Įprastiems užfiksuotiems vaizdams ši parinktis nebus rodoma.
{% endhint %}

### RAW (atspindys)

* Kalibruotas atspindžio išvesties vaizdas
* Koreguotas vinjetas (jei įjungta apdorojimo metu)
* Atspindys kalibruotas naudojant tikslo duomenis (jei įjungta)
* Daugialypės juostos TIFF su visais kameros kanalais
* Pikselių vertės atspindi atspindžio procentą (naudojant procentų režimą)
* Paruošta manipuliuoti su [Index/LUT Sandbox](index-lut-sandbox.md)

**Kada peržiūrėti:**

* Kalibruotų rezultatų tikrinimas
* Kalibravimo kokybės tikrinimas
* Pikselių verčių tikrinimas mokslinio tikslumo atžvilgiu
* Palyginimas su originalu, kad būtų matomi kalibravimo efektai

{% hint style=&quot;success&quot; %}
**Rekomenduojama**: Naudokite RAW (atspindžio) sluoksnį, kai tikrinate pikselių vertes mokslinėms matavimams ir analizei.
{% endhint %}

### RAW (NDVI indeksas)... ir panašūs

* Apskaičiuotas augmenijos indekso vaizdas (šiuo atveju NDVI)
* Indekso pavadinimas keičiasi priklausomai nuo to, kuris indeksas buvo sukonfigūruotas apdorojimo metu
* Pavyzdžiai: RAW (NDVI indeksas), RAW (NDRE indeksas), RAW (GNDVI indeksas) ir kt.
* Vienos juostos pilkosios skalės vaizdas, rodantis indekso apskaičiavimo rezultatus
* Kiekvienam indekso nustatymui projekto nustatymuose rodomas vienas sluoksnis

**Galimi indekso pavadinimai:**

* RAW (NDVI indeksas)
* RAW (NDRE indeksas)
* RAW (GNDVI indeksas)
* RAW (OSAVI indeksas)
* RAW (EVI indeksas)
* RAW (SAVI indeksas)
* Ir daugelis kitų... (žr. [Daugiaspektrinių indeksų formulės](../project-settings/multispectral-index-formulas.md))

**Kada peržiūrėti:**

* Nagrinėjant indekso skaičiavimo rezultatus
* Tikrinant indekso vertės diapazonus
* Nustatant dominančias sritis
* Tikrinant indekso vaizdus prieš naudojant GIS ar analizėje

***

## Sluoksnių pasirinkimo naudojimas

### Išskleidžiamojo meniu atidarymas

1. Atidarykite vaizdą visos ekrano režimu (spustelėkite bet kurią miniatiūrą vaizdo peržiūroje)
2. Raskite **sluoksnių išskleidžiamąjį meniu** peržiūros programos dešiniame viršutiniame kampe
3. Išskleidžiamajame meniu rodomas šiuo metu pasirinktas sluoksnis (pvz., „JPG“)
4. Spustelėkite išskleidžiamąjį meniu, kad pamatytumėte visus galimus sluoksnius

### Sluoksnių perjungimas

1. Spustelėkite sluoksnių išskleidžiamąjį meniu, kad atidarytumėte sąrašą
2. Rodomi visi galimi dabartinio vaizdo sluoksniai
3. Spustelėkite bet kurį sluoksnio pavadinimą, kad perjungtumėte į tą versiją
4. Vaizdas iš karto atnaujinamas, kad būtų rodomas pasirinktas sluoksnis.

**Greitas perjungimas:**

* Išskleidžiamasis meniu įsimena jūsų paskutinį pasirinkimą.
* Pereidžiant prie kito vaizdo, Chloros bando rodyti tą patį sluoksnio tipą.
* Jei to sluoksnio nėra kitame vaizde, numatytasis tipas yra JPG.

### Sluoksnių prieinamumas

Ne visi sluoksniai yra prieinami kiekvienam vaizdui:

**Visada prieinami:**

* ✅ JPG (kiekvienas vaizdas turi JPG peržiūrą)

**Prieinami tam tikromis sąlygomis:**

* ⚠️ RAW (Original) – tik jei vaizdas buvo užfiksuotas RAW arba RAW+JPG režimu
* ⚠️ RAW (Target) – tik jei vaizde yra aptikti kalibravimo taškai
* ⚠️ RAW (atspindys) – tik po apdorojimo su įjungtu atspindžio kalibravimu
* ⚠️ RAW (\[indeksas] indeksas) – tik po apdorojimo su sukonfigūruotais indeksais

***

## Sluoksnių išlikimas

### Perėjimas tarp vaizdų

Kai pereinate prie kito vaizdo (naudodami rodyklių klavišus arba spustelėdami miniatiūras):

**Sluoksnio nuostatos išsaugomos:**

* Jei peržiūrite „RAW (atspindys)“, kitas vaizdas rodo „RAW (atspindys)“ (jei yra)
* Jei peržiūrite „RAW (NDVI indeksas)“, kitas vaizdas rodo „RAW (NDVI indeksas)“ (jei yra)
* Jei to paties sluoksnio nėra, numatytasis nustatymas yra JPG

**Pavyzdinis darbo srautas:**

1. Atidarykite 1 paveikslėlį, perjunkite į RAW (NDVI indeksas)
2. Paspauskite →, kad peržiūrėtumėte 2 paveikslėlį
3. 2 paveikslėlyje automatiškai rodomas RAW (NDVI indeksas) sluoksnis
4. Tęskite naršymą – visi vaizdai rodo NDVI sluoksnį
5. Labai efektyvu peržiūrint indeksų rezultatus daugelyje vaizdų

***

## Įprasti darbo srautai

### Darbo srautas 1: Palyginimas prieš ir po

**Tikslas**: Palyginti originalų ir kalibruotą vaizdą

1. Atidarykite apdorotą vaizdą vaizdų peržiūroje
2. Išskleidžiamajame meniu pasirinkite **RAW (Original)**.
3. Atkreipkite dėmesį į vinjetavimą ir nekalibruotas vertes.
4. Išskleidžiamajame meniu pasirinkite **RAW (Reflectance)**.
5. Palyginkite – vinjetavimas pašalintas, vertės kalibruotos.

### Darbo eiga 2: Indekso peržiūra

**Tikslas**: greitai peržiūrėti NDVI rezultatus visame duomenų rinkinyje.

1. Atidarykite pirmąjį apdorotą vaizdą.
2. Išskleidžiamajame meniu pasirinkite **RAW (NDVI indeksas)**.
3. Naudodami rodyklės klavišą → pereikite prie kito vaizdo.
4. NDVI sluoksnis išlieka automatiškai.
5. Tęskite per visus vaizdus, tikrindami NDVI modelius.
6. Perjunkite į **RAW (NDRE indeksas)**, kad palygintumėte.

### Darbo eiga 3: Tikslo patikrinimas

**Tikslas**: Patikrinti, ar visi tikslo vaizdai buvo aptikti teisingai.

1. Pereikite prie tikslo vaizdo.
2. Išskleidžiamajame meniu pasirinkite **RAW (Tikslas)**.
3. Patikrinkite, ar kalibravimo tikslai yra aiškiai matomi ir aptikti.
4. Pereikite prie kito tikslinio vaizdo
5. Pakartokite patikrinimą visiems tikslams

### Darbo eiga 4: Pikselių vertės tikrinimas

**Tikslas**: Patikrinti atspindžio vertes dėl mokslinio tikslumo

1. Atidarykite apdorotą vaizdą
2. Pasirinkite **RAW (Atspindys)** sluoksnį
3. Įjunkite **Pikselių procentas** režimą (mygtukas viršutiniame dešiniajame įrankių juostoje)
4. Perkelkite žymeklį ant augmenijos plotų.
5. Patikrinkite, ar pikselių vertės yra numatytame intervale (30–70 % NIR, 5–15 % Red).
6. Patikrinkite, ar dirvožemio ir vandens plotų vertės yra tinkamos.

***

## Pikselių verčių supratimas pagal sluoksnius

Skirtingi sluoksniai rodo skirtingus pikselių verčių intervalus:

### JPG sluoksnis

* **Intervalas**: 0–255 (8 bitai)
* **Reikšmė**: rodomos vertės, koreguotos pagal gama
* **Naudojimas**: tik vizualinis patikrinimas, ne moksliniai matavimai

### RAW (originalas)

* **Intervalas**: 0–65535 (16 bitai)
* **Reikšmė**: Neapdoroti skaitmeniniai jutiklio skaičiai
* **Naudojimas**: Jutiklio veikimo tikrinimas, nekalibruotas

### RAW (atspindys)

* **Diapazonas**: 0–65 535 (16 bitų TIFF) arba 0,0–1,0 (32 bitų procentai)
* **Reikšmė**: Kalibruotas atspindžio procentas
* **Naudojimas**: Moksliniai matavimai ir analizė

**16 bitų TIFF atveju:** Padalinkite iš 65 535, kad gautumėte atspindžio procentą **32 bitų procento atveju:** Vertės tiesiogiai atspindi procentą (0,5 = 50 % atspindys)

### RAW (indeksų vaizdai)

* **Diapazonas**: Skiriasi pagal indeksą (paprastai nuo -1,0 iki +1,0 normalizuotiems indeksams)
* **Reikšmė**: Indekso apskaičiavimo rezultatas
* **Pavyzdžiai**:
  * NDVI: nuo -1 iki +1 (augmenija paprastai nuo 0,4 iki 0,9)
  * NDRE: nuo -1 iki +1 (streso nustatymas)
  * EVI: nuo 0 iki 1 (pagerinta augmenija)

***

## Patarimai ir geriausia praktika

### Efektyvus sluoksnių perjungimas

* **Klaviatūros sparčiųjų klavišų žinojimas**: nors sluoksniams nėra sparčiųjų klavišų, navigacijos rodyklės (←/→) veikia visuose sluoksniuose
* **Nuoseklios darbo eigos**: pasirinkite vieną sluoksnį (pvz., NDVI) ir peržiūrėkite visą duomenų rinkinį, prieš pereidami prie kito
* **Greiti palyginimai**: perjunkite tarp „Original“ ir „Reflectance“, kad patikrintumėte apdorojimo kokybę

### Našumo aspektai

* **JPG įkeliami greičiausiai**: naudokite greitam naršymui per daugybę vaizdų.
* **RAW sluoksniai įkeliami lėčiau**: didesnė skiriamoji geba ir bitų gylis.
* **Indeksų sluoksniai**: panašus greitis kaip atspindžio sluoksnių.
* **Pirmasis įkėlimas yra lėčiausias**: vėlesni to paties sluoksnio peržiūrėjimai yra talpinami į talpyklą ir yra greitesni.

### Kokybės patikrinimas

* **Visada tikrinkite RAW (originalą)**: prieš pasitikėdami apdorotais rezultatais, patikrinkite šaltinio duomenų kokybę.
* **Palyginkite sluoksnius**: naudokite sluoksnių perjungimą, kad patikrintumėte, ar apdorojimas veikė teisingai.
* **Patikrinkite indeksų diapazonus**: naudokite pikselių procentų režimą su indeksų sluoksniais, kad patikrintumėte, ar vertės yra pagrįstos.

***

## Trikčių šalinimas

### Sluoksnis nėra prieinamas

**Problema**: laukiamasis sluoksnis nerodomas išskleidžiamajame meniu.

**Galimos priežastys:**

* Vaizdas nebuvo apdorotas (galimi tik JPG ir RAW (originalūs) vaizdai)
* Apdorojimo metu buvo išjungtas atspindžio kalibravimas
* Projekto nustatymuose nebuvo sukonfigūruotas konkretus indeksas
* Vaizdas yra tik tikslinis vaizdas (tikslams indeksai nesukurti)

**Sprendimai:**

1. Patikrinkite, ar vaizdas buvo apdorotas (patikrinkite išvesties aplanką, ar yra apdoroti failai)
2. Patikrinkite projekto nustatymus, kad įsitikintumėte, jog indeksai buvo sukonfigūruoti
3. Apdorokite iš naujo, įjungę norimus indeksus

### Rodomas neteisingas sluoksnis

**Problema**: Vaizdas atidaromas netikėtu sluoksniu

**Priežastis**: Perkelti ankstesnio vaizdo sluoksnio nustatymai, tačiau to sluoksnio nėra dabartiniame vaizde

**Sprendimas**: Chloros automatiškai grįžta prie JPG, kai pageidaujamas sluoksnis nėra prieinamas – tai yra normalu.

### Negalima pamatyti kalibravimo tikslų

**Problema**: RAW (tikslo) sluoksnis nerodo tikslo aptikimo.

**Galimos priežastys:**

* Tikslai nebuvo aptikti apdorojimo metu.
* Vaizdas iš tikrųjų neturi tikslų.
* Tikslo aptikimo nustatymai pernelyg griežti

**Sprendimai:**

1. Debug Log žurnale patikrinkite, ar yra pranešimų „Target found“ (Tikslas rastas)
2. Patikrinkite, ar vaizde iš tiesų yra matomi kalibravimo tikslai
3. Projektų nustatymuose pakoreguokite tikslo aptikimo nustatymus
4. Žr. [Tikslo vaizdų pasirinkimas](../processing-images-gui/choosing-target-images.md)

***

## Susijusios funkcijos

### Vaizdo peržiūros įrankiai

Peržiūrėdami bet kurį sluoksnį, galite naudoti:

* **Zoom controls**: Padidinkite, kad galėtumėte peržiūrėti detales
* **Pan**: Spustelėkite ir vilkite, kad perkelti padidintą vaizdą
* **Pixel value inspection**: Peržiūrėkite vertes kursoriaus vietoje
* **Navigation arrows**: Perkelkite tarp vaizdų išlaikydami sluoksnį
* **Pixel Percent mode**: Perjunkite tarp DN ir procentinio rodymo

Visą vaizdo peržiūros dokumentaciją rasite [Vaizdo atidarymas visame ekrane](opening-an-image-full-screen.md).

### Indekso/LUT smėlio dėžė

Interaktyviam indekso testavimui ir vizualizavimui:

* **Indekso skaičiavimas realiuoju laiku**: išbandykite įvairias indekso formules
* **LUT spalvų atvaizdavimas**: pritaikykite spalvų gradientus pilkosios skalės indeksams
* **Vizualizacijos eksportavimas**: išsaugokite spalvotus indekso vaizdus

Išsamią informaciją rasite [Indekso/LUT smėlio dėžė](index-lut-sandbox.md).

***

## Tolimesni veiksmai

Dabar, kai suprantate vaizdo sluoksnius:

* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) – išsamus Image Viewer vadovas
* [**Index/LUT Sandbox**](index-lut-sandbox.md) – interaktyvus indeksų vizualizavimas
* [**Daugiaspektrinės indeksų formulės**](../project-settings/multispectral-index-formulas.md) – Galimų indeksų sąrašas
* [**Apdorojimo užbaigimas**](../processing-images-gui/finishing-the-processing.md) – Apdorotų rezultatų supratimas
