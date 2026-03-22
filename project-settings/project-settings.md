# Projekto nustatymai

Projekto nustatymai <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> šoninėje juostoje Chloros leidžia konfigūruoti visus vaizdo apdorojimo, kalibravimo taškų aptikimo, multispektrinių indeksų skaičiavimo ir eksporto parinktis jūsų projektui. Šie nustatymai yra išsaugomi kartu su projektu ir gali būti išsaugoti kaip šablonai, kuriuos galima pakartotinai naudoti keliuose projektuose.

## Prieiga prie projekto nustatymų

Norėdami pasiekti projekto nustatymus:

1. Atidarykite projektą Chloros
2. Spustelėkite **Projekto nustatymai**  <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> skirtuką kairėje šoninėje juostoje
3. Nustatymų skydelyje bus rodomos visos galimos konfigūracijos parinktys, suskirstytos pagal kategorijas

***

## Taikinio aptikimas

Šie nustatymai kontroliuoja, kaip Chloros aptinka ir apdoroja kalibravimo taikinius jūsų vaizduose.

### Mažiausias kalibravimo mėginio plotas (px)

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 10 000 pikselių
* **Numatytasis**: 25 pikseliai
* **Aprašymas**: Nustato mažiausią plotą (pikseliais), reikalingą, kad aptiktas regionas būtų laikomas galiojančiu kalibravimo tikslo mėginiu. Mažesnės reikšmės aptiks mažesnius tikslus, bet gali padidinti klaidingų teigiamų rezultatų skaičių. Didesnės reikšmės reikalauja didesnių ir aiškesnių tikslų sričių aptikimui.
* **Kada reguliuoti**:
  * Padidinkite, jei gaunate klaidingus aptikimus dėl mažų vaizdo artefaktų
  * Sumažinkite, jei jūsų kalibravimo tikslai vaizduose atrodo maži ir nėra aptinkami

### Minimalus tikslų grupavimas (0–100)

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 100
* **Numatytasis**: 60
* **Aprašymas**: Nustato grupavimo ribą, pagal kurią grupuojami panašios spalvos regionai aptinkant kalibravimo taikinius. Didesnės reikšmės reikalauja, kad būtų sugrupuotos daugiau panašių spalvų, todėl taikinių aptikimas tampa konservatyvesnis. Mažesnės reikšmės leidžia daugiau spalvų variacijų vienoje taikinių grupėje.
* **Kada reguliuoti**:
  * Padidinkite, jei kalibravimo tikslai yra suskaidomi į kelis aptikimus
  * Sumažinkite, jei spalvų variacijų turintys kalibravimo tikslai nėra visiškai aptinkami

***

## Apdorojimas

Šie nustatymai kontroliuoja, kaip Chloros apdoroja ir kalibruoja jūsų vaizdus.

### Vignette korekcija

* **Tipas**: Žymės langelis
* **Numatytasis**: Įjungta (pažymėta)
* **Aprašymas**: Taiko vinjetės korekciją, kad kompensuotų objektyvo tamsėjimą vaizdų kraštuose. Vinjetė yra dažnas optinis reiškinys, kai vaizdo kampai ir kraštai atrodo tamsesni nei centras dėl objektyvo savybių.
* **Kada išjungti**: Išjunkite tik tuo atveju, jei jūsų fotoaparato ir objektyvo derinys jau taiko vinjetės korekciją arba jei norite rankiniu būdu koreguoti vinjetę apdorojimo metu.

### Atspindžio kalibravimas / baltos spalvos balansas

* **Tipas**: Žymės langelis
* **Numatytasis**: Įjungta (pažymėta)
* **Aprašymas**: Įjungia automatinį atspindžio kalibravimą, naudojant vaizduose aptiktus kalibravimo taškus. Tai normalizuoja atspindžio vertes visame duomenų rinkinyje ir užtikrina nuoseklius matavimus nepriklausomai nuo apšvietimo sąlygų.
* **Kada išjungti**: Išjunkite tik tuo atveju, jei norite apdoroti neapdorotus, nekalibruotus vaizdus arba jei naudojate kitą kalibravimo darbo eigą.

### Debayerio metodas

* **Tipas**: Išskleidžiamas meniu
* **Parinktys**:
  * Standartinis (greitas, vidutinė kokybė)
  * Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė) \[Chloros+]
* **Numatytasis**: Standartinis (greitas, vidutinė kokybė)
* **Aprašymas**: Pasirenkamas demosaicingo algoritmas, naudojamas neapdorotų Bayer modelio jutiklio duomenų konvertavimui į spalvotus vaizdus. „Standartinis (greitas, vidutinė kokybė)“ metodas užtikrina optimalų apdorojimo greičio ir vaizdo kokybės balansą. „Tekstūros atpažinimas (lėtas, aukščiausia kokybė)“ \[Chloros+] naudoja aukštos kokybės kraštų atpažinimo demosaicingą, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą demosaicingo triukšmą. Tekstūros atpažinimo modeliui veikti reikalinga GPU atmintis (VRAM). Rekomenduojame jį naudoti, jei turite &gt;4 GB VRAM, kad apdorojimas būtų greitesnis.
* **Pastaba**: Ateityje Chloros versijose gali būti pridėti papildomi debayer metodai.

### Minimalus pakartotinio kalibravimo intervalas

* **Tipas**: Skaičius
* **Diapazonas**: nuo 0 iki 3 600 sekundžių
* **Numatytasis**: 0 sekundžių
* **Aprašymas**: Nustato minimalų laiko intervalą (sekundėmis) tarp kalibravimo taškų naudojimo. Nustačius 0, Chloros naudos kiekvieną aptiktą kalibravimo tašką. Nustačius didesnę vertę, Chloros naudos tik tuos kalibravimo taškus, kurie yra atskirti bent šiuo sekundžių skaičiumi, taip sumažindamas apdorojimo laiką duomenų rinkiniams, kuriuose dažnai fiksuojami kalibravimo taškai.
* **Kada reguliuoti**:
  * Nustatykite 0, kad pasiektumėte maksimalų kalibravimo tikslumą, kai apšvietimo sąlygos kinta
  * Padidinkite (pvz., iki 60–300 sekundžių), kad apdorojimas būtų greitesnis, kai apšvietimas yra pastovus ir turite dažnus kalibravimo taškų vaizdus

### Šviesos jutiklio laiko juostos nuokrypis

* **Tipas**: Skaičius
* **Diapazonas**: nuo -12 iki +12 valandų
* **Numatytasis**: 0 valandų
* **Aprašymas**: Nurodo laiko juostos poslinkį (valandomis nuo UTC) šviesos jutiklio duomenų laiko žymoms. Tai naudojama apdorojant PPK (Post-Processed Kinematic) duomenų failus, siekiant užtikrinti teisingą laiko sinchronizaciją tarp vaizdų užfiksavimo ir GPS duomenų.
* **Kada reguliuoti**: Nustatykite savo vietos laiko juostos poslinkį, jei jūsų PPK duomenys naudoja vietos laiką, o ne UTC. Pavyzdžiui:
  * Ramiojo vandenyno laikas: -8 arba -7 (priklausomai nuo vasaros laiko)
  * Rytų laikas: -5 arba -4 (priklausomai nuo vasaros laiko)
  * Vidurio Europos laikas: +1 arba +2 (priklausomai nuo vasaros laiko)

### Taikyti PPK pataisas

* **Tipas**: Žymės langelis
* **Numatytasis**: Išjungta (nepažymėta)
* **Aprašymas**: Įjungia Post-Processed Kinematic (PPK) korekcijų naudojimą iš MAPIR DAQ įrašymo įrenginių, turinčių GPS (GNSS). Kai įjungta, Chloros naudos visus .daq žurnalo failus, turinčius ekspozicijos pin duomenis jūsų projekto kataloge, ir pritaikys tikslias geolokacijos korekcijas jūsų vaizdams.
* **Reikalavimas**: Jūsų projekto kataloge turi būti .daq žurnalo failas su ekspozicijos kontaktų įrašais
* **Kada įjungti**: Rekomenduojama visada įjungti PPK korekciją, jei jūsų .daq žurnalo faile yra ekspozicijos grįžtamojo ryšio įrašai.

### Ekspozicijos kontaktas 1

* **Tipas**: Išskleidžiamas meniu
* **Matomumas**: Matomas tik tada, kai įjungta funkcija „Taikyti PPK korekcijas“ IR yra ekspozicijos duomenys kontaktui 1
* **Parinktys**:
  * Projekte aptikti fotoaparato modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis**: Pasirenkama automatiškai pagal projekto konfigūraciją
* **Aprašymas**: Priskiria konkrečią kamerą ekspozicijos kontaktui 1 PPK laiko sinchronizavimui. Ekspozicijos kontaktas įrašo tikslų laiką, kada suveikia kameros užraktas, o tai yra labai svarbu tiksliai PPK geolokacijai.
* **Automatinio pasirinkimo veikimas**:
  * Viena kamera + vienas kontaktas: Automatiškai pasirenka kamerą
  * Viena kamera + du kaiščiai: 1-asis kaištis automatiškai priskiriamas kamerai
  * Kelios kameros: Reikia pasirinkti rankiniu būdu

### Ekspozicijos kaištis 2

* **Tipas**: Pasirinkimas iš išskleidžiamojo meniu
* **Matomumas**: Matomas tik tada, kai įjungta funkcija „Taikyti PPK pataisas“ IR yra ekspozicijos duomenų 2-ajam kaiščiui
* **Parinktys**:
  * Projekte aptikti kamerų modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis**: Pasirenkama automatiškai pagal projekto konfigūraciją
* **Aprašymas**: Priskiria konkrečią kamerą ekspozicijos kontaktui 2, kad būtų sinchronizuotas PPK laikas, kai naudojama dviejų kamerų konfigūracija.
* **Automatinio pasirinkimo elgsena**:
  * Viena kamera + vienas kontaktas: Kontaktas 2 automatiškai nustatomas į „Nenaudoti“
  * Viena kamera + du kaiščiai: 2-asis kaištis automatiškai nustatomas į „Nenaudoti“
  * Kelios kameros: Reikalingas rankinis pasirinkimas
* **Pastaba**: Ta pati kamera negali būti priskirta 1-ajam ir 2-ajam kaiščiams vienu metu.***

## Indeksas

Šie nustatymai leidžia konfigūruoti multispektrinius indeksus analizės ir vizualizacijos tikslais.

### Pridėti indeksą

* **Tipas**: Specialus indeksų konfigūracijos skydelis
* **Aprašymas**: Atidaro interaktyvų skydelį, kuriame galite pasirinkti ir konfigūruoti multispektrinius augmenijos indeksus (NDVI, NDRE, EVI ir kt.), kurie bus apskaičiuojami vaizdo apdorojimo metu. Galite pridėti kelis indeksus, kiekvienam nustatydami atskirus vizualizavimo parametrus.
* **Galimi indeksai**: Sistemoje yra daugiau nei 30 iš anksto apibrėžtų daugiaspektrinių indeksų, įskaitant:
  * NDVI (Normalizuotas augmenijos skirtumo indeksas)
  * NDRE (normalizuotas skirtumas RedEdge)
  * EVI (patobulintas augmenijos indeksas)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Ir daugelis kitų (žr. [Daugiaspektrinių indeksų formulės](multispectral-index-formulas.md) norėdami pamatyti pilną sąrašą)
* **Funkcijos**:
  * Pasirinkite iš iš anksto apibrėžtų indeksų formulių
  * Konfigūruokite vizualizacijos spalvų gradientus (LUT – paieškos lentelės)
  * Nustatykite analizės ribines vertes
  * Sukurkite pasirinktines indeksų formules

### Pasirinktinės formulės (Chloros+ funkcija)

* **Tipas**: Pasirinktinių formulių apibrėžimų masyvas
* **Aprašymas**: Leidžia kurti ir išsaugoti pasirinktines multispektrinių indeksų formules naudojant juostų matematiką. Pasirinktinės formulės išsaugomos kartu su jūsų projekto nustatymais ir gali būti naudojamos kaip įdiegtieji indeksai.
* **Kaip sukurti**:
  1. Indekso konfigūracijos skydelyje suraskite pasirinktinių formulių parinktį
  2. Apibrėžkite formulę naudodami juostų identifikatorius (pvz., NIR, Red, Green, Blue)
  3. Išsaugokite formulę su aprašomuoju pavadinimu
* **Formulės sintaksė**: Palaikomos standartinės matematinės operacijos, įskaitant:
  * Aritmetika: `+`, `-`, `*`, `/`
  * Skliausteliai operacijų tvarkai
  * Juostų nuorodos: NIR, Red, Green, Blue, RedEdge, Cyan, Orange, NIR1, NIR2

***

## Eksportavimas

Šie nustatymai kontroliuoja eksportuojamų apdorotų vaizdų formatą ir kokybę.

### Kalibruoto vaizdo formatas

* **Tipas**: Išskleidžiamas meniu
* **Parinktys**:
  * **TIFF (16 bitų)** – Nesuspaustas 16 bitų TIFF formatas
  * **TIFF (32 bitai, procentais)** – 32 bitų slankiojo kablelio TIFF su atspindžio vertėmis procentais
  * **PNG (8 bitai)** - Suspaustas 8 bitų PNG formatas
  * **JPG (8 bitų)** - Suspaustas 8 bitų JPEG formatas
* **Numatytasis**: TIFF (16 bitų)
* **Aprašymas**: Pasirenkamas failo formatas, kuriuo bus išsaugoti apdoroti ir kalibruoti vaizdai.
* **Formato rekomendacijos**:
  * **TIFF (16 bitų)**: Rekomenduojamas moksliniams tyrimams ir profesionaliems darbo procesams. Užtikrina maksimalią duomenų kokybę be suspaudimo artefaktų. Geriausiai tinka multispektrinei analizei ir tolesniam apdorojimui GIS programinėje įrangoje.
  * **TIFF (32 bitai, procentais)**: Geriausiai tinka darbo eigoms, kurioms reikalingos atspindžio vertės procentais (0–100 %). Užtikrina maksimalų radiometrinių matavimų tikslumą.
  * **PNG (8 bitai)**: Tinka peržiūrai internete ir bendrai vizualizacijai. Mažesni failų dydžiai su nesuspaudimu be nuostolių, tačiau sumažintas dinaminis diapazonas.
  * **JPG (8 bitai)**: Mažiausi failų dydžiai, geriausiai tinka tik peržiūrai ir rodymui internete. Naudoja suspaudimą su nuostoliais, kuris netinka mokslinei analizei.***

## Išsaugoti projekto šabloną

Ši funkcija leidžia išsaugoti dabartinius projekto nustatymus kaip pakartotinai naudojamą šabloną.

* **Tipas**: Teksto įvedimas + „Išsaugoti“ mygtukas
* **Aprašymas**: Įveskite aprašomąjį pavadinimą savo nustatymų šablonui ir spustelėkite išsaugojimo piktogramą. Šablone bus išsaugoti visi dabartiniai projekto nustatymai (tikslo aptikimas, apdorojimo parinktys, indeksai ir eksporto formatas), kad juos būtų galima lengvai pakartotinai naudoti būsimuose projektuose.
* **Naudojimo atvejai**:
  * Sukurkite šablonus skirtingoms kamerų sistemoms (RGB, multispektrinė, NIR)
  * Išsaugokite standartines konfigūracijas konkretiems pasėlių tipams ar analizės darbo eigoms
  * Dalinkitės nuosekliais nustatymais su visa komanda
* **Kaip naudoti**:
  1. Nustatykite visus norimus projekto nustatymus
  2. Įveskite šablono pavadinimą (pvz., „RedEdge Survey3 NDVI Standartinis“)
  3. Spustelėkite išsaugojimo piktogramą
  4. Dabar šabloną galima įkelti kuriant naujus projektus

***

## Projekto aplanko išsaugojimas

Šis nustatymas nurodo, kur nauji projektai yra išsaugomi pagal numatytuosius nustatymus.

* **Tipas**: Katalogų kelio rodymas + Redaguoti mygtukas
* **Numatytasis (Windows)**: `C:\Users\[Username]\Chloros Projects`
* **Numatytasis (Linux)**: `~/.local/share/chloros/projects`
* **Aprašymas**: Rodo dabartinį numatytąjį katalogą, kuriame kuriamos naujos Chloros projektai. Spustelėkite redagavimo piktogramą, kad pasirinkite kitą katalogą.
* **Kada keisti**:
  * Nustatykite tinklo diską komandiniam bendradarbiavimui
  * Pakeiskite į diską su daugiau saugojimo vietos dideliems duomenų rinkiniams
  * Tvarkykite projektus pagal metus, klientą ar projekto tipą skirtingose aplankose
* **Pastaba**: Šio parametro pakeitimas turi įtakos tik NAUJIEMS projektams. Esami projektai lieka savo pradinėse vietose.***

## Parametrų išsaugojimas

Visi projekto nustatymai automatiškai išsaugomi kartu su projekto failu (`.mapir` projekto formatas). Kai vėl atidarote projektą, visi nustatymai atkurti tiksliai taip, kaip juos palikote.

### Nustatymų hierarchija

Nustatymai taikomi tokia tvarka:

1. **Sistemos numatyti nustatymai** – įdiegtieji numatyti nustatymai, apibrėžti Chloros
2. **Šablono nustatymai** – jei kurdami projektą įkeliate šabloną
3. **Išsaugoti projekto nustatymai** – su projekto failu išsaugoti nustatymai
4. **Rankiniai koregavimai** – bet kokie pakeitimai, kuriuos atliekate per dabartinę sesiją

### Nustatymai ir vaizdų apdorojimas

Dauguma nustatymų pakeitimų (ypač kategorijose „Apdorojimas“ ir „Eksportavimas“) sukels vaizdų pakartotinį apdorojimą, kad būtų atsižvelgta į naujus nustatymus. Tačiau kai kurie nustatymai yra „tik eksportavimui“ ir nereikalauja nedelsiamo pakartotinio apdorojimo:

* Išsaugoti projekto šabloną
* Darbinis katalogas
* Kalibruotas vaizdo formatas (taikomas eksportuojant)

***

## Geriausia praktika

1. **Pradėkite nuo numatytųjų nustatymų**: Numatytieji nustatymai puikiai tinka daugumai MAPIR kamerų sistemų ir tipinių darbo eigų.
2. **Sukurkite šablonus**: Kai optimizuosite nustatymus konkrečiai darbo eigai ar kamerai, išsaugokite juos kaip šabloną, kad užtikrintumėte nuoseklumą visuose projektuose.
3. **Išbandykite prieš visišką apdorojimą**: Eksperimentuodami su naujais nustatymais, išbandykite juos su nedideliu vaizdų rinkiniu, prieš apdorodami visą duomenų rinkinį.
4. **Užfiksuokite savo nustatymus**: Naudokite aprašomuosius šablonų pavadinimus, nurodančius kamerų sistemą, apdorojimo tipą ir numatytą paskirtį (pvz., „Survey3\_RGB\_NDVI\_Agriculture“).
5. **Eksporto formato pasirinkimas**: Pasirinkite eksporto formatą pagal galutinį naudojimo tikslą:
   * Mokslinė analizė → TIFF (16 bitų arba 32 bitų)
   * GIS apdorojimas → TIFF (16 bitų)
   * Greitas vizualizavimas → PNG (8 bitų)
   * Dalijimasis internete → JPG (8 bitų)

***

Daugiau informacijos apie daugiaspektrinius indeksus Chloros rasite puslapyje [Daugiaspektrinių indeksų formulės](multispectral-index-formulas.md).
