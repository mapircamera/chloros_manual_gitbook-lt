# Vaizdo sluoksniai

Chloros vaizdo peržiūros programos išskleidžiamajame meniu „Vaizdo sluoksniai“ galite greitai perjungti skirtingas to paties vaizdo versijas – nuo originalių nuotraukų iki apdorotų atspindžio rezultatų ir apskaičiuotų indeksų vaizdų.

## Kas yra vaizdo sluoksniai?

Chloros programoje **sluoksniai** reiškia skirtingus vaizdo išvesties variantus, kurie yra prieinami vienam šaltinio vaizdui. Apdorojant vaizdus, Chloros sukuria keletą versijų:

* **Originalūs vaizdai** (JPG ir RAW failai iš jūsų fotoaparato)
* **Atšvaitos kalibruoti** išvesties variantai (jei buvo įjungtas atspindžio kalibravimas)
* **Tiksliniai vaizdai** (jei vaizde yra kalibravimo tikslai)
* **Indeksų vaizdai** (NDVI, NDRE, GNDVI ir kt., jei buvo sukonfigūruoti indeksai)

**Sluoksnių pasirinkimo išskleidžiamasis meniu**, esantis vaizdo peržiūros programos dešiniame viršutiniame kampe, leidžia greitai perjungti šias versijas neišeinant iš peržiūros programos.

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

* Tikrinant originalių jutiklio duomenų kokybę
* Tikrinant jutiklio problemas ar artefaktus
* Lyginant prieš ir po apdorojimo rezultatus

### RAW (tikslas)

* Rodomas tik vaizdams, kuriuose yra kalibravimo tikslai
* Rodo originalų RAW vaizdą su aptiktu tikslu
* Naudojamas tikslų aptikimo sėkmei patikrinti

**Kada peržiūrėti:**

* Patvirtinant, kad kalibravimo tikslai buvo aptikti teisingai
* Tikrinant tikslo vaizdo kokybę
* Šalinant kalibravimo problemas

{% hint style=&quot;info&quot; %}
**Tikslo sluoksnis**: Šis sluoksnis rodomas tik išskleidžiamajame meniu vaizdams, kuriuose yra kalibravimo tikslai. Įprastiems užfiksuotiems vaizdams ši parinktis nebus rodomas.
{% endhint %}

### RAW (atspindys)

* Kalibruotas atspindžio išvesties vaizdas
* Koreguota vinjetė (jei įjungta apdorojimo metu)
* Atspindžio kalibruotas naudojant tikslo duomenis (jei įjungta)
* Daugialypė juosta TIFF su visais kameros kanalais
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
* Indekso pavadinimas keičiasi priklausomai nuo to, kuris indeksas buvo konfigūruotas apdorojimo metu
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

* Tikrinant indekso skaičiavimo rezultatus
* Tikrinant indekso verčių diapazonus
* Nustatant dominančias sritis
* Tikrinant indekso vaizdus prieš naudojant GIS ar analizėje

***

## Sluoksnių pasirinkimo naudojimas

### Išskleidžiamojo meniu atidarymas

1. Atidarykite vaizdą visos ekrano režimu (spustelėkite bet kurią miniatiūrą vaizdo peržiūroje)
2. Raskite **sluoksnių išskleidžiamąjį meniu** peržiūros programos dešiniame viršutiniame kampe.
3. Išskleidžiamajame meniu rodomas šiuo metu pasirinktas sluoksnis (pvz., „JPG“).
4. Spustelėkite išskleidžiamąjį meniu, kad pamatytumėte visus galimus sluoksnius.

### Sluoksnių perjungimas

1. Spustelėkite sluoksnių išskleidžiamąjį meniu, kad atidarytumėte sąrašą.
2. Rodomi visi galimi dabartinio vaizdo sluoksniai.
3. Spustelėkite bet kurį sluoksnio pavadinimą, kad perjungtumėte į tą versiją.
4. Vaizdas iš karto atnaujinamas, kad būtų rodomas pasirinktas sluoksnis.

**Greitas perjungimas:**

* Išskleidžiamasis meniu įsimena jūsų paskutinį pasirinkimą.
* Pereidžiant prie kito vaizdo, Chloros bando rodyti tą patį sluoksnio tipą.
* Jei to sluoksnio nėra kitame vaizde, numatytasis sluoksnis yra JPG.

### Sluoksnių prieinamumas

Ne visi sluoksniai yra prieinami kiekvienam vaizdui:

**Visada prieinami:**

* ✅ JPG (kiekvienas vaizdas turi JPG peržiūrą)

**Prieinami tam tikromis sąlygomis:**

* ⚠️ RAW (originalas) – tik jei vaizdas buvo užfiksuotas RAW arba RAW+JPG režimu
* ⚠️ RAW (tikslas) – tik jei vaizde yra aptikti kalibravimo tikslai
* ⚠️ RAW (atspindys) – tik po apdorojimo su įjungtu atspindžio kalibravimu
* ⚠️ RAW ([Indeksas] indeksas) – tik po apdorojimo su sukonfigūruotais indeksais

***

## Sluoksnių išlikimas

### Perėjimas tarp vaizdų

Kai pereinate prie kito vaizdo (naudodami rodyklių klavišus arba spustelėdami miniatiūras):

**Sluoksnių nustatymas išlieka:**

* Jei žiūrite „RAW (atspindys)“, kitas vaizdas rodo „RAW (atspindys)“ (jei yra)
* Jei žiūrite „RAW (NDVI indeksas)“, kitas vaizdas rodo „RAW (NDVI indeksas)“ (jei yra)
* Jei to paties sluoksnio nėra, numatytasis yra JPG

**Pavyzdinis darbo srautas:**

1. Atidarykite vaizdą 1, perjunkite į RAW (NDVI indeksas)
2. Paspauskite →, kad peržiūrėtumėte vaizdą 2
3. Vaizde 2 automatiškai rodomas sluoksnis RAW (NDVI indeksas)
4. Tęskite naršymą – visi vaizdai rodo NDVI sluoksnį.
5. Labai efektyvu peržiūrint indeksų rezultatus daugelyje vaizdų.

***

## Įprasti darbo srautai

### Darbo srautas 1: Palyginimas prieš ir po

**Tikslas**: Palyginti originalų ir kalibruotą vaizdą.

1. Atidarykite apdorotą vaizdą vaizdų peržiūroje.
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
6. Perjunkite į **RAW (NDRE indeksas)**, kad palygintumėte

### Darbo eiga 3: Tikslo patikrinimas

**Tikslas**: Patikrinti, ar visi tikslo vaizdai buvo aptikti teisingai

1. Pereikite prie tikslo vaizdo
2. Išskleidžiamajame meniu pasirinkite **RAW (Tikslas)**
3. Patikrinkite, ar kalibravimo tikslai yra aiškiai matomi ir aptikti
4. Pereikite prie kito tikslo vaizdo
5. Pakartokite patikrinimą visiems tikslams

### Darbo eiga 4: Pikselių vertės tikrinimas

**Tikslas**: Patikrinti atspindžio vertes dėl mokslinio tikslumo

1. Atidarykite apdorotą vaizdą
2. Pasirinkite **RAW (Atspindys)** sluoksnį
3. Įjunkite **Pikselių procentas** režimą (mygtukas viršutiniame dešiniajame įrankių juostoje)
4. Perkelkite žymeklį ant augmenijos plotų.
5. Patikrinkite, ar pikselių vertės yra numatytuose intervaluose (30–70 % NIR, 5–15 % Red).
6. Patikrinkite, ar dirvožemio ir vandens plotų vertės yra tinkamos.

***

## Pikselių verčių supratimas pagal sluoksnį

Skirtingi sluoksniai rodo skirtingus pikselių verčių intervalus:

### JPG sluoksnis

* **Intervalas**: 0–255 (8 bitai)
* **Reikšmė**: rodomos vertės, koreguotos pagal gama
* **Naudojimas**: tik vizualiai apžiūrėti, ne mokslinėms matavimams

### RAW (originalus)

* **Intervalas**: 0–65535 (16 bitai)
* **Reikšmė**: neapdoroti jutiklio skaitmeniniai skaičiai
* **Naudojimas**: jutiklio veikimo patikrinimas, nekalibruotas

### RAW (atspindys)

* **Diapazonas**: 0–65 535 (16 bitų TIFF) arba 0,0–1,0 (32 bitų procentai)
* **Reikšmė**: Kalibruotas atspindžio procentas
* **Naudojimas**: Moksliniai matavimai ir analizė

**16 bitų TIFF atveju:** Padalinkite iš 65 535, kad gautumėte atspindžio procentą **32 bitų procentų atveju:** Vertės tiesiogiai atspindi procentą (0,5 = 50 % atspindys)

### RAW (indeksų vaizdai)

* **Diapazonas**: Skiriasi pagal indeksą (paprastai nuo -1,0 iki +1,0 normalizuotiems indeksams)
* **Reikšmė**: Indekso skaičiavimo rezultatas
* **Pavyzdžiai**:
  * NDVI: nuo -1 iki +1 (augmenija paprastai nuo 0,4 iki 0,9)
  * NDRE: nuo -1 iki +1 (streso nustatymas)
  * EVI: nuo 0 iki 1 (pagerinta augmenija)

***

## Patarimai ir geriausia praktika

### Efektyvus sluoksnių perjungimas

* **Klaviatūros sparčiųjų klavišų žinojimas**: Nors sluoksniams nėra klavišų kombinacijų, navigacijos rodyklės (←/→) veikia visuose sluoksniuose
* **Nuoseklios darbo eigos**: pasirinkite vieną sluoksnį (pvz., NDVI) ir peržiūrėkite visą duomenų rinkinį, prieš pereidami prie kito
* **Greitas palyginimas**: perjunkite tarp „Original“ ir „Reflectance“, kad patikrintumėte apdorojimo kokybę

### Našumo aspektai

* **JPG įkeliami greičiausiai**: naudokite greitam naršymui per daugybę vaizdų.
* **RAW sluoksniai įkeliami lėčiau**: didesnė skiriamoji geba ir bitų gylis.
* **Indeksų sluoksniai**: panašus greitis kaip atspindžio sluoksnių.
* **Pirmasis įkėlimas yra lėčiausias**: vėlesni to paties sluoksnio peržiūrėjimai yra talpinami į talpyklą ir yra greitesni.

### Kokybės patikrinimas

* **Visada tikrinkite RAW (originalą)**: patikrinkite šaltinio duomenų kokybę, prieš pasitikėdami apdorotais rezultatais.
* **Palyginkite sluoksnius**: naudokite sluoksnių perjungimą, kad patikrintumėte, ar apdorojimas veikė teisingai
* **Patikrinkite indeksų diapazonus**: naudokite pikselių procentų režimą su indeksų sluoksniais, kad patikrintumėte, ar vertės yra pagrįstos

***

## Trikčių šalinimas

### Sluoksnis nėra prieinamas

**Problema**: laukiamasis sluoksnis nerodomas išskleidžiamajame meniu

**Galimos priežastys:**

* Vaizdas nebuvo apdorotas (galimi tik JPG ir RAW (originalūs) failai)
* Apdorojimo metu buvo išjungtas atspindžio kalibravimas
* Projekto nustatymuose nebuvo sukonfigūruotas konkretus indeksas
* Vaizdas yra tik tikslinis vaizdas (tikslams indeksai nebuvo sugeneruoti)

**Sprendimai:**

1. Patikrinkite, ar vaizdas buvo apdorotas (patikrinkite apdorotų failų aplanką)
2. Patikrinkite projekto nustatymus, kad įsitikintumėte, jog indeksai buvo sukonfigūruoti
3. Apdorokite iš naujo, įjungę norimus indeksus.

### Rodomas neteisingas sluoksnis

**Problema**: Vaizdas atidaromas netikėtu sluoksniu.

**Priežastis**: Perkelti ankstesnio vaizdo sluoksnio nustatymai, tačiau to sluoksnio nėra dabartiniame vaizde.

**Sprendimas**: Chloros automatiškai grįžta prie JPG, kai pageidaujamas sluoksnis nėra prieinamas – tai yra normalu.

### Negaliu matyti kalibravimo tikslų

**Problema**: RAW (tikslo) sluoksnis nerodo tikslo aptikimo.

**Galimos priežastys:**

* Tikslai nebuvo aptikti apdorojimo metu.
* Vaizdas iš tikrųjų neturi tikslų.
* Tikslo aptikimo nustatymai yra pernelyg griežti.

**Sprendimai:**

1. Patikrinkite „Debug Log“ (Debug žurnalą) dėl pranešimų „Target found“ (Tikslas rastas).
2. Patikrinkite, ar vaizdas iš tikrųjų turi matomus kalibravimo tikslus.
3. Nustatykite tikslų aptikimo parametrus projekto nustatymuose.
4. Žr. [Tikslų vaizdų pasirinkimas](../processing-images-gui/choosing-target-images.md).

***

## Susijusios funkcijos

### Vaizdo peržiūros įrankiai

Peržiūrėdami bet kurį sluoksnį, galite naudoti:

* **Zoom controls**: padidinkite, kad galėtumėte peržiūrėti detales.
* **Pan**: spustelėkite ir vilkite, kad perkelti padidintą vaizdą.
* **Pikselių vertės tikrinimas**: peržiūrėkite vertes kursoriaus vietoje
* **Navigacijos rodyklės**: perkelkite tarp vaizdų išlaikydami sluoksnį
* **Pikselių procentų režimas**: perjunkite tarp DN ir procentų rodymo

Visą vaizdo peržiūros dokumentaciją rasite [Vaizdo atidarymas visame ekrane](opening-an-image-full-screen.md).

### Indekso/LUT smėlio dėžė

Interaktyviam indekso testavimui ir vizualizavimui:

* **Indekso skaičiavimas realiuoju laiku**: išbandykite įvairias indekso formules
* **LUT spalvų atvaizdavimas**: pritaikykite spalvų gradientus pilkosios skalės indeksams
* **Vizualizacijų eksportavimas**: išsaugokite spalvotus indekso vaizdus

Išsamią informaciją rasite [Indekso/LUT smėlio dėžėje](index-lut-sandbox.md).

***

## Kiti žingsniai

Dabar, kai suprantate vaizdo sluoksnius:

* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) – išsamus Image Viewer vadovas
* [**Index/LUT Sandbox**](index-lut-sandbox.md) – interaktyvus indeksų vizualizavimas
* [**Daugiaspektrinės indeksų formulės**](../project-settings/multispectral-index-formulas.md) – Galimų indeksų sąrašas
* [**Apdorojimo užbaigimas**](../processing-images-gui/finishing-the-processing.md) – Apdorotų rezultatų supratimas
