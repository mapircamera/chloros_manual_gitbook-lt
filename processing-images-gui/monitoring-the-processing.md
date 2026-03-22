# Apdorojimo stebėjimas

Pradėjus apdorojimą, Chloros siūlo keletą būdų, kaip stebėti pažangą, tikrinti, ar nėra problemų, ir suprasti, kas vyksta su jūsų duomenų rinkiniu. Šiame puslapyje paaiškinama, kaip stebėti apdorojimo eigą ir kaip interpretuoti Chloros pateikiamą informaciją.

## Pažangos juostos apžvalga

Pažangos juosta viršutiniame antrašte rodo apdorojimo būseną realiuoju laiku ir užbaigtumo procentą.

### Laisvojo režimo pažangos juosta

Vartotojams, neturintiems Chloros+ licencijos:

**2 etapų pažangos rodymas:**

1.**Tikslo aptikimas** – kalibravimo tikslų paieška vaizduose
2. **Apdorojimas** – korekcijų taikymas ir eksportavimas**Pažangos juosta rodo:**

* Bendrą užbaigtumo procentą (0–100 %)
* Dabartinio etapo pavadinimą
* Paprastą horizontalios juostos vizualizaciją

### Chloros+ pažangos juosta

Vartotojams, turintiems Chloros+ licenciją:

**4 etapų pažangos rodymas:**

1.**Aptikimas** – kalibravimo taškų paieška
2. **Analizė** – vaizdų tikrinimas ir proceso paruošimas
3. **Kalibravimas** – vinjetės ir atspindžio korekcijų taikymas
4. **Eksportavimas** – apdorotų failų išsaugojimas**Interaktyvios funkcijos:*** **Pereikite pelės žymekliu** per pažangos juostą, kad pamatytumėte išplėstą 4 etapų skydelį
* **Spustelėkite** pažangos juostą, kad sustabdytumėte / pritvirtintumėte išplėstą skydelį
* **Spustelėkite dar kartą**, kad atšauktumėte sustabdymą ir skydelis automatiškai pasislėptų, kai nuimsite pelės žymeklį
* Kiekviename etape rodomas individualus pažangos lygis (0–100 %)

***

## Kiekvieno apdorojimo etapo supratimas

{% hint style="info" %}
**Pipeline architektūra**: Šie 4 GUI etapai atitinka [4-siūlų apdorojimo pipeline](../processing-architecture/processing-pipeline.md). Sistemose su GPU pagreitinimu, 3-iasis siūlas (Kalibravimas) naudoja [Dinaminį skaičiavimo pritaikymą](../processing-architecture/dynamic-compute-adaptation.md), kuris optimizuoja apdorojimą jūsų konkrečiai aparatūrai.
{% endhint %}

### 1 etapas: Aptikimas (tikslo aptikimas)

**Kas vyksta:**

* Chloros nuskaito vaizdus, pažymėtus žymės langeliu „Tikslas“
* Kompiuterinio matymo algoritmai identifikuoja 4 kalibravimo plokštes
* Iš kiekvienos plokštės išgautos atspindžio vertės
* Užregistruoti tikslo laiko žymos, reikalingos tinkamam kalibravimo planavimui

**Trukmė:**

* Su pažymėtais tikslais: 10–60 sekundžių
* Be pažymėtų tikslų: 5–30+ minučių (nuskaito visus vaizdus)

**Pažangos indikatorius:**

* Aptikimas: 0 % → 100 %
* Nuskaitytų vaizdų skaičius
* Rastų tikslų skaičius

**Į ką atkreipti dėmesį:**

* Turėtų baigtis greitai, jei tikslai pažymėti tinkamai
* Jei trunka per ilgai, tikslai gali būti nepažymėti
* Patikrinkite „Debug Log“ žurnale, ar yra pranešimų „Target found“

### 2 etapas: Analizė

**Kas vyksta:**

* Vaizdo EXIF metaduomenų skaitymas (laiko žymos, ekspozicijos nustatymai)
* Kalibravimo strategijos nustatymas remiantis objektų laiko žymėmis
* Vaizdų apdorojimo eilės tvarkymas
* Lygiagretaus apdorojimo procesų paruošimas (tik Chloros+)

**Trukmė:** 5–30 sekundžių**Pažangos indikatorius:**

* Analizė: 0 % → 100 %
* Greitas etapas, paprastai baigiamas greitai

**Į ką atkreipti dėmesį:**

* Pažanga turėtų vykti tolygiai, be pertraukų
* Įspėjimai apie trūkstamus metaduomenis bus rodomi Debug Log

### 3 etapas: Kalibravimas

**Kas vyksta:*** **Debayering**: RAW Bayer modelio konvertavimas į 3 kanalus
* **Vignette korekcija**: objektyvo kraštų patamsėjimo pašalinimas
* **Atspindžio kalibravimas**: normalizavimas pagal tikslinės reikšmes
* **Indekso skaičiavimas**: daugiaspektrinių indeksų skaičiavimas
* Kiekvieno vaizdo apdorojimas per visą procesą

**Trukmė:** Didžioji dalis bendro apdorojimo laiko (60–80 %)**Pažangos indikatorius:**

* Kalibravimas: 0 % → 100 %
* Šiuo metu apdorojamas vaizdas
* Apdoroti vaizdai / Visi vaizdai

**Apdorojimo elgsena:*** **Laisvasis režimas**: Apdoroja po vieną vaizdą paeiliui
* **Chloros+ režimas**: Apdoroja iki 16 vaizdų vienu metu
* **GPU pagreitinimas**: Žymiai pagreitina šį etapą**Į ką atkreipti dėmesį:**

* Nuoseklus pažangos rodymas pagal vaizdų skaičių
* Patikrinkite „Debug Log“ (Debug žurnalo) įrašus dėl kiekvieno vaizdo apdorojimo pranešimų
* Įspėjimai apie vaizdo kokybės ar kalibravimo problemas

### 4 etapas: Eksportavimas

**Kas vyksta:**

* Kalibruotų vaizdų įrašymas į diską pasirinktu formatu
* Daugiaspektrinių indeksinių vaizdų eksportavimas su LUT spalvomis
* Kameros modelio pakatalogių kūrimas
* Originalių failų pavadinimų išsaugojimas su atitinkamais priesagais

**Trukmė:** 10–20 % viso apdorojimo laiko**Pažangos indikatorius:**

* Eksportavimas: 0 % → 100 %
* Rašomi failai
* Eksporto formatas ir paskirties vieta

**Į ką reikia atkreipti dėmesį:**

* Įspėjimai apie disko vietos trūkumą
* Failų rašymo klaidos
* Visų sukonfigūruotų išvesties duomenų užbaigimas

***

## Skirtukas „Debug Log“ (Debugavimo žurnalas)

Debugavimo žurnale pateikiama išsami informacija apie apdorojimo eigą ir visas iškilusias problemas.

### Prieiga prie debugavimo žurnalo

1. Spustelėkite piktogramą **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> piktogramą kairiajame šoniniame meniu
2. Atsidarys žurnalo langas, kuriame rodomi apdorojimo pranešimai realiuoju laiku
3. Langas automatiškai slinks, kad būtų rodomi naujausi pranešimai

### Žurnalo pranešimų supratimas

#### Informaciniai pranešimai (balti/pilki)

Įprasti apdorojimo atnaujinimai:

```
[INFO] Processing started
[INFO] Target detected in IMG_0015.RAW - 4 panels found
[INFO] Calibrating IMG_0234.RAW
[INFO] Exported NDVI image: IMG_0234_NDVI.tif
[INFO] Processing complete
```

#### Įspėjamieji pranešimai (geltoni)

Nekritinės problemos, kurios nesustabdo apdorojimo:

```
[WARN] No GPS data found in IMG_0145.RAW
[WARN] Target image timestamp gap > 30 minutes
[WARN] Low contrast in calibration panel - results may vary
```

**Veiksmas:** Peržiūrėkite įspėjimus po apdorojimo, bet nepertraukite

#### Klaidų pranešimai (Red)

Kritinės problemos, dėl kurių apdorojimas gali žlugti:

```
[ERROR] Cannot write file - disk full
[ERROR] Corrupted image file: IMG_0299.RAW
[ERROR] No targets detected - enable reflectance calibration or mark target images
```

**Veiksmas:** Sustabdykite apdorojimą, išspręskite klaidą, paleiskite iš naujo

### Dažni žurnalo pranešimai

| Pranešimas                          | Reikšmė                                | Reikalingas veiksmas                                         |
| -------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| „Tikslas aptiktas \[failo pavadinimas]“ | Kalibravimo tikslas sėkmingai rastas  | Nėra – normalu                                         |
| „Apdorojamas vaizdas X iš Y“        | Dabartinė pažanga                | Nėra – normalu                                         |
| „Tikslai nerasti“               | Kalibravimo tikslai nerasti        | Pažymėkite tikslo vaizdus arba išjunkite atspindžio kalibravimą |
| „Nepakanka vietos diske“        | Nepakanka vietos išvesties duomenims          | Atlaisvinkite vietos diske                                    |
| „Praleidžiamas sugadintas failas“        | Vaizdo failas yra sugadintas                  | Perkopijuokite failą iš SD kortelės                             |
| „PPK duomenys pritaikyti“               | GPS pataisymai iš .daq failo pritaikyti | Nėra – normalu                                         |

### Žurnalo duomenų kopijavimas

Norėdami nukopijuoti žurnalą trikčių šalinimo ar pagalbos tikslais:

1. Atidarykite „Debug Log“ (Trikčių šalinimo žurnalas) skydelį
2. Spustelėkite mygtuką **„Copy Log“** (Kopijuoti žurnalą) (arba dešiniuoju pelės mygtuku spustelėkite → „Select All“ (Pažymėti viską))
3. Įklijuokite į tekstinį failą arba el. laišką
4. Jei reikia, nusiųskite į „MAPIR“ pagalbos tarnybą

***

## Sistemos išteklių stebėjimas

### CPU naudojimas

**Laisvasis režimas:**

* 1 CPU branduolys ~100 %
* Kiti branduoliai neveikia arba yra laisvi
* Sistema išlieka reaguojanti

**Chloros+ lygiagretusis režimas:**

* Keletas branduolių 80–100 % (iki 16 branduolių)
* Didelis bendras CPU panaudojimas
* Sistema gali atrodyti mažiau reaguojanti

**Kaip stebėti:**

* Windows Užduočių tvarkyklė (Ctrl+Shift+Esc)
* Skirtukas „Našumas“ → Skyrius „CPU“
* Ieškokite procesų „Chloros“ arba „chloros-backend“

### Atminties (RAM) naudojimas

**Tipinis naudojimas:**

* Maži projektai (&lt; 100 vaizdų): 2–4 GB
* Vidutinio dydžio projektai (100–500 vaizdų): 4–8 GB
* Didelio dydžio projektai (500+ vaizdų): 8–16 GB
* Chloros+ lygiagretaus režimo metu sunaudojama daugiau RAM

**Jei trūksta atminties:**

* Apdorokite mažesnes partijas
* Uždarykite kitas programas
* Jei reguliariai apdorojate didelius duomenų rinkinius, atnaujinkite RAM

### GPU naudojimas (Chloros+ su CUDA)

Kai įjungtas GPU pagreitinimas:

* NVIDIA GPU rodo didelį panaudojimą (60–90 %)
* VRAM naudojimas padidėja (reikia 4 GB+ VRAM)
* Kalibravimo etapas yra žymiai greitesnis

**Kaip stebėti:**

* NVIDIA sistemos dėklo piktograma
* Užduočių tvarkyklė → Našumas → GPU
* GPU-Z arba panaši stebėjimo priemonė

### Disko įvesties/išvesties operacijos

**Ko tikėtis:**

* Didelis disko skaitymo intensyvumas analizavimo etape
* Didelis disko rašymo intensyvumas eksportavimo etape
* SSD yra žymiai greitesnis nei HDD

**Našumo patarimas:**

* Jei įmanoma, naudokite SSD projektų aplankams
* Venkite tinklo diskų dideliems duomenų rinkiniams
* Užtikrinkite, kad disko talpa nebūtų beveik išnaudota (tai daro įtaką rašymo greičiui)

***

## Problemų aptikimas apdorojimo metu

### Įspėjamieji ženklai

**Procesas sustoja (jokių pokyčių 5 ar daugiau minučių):**

* Patikrinkite „Debug Log“ dėl klaidų
* Patikrinkite, ar yra laisvos vietos diske
* Patikrinkite „Task Manager“, ar veikia „Chloros“

**Dažnai rodomi klaidų pranešimai:**

* Sustabdykite apdorojimą ir peržiūrėkite klaidas
* Dažniausios priežastys: vietos trūkumas diske, sugadinti failai, atminties problemos
* Žiūrėkite skyrių „Problemų sprendimas“ žemiau

**Sistema nereaguoja:**

* Chloros+ lygiagretaus režimo naudojimas sunaudoja per daug išteklių
* Apsvarstykite galimybę sumažinti vienu metu vykdomų užduočių skaičių arba atnaujinti aparatūrą
* Laisvasis režimas sunaudoja mažiau išteklių

### Kada sustabdyti apdorojimą

Sustabdykite apdorojimą, jei matote:

* ❌ Klaidas „Diskas pilnas“ arba „Negaliu įrašyti failo“
* ❌ Pasikartojančios vaizdo failų sugadinimo klaidos
* ❌ Sistema visiškai užstrigo (nereaguoja)
* ❌ Supratote, kad buvo nustatyti neteisingi parametrai
* ❌ Importuoti neteisingi vaizdai

**Kaip sustabdyti:**

1. Spustelėkite**„Sustabdyti/Atšaukti“ mygtuką** (pakeičia „Pradėti“ mygtuką)
2. Apdorojimas sustabdomas, pažanga prarandama
3. Išspręskite problemas ir pradėkite iš naujo

***

## Problemų sprendimas apdorojimo metu

### Apdorojimas vyksta labai lėtai

**Galimos priežastys:**

* Nepažymėti tiksliniai vaizdai (nuskaitomi visi vaizdai)
* Naudojamas HDD, o ne SSD saugykla
* Nepakankami sistemos ištekliai
* Nustatyta daug indeksų
* Prieiga prie tinklo disko

**Sprendimai:**

1. Jei tik pradėjote ir esate aptikimo etape: atšaukti, pažymėti tikslus, paleisti iš naujo
2. Ateityje: naudokite SSD, sumažinkite indeksų skaičių, atnaujinkite aparatūrą
3. Apsvarstykite CLI naudojimą didelių duomenų rinkinių paketiniam apdorojimui

### Įspėjimai apie „diskų vietą“

**Sprendimai:**

1. Nedelsiant atlaisvinkite vietos diske
2. Perkelkite projektą į diską, kuriame yra daugiau vietos
3. Sumažinkite eksportuotinų indeksų skaičių
4. Vietoj TIFF naudokite JPG formatą (mažesni failai)

### Dažni pranešimai apie „sugadintus failus“

**Sprendimai:**

1. Perkopijuokite vaizdus iš SD kortelės dar kartą, kad užtikrintumėte jų vientisumą
2. Patikrinkite SD kortelę dėl klaidų
3. Pašalinkite sugadintus failus iš projekto
4. Tęskite likusių vaizdų apdorojimą

### Sistemos perkaitimas / greičio ribojimas

**Sprendimai:**

1. Užtikrinkite tinkamą ventiliaciją
2. Nuvalykite dulkes iš kompiuterio ventiliacijos angų
3. Sumažinkite apdorojimo apkrovą (naudokite „Free“ režimą vietoj Chloros+)
4. Apdorokite vėsesniu paros metu

***

## Pranešimas apie apdorojimo pabaigą

Kai apdorojimas baigiamas:

* Pažangos juosta pasiekia 100 %
* **„Apdorojimas baigtas“** pranešimas pasirodo „Debug Log“
* „Pradėti“ mygtukas vėl tampa aktyvus
* Visi išvesties failai yra fotoaparato modelio pakatalogyje

***

## Tolimesni veiksmai

Kai apdorojimas baigtas:

1. **Peržiūrėkite rezultatus** – Žr. [Apdorojimo užbaigimas](finishing-the-processing.md)
2. **Patikrinkite išvesties aplanką** – įsitikinkite, kad visi failai buvo eksportuoti teisingai
3. **Peržiūrėkite „Debug Log“** – patikrinkite, ar nėra įspėjimų ar klaidų
4. **Peržiūrėkite apdorotus vaizdus** – naudokite „Image Viewer“ arba išorinę programinę įrangą

Informaciją apie apdorotų rezultatų peržiūrą ir naudojimą rasite skyriuje [Apdorojimo užbaigimas](finishing-the-processing.md).
