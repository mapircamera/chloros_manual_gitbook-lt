# Indeksų/LUT bandymų aplinka

Indeksų/LUT bandymų aplinka – tai interaktyvi darbo erdvė, esanti programoje „Chloros Image Viewer“, leidžianti realiuoju laiku eksperimentuoti su daugiaspektrinių indeksų skaičiavimais ir spalvotais vaizdais. Šis galingas įrankis padeda išbandyti įvairius indeksus, patikslinti verčių intervalus ir kurti publikavimui parengtus vaizdus, neperdirbant viso duomenų rinkinio.

## Kas yra indeksų/LUT smėlio dėžė?

### Tikslas

Smėlio dėžė suteikia:

* **Indeksų skaičiavimą realiuoju laiku** – bet kokį augmenijos indeksą pritaikykite akimirksniu
* **Interaktyvų LUT koregavimą** – tiksliai sureguliuokite spalvų perėjimus ir intervalus
* **Darbo eigos optimizavimą** – nustatykite geriausius parametrus prieš atliekant paketinį apdorojimą

### „Sandbox“ ir projekto apdorojimas

**„Index/LUT Sandbox“ (interaktyvus):**

* Vienas vaizdas vienu metu
* Momentinė grįžtamoji informacija
* Eksperimentinis ir iteracinis
* Nėra nuolatinių failų pakeitimų
* Puikiai tinka tyrinėjimui ir bandymams

**Projekto apdorojimas (partija):**

* Visas duomenų rinkinys iš karto
* Iš anksto sukonfigūruoti nustatymai
* Nuolatiniai išvesties failai
* Laiko reikalaujantis
* Geriausias, kai nustatymai yra galutiniai

{% hint style="success" %}
**Geriausias darbo srautas**: Naudokite „Sandbox“, kad eksperimentuotumėte ir rastumėte optimalius indekso ir LUT nustatymus, tada taikykite tuos nustatymus visam duomenų rinkiniui projekto apdorojimo metu.
{% endhint %}

***

## Darbas su indeksų/LUT „Sandbox“

### Iš anksto apskaičiuotų indeksų supratimas

Chloros indeksai gali būti taikomi projekto apdorojimo metu. Norint nuspręsti, kokius indeksų ir LUT nustatymus norite taikyti eksportuojant, paprasčiausia naudoti vaizdų peržiūros „Sandbox“.

„Sandbox“ leidžia jums:

* **Taikyti naujus indeksus ir spalvų gradientus (LUT)** duomenims vizualizuoti
* **Interaktyviai reguliuoti vizualizacijos nustatymus*** **Peržiūrėti** jau apskaičiuotus indeksinius vaizdus
* **Tikrinti** pikselių vertes visuose mastelio lygiuose

### Sandbox atidarymas

Prie indeksų/LUT sandbox galima prisijungti per **vaizdų peržiūros programos** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> šoninės juostos skirtuke:

1. Spustelėkite vaizdą failų naršyklės vaizdų lentelėje, jis atsidarys **vaizdų peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> skirtuke
2. Spustelėkite **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> , kad atidarytumėte kairėje esantį iššokantį šoninį meniu, jei jis dar nėra atidarytas

### Vaizdo pasirinkimas, kuriam taikyti indeksą/LUT

Norėdami dirbti su indeksu „Image Viewer“ <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sandbox:

1. **Atidarykite vaizdą** iš pagrindinio vaizdų tinklelio, spustelėdami jį
2. Tada atsidarys **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> skirtukas
3. Spustelėkite **sluoksnių išskleidžiamąjį meniu** (peržiūros lango viršutiniame dešiniajame kampe)
4. Išskleidžiamajame meniu pasirinkite sluoksnį:
   * RAW (atspindys)

### Indekso taikymas vaizdui

Kai vaizdas rodomas visame ekrane ir atidarytas **vaizdo peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> šoninė juosta:

1. Pažymėkite langelį „Indeksas“ šoninės juostos viršuje
2. Pasirinkite savo fotoaparato filtrą iš kairiojo išskleidžiamojo meniu
3. Pasirinkite norimą indekso formulę iš dešiniojo išskleidžiamojo meniu
4. Perkelkite filtro kanalo spalvų apskritimus į vietas žemiau esančioje indekso formulėje
5. Kai formulė bus teisinga, vaizdas atsinaujins ir parodys indekso vertes
6. Judinkite pelės žymeklį, kad pamatytumėte vertes žymeklio vietoje
7. Padidinkite vaizdą, kad pamatytumėte atskirus pikselius ir su jais susijusias vertes

Kiekvienas indeksas turi konkretų verčių diapazoną ir reikšmę:

#### NDVI pavyzdys

```

Formula: (NIR - Red) / (NIR + Red)

For Survey3W RGN camera:
NIR = 850nm band
Red = 661nm band

Result range: -1.0 to +1.0
Typical vegetation: 0.4 to 0.9
Stressed vegetation: 0.2 to 0.4
Bare soil: 0.0 to 0.2
Water: -0.1 to 0.1
```

Išsamią indeksų formulių dokumentaciją rasite [Daugiaspektrinių indeksų formulės](../project-settings/multispectral-index-formulas.md).

***

## Darbas su LUT (paieškos lentelėmis)

### Kas yra LUT?

**Paieškos lentelė (LUT)** susieja skaitmenines indekso vertes su spalvomis vizualizavimui:

* **Įvestis**: Indekso pikselio vertė (pvz., NDVI 0,65)
* **Išvestis**: RGB spalva (pvz., ryškiai žalia)
* **Tikslas**: Padaryti modelius lengviau matomus ir interpretuojamus**Pilkosios skalės ir spalvų LUT palyginimas:**

* Pilkosios skalės: Moksliškos ir neutralios, rodo neapdorotus duomenis
* Spalvų LUT: Intuityvios ir įspūdingos, išryškina modelius ir skirtumus

{% hint style="success" %}
**Vizualizacijos galia**: Taikant spalvotą LUT pilkosios skalės indeksiniam vaizdui, iš pirmo žvilgsnio tampa žymiai lengviau atpažinti modelius, anomalijas ir dominančias sritis.
{% endhint %}

### LUT taikymas indeksiniam vaizdui

Kai turite indeksinį vaizdą, kuriame rodomas

1. Spustelėkite <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> „+Pridėti LUT“ mygtuką
2. Pasirinkite spalvų gradientą
3. Nustatykite apkarpymo minimalius ir maksimalius galinius taškus
4. Nustatykite apkarpymo režimą
5. Pažymėkite langelį „Indeksas“ **vaizdo peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> , kad pritaikytumėte LUT

### Spalvų gradiento pasirinkimas

**Gradiento pasirinkimas:**

1. LUT skydelyje suraskite**spalvotą gradientų juostą**

2. Užveskite pelę ant jos, kad pamatytumėte galimus gradientų nustatymus
3. Pasirinkite norimą gradientą
4. Vaizdas **atnaujinamas iš karto** su naujomis spalvomis, kai pažymėta langelis „Indeksas“

{% hint style="success" %}
**Geriausia praktika**: Tokiems augmenijos indeksams kaip NDVI, Red-Yellow-Green gradientas yra intuityviausias, nes atitinka natūralias spalvų asociacijas (žalia = sveika, geltona = vidutinė, raudona = streso paveikta).
{% endhint %}

### Spalvų klasių nustatymas

**Klasės valdiklis**nustato, kiek atskirų spalvų pakopų bus jūsų gradiente:**Klasės skaičiaus parinktys:*** **2–5 klasės**: labai plačios kategorijos, aiškios zonos
* **6–10 klasių**: subalansuotas, tinkamas klasifikavimui
* **11–20 klasių**: sklandūs gradientai, ištisinis vaizdas
* **20+ klasių**: Beveik ištisinis, maksimalus sklandumas**Kaip reguliuoti:**

1. LUT skydelyje suraskite**spalvų pavyzdžių kvadratėlius po gradientų juosta**

2. Reguliuokite klasių skaičių, pridedant su mygtuku „+“
3. Pašalinkite klasių skaičių, dukart spustelėdami spalvų pavyzdį
4. Gradientas atnaujinamas **realaus laiko režimu** ant vaizdo**Poveikis vizualizacijai:*** **Mažiau klasių** (3–5): Sukuria aiškias zonas, supaprastintą klasifikaciją, lengviau atskirti kategorijas
* **Vidutinis klasių skaičius** (6–10): Subalansuotas požiūris, tinka daugumai taikymų
* **Daugiau klasių** (15–20): Sklandūs perėjimai, detalūs skirtumai, fotografinis vaizdas**Kada naudoti:*** **Mažai klasių (3–5)**: pristatymų skaidrės, klasifikavimo žemėlapiai, paprastos ataskaitos
* **Vidutinis klasių skaičius (6–10)**: bendroji analizė, subalansuotos detalės, standartinės ataskaitos
* **Daug klasių (15–20)**: mokslinė analizė, išsamus tyrimas, leidybos kokybės rezultatai

### Vertės intervalų tikslinimas

**Vertės intervalų valdikliai**nustato, kokios indekso vertės atitinka kokias spalvas jūsų gradiente:**Intervalų valdikliai LUT skydelyje:*** **Minimali vertė**: Spalvų skalės apatinė riba
* **Maksimali vertė**: Spalvų skalės viršutinė riba
* **Tarpinės vertės**: Automatiškai paskirstomos tarp minimalios ir maksimalios (remiantis klasių skaičiumi)

#### Mažiausios ir didžiausios verčių koregavimas

**Norėdami koreguoti verčių intervalus:**

1. LUT skydelyje suraskite įvesties laukelius**Min Value**ir**Max Value**

2. Spustelėkite laukelį**Min Value**

3. Įveskite norimą mažiausią vertę (pvz., `0.2`)
4. Paspauskite **Enter** arba spustelėkite už lauko ribų
5. Pakartokite su lauku **Maks. vertė** (pvz., `0.9`)
6. Vizualizacija **atnaujinama iš karto**{% hint style="info" %}**Automatinis mastelio keitimas**: Kai pirmą kartą pritaikote LUT, Chloros automatiškai nustato mažiausią ir didžiausią vertes pagal faktinį duomenų diapazoną vaizde. Tada galite susiaurinti šį diapazoną, kad sutelktumėte dėmesį į konkrečius jus dominančius verčių diapazonus.
{% endhint %}

**Pavyzdiniai NDVI diapazono koregavimai:*** **Visas diapazonas**: nuo `-1.0` iki `1.0` (rodo visas galimas vertes)
* **Sutelktas į augmeniją**: nuo `0.2` iki `0.9` (neįtraukiant pliko dirvožemio ir vandens)
* **Tik sveika augmenija**: nuo `0.5` iki `0.9` (paryškinti tik stiprius augalus)
* **Streso nustatymas**: nuo `0.2` iki `0.5` (paryškinti problemines vietas)
* **Pasirinktinis diapazonas**: Reguliuokite pagal stebėtus pikselių vertes**Kodėl reikia reguliuoti diapazonus?*** **Padidinkite kontrastą** jus dominančioje srityje
* **Išskirkite nereikšmingas vertes** (pvz., vandens telkinius, pliką dirvą)
* **Standartizuokite vizualizaciją** keliuose vaizduose ar skirtingomis datomis
* **Pabrėžkite subtilius skirtumus** siaurame verčių diapazone

### Vertės, viršijančios diapazoną, apkarpymas

Kai pikselių vertės viršija jūsų nustatytą minimalų/maksimalų diapazoną, galite kontroliuoti, kaip jos rodomos, naudodami **apkarpymo režimus**.

#### **Galimi apkarpymo režimo variantai:**

#### 1. Minimalus ir maksimalus

* Pikseliai, **mažesni už minimalų**→ rodomi naudojant**pirmąją spalvą** gradientu (pvz., raudoną)
* Pikseliai, **viršijantys maksimumą**→ rodomi naudojant**paskutinę spalvą** gradientu (pvz., žalia)
* **Naudojimo atvejis**: pabrėžti kraštutines vertes, parodyti visą duomenų intervalą su sodriomis spalvomis ribose
* **Pavyzdys**: NDVI vertės, mažesnės nei 0,2, visos rodomos raudonai, vertės, didesnės nei 0,9, visos rodomos žaliai

#### 2. Skaidrus fonas

* Pikseliai **už diapazono ribų**tampa**visiškai skaidrūs*** Tik pikseliai **diapazono ribose** rodo spalvų gradientą
* **Naudojimo atvejis**: GIS perdanga, konkrečių verčių diapazonų izoliavimas, tik dominančių sričių išskyrimas
* **Pavyzdys**: Rodyti spalvotai tik NDVI 0,4–0,7, visa kita – skaidru

{% hint style="warning" %}
**Skaidrumo apribojimas**: Skaidrūs pikseliai peržiūroje bus rodomi kaip fono spalva. Eksportuojant apdorojimo metu, skaidrumas išsaugomas PNG formatu, bet ne JPG.
{% endhint %}

#### 3. Indekso fonas

* Pikseliai **už ribų**rodomi**pilkosios skalės** (rodomos neapdorotos indekso reikšmės)
* Pikseliai **ribose**rodo**spalvų perėjimą*** **Naudojimo atvejis**: Subtilus išryškinimas, išlaikant kontekstą ir pabrėžiant dominančias sritis
* **Pavyzdys**: Spalvomis išryškinkite stresą patiriančią augmeniją (NDVI 0,3–0,5), o sveikas sritis rodykite pilka spalva

#### 4. Originalus fonas

* Pikseliai, **esančios už ribų**, rodo**originalų multispektrinį vaizdą*** Pikseliai, **esančios ribose**, rodo**spalvų gradientą*** **Naudojimo atvejis**: Labiausiai intuityvus – derina natūralų vaizdo kontekstą su analitiniu spalvotu sluoksniu
* **Pavyzdys**: Matykite tikrąjį lauko/pasėlių vaizdą su spalvomis pažymėtomis streso sritimis

### Tinkamo apkarpymo režimo pasirinkimas

| Apkarpymo režimas              | Tinkamiausias                                   | Vizualizacijos stilius          |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| **Minimalus ir maksimalus**    | Visų duomenų rodymas, mokslinė analizė     | Visi pikseliai nuspalvinti           |
| **Skaidrus fonas** | GIS perdangos, konkrečių intervalų izoliavimas    | Spalva intervale, už jo – tuščia |
| **Indeksinis fonas**       | Subtilus pabrėžimas, išlaikant duomenų kontekstą  | Spalva diapazone, už jo – pilka  |
| **Originalus fonas**    | Ataskaitos, pristatymai, intuityvi analizė | Spalva diapazone, už jo – nuotrauka |

### Pasirinktinių LUT spalvų kūrimas

Norėdami visiškai kontroliuoti vizualizaciją, galite kurti **pasirinktinius spalvų gradientus**, redaguodami atskirus spalvų sustojimus.**Norėdami sukurti pasirinktinį gradientą:**

1. LUT skydelyje suraskite**gradiento peržiūros juostą**

2. Po gradientu ieškokite**spalvų pavyzdžių kvadratėlių**

3.**Spustelėkite spalvų perėjimą**, kad jį pasirinkite
4. Atsivers **spalvų pasirinkimo langas**

5. Pasirinkite naują spalvą naudodami:
   * **Spalvų ratą**: vizualus spalvų pasirinkimas
   * **RGB/HSV slankikliai**: tikslus spalvų valdymas
   * **Šešioliktainio kodo įvedimas**: tiksli spalvos specifikacija (pvz., `#FF0000` raudonai)
6. Spustelėkite už spalvų pasirinkimo lango ribų, **kad pritaikytumėte naują spalvą**

7. Gradientas**atnaujinamas iš karto** vaizde**Spalvų sustojimų pridėjimas arba pašalinimas:*** **Pridėti sustojimą**: Spustelėkite piktogramą „+“, kad pabaigoje pridėtumėte naują spalvų pavyzdį
* **Pašalinti sustojimą**: Dukart spustelėkite spalvų kvadratą, kad pašalintumėte spalvų pavyzdį**Pritaikymo strategijos:*** **Gradiento apgręžimas**: Apverskite spalvų tvarką, kad pakeistumėte reikšmę (pvz., žalia = žema, raudona = aukšta)
* **Prekės ženklo spalvos**: suderinkite su savo organizacijos spalvų palete ataskaitoms
* **Tinkama spalvų akliesiems**: naudokite oranžinės-mėlynos arba violetinės-geltonos spalvų derinius
* **Spausdinimo optimizavimas**: pasirinkite spalvas, kurios tinka tiek spalvotam, tiek pilkosios skalės spausdinimui
* **Daugialygis**: naudokite skirtingas spalvas tam tikroms vertės riboms klasifikuoti

{% hint style="info" %}
**Pasirinktinių gradientų išsaugojimas**: Pasirinktinius gradientus galima išsaugoti ir naudoti pakartotinai. Spustelėkite išsaugojimo piktogramą LUT skydelyje, kad išsaugotumėte savo pasirinktines spalvų schemas ateityje.
{% endhint %}

***

## Interaktyvus darbo srautas

### Atnaujinimai realiuoju laiku

Visi LUT koregavimai „sandbox“ aplinkoje atnaujina vaizdą **akimirksniu ir interaktyviai**:

* **Perkelkite sluoksnį** → Vaizdas pasikeičia iš karto
* **Pasirinkite gradientą** → Spalvos atnaujinamos akimirksniu
* **Koreguokite verčių diapazoną** → Kontrastas keičiasi realiuoju laiku
* **Keiskite klases** → Gradiento sklandumas atnaujinamas iš karto
* **Modifikuokite apkarpymą** → Fono rodymas pasikeičia akimirksniu
* **Redaguokite spalvas** → Pasirinktoji spalvų gama pritaikoma iš karto**Nereikia „Taikyti“ mygtuko** – visi pakeitimai vyksta tiesiogiai ir interaktyviai!

{% hint style="success" %}
**Tiesioginis grįžtamasis ryšys**: Tiesioginis vizualus grįžtamasis ryšys leidžia greitai eksperimentuoti su įvairiais nustatymais, kol rasite optimalų vaizdą, atitinkantį jūsų analizės poreikius.
{% endhint %}

### Pakartotinio tobulinimo darbo eiga

**Tipinė LUT optimizavimo darbo eiga:**

1.**Pasirinkite indeksinį sluoksnį** (pvz., RAW (atspindys))
2. **Taikykite indeksą** – pasirinkite kameros filtrą ir indekso formulę, vilkite spalvotus apskritimus į tinkamą vietą indekso formulėje
3. **Taikykite LUT gradientą** – pradėkite nuo Red-Yellow-Green nustatymų
4. **Patikrinkite pikselių vertes** – judinkite žymeklį, atkreipkite dėmesį į verčių intervalus
5. **Nustatykite min./maks.** – susiaurinkite, kad sutelktumėte dėmesį į augmeniją (pvz., nuo 0,2 iki 0,9)
6. **Pasirinkite apkarpymą** – išbandykite „Original Background“ (Originalus fonas) kontekstui
7. **Patobulinkite spalvas** – prireikus pritaikykite gradientą, jei norite pabrėžti tam tikrus elementus
8. **Užbaigkite nustatymus**– užfiksuokite nustatymus ir nukopijuokite į „Project Settings“ (Projekto nustatymus) eksporto apdorojimui

### Pikselių verčių tikrinimas

Faktinių pikselių verčių supratimas yra labai svarbus nustatant veiksmingus LUT intervalus:**Kaip tikrinti vertes:**

1. Pikselių vertės rodomos, kai vaizde yra pažymėtas langelis „Indeksas“ arba abu langeliai „Indeksas“ ir „LUT“.
2. **Perkelkite žymeklį** per skirtingas vaizdo sritis
3. **Stebėkite pikselių vertes**, rodomas legendoje, kai užvedate žymeklį
4. Padidinkite vaizdą, kad pamatytumėte atskirus pikselius, pažymėtus plaukiojančia verte
5. **Užsirašykite** skirtingų objektų verčių diapazonus:
   * **Sveika augmenija**: pvz., NDVI 0,55–0,85
   * **Stresą patyrusi augmenija**: pvz., NDVI 0,30–0,50
   * **Plika dirva**: pvz., NDVI 0,05–0,25
   * **Vanduo** (jei yra): pvz., NDVI -0,05–0,10**Pikselių verčių naudojimas LUT intervalams nustatyti:**Išnagrinėję pikselių vertes, atitinkamai pakoreguokite LUT minimalią ir maksimalią vertes:**Pavyzdinis scenarijus:*** **Stebėjimas**: Dirvos vertės = 0,05–0,25, Stresuota = 0,25–0,50, Sveika = 0,50–0,85
* **Tikslas**: Vizualizuoti tik augalų sveikatos būklę (neįtraukiant dirvos)
* **LUT nustatymai**: Min = `0.25`, Max = `0.85`
* **Apkarpymas**: „Originalus fonas“, kad dirvožemis būtų matomas natūralia spalva
* **Rezultatas**: Spalvų gradientas taikomas tik augmenijai, dirvožemis rodomas kaip originalus vaizdas

{% hint style="info" %}
**Dinaminis diapazonas**: Skirtingiems pasėliams, sezonams ir augimo etapams bus taikomi skirtingi verčių diapazonai. Prieš nustatydami LUT diapazonus, visada patikrinkite pikselių vertes savo konkrečiame duomenų rinkinyje.
{% endhint %}

***

## Pasirinktiniai indeksai (Chloros+)

### Pasirinktinių indeksų formulių kūrimas

{% hint style="info" %}
**Kur kurti**: Pasirinktinius indeksus galima konfigūruoti**Projekto nustatymuose** prieš apdorojimą, taip pat vaizdo peržiūros programos „sandbox“ šoninėje juostoje.
{% endhint %}

**Norėdami sukurti pasirinktinį indeksą:**

1.**Atidarykite projekto nustatymus** (prieš apdorojimą) arba vaizdų peržiūros programos „sandbox“ šoninę juostą
2. Pereikite prie **indekso formulės išskleidžiamojo meniu**

3. Ieškokite parinkties**„Custom“** (turite būti prisijungę su Chloros+ licencija)
4. **Nustatykite formulę** naudodami juostų kintamuosius:
   * Juostų pavadinimai: `NIR`, `Red`, `Green`, `Blue`, `RedEdge` ir t. t.
   * Operatoriai: `+`, `-`, `*`, `/`, `^` (eksponentas)
   * Funkcijos: `sqrt()`, `abs()` ir kt. (jei palaikoma)
   * Skliausteliai: `()` operacijų eiliškumui
5. **Pavadinkite savo indeksą** (pvz., „MyIndex“ arba „CustomNDVI“)
6. **Išsaugokite konfigūraciją**

**Pavyzdinės pasirinktinės formulės:**

```

Modified NDVI with offset:
(NIR - Red) / (NIR + Red + 0.5)

Simple ratio:
NIR / Red

Complex multi-band:
(NIR - Red) / (NIR + Red - Blue)

Exponential index:
(NIR / Red) ^ 2
```

{% hint style="warning" %}
**Formulės patvirtinimas**: Įsitikinkite, kad jūsų formulė naudoja jūsų kameroje prieinamus juostų diapazonus. Pavyzdžiui, RedEdge yra prieinama tik kamerose su RedEdge filtru.
{% endhint %}

***

## Tolimesni veiksmai

Dabar, kai supratote indeksų / LUT bandymų aplinką:

* **Taikykite apdorojimui**: naudokite nustatytus parametrus [Projekto nustatymuose](../project-settings/project-settings.md)
* **Apdorokite partiją**: pritaikykite optimizuotus indeksus visam duomenų rinkiniui
* **Sužinokite daugiau**: Perskaitykite [Daugiaspektrinių indeksų formulės](../project-settings/multispectral-index-formulas.md)

Susijusi dokumentacija:

* [**Vaizdo sluoksniai**](image-layers.md) - Sluoksnių valdymas ir vizualizavimas
* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) – Vaizdo peržiūros programos pagrindai
* [**Vaizdų apdorojimas (GUI)**](../processing-images-gui/adding-files-to-a-project.md) – Visas apdorojimo darbo srautas
