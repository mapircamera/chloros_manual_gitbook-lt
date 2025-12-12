# Apdorojimo užbaigimas

Kai Chloros užbaigs apdorojimą, atėjo laikas peržiūrėti rezultatus, patikrinti išvesties kokybę ir paruošti apdorotus vaizdus naudoti darbo eigoje. Šiame puslapyje pateikiami galutiniai žingsniai ir tolesni veiksmai.

## Apdorojimo užbaigimo indikatorius

Sėkmingai užbaigus apdorojimą, matysite kelis indikatorius:

* ✅ **Pažangos juosta**: pasiekia 100 % užbaigtumą
* ✅ **Debug log**: rodo pranešimą „Processing Complete“ (Apdorojimas užbaigtas)
* ✅ **Pradžios mygtukas**: vėl tampa aktyvus (paruoštas kitam apdorojimo ciklui)
* ✅ **Išvesties failai**: visi apdoroti vaizdai išsaugomi fotoaparato modelio pakatalogyje

***

## Apdorotų vaizdų paieška

### Išvesties aplanko atidarymas

1. Spustelėkite **Pagrindinis meniu** <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> (viršutiniame kairiajame kampe)
2. Pasirinkite **„Atidaryti projekto aplanką“**
3. Jūsų failų naršyklė atidarys projekto katalogą
4. Suraskite savo projektą pagal pavadinimą

***

## Apdorotų vaizdų peržiūra

### Greitas peržiūrėjimas failų naršyklėje

**Windows integruota peržiūra:**

1. Pereikite į fotoaparato modelio pakatalogį
2. Pasirinkite vaizdo failą
3. Peržiūra atsiras Windows Explorer peržiūros lange
4. Naudokite rodyklių klavišus, kad peržiūrėtumėte vaizdus

### Peržiūra išorinėse vaizdų peržiūros programose

**Rekomenduojamos peržiūros programos:**

* **QGIS** – nemokama GIS programinė įranga (geriausiai tinka georeferencinei multispektrinei analizei)
* **IrfanView** – greita, lengva vaizdų peržiūros programa (palaiko TIFF)
* **Adobe Photoshop** – profesionalus redagavimas (TIFF palaikymas)
* **GIMP** – nemokama alternatyva Photoshop
* **Windows Photos** – pagrindinis peržiūrėjimas (gali nepalaikyti 16 bitų TIFF)

### Peržiūra Chloros vaizdų peržiūros programoje

Naudokite Chloros integruotą vaizdų peržiūros programą išsamiam vaizdų peržiūrėjimui:

1. Spustelėkite vaizdo miniatiūrą failų naršyklėje.
2. Vaizdas atsidarys pagrindiniame peržiūros lange.
3. Spustelėkite **Vaizdų peržiūros programa** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> skirtuką kairėje šoninėje juostoje.
4. Naudokite [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) interaktyviai analizei.

Išsamias instrukcijas rasite [Image Viewer](../image-viewer-gui/opening-an-image-full-screen.md).

***

## Debug log peržiūra

### Patikrinkite įspėjimus ar klaidas

1. Atidarykite **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> skirtuką
2. Peržiūrėkite pranešimus
3. Ieškokite geltonų įspėjimų arba raudonų klaidų
4. Peržiūrėkite visas pažymėtas problemas
5. Kreipkitės į MAPIR pagalbos tarnybą

### Žurnalo išsaugojimas

Norėdami išsaugoti apdorojimo įrašą arba nusiųsti jį MAPIR pagalbos tarnybai:

1. Spustelėkite mygtuką **„Kopijuoti“** arba **„Atsisiųsti“**
2. Išsaugokite kaip tekstinį failą projekto aplanke
3. Pridėkite prie projekto dokumentacijos
4. Jei kyla problemų, nusiųskite MAPIR palaikymo tarnybai

***

## Dažnos išvesties problemos ir sprendimai

### Problema: trūksta išvesties failų

**Galimos priežastys:**

* Failai neatitiko apdorojimo kriterijų.
* Tik tiksliniai vaizdai (neįtraukti į eksportą).
* Eksporto metu baigėsi disko vieta.
* Failų sugadinimas apdorojimo metu.

**Sprendimai:**

1. Patikrinkite Debug Log, ar nėra praleidimo/klaidų pranešimų.
2. Patikrinkite, ar buvo pakankamai disko vietos.
3. Suskaičiuokite failus: turėtų sutapti (pirminis skaičius – tikslinis skaičius) × (indeksai + 1)
4. Pakartotinai importuokite ir apdorokite trūkstamus failus.

### Problema: tamsūs arba šviesūs kraštai (vis dar matomas vinjetavimas)

**Galimos priežastys:**

* Vinjetavimo korekcija išjungta.
* Fotoaparatas/objektyvas nėra Chloros profilio duomenų bazėje.
* Ekstremalus vinjetavimas, kurio neįmanoma pakoreguoti.

**Sprendimai:**

1. Patikrinkite, ar projekto nustatymuose įjungtas vinjetės koregavimas.
2. Patikrinkite, ar teisingai nustatytas fotoaparato modelis.
3. Jei vinjetė išlieka, susisiekite su MAPIR pagalbos tarnyba.

### Problema: neteisingos spalvos arba vertės

**Galimos priežastys:**

* Nėra aptiktų kalibravimo tikslų.
* Pasirinkta neteisinga kalibravimo tikslo modelis.
* Atspindžio kalibravimas išjungtas.
* Blogos kokybės tikslo vaizdai.

**Sprendimai:**

1. Patikrinkite, ar įjungtas atspindžio kalibravimas.
2. Patikrinkite „Tikslas rastas“ pranešimus Debug Log.
3. Patikrinkite tikslo vaizdo kokybę.
4. Pakartotinai apdorokite, pažymėdami tinkamus tikslus.

### Problema: NDVI reikšmės atrodo neteisingos

**Tikėtini NDVI diapazonai:**

* **Vanduo, akmenys, dirvožemis**: nuo -0,1 iki 0,2
* **Retas/nesveikas augmenija**: nuo 0,2 iki 0,4
* **Vidutinis augmenija**: nuo 0,4 iki 0,6
* **Sveika, tanki augmenija**: nuo 0,6 iki 0,9

**Jei vertės neatitinka šių intervalų:**

1. Patikrinkite, ar buvo taikytas atspindžio kalibravimas.
2. Patikrinkite, ar buvo įtrauktas šviesos jutiklio žurnalas.
3. Patikrinkite, ar buvo aptikti kalibravimo taškai.
4. Įsitikinkite, kad buvo aptiktas teisingas fotoaparato modelis.
5. Peržiūrėkite taško vaizdo užfiksavimo laiką ir sąlygas.

***

## Apdorotų vaizdų naudojimas

### Fotogrametrijai / ortomozaiikos kūrimui

**Rekomenduojamas darbo eiga:**

1. **Importuokite kalibruotus atspindžio vaizdus** į fotogrametrijos programinę įrangą:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **Išsaugokite EXIF metaduomenis**: užtikrinkite, kad GPS duomenys būtų išsaugoti geotaggingui
3. **Kalibruoti darbo srautai**: naudokite atspindžio vaizdus mokslinio tikslumo užtikrinimui
4. **Apdorokite indeksų mozaikas**: Sukurkite NDVI ortomozaiikas iš atskirų indeksų vaizdų
5. **Eksportuokite georeferencinius GeoTIFF**: naudoti GIS programose

### GIS analizei

**Rekomenduojamas darbo eiga:**

1. **Įkelkite į QGIS, ArcGIS ar panašią programą**
2. **Naudokite 16 bitų TIFF** atspindžio vaizdus daugiabandinei analizei
3. **Naudokite indeksinius vaizdus** (NDVI, NDRE) kaip paruoštus naudoti augmenijos sluoksnius
4. **Rastro skaičiuoklė**: sujunkite juostas individualiai analizei
5. **Eksportuokite**: kurkite klasifikavimo žemėlapius, keitimo aptikimą, augmenijos sveikatos žemėlapius.

### Tiesioginei analizei / ataskaitoms

**Rekomenduojamas darbo srautas:**

1. **Naudokite indeksinius vaizdus su LUT spalvomis** vizualinėms ataskaitoms.
2. **Išgaukite statistinius duomenis**: vidutinis NDVI pagal lauką / sklypą.
3. **Laiko eilutės**: palyginkite indeksus per kelis sesijos
4. **Sukurkite ataskaitas**: įtraukite žemėlapius, statistinius duomenis ir vizualizacijas

***

## Archyvavimas ir atsarginės kopijos

### Rekomenduojama atsarginių kopijų strategija

**Ką išsaugoti:**

* ✅ **Originalius RAW/JPG vaizdus** – archyvuokite atskirame diske/debesyje
* ✅ **Apdoroti rezultatai** – išsaugokite kalibruotus vaizdus ir indeksus
* ✅ **Projekto failas** – jame yra visi nustatymai, reikalingi pakartotiniam apdorojimui, jei to prireiktų
* ✅ **Debug Log** – dokumentuoja apdorojimo detales
* ✅ **Kalibravimo tiksliniai vaizdai** – tikrinimui ir pakartotiniam apdorojimui

**Rekomendacijos dėl saugojimo:**

* **Nedelsiant atlikite atsarginę kopiją**: išorinis kietasis diskas
* **Ilgalaikis archyvavimas**: saugojimas debesyje (Google Drive, Dropbox ir kt.)
* **Svarbūs duomenys**: laikykite 2–3 kopijas skirtingose vietose

***

## Kiti apdorojimo ciklai

### Projekto nustatymų pakartotinis naudojimas

Jei ateityje apdorosite panašius duomenų rinkinius:

1. **Išsaugokite projekto šabloną** (jei dar to nepadarėte)
2. **Sukurkite naują projektą** naudodami išsaugotą šabloną
3. **Importuokite naujus vaizdus**
4. **Apdorokite** naudodami identiškus nustatymus, kad būtų užtikrintas nuoseklumas

### Daugių sesijų apdorojimas partijomis

Daugioms sesijoms/duomenų rinkiniams:

**1 variantas: GUI – keli projektai**

* Sukurkite atskirą projektą kiekvienai sesijai.
* Naudokite nuoseklius šablono nustatymus.
* Apdorokite po vieną.

**2 variantas: Chloros CLI (tik Chloros+)**

* Automatizuokite paketinį apdorojimą.
* Apdorokite kelis aplankus naudodami scenarijus.
* Žr. [CLI dokumentaciją](../CLI.md)

**3 variantas: Python SDK (tik Chloros+)**

* Programinis valdymas
* Integracija su analizės procesais
* Žr. [API dokumentaciją](../api-python-sdk.md)

***

## Problemų sprendimas po apdorojimo

### Pakartotinis apdorojimas su kitokiais nustatymais

Jei rezultatai nėra patenkinami:

1. Išsaugokite originalias nuotraukas (niekada neištrinkite)
2. Atidarykite tą patį projektą Chloros
3. Nustatykite parametrus projekto nustatymų skydelyje
4. Apdorokite dar kartą – rezultatai bus perrašyti ankstesniais rezultatais

### Vaizdų pogrupio apdorojimas

Norėdami pakartotinai apdoroti tik tam tikrus vaizdus:

1. Sukurkite naują projektą
2. Importuokite tik tuos vaizdus, kuriuos reikia pakartotinai apdoroti
3. Naudokite tą patį nustatymų šabloną
4. Apdorokite mažesnį duomenų rinkinį

### Pagalba

Jei susiduriate su problemomis:

* 📧 **El. paštas**: info@mapir.camera (pridėkite Debug Log)
* 🌐 **Pagalba**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **DUK**: [Dažnai užduodami klausimai](../faq.md)
* 📖 **Dokumentacija**: [Chloros vadovas](../)

***

## Santrauka: užbaigtas darbo srautas

Dabar baigėte visą Chloros apdorojimo darbo eigą:

1. ✅ **Sukurtas projektas** – žr. [Projektai](../projects.md)
2. ✅ **Pridėti failai** – žr. [Failų pridėjimas](adding-files-to-a-project.md)
3. ✅ **Pritaikyti nustatymai** – žr. [Projekto nustatymų pritaikymas](adjusting-project-settings.md)
4. ✅ **Pažymėti tikslai** – žr. [Tikslinės nuotraukos pasirinkimas](choosing-target-images.md)
5. ✅ **Pradėtas apdorojimas** – žr. [Apdorojimo pradžia](starting-the-processing.md)
6. ✅ **Stebėta pažanga** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)
7. ✅ **Peržiūrėti rezultatai** – Ši puslapis

**Jūsų kalibruoti, atspindžio koreguoti daugiaspektriniai vaizdai yra paruošti analizės!**

***

## Papildomi ištekliai

### Išplėstinės funkcijos

* [**Vaizdų peržiūros programa**](../image-viewer-gui/opening-an-image-full-screen.md) – Interaktyvus vizualizavimas ir analizė
* [**Indeksas/LUT smėlio dėžė**](../image-viewer-gui/index-lut-sandbox.md) – individualus indekso testavimas
* [**Daugiaspektrinės indekso formulės**](../project-settings/multispectral-index-formulas.md) – išsamus indekso žinynas

### Automatizavimas ir integracija

* [**CLI dokumentacija**](../CLI.md) – komandinės eilutės paketinis apdorojimas
* [**Python SDK**](../api-python-sdk.md) – Programinė automatizacija
* [**Chloros+ funkcijos**](../#chloros) – Išplėstos apdorojimo galimybės

### Pagalba ir mokymasis

* [**DUK**](../faq.md) – atsakymai į dažniausiai užduodamus klausimus
* [**Kalibravimo tikslai**](../calibration-targets.md) – atspindžio kalibravimo supratimas
* [**Palaikomos kameros**](../supported-cameras.md) – Suderinama įranga
