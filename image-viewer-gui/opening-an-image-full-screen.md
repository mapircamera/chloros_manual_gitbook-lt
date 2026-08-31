# Vaizdo atidarymas visame ekrane

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption><p>Visame ekrane atidarytas vaizdas, dešinėje viršuje esantis sluoksnių pasirinkimo meniu</p></figcaption></figure>

„Chloros“ vaizdų peržiūros programa yra visą ekraną užimanti sąsaja, skirta vaizdams peržiūrėti, tikrinti ir matuoti. Čia galite matyti **tikrąsias pikselių vertes** — kiekvieno kanalo DN, atspindžio procentą arba spinduliavimą W/m²/sr/nm — o ne iškraipytą peržiūros vaizdą, kurį rodo ekranas.

## Kaip pasiekti vaizdų peržiūros programą

### Iš failų naršyklės

1. Atidarykite skirtuką **Failų naršyklė** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Spustelėkite bet kurią **miniatiūrą** [vaizdų lentelėje](image-grid.md)
3. Vaizdas atsidarys visame ekrane skirtuke **Vaizdų peržiūros programa**

Vaizdas atsidarys tame produkte, kurį rodė lentelė. Jei tinklelis nustatytas kaip `RAW (Reflectance)`, tai bus tas sluoksnis, kuriame atsidursite.

### Vaizdo peržiūros programos šoninės juostos atidarymas

Spustelėkite **Vaizdo peržiūros programa** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> piktogramą kairėje šoninėje juostoje, kad išslystų analizės skydelis. Jame iš viršaus į apačią yra:

* vaizdo pavadinimas ir jo kameros modelis
* mygtukas **Eksportuoti/Išsaugoti vaizdus** (veikia tik tada, kai aktyvus indeksas arba LUT)
* žymimieji langeliai **Indeksas**ir**LUT** bei indekso konfigūracijos skydelis — žr. [Indekso/LUT bandymų aplinka](index-lut-sandbox.md)
* **Kursoriaus reikšmės** skydelis: duomenų rodymas pagal kanalus, sluoksnio histograma ir GSD reguliatorius***

## Navigacija ir mastelio keitimas

### Vaizdų peržiūra

* **Kitas vaizdas**: mygtukas → arba klavišas**→** (dešinė rodyklė)
* **Ankstesnis vaizdas**: mygtukas ← arba klavišas**←** (kairė rodyklė)
* **Perėjimas prie konkretaus vaizdo**: grįžkite į tinklelį ir spustelėkite jo miniatiūrą

Mastelio keitimas ir peržiūros poslinkis išlieka, kai pereinate iš vieno vaizdo į kitą, todėl galite peržiūrėti visą rinkinį, likdami toje pačioje kadro dalyje.

### Mastelio keitimas

Mastelį keičiate **pelės ratuku**, 15 % žingsniais, fiksuojant pagal žymeklį — taškas po žymekliu lieka po žymekliu. Diapazonas ribojamas vaizdo ir lango dydžio: negalima sumažinti vaizdo labiau nei „pritaikyti prie lango“, o viršutinė riba nustatoma pagal vaizdo natūralią skiriamąją gebą.

Pilno ekrano peržiūroje nėra specialių priartinimo klavišų. (Tinklelyje **Ctrl + `+` / `−`** keičia miniatiūrų dydį — tai kitoks valdymo būdas.)

### Peržiūros perkelimas priartinus

Spustelėkite ir laikykite nuspaudę kairįjį pelės mygtuką ant vaizdo ir vilkite. Perkelimas ribojamas, todėl vaizdo negalima išvilkti už ekrano ribų.

### Vaizdo tikrinimas pikselio tikslumu esant dideliam masteliui

Kai faktinis didinimas viršija **60×**, Chloros nubrėžia paryškinimo langelį aplink atskirą rodomą pikselį po žymekliu ir šalia jo rodo plaukiojančią reikšmę.

„Efektyvusis“ didinimas skaičiuojamas pagal GSD bloko dydį: esant 8 bloko dydžiui, paryškinimas atsiranda esant 7,5×, o ne 60× didinimui, nes vienas rodomas pikselis jau sudaro 8 × 8 šaltinio pikselių. Sumažinkite vaizdą žemiau šio slenksčio, ir paryškinimas išnyks.

### Klavišų kombinacijos

| Klavišas                             | Kur       | Veiksmas                              |
| ------------------------------- | ----------- | ----------------------------------- |
| **→**                           | Visas ekranas | Kitas vaizdas                          |
| **←**                           | Visas ekranas | Ankstesnis vaizdas                      |
| **Ctrl + R**                    | Visas ekranas | Atstatyti indekso/LUT smėlio dėžę         |
| **Ctrl + `+`**/**Ctrl + `=`** | Tinklelis        | Didesnės miniatiūros (4 pikseliai per paspaudimą)  |
| **Ctrl + `−`**                  | Tinklelis        | Mažesnės miniatiūros (4 pikseliai už kiekvieną paspaudimą) |***

## Kursoriaus reikšmės

Pajudinkite kursorių virš paveikslėlio, ir skydelyje **„Kursoriaus reikšmės“** bus rodomos kiekvieno po juo esančio kanalo reikšmės.

{% hint style="success" %}
**Tai yra tikrieji failo skaičiai.** Ekrane rodomas paveikslas yra 8 bitų išplėstas peržiūros vaizdas, kuris negali pateikti šių verčių, todėl Chloros imą iš tikrojo produkto failo, kad galėtų jas parodyti. Štai kodėl 12 bitų neapdorotas kadras rodo vertes, didesnes nei 255, o „float32“ spinduliavimo sluoksnis rodo fizines vienetus.
{% endhint %}

### Stulpelių reikšmės

Skirtukas prisitaiko prie peržiūrimo sluoksnio:

| Peržiūrimas sluoksnis              | Rodomi stulpeliai    | Pastabos                                                                                           |
| ---------------------------------- | ---------------- | ----------------------------------------------------------------------------------------------- |
| Atspindžio koeficientas                        | **DN**ir**%** | Procentinė vertė apskaičiuojama pagal to failo mastelį — žr. toliau                                      |
| Šviesos srautas                           | **W/m²/sr/nm**   | „Float“ tipo fizinės vertės; nėra stulpelio „DN“, nes čia „DN“ neturi prasmės                           |
| Neapdoroti / Debayered / peržiūra / JPG    | **DN**           | Sveikieji skaitmeniniai skaičiai                                                                         |
| 32 bitų atspindžio procentų eksportavimas | **%** tik       | Saugomas plūduriuojančiojo kintamojo skaičius nėra DN, todėl suapvalinus jį iki sveikojo skaičiaus būtų išspausdintas beprasmis `0` arba `1` |

Kiekviena eilutė pažymėta jūsų fotoaparato filtro kanalo pavadinimu — „`Red / Green / NIR`“ atitinka „RGN“, „`Orange / Cyan / NIR`“ atitinka „OCN“, `NIR / Green / Blue` – NGB, `Red / Green / Blue` – RGB, o RE, NIR ir „mono M3M“ kameroms. Kiekviena etiketė pažymėta spalvotu tašku, atitinkančiu indekso formulės redaktoriuje naudojamus kanalų apskritimus.

Išsaugoti **indekso ir LUT** vaizdai yra ypatingas atvejis: juose yra spalvų žemėlapio komponentai, o ne spektriniai juostiniai, todėl jų eilutės pažymėtos kaip „`Red / Green / Blue`“ (arba „`Index`“ vienkanalio indekso failo atveju), o ne kameros filtro pavadinimais.

Kai indeksas yra aktyvus „sandbox“ aplinkoje, po kanalais atsiranda papildoma eilutė, rodanti **indekso vertę** žymeklio vietoje, kartu su indekso pavadinimu ir baltu tašku, atitinkančiu jo žymeklį histogramoje.

### Atspindžio procentas naudoja kiekvieno failo savąją skalę

{% hint style="warning" %}
**Negalima manyti, kad 65535 = 100 %.** Chloros saugo atspindžio procentą skirtingomis skalėmis, priklausomai nuo to, kuri kamera jį sukūrė, o peržiūros programa kiekvienam failui nustato teisingą skalę.
{% endhint %}

| Šaltinis                  | DN, atitinkantis atspindžio koeficientą 1,0 | Kaip jis nustatomas                                                                                                                               |
| ----------------------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **LATTICE**(M3C / M3M) |**32768**                      | XMP žymė „`Chloros:PixelScale=32768`“ įrašoma į kiekvieną „LATTICE“ atspindžio eksportą. 2× atsarga leidžia failui perduoti ρ, didesnį nei 1,0, be apkarpymo |
| **Survey3**|**65535**                      | Nėra Chloros XMP mastelio žymės — Survey3 kalibravimas įrašo ρ × dtype-max ir apriboja vertę 1,0                                                               |

Peržiūros programa, indeksų/LUT bandymo aplinka ir indeksų eksportas mastelį nustato naudodami tą pačią vienintelę įgyvendinimo versiją, todėl vertė, kurią matote žymeklio vietoje, yra ta pati vertė, kurią naudojo indeksų skaičiavimai.

Dvi pasekmės, kurias verta žinoti:

* **32 bitų procentinis**TIFF saugo DN/65535 kaip plūduriuojančiojo kablelio skaičių, o**8 bitų** PNG/JPG eksportas saugo DN × 255/65535 — peržiūros programa abu verčia atgal prieš išspausdindama procentinę vertę.
* Vieno atvejo atkurti neįmanoma: **8-bitis TIFF eksportas iš 8-bitio šaltinio užfiksuoto vaizdo** yra apribojamas iki 0–255, o ne perskaičiuojamas, ir sąmoningai neturi mastelio žymės. Šių failų atveju skydelyje spausdinamas tik DN, be procentų stulpelio. Tai yra teisingas atsakymas, o ne klaida.***

## Sluoksnio histograma

Po žymeklio eilutėmis rodoma tiesioginė peržiūrimo sluoksnio histograma, suskirstyta į **256 intervalus**. Pagal numatytuosius nustatymus ji piešia vieną sujungtą kreivę, svertinę `(R + 2G + B) / 4` — ta pati matavimo erdvė, kurią naudoja „LATTICE“ kameros histogramos. Įjungus**RGB**, ji pakeičiama atskirų kanalų kreivėmis, atvaizduojamomis kanalų spalvomis, kurios yra adityviai sumaišytos, kad sutapimai išliktų įskaitomi. Monokromatiniai sluoksniai visada atvaizduoja vieną kreivę.

Horizontali ašis yra išreikšta sluoksnio vienetais:

| Sluoksnis       | Ašies vienetas  | Ašies maksimumas                                               |
| ----------- | ---------- | ---------------------------------------------------------- |
| Atspindžio koeficientas | procentai    | 125 % — produkto rezervas leidžia ρ didesnį nei 1,0           |
| Spinduliavimas    | W/m²/sr/nm | Kadro maksimali vertė, suapvalinta iki dviejų reikšmingų skaitmenų |
| 8 bitų duomenys | DN         | 255                                                        |
| 12 bitų duomenys | DN         | 4095                                                       |
| 16 bitų duomenys | DN         | 65535                                                      |

Kai ašis yra DN ir pasiekia vieną iš šių trijų viršutinių ribų, Chloros taip pat žino, kokio bitų gylio yra tai, ką žiūrite.

Virš histogramos yra trys mygtukai:

| Mygtukas     | Numatytasis | Poveikis                                                                                                                                                                                                                                                                                                           |
| ---------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **KURSIORIUS** | Įjungta    | Ant histogramos nubrėžia žymeklines linijas tiksliai pagal viršutiniuose eilutėse nurodytas reikšmes, kad galėtumėte matyti, kur kadrų pasiskirstyme yra pikselis po jūsų kursoriumi. RGB režime kiekvienam kanalui yra po vieną žymeklį, pažymėtą atskira spalva; kitais atvejais – vienas baltas žymeklis, atitinkantis bendrą vertę |
| **INDEX**| Įjungta      | Rodoma tik tada, kai indeksas yra aktyvus. Perjungia histogramą iš šaltinio juostų į**indekso reikšmių pasiskirstymą**, kur dvi apribojimo ribos nubrėžiamos kaip oranžinės punktyrinės linijos, o žymeklio indekso reikšmė – kaip balta linija                                                          |
| **RGB**| Išjungta     | Perjungia iš sujungtos kreivės į atskiras kiekvieno kanalo kreives. Naudojant monokromatinį jutiklį, šis mygtukas rodo užrašą**MONO** ir yra išjungtas — rodyti galima tik vieną kanalą                                                                                                                                  |

Histograma skaičiuojama pagal **matomus blokus**, o ne pagal už jų esančius šaltinio pikselius: pakeitus GSD bloko dydį, pasiskirstymas perskaičiuojamas, todėl histograma, žymeklio žymė ir rodomas vaizdas visada sutampa.***

## GSD bloko dydis

Skydo apačioje yra **GSD (px)**valdymo elementas: skaičių laukelis, slankiklis nuo**1 iki 256**ir mygtukas**RESET**.

Jis sumažina _rodomo_ vaizdo raišką, apskaičiuodamas N × N šaltinio pikselių bloko vidurkį į vieną rodomą pikselį. `1` yra natūrali skiriamoji geba.

* Tai veikia **visą ekraną, tinklelio miniatiūras, žymeklio rodmenis ir abu histogramus** — viskas, kas rodo vaizdą, atitinka tą pačią pagrindinę skiriamąją gebą.
* Tai taikoma **tik rodymui**. Apdorojimas ir eksportavimas lieka nepakitę. Vienintelė išimtis yra sąmoningai numatyta: eksportuojant per [Index/LUT Sandbox](index-lut-sandbox.md) išsaugoma tai, ką matote, todėl jame išsaugomas dabartinis bloko dydis, o eksporto skydelyje pasirodo įspėjimas, kai bloko dydis viršija 1.
* Ši reikšmė saugoma **kiekvienam projektui atskirai** kaip `viewer_display.gsd_bin` faile `project.json`, todėl ji išlieka uždarius ir vėl atidarius programą.
* Kursoriaus rodmenys rodo bloko, o ne šaltinio pikselio vertę, kai bloko dydis viršija 1 — rodoma vertė yra bloko, esančio po jūsų kursoriumi, vidurkis.

{% hint style="info" %}
**Kodėl „bloko dydis“, o ne centimetrai vienam pikseliui?** Skaičiui cm/px reikia aukščio virš žemės paviršiaus. Vieno kadro EXIF duomenyse nurodytas GPS aukštis virš vidutinio jūros lygio, o ne virš reljefo, į kurį buvo nukreiptas fotoaparatas, todėl Chloros nerodys atstumo iki žemės, kurio negali tiksliai apskaičiuoti. Bloko dydis šaltinio pikseliais yra tas pats atsarginis sprendimas, kurį naudoja „MAPIR“ debesų įrankiai, kai nežinomas atstumas iki žemės.
{% endhint %}

***

## Vaizdų tipai, kuriuos galite peržiūrėti

Peržiūros lango dešinėje viršutinėje dalyje esantis sluoksnių išskleidžiamasis meniu rodo visas dabartinio vaizdo versijas. Kokie įrašai rodomi, priklauso nuo fotoaparato ir nuo to, kas buvo apdorota — visą sąrašą ir informaciją apie išskleidžiamojo meniu veikimą rasite skyriuje [Vaizdo sluoksniai](image-layers.md).

### Survey3

* **JPG** — pačios kameros peržiūros failas
* **RAW (Original)** — šaltinis `.RAW`, be „bayeringo“ (debayering), be korekcijų
* **RAW (Tikslas)** — kadras, identifikuotas kaip turintis kalibravimo tikslą
* **RAW (Atspindžio koeficientas)** — kalibruotas atspindžio koeficiento produktas (65535 = ρ 1,0)
* **Ištaisyta vinjetė**/**Jutiklio atsakas** — nekalibruotas atsarginis produktas
* **White Balanced** — baltos spalvos balanso koreguotas produktas
* **RAW (`<INDEX>` indeksas)**ir**`<INDEX>` LUT** — apskaičiuoti indeksiniai vaizdai

### LATTICE

„LATTICE“ kadruose naudojamas tas pats išskleidžiamasis meniu su apdorojimo grandinės lygių pavadinimais:

| Sluoksnis                 | Ką jis apima                                                        |
| --------------------- | -------------------------------------------------------------------- |
| **RAW (Originalus)**    | Užfiksuotas pirminis RAW kadras                                     |
| **RAW (Debayered)**   | Linijinis debayered vaizdas                                           |
| **RAW (Peržiūra)**     | Ekrano peržiūra — netikrų spalvų išplėtimas, skirtas daugiaspektrinėms kameroms |
| **Su baltos spalvos balansu**    | Ekrano peržiūra, skirta RGB pagrindinėms kameroms (baltos spalvos balansas + gama)   |
| **RAW (spinduliavimas)**    | „Float32“ spektrinis spinduliavimas, išreikštas W/m²/sr/nm                              |
| **RAW (atspindys)** | „uint16“ atspindys, 32768 = ρ 1,0                                    |

Spinduliavimas ir atspindžio koeficientas yra tik daugiaspektriniai: pagrindinė kamera „RGB“ neturi radiometrijos pagal juostas, todėl šie sluoksniai jai nėra generuojami.

***

## Indeksų ir LUT taikymas

Taikykite daugiaspektrinius indeksus ir spalvų paieškos lenteles (LUT) iš šoninės juostos:

1. Atidarykite **Vaizdo peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> šoninę juostą
2. Pažymėkite **Indeksas**

3. Pasirinkite savo kameros filtrą ir indekso formulę, tada vilkite kanalų apskritimus į formulės langelius
4. Pridėkite LUT ir pasirinkite gradientą, slenksčius bei apkarpymo režimą
5. Peržiūrėkite vertes prie žymeklio ir išsaugokite rezultatą pasirinkdami **Eksportuoti/Išsaugoti vaizdus**Visą instrukciją rasite [Indeksų/LUT bandymų srityje](index-lut-sandbox.md) rasite išsamų vadovą.***

## Problemų sprendimas

### Vaizdas neatsidaro

**Galimos priežastys**: failas buvo perkeliamas arba ištrintas po importavimo; produktas nebuvo įrašytas; nepakanka atminties labai dideliam vaizdui.**Ką daryti**:

1. Patikrinkite, ar sluoksnio failas vis dar yra projekto išvesties medyje
2. Atidarykite failą išorinėje peržiūros programoje, kad įsitikintumėte, jog jis yra nesugadintas
3. Uždarykite kitas programas, kad atlaisvintumėte atminties

### Vaizdas yra juodas, baltas arba labai iškraipytų spalvų

**Galimos priežastys**: ekrano ištempimui nėra su kuo dirbti (beveik pastovus kadras); „float32“ sluoksnis su neįprastomis reikšmėmis; indeksas, kuris nesukūrė galiojančių duomenų.**Ką daryti**:

1. Patikrinkite žymeklio reikšmes — jei kiekvieno kanalo reikšmė yra lygi nuliui arba artima nuliui, problema yra duomenyse, o ne ekrane
2. Patikrinkite histogramą: vienintelis smailas viename gale rodo, kad kadras yra apkarpytas arba tuščias
3. Patikrinkite apdorojimo žurnalą, susijusį su vykdymu, kurio metu buvo sukurtas sluoksnis

### Vertės atrodo neteisingos

**Galimos priežastys**: esate kitame sluoksnyje, nei manote; lyginate procentinę vertę su neapdorotu DN; lyginate „LATTICE“ failą su „Survey3“ failu, naudodami tą patį daliklį.**Ką daryti**:

1. Patikrinkite, ar išskleidžiamajame meniu pasirinktas teisingas sluoksnis – skydelio matavimo vienetai priklauso nuo sluoksnio
2. Atspindžio koeficientui naudokite **%** stulpelį, o ne dalykite DN patys; jei privalote dalyti, naudokite to failo `Chloros:PixelScale` (32768 „LATTICE“ atveju, jei nėra – tai reiškia 65535 „Survey3“ atveju)
3. GSD bloko dydį nustatykite atgal į 1 — jei dydis didesnis nei 1, matote bloko vidurkį, o ne pikselį
4. Patikrinkite, ar atspindžio kalibravimas iš tikrųjų buvo atliktas tam kadrui; nekalibruotas atsarginis produktas („Sensor Response“ / „Vignette Corrected“) nėra atspindys

***

## Tolimesni veiksmai

* [**Vaizdo sluoksniai**](image-layers.md) — kiekvieno sluoksnio pavadinimas (jei toks yra) ir jo reikšmių aprašymas
* [**Indeksų / LUT bandymų erdvė**](index-lut-sandbox.md) — kurkite, derinkite ir eksportuokite indeksų vizualizacijas
* [**Žemėlapio žymekliai**](map-markers.md) — tas pats vaizdų rinkinys žemėlapyje
* [**Daugiaspektrinių indeksų formulės**](../project-settings/multispectral-index-formulas.md) — indeksų žinynas

Apie apdorojimo eigą skaitykite skyriuje [Vaizdų apdorojimas (GUI)](../processing-images-gui/adding-files-to-a-project.md).
