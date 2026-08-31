# Apdorojimo stebėjimas

Pradėjus apdorojimą, Chloros siūlo keletą būdų stebėti pažangą, tikrinti, ar nėra problemų, ir suprasti, kas vyksta su jūsų duomenų rinkiniu. Šiame puslapyje paaiškinama, kaip stebėti apdorojimą ir interpretuoti informaciją, kurią teikia Chloros.

## Pažangos juostos apžvalga

Viršutinėje antraštėje esanti pažangos juosta rodo apdorojimo būseną realiuoju laiku ir užbaigtumo procentą. Pažanga transliuojama tiesiogiai iš serverio per „Server-Sent Events“ (SSE), todėl juosta atspindi tai, ką apdorojimo grandinė iš tikrųjų daro.

### Pažangos juosta nemokamame režime

Vartotojams, neturintiems „Chloros+“ licencijos:

**2 etapų pažangos rodymas:**

1.**Tikslo aptikimas** – kalibravimo tikslų paieška vaizduose
2. **Apdorojimas** – pataisų taikymas ir eksportavimas**Pažangos juosta rodo:**

* Bendrą užbaigtumo procentą (0–100 %)
* Dabartinio etapo pavadinimą
* Paprastą horizontalios juostos vizualizaciją

### „Chloros+“ pažangos juosta

Vartotojams, turintiems „Chloros+“ licenciją:

**4 etapų pažangos rodymas:**

1.**Aptikimas** – kalibravimo taškų paieška
2. **Analizė** – vaizdų tyrimas ir apdorojimo proceso paruošimas
3. **Kalibravimas** – vinjetės ir atspindžio korekcijų taikymas
4. **Eksportavimas** – apdorotų failų išsaugojimas**Interaktyvios funkcijos:*** **Nukreipkite pelę** ant pažangos juostos, kad pamatytumėte išplėstą 4 etapų skydelį
* **Spustelėkite** pažangos juostą, kad sustabdytumėte / pritvirtintumėte išplėstą skydelį
* **Spustelėkite dar kartą**, kad atšaldytumėte ir automatiškai paslėptumėte, kai pelė bus nuvesta
* Kiekviename etape rodoma atskira pažanga (0–100 %)

{% hint style="info" %}
**CLI paritetas**: vykstant `chloros-cli process` procesui tie patys keturi srautai rodo „Aptinkama“, „Analizuojama“, „Apdorojama“, „Eksportuojama“, o `chloros-cli export-status` rodo 4-ojo srauto eksporto pažangą iš kito terminalo. Žr. [CLI nuorodą](../reference/cli-reference.md).
{% endhint %}

***

## Kiekvieno apdorojimo etapo supratimas

{% hint style="info" %}
**Konvejerio architektūra**: Šie 4 GUI etapai atitinka [4 sriegių apdorojimo konvejerį](../processing-architecture/processing-pipeline.md). Sistemose su GPU pagreitinimu 3-iasis sriegis (Kalibravimas) naudoja [dinaminį skaičiavimo pritaikymą](../processing-architecture/dynamic-compute-adaptation.md), kuris optimizuoja apdorojimą pagal jūsų konkrečią aparatūrą.
{% endhint %}

### 1 etapas: (Tikslo aptikimas)

**Kas vyksta:**

* Chloros nuskaito vaizdus, kuriuos pažymėjote žymimuoju langeliu „Tikslas“ (visus vaizdus, jei nė vienas nėra pažymėtas)
* Kompiuterinio regėjimo algoritmai atpažįsta kalibravimo plokštes
* Iš kiekvienos plokštės išgautos atspindžio vertės
* Užregistruojami tikslų laiko žymos, kad būtų galima tinkamai suplanuoti kalibravimą

**Trukmė:**

* Su pažymėtais tikslais: 10–60 sekundžių
* Be pažymėtų tikslų: 5–30+ minučių (nuskaitomi visi vaizdai)

**Pažangos indikatorius:**

* Aptikimas: 0 % → 100 %
* Nuskaitytų vaizdų skaičius (skaičiuojami tik tie vaizdai, kurie iš tiesų yra nuskaityti)
* Rastų tikslų skaičius

**Į ką reikia atkreipti dėmesį:**

* Jei tikslai tinkamai pažymėti, procesas turėtų baigtis greitai
* Jei trunka per ilgai, tikslai gali būti nepažymėti
* Patikrinkite „Debug Log“ (derinimo žurnalą), ar yra pranešimų „Target found“ (Tikslas rastas)

### 2 etapas: Analizė

**Kas vyksta:**

* Vaizdų EXIF metaduomenų skaitymas (laiko žymos, ekspozicijos nustatymai)
* Kalibravimo strategijos nustatymas remiantis taikinio laiko žymomis ir turimais DAQ duomenimis apie spinduliavimą žemyn
* Vaizdų apdorojimo eilės tvarkymas
* Lygiagretaus apdorojimo procesų paruošimas (tik Chloros+)

**Trukmė:** 5–30 sekundžių**Pažangos indikatorius:**

* Analizuojama: 0 % → 100 %
* Greitas etapas, paprastai baigiasi greitai

**Į ką reikia atkreipti dėmesį:**

* Pažanga turėtų vykti tolygiai, be pertraukų
* Įspėjimai apie trūkstamus metaduomenis bus rodomi „Debug Log“

### 3 etapas: Kalibravimas

**Kas vyksta:*** **Debayering**: RAW Bayer modelio konvertavimas į 3 kanalus (praleidžiama „LATTICE“ mono moduliams, su pastaba)
* **Vignette korekcija**: objektyvo kraštų patamsėjimo pašalinimas
* **Atspindžio kalibravimas**: normalizavimas pagal tikslines vertes ir (arba) DAQ žemyn nukreiptą spinduliavimą
* **Indeksų skaičiavimas**: daugiaspektrinių indeksų apskaičiavimas
* Kiekvieno vaizdo apdorojimas per visą apdorojimo grandinę

**Trukmė:** didžioji dalis bendro apdorojimo laiko (60–80 %)**Pažangos indikatorius:**

* Kalibravimas: 0 % → 100 %
* Šiuo metu apdorojamas vaizdas
* Apdoroti vaizdai / Visi vaizdai

**Apdorojimo elgsena:*** **Laisvasis režimas**: Apdoroja po vieną vaizdą paeiliui
* **Chloros+ režimas**: Naudoja prie aparatinės įrangos prisitaikantį darbininkų pulką — 1–4 vienu metu veikiančius darbininkus GPU sistemose (priklausomai nuo VRAM), po vieną darbininką kiekvienam fiziniam branduoliui (atėmus vieną) sistemose, turinčiose tik CPU. Žr. [Dinaminis skaičiavimo prisitaikymas](../processing-architecture/dynamic-compute-adaptation.md)
* **GPU pagreitinimas**: Žymiai pagreitina šį etapą**Į ką reikia atkreipti dėmesį:**

* Nuolatinė pažanga pagal vaizdų skaičių
* Patikrinkite „Debug Log“ (derinimo žurnalą), ar yra pranešimai apie kiekvieno vaizdo užbaigimą
* Įspėjimai apie vaizdo kokybės ar kalibravimo problemas

### 4 etapas: Eksportavimas

**Kas vyksta:**

* Apdoroti vaizdai, kai tik baigiami apdoroti, įrašomi į diską pasirinktu formatu
* **LATTICE**: kiekvienas kadras išskirstomas į visus įjungtus produktus (debayered / peržiūra / spinduliavimas / atspindys)
* Eksportuojami multispektriniai indeksiniai vaizdai su LUT spalvomis
* Sukuriama išvesties medžio struktūra `<project>/<camera>/<format>/<Product>_Images/` — eksportuoti failai išlaiko šaltinio failo pavadinimą; aplankas identifikuoja produktą

**Trukmė:** 10–20 % viso apdorojimo laiko**Pažangos indikatorius:**

* Eksportavimas: 0 % → 100 %
* Rašomi failai
* Eksporto formatas ir paskirties vieta

**Į ką reikia atkreipti dėmesį:**

* Įspėjimai apie disko vietos trūkumą
* Failų rašymo klaidos
* Visų sukonfigūruotų išvesties elementų užbaigimas

***

## Skirtukas „Debug Log“

Debug Log pateikia išsamią informaciją apie apdorojimo eigą ir visas iškilusias problemas. Į žurnalo konsolę taip pat perrašomi vidinio modulio paleidimo pranešimai, todėl žurnale pateikiama visa informacija, net jei jį atidarote vėliau.

### Kaip pasiekti „Debug Log“

1. Spustelėkite **„Debug Log“** piktogramą<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

kairiajame šoniniame meniu
2. Atsidarys žurnalo skydelis, kuriame rodomi apdorojimo pranešimai realiuoju laiku
3. Žurnalas automatiškai slenka, kad būtų rodomi naujausi pranešimai

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written) -->

### Kaip suprasti žurnalo pranešimus

Chloros žurnalo eilutės prasideda skliaustuose nurodytomis žymėmis, nurodančiomis posistemį — pavyzdžiui, `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Svarbiausia žinoti **vykdymo santrauką**, pateikiamas kiekvieno vykdymo pabaigoje (įskaitant sustabdytus vykdymus):

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Papildomos `[RUN-SUMMARY]` paaiškinamosios eilutės pateikiamos tada, kai reikia ką nors paaiškinti — pavyzdžiui, jei vykdymas nieko nesukūrė arba jei kameros prašomas produktas buvo praleistas kaip netaikytinas. `[EXPORT-CHECK]` eilutės paaiškina praleidimus pagal kameras (pvz., kodėl kamera RGB negavo spinduliavimo produkto).

Bendros pranešimų svarbos kategorijos (žemiau pateikti pavyzdžiai yra iliustraciniai, o ne pažodiniai):

#### Informaciniai pranešimai (balti/pilki)

Įprasti apdorojimo atnaujinimai: apdorojimas pradėtas, aptikti objektai (su skydelių skaičiumi), kiekvieno vaizdo kalibravimo eiga, eksportuoti failai, apdorojimas baigtas.

#### Įspėjamieji pranešimai (geltoni)

Nekritinės problemos, kurios nesustabdo apdorojimo — pvz., trūkstami GPS duomenys kadre, didelis laiko žymų tarpas tarp tikslų vaizdų arba mažas kalibravimo panelės kontrastas.

**Veiksmas:** Peržiūrėkite įspėjimus po apdorojimo, bet nepertraukite

#### Klaidų pranešimai (Red)

Kritinės problemos, dėl kurių apdorojimas gali žlugti — pvz., užpildytas diskas, sugadintas vaizdo failas arba neaptikti objektai, kai buvo prašoma atlikti atspindžio kalibravimą.

**Veiksmas:** Sustabdykite apdorojimą, pašalinkite klaidą, paleiskite iš naujo

### Dažnos žurnalo situacijos

| Situacija                             | Reikšmė                                       | Reikalingas veiksmas                                         |
| ------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| Taikinys aptiktas \[failo pavadinimas]        | Kalibravimo taikinys sėkmingai rastas         | Nėra – normalu                                         |
| Pažangos juostos prie kiekvieno vaizdo              | Dabartinės pažangos atnaujinimas                       | Nėra – normalu                                         |
| Nėra aptiktų tikslų                      | Neaptikta kalibravimo tikslų               | Pažymėkite tikslų vaizdus arba išjunkite atspindžio kalibravimą |
| Nepakanka vietos diske               | Nepakanka vietos išvesties duomenims                 | Atlaisvinkite vietą diske                                    |
| Praleidžiamas sugadintas failas               | Vaizdo failas yra sugadintas                         | Pakartotinai nukopijuokite failą iš SD kortelės                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Negalima apdoroti įrašo be „raw“ kadro | Pakartotinai užfiksuokite su „raw“ arba naudokite CLI `--input-level`  |
| `[RUN-SUMMARY] ... 0 file(s) written` | Vykdymo metu nesukurta jokių vaizdo produktų — pranešta apie nesėkmę su patarimais | Perskaitykite patarimų eilutes; patikrinkite, kas buvo praleista ir kodėl |

### Žurnalo duomenų kopijavimas

Norėdami nukopijuoti žurnalą trikčių šalinimo ar techninės pagalbos tikslais:

1. Atidarykite „Debug Log“ (Debugavimo žurnalas) skydelį
2. Spustelėkite mygtuką **„Copy Log“** (Kopijuoti žurnalą) (arba dešiniuoju pelės mygtuku spustelėkite → „Select All“ (Pažymėti viską))
3. Įklijuokite į tekstinį failą arba el. laišką
4. Jei reikia, nusiųskite į MAPIR techninę pagalbą

***

## Sistemos išteklių stebėjimas

### Procesoriaus (CPU) naudojimas

**Laisvasis režimas:**

* 1 procesoriaus branduolys veikia ~100 %
* Kiti branduoliai neveikia arba yra laisvi
* Sistema išlieka reaguojanti

**Chloros+ Lygiagretusis režimas:**

* Daugelis branduolių dirba dideliu našumu — kiek tiksliai, priklauso nuo strategijos, pasirinktos naudojant [dinaminį skaičiavimo prisitaikymą](../processing-architecture/dynamic-compute-adaptation.md)
* Sistema gali atrodyti mažiau reaguojanti

**Kaip stebėti:**

* „Windows“ užduočių tvarkyklė (Ctrl+Shift+Esc)
* Skirtukas „Našumas“ → skyrius „Procesorius“
* Ieškokite procesų „Chloros“ arba „chloros-backend“

### Atminties (RAM) naudojimas

**Tipinis naudojimas:**

* Maži projektai (&lt; 100 vaizdų): 2–4 GB
* Vidutinio dydžio projektai (100–500 vaizdų): 4–8 GB
* Didelio dydžio projektai (500+ vaizdų): 8–16 GB
* „Chloros+“ lygiagretusis režimas naudoja daugiau RAM

**Jei trūksta atminties:**

* Apdorokite mažesnes partijas
* Uždarykite kitas programas
* Jei reguliariai apdorojate didelius duomenų rinkinius, padidinkite RAM talpą

### GPU naudojimas (Chloros+ su CUDA)

Kai įjungtas GPU pagreitinimas:

* NVIDIA GPU naudojimas yra didelis (60–90 %)
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
* SSD veikia žymiai greičiau nei HDD

**Našumo patarimas:**

* Jei įmanoma, projekto aplankui naudokite SSD
* Vengti tinklo diskų, jei duomenų rinkiniai dideli
* Užtikrinkite, kad diske nebūtų beveik visos talpos (tai daro įtaką rašymo greičiui)

***

## Problemų nustatymas apdorojimo metu

### Įspėjamieji ženklai

**Procesas sustoja (jokių pokyčių 5 ar daugiau minučių):**

* Patikrinkite „Debug Log“ dėl klaidų
* Patikrinkite, ar yra laisvos vietos diske
* Patikrinkite užduočių tvarkyklę, ar veikia procesas „Chloros“

**Dažnai rodomi klaidų pranešimai:**

* Sustabdykite apdorojimą ir peržiūrėkite klaidas
* Dažniausios priežastys: vietos trūkumas diske, sugadinti failai, atminties problemos
* Žr. skyrių „Trikčių šalinimas“ žemiau

**Sistema nereaguoja:**

* „Chloros+“ lygiagretusis režimas naudoja per daug išteklių
* Apsvarstykite galimybę sumažinti vienu metu vykdomų užduočių skaičių arba atnaujinti aparatinę įrangą
* Laisvasis režimas naudoja mažiau išteklių

### Kada sustabdyti apdorojimą

Sustabdykite apdorojimą, jei pastebite:

* ❌ Klaidas „Diskas pilnas“ arba „Negaliu įrašyti failo“
* ❌ Pasikartojančias vaizdo failų sugadinimo klaidas
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

* Nežymėti tiksliniai vaizdai (nuskaitomi visi vaizdai)
* Naudojamas HDD, o ne SSD
* Nepakankami sistemos ištekliai
* Nustatyta daug indeksų
* Prieiga prie tinklo disko

**Sprendimai:**

1. Jei procesas ką tik prasidėjo ir yra „Aptikimo“ etape: sustabdykite, pažymėkite tikslus, paleiskite iš naujo
2. Ateityje: naudokite SSD, sumažinkite indeksų skaičių, atnaujinkite aparatūrą
3. Apsvarstykite galimybę naudoti „CLI“ didelių duomenų rinkinių paketiniam apdorojimui

### Įspėjimai apie „diskų vietą“

**Sprendimai:**

1. Nedelsiant atlaisvinkite vietos diske
2. Perkelkite projektą į diską, kuriame yra daugiau vietos
3. Sumažinkite eksportuotinų indeksų skaičių
4. Išjunkite nereikalingus „LATTICE“ eksporto produktus („Project Settings“ → „Processing“)
5. Vietoj „TIFF“ naudokite JPG formatą (mažesni failai)

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
3. Sumažinkite apdorojimo apkrovą (naudokite „Free“ režimą vietoj „Chloros+“)
4. Apdorokite vėsesniu paros metu

***

## Pranešimas apie apdorojimo pabaigą

Kai apdorojimas baigiamas:

* Pažangos juosta pasiekia 100 %
* Debug Log žurnale pasirodo eilutės „`[RUN-SUMMARY]`“ su galutiniais skaičiais
* Mygtukas „Pradėti“ vėl tampa aktyvus
* Visi išvesties failai yra projekto išvesties medyje, suskirstyti pagal kameras: `<project>/<camera>/<format>/<Product>_Images/`

***

## Tolimesni veiksmai

Baigus apdorojimą:

1. **Peržiūrėkite rezultatus** – žr. [Apdorojimo užbaigimas](finishing-the-processing.md)
2. **Patikrinkite išvesties aplanką** – įsitikinkite, kad visi failai buvo eksportuoti teisingai
3. **Peržiūrėkite „Debug Log“** – patikrinkite, ar nėra įspėjimų ar klaidų
4. **Peržiūrėkite apdorotus vaizdus** – naudokite vaizdų peržiūros programą arba išorinę programinę įrangą

Informaciją apie apdorotų rezultatų peržiūrą ir naudojimą rasite skyriuje [Apdorojimo užbaigimas](finishing-the-processing.md).
