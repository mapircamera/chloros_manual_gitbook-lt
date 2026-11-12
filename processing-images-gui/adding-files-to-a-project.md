# Failų pridėjimas į projektą

Sukūrę arba atidarę projektą Chloros programoje, kitas žingsnis – pridėti daugiaspektrinius vaizdus, kad būtų galima pradėti jų apdorojimą. Skirtukas „Failų naršyklė“ <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> leidžia lengvai importuoti vaizdus ir tvarkyti duomenų rinkinį.

## Prieiga prie failų naršyklės

1. Atidarykite arba sukurkite projektą Chloros programoje
2. Spustelėkite **„File Browser“** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> piktogramą kairiajame šoniniame meniu
3. „File Browser“ skydelyje bus rodomas jūsų projekto failų sąrašas

{% hint style="info" %}
**Palaikomi failų tipai**:

* **Survey3W / Survey3N**: RAW+JPG poros ir JPG vaizdai (rekomenduojama naudoti RAW+JPG)
* **LATTICE**: „`.tif`“ / „`.tiff`“ įrašai — įrašyti naudojant Chloros kameros valdymą arba „LATTICE“ šakotuvą
* **Šviesos jutiklio duomenys**: `.daq` įrašai (DAQ-U/M/E) ir DAQ-M `.csv` žemyn nukreiptų spindulių registravimo duomenys — importuojami kartu su vaizdais, siekiant atlikti atspindžio kalibravimą
{% endhint %}

***

## Vaizdų pridėjimas prie projekto

Yra du pagrindiniai būdai, kaip pridėti vaizdus prie projekto:

### 1-asis būdas: failų pridėjimas

Naudokite šią parinktį, norėdami importuoti atskirus vaizdo failus arba nedidelį failų rinkinį.

1. Spustelėkite mygtuką **„Pridėti failus“** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line">, esantį failų naršyklės skydelio viršuje
2. Pereikite į aplanką, kuriame yra jūsų vaizdai
3. Pasirinkite vieną ar kelis vaizdo failus (laikydami nuspaudę klavišą **Ctrl**, galite pasirinkti kelis failus)
4. Spustelėkite **„Atidaryti“**, kad importuotumėte pasirinktus failus

### 2-asis būdas: pridėti aplanką

Naudokite šią parinktį, jei norite vienu metu importuoti visus vaizdus iš aplanko. Viename dialogo lange galite pasirinkti **kelis aplankus**.

1. Spustelėkite mygtuką **„Pridėti aplanką“** <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line">, esantį failų naršyklės skydelio viršuje
2. Pereikite į aplanką (-us), kuriame (-iuose) yra jūsų fotografavimo sesijos nuotraukos, ir jį (-us) pasirinkite
3. Spustelėkite **„Pasirinkti aplanką“**, kad importuotumėte visas palaikomas nuotraukas

{% hint style="info" %}
**Pranešama apie failus, kurių nepavyko įkelti.** Jei aplanke yra failų, kuriuos Chloros atpažįsta, bet negali įkelti, apie tai pranešamas įspėjimas — vaizdai iš tinklelio neišnyksta nepastebimai.
{% endhint %}

***

## „LATTICE“ fiksavimo aplankų importavimas

„LATTICE“ fiksavimo duomenys išsaugomi **po vieną pakatalogį kiekvienam eksporto lygiui** — pavyzdžiui, `raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/` — su atitinkamu „`.daq`“ žemyn nukreiptu failu šaknies kataloge:

```
output/
├── raw/           capture_<timestamp>_SN<serial>_raw.tif
├── debayered/     capture_<timestamp>_SN<serial>_debayered.tif
├── preview/       capture_<timestamp>_SN<serial>_display.tif
└── *.daq          the downwelling reading matched to the capture
```

**Nurodykite aplanką, esantį „captures“ šakninėje aplankoje** (žr. aukščiau esantį `output/`). Jei pasirinktame aplanke pačiame nėra vaizdų, bet yra pakatalogiai, Chloros automatiškai juos peržiūri — visų lygių pakatalogiai ir šakninis katalogas „`.daq`“ surenkami vienu kartu.

**Kaip importuojami įrašai:**

* Kiekvienas įrašas importuojamas kaip **vienas vaizdas**, sugrupuotas pagal įrašą (ne po vieną įrašą kiekviename lygyje). Kiti to paties įrašo lygiai rodomi kaip to vieno vaizdo peržiūros režimai.
* **Apdorojimas visada prasideda nuo neapdoroto kadro.** Kiti lygiai yra peržiūrimi, tačiau per apdorojimo grandinę perduodamas tik „`raw`“ — pakartotinis jau apdoroto produkto apdorojimas dvigubai pritaikytų korekcijas, todėl Chloros to atsisako. Pakartotinai importuotas eksportas niekada negali užimti kadrų neapdorotų duomenų vietos.
* Kadrų aplankas, išsaugotas **be** neapdorotų duomenų, importuojamas ir rodomas įprastai, tačiau apdorojimas jį praleidžia ir apie tai praneša žurnale. (CLI žymė `--input-level` gali priversti nustatyti įėjimo tašką šiuo atveju — žr. [CLI žinyną](../reference/cli-reference.md#what-a-captures-folder-looks-like).)
* Nuo 1.3.0 versijos užfiksuotų vaizdų aplankas taip pat gali turėti `clamp_masks/` aplanką (pikselių lygio kaukės spinduliavimo ir atspindžio lygiams — žr. [Išvesties vaizdų formatai](../output-image-formats.md#clamp-masks)). Kaukės nėra scenos vaizdai, todėl Chloros jas ignoruoja importuojant, nesvarbu, kur jos būtų.

**„LATTICE hub“ sesijos** importuojamos taip pat: spustelėkite „Add Folder“ (Pridėti aplanką) ir nurodykite iš „hub“ nukopijuotą sesijos aplanką (jame yra „`raw/`“ ir „`previews/`“), kartu su bet kokiu DAQ-M „`.csv`“ žemyn nukreipto spinduliavimo žurnalu. Jei kameros ar DAQ kalibravimo duomenys dar nėra išsaugoti jūsų kompiuterio talpykloje, Chloros juos importuojant automatiškai atsisiunčia pagal serijos numerį (reikia vienkartinio prisijungimo prie interneto).

***

## Failų naršyklės lentelės supratimas

Importavus vaizdus, jie rodomi lentelėje su šiomis skiltimis:

### Failo pavadinimas

* Originalus failo pavadinimas iš fotoaparato
* Išlaiko fotoaparato pavadinimų sudarymo taisykles (pvz., IMG\_0001.RAW arba capture\_20260816\_101500\_SN213800234\_raw.tif)

### Laiko žyma

* Nuotraukos užfiksavimo data ir laikas
* Išgauta iš nuotraukos EXIF metaduomenų
* Naudojama šviesos jutiklių suderinimui, PPK sinchronizavimui ir kalibravimo taškų planavimui

### Fotoaparato modelis

* Automatiškai nustatyta kameros ir filtro konfigūracija
* Survey3 pavyzdžiai: Survey3W_RGN, Survey3N_OCN, Survey3W_RGB
* „LATTICE“ pavyzdžiai: LATT-M3M-L41-F550, LATT-M3C-L87-FRGN
* Naudojama teisingiems apdorojimo profiliams taikyti

### Tikslo stulpelis (žymės langelis)

* Pažymėkite šį langelį, jei vaizduose yra kalibravimo taikiniai
* Kai pažymėtas bent vienas vaizdas, **tik pažymėti vaizdai yra nuskaitomi** ieškant taikinų
* Daugiau informacijos rasite skyriuje [Tikslo vaizdų pasirinkimas](choosing-target-images.md)

### Vaizdo metaduomenų peržiūra

Paspaudus perjungimo mygtuką viršutiniame dešiniajame kampe virš lentelės, pasirinktų vaizdų metaduomenys rodomi vaizdų tinklelio srityje.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Jūsų projekte esantys šviesos jutiklio failai

* Failai „`.daq`“ ir „`.csv`“ rodomi failų naršyklės sąraše, tačiau jų negalima paspausti kaip vaizdų — jie pateikia žemyn nukreiptą spinduliavimo intensyvumą atspindžio kalibravimui.
* Kiekvienas importuotas `.daq`/`.csv` failas yra išvardytas skyriuje **Projekto nustatymai → DAQ šviesos jutiklis**, kur galite peržiūrėti kiekvienam failui taikomą difuzoriaus dangtelio korekciją. Žr. [Projekto nustatymų koregavimas](adjusting-project-settings.md).
* Įrašai, kuriuos atliekate skirtuke **Šviesos jutikliai**, automatiškai pridedami prie atidaryto projekto — rankinis importavimas nereikalingas.

***

## Failų tvarkymas projekte

### Failų pašalinimas

Norėdami pašalinti nereikalingus vaizdus iš savo projekto:

1. Pasirinkite vieną ar kelis vaizdus lentelėje „Failų naršyklė“
2. Spustelėkite mygtuką **„Pašalinti pasirinktus“** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
3. Patvirtinkite pašalinimą (failai nėra ištrinami iš disko, tik pašalinami iš projekto)

### Rūšiavimas ir filtravimas

* **Rūšiavimas pagal stulpelį**: spustelėkite bet kurį stulpelio antraštę, kad surūšiuotumėte vaizdus
* **Rūšiavimas pagal laiko žymą**: naudinga tvarkant chronologines fotografavimo sekas
* **Filtras pagal fotoaparato modelį**: sugrupuokite vaizdus pagal fotoaparato tipą, jei naudojate kelis fotoaparatus***

## Vaizdo peržiūra

### Pilno vaizdo peržiūra

Spustelėkite bet kurią vaizdo miniatiūrą failų naršyklėje, kad ji būtų rodoma pagrindiniame peržiūros srityje:

1. Vaizdas pasirodo centriniame peržiūros skydelyje
2. Naudokite mastelio reguliavimo valdiklius, kad apžiūrėtumėte vaizdo detales
3. Naršykite tarp vaizdų naudodami rodyklių klavišus

### Greita navigacija

* **Ankstesnis vaizdas**: spustelėkite kairę rodyklę arba paspauskite klavišą ←
* **Kitas vaizdas**: spustelėkite dešinę rodyklę arba paspauskite klavišą →
* **Padidinimas/sumažinimas**: naudokite pelės ratuką arba mastelio keitimo mygtukus
* **Perkėlimas**: padidinus vaizdą, spustelėkite ir vilkite pelę***

## Darbo su pasikartojančiais failais tvarka

Chloros automatiškai aptinka ir ignoruoja pasikartojančius failus:

* Failai su identiškais pavadinimais yra praleidžiami
* Apsaugo nuo netyčinio dvigubo apdorojimo
* Aptikus pasikartojančius failus, rodomas įspėjamasis pranešimas

{% hint style="warning" %}
**Svarbu**: Prieš importuojant nepakeiskite originalių vaizdo failų pavadinimų ir nemodifikuokite jų. Chloros tinkamam apdorojimui naudoja originalius failų pavadinimus ir metaduomenis.
{% endhint %}

***

## Mišrūs kamerų duomenų rinkiniai

Jei jūsų projekte yra vaizdų, padarytų naudojant kelias MAPIR kameras:

1. Chloros automatiškai aptinka kiekvieną kameros modelį — Survey3, „LATTICE“ arba jų derinį
2. Kiekvienas kameros tipas apdorojamas naudojant atitinkamą kalibravimo profilį
3. Failų naršyklėje kameros modelis rodomas stulpelyje „Camera Model“
4. Apdorojant kiekvienai kamerai sukuriama atskira išvesties aplankų struktūra

**Pavyzdiniai scenarijai**: Survey3W RGN + Survey3N OCN dviejų kamerų konfigūracija arba „LATTICE“ matrica su RGB pagrindine kamera ir keliais siaurajuosčio dažnio moduliais***

## Geriausia praktika

### Tvarkykite prieš importuojant

* Laikykite kalibravimo taikinio vaizdus toje pačioje aplankėje kaip ir tyrimo vaizdus
* Kiekvienos fotografavimo sesijos šviesos jutiklio failus `.daq` / `.csv` laikykite kartu su tos sesijos vaizdais
* Išsaugokite originalią aplankų struktūrą iš savo kameros / SD kortelės / koncentratoriaus
* Viename projekte nemaišykite skirtingų sesijų duomenų rinkinių

### Failų pavadinimai

* Išsaugokite originalius fotoaparato failų pavadinimus (IMG\_0001.RAW, capture\_... ir pan.)
* Prieš importuojant failų nepervardykite
* Originalūs pavadinimai turi svarbių metaduomenų

### Kalibravimo taškų vaizdai

* Visada įtraukite 1–2 kalibravimo taškų vaizdus iš kiekvienos sesijos (Survey3; „LATTICE“ atveju juos gali pakeisti DAQ įrašas — žr. [Kalibravimo taškų vaizdų pasirinkimas](choosing-target-images.md))
* Nufotografuokite kalibravimo taikinius prieš ir po fotografavimo sesijos
* Pastatykite taikinius tokiomis pačiomis apšvietimo sąlygomis kaip ir fotografuojamą plotą
* Pažymėkite taikinius, pažymėdami langelį „Target“

***

## Dažnos problemos ir jų sprendimai

### Vaizdai neatsiranda po importavimo

**Galimos priežastys:**

* Nepalaikomas failo formatas (žr. palaikomų tipų sąrašą šio puslapio viršuje)
* Vaizdai yra iš ne MAPIR fotoaparatų (žr. [Palaikomi fotoaparatai](../supported-cameras.md))
* Sugadintas failas arba neišsamus perkėlimas iš SD kortelės

**Sprendimas**: Patikrinkite failo formato ir fotoaparato modelio suderinamumą bei peržiūrėkite failų įkėlimo įspėjimą, kad nustatytumėte, kokie tiksliai failai nebuvo įkelti

### Neaptiktas fotoaparato modelis

**Galimos priežastys:**

* Pakeisti EXIF metaduomenys
* Vaizdai redaguoti išorinėje programinėje įrangoje
* Neužbaigtas failų perkėlimas

**Sprendimas**: Iš naujo importuokite originalius, nepakeistus failus iš fotoaparato / SD kortelės

### Trūksta laiko žymų

**Galimos priežastys:**

* Netinkamai nustatytas fotoaparato laikrodis
* EXIF duomenys pašalinti išorinės programinės įrangos

**Sprendimas**: Patikrinkite, ar fotografavimo metu fotoaparato laiko nustatymai buvo teisingi

### Vėl atidarytas projektas praneša apie trūkstamus failus

Jei šaltinio failai buvo perkelti arba ištrinti nuo paskutinio projekto atidarymo, Chloros praneš, **kurie**failai dingo, o ne atidarys tuščią tinklelį. Jei buvo perkelta pati projekto aplankas, Chloros nukreipia šaltinio kelius į aplanką, kuriame dabar yra „`project.json`“, ir išsaugo atsarginę kopiją („`project.json.before-relink-<date>-<time>`“). Jei failų nerandama, lange**„Šaltinio failai nerasti“**siūloma pasinaudoti funkcija**„Naršyti“**, kad surastumėte naują vietą, o tada pasirinkti „Naujas projektas“ arba „Ištrinti projektą“. Naudodami CLI, naudokite `chloros-cli project open PATH --relink FOLDER`; naudodami SDK – `open_project(path, relink=...)`.

***

## Tolimesni veiksmai

Kai failai bus importuoti:

1. **Peržiūrėkite failų sąrašą** – įsitikinkite, kad visi vaizdai įkelti teisingai
2. **Patikrinkite kamerų modelius** – įsitikinkite, kad kameros atpažintos teisingai
3. **Pažymėkite tikslinius vaizdus** – žr. [Tikslinių vaizdų pasirinkimas](choosing-target-images.md)
4. **Nustatykite parametrus** – sukonfigūruokite apdorojimo parinktis [Projekto nustatymuose](adjusting-project-settings.md)
5. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)

Išsamią informaciją apie projekto konfigūraciją rasite skyriuje [Projekto nustatymų koregavimas](adjusting-project-settings.md).
