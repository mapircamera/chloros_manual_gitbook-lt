# Indeksas/LUT smėlio dėžė

Indeksas/LUT smėlio dėžė yra interaktyvi darbo erdvė Chloros vaizdų peržiūros programoje, kurioje galite eksperimentuoti su daugiaspektrinių indeksų skaičiavimais ir spalvų vizualizacijomis realiuoju laiku. Šis galingas įrankis padeda išbandyti skirtingus indeksus, patobulinti verčių diapazonus ir sukurti publikavimui parengtas vizualizacijas be viso duomenų rinkinio perdirbimo.

## Kas yra indeksas/LUT smėlio dėžė?

### Tikslas

Smėlio dėžė suteikia:

* **Indekso skaičiavimą realiuoju laiku** – bet kokį augmenijos indeksą galima taikyti iš karto.
* **Interaktyvų LUT koregavimą** – spalvų gradientų ir diapazonų tikslinimą.
* **Darbo eigos optimizavimą** – geriausių nustatymų nustatymą prieš partijos apdorojimą.

### Sandbox ir projektų apdorojimas

**Index/LUT Sandbox (interaktyvus):**

* Vienas vaizdas vienu metu
* Momentinis grįžtamasis ryšys
* Eksperimentinis ir iteracinis
* Nėra nuolatinių failų pakeitimų
* Puikiai tinka tyrinėjimui ir bandymui

**Projektų apdorojimas (partijos):**

* Visas duomenų rinkinys vienu metu
* Iš anksto sukonfigūruoti nustatymai
* Nuolatiniai išvesties failai
* Laiko intensyvus
* Geriausias, kai nustatymai yra galutiniai

{% hint style=&quot;success&quot; %}
**Geriausias darbo srautas**: naudokite „Sandbox“, kad eksperimentuotumėte ir rastumėte optimalius indeksų ir LUT nustatymus, tada taikyti tuos nustatymus projekto apdorojimo metu visam duomenų rinkiniui.
{% endhint %}

***

## Darbas su indeksų/LUT „Sandbox“

### Iš anksto apskaičiuotų indeksų supratimas

Chloros indeksai gali būti taikomi projekto apdorojimo metu. Norint nustatyti, kokius indeksų ir LUT nustatymus norite taikyti eksportui, paprasčiausia naudoti vaizdų peržiūros sandbox.

Sandbox leidžia:

* **Taikyti naujus indeksus ir spalvų gradientus (LUT)**, kad vizualizuotumėte duomenis.
* **Interaktyviai reguliuoti vizualizacijos nustatymus**.
* **Peržiūrėti** jau apskaičiuotus indeksų vaizdus.
* **Tikrinti** pikselių vertes visais mastelio lygiuose.

### Sandbox atidarymas

Indeksų/LUT sandbox pasiekiamas **vaizdo peržiūros programos** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> šoninės juostos skirtuke:

1. Spustelėkite vaizdą failų naršyklės vaizdų tinklelyje, jis atsidarys **vaizdų peržiūros programos** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> skirtuke
2. Spustelėkite **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> , kad atidarytumėte kairėje pusėje esantį iššokantį šoninį meniu, jei jis dar nėra atidarytas

### Vaizdo pasirinkimas, kuriam bus taikomas indeksas/LUT

Norėdami dirbti su indeksu Image Viewer <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sandbox:

1. **Atidarykite vaizdą** iš pagrindinio vaizdų tinklelio, spustelėdami jį.
2. Tuomet atsidarys **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kortelė
3. Spustelėkite **Sluoksnio išskleidžiamąjį meniu** (dešinėje peržiūros lango viršuje)
4. Išskleidžiamajame meniu pasirinkite sluoksnį:
   * RAW (atspindys)

### Indekso taikymas vaizdui

Kai vaizdas rodomas visame ekrane ir **Vaizdo peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> atidarytas:

1. Pažymėkite langelį „Indeksas“ šoninės juostos viršuje.
2. Iš kairėje esančio išskleidžiamojo meniu pasirinkite savo fotoaparato filtrą.
3. Iš dešinėje esančio išskleidžiamojo meniu pasirinkite norimą indekso formulę.
4. Filtro kanalo spalvų apskritimus perkelkite į indekso formulės vietas žemiau.
5. Kai formulė bus galiojanti, vaizdas bus atnaujintas ir parodys indekso vertes.
6. Pajudinkite pelės žymeklį, kad pamatytumėte vertes žymeklio vietoje.
7. Padidinkite vaizdą, kad pamatytumėte atskirus pikselius ir su jais susijusias vertes.

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

* **Įvestis**: indekso pikselio vertė (pvz., NDVI 0,65)
* **Išvestis**: RGB spalva (pvz., ryškiai žalia)
* **Tikslas**: Padaryti modelius lengviau matomus ir interpretuojamus**Pilkosios skalės ir spalvų LUT:**

* Pilkosios skalės: Mokslinės ir neutralios, rodo neapdorotus duomenis
* Spalvų LUT: Intuityvios ir įspūdingos, išryškina modelius ir skirtumus

{% hint style=&quot;success&quot; %}
**Vizualizacijos galia**: spalvų LUT taikymas pilkosios skalės indeksiniam vaizdui leidžia žymiai lengviau iš pirmo žvilgsnio atpažinti modelius, anomalijas ir dominančias sritis.
{% endhint %}

### LUT taikymas indeksiniam vaizdui

Kai turite indeksinį vaizdą, rodantį

1. Spustelėkite <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> „+Pridėti LUT“ mygtuką
2. Pasirinkite spalvų gradientą
3. Nustatykite apkarpymo minimalius/maksimalius galinius taškus
4. Nustatykite apkarpymo režimą
5. Pažymėkite langelį „Indeksas“ **vaizdo peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> skirtuke, kad pritaikytumėte LUT.

### Spalvų gradiento pasirinkimas

**Gradiento pasirinkimas:**

1. LUT skydelyje raskite**spalvotą gradiento juostą**

2. Užveskite pelę ant jo, kad peržiūrėtumėte galimus gradientų nustatymus
3. Pasirinkite norimą gradientą
4. Vaizdas **iš karto atnaujinamas** naujomis spalvomis, kai pažymėtas langelis „Indeksas“

{% hint style=&quot;success&quot; %}
**Geriausia praktika**: augmenijos indeksams, pvz., NDVI, labiausiai intuityvus yra Red-Yellow-Green gradientas, nes jis atitinka natūralias spalvų asociacijas (žalia = sveika, geltona = vidutinė, raudona = streso paveikta).
{% endhint %}

### Spalvų klasių reguliavimas

**Klasės kontrolė**nustato, kiek atskirų spalvų pakopų bus jūsų gradiente:**Klasės skaičiaus parinktys:*** **2–5 klasės**: labai plačios kategorijos, aiškios zonos
* **6–10 klasių**: subalansuotos, tinkamos klasifikavimui
* **11–20 klasių**: sklandūs gradientai, ištisinis vaizdas
* **20+ klasių**: beveik ištisinis, maksimalus sklandumas**Kaip reguliuoti:**

1. LUT skydelyje raskite**spalvų pavyzdžių kvadratėlius po gradientų juosta**

2. Nustatykite klasių skaičių, spausdami mygtuką „+“.
3. Pašalinkite klasių skaičių, dukart spustelėdami spalvų pavyzdį.
4. Gradientas atnaujinamas **realaus laiko** režimu vaizde.**Poveikis vizualizacijai:*** **Mažiau klasių** (3–5): sukuria aiškias zonas, supaprastina klasifikavimą, lengviau atskirti kategorijas.
* **Vidutinis klasių skaičius** (6–10): subalansuotas požiūris, tinkamas daugumai taikymų.
* **Daug klasių** (15–20): sklandūs perėjimai, išsamūs pokyčiai, fotografijos išvaizda.**Kada naudoti:*** **Nedaug klasių (3–5)**: prezentacijų skaidrės, klasifikavimo žemėlapiai, paprastos ataskaitos.
* **Vidutinis klasių skaičius (6–10)**: bendra analizė, subalansuoti detalės, standartinės ataskaitos.
* **Daug klasių (15–20)**: mokslinė analizė, išsamus tikrinimas, leidinių kokybės rezultatai

### Vertės diapazonų tikslinimas

**Vertės diapazono valdikliai**nustato, kurios indekso vertės atitinka kokias spalvas jūsų gradiente:**Diapazono valdikliai LUT skydelyje:*** **Minimali vertė**: spalvų skalės apatinė riba
* **Maksimali vertė**: spalvų skalės viršutinė riba
* **Tarpinės vertės**: automatiškai paskirstomos tarp minimalios ir maksimalios vertės (pagal klasės skaičių)

#### Minimalaus/maksimalaus verčių reguliavimas

**Vertės intervalų reguliavimas:**

1. LUT skydelyje raskite įvesties laukelius**Minimali vertė**ir**Maksimali vertė**

2. Spustelėkite laukelį**Minimalus dydis**

3. Įveskite norimą minimalų dydį (pvz., `0.2`)
4. Paspauskite **Enter** arba spustelėkite už laukelio ribų
5. Pakartokite tą patį su laukeliu **Maksimalus dydis** (pvz., `0.9`)
6. Vizualizacija **atnaujinama iš karto**{% hint style=&quot;info&quot; %}**Automatinis mastelio keitimas**: Kai pirmą kartą taikote LUT, Chloros automatiškai nustato minimalią/maksimalią vertę pagal faktinį duomenų diapazoną vaizde. Tada galite susiaurinti šį diapazoną, kad sutelktumėte dėmesį į konkrečius jus dominančius verčių diapazonus.
{% endhint %}

**Pavyzdys NDVI diapazono koregavimas:*** **Visas diapazonas**: `-1.0` iki `1.0` (rodo visas galimas vertes)
* **Sutelktas į augmeniją**: `0.2` iki `0.9` (neįtraukia pliko dirvožemio ir vandens)
* **Tik sveika augmenija**: nuo `0.5` iki `0.9` (paryškinti tik stiprius augalus)
* **Streso aptikimas**: nuo `0.2` iki `0.5` (pabrėžkite problemines sritis)
* **Pasirinktinis diapazonas**: reguliuokite pagal stebėtus pikselių vertes**Kodėl reikia reguliuoti diapazonus?*** **Padidinkite kontrastą** jus dominančioje srityje
* **Išskirkite nereikšmingas vertes** (pvz., vandens telkinius, pliką dirvožemį)
* **Standartizuokite vizualizaciją** keliuose vaizduose ar datose
* **Pabrėžkite subtilius skirtumus** siaurame verčių diapazone

### Ribojimas verčių, kurios yra už diapazono ribų

Kai pikselių vertės yra už jūsų nustatyto minimalaus/maksimalaus diapazono ribų, galite kontroliuoti, kaip jos rodomos, naudodami **ribojimo režimus**.

#### **Galimi ribojimo režimo variantai:**

#### 1. Minimalus ir maksimalus

* Pikseliai, **mažesni už minimalų**→ rodomi naudojant**pirmąją spalvą** gradiente (pvz., raudoną)
* Pikseliai **virš maksimumo**→ rodomi naudojant**paskutinę spalvą** gradiente (pvz., žalia)
* **Naudojimo atvejis**: pabrėžti kraštutines vertes, rodyti visą duomenų intervalą su prisotintomis spalvomis ribose
* **Pavyzdys**: NDVI vertės, mažesnės nei 0,2, rodomos raudonai, vertės, didesnės nei 0,9, rodomos žaliai

#### 2. Skaidrus fonas

* Pikseliai **už diapazono ribų**tampa**visiškai skaidrūs*** Tik pikseliai **diapazone** rodo spalvų gradientą
* **Naudojimo atvejis**: GIS perdanga, izoliuojant konkrečius verčių diapazonus, pabrėžiant tik dominančias sritis
* **Pavyzdys**: Rodyti tik NDVI 0,4–0,7 spalvą, visa kita – skaidru

{% hint style=&quot;warning&quot; %}
**Skaidrumo apribojimas**: Skaidrūs pikseliai žiūrovui bus rodomi kaip fono spalva. Eksportuojant apdorojimo metu, skaidrumas išlieka PNG formate, bet ne JPG.
{% endhint %}

#### 3. Indekso fonas

* Pikseliai **už ribų**rodomi**pilkos spalvos** (rodo neapdorotus indekso vertes)
* Pikseliai **ribose**rodomi**spalvų gradientu*** **Naudojimo atvejis**: subtilus paryškinimas, konteksto išlaikymas, pabrėžiant dominančias sritis
* **Pavyzdys**: spalvomis paryškinti pabrėžti augmenija (NDVI 0,3–0,5), o sveikos sritys rodomos pilka spalva

#### 4. Originalus fonas

* Pikseliai, esantys **už ribų**, rodomi**originalioje daugiaspektrinėje nuotraukoje*** Pikseliai **intervalo ribose**rodo**spalvų gradientą*** **Naudojimo atvejis**: labiausiai intuityvus – derina natūralų vaizdo kontekstą su analitiniu spalvų sluoksniu
* **Pavyzdys**: matykite tikrąjį lauko/pasėlių išvaizdą su spalvomis pažymėtomis stresą patiriančiomis sritimis

### Tinkamo apkarpymo režimo pasirinkimas

| Apkarpymo režimas              | Tinkamiausias                                   | Vizualizacijos stilius          |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| **Minimalus ir maksimalus**    | Visų duomenų rodymas, mokslinė analizė     | Visi pikseliai spalvoti           |
| **Skaidrus fonas** | GIS perdangos, izoliuojantys konkrečius diapazonus    | Spalva diapazone, tuščia už jo ribų |
| **Indekso fonas**       | Subtilus pabrėžimas, išlaikant duomenų kontekstą  | Spalva diapazone, pilka už jo ribų  |
| **Originalus fonas**    | Ataskaitos, pristatymai, intuityvi analizė | Spalva diapazone, nuotrauka už jo ribų |

### Individualizuotų LUT spalvų kūrimas

Norėdami visiškai kontroliuoti vizualizaciją, galite kurti **individualizuotus spalvų gradientus**, redaguodami atskirus spalvų perėjimus.**Norėdami sukurti individualizuotą gradientą:**

1. LUT skydelyje suraskite**gradiento peržiūros juostą**.
2. Paieškokite **spalvų pavyzdžių kvadratėlius** po gradientu.
3. **Spustelėkite spalvų perėjimą**, kad jį pasirinkite.
4. Atsidarys **spalvų pasirinkimo langas**.
5. Pasirinkite naują spalvą naudodami:
   * **Spalvų ratą**: vizualus spalvų pasirinkimas.
   * **RGB/HSV slankiklius**: tikslus spalvų valdymas.
   * **Šešioliktainio kodo įvedimą**: tiksli spalvų specifikacija (pvz., `#FF0000` raudonai).
6. Spustelėkite spalvų pasirinkimo langą **, kad pritaikytumėte naują spalvą**

7. Gradientas**iš karto atnaujinamas** paveikslėlyje**Spalvų sustojimų pridėjimas arba pašalinimas:*** **Pridėti sustojimą**: spustelėkite piktogramą „+“, kad pridėtumėte naują pavyzdį gale
* **Pašalinti sustojimą**: dukart spustelėkite spalvų kvadratą, kad pašalintumėte pavyzdį**Tinkinimo strategijos:*** **Invertuoti gradientą**: apverskite spalvų tvarką, kad pakeistumėte reikšmę (pvz., žalia = žema, raudona = aukšta)
* **Prekės ženklo spalvos**: suderinkite savo organizacijos spalvų paletę ataskaitoms
* **Tinkamas spalvų akliesiems**: naudokite oranžinės-mėlynos arba violetinės-geltonos spalvų derinius
* **Spausdinimo optimizavimas**: rinkitės spalvas, kurios tinka tiek spalvotam, tiek pilkam spausdinimui
* **Daugiau slenksčių**: naudokite skirtingas spalvas tam tikroms vertės riboms klasifikuoti

{% hint style=&quot;info&quot; %}
**Pasirinktinių gradientų išsaugojimas**: Pasirinktinius gradientus galima išsaugoti ir pakartotinai naudoti. Spustelėkite išsaugojimo piktogramą LUT skydelyje, kad išsaugotumėte pasirinktines spalvų schemas ateities naudojimui.
{% endhint %}

***

## Interaktyvus darbo srautas

### Atnaujinimai realiuoju laiku

Visi LUT koregavimai smėlio dėžėje atnaujina vaizdą **akimirksniu ir interaktyviai**:

* **Perjungti sluoksnį** → Vaizdas keičiasi iš karto
* **Pasirinkti gradientą** → Spalvos atnaujinamos akimirksniu
* **Koreguoti verčių diapazoną** → Kontrastas keičiasi realiuoju laiku
* **Keisti klases** → Gradiento sklandumas atnaujinamas iš karto
* **Modifikuoti apkarpymą** → Fono rodymas keičiasi akimirksniu
* **Redaguoti spalvas** → Pasirinktinis gradientas taikomas iš karto**Nereikia „Taikyti“ mygtuko** – visi pakeitimai yra tiesioginiai ir interaktyvūs!

{% hint style=&quot;success&quot; %}
**Tiesioginis grįžtamasis ryšys**: Tiesioginis vizualinis grįžtamasis ryšys leidžia greitai eksperimentuoti su įvairiais nustatymais, kol rasite optimalų vizualizavimą savo analizės poreikiams.
{% endhint %}

### Iteracinis tobulinimo darbo srautas

**Tipinis LUT optimizavimo darbo srautas:**

1.**Pasirinkite indeksų sluoksnį** (pvz., RAW (atspindys))
2. **Taikykite indeksą** – pasirinkite kameros filtrą ir indekso formulę, spalvotus apskritimus perkelkite į tinkamą vietą indekso formulėje
3. **Taikykite LUT gradientą** – pradėkite nuo Red-Yellow-Green nustatymų
4. **Patikrinkite pikselių vertes** – perkelkite žymeklį, atkreipkite dėmesį į verčių diapazonus
5. **Nustatykite min/maks** – susiaurinkite, kad sutelktumėte dėmesį į augmeniją (pvz., nuo 0,2 iki 0,9)
6. **Pasirinkite apkarpymą** – išbandykite „Original Background“ (Originalus fonas) kontekstui
7. **Patobulinkite spalvas** – prireikus pritaikykite gradientą, kad pabrėžtumėte tam tikrus elementus
8. **Užbaigti nustatymus**– užfiksuokite nustatymus ir nukopijuokite į projekto nustatymus eksporto apdorojimui

### Pikselių verčių tikrinimas

Faktinių pikselių verčių supratimas yra labai svarbus norint nustatyti veiksmingus LUT intervalus:**Kaip tikrinti vertes:**

1. Pikselių vertės rodomos, kai vaizde yra pažymėtas indeksas arba indeksas ir LUT**langeliai.**

2.**Pereikite pelės žymekliu** per skirtingas vaizdo sritis
3. **Stebėkite pikselių vertes**, rodomas legendoje, kai pelės žymeklis yra virš jų
4. Padidinkite vaizdą, kad matytumėte atskirus pikselius, pažymėtus plaukiojančia verte
5. **Užsirašykite** skirtingų savybių verčių diapazonus:
   * **Sveika augmenija**: pvz., NDVI 0,55–0,85
   * **Stresą patirianti augmenija**: pvz., NDVI 0,30–0,50
   * **Plikas dirvožemis**: pvz., NDVI 0,05–0,25
   * **Vanduo** (jei yra): pvz., NDVI -0,05 iki 0,10**Pikselių verčių naudojimas LUT diapazonams nustatyti:**Patikrinę pikselių vertes, atitinkamai sureguliuokite LUT min/maks:**Pavyzdinis scenarijus:*** **Stebėjimas**: Dirvožemio vertės = 0,05–0,25, Stresas = 0,25–0,50, Sveikata = 0,50–0,85
* **Tikslas**: vizualizuoti tik augalų sveikatą (neįtraukiant dirvožemio)
* **LUT nustatymai**: Min = `0.25`, Maks = `0.85`
* **Apkarpymas**: „Originalus fonas“, kad dirvožemis būtų matomas natūralia spalva
* **Rezultatas**: spalvų gradientas taikomas tik augmenijai, dirvožemis rodomas kaip originalus vaizdas

{% hint style=&quot;info&quot; %}
**Dinaminis diapazonas**: skirtingiems pasėliams, sezonams ir augimo etapams bus taikomi skirtingi verčių diapazonai. Prieš nustatydami LUT diapazonus, visada patikrinkite pikselių vertes savo konkrečiame duomenų rinkinyje.
{% endhint %}

***

## Pasirinktiniai indeksai (Chloros+)

### Pasirinktinių indeksų formulių kūrimas

{% hint style=&quot;info&quot; %}
**Kur kurti**: Pasirinktinius indeksus galima konfigūruoti**Projekto nustatymuose** prieš apdorojimą, taip pat vaizdo peržiūros programos smėlio dėžės šoninėje juostoje.
{% endhint %}

**Norėdami sukurti pasirinktinį indeksą:**

1.**Atidarykite projekto nustatymus** (prieš apdorojimą) arba vaizdo peržiūros programos smėlio dėžės šoninę juostą.
2. Pereikite prie **indekso formulės išskleidžiamojo meniu**.
3. Ieškokite **„Pasirinktinis“** parinkties (būtina prisijungti su Chloros+ licencija).
4. **Nustatykite formulę** naudodami juostos kintamuosius:
   * Juostos pavadinimai: `NIR`, `Red`, `Green`, `Blue`, `RedEdge` ir kt.
   * Operatoriai: `+`, `-`, `*`, `/`, `^` (eksponentas)
   * Funkcijos: `sqrt()`, `abs()` ir kt. (jei palaikoma)
   * Skliausteliai: `()` operacijų tvarkai
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

{% hint style=&quot;warning&quot; %}
**Formulės patvirtinimas**: įsitikinkite, kad jūsų formulė naudoja jūsų fotoaparate esančias juostas. Pavyzdžiui, RedEdge yra prieinama tik kamerose su RedEdge filtru.
{% endhint %}

***

## Tolimesni veiksmai

Dabar, kai supratote indeksų / LUT smėlio dėžę:

* **Taikyti apdorojimui**: naudokite nustatytus parametrus [Projekto nustatymai](../project-settings/project-settings.md)
* **Paketinis apdorojimas**: optimizuotus indeksus taikykite visam duomenų rinkiniui
* **Daugiau informacijos**: skaitykite [Daugiaspektrinių indeksų formulės](../project-settings/multispectral-index-formulas.md)

Susijusi dokumentacija:

* [**Vaizdo sluoksniai**](image-layers.md) – sluoksnių valdymas ir vizualizavimas
* [**Vaizdo atidarymas per visą ekraną**](opening-an-image-full-screen.md) – vaizdo peržiūros pagrindai
* [**Vaizdų apdorojimas (GUI)**](../processing-images-gui/adding-files-to-a-project.md) – visas apdorojimo darbo srautas
