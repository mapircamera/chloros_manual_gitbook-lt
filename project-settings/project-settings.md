# Projekto nustatymai

Projekto nustatymai <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> šoninėje juostoje Chloros galite konfigūruoti visus vaizdo apdorojimo, kalibravimo tikslo nustatymo, daugiaspektrinių indeksų skaičiavimo ir eksporto parinktis savo projektui. Šie nustatymai išsaugomi kartu su projektu ir gali būti išsaugoti kaip šablonai, kad būtų galima juos pakartotinai naudoti keliuose projektuose.

## Prieiga prie projekto nustatymų

Norėdami pasiekti projekto nustatymus:

1. Atidarykite projektą Chloros
2. Spustelėkite **Projekto nustatymai**  <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> skirtuką kairėje šoninėje juostoje
3. Nustatymų skydelyje bus rodomos visos galimos konfigūracijos parinktys, suskirstytos pagal kategorijas

***

## Tikslo aptikimas

Šie nustatymai kontroliuoja, kaip Chloros aptinka ir apdoroja kalibravimo tikslus jūsų vaizduose.

### Minimalus kalibravimo mėginio plotas (px)

* **Tipas**: skaičius
* **Diapazonas**: nuo 0 iki 10 000 pikselių
* **Numatytasis**: 25 pikseliai
* **Aprašymas**: nustato minimalų plotą (pikseliais), reikalingą, kad aptiktas regionas būtų laikomas tinkamu kalibravimo tikslo pavyzdžiu. Mažesnės vertės aptiks mažesnius tikslus, bet gali padidinti klaidingų teigiamų rezultatų skaičių. Didesnės vertės reikalauja didesnių, aiškesnių tikslų regionų aptikimui.
* **Kada reguliuoti**:
  * Padidinkite, jei gaunate klaidingus aptikimus mažose vaizdo artefaktose.
  * Sumažinkite, jei jūsų kalibravimo tikslai atrodo maži vaizduose ir nėra aptinkami.

### Minimalus tikslų grupuojimas (0–100)

* **Tipas**: skaičius
* **Diapazonas**: nuo 0 iki 100
* **Numatytasis**: 60
* **Aprašymas**: kontroliuoja klasterizavimo slenkstį, skirtą panašių spalvų regionų grupuojimui aptinkant kalibravimo tikslus. Didesnės vertės reikalauja, kad būtų grupuojamos panašesnės spalvos, todėl tikslų aptikimas tampa konservatyvesnis. Mažesnės vertės leidžia didesnį spalvų įvairovę tikslo grupėje.
* **Kada reguliuoti**:
  * Padidinkite, jei kalibravimo tikslai yra suskaidomi į kelis aptikimus.
  * Sumažinkite, jei kalibravimo tikslai su spalvų variacijomis nėra visiškai aptinkami.

***

## Apdorojimas

Šie nustatymai kontroliuoja, kaip Chloros apdoroja ir kalibruoja jūsų vaizdus.

### Vignette korekcija

* **Tipas**: žymės langelis
* **Numatytasis**: įjungta (pažymėta)
* **Aprašymas**: Taiko vinjetės korekciją, kad kompensuotų objektyvo patamsėjimą vaizdų kraštuose. Vinjetė yra dažnas optinis reiškinys, kai vaizdo kampai ir kraštai atrodo tamsesni nei centras dėl objektyvo savybių.
* **Kada išjungti**: Išjunkite tik tuo atveju, jei jūsų fotoaparato ir objektyvo kombinacija jau taiko vinjetės korekciją arba jei norite rankiniu būdu koreguoti vinjetę apdorojimo metu.

### Atspindžio kalibravimas / baltos spalvos balansas

* **Tipas**: žymės langelis
* **Numatytasis**: įjungta (pažymėta)
* **Aprašymas**: įjungia automatinį atspindžio kalibravimą, naudojant vaizduose aptiktus kalibravimo tikslus. Tai normalizuoja atspindžio vertes visame duomenų rinkinyje ir užtikrina nuoseklius matavimus nepriklausomai nuo apšvietimo sąlygų.
* **Kada išjungti**: išjunkite tik tuo atveju, jei norite apdoroti neapdorotus, nekalibruotus vaizdus arba jei naudojate kitą kalibravimo darbo eigą.

### Debayer metodas

* **Tipas**: išskleidžiamas meniu
* **Parinktys**:
  * Aukšta kokybė (greičiau) – šiuo metu vienintelė galima parinktis
* **Numatytasis**: Aukšta kokybė (greičiau)
* **Aprašymas**: Pasirenkamas demosaicing algoritmas, naudojamas neapdorotiems Bayer modelio jutiklio duomenims konvertuoti į spalvotus vaizdus. „Aukšta kokybė (greičiau)“ metodas užtikrina optimalų apdorojimo greičio ir vaizdo kokybės balansą.
* **Pastaba**: Ateityje Chloros versijose gali būti pridėti papildomi debayer metodai.

### Minimalus pakalibravimo intervalas

* **Tipas**: skaičius
* **Diapazonas**: nuo 0 iki 3600 sekundžių
* **Numatytasis**: 0 sekundės
* **Aprašymas**: nustato minimalų laiko intervalą (sekundėmis) tarp kalibravimo tikslų naudojimo. Nustačius 0, Chloros naudos kiekvieną aptiktą kalibravimo tikslą. Nustačius didesnę vertę, Chloros naudos tik kalibravimo tikslus, kurie yra atskirti bent jau šiuo sekundžių skaičiumi, taip sumažindamas duomenų rinkinių, kuriuose dažnai fiksuojami kalibravimo tikslai, apdorojimo laiką.
* **Kada reguliuoti**:
  * Nustatykite 0, kad būtų pasiektas maksimalus kalibravimo tikslumas, kai apšvietimo sąlygos kinta.
  * Padidinkite (pvz., iki 60–300 sekundžių), kad apdorojimas būtų greitesnis, kai apšvietimas yra pastovus ir turite dažnus kalibravimo tikslų vaizdus.

### Šviesos jutiklio laiko juostos nuokrypis

* **Tipas**: skaičius
* **Diapazonas**: nuo -12 iki +12 valandų
* **Numatytasis**: 0 valandos
* **Aprašymas**: Nurodo laiko juostos poslinkį (valandomis nuo UTC) šviesos jutiklio duomenų laiko žymėms. Tai naudojama apdorojant PPK (Post-Processed Kinematic) duomenų failus, siekiant užtikrinti teisingą laiko sinchronizaciją tarp vaizdų užfiksavimo ir GPS duomenų.
* **Kada koreguoti**: Nustatykite savo vietos laiko juostos nuokrypį, jei jūsų PPK duomenys naudoja vietos laiką, o ne UTC. Pavyzdžiui:
  * Ramiojo vandenyno laikas: -8 arba -7 (priklausomai nuo vasaros laiko)
  * Rytų laikas: -5 arba -4 (priklausomai nuo vasaros laiko)
  * Vidurio Europos laikas: +1 arba +2 (priklausomai nuo vasaros laiko)

### Taikyti PPK pataisas

* **Tipas**: žymės langelis
* **Numatytasis**: išjungta (nepažymėta)
* **Aprašymas**: įjungia galimybę naudoti post-processed kinematic (PPK) pataisas iš MAPIR DAQ įrašymo įrenginių, turinčių GPS (GNSS). Kai įjungta, Chloros naudos visus .daq žurnalo failus, kuriuose yra ekspozicijos kaiščio duomenys jūsų projekto kataloge, ir taikys tikslias geografinės vietos korekcijas jūsų vaizdams.
* **Reikalavimas**: .daq žurnalo failas su ekspozicijos kaiščio įrašais turi būti jūsų projekto kataloge
* **Kada įjungti**: Rekomenduojama visada įjungti PPK korekciją, jei jūsų .daq žurnalo faile yra ekspozicijos grįžtamojo ryšio įrašai.

### Ekspozicijos kontaktas 1

* **Tipas**: išskleidžiamas meniu
* **Matomumas**: matomas tik tada, kai įjungta funkcija „Taikyti PPK pataisas“ IR yra ekspozicijos duomenys kontaktui 1
* **Parinktys**:
  * Projekto aptikti fotoaparato modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis**: automatiškai parenkamas pagal projekto konfigūraciją
* **Aprašymas**: Priskiria konkrečią kamerą ekspozicijos kontaktui 1 PPK laiko sinchronizavimui. Ekspozicijos kontaktas įrašo tikslų laiką, kai suveikia kameros užraktas, o tai yra labai svarbu tiksliai PPK geografinės vietos nustatymui.
* **Automatinio pasirinkimo veikimas**:
  * Viena kamera + vienas kontaktas: Automatiškai pasirenka kamerą
  * Viena kamera + du kaiščiai: 1 kaištis automatiškai priskiriamas kamerai
  * Kelios kameros: Reikia pasirinkti rankiniu būdu

### Ekspozicijos kaištis 2

* **Tipas**: Pasirinkimas iš išskleidžiamojo meniu
* **Matomumas**: Matomas tik tada, kai įjungta funkcija „Taikyti PPK pataisas“ IR kai ekspozicijos duomenys yra prieinami 2 kaiščiui
* **Parinktys**:
  * Projekto metu aptikti kamerų modelių pavadinimai
  * „Nenaudoti“ – ignoruoti šį ekspozicijos kontaktą
* **Numatytasis**: automatiškai pasirenkamas pagal projekto konfigūraciją
* **Aprašymas**: priskiria konkretų fotoaparatą ekspozicijos kontaktui 2 PPK laiko sinchronizavimui, kai naudojama dviejų fotoaparatų konfigūracija.
* **Automatinio pasirinkimo elgsena**:
  * Vienas fotoaparatas + vienas kontaktas: kontaktas 2 automatiškai nustatomas kaip „Nenaudoti“
  * Viena kamera + du kaiščiai: 2 kaištis automatiškai nustatomas kaip „Nenaudoti“
  * Kelios kameros: reikalingas rankinis pasirinkimas
* **Pastaba**: ta pati kamera negali būti priskirta ir 1, ir 2 kaiščiams tuo pačiu metu.

***

## Indeksas

Šie nustatymai leidžia konfigūruoti daugiaspektrinius indeksus analizės ir vizualizavimo tikslais.

### Pridėti indeksą

* **Tipas**: specialus indeksų konfigūracijos skydelis
* **Aprašymas**: Atidaro interaktyvų skydelį, kuriame galite pasirinkti ir konfigūruoti daugiaspektrinius augmenijos indeksus (NDVI, NDRE, EVI ir kt.), kurie bus apskaičiuojami vaizdo apdorojimo metu. Galite pridėti kelis indeksus, kiekvienam iš jų nustatydami savo vizualizavimo parametrus.
* **Galimi indeksai**: Sistema apima daugiau nei 30 iš anksto nustatytų daugiaspektrinių indeksų, įskaitant:
  * NDVI (normalizuotas augmenijos skirtumo indeksas)
  * NDRE (normalizuotas skirtumas RedEdge)
  * EVI (patobulintas augmenijos indeksas)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Ir daugelis kitų (visą sąrašą rasite [Daugiaspektrinių indeksų formulės](multispectral-index-formulas.md))
* **Savybės**:
  * Pasirinkite iš iš anksto nustatytų indeksų formulių
  * Konfigūruokite vizualizacijos spalvų gradientus (LUT – paieškos lentelės)
  * Nustatykite analizės ribines vertes
  * Sukurkite pasirinktines indeksų formules

### Pasirinktinės formulės (Chloros+ funkcija)

* **Tipas**: Pasirinktinių formulių apibrėžimų masyvas
* **Aprašymas**: Leidžia kurti ir išsaugoti pasirinktines multispektrinių indeksų formules naudojant juostų matematiką. Pasirinktinės formulės išsaugomos su jūsų projekto nustatymais ir gali būti naudojamos kaip įdiegtieji indeksai.
* **Kaip sukurti**:
  1. Indekso konfigūracijos skydelyje ieškokite pasirinktinės formulės parinkties.
  2. Apibrėžkite formulę naudodami juostų identifikatorius (pvz., NIR, Red, Green, Blue).
  3. Išsaugokite formulę su aprašomuoju pavadinimu.
* **Formulės sintaksė**: Palaikomos standartinės matematinės operacijos, įskaitant:
  * Aritmetika: `+`, `-`, `*`, `/`
  * Skaičiavimo operacijų eiliškumo skliausteliai
  * Juostos nuorodos: NIR, Red, Green, Blue, RedEdge, Cyan, Orange, NIR1, NIR2

***

## Eksportavimas

Šie nustatymai kontroliuoja eksportuojamų apdorotų vaizdų formatą ir kokybę.

### Kalibruotas vaizdo formatas

* **Tipas**: išskleidžiamas meniu
* **Parinktys**:
  * **TIFF (16 bitų)** – nesuspaustas 16 bitų TIFF formatas
  * **TIFF (32 bitai, procentai)** – 32 bitų slankiojo kablelio TIFF su atspindžio vertėmis procentais
  * **PNG (8 bitai)** - Suspaustas 8 bitų PNG formatas
  * **JPG (8 bitai)** - Suspaustas 8 bitų JPEG formatas
* **Numatytasis**: TIFF (16 bitų)
* **Aprašymas**: Pasirenkamas failo formatas, kuriuo bus išsaugoti apdoroti ir kalibruoti vaizdai.
* **Rekomenduojami formatai**:
  * **TIFF (16 bitų)**: Rekomenduojamas moksliniams tyrimams ir profesionaliems darbo procesams. Išsaugo maksimalią duomenų kokybę be suspaudimo artefaktų. Geriausiai tinka daugiaspektrinei analizei ir tolesniam apdorojimui GIS programinėje įrangoje.
  * **TIFF (32 bitai, procentai)**: geriausiai tinka darbo eigai, kuriai reikalingos atspindžio vertės procentais (0–100 %). Užtikrina maksimalų radiometrinių matavimų tikslumą.
  * **PNG (8 bitai)**: tinka peržiūrai internete ir bendrai vizualizacijai. Mažesni failų dydžiai su be nuostolių suspaudimu, bet sumažintas dinaminis diapazonas.
  * **JPG (8 bitai)**: Mažiausi failų dydžiai, geriausiai tinka tik peržiūrai ir rodymui internete. Naudoja suspaudimą su nuostoliais, kuris netinka moksliniams tyrimams.

***

## Projekto šablono išsaugojimas

Ši funkcija leidžia išsaugoti dabartinius projekto nustatymus kaip pakartotinai naudojamą šabloną.

* **Tipas**: Teksto įvedimas + mygtukas „Išsaugoti“
* **Aprašymas**: Įveskite aprašomąjį pavadinimą savo nustatymų šablonui ir spustelėkite išsaugojimo piktogramą. Šablonas išsaugos visus dabartinius projekto nustatymus (tikslo aptikimas, apdorojimo parinktys, indeksai ir eksporto formatas), kad juos būtų galima lengvai pakartotinai naudoti būsimuose projektuose.
* **Naudojimo atvejai**:
  * Sukurkite šablonus skirtingoms kamerų sistemoms (RGB, multispektrinė, NIR)
  * Išsaugokite standartines konfigūracijas konkretiems pasėlių tipams ar analizės darbo eigai
  * Dalinkitės nuosekliais nustatymais su visa komanda
* **Kaip naudoti**:
  1. Nustatykite visus norimus projekto nustatymus
  2. Įveskite šablono pavadinimą (pvz., „RedEdge Survey3 NDVI Standartinis“)
  3. Spustelėkite išsaugojimo piktogramą
  4. Dabar šabloną galima įkelti kuriant naujus projektus

***

## Projekto aplanko išsaugojimas

Šis nustatymas nurodo, kur nauji projektai yra išsaugomi pagal numatytuosius nustatymus.

* **Tipas**: Katalogų kelio rodymas + redagavimo mygtukas
* **Numatytasis**: `C:\Users\[Username]\Chloros Projects`
* **Aprašymas**: Rodo dabartinį numatytąjį katalogą, kuriame yra kuriamos naujos Chloros projektai. Spustelėkite redagavimo piktogramą, kad pasirinkite kitą katalogą.
* **Kada keisti**:
  * Nustatykite tinklo diską komandiniam bendradarbiavimui.
  * Keiskite į diską su daugiau saugojimo vietos dideliems duomenų rinkiniams.
  * Tvarkykite projektus pagal metus, klientą ar projekto tipą skirtingose aplankose.
* **Pastaba**: Šio parametro keitimas turi įtakos tik NAUJIEMS projektams. Esami projektai lieka savo pradinėse vietose.

***

## Parametrų išsaugojimas

Visi projekto nustatymai automatiškai išsaugomi su jūsų projekto failu (`.mapir` projekto formatas). Kai vėl atidarote projektą, visi nustatymai atkurti tiksliai taip, kaip juos palikote.

### Nustatymų hierarchija

Nustatymai taikomi tokia tvarka:

1. **Sistemos numatyti nustatymai** – įdiegtieji numatyti nustatymai, apibrėžti Chloros
2. **Šablono nustatymai** – jei kurdami projektą įkeliate šabloną
3. **Išsaugoti projekto nustatymai** – su projekto failu išsaugoti nustatymai
4. **Rankiniai koregavimai** – bet kokie pakeitimai, kuriuos atliekate per esamą sesiją

### Nustatymai ir vaizdų apdorojimas

Dauguma nustatymų pakeitimų (ypač kategorijose „Apdorojimas“ ir „Eksportavimas“) sukels vaizdų perdirbimą, kad būtų atspindėti nauji nustatymai. Tačiau kai kurie nustatymai yra „tik eksportavimo“ ir nereikalauja nedelsiamo pakartotinio apdorojimo:

* Išsaugoti projekto šabloną
* Darbinis katalogas
* Kalibruotas vaizdo formatas (taikoma eksportuojant)

***

## Geriausia praktika

1. **Pradėkite nuo numatytųjų nustatymų**: numatytieji nustatymai puikiai tinka daugumai MAPIR kamerų sistemų ir tipiniams darbo srautams.
2. **Sukurkite šablonus**: kai optimizavote nustatymus konkrečiam darbo srautui ar kamerai, išsaugokite juos kaip šabloną, kad užtikrintumėte nuoseklumą visuose projektuose.
3. **Išbandykite prieš visą apdorojimą**: kai bandote naujus nustatymus, išbandykite juos nedidelėje vaizdų dalyje, prieš apdorodami visą duomenų rinkinį.
4. **Užrašykite savo nustatymus**: naudokite aprašomuosius šablonų pavadinimus, kurie nurodo kameros sistemą, apdorojimo tipą ir numatytą naudojimą (pvz., „Survey3\_RGB\_NDVI\_Agriculture“).
5. **Eksporto formato pasirinkimas**: Pasirinkite eksporto formatą pagal galutinę paskirtį:
   * Mokslinė analizė → TIFF (16 bitų arba 32 bitų)
   * GIS apdorojimas → TIFF (16 bitų)
   * Greitas vizualizavimas → PNG (8 bitai)
   * Dalijimasis internete → JPG (8 bitai)

***

Daugiau informacijos apie daugiaspektrinius indeksus Chloros rasite puslapyje [Daugiaspektrinių indeksų formulės](multispectral-index-formulas.md).
