# Apdorojimo užbaigimas

Kai „Chloros“ baigs apdorojimą, atėjo laikas peržiūrėti rezultatus, patikrinti išvesties kokybę ir paruošti apdorotus vaizdus naudojimui darbo eigoje. Šiame puslapyje rasite gaires, kaip atlikti paskutinius veiksmus ir tolesnius veiksmus.

## Apdorojimo užbaigimo indikatoriai

Sėkmingai užbaigus apdorojimą, pamatysite keletą indikatorių:

* ✅ **Pažangos juosta**: Pasiekia 100 % užbaigimo lygį
* ✅ **Debug Log**: Rodo pranešimą „Processing Complete“
* ✅ **Pradžios mygtukas**: Vėl tampa aktyvus (paruoštas kitam apdorojimo ciklui)
* ✅ **Išvesties failai**: Visi apdoroti vaizdai išsaugoti fotoaparato modelio pakatalogyje***

## Apdorotų vaizdų paieška

### Išvesties aplanko atidarymas

1. Spustelėkite **Pagrindinio meniu** <img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> piktogramą (viršuje kairėje)
2. Pasirinkite **„Atidaryti projekto aplanką“**

3. Failų naršyklė atidarys projekto katalogą
4. Suraskite savo projektą pagal pavadinimą

***

## Apdorotų vaizdų peržiūra

### Greitas peržiūrėjimas failų naršyklėje

**Windows integruota peržiūra:**

1. Pereikite į kameros modelio pakatalogį
2. Pasirinkite vaizdo failą
3. Peržiūra pasirodo Windows Explorer peržiūros lange
4. Naudokite rodyklių klavišus, kad peržiūrėtumėte vaizdus

### Peržiūra išorinėse vaizdų peržiūros programose

**Rekomenduojamos peržiūros programos:*** **QGIS** – nemokama GIS programinė įranga (geriausia georeferencinei multispektrinei analizei)
* **IrfanView** – greita, lengva vaizdų peržiūros programa (palaiko TIFF)
* **Adobe Photoshop** – profesionalus redagavimas (palaiko TIFF)
* **GIMP** – nemokama alternatyva Photoshop
* **Windows Photos** – pagrindinis peržiūrėjimas (gali nepalaikyti 16 bitų TIFF)

### Peržiūra „Chloros“ vaizdų peržiūros programoje

Naudokite „Chloros“ integruotą vaizdų peržiūros programą išplėstinei vizualizacijai:

1. Spustelėkite vaizdo miniatiūrą failų naršyklėje
2. Vaizdas atsidarys pagrindinėje peržiūros srityje
3. Spustelėkite **Vaizdų peržiūros programa** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> skirtuką kairėje šoninėje juostoje
4. Naudokite [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) interaktyviai analizei

Išsamias instrukcijas rasite [Image Viewer](../image-viewer-gui/opening-an-image-full-screen.md).

***

## Debug Log peržiūra

### Patikrinkite, ar nėra įspėjimų ar klaidų

1. Atidarykite **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> skirtuką
2. Peržiūrėkite pranešimus
3. Ieškokite geltonų įspėjimų arba raudonų klaidų
4. Peržiūrėkite visas pastebėtas problemas
5. Kreipkitės į MAPIR pagalbos tarnybą, jei reikia pagalbos

### Žurnalo išsaugojimas

Norėdami išsaugoti apdorojimo įrašą arba nusiųsti jį MAPIR pagalbos tarnybai:

1. Spustelėkite mygtuką **„Kopijuoti“**arba**„Atsisiųsti“**

2. Išsaugokite kaip tekstinį failą projekto aplanke
3. Pridėkite prie projekto dokumentacijos
4. Jei susidūrėte su problemomis, nusiųskite MAPIR palaikymo tarnybai

***

## Dažnos išvesties problemos ir sprendimai

### Problema: Trūksta išvesties failų

**Galimos priežastys:**

* Failai neatitiko apdorojimo kriterijų
* Tik tiksliniai vaizdai (neįtraukti į eksportą)
* Eksporto metu baigėsi vietos diske
* Failų sugadinimas apdorojimo metu

**Sprendimai:**

1. Patikrinkite „Debug Log“ dėl praleidimo/klaidų pranešimų
2. Patikrinkite, ar buvo pakankamai vietos diske
3. Suskaičiuokite failus: skaičius turėtų sutapti su (pradinis skaičius – tikslinis skaičius) × (indeksai + 1)
4. Pakartotinai importuokite ir apdorokite trūkstamus failus

### Problema: Tamsūs arba šviesūs kraštai (vis dar matomas vinjetavimas)

**Galimos priežastys:**

* Vinjetavimo korekcija išjungta
* Fotoaparatas/objektyvas nėra Chloros profilių duomenų bazėje
* Ekstremalus vinjetavimas, kurio negalima pakoreguoti

**Sprendimai:**

1. Patikrinkite, ar vinjetavimo korekcija buvo įjungta projekto nustatymuose
2. Patikrinkite, ar fotoaparato modelis buvo teisingai atpažintas
3. Jei vinjetavimas išlieka, susisiekite su MAPIR palaikymo tarnyba

### Problema: Neteisingos spalvos arba vertės

**Galimos priežastys:**

* Nėra aptiktų kalibravimo taškų
* Pasirinkta neteisinga kalibravimo taškų modelis
* Atspindžio kalibravimas išjungtas
* Prastos kokybės taškų vaizdai

**Sprendimai:**

1. Patikrinkite, ar atspindžio kalibravimas buvo įjungtas
2. Patikrinkite „Tikslas rastas“ pranešimus „Debug Log“
3. Peržiūrėkite tikslo vaizdo kokybę
4. Pakartotinai apdorokite, pažymėdami tinkamus tikslus

### Problema: NDVI reikšmės atrodo neteisingos

**Numatomi NDVI intervalai:*** **Vanduo, akmenys, dirvožemis**: nuo -0,1 iki 0,2
* **Retas/nesveikas augmenija**: nuo 0,2 iki 0,4
* **Vidutinė augmenija**: nuo 0,4 iki 0,6
* **Sveika, tanki augmenija**: nuo 0,6 iki 0,9**Jei vertės yra už šių ribų:**

1. Patikrinkite, ar buvo pritaikytas atspindžio kalibravimas
2. Patikrinkite, ar buvo įtrauktas šviesos jutiklio žurnalas
3. Patikrinkite, ar buvo aptikti kalibravimo taškai
4. Įsitikinkite, kad buvo aptiktas teisingas kameros modelis
5. Peržiūrėkite taškų vaizdų fiksavimo laiką ir sąlygas

***

## Apdorotų vaizdų naudojimas

### Fotogrametrijai / ortomozaikos kūrimui

**Rekomenduojama darbo eiga:**

1.**Importuokite kalibruotus atspindžio vaizdus** į fotogrametrijos programinę įrangą:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **Išsaugokite EXIF metaduomenis**: užtikrinkite, kad GPS duomenys būtų išsaugoti geotagavimui
3. **Kalibruotos darbo eigos**: naudokite atspindžio vaizdus mokslinio tikslumo užtikrinimui
4. **Apdorokite indeksines mozaikas**: Sukurkite NDVI ortomozaikas iš atskirų indeksinių vaizdų
5. **Eksportuokite georeferencinius GeoTIFF**: Naudojimui GIS programose

### GIS analizei

**Rekomenduojama darbo eiga:**

1.**Įkelkite į QGIS, ArcGIS ar panašią programą**

2.**Naudokite 16 bitų TIFF** atspindžio vaizdus daugiajuostinei analizei
3. **Naudokite indeksinius vaizdus** (NDVI, NDRE) kaip paruoštus naudoti augmenijos sluoksnius
4. **Rastro skaičiuoklė**: sujunkite juostas individualiai analizei
5. **Eksportavimas**: kurkite klasifikavimo žemėlapius, pokyčių aptikimą, augmenijos būklės žemėlapius

### Tiesioginei analizei / ataskaitoms

**Rekomenduojamas darbo srautas:**

1.**Naudokite indeksinius vaizdus su LUT spalvomis** vizualiosioms ataskaitoms
2. **Išgaukite statistinius duomenis**: vidutinis NDVI kiekvienam laukui/sklypui
3. **Laiko eilutės**: palyginkite indeksus per kelis sesijų laikotarpius
4. **Sukurkite ataskaitas**: įtraukite žemėlapius, statistinius duomenis ir vizualizacijas***

## Archyvavimas ir atsarginės kopijos

### Rekomenduojama atsarginių kopijų strategija

**Ką išsaugoti:*** ✅ **Originalūs RAW/JPG vaizdai** – archyvuokite atskirame diske/debesyje
* ✅ **Apdoroti rezultatai** – išsaugokite kalibruotus vaizdus ir indeksus
* ✅ **Projekto failas** – jame yra visi nustatymai, reikalingi pakartotiniam apdorojimui, jei to prireiktų
* ✅ **Debug Log** – apdorojimo detalės
* ✅ **Kalibravimo taikinio vaizdai** – patikrinimui ir pakartotiniam apdorojimui**Rekomendacijos dėl saugojimo:*** **Greita atsarginė kopija**: Išorinis kietasis diskas
* **Ilgalaikis archyvas**: Debesų saugykla (Google Drive, Dropbox ir pan.)
* **Svarbūs duomenys**: Laikykite 2–3 kopijas skirtingose vietose***

## Kiti apdorojimo ciklai

### Projekto nustatymų pakartotinis naudojimas

Jei ateityje apdorosite panašius duomenų rinkinius:

1. **Išsaugokite projekto šabloną** (jei dar to nepadarėte)
2. **Sukurkite naują projektą** naudodami išsaugotą šabloną
3. **Importuokite naujus vaizdus**

4.**Apdorokite**naudodami identiškus nustatymus, kad būtų užtikrintas nuoseklumas

### Daugelio sesijų apdorojimas partijomis

Daugeliui sesijų / duomenų rinkinių:**1 variantas: GUI – keli projektai**

* Sukurkite atskirą projektą kiekvienai sesijai
* Naudokite nuoseklius šablono nustatymus
* Apdorokite po vieną

**2 variantas: Chloros CLI (tik Chloros+)**

* Automatizuokite paketinį apdorojimą
* Apdorokite kelis aplankus naudodami skriptus
* Žr. [CLI dokumentaciją](../CLI.md)

**3 variantas: Python SDK (tik Chloros+)**

* Programinis valdymas
* Integracija su analizės procesais
* Žr. [API dokumentaciją](../api-python-sdk.md)

***

## Problemų sprendimas po apdorojimo

### Pakartotinis apdorojimas su kitomis nustatymomis

Jei rezultatai nėra patenkinami:

1. Išsaugokite originalius vaizdus (niekada neištrinkite)
2. Atidarykite tą patį projektą Chloros
3. Pakoreguokite nustatymus projekto nustatymų skydelyje
4. Apdorokite dar kartą – rezultatai perrašys ankstesnius rezultatus

### Vaizdų dalies apdorojimas

Norėdami pakartotinai apdoroti tik tam tikrus vaizdus:

1. Sukurkite naują projektą
2. Importuokite tik tuos vaizdus, kuriuos reikia pakartotinai apdoroti
3. Naudokite tą patį nustatymų šabloną
4. Apdorokite mažesnį duomenų rinkinį

### Pagalba

Jei susiduriate su problemomis:

* 📧 **El. paštas**: info@mapir.camera (pridėkite „Debug Log“)
* 🌐 **Pagalba**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **DUK**: [Dažnai užduodami klausimai](../faq.md)
* 📖 **Dokumentacija**: [Chloros vadovas](../)***

## Santrauka: užbaigtas darbo srautas

Jūs baigėte visą Chloros apdorojimo darbo eigą:

1. ✅ **Sukurtas projektas** – Žr. [Projektai](../projects.md)
2. ✅ **Pridėti failai** – žr. [Failų pridėjimas](adding-files-to-a-project.md)
3. ✅ **Nustatyti parametrai** – žr. [Projekto parametrų nustatymas](adjusting-project-settings.md)
4. ✅ **Pažymėti tikslai** – žr. [Tiksliniai vaizdai](choosing-target-images.md)
5. ✅ **Pradėtas apdorojimas** – žr. [Apdorojimo pradžia](starting-the-processing.md)
6. ✅ **Stebėta pažanga** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)
7. ✅ **Peržiūrėti rezultatai** – ši puslapis**Jūsų kalibruoti, atspindžio koreguoti multispektriniai vaizdai yra paruošti analizei!**

***

## Papildomi ištekliai

### Išplėstinės funkcijos

* [**Vaizdų peržiūros programa**](../image-viewer-gui/opening-an-image-full-screen.md) – Interaktyvus vizualizavimas ir analizė
* [**Indeksų/LUT bandymų aplinka**](../image-viewer-gui/index-lut-sandbox.md) – Individualių indeksų testavimas
* [**Daugiaspektrinių indeksų formulės**](../project-settings/multispectral-index-formulas.md) – išsamus indeksų žinynas

### Automatizavimas ir integracija

* [**CLI dokumentacija**](../CLI.md) – komandų eilutės paketinis apdorojimas
* [**Python SDK**](../api-python-sdk.md) – Programinė automatizacija
* [**Chloros+ Funkcijos**](../#chloros) – Išplėstinės apdorojimo galimybės

### Pagalba ir mokymasis

* [**DUK**](../faq.md) – Atsakymai į dažniausiai užduodamus klausimus
* [**Kalibravimo taikiniai**](../calibration-targets.md) – Atspindžio kalibravimo supratimas
* [**Palaikomos kameros**](../supported-cameras.md) – Suderinama įranga
