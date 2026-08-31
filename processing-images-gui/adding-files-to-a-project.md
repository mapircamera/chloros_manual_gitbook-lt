# Failų pridėjimas į projektą

Sukūrę arba atidarę projektą programoje Chloros, kitas žingsnis – pridėti daugiaspektrinius vaizdus, kad būtų galima pradėti apdorojimą. Skirtuke „Failų naršyklė“ (<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">) galima lengvai importuoti vaizdus ir tvarkyti duomenų rinkinį.

## Prieiga prie failų naršyklės

1. Atidarykite arba sukurkite projektą programoje „Chloros“
2. Kairiajame šoniniame meniu spustelėkite piktogramą **„File Browser“** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
3. „File Browser“ skydelyje bus rodomas jūsų projekto failų sąrašas

{% hint style="info" %}
**Palaikomi failų tipai**:

* **Survey3W / Survey3N**: RAW+JPG poros ir JPG vaizdai (rekomenduojama naudoti RAW+JPG)
* **LATTICE**: `.tif` / `.tiff` įrašai — įrašyti naudojant Chloros kameros valdymą arba „LATTICE“ šakotuvą
* **Šviesos jutiklio duomenys**: `.daq` įrašai (DAQ-U/M/E) ir DAQ-M `.csv` žemyn nukreiptų spindulių registravimo duomenys — importuojami kartu su vaizdais, siekiant atlikti atspindžio kalibravimą
{% endhint %}

***

## Vaizdų pridėjimas prie projekto

Yra du pagrindiniai būdai, kaip pridėti vaizdus prie projekto:

### 1-asis būdas: Failų pridėjimas

Naudokite šią parinktį, norėdami importuoti atskirus vaizdo failus arba nedidelį failų rinkinį.

1. Spustelėkite mygtuką **„Pridėti failus“** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line">, esantį viršuje failų naršyklės skydelyje
2. Pereikite į aplanką, kuriame yra jūsų vaizdai
3. Pasirinkite vieną ar daugiau vaizdų failų (laikydami nuspaudę klavišą **Ctrl**, galite pasirinkti kelis failus)
4. Spustelėkite **„Atidaryti“**, kad importuotumėte pasirinktus failus

### 2-asis būdas: Aplanko pridėjimas

Naudokite šią parinktį, jei norite iš karto importuoti visus vaizdus iš vieno aplanko. Viename dialogo lange galite pasirinkti **kelis aplankus**.

1. Spustelėkite mygtuką **„Pridėti aplanką“** „<img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line">“ failų naršyklės skydelio viršuje
2. Pereikite į aplanką (-us), kuriame (-iuose) yra jūsų fotografavimo sesijos nuotraukos, ir juos pasirinkite
3. Spustelėkite **„Pasirinkti aplanką“**, kad importuotumėte visas palaikomas nuotraukas

{% hint style="info" %}
**Pranešama apie failus, kurių nepavyko įkelti.** Jei aplanke yra failų, kuriuos „Chloros“ atpažįsta, bet negali įkelti, apie tai pranešama įspėjimu — vaizdai iš tinklelio nedingsta nepastebimai.
{% endhint %}

***

## „LATTICE Capture“ aplankų importavimas

„LATTICE Capture“ vaizdai išsaugomi **po vieną pakatalogį kiekvienam eksporto lygiui** — pavyzdžiui, `raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/` — su atitinkamu „`.daq`“ žemyn nukreiptu failu šaknies kataloge:

```
output/
├── raw/           capture_<timestamp>_SN<serial>_raw.tif
├── debayered/     capture_<timestamp>_SN<serial>_debayered.tif
├── preview/       capture_<timestamp>_SN<serial>_display.tif
└── *.daq          the downwelling reading matched to the capture
```

**Nurodykite aplanką, esantį „captures“ šakninėje aplankoje** (`output/` aukščiau). Jei pasirinktame aplanke pačiame nėra vaizdų, bet yra pakatalogiai, Chloros automatiškai pereina į juos — visų lygių pakatalogiai ir pagrindinis katalogas `.daq` surenkami vienu kartu.**Kaip importuojami užfiksuoti vaizdai:*** Kiekvienas užfiksuotas vaizdas importuojamas kaip **vienas vaizdas**, sugrupuotas pagal užfiksuotą vaizdą (ne po vieną įrašą kiekviename lygyje). Kiti to paties užfiksuoto vaizdo lygiai rodomi kaip to vieno vaizdo peržiūros režimai.
* **Apdorojimas visada prasideda nuo neapdoroto kadro.** Kiti lygiai yra peržiūrimi, tačiau per apdorojimo grandinę perduodamas tik „`raw`“ – pakartotinis jau apdoroto produkto apdorojimas dvigubai pritaikytų korekcijas, todėl „Chloros“ atmetamas. Pakartotinai importuotas eksportas niekada negali užimti užfiksuoto vaizdo neapdoroto kadro vietos.
* Užfiksuotų vaizdų aplankas, išsaugotas **be** neapdorotų kadrų, rodomas įprastai, tačiau apdorojimo procesas jį praleidžia ir apie tai praneša žurnale. (Žymė „CLI“ „`--input-level`“ gali priversti nustatyti įėjimo tašką šiuo atveju — žr. [„CLI“ nuorodą](../reference/cli-reference.md#what-a-captures-folder-looks-like).)**„LATTICE“ koncentratoriaus sesijos** importuojamos taip pat: nurodykite „Add Folder“ (Pridėti aplanką) į iš koncentratoriaus nukopijuotą sesijos aplanką (jame yra `raw/` ir `previews/`), kartu su bet kokiu DAQ-M `.csv` žemyn nukreiptu žurnalu. Jei kameros ar DAQ kalibravimo duomenys dar nėra išsaugoti jūsų kompiuterio talpykloje, Chloros juos automatiškai atsisiųs pagal serijos numerį importavimo metu (reikės vienkartinio interneto ryšio).***

## Failų naršyklės lentelės supratimas

Importavus vaizdus, jie rodomi lentelėje su šiomis skiltimis:

### Failo pavadinimas

* Originalus failo pavadinimas iš kameros
* Išlaiko kameros pavadinimų sudarymo taisykles (pvz., IMG\_0001.RAW arba capture\_20260816\_101500\_SN213800234\_raw.tif)

### Laiko žyma

* Nuotraukos užfiksavimo data ir laikas
* Išgauta iš nuotraukos EXIF metaduomenų
* Naudojama šviesos jutiklių suderinimui, PPK sinchronizavimui ir kalibravimo taškų planavimui

### Fotoaparato modelis

* Automatiškai nustatyta fotoaparato ir filtro konfigūracija
* Survey3 pavyzdžiai: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* LATTICE pavyzdžiai: LATT-M3M-L41-F550, LATT-M3C-L87-FRGN
* Naudojama teisingiems apdorojimo profiliams taikyti

### Tikslo stulpelis (žymės langelis)

* Pažymėkite šį langelį, jei vaizduose yra kalibravimo taikiniai
* Kai pažymėtas bent vienas vaizdas, **tik pažymėti vaizdai yra nuskaitomi** ieškant taikinų
* Daugiau informacijos rasite skyriuje [Tikslinių vaizdų pasirinkimas](choosing-target-images.md)

### Vaizdo metaduomenų peržiūra

Paspaudus perjungimo mygtuką viršutiniame dešiniajame kampe virš lentelės, vaizdų tinklelio srityje rodomos pasirinktų vaizdų metaduomenys.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Jūsų projekte esantys šviesos jutiklio failai

* Failai „`.daq`“ ir „`.csv`“ rodomi failų naršyklės sąraše, tačiau juos negalima paspausti kaip vaizdus — jie pateikia žemyn nukreiptą spinduliavimo intensyvumą atspindžio kalibravimui.
* Kiekvienas importuotas `.daq`/`.csv` failas yra išvardytas skyriuje **Projekto nustatymai → DAQ šviesos jutiklis**, kur galite peržiūrėti kiekvienam failui taikomą difuzoriaus dangtelio korekciją. Žr. [Projekto nustatymų koregavimas](adjusting-project-settings.md).
* Įrašai, kuriuos atliekate skirtuke **Šviesos jutikliai**, automatiškai pridedami prie atidaryto projekto — rankinio importavimo nereikia.***

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

Spustelėkite bet kurią vaizdo miniatiūrą failų naršyklėje, kad ji būtų parodyta pagrindinėje peržiūros srityje:

1. Vaizdas pasirodo centriniame peržiūros skydelyje
2. Naudokite mastelio reguliavimo valdiklius, kad apžiūrėtumėte vaizdo detales
3. Naršykite tarp vaizdų naudodami rodyklių klavišus

### Greita navigacija

* **Ankstesnis vaizdas**: spustelėkite kairę rodyklę arba paspauskite klavišą ←
* **Kitas vaizdas**: spustelėkite dešinę rodyklę arba paspauskite klavišą →
* **Padidinimas / sumažinimas**: naudokite pelės ratuką arba mastelio keitimo mygtukus
* **Perkėlimas**: padidinus vaizdą, spustelėkite ir vilkite pelę ant vaizdo***

## Darbai su pasikartojančiais failais

Chloros automatiškai aptinka ir ignoruoja pasikartojančius failus:

* Failai su identiškais pavadinimais yra praleidžiami
* Apsaugo nuo netyčinio dvigubo apdorojimo
* Aptikus pasikartojančius failus, rodomas įspėjamasis pranešimas

{% hint style="warning" %}
**Svarbu**: Prieš importuojant nepervardykite ir nemodifikuokite originalių vaizdo failų. Chloros tinkamam apdorojimui naudoja originalius failų pavadinimus ir metaduomenis.
{% endhint %}

***

## Mišrūs kamerų duomenų rinkiniai

Jei jūsų projekte yra vaizdų iš kelių MAPIR kamerų:

1. Chloros automatiškai aptinka kiekvieną kameros modelį — Survey3, LATTICE arba mišinį
2. Kiekvieno kameros tipo duomenys apdorojami naudojant atitinkamą kalibravimo profilį
3. Failų naršyklėje kameros modelis rodomas stulpelyje „Kameros modelis“
4. Apdorojant kiekvienai kamerai sukuriama atskira išvesties aplankų struktūra

**Pavyzdiniai scenarijai**: Survey3W RGN + Survey3N OCN dviejų kamerų konfigūracija, arba „LATTICE“ matrica su pagrindine kamera „RGB“ ir keliais siaurajuosčio dažnio moduliais***

## Geriausia praktika

### Tvarkykite prieš importavimą

* Kalibravimo taikinio vaizdus laikykite toje pačioje aplankėje kaip ir tyrimo vaizdus
* Kiekvienos fotografavimo sesijos `.daq` / `.csv` šviesos jutiklių failus laikykite kartu su tos sesijos vaizdais
* Išsaugokite originalią aplankų struktūrą iš savo kameros / SD kortelės / koncentratoriaus
* Viename projekte nemaišykite skirtingų sesijų duomenų rinkinių

### Failų pavadinimai

* Išsaugokite originalius fotoaparato failų pavadinimus (IMG\_0001.RAW, capture\_... ir pan.)
* Prieš importuojant failų nepervardykite
* Originalūs pavadinimai turi svarbių metaduomenų

### Kalibravimo taikinio vaizdai

* Visada įtraukite 1–2 kalibravimo taikinio vaizdus per sesiją (Survey3; naudojant LATTICE, juos gali pakeisti DAQ įrašas — žr. [Taikinio vaizdų pasirinkimas](choosing-target-images.md))
* Užfiksuokite etalonus prieš ir po fotografavimo sesijos
* Pastatykite etalonus tokiomis pačiomis apšvietimo sąlygomis kaip ir fotografuojamą plotą
* Pažymėkite etaloninius vaizdus, pažymėdami žymės langelį „Target“

***

## Dažniausiai pasitaikančios problemos ir jų sprendimai

### Vaizdai neatsiranda po importavimo

**Galimos priežastys:**

* Nepalaikomas failo formatas (žr. palaikomų tipų sąrašą šio puslapio viršuje)
* Vaizdai gauti iš ne „MAPIR“ kamerų (žr. [Palaikomos kameros](../supported-cameras.md))
* Failas sugadintas arba nevisai perkeltas iš SD kortelės

**Sprendimas**: Patikrinkite failo formato ir fotoaparato modelio suderinamumą bei peržiūrėkite failų įkėlimo įspėjimą, kad nustatytumėte, kurie būtent failai nebuvo įkelti

### Neaptiktas fotoaparato modelis

**Galimos priežastys:**

* Pakeisti EXIF metaduomenys
* Nuotraukos redaguotos išorinėje programinėje įrangoje
* Neužbaigtas failų perkėlimas

**Sprendimas**: Iš naujo importuokite originalius, nepakeistus failus iš fotoaparato / SD kortelės

### Trūksta laiko žymų

**Galimos priežastys:**

* Netinkamai nustatytas fotoaparato laikrodis
* Išorinė programinė įranga pašalino EXIF duomenis

**Sprendimas**: Patikrinkite, ar fotoaparato laiko nustatymai buvo teisingi fotografavimo metu

### Vėl atidarytas projektas praneša apie trūkstamus failus

Jei šaltinio failai buvo perkelti arba ištrinti nuo paskutinio projekto atidarymo, Chloros praneš, **kurie** failai dingo, o ne atidarys tuščią lentelę. Atkurkite failus jų pradiniuose keliuose arba pašalinkite trūkstamus įrašus ir importuokite iš naujo.***

## Tolimesni veiksmai

Kai failai bus importuoti:

1. **Peržiūrėkite failų sąrašą** – įsitikinkite, kad visi vaizdai įkelti teisingai
2. **Patikrinkite fotoaparatų modelius** – įsitikinkite, kad fotoaparatai atpažinti teisingai
3. **Pažymėkite tikslinį vaizdus** – žr. [Tikslinio vaizdo pasirinkimas](choosing-target-images.md)
4. **Nustatykite parametrus** – sukonfigūruokite apdorojimo parinktis [Projekto nustatymuose](adjusting-project-settings.md)
5. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)

Išsamią informaciją apie projekto konfigūraciją rasite skyriuje [Projekto nustatymų koregavimas](adjusting-project-settings.md).
