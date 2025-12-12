# Vaizdo atidarymas per visą ekraną

Chloros vaizdo peržiūros programa suteikia specialią visą ekraną užimančią sąsają, skirtą daugiaspektrių vaizdų peržiūrai, analizei ir redagavimui. Nepriklausomai nuo to, ar peržiūrite originalius vaizdus, ar apdorotus rezultatus, vaizdo peržiūros programa siūlo galingus įrankius tikrinimui ir analizei.

## Prieiga prie Image Viewer

### Iš failų naršyklės

Dažniausias būdas atidaryti vaizdą Image Viewer:

1. Įsitikinkite, kad esate **Failų naršyklės** skirtuke <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Spustelėkite bet kurį **vaizdo miniatiūrą** vaizdų tinklelyje
3. Vaizdas atsidaro **pagrindinėje peržiūros srityje** (ekrano centre)
4. Vaizdas dabar įkeltas ir paruoštas peržiūrai visame ekrane

### Image Viewer skirtuko atidarymas

Kai vaizdas įkeltas į peržiūros sritį:

1. Spustelėkite **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> piktogramą kairėje šoninėje juostoje
2. Atsidarys vaizdų peržiūros skirtukas, kuriame pasirinktas vaizdas bus rodomas visame ekrane
3. Kairėje šoninėje juostoje bus prieinami išplėstiniai peržiūros ir analizės įrankiai

***

## Vaizdų peržiūros sąsajos apžvalga

### Pagrindinė rodymo sritis

Didžiausia ekrano dalis rodo jūsų vaizdą:

* **Pilna skiriamoji geba**: vaizdai rodomi natūralia skiriamąja geba
* **Zoomable**: Naudokite valdiklius arba pelės ratuką, kad padidintumėte vaizdą
* **Pannable**: Spustelėkite ir vilkite, kad perkelti vaizdą, kai jis padidintas
* **Aspect ratio maintained**: Vaizdai proporcingai masteliuojami

***

## Peržiūros parinktys

### Pagrindinė vaizdų navigacija

#### Vaizdų peržiūra

Naršykite vaizdų rinkinį naudodami klavišų kombinacijas arba mygtukus:

* **Kitas vaizdas**: spustelėkite mygtuką → arba paspauskite klavišą **→** (dešinė rodyklė)
* **Ankstesnis vaizdas**: spustelėkite mygtuką ← arba paspauskite klavišą **←** (kairė rodyklė)
* **Perėjimas prie konkretaus vaizdo**: grįžkite į failų naršyklę ir spustelėkite norimą miniatiūrą

#### Padidinimo valdikliai

Reguliuokite padidinimą, kad galėtumėte peržiūrėti vaizdo detales:

**Padidinti:**

* Spustelėkite mygtuką **+** (pliusas)
* Paspauskite klavišą **+** arba **=**
* Pasukite pelės ratuką **aukštyn**

**Sumažinti:**

* Spustelėkite mygtuką **−** (minusas)
* Paspauskite klavišą **−** (minusas)
* Pasukite pelės ratuką **žemyn**

**Pritaikyti prie ekrano:**

* Spustelėkite mygtuką **↔** (Pritaikyti)
* Paspauskite klavišą **0** (Nulis)
* Dukart spustelėkite vaizdą

#### Peržiūra padidinus

Kai padidinama daugiau nei ekrano dydis:

1. Perkelkite pelės žymeklį ant vaizdo
2. Spustelėkite ir **laikykite nuspaudę kairįjį pelės mygtuką**
3. **Vilkite**, kad perkelti vaizdą
4. Atleiskite, kad sustabdyti perkelimą

**Alternatyva**: Naudokite rodyklių klavišus, kad perkelti mažais žingsniais

***

## Pikselių vertės tikrinimas

### Pikselių verčių peržiūra prie žymeklio

Kai pelės žymeklį perkelite ant vaizdo, pikselių vertės rodomos realiuoju laiku:

**Vertės rodymo vieta:**

* **Plaukiojantis skaičius ir raudona linija dešinėje pusėje esančioje indeksų LUT gradiento legendoje**
* **Padidinus vaizdą, plaukiojanti vertė rodomas šalia žymeklio ir paryškintos pikselės**
* Rodo pikselių, esančių **po žymekliu arba paryškintų**, vertes
* Atnaujinama, kai judinate pelę

***

## Vaizdų tipai, kuriuos galite peržiūrėti

### Originalūs vaizdai (prieš apdorojimą)

**RAW + JPG vaizdai iš fotoaparato:**

* Rodo RAW duomenis kaip peržiūrą
* Rodo originalias, nekoryguotas vertes
* Naudinga vaizdo kokybei patikrinti prieš apdorojimą

### Kalibruoti atspindžio vaizdai

**Po apdorojimo:**

* Koreguota vinjetė
* Kalibruotas atspindys
* Daugialypės juostos TIFF (Red, Green, NIR ir kt.)
* Moksliniai duomenys paruošti analizės

### Indekso vaizdai

**NDVI, NDRE, GNDVI ir kt. (\_NDVI.tif failai):**

* Vienos juostos pilkosios skalės vaizdai
* Pikselių vertės atspindi indekso skaičiavimo rezultatus
* Normalizuotų indeksų diapazonas paprastai yra nuo -1 iki +1
* Vizualizavimui galima taikyti spalvų LUT

***

## Indekso ir LUT taikymas

Taikykite daugiaspektrinius indeksus ir spalvų paieškos lenteles:

1. **Image Viewer** šoninėje juostoje raskite **Index/LUT Sandbox** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> šoninėje juostoje
2. Pasirinkite augmenijos indeksą (NDVI, NDRE ir kt.)
3. Pasirinkite daugiaspektrę formulę arba sukurkite savo (tik Chloros+)
4. Vizualizavimui taikykite spalvų LUT gradientą
5. Nustatykite verčių intervalus ir ribas

Išsamias instrukcijas rasite [Indekso/LUT smėlio dėžėje](index-lut-sandbox.md).

***

## Klavišų kombinacijos

### Navigacija

* **→** (dešinė rodyklė): Kitas vaizdas
* **←** (kairė rodyklė): Ankstesnis vaizdas
* **Pagrindinis**: Pirmasis vaizdas sąraše
* **End**: paskutinis vaizdas sąraše

### Padidinimas

* **+** arba **=**: padidinti
* **−**: sumažinti
* **0** (nulis): pritaikyti prie ekrano
* **Peles ratukas**: padidinti/sumažinti

### Peržiūros valdikliai

* **P**: Perjungti pikselių procentų režimą
* **L**: Perjungti sluoksnių skydelį
* **Esc**: Uždaryti visą ekraną arba grįžti į failų naršyklę

### Kita

* **Ctrl+S**: Išsaugoti dabartinį vaizdą
* **F**: Visas ekranas (jei galima)

***

### Indeksų skaičiavimo tikrinimas

Patikrinkite, ar indeksai apskaičiuoti teisingai:

1. Atidarykite NDVI arba kitą indekso vaizdą.
2. Patikrinkite augmenijos plotus:
   * **NDVI**: Sveikiems augalams turėtų rodyti 0,4–0,9.
   * **NDRE**: didesni skaičiai rodo spartų augimą
   * **GNDVI**: panašus į NDVI, bet jautrus chlorofilui
3. Patikrinkite neaugmenį:
   * **Dirvožemis**: artimas 0 arba šiek tiek neigiamas
   * **Vanduo**: neigiamos vertės (-0,5 iki 0)

***

## Vaizdo peržiūros problemų sprendimas

### Vaizdas neatsidaro

**Galimos priežastys:**

* Failas sugadintas apdorojimo metu
* Nepalaikomas failo formatas
* Nepakankama atmintis dideliam vaizdui

**Sprendimai:**

1. Pabandykite atidaryti išorinėje peržiūros programoje, kad patikrintumėte failo vientisumą
2. Patikrinkite, ar failo formatas atitinka tikėtiną tipą
3. Uždarykite kitas programas, kad atlaisvintumėte atmintį
4. Pabandykite mažesnį / kitokį vaizdą

### Juodas arba baltas vaizdas

**Galimos priežastys:**

* Vertės diapazonas viršija ekrano galimybes
* 32 bitų plūduriuojantis vaizdas su neįprastomis vertėmis
* Indekso skaičiavimo klaida

**Sprendimai:**

1. Patikrinkite pikselių vertes – jei visos labai mažos arba labai didelės, sureguliuokite ekrano diapazoną.
2. Pabandykite atidaryti QGIS arba panašioje programoje su automatiniu diapazono reguliavimu.
3. Patikrinkite apdorojimo klaidų žurnalą, ar nėra klaidų.

### Pikselių vertės atrodo neteisingos

**Galimos priežastys:**

* Peržiūrimas neteisingas vaizdas (originalus, o ne apdorotas)
* Kalibravimas nebuvo taikytas teisingai
* Įvesties duomenys nebuvo įtraukti į šviesos jutiklio duomenis
* Procentinis režimas buvo perjungtas neteisingai

**Sprendimai:**

1. Patikrinkite, ar žiūrite apdorotą rezultatą (patikrinkite failo pavadinimo galūnę)
2. Patikrinkite procentinio režimo mygtuko būseną
3. Palyginkite su žinomais gerais vaizdais iš to paties duomenų rinkinio

***

## Tolimesni veiksmai

Dabar, kai galite peržiūrėti vaizdus visame ekrane:

* [**Vaizdų sluoksniai**](image-layers.md) – sužinokite apie daugiabandę vizualizaciją
* [**Indeksas/LUT smėlio dėžė**](index-lut-sandbox.md) – taikykite pasirinktinius indeksus ir spalvų atitikmenis
* [**Daugiaspektrinės indeksų formulės**](../project-settings/multispectral-index-formulas.md) – Sužinokite apie galimus indeksus

Dėl apdorojimo darbo eigos žr.:

* [**Vaizdų apdorojimas (GUI)**](../processing-images-gui/adding-files-to-a-project.md) – Išsamus apdorojimo vadovas
