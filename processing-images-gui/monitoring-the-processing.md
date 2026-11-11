# Apdorojimo stebėjimas

Pradėjus apdorojimą, Chloros

siūlo keletą būdų stebėti pažangą, tikrinti, ar nėra problemų, ir suprasti, kas vyksta su jūsų duomenų rinkiniu. Šiame puslapyje paaiškinama, kaip stebėti apdorojimo eigą ir interpretuoti Chloros

teikiamą informaciją.

## Pažangos juostos apžvalga

Viršutinėje antraštėje esanti pažangos juosta rodo apdorojimo būseną realiuoju laiku ir užbaigtumo procentą. Pažanga transliuojama tiesiogiai iš serverio per „Server-Sent Events“ (SSE), todėl juosta atspindi tai, ką apdorojimo grandinė iš tikrųjų atlieka.

### „Free Mode“ pažangos juosta

Vartotojams, neturintiems „Chloros

+“ licencijos:

**2 etapų pažangos rodymas:**

1.**Tikslo aptikimas** – kalibravimo tikslų paieška vaizduose
2. **Apdorojimas** – pataisų taikymas ir eksportavimas**Pažangos juosta rodo:**

* Bendrą užbaigtumo procentą (0–100 %)
* Dabartinio etapo pavadinimą
* Paprastą horizontalios juostos vizualizaciją

### „Chloros

+“ pažangos juosta

Vartotojams, turintiems „Chloros

+“ licenciją:

**4 etapų pažangos rodymas:**

1.**Aptikimas** – kalibravimo taškų paieška
2. **Analizė** – vaizdų tyrimas ir apdorojimo proceso paruošimas
3. **Kalibravimas** – vinjetės ir atspindžio korekcijų taikymas
4. **Eksportavimas** – apdorotų failų išsaugojimas**Interaktyvios funkcijos:*** **Užveskite pelę** ant pažangos juostos, kad pamatytumėte išplėstą 4 etapų skydelį
* **Spustelėkite** pažangos juostą, kad užfiksuotumėte išplėstą skydelį
* **Spustelėkite dar kartą**, kad atšaldytumėte ir kad skydelis automatiškai pasislėptų, kai nuimsite pelę
* Kiekviename etape rodoma atskira pažanga (0–100 %)

{% hint style="info" %}
**CLI

atitikimas**: vykdant `chloros-cli process`, tie patys keturi srautai rodomi kaip „Aptikimas“, „Analizė“, „Apdorojimas“, „Eksportavimas“, o `chloros-cli export-status` rodo 4-ojo srauto eksporto pažangą realiuoju laiku iš kito terminalo. Žr. [CLI

žinyną](../reference/cli-reference.md).
{% endhint %}

***

## Kiekvieno apdorojimo etapo supratimas

{% hint style="info" %}
**Konvejerio architektūra**: Šie 4 GUI etapai atitinka [4 sriegių apdorojimo konvejerį](../processing-architecture/processing-pipeline.md). Sistemose su GPU pagreitinimu 3-iasis srautas (kalibravimas) naudoja [dinaminį skaičiavimo pritaikymą](../processing-architecture/dynamic-compute-adaptation.md), kuris optimizuoja apdorojimą pagal jūsų konkrečią aparatinę įrangą.
{% endhint %}

### 1 etapas: aptikimas (tikslo aptikimas)

**Kas vyksta:**

* Chloros

nuskaito vaizdus, kuriuos pažymėjote žymimuoju langeliu „Tikslas“ (visus vaizdus, jei nė vienas nėra pažymėtas)
* Kompiuterinio regėjimo algoritmai atpažįsta kalibravimo plokštes
* Iš kiekvienos plokštės išgaunamos atspindžio vertės
* Užregistruojami tikslų laiko žymos, kad būtų galima tinkamai suplanuoti kalibravimą

**Trukmė:**

* Su pažymėtais tikslais: 10–60 sekundžių
* Be pažymėtų tikslų: 5–30+ minučių (nuskaitomi visi vaizdai)

**Pažangos indikatorius:**

* Aptikimas: 0 % → 100 %
* Nuskaitytų vaizdų skaičius (skaičiuojami tik tie vaizdai, kurie iš tikrųjų yra nuskaitomi)
* Rastų tikslų skaičius

**Į ką reikia atkreipti dėmesį:**

* Jei tikslai tinkamai pažymėti, procesas turėtų baigtis greitai
* Jei trunka per ilgai, tikslai gali būti nepažymėti
* Patikrinkite „Debug Log“ (derinimo žurnalą), ar yra pranešimų „Target found“ (Tikslas rastas)

### 2 etapas: Analizė

**Kas vyksta:**

* Vaizdų EXIF metaduomenų skaitymas (laiko žymos, ekspozicijos nustatymai)
* Kalibravimo strategijos nustatymas remiantis tikslų laiko žymomis ir turimais DAQ žemyn nukreiptų spindulių duomenimis
* Vaizdų apdorojimo eilės organizavimas
* Lygiagretaus apdorojimo procesų paruošimas (tik „Chloros

+“)

**Trukmė:** 5–30 sekundžių**Pažangos indikatorius:**

* Analizuojama: 0 % → 100 %
* Greitas etapas, paprastai užbaigiamas greitai

**Į ką reikia atkreipti dėmesį:**

* Pažanga turėtų vykti tolygiai, be pertraukų
* Įspėjimai apie trūkstamus metaduomenis bus rodomi „Debug Log“

### 3 etapas: Kalibravimas

**Kas vyksta:*** **Debayering**: RAW Bayer modelio konvertavimas į 3 kanalus (praleidžiama „LATTICE“ mono moduliams, su pastaba)
* **Vignette korekcija**: objektyvo kraštų patamsėjimo pašalinimas
* **Atspindžio kalibravimas**: normalizavimas pagal tikslinės reikšmes ir (arba) DAQ duomenų srautą
* **Indekso skaičiavimas**: multispektrinių indeksų apskaičiavimas
* Kiekvieno vaizdo apdorojimas per visą apdorojimo grandinę

**Trukmė:** Didžioji dalis bendro apdorojimo laiko (60–80 %)**Pažangos indikatorius:**

* Kalibravimas: 0 % → 100 %
* Šiuo metu apdorojamas vaizdas
* Apdoroti vaizdai / Visi vaizdai

**Apdorojimo režimai:*** **Laisvasis režimas**: Apdoroja po vieną vaizdą paeiliui
* **„Chloros

+“ režimas**: Naudoja prie aparatinės įrangos prisitaikantį darbininkų pulką — 1–4 vienu metu veikiančius darbininkus GPU sistemose (priklausomai nuo VRAM), po vieną darbininką kiekvienam fiziniam branduoliui (atėmus vieną) sistemose, turinčiose tik CPU. Žr. [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md)
* **GPU pagreitinimas**: Žymiai pagreitina šį etapą**Į ką reikia atkreipti dėmesį:**

* Nuoseklus pažangos rodymas pagal vaizdų skaičių
* Patikrinkite „Debug Log“ (derinimo žurnalą), ar yra pranešimai apie kiekvieno vaizdo apdorojimo užbaigimą
* Įspėjimai apie vaizdo kokybės ar kalibravimo problemas

### 4 etapas: Eksportavimas

**Kas vyksta:**

* Apdoroti vaizdai, kai tik baigiami apdoroti, įrašomi į diską pasirinktu formatu
* **LATTICE**: kiekvienas kadras išskirstomas į visus įjungtus produktus (debayering / peržiūra / spinduliavimas / atspindys)
* Eksportuojami multispektriniai indeksiniai vaizdai su LUT spalvomis
* Sukuriama išvesties medžio struktūra `<project>/<camera>/<format>/<Product>_Images/` — eksportuoti failai išlaiko šaltinio failo pavadinimą; aplankas identifikuoja produktą

**Trukmė:** 10–20 % viso apdorojimo laiko**Pažangos indikatorius:**

* Eksportavimas: 0 % → 100 %
* Įrašomi failai
* Eksporto formatas ir paskirties vieta

**Į ką reikia atkreipti dėmesį:**

* Įspėjimai apie disko vietos trūkumą
* Failų įrašymo klaidos
* Visų sukonfigūruotų išvesties duomenų užbaigimas

***

## Skirtukas „Debug Log“ (Debugavimo žurnalas)

Debugavimo žurnale pateikiama išsami informacija apie apdorojimo eigą ir visas iškilusias problemas. Žurnalo konsolėje taip pat atkuriamos vidinės sistemos paleidimo žinutės, todėl žurnalas pateikia visą informaciją, net jei jį atidarysite vėliau.

### Kaip pasiekti „Debug Log“

1. Spustelėkite **„Debug Log“** piktogramą <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

kairiajame šoniniame meniu
2. Atsidarys žurnalo langas, kuriame rodomi apdorojimo pranešimai realiuoju laiku
3. Langas automatiškai slenka, kad būtų rodomi naujausi pranešimai

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written), with the 1.3.0 bug icon (Report a bug) visible at the top of the tab -->

### Žurnalo pranešimų supratimas

Chloros

žurnalo eilutės prasideda skliaustuose esančiomis žymėmis, nurodančiomis posistemį — pavyzdžiui, `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Svarbiausia žinoti **vykdymo santrauką**, pateikiamą kiekvieno vykdymo pabaigoje (įskaitant sustabdytus vykdymus):

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Papildomos `[RUN-SUMMARY]` paaiškinamosios eilutės pateikiamos tada, kai reikia ką nors paaiškinti — pavyzdžiui, jei vykdymo metu nebuvo gauta jokių rezultatų, arba kamera, kurios prašytas rezultatas buvo praleistas kaip netaikytinas. `[EXPORT-CHECK]` eilutės paaiškina praleidimus pagal kamerą (pvz., kodėl RGB

kamera negavo spinduliavimo rezultato).

Bendros pranešimų svarbos kategorijos (toliau pateikti pavyzdžiai yra iliustraciniai, o ne pažodiniai):

#### Informaciniai pranešimai (balti/pilki)

Įprasti apdorojimo atnaujinimai: apdorojimas pradėtas, aptikti taikiniai (su skydelių skaičiumi), kiekvieno vaizdo kalibravimo eiga, eksportuoti failai, apdorojimas baigtas.

#### Įspėjamieji pranešimai (geltoni)

Nekritinės problemos, kurios nesustabdo apdorojimo — pvz., trūkstami GPS duomenys kadre, didelis laiko žymių skirtumas tarp tikslų vaizdų arba mažas kalibravimo panelės kontrastas.

**Veiksmas:** Peržiūrėkite įspėjimus po apdorojimo, bet nepertraukite jo

#### Klaidų pranešimai (raudoni – Red

)

Kritinės problemos, dėl kurių apdorojimas gali žlugti – pvz., užpildytas diskas, sugadintas vaizdo failas arba neaptikti objektai, kai buvo prašoma atlikti atspindžio kalibravimą.

**Veiksmas:** Sustabdykite apdorojimą, išspręskite klaidą, paleiskite iš naujo

### Dažnos žurnalo situacijos

| Situacija                             | Reikšmė                                       | Reikalingi veiksmai                                         |
| ------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| Objektas aptiktas \[failo pavadinimas]        | Kalibravimo objektas sėkmingai rastas         | Nėra – normalu                                         |
| Pažangos juostos kiekvienam vaizdui              | Dabartinės pažangos atnaujinimas                       | Nėra – normalu                                         |
| Taikinių nerasta                      | Nerasta kalibravimo taikinių               | Pažymėkite taikinių vaizdus arba išjunkite atspindžio kalibravimą |
| Nepakanka vietos diske               | Nepakanka vietos išvesties duomenims                 | Atlaisvinkite vietos diske                                    |
| Praleidžiamas sugadintas failas               | Vaizdo failas yra sugadintas                         | Pakartotinai nukopijuokite failą iš SD kortelės                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Negalima apdoroti įrašo be „raw“ kadro | Pakartotinai užfiksuokite su „raw“, arba naudokite CLI

`--input-level`  |
| `[RUN-SUMMARY] ... 0 file(s) written` | Vykdymo metu nebuvo sukurta jokių vaizdų produktų — pranešta apie nesėkmę su patarimais | Perskaitykite patarimų eilutes; patikrinkite, kas buvo praleista ir kodėl |
| `[PROCESS] refused: The project folder is too deep for this computer…` | Windows

ilgieji keliai išjungti, o projekto aplankas yra per giliai, kad tilptų į 260 simbolių kelio ribą; niekas nebuvo apdorota | Naudokite trumpesnį projekto aplanką (pranešime nurodytas ilgiausias tinkamas kelias), arba įjunkite Windows

ilgus kelius ir iš naujo paleiskite Chloros

— žr. [Apdorojimo paleidimas](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer) |
| `[unmix-clamp-mask] …` | Nepavyko įrašyti „LATTICE“ produkto fiksavimo kaukės; pats produktas veikia tinkamai ir rodo `Chloros:UnmixClampMask=unavailable` | Patikrinkite disko vietą ir aplanko teises, tada, jei kaukė jums reikalinga, apdorokite dar kartą |

### Žurnalo duomenų kopijavimas

Norėdami nukopijuoti žurnalą trikčių šalinimo ar techninės pagalbos tikslais:

1. Atidarykite „Debug Log“ skydelį
2. Spustelėkite mygtuką **„Copy“**(žurnalas nukopijuojamas į iškarpinę) arba**„Download“**, kad jį išsaugotumėte kaip tekstinį failą
3. Įklijuokite į tekstinį failą arba el. laišką
4. Jei reikia, nusiųskite į MAPIR

techninę pagalbą

### Klaidos pranešimas iš žurnalo skirtuko

Nauja 1.3.0 versijoje: klaidos piktograma (**Pranešti apie klaidą**) „Debug Log“ skirtuko viršuje siunčia pranešimą tiesiai į MAPIR

.

1. Spustelėkite klaidos piktogramą „Debug Log“ skirtuko viršuje
2. Aprašykite problemą: ką darėte, ko tikėjotės ir kas įvyko vietoj to (privaloma)
3. Jei norite, pažymėkite **Pridėti keletą paveikslėlių (iki 10)**ir pasirinkite paveikslėlius iš atidaryto projekto arba naudokite**Naršyti...**, kad pasirinkite bet kurį kitą paveikslėlio failą
4. Spustelėkite **Siųsti**

Ataskaitoje pateikiamas jūsų aprašymas, visi pridėti paveikslėliai, Chloros

žurnalai ir sistemos duomenys (Chloros

versija, operacinė sistema, GPU ir projekto nustatymai). Norėdami išsiųsti ataskaitą, turite būti prisijungę prie Chloros

(„Vartotojas“ skirtuke). Siuntimo metu galite uždaryti langą; kai siuntimas bus baigtas, į žurnalą bus įrašytas nuorodos numeris, pvz., `Bug report sent. Reference: BR-…` — jį nurodykite, kai kreipsitės į MAPIR

. Jei ataskaitos nepavyksta įkelti (pavyzdžiui, dėl interneto ryšio nebuvimo), Chloros

ją išsaugo jūsų kompiuteryje ir parodo jos kelią, kad galėtumėte ją išsiųsti el. paštu adresu support@mapir.camera.

***

## Sistemos išteklių stebėjimas

### Procesoriaus (CPU) naudojimas

**Laisvasis režimas:**

* 1 procesoriaus branduolys veikia ~100 %
* Kiti branduoliai neveikia arba yra laisvi
* Sistema išlieka reaguojanti

**Chloros

lygiagretusis režimas:**

* Keli branduoliai veikia dideliu našumu — kiek tiksliai, priklauso nuo strategijos, pasirinktos naudojant [dinaminį skaičiavimo pritaikymą](../processing-architecture/dynamic-compute-adaptation.md)
* Sistema gali atrodyti mažiau reaguojanti

**Kaip stebėti:**

* Windows

užduočių tvarkyklė (Ctrl+Shift+Esc)
* Skirtukas „Našumas“ → skyrius „Procesorius“
* Ieškokite procesų „Chloros

“ arba „chloros-backend“

### Atminties (RAM) naudojimas

**Tipinis naudojimas:**

* Maži projektai (&lt; 100 vaizdų): 2–4 GB
* Vidutinio dydžio projektai (100–500 vaizdų): 4–8 GB
* Didelio dydžio projektai (500+ vaizdų): 8–16 GB
* „Chloros

+“ lygiagretusis režimas naudoja daugiau RAM

**Jei trūksta atminties:**

* Apdorokite mažesnes partijas
* Uždarykite kitas programas
* Jei reguliariai apdorojate didelius duomenų rinkinius, padidinkite RAM

### GPU naudojimas („Chloros

+“ su CUDA)

Kai įjungtas GPU pagreitinimas:

* „NVIDIA“ GPU naudojimas yra didelis (60–90 %)
* Padidėja VRAM naudojimas (reikia 4 GB ir daugiau VRAM; 7 GB ir daugiau, jei vienu metu atliekamas „Texture Aware“ debayeringas)
* Kalibravimo etapas vyksta žymiai greičiau

**Kaip stebėti:**

* „NVIDIA“ piktograma sisteminiame dėkle
* Užduočių tvarkyklė → Našumas → GPU
* „GPU-Z“ ar panaši stebėjimo priemonė

### Disko įvesties/išvesties operacijos

**Ko tikėtis:**

* Didelis disko skaitymo intensyvumas analizavimo etape
* Didelis disko rašymo intensyvumas eksportavimo etape
* SSD yra žymiai greitesnis už HDD

**Našumo patarimas:**

* Jei įmanoma, projekto aplankui naudokite SSD
* Dideliems duomenų rinkiniams venkite tinklo diskų
* Įsitikinkite, kad disko talpa nėra beveik išnaudota (tai daro įtaką rašymo greičiui)

***

## Problemų nustatymas apdorojimo metu

### Įspėjamieji ženklai

**Procesas sustoja (jokių pokyčių 5 ar daugiau minučių):**

* Patikrinkite „Debug Log“ dėl klaidų
* Patikrinkite, ar yra laisvos vietos diske
* Patikrinkite „Task Manager“, ar veikia Chloros



**Dažnai rodomi klaidų pranešimai:**

* Sustabdykite apdorojimą ir peržiūrėkite klaidas
* Dažniausios priežastys: vietos trūkumas diske, sugadinti failai, atminties problemos
* Žr. skyrių „Trikčių šalinimas“ žemiau

**Sistema nereaguoja:**

* Chloros

lygiagretusis režimas naudoja per daug išteklių
* Apsvarstykite galimybę sumažinti vienu metu vykdomų užduočių skaičių arba atnaujinti aparatinę įrangą
* Laisvasis režimas naudoja mažiau išteklių

### Kada sustabdyti apdorojimą

Sustabdykite apdorojimą, jei pastebite:

* ❌ Klaidos „Diskas užpildytas“ arba „Negaliu įrašyti failo“
* ❌ Pasikartojančios vaizdo failų sugadinimo klaidos
* ❌ Sistema visiškai užstrigo (nereaguoja)
* ❌ Supratote, kad buvo nustatyti neteisingi parametrai
* ❌ Importuoti neteisingi vaizdai

**Kaip sustabdyti:**

1. Spustelėkite**„Sustabdyti“ mygtuką** (jis pakeičia „Pradėti“ mygtuką) — pakanka vieno karto
2. Juostoje rodomas užrašas „Sustabdoma...“, kol baigiamas apdorojamas vaizdas, tada apdorojimas baigiasi sustabdytu būsenoje
3. Jau eksportuoti produktai lieka diske; žurnale išspausdinamas tikslus `[RUN-SUMMARY]` pranešimas apie tai, kas buvo užbaigta
4. Ištaisykite problemas ir paleiskite iš naujo — apdorojimas prasideda nuo pradžių

***

## Problemų šalinimas apdorojimo metu

### Apdorojimas vyksta labai lėtai

**Galimos priežastys:**

* Nepažymėti tiksliniai vaizdai (nuskaitomi visi vaizdai)
* Naudojamas HDD, o ne SSD
* Nepakankami sistemos ištekliai
* Nustatyta daug indeksų
* Prieiga prie tinklo disko

**Sprendimai:**

1. Jei procesas ką tik prasidėjo ir yra „Aptikimo“ etape: sustabdykite, pažymėkite tikslinius vaizdus, paleiskite iš naujo
2. Ateityje: naudokite SSD, sumažinkite indeksų skaičių, atnaujinkite aparatinę įrangą
3. Apsvarstykite galimybę naudoti CLI

, norint apdoroti didelius duomenų rinkinius partijomis

### Įspėjimai apie „diskų vietą“

**Sprendimai:**

1. Nedelsiant atlaisvinkite vietos diske
2. Perkelkite projektą į diską, kuriame yra daugiau vietos
3. Sumažinkite eksportuotinų indeksų skaičių
4. Išjunkite nereikalingus „LATTICE“ eksporto produktus („Project Settings“ → „Processing“)
5. Vietoj TIFF

naudokite „JPG“ formatą (mažesni failai)

### Dažni pranešimai apie „sugadintus failus“

**Sprendimai:**

1. Pakartotinai nukopijuokite vaizdus iš SD kortelės, kad užtikrintumėte jų vientisumą
2. Patikrinkite SD kortelę dėl klaidų
3. Pašalinkite sugadintus failus iš projekto
4. Tęskite likusių vaizdų apdorojimą

### Sistemos perkaitimas / našumo ribojimas

**Sprendimai:**

1. Užtikrinkite tinkamą ventiliaciją
2. Nuvalykite dulkes iš kompiuterio ventiliacijos angų
3. Sumažinkite apdorojimo apkrovą (naudokite „Free“ režimą vietoj „Chloros

+“)
4. Apdorokite vėsesniu paros metu

***

## Pranešimas apie apdorojimo pabaigą

Kai apdorojimas baigiamas:

* Pažangos juosta pasiekia 100 %
* Debug Log žurnale pasirodo eilutės „`[RUN-SUMMARY]`“ su galutiniais skaičiais
* Vėl tampa aktyvus mygtukas „Pradėti“
* Visi išvesties failai yra projekto išvesties medžio kataloge, skirtame kiekvienai kamerai: `<project>/<camera>/<format>/<Product>_Images/`

***

## Tolimesni veiksmai

Baigus apdorojimą:

1. **Peržiūrėkite rezultatus** – žr. [Apdorojimo užbaigimas](finishing-the-processing.md)
2. **Patikrinkite išvesties aplanką** – įsitikinkite, kad visi failai buvo eksportuoti teisingai
3. **Peržiūrėkite derinimo žurnalą** – patikrinkite, ar nėra įspėjimų ar klaidų
4. **Peržiūrėkite apdorotus vaizdus** – naudokite vaizdų peržiūros programą arba išorinę programinę įrangą

Informaciją apie apdorotų rezultatų peržiūrą ir naudojimą rasite skyriuje [Apdorojimo užbaigimas](finishing-the-processing.md).
