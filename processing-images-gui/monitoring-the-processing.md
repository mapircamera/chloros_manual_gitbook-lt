# Apdorojimo stebėjimas

Pradėjus apdorojimą, Chloros siūlo keletą būdų stebėti pažangą, tikrinti problemas ir suprasti, kas vyksta su jūsų duomenų rinkiniu. Šiame puslapyje paaiškinama, kaip stebėti apdorojimą ir interpretuoti Chloros teikiamą informaciją.

## Pažangos juostos apžvalga

Pažangos juosta viršutiniame antraštės skyriuje rodo apdorojimo būseną ir užbaigtumo procentą realiuoju laiku.

### Laisvojo režimo pažangos juosta

Vartotojams, neturintiems Chloros+ licencijos:

**2 etapų pažangos rodymas:**

1. **Tikslo aptikimas** – kalibravimo tikslų paieška vaizduose
2. **Apdorojimas** – pataisymų taikymas ir eksportavimas

**Pažangos juosta rodo:**

* Bendrą užbaigtumo procentą (0–100 %)
* Dabartinio etapo pavadinimą
* Paprastą horizontalią juostą

### Chloros+ pažangos juosta

Vartotojams, turintiems Chloros+ licenciją:

**4 etapų pažangos rodymas:**

1. **Aptikimas** – kalibravimo tikslų paieška
2. **Analizė** – vaizdų tikrinimas ir ruošimas
3. **Kalibravimas** – vinjetės ir atspindžio korekcijų taikymas
4. **Eksportavimas** – apdorotų failų išsaugojimas

**Interaktyvios funkcijos:**

* **Pereikite pelės žymekliu per** pažangos juostą, kad pamatytumėte išplėstą 4 etapų skydelį
* **Spustelėkite** pažangos juostą, kad sustabdytumėte / pritvirtintumėte išplėstą skydelį
* **Spustelėkite dar kartą**, kad atšildytumėte ir automatiškai paslėptumėte, kai nuimsite pelę
* Kiekvienas etapas rodo individualią pažangą (0–100 %)

***

## Kiekvieno apdorojimo etapo supratimas

### 1 etapas: aptikimas (tikslo aptikimas)

**Kas vyksta:**

* Chloros nuskaito vaizdus, pažymėtus žymės langeliu „Tikslas“
* Kompiuterinio matymo algoritmai identifikuoja 4 kalibravimo skydelius
* Iš kiekvieno skydelio išgautos atspindžio vertės
* Tikslo laiko žymos įrašomos tinkamam kalibravimo planavimui

**Trukmė:**

* Su pažymėtais tikslais: 10–60 sekundžių
* Be pažymėtų tikslų: 5–30+ minučių (nuskaito visus vaizdus)

**Pažangos indikatorius:**

* Aptikimas: 0 % → 100 %
* Nuskaitytų vaizdų skaičius
* Rasti tikslai

**Į ką reikia atkreipti dėmesį:**

* Turėtų būti baigta greitai, jei tikslai pažymėti tinkamai
* Jei trunka per ilgai, tikslai gali būti nepažymėti
* Patikrinkite „Debug Log“ (Debug žurnalą) dėl pranešimų „Target found“ (Rastas tikslas)

### 2 etapas: Analizė

**Kas vyksta:**

* Skaityti vaizdo EXIF metaduomenis (laiko žymes, ekspozicijos nustatymus)
* Nustatyti kalibravimo strategiją pagal tikslų laiko žymes
* Tvarkyti vaizdo apdorojimo eilę
* Parengti lygiagretaus apdorojimo darbuotojus (tik Chloros+)

**Trukmė:** 5–30 sekundžių

**Pažangos indikatorius:**

* Analizė: 0 % → 100 %
* Greitas etapas, paprastai baigiamas greitai

**Į ką reikia atkreipti dėmesį:**

* Turėtų vykti tolygiai, be pertraukų
* Įspėjimai apie trūkstamus metaduomenis bus rodomi „Debug Log“ (Debugavimo žurnale)

### 3 etapas: Kalibravimas

**Kas vyksta:**

* **Debayering**: RAW Bayer modelio konvertavimas į 3 kanalus
* **Vignette korekcija**: objektyvo kraštų patamsėjimo pašalinimas
* **Atspindžio kalibravimas**: normalizavimas pagal tikslinės vertės
* **Indekso skaičiavimas**: daugiaspektrinių indeksų skaičiavimas
* Kiekvieno vaizdo apdorojimas per visą procesą

**Trukmė:** didžioji dalis viso apdorojimo laiko (60–80 %)

**Pažangos indikatorius:**

* Kalibravimas: 0 % → 100 %
* Šiuo metu apdorojamas vaizdas
* Užbaigti vaizdai / Visi vaizdai

**Apdorojimo elgsena:**

* **Laisvasis režimas**: apdoroja po vieną vaizdą iš eilės
* **Chloros+ režimas**: apdoroja iki 16 vaizdų vienu metu
* **GPU pagreitinimas**: žymiai pagreitina šį etapą

**Į ką atkreipti dėmesį:**

* Pastovi pažanga pagal vaizdų skaičių
* Patikrinkite „Debug Log“ (Debugavimo žurnalą) dėl pranešimų apie kiekvieno vaizdo apdorojimo pabaigą
* Įspėjimai apie vaizdo kokybės ar kalibravimo problemas

### 4 etapas: Eksportavimas

**Kas vyksta:**

* Kalibruotų vaizdų rašymas į diską pasirinktu formatu
* Daugiaspektrinių indeksų vaizdų eksportavimas su LUT spalvomis
* Kameros modelio pakatalogių kūrimas
* Originalių failų pavadinimų išsaugojimas su atitinkamais priesagais

**Trukmė:** 10–20 % viso apdorojimo laiko

**Pažangos indikatorius:**

* Eksportavimas: 0 % → 100 %
* Rašomi failai
* Eksporto formatas ir paskirties vieta

**Į ką reikia atkreipti dėmesį:**

* Įspėjimai apie disko vietos trūkumą
* Failų rašymo klaidos
* Visų sukonfigūruotų išvesties užbaigimas

***

## Debug Log (Debug žurnalo) skirtukas

Debug žurnale pateikiama išsami informacija apie apdorojimo pažangą ir visas iškilusias problemas.

### Prieiga prie Debug žurnalo

1. Spustelėkite **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> piktogramą kairėje šoninėje juostoje.
2. Atsidarys žurnalo langas, kuriame bus rodomi apdorojimo pranešimai realiuoju laiku.
3. Automatiškai slinks, kad būtų rodomi naujausi pranešimai.

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

**Veiksmas:** Peržiūrėkite įspėjimus po apdorojimo, bet nepertraukite jo.

#### Klaidų pranešimai (Red)

Kritinės problemos, dėl kurių apdorojimas gali žlugti:

```
[ERROR] Cannot write file - disk full
[ERROR] Corrupted image file: IMG_0299.RAW
[ERROR] No targets detected - enable reflectance calibration or mark target images
```

**Veiksmas:** Sustabdyti apdorojimą, išspręsti klaidą, paleisti iš naujo.

### Įprasti žurnalo pranešimai

| Pranešimas                          | Reikšmė                                | Reikalingas veiksmas                                         |
| -------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| „Tikslas aptiktas \[filename]“ | Kalibravimo tikslas sėkmingai rastas  | Nėra – normalu                                         |
| „Apdorojamas vaizdas X iš Y“        | Dabartinės pažangos atnaujinimas                | Nėra – normalu                                         |
| „Tikslai nerasti“               | Kalibravimo tikslai nerasti        | Pažymėkite tikslo vaizdus arba išjunkite atspindžio kalibravimą |
| „Nepakankama disko vieta“        | Nepakanka vietos išvesties failams          | Atlaisvinkite disko vietą                                    |
| „Pereiti prie sugadinto failo“        | Vaizdo failas sugadintas                  | Pakartotinai nukopijuokite failą iš SD kortelės                             |
| „PPK duomenys pritaikyti“               | GPS pataisymai iš .daq failo pritaikyti | Nėra – normalus                                         |

### Žurnalo duomenų kopijavimas

Norėdami kopijuoti žurnalą trikčių šalinimui ar pagalbai:

1. Atidarykite „Debug Log“ (Debugavimo žurnalas) skydelį.
2. Spustelėkite mygtuką „Copy Log“ (Kopijuoti žurnalą) (arba dešiniuoju pelės mygtuku spustelėkite → „Select All“ (Pasirinkti viską)).
3. Įklijuokite į tekstinį failą arba el. laišką.
4. Jei reikia, nusiųskite MAPIR pagalbos tarnybai.

***

## Sistemos išteklių stebėjimas

### CPU naudojimas

**Laisvasis režimas:**

* 1 CPU branduolys ~100 %
* Kiti branduoliai neveikia arba yra laisvi
* Sistema lieka reaguojanti

**Chloros+ Lygiagretusis režimas:**

* Keli branduoliai 80–100 % (iki 16 branduolių)
* Didelis bendras CPU naudojimas
* Sistema gali atrodyti mažiau reaguojanti

**Stebėti:**

* Windows Užduočių tvarkyklė (Ctrl+Shift+Esc)
* Skirtukas „Našumas“ → skyrius „CPU“
* Ieškokite procesų „Chloros“ arba „chloros-backend“

### Atminties (RAM) naudojimas

**Tipinis naudojimas:**

* Maži projektai (&lt; 100 vaizdų): 2–4 GB
* Vidutiniai projektai (100–500 vaizdų): 4–8 GB
* Dideliai projektai (500+ vaizdų): 8–16 GB
* Chloros+ lygiagretusis režimas naudoja daugiau RAM

**Jei atminties mažai:**

* Apdorokite mažesnes partijas
* Uždarykite kitas programas
* Jei reguliariai apdorojate didelius duomenų rinkinius, atnaujinkite RAM

### GPU naudojimas (Chloros+ su CUDA)

Kai įjungtas GPU pagreitinimas:

* NVIDIA GPU rodo didelį panaudojimą (60–90 %)
* VRAM naudojimas padidėja (reikia 4 GB+ VRAM)
* Kalibravimo etapas yra žymiai greitesnis

**Stebėti:**

* NVIDIA sistemos dėklo piktograma
* Užduočių tvarkyklė → Našumas → GPU
* GPU-Z arba panaši stebėjimo priemonė

### Diskas I/O

**Ko tikėtis:**

* Didelis disko skaitymo greitis analizės etape
* Didelis disko rašymo greitis eksporto etape
* SSD yra žymiai greitesnis nei HDD

**Našumo patarimas:**

* Jei įmanoma, naudokite SSD projektų aplankams
* Venkite tinklo diskų dideliems duomenų rinkiniams
* Įsitikinkite, kad diskas nėra beveik užpildytas (tai turi įtakos rašymo greičiui)

***

## Problemų aptikimas apdorojimo metu

### Įspėjamieji ženklai

**Procesas sustoja (jokių pokyčių 5+ minutes):**

* Patikrinkite, ar nėra klaidų Debug Log
* Patikrinkite, ar yra laisvos vietos diske
* Patikrinkite Task Manager, ar veikia Chloros

**Dažnai rodomi klaidų pranešimai:**

* Sustabdykite apdorojimą ir peržiūrėkite klaidas
* Dažniausios priežastys: disko vietos trūkumas, sugadinti failai, atminties problemos
* Žr. skyrių „Problemų sprendimas“ žemiau

**Sistema nereaguoja:**

* Chloros+ lygiagretusis režimas naudoja per daug išteklių
* Apsvarstykite galimybę sumažinti vienu metu vykdomų užduočių skaičių arba atnaujinti aparatinę įrangą
* Laisvasis režimas naudoja mažiau išteklių

### Kada sustabdyti apdorojimą

Sustabdykite apdorojimą, jei matote:

* ❌ Klaidas „Diskas pilnas“ arba „Negaliu įrašyti failo“
* ❌ Pakartotinės vaizdo failų sugadinimo klaidos
* ❌ Sistema visiškai užstrigo (nereaguoja)
* ❌ Supratote, kad buvo nustatyti neteisingi parametrai
* ❌ Importuoti neteisingi vaizdai

**Kaip sustabdyti:**

1. Spustelėkite **Sustabdyti/Atšaukti mygtuką** (pakeičia Pradėti mygtuką)
2. Apdorojimas sustabdomas, pažanga prarandama
3. Išspręskite problemas ir pradėkite iš naujo

***

## Problemų sprendimas apdorojimo metu

### Apdorojimas labai lėtas

**Galimos priežastys:**

* Nežymėti tiksliniai vaizdai (nuskaitomi visi vaizdai)
* HDD vietoj SSD saugyklos
* Nepakankami sistemos ištekliai
* Nustatyta daug indeksų
* Prieiga prie tinklo disko

**Sprendimai:**

1. Jei tik pradėta ir esate aptikimo etape: atšaukti, pažymėti tikslus, pradėti iš naujo
2. Ateityje: naudoti SSD, sumažinti indeksus, atnaujinti aparatūrą
3. Apsvarstyti CLI didelių duomenų rinkinių paketinio apdorojimo atveju

### „Diskų vietos“ įspėjimai

**Sprendimai:**

1. Nedelsiant išlaisvinti diskų vietą
2. Perkelti projektą į diską, kuriame yra daugiau vietos
3. Sumažinkite eksportuojamų indeksų skaičių.
4. Naudokite JPG formatą vietoj TIFF (mažesni failai).

### Dažni „Sugadinto failo“ pranešimai

**Sprendimai:**

1. Pakartotinai nukopijuokite vaizdus iš SD kortelės, kad užtikrintumėte jų vientisumą.
2. Patikrinkite SD kortelę dėl klaidų.
3. Pašalinkite sugadintus failus iš projekto.
4. Tęskite likusių vaizdų apdorojimą.

### Sistemos perkaitimas / greičio ribojimas

**Sprendimai:**

1. Užtikrinkite tinkamą ventiliaciją.
2. Nuvalykite dulkes nuo kompiuterio ventiliacijos angų.
3. Sumažinkite apdorojimo apkrovą (naudokite laisvąjį režimą vietoj Chloros+).
4. Apdorokite vėsesniu paros metu.

***

## Pranešimas apie apdorojimo pabaigą

Kai apdorojimas baigiamas:

* Pažangos juosta pasiekia 100 %
* Debug Log (Debug žurnale) pasirodo pranešimas **„Processing Complete“ (Apdorojimas baigtas)**
* Vėl įjungiamas paleidimo mygtukas
* Visi išvesties failai yra fotoaparato modelio pakatalogyje

***

## Tolimesni veiksmai

Baigus apdorojimą:

1. **Peržiūrėkite rezultatus** – žr. [Apdorojimo užbaigimas](finishing-the-processing.md)
2. **Patikrinkite išvesties aplanką** – patikrinkite, ar visi failai eksportuoti teisingai
3. **Peržiūrėkite Debug Log** – patikrinkite, ar nėra įspėjimų ar klaidų
4. **Peržiūrėkite apdorotus vaizdus** – naudokite Image Viewer arba išorinę programinę įrangą

Informaciją apie apdorotų rezultatų peržiūrą ir naudojimą rasite [Apdorojimo užbaigimas](finishing-the-processing.md).
