# Apdorojimo užbaigimas

Kai „Chloros“ užbaigs apdorojimą, atėjo laikas peržiūrėti rezultatus, patikrinti išvesties kokybę ir paruošti apdorotus vaizdus naudojimui jūsų darbo eigoje. Šiame puslapyje pateikiami nurodymai, kaip atlikti paskutinius veiksmus ir tolesnius veiksmus.

## Apdorojimo užbaigimo rodikliai

Sėkmingai užbaigus apdorojimą, matysite keletą rodiklių:

* ✅ **Pažangos juosta**: pasiekia 100 % užbaigtumo
* ✅ **Debug Log**: rodo paskutines `[RUN-SUMMARY]` eilutes su skaičiais (vaizdai, kamerų grupės, taikiniai, kalibruoti vaizdai, įrašyti failai)
* ✅ **Pradžios mygtukas**: vėl tampa aktyvus (paruoštas kitam apdorojimo ciklui)
* ✅ **Išvesties failai**: Visi apdoroti vaizdai išsaugomi projekto išvesties medžio struktūroje (žemiau)

{% hint style="warning" %}
**Apdorojimo ciklas, kurio metu neįrašoma jokių vaizdų, laikomas nesėkme.** Jei užsisakėte vaizdų produktus, o apdorojimo ciklas jų neįrašė, Chloros praneša apie nesėkmę — `[RUN-SUMMARY]` žurnalo pavadinime nurodo galimą priežastį (nieko neįkelta, neaptiktas joks taikinys arba visi užsakyti produktai praleisti kaip netinkami). CLI ekvivalentas baigiasi nelygiu nuliui rezultatu. Sąmoningas vykdymas, skirtas tik metaduomenims (visi eksporto produktai išjungti, nėra indeksų), vis tiek laikomas sėkmingu. Žr. [CLI nuorodą](../reference/cli-reference.md#a-run-that-writes-no-images-fails).
{% endhint %}

***

## Apdorotų vaizdų paieška

### Išvesties aplanko atidarymas

1. Spustelėkite **Pagrindinio meniu** piktogramą „<img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">“ (viršuje kairėje)
2. Pasirinkite **„Atidaryti projekto aplanką“**

3. Atsidarys failų naršyklė, rodanti projekto katalogą
4. Suraskite savo projektą pagal pavadinimą

### Išvesties medis

Produktai įrašomi **projekto aplanke, sugrupuoti pagal fotoaparatą, o po to – pagal failo formatą**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per selected index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

* **Fotoaparato aplankas**: `LATT-<sensor>-<lens>-F<filter>` – „LATTICE“ (atitinka nuotraukos EXIF duomenis `Model`), `<model>_<filter>` – skirtas „Survey3“ (pvz., „`Survey3N_RGN`“). Dvi kameros, turinčios tą patį jutiklį ir filtrą, bet besiskiriančios objektyvu, turi atskiras medžio struktūras – skiriasi vinjetė, matymo laukas ir iškraipymas.
* **Formato aplankas**: atitinka jūsų eksporto formato nustatymus — `tiff16`, `tiff8`, `png8`, `jpg8` arba `tiff32`, pvz., TIFF (32 bitai, procentais). Spinduliavimas visada yra „float32“ tipo ir visada priskiriamas `tiff32`.
* **Produkto aplankai**:
  * `Reflectance_Calibrated_Images/` — kalibruotas atspindžio koeficientas
  * `Debayered_Images/` — linijinis debayeringas (LATTICE)
  * `Preview_Images/` — peržiūra ekrane (LATTICE)
  * `Radiance_Images/` — „float32“ spektrinis spinduliavimas, W/m²/sr/nm (LATTICE daugiaspektrinis)
  * `Vignette_Corrected_Images/` **arba** `Sensor_Response_Images/` — nekalibruotas atsarginis variantas kadrams be atspindžio etalono; kiekvienam apdorojimo ciklui būna tik vienas iš šių dviejų variantų, pasirinktas pagal „Vignette“ korekcijos nustatymą
  * `<INDEX>_Index_Images/` — po vieną aplanką kiekvienam pasirinktam indeksui (pvz., `NDVI_Index_Images`)

{% hint style="info" %}
**Kiekvienas eksportuotas produktas išlaiko ŠALTINIO failo pavadinimą.**„`capture_..._raw.tif`“ spinduliavimo eksportas vis dar vadinamas „`capture_..._raw.tif`“ — jis tiesiog yra „`tiff32/Radiance_Images/`“ aplanke.**Produktą identifikuoja aplankas, o ne failo pavadinimas**, todėl ieškant `*radiance*.tif` nieko nerandama; vietoj to ieškokite pagal aplanką.
{% endhint %}



<!-- SCREENSHOT-NEEDED: Windows Explorer open on a processed project folder showing the tree: a LATT-… camera folder expanded with tiff16 (Reflectance_Calibrated_Images, Debayered_Images, Preview_Images, NDVI_Index_Images) and tiff32 (Radiance_Images) subfolders visible -->### Kiek failų turėtų būti?

Neskaičiuokite pagal formulę — išvesties skaičius priklauso nuo to, kurie produktai buvo įjungti ir kurie taikomi kiekvienai kamerai (pvz.pvz., RGB kameros negauna spinduliavimo/atspindžio duomenų). Tikslus skaičius nurodytas žurnale: paskutinėje eilutėje „`[RUN-SUMMARY]`“ tiksliai nurodoma, kiek failų buvo įrašyta, o paaiškinamosiose eilutėse paaiškinama, kas buvo praleista.

***

## Apdorotų vaizdų peržiūra

### Greita peržiūra failų naršyklėje

**Windows integruota peržiūra:**

1. Pereikite į produkto aplanką (pvz., `tiff16/Reflectance_Calibrated_Images/`)
2. Pasirinkite vaizdo failą
3. Peržiūra pasirodo „Windows Explorer“ peržiūros lange
4. Naudokite rodyklių klavišus, kad peržiūrėtumėte vaizdus

### Peržiūra išorinėse vaizdų peržiūros programose

**Rekomenduojamos peržiūros programos:*** **QGIS** – nemokama GIS programinė įranga (geriausiai tinka georeferencinei multispektrinei analizei)
* **IrfanView** – greita, lengva vaizdų peržiūros programa (palaiko TIFF)
* **Adobe Photoshop** – profesionali redagavimo programa (palaiko TIFF)
* **GIMP** – nemokama „Photoshop“ alternatyva
* **Windows Photos** – pagrindinės peržiūros funkcijos (gali nepalaikyti 16 bitų TIFF)

### Peržiūra „Chloros“ vaizdų peržiūros programoje

Naudokite „Chloros“ integruotą „Image Viewer“ išplėstiniam peržiūrėjimui:

1. Spustelėkite vaizdo miniatiūrą failų naršyklėje
2. Vaizdas atsidarys pagrindinėje peržiūros srityje
3. Spustelėkite kairiajame šoniniame meniu esantį skirtuką **„Image Viewer“** „<img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">“
4. Interaktyviai analizei naudokite [Indekso/LUT smėlio dėžę](../image-viewer-gui/index-lut-sandbox.md)

Išsamias instrukcijas rasite [vaizdų peržiūros programoje](../image-viewer-gui/opening-an-image-full-screen.md).

***

## Atspindžio pikselių verčių skaitymas (GIS / Pix4D / Skriptai)

Atspindys saugomas kaip sveikasis skaičius DN, o **DN, reiškiantis ρ = 1,0, priklauso nuo šaltinio kameros**:

| Šaltinis          | ρ = 1,0 yra | Kaip nustatyti                                        |
| --------------- | ---------- | -------------------------------------------------- |
| LATTICE (M3C/M3M) | **32768** (rezervas iki ρ 2,0) | Failui priskirta XMP žyma `Chloros:PixelScale=32768` |
| Survey3         | **65535** (apkarpyta ties ρ 1,0)     | Nėra `Chloros:*` XMP žymių — šis trūkumas yra signalas |

**Perskaitykite `Chloros:PixelScale` žymą ir padalinkite iš jos**, o ne laikykitės bendros 65535 reikšmės — padalinus LATTICE atspindžio koeficientą iš 65535, kiekviena reikšmė tyliai sumažinama perpus. Vienas kraštutinis atvejis pagal projektą neturi mastelio: 8 bitų šaltinio įrašas, užrašytas kaip 8 bitų išvestis, yra apribojamas, o ne perskaičiuojamas, ir sąmoningai negauna mastelio žymės — vietoj dalybos eksportuokite iš naujo 16 bitų arba 32 bitų formatu. Išsamią informaciją rasite skyriuje [Išvesties vaizdo formatai](../output-image-formats.md).***

## Į eksportuojamus failus perkeliami metaduomenys

Kiekvienas produktas išlaiko šaltinio įrašo **GPS bloką**ir jo**EXIF sub-IFD**, todėl
eksportuojant perkeliami `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` ir
`CameraSerialNumber`, taip pat georeferencinius duomenis.

{% hint style="warning" %}
**Jei ortomozaika gaunama absurdišku masteliu, pirmiausia patikrinkite `FocalLength`.**
„Pix4D“ apskaičiuoja atstumą tarp taškų ant žemės pagal židinio nuotolį ir aukštį. Be šio žymės
programa grįžta prie visiškai neteisingo mastelio — vieno skrydžio, kurio metu buvo padaryti 49 kadrai, metu 411 m × 160 m
apelsinų giraitė buvo atkurta kaip 47,8 km × 13 km, sukuriant 455 megapikselių ortomoziką, kurioje daugiausia
tuščia erdvė. Lėtas mozaikos sudėliojimas ir netikėtai didelis failo dydis yra šio reiškinio simptomai, o ne atskiros
problemos.

```bash
exiftool -FocalLength -GPSLatitude "YourProject/.../some_export.tif"
```
{% endhint %}

Ne *visos* žymos yra kopijuojamos. IFD0 struktūrinės žymos sąmoningai nekopijuojamos (jų kopijavimas
sugadina LATTICE išvestį), o `ExifImageWidth` / `ExifImageHeight` yra neįtraukiamos,
nes jos apibūdina pradinį įrašą — kitaip eksportuotas failas, kurio dydis buvo pakeistas,
nurodytų matmenis, kurie prieštarautų jo pačio rastrui.

***

## Debugavimo žurnalo peržiūra

### Įspėjimų ar klaidų paieška

1. Atidarykite **Debug Log** skirtuką „<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">“
2. Peržiūrėkite pranešimus
3. Ieškokite geltonų įspėjimų arba raudonų klaidų
4. Perskaitykite `[RUN-SUMMARY]` eilutes ir visus patarimus
5. Kreipkitės į „MAPIR“ pagalbos tarnybą dėl pagalbos

### Žurnalo išsaugojimas

Norėdami išsaugoti apdorojimo įrašą arba nusiųsti jį „MAPIR“ pagalbos tarnybai:

1. Spustelėkite mygtuką **„Kopijuoti“**arba**„Atsisiųsti“**

2. Išsaugokite kaip tekstinį failą projekto aplanke
3. Pridėkite prie projekto dokumentacijos
4. Jei iškilo problemų, nusiųskite į „MAPIR“ pagalbos tarnybą

***

## Dažnos išvesties problemos ir jų sprendimai

### Problema: Trūksta išvesties failų

**Galimos priežastys:**

* Produktas netaikomas tai kamerai (pvz., spinduliavimo/atspindžio koeficientai RGB kameroms — tai nurodyta žurnale)
* Trūko reikiamos nuorodos (pvz., atspindžio koeficientas be taikinio ir be `.daq` žemyn nukreipto spinduliavimo)
* Projekto nustatymuose buvo išjungtas produkto eksporto žymimasis langelis
* Eksporto metu baigėsi vietos diske

**Sprendimai:**

1. Patikrinkite „`[RUN-SUMMARY]`“ patarimus ir „`[EXPORT-CHECK]`“ eilutes „Debug Log“ (diagnozavimo žurnale) – juose paaiškinami praleidimai pagal kamerą
2. Patikrinkite produkto eksporto žymimuosius langelius [Projekto nustatymuose](adjusting-project-settings.md)
3. Patikrinkite, ar buvo pakankamai vietos diske
4. Ištaisę priežastį, apdorokite iš naujo

### Problema: Tamsūs arba šviesūs kraštai (vis dar matomas vinjetavimas)

**Galimos priežastys:**

* Išjungta vinjetavimo korekcija
* Kameros/objektyvo nėra „Chloros“ profilių duomenų bazėje
* Ekstremalus vinjetavimas, kurio negalima pakoreguoti

**Sprendimai:**

1. Patikrinkite, ar projekto nustatymuose įjungta vinjetavimo korekcija
2. Patikrinkite, ar teisingai nustatytas fotoaparato modelis
3. Jei vinjetavimas išlieka, susisiekite su „MAPIR“ technine pagalba

### Problema: Neteisingos spalvos arba reikšmės

**Galimos priežastys:**

* Neaptikta kalibravimo taškų
* Pasirinkta neteisinga kalibravimo taškų modelis
* Atspindžio kalibravimas išjungtas
* Prastos kokybės taškų vaizdai

**Sprendimai:**

1. Patikrinkite, ar buvo įjungtas atspindžio kalibravimas
2. Patikrinkite pranešimus „Tikslas rastas“ (Target found) derinimo žurnale (Debug Log)
3. Įvertinkite tikslų vaizdų kokybę
4. Pakartotinai apdorokite, pažymėdami tinkamus tikslus

### Problema: NDVI reikšmės atrodo neteisingos

**Numatomi NDVI intervalai:*** **Vanduo, uolienos, dirvožemis**: nuo -0,1 iki 0,2
* **Retas/nesveikas augmenijos sluoksnis**: nuo 0,2 iki 0,4
* **Vidutinė augmenija**: nuo 0,4 iki 0,6
* **Sveika, tanki augmenija**: nuo 0,6 iki 0,9**Jei reikšmės neatitinka šių intervalų:**

1. Patikrinkite, ar buvo pritaikytas atspindžio kalibravimas
2. Patikrinkite, ar buvo įtrauktas šviesos jutiklio žurnalas
3. Patikrinkite, ar buvo aptikti kalibravimo taikiniai
4. Įsitikinkite, kad buvo nustatytas teisingas fotoaparato modelis
5. Peržiūrėkite taikinio vaizdo fiksavimo laiką ir sąlygas
6. Jei indeksus skaičiuojate patys iš atspindžio failų, patvirtinkite, kad padalijote iš failo `Chloros:PixelScale` (žr. aukščiau)

***

## Apdorotų vaizdų naudojimas

### Fotogrametrijai / ortomozaikos kūrimui

**Rekomenduojama darbo eiga:**

1.**Importuokite kalibruotus atspindžio vaizdus** į fotogrametrijos programinę įrangą:
   * „Pix4Dmapper“
   * „Agisoft Metashape“
   * „DroneDeploy“
   * „WebODM“
2. **Išsaugokite EXIF metaduomenis**: užtikrinkite, kad GPS duomenys būtų išsaugoti geotagavimui
3. **Kalibruoti darbo srautai**: naudokite atspindžio vaizdus, siekiant užtikrinti mokslinį tikslumą — „LATTICE“ atspindžio vaizduose yra XMP kalibravimo žymės, kurias skaito „Pix4D“
4. **Apdorokite indeksinius mozaikos vaizdus**: iš atskirų indeksinių vaizdų sukurkite ortomozaikas „NDVI“
5. **Eksportuokite georeferencinius „GeoTIFF“**: Naudojimui GIS programose

### GIS analizei

**Rekomenduojama darbo eiga:**

1.**Įkelkite į „QGIS“, „ArcGIS“ ar panašią programą**

2.**Naudokite 16 bitų TIFF** atspindžio vaizdus daugiabandinei analizei (padalinkite iš failo `Chloros:PixelScale`)
3. **Naudokite indeksinius vaizdus** (NDVI, NDRE) kaip paruoštus naudoti augmenijos sluoksnius
4. **Rastro skaičiuoklė**: sujunkite juostas individualiai analizei
5. **Eksportavimas**: Sukurkite klasifikavimo žemėlapius, pokyčių aptikimą, augmenijos būklės žemėlapius

### Tiesioginei analizei / ataskaitų rengimui

**Rekomenduojama darbo eiga:**

1.**Naudokite indeksinius vaizdus su LUT spalvomis** vizualiosioms ataskaitoms
2. **Išgaukite statistinius duomenis**: vidutinis NDVI rodiklis pagal lauką / sklypą
3. **Laiko eilutės**: palyginkite indeksus iš skirtingų sesijų
4. **Sukurkite ataskaitas**: įtraukite žemėlapius, statistinius duomenis ir vizualizacijas***

## Archyvavimas ir atsarginės kopijos

### Rekomenduojama atsarginių kopijų strategija

**Ką išsaugoti:*** ✅ **Originalūs RAW/JPG vaizdai arba „LATTICE“ neapdoroti įrašai** – archyvuokite atskirame diske ar debesyje; neapdoroti duomenys yra apdorojimo proceso šaltinis, o viską kitą galima atkurti iš jų
* ✅ **`.daq` / `.csv` šviesos jutiklių failai** – reikalingi, kad vėliau būtų galima iš naujo apskaičiuoti atspindžio koeficientą
* ✅ **Apdoroti rezultatai** – išsaugokite kalibruotus vaizdus ir indeksus
* ✅ **Projekto aplankas** (`project.json` ir susiję failai) – jame yra visi nustatymai, reikalingi pakartotiniam apdorojimui, jei prireiktų
* ✅ **Debug Log** – apdorojimo detalių dokumentai
* ✅ **Kalibravimo etaloniniai vaizdai** – Skirti patikrai ir pakartotiniam apdorojimui**Rekomendacijos dėl saugojimo:*** **Nedelsiant atlikti atsarginę kopiją**: Išorinis kietasis diskas
* **Ilgalaikis archyvavimas**: Debesų saugykla („Google Drive“, „Dropbox“ ir pan.)
* **Svarbūs duomenys**: laikykite 2–3 kopijas skirtingose vietose***

## Kiti apdorojimo ciklai

### Projekto nustatymų pakartotinis naudojimas

Jei ateityje apdorosite panašius duomenų rinkinius:

1. **Išsaugokite projekto šabloną** (jei dar to nepadarėte)
2. **Sukurkite naują projektą** naudodami išsaugotą šabloną
3. **Importuokite naujus vaizdus**

4.**Apdorokite**naudodami identiškus nustatymus, kad užtikrintumėte nuoseklumą

### Keleto sesijų apdorojimas partijomis

Jei turite keletą sesijų / duomenų rinkinių:**1 variantas: GUI – keli projektai**

* Kiekvienai sesijai sukurkite atskirą projektą
* Naudokite nuoseklius šablono nustatymus
* Apdorokite po vieną

**2-asis variantas: Chloros CLI (tik su „Chloros+“)**

* Automatizuokite paketinį apdorojimą
* Apdorokite kelias aplankus naudodami skriptus
* Žr. [CLI dokumentaciją](../CLI.md) ir [CLI žinyną](../reference/cli-reference.md)

**3 variantas: Python SDK (tik Chloros+ versijose)**

* Programinis valdymas
* Integracija su analizės procesų grandinėmis
* Žr. [API dokumentaciją](../api-python-sdk.md) ir [SDK žinyną](../reference/sdk-reference.md)

***

## Problemų sprendimas po apdorojimo

### Pakartotinis apdorojimas su kitokiais nustatymais

Jei rezultatai nėra patenkinami:

1. Išsaugokite originalius vaizdus (niekada neištrinkite)
2. Atidarykite tą patį projektą Chloros
3. Pakoreguokite nustatymus skydelyje „Project Settings“
4. Apdorokite dar kartą — rezultatai bus išsaugoti tuose pačiuose produkto aplankuose, todėl failai su tuo pačiu pavadinimu iš ankstesnio apdorojimo bus pakeisti

### Vaizdų pogrupio apdorojimas

Norėdami pakartotinai apdoroti tik tam tikrus vaizdus:

1. Sukurkite naują projektą
2. Importuokite tik tuos vaizdus, kuriuos reikia apdoroti iš naujo
3. Naudokite tą patį nustatymų šabloną
4. Apdorokite mažesnį duomenų rinkinį

### Pagalba

Jei kyla problemų:

* 📧 **El. paštas**: info@mapir.camera (pridėkite derinimo žurnalą)
* 🌐 **Pagalba**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **DUK**: [Dažnai užduodami klausimai](../faq.md)
* 📖 **Dokumentacija**: [Chloros vadovas](../)***

## Santrauka: Visas darbo srautas

Dabar baigėte visą Chloros apdorojimo darbo srautą:

1. ✅ **Sukurtas projektas** – žr. [Projektai](../projects.md)
2. ✅ **Pridėti failai** – žr. [Failų pridėjimas](adding-files-to-a-project.md)
3. ✅ **Nustatyti parametrai** – žr. [Projekto parametrų nustatymas](adjusting-project-settings.md)
4. ✅ **Pažymėti taikiniai** – žr. [Taikinių vaizdų pasirinkimas](choosing-target-images.md)
5. ✅ **Pradėtas apdorojimas** – žr. [Apdorojimo pradžia](starting-the-processing.md)
6. ✅ **Stebėta pažanga** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)
7. ✅ **Peržiūrėti rezultatai** – ši puslapis**Jūsų kalibruoti, atspindžio koreguoti multispektriniai vaizdai yra paruošti analizei!**

***

## Papildomi ištekliai

### Išplėstinės funkcijos

* [**Vaizdų peržiūros programa**](../image-viewer-gui/opening-an-image-full-screen.md) – Interaktyvus vizualizavimas ir analizė
* [**Indeksų/LUT bandymų aplinka**](../image-viewer-gui/index-lut-sandbox.md) – individualių indeksų testavimas
* [**Daugiaspektrinių indeksų formulės**](../project-settings/multispectral-index-formulas.md) – išsamus indeksų žinynas

### Automatizavimas ir integracija

* [**CLI dokumentacija**](../CLI.md) – Komandinės eilutės paketinis apdorojimas
* [**Python SDK**](../api-python-sdk.md) – Automatizavimas programavimo būdu
* [**Chloros+ Funkcijos**](../#chloros) – Išplėstinės apdorojimo galimybės

### Pagalba ir mokymasis

* [**DUK**](../faq.md) – Atsakymai į dažniausiai užduodamus klausimus
* [**Kalibravimo taikiniai**](../calibration-targets.md) – Atspindžio kalibravimo principai
* [**Palaikomos kameros**](../supported-cameras.md) – Suderinama įranga
