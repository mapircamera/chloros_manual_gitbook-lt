# Apdorojimo pradžia

Kai importavote vaizdus, pažymėjote kalibravimo taškus ir sukonfigūravote projekto nustatymus, galite pradėti apdorojimą. Šiame puslapyje pateikiamos instrukcijos, kaip paleisti Chloros apdorojimo procesą.

## Parengiamojo apdorojimo patikrinimo sąrašas

Prieš spustelėdami mygtuką „Pradėti“, įsitikinkite, kad viskas paruošta:

* [ ] **Failai importuoti** – visi vaizdai rodomi failų naršyklėje
* [ ] **Tiksliniai vaizdai pažymėti** – kalibravimo vaizdų stulpelyje pažymėta „Tiksliniai“ (arba importuotas „`.daq`“ įrašas, skirtas „LATTICE“)
* [ ] **Aptikti kamerų modeliai** – stulpelyje „Kameros modelis“ rodomos teisingos kameros
* [ ] **Nustatymai sukonfigūruoti** – projekto nustatymai peržiūrėti ir pakoreguoti
* [ ] **Pasirinkti indeksai** – pridėti norimi multispektriniai indeksai (jei reikia)
* [ ] **Pasirinkta eksporto forma** – jūsų darbo eigai tinkama išvesties forma

{% hint style="info" %}
**Patarimas**: Prieš pradedant apdorojimą, peržiūrėkite keletą vaizdų failų naršyklėje, kad įsitikintumėte, jog jie įkelti teisingai.
{% endhint %}

***

## Apdorojimo pradžia

### „Pradėti“ mygtuko paieška

„Pradėti/Paleisti“ mygtukas yra Chloros viršutinėje antraštės juostoje:

* Vieta: lango viršutinėje vidurinėje dalyje
* Piktograma: **„Paleisti/Pradėti“ mygtukas** <img src="../.gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">
* Būklė: mygtukas aktyvus (šviesus), kai viskas paruošta apdorojimui

### Spustelėkite, kad pradėtumėte

1. Spustelėkite **„Paleisti“/„Pradėti“ mygtuką** viršutinėje antraštėje
2. Apdorojimas prasideda iš karto
3. Apdorojimo metu mygtukas tampa **„Sustabdyti“** mygtuku
4. Pažangos juosta atnaujinama, rodydama apdorojimo būseną

{% hint style="success" %}
**Apdorojimas pradėtas**: Paspaudus mygtuką, Chloros automatiškai atlieka visus apdorojimo etapus – taškų aptikimą, „debayering“, kalibravimą, indekso skaičiavimą ir eksportavimą. Programa automatiškai nustato, ar jūsų projektas yra Survey3, „LATTICE“ ar jų derinys, ir kiekvienai kamerai taiko tinkamą apdorojimo seką.
{% endhint %}

***

## Apdorojimo režimų apžvalga

Chloros veikia dviem skirtingais apdorojimo režimais, priklausomai nuo jūsų licencijos:

### Nemokamas režimas (sekcinis apdorojimas)

**Prieinamas visiems vartotojams**

**Kaip veikia:**

* Vaizdus apdoroja po vieną, nuosekliai
* Vienos sriegio operacija
* Mažesnis atminties naudojimas

**Pažangos juosta rodo 2 etapus:**

1.**Tikslo aptikimas** – kalibravimo tikslų paieška
2. **Apdorojimas** – kalibravimo taikymas ir vaizdų eksportavimas**Apdorojimo trukmė:**

* Žymiai lėčiau nei „Chloros+“ lygiagretaus apdorojimo režime
* Tinka mažoms ir vidutinio dydžio duomenų rinkiniams (&lt; 200 vaizdų)

### „Chloros+“ režimas (lygiagretus apdorojimas)

**Reikalinga „Chloros+“ licencija**

**Kaip tai veikia:**

* Vienu metu apdoroja kelis vaizdus naudodamas [4 sriegių apdorojimo grandinę](../processing-architecture/processing-pipeline.md)
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) paleidimo metu automatiškai parenka optimalią strategiją jūsų aparatinei įrangai
* GPU (CUDA) pagreitinimas su „NVIDIA“ vaizdo plokštėmis
* **Darbininkų skaičius prisitaiko prie aparatinės įrangos**: GPU strategijos paleidžia**2–4 vienu metu veikiančius darbininkus** (skaičius priklauso nuo VRAM — 3 ant 2–12 GB GPU, 2–4 ant 12 GB ir didesnės talpos GPU); Sistemose, kuriose naudojamas tik CPU, vienam fiziniam branduoliui tenka vienas darbinis procesas, atėmus vieną**Pažangos juosta rodo 4 etapus** (atitinkančius 4 konvejerio sriegius):

1. **Aptikimas** (1-asis sriegis) – kalibravimo taškų paieška
2. **Analizė** (sąlytis Nr. 2) – vaizdo metaduomenų tikrinimas ir kalibravimo skaičiavimas
3. **Kalibravimas** (sąlytis Nr. 3) – „debayering“, vinjetės korekcija, kalibravimas, indekso skaičiavimas
4. **Eksportavimas** (sąlytis Nr. 4) – apdorotų vaizdų ir indeksų išsaugojimas**Veiksmai su pažangos juosta:*** **Užveskite pelę** ant juostos, kad pamatytumėte išsamų 4 etapų išskleidžiamąjį skydelį
* **Spustelėkite** pažangos juostą, kad išskleidžiamasis skydelis liktų fiksuotas
* **Spustelėkite dar kartą**, kad atšaldytumėte ir paslėptumėte skydelį**Apdorojimo trukmė:**

* Žymiai greitesnis nei nemokamas režimas
* GPU pagreitinimas dar labiau padidina greitį

{% hint style="info" %}
**„Chloros+“ greitis**: Dideliems duomenų rinkiniams lygiagretus apdorojimas gali būti 5–10 kartų greitesnis nei nuoseklusis režimas. 500 vaizdų projektas, kurio apdorojimas nemokamame režime trunka 2 valandas, naudojant „Chloros+“ gali būti užbaigtas per 15–20 minučių.
{% endhint %}

***

## Kas vyksta apdorojimo metu

### 1 etapas: Tikslo aptikimas

**Ką daro „Chloros“:**

* Nuskaito vaizdus, kuriuos pažymėjote stulpelyje „Tikslas“ (jei nepažymėta nė vieno, nuskaitomi visi vaizdai)
* Nustato kalibravimo plokštes kiekviename tiksle
* Išskiria atspindžio vertes iš tikslų plokščių
* Įrašo tikslų laiko žymes kalibravimo planavimui

**Trukmė:** 1–30 sekundžių (su pažymėtais tikslais), 5–30+ minučių (be pažymėjimų)

### 2 etapas: „Debayering“ (RAW konversija)

**Ką daro „Chloros“:**

* Konvertuoja RAW „Bayer“ modelio duomenis į pilnus 3 kanalų vaizdus („LATTICE“ mono moduliai lieka vienos juostos – jiems „debayering“ praleidžiamas, o žurnale paliekama pastaba)
* Taiko pasirinktą demosaicingo algoritmą
* Išsaugo maksimalią vaizdo kokybę ir detales

**Trukmė:** Priklauso nuo vaizdų skaičiaus ir CPU/GPU greičio

### 3 etapas: Kalibravimas

**Ką atlieka „Chloros“:*** **Vignette korekcija**: Pašalina objektyvo tamsėjimą kraštuose
* **Atspindžio kalibravimas**: normalizuoja naudojant tikslinės atspindžio reikšmes ir (arba) DAQ duomenis apie žemyn nukreiptą spinduliavimą
* Taiko korekcijas visose juostose/kanaluose
* Naudoja tinkamą kalibravimo etaloną kiekvienam vaizdui, remiantis laiko žyma

**Trukmė:** didžioji dalis apdorojimo laiko

### 4 etapas: Indeksų skaičiavimas

**Ką atlieka „Chloros“:**

* Apskaičiuoja sukonfigūruotus multispektrinius indeksus (NDVI, NDRE ir kt.)
* Taiko juostų skaičiavimus kalibruotiems vaizdams
* Sukuria indeksinius vaizdus kiekvienam pasirinktam indeksui

**Trukmė:** Kelios sekundės vienam vaizdui

### 5 etapas: Eksportavimas

**Ką atlieka „Chloros“:**

* Išsaugo apdorotus vaizdus pasirinktu formatu
* **„LATTICE“ išskirstymas**: kiekvienas neapdorotas „LATTICE“ kadras vienu veiksmu eksportuojamas kaip kiekvienas įjungtas produktas — be „bayeringo“, peržiūra, spinduliavimas (visada „float32“), atspindys
* Įrašo failus į projekto išvesties medį: `<project>/<camera>/<format>/<Product>_Images/`
* **Išsaugo šaltinio failo pavadinimą** — produktą identifikuoja aplankas, priesaga nepridedama**Trukmė:** Priklauso nuo eksporto formato ir failo dydžio***

## Apdorojimo veikimas

### Automatinis apdorojimo procesas

Pradėjus veikti, visas procesas vyksta automatiškai:

* Vartotojo įsikišimas nereikalingas
* Visi sukonfigūruoti žingsniai vykdomi paeiliui
* Vykdymo pažanga rodoma realiuoju laiku
* Eksportuoti failai įrašomi į diską, kai tik baigiami apdoroti — galite atidaryti baigtus rezultatus, kol procesas tęsiasi

### Kompiuterio naudojimas apdorojimo metu

**Laisvasis režimas:**

* Santykinai mažas procesoriaus (CPU) apkrovimas (vienos sriegio)
* Kompiuteris išlieka reaguojantis kitoms užduotims
* Saugu sumažinti „Chloros“ langą iki minimalaus dydžio ir dirbti kitose programose

**„Chloros+“ lygiagretusis režimas:**

* Didelis procesoriaus (CPU) apkrovimas visoje strategijos darbininkų grupėje
* Naudojant GPU pagreitinimą: didelė GPU apkrova
* Apdorojimo metu kompiuteris gali reaguoti lėčiau
* Venkite paleisti kitas daug procesoriaus resursų reikalaujančias užduotis

{% hint style="warning" %}
**Našumo patarimas**: Norėdami pasiekti geriausią „Chloros+“ našumą, uždarykite kitas programas ir leiskite „Chloros“ naudoti visus sistemos išteklius.
{% endhint %}

### Apdorojimo negalima pristabdyti (tačiau sustabdymas vyksta sklandžiai)

* Kartą pradėtas apdorojimas negali būti pristabdytas ir vėliau atnaujintas
* Paspaudus **„Stop“**, apdorojimas tvarkingai sustabdomas jau po pirmojo paspaudimo
* Prieš sustabdymą jau eksportuoti produktai lieka diske
* Sustabdytas apdorojimas tiksliai praneša, ką pavyko užbaigti (žr. žurnalo eilutes „`[RUN-SUMMARY]`“)
* Naujas apdorojimo ciklas pradeda procesą nuo pradžių

**Planavimo patarimas:** Vykdant labai didelius projektus, apsvarstykite galimybę apdoroti duomenis partijomis arba naudoti komandinės eilutės sąsają (CLI), kad galėtumėte geriau kontroliuoti procesą.***

## Apdorojimo stebėjimas

Kol vyksta apdorojimas, galite:

* **Stebėti pažangos juostą** – matyti bendrą užbaigtumo procentą
* **Peržiūrėti dabartinį etapą** – aptikimas, analizė, kalibravimas arba eksportavimas
* **Patikrinti žurnalo skirtuką** – peržiūrėti išsamius apdorojimo pranešimus ir įspėjimus
* **Peržiūrėti užbaigtus vaizdus** – eksportuojami failai atsiranda diske apdorojimo metu

Išsamią informaciją apie stebėjimą rasite skyriuje [Apdorojimo stebėjimas](monitoring-the-processing.md).

***

## Apdorojimo sustabdymas

Jei reikia sustabdyti apdorojimą:

### Kaip sustabdyti

1. Suraskite **mygtuką „Sustabdyti“** (apdorojimo metu jis pakeičia mygtuką „Pradėti“)
2. Spustelėkite jį vieną kartą — juostoje pasirodys užrašas **„Sustabdoma...“**, kol bus užbaigtas apdorojamas vaizdas
3. Apdorojimas baigsis galutinai sustabdytu būsenoje, o žurnale bus išspausdintas tikslus `[RUN-SUMMARY]` ataskaita apie tai, kas buvo užbaigta

### Kada sustabdyti

**Pagrįstos priežastys sustabdyti:**

* Paaiškėjo, kad buvo naudojami neteisingi nustatymai
* Pamiršote pažymėti tikslinius vaizdus
* Importuoti neteisingi vaizdai
* Sistema veikia per lėtai arba nereaguoja

**Po sustabdymo:**

* Prieš sustabdymą eksportuoti produktai lieka diske
* Peržiūrėkite ir ištaisykite visas problemas, prireikus pakoreguokite nustatymus
* Iš naujo paleiskite apdorojimą — apdorojimas prasideda nuo pradžių

***

## Apdorojimo trukmės įvertinimai

Faktinė apdorojimo trukmė labai skiriasi priklausomai nuo:

* Vaizdų skaičiaus
* Vaizdų skiriamosios gebos
* Įvesties formato (RAW ar JPG)
* Apdorojimo režimo (nemokamas arba „Chloros+“)
* Procesoriaus greičio ir branduolių skaičiaus
* Vaizdo plokštės (GPU) prieinamumo (tik „Chloros+“)
* Apskaičiuotinų indeksų skaičiaus
* Įjungtų eksportuojamų produktų skaičiaus (LATTICE)

### Apytikriai apskaičiuoti duomenys („Chloros+“, 12 MP vaizdai, šiuolaikinis procesorius)

| Vaizdų skaičius | Nemokamas režimas | „Chloros+“ (procesorius) | „Chloros+“ (grafikos procesorius) |
| ----------- | --------- | -------------- | -------------- |
| 50 vaizdų   | 15–20 min. | 5–8 min.        | 3–5 min.        |
| 100 vaizdų  | 30–40 min. | 10–15 min.      | 5–8 min.        |
| 200 vaizdų  | 1–1,5 val. | 20–30 min.      | 10–15 min.      |
| 500 vaizdų  | 2–3 val.   | 45–60 min.      | 20–30 min.      |
| 1000 vaizdų | 4–6 val.   | 1,5–2 val.      | 40–60 min.      |

{% hint style="info" %}
**Pirmasis paleidimas**: Pradinis apdorojimas gali užtrukti ilgiau, nes „Chloros“ kuria talpyklas ir profilius. Vėlesnis panašių duomenų rinkinių apdorojimas vyks greičiau.
{% endhint %}

***

## Dažniausiai pasitaikančios problemos paleidžiant

### Įjungimo mygtukas neaktyvus (pilkos spalvos)

**Galimos priežastys:**

* Įkelta vaizdų nėra
* Užkulisiai dar nėra visiškai paleisti
* Vis dar vyksta ankstesnis apdorojimas
* Projektas nėra visiškai įkeltas

**Sprendimai:**

1. Palaukite, kol foninė sistema visiškai įsikraus (patikrinkite pagrindinio meniu piktogramą)
2. Patikrinkite, ar vaizdai importuoti failų naršyklėje
3. Jei mygtukas tebėra neaktyvus, paleiskite „Chloros“ iš naujo
4. Patikrinkite „Debug Log“ dėl klaidų pranešimų

### Apdorojimas prasideda, bet iš karto žlunga

**Galimos priežastys:**

* Projekte nėra tinkamų vaizdų
* Sugadinti vaizdų failai
* Nepakanka vietos diske
* Nepakanka atminties (RAM)

**Sprendimai:**

1. Patikrinkite „Debug Log“ <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">, ar nėra klaidų pranešimų
2. Patikrinkite, ar yra laisvos vietos diske
3. Pabandykite apdoroti mažesnį vaizdų rinkinį
4. Patikrinkite, ar vaizdai nėra sugadinti

### Vykdymas baigiamas, bet vaizdai neįrašomi

Vykdymas, kurio metu buvo prašoma sukurti vaizdų produktus, bet nė vienas nebuvo įrašytas, laikomas **nesėkme, o ne sėkme** — Chloros apie tai aiškiai praneša:

* GUI žurnale rodomi pranešimai „`[RUN-SUMMARY]`“, nurodančios galimas priežastis — nebuvo importuota jokių vaizdų, nebuvo aptiktas tikslas arba visi prašyti produktai buvo praleisti kaip netinkami (pvz., prašant spinduliavimo/atspindžio duomenų iš kamerų, turinčių tik RGB)
* Atitinkamas CLI kodas (`chloros-cli process`) išveda `Processing finished but wrote no image products.` ir **baigia veikimą su nelygiu nuliui rezultatu**, todėl skriptai gali tai aptikti
* Tyčinis vykdymas, skirtas tik metaduomenims (visi eksporto produktai išjungti, indeksų nėra), vis tiek laikomas sėkmingu

Visą semantiką rasite [CLI žinyne](../reference/cli-reference.md#a-run-that-writes-no-images-fails).

### Projekto aplankas yra per giliai įdėtas šiame kompiuteryje

**Tik Windows, nauja funkcija versijoje 1.3.0.** Windows sistemoje išjungti ilgi keliai, todėl joks failo kelias negali būti ilgesnis nei 260 simbolių. „Chloros“ produktai yra išdėstyti maždaug 130 simbolių žemiau projekto aplanko, todėl projekto aplankas, esantis giliau nei nurodyta, negalės priimti produktų. Vietoj to, kad paleistų vykdymą ir nieko neįrašytų, Chloros dabar prieš pradėdamas apdorojimą patikrina:

* Paspaudus „Pradėti“ vykdymas nepradedamas: pažangos juosta rodo priežastį, o „Debug Log“ rodo „`[PROCESS] refused: The project folder is too deep for this computer…`“. Pranešime nurodytas projekto aplanko ilgis ir ilgiausias projekto aplankas, kuris tilptų.
* Taip pat atmetamas projekto kūrimas ar atidarymas aplanke, kuris yra per gilus net pačiam projektui. Pranešimo lange nurodoma priežastis, pateikiamas ilgiausias tinkamas projekto aplanko pavadinimas ir nurodoma, kaip įjungti ilgus kelius, o nieko nesukurta ir neatidaryta; derinimo žurnale tai užregistruojama kaip „`[PROJECT] refused: …`“. Projekto kopijavimas su ilgesniu pavadinimu taip pat gali viršyti ribą: kopija sukurta, bet neatidaryta, o tas pats pranešimas nurodo priežastį.

**Sprendimai:**

1. Laikykite projektus trumpesnėje aplankoje — pakeiskite**Darbinį katalogą** [Projekto nustatymuose](../project-settings/project-settings.md) ir vėl sukurkite projektą, arba
2. Įjunkite Windows ilgus kelius ir iš naujo paleiskite „Chloros“: vėl paleiskite Chloros diegimo programą, pažymėję parinktį **Įjungti Windows ilgus kelius** (ji pažymėta pagal numatytuosius nustatymus), arba naudokite grupės politiką: *Kompiuterio konfigūracija &gt; Administraciniai šablonai &gt; Sistema &gt; Failų sistema &gt; Įjungti „Win32“ ilgus kelius*.

### Įspėjimas „Nerasta tikslų“

**Galimos priežastys:**

* Pamiršote pažymėti tikslinius vaizdus
* Tiksliniai vaizdai neapima matomų tikslų
* Tikslo aptikimo nustatymai pernelyg griežti

**Sprendimai:**

1. Peržiūrėkite [Tikslinių vaizdų pasirinkimas](choosing-target-images.md)
2. Pažymėkite atitinkamus vaizdus stulpelyje „Tikslas“
3. Patikrinkite, ar pažymėtuose vaizduose tikslai yra matomi
4. Jei reikia, pakoreguokite tikslų aptikimo nustatymus

***

## Patarimai sėkmingam apdorojimui

### Prieš pradedant

1. **Pirmiausia išbandykite su nedideliu vaizdų rinkiniu** – apdorokite 10–20 vaizdų, kad patikrintumėte nustatymus
2. **Patikrinkite laisvą vietą diske** – užtikrinkite, kad laisvos vietos būtų 2–3 kartus daugiau nei duomenų rinkinio dydis (daugiau, jei įjungti visi „LATTICE“ produktai)
3. **Uždarykite nereikalingas programas** – atlaisvinkite sistemos išteklius
4. **Patikrinkite tikslų vaizdus** – peržiūrėkite pažymėtus tikslus, kad įsitikintumėte dėl kokybės
5. **Išsaugokite projektą** – projektas išsaugomas automatiškai, tačiau rekomenduojama išsaugoti ir rankiniu būdu

### Apdorojimo metu

1. **Venkite sistemos miego režimo** – išjunkite energijos taupymo režimus
2. **Laikykite „Chloros“ priešakyje** – arba bent jau matomą užduočių juostoje
3. **Kartais stebėkite pažangą** – tikrinkite, ar nėra įspėjimų ar klaidų
4. **Nekraukite kitų išteklius eikvojančių programų** – ypač naudojant „Chloros+“ lygiagretaus apdorojimo režimą

### „Chloros+“ GPU pagreitinimas

Jei naudojate „NVIDIA“ GPU pagreitinimą:

1. Atnaujinkite „NVIDIA“ tvarkykles iki naujausios versijos
2. Įsitikinkite, kad GPU turi 4 GB ar daugiau VRAM (7 GB ar daugiau, jei vienu metu atliekamas „Texture Aware“ debayeringas)
3. Uždarykite daug GPU išteklių reikalaujančias programas (žaidimus, vaizdo redagavimo programas)
4. Stebėkite GPU temperatūrą (užtikrinkite tinkamą aušinimą)

***

## Tolimesni veiksmai

Pradėjus apdorojimą:

1. **Stebėkite apdorojimo eigą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)
2. **Laukite, kol apdorojimas bus baigtas** – apdorojimas vyksta automatiškai
3. **Peržiūrėkite rezultatus** – žr. [Apdorojimo užbaigimas](finishing-the-processing.md)

Informaciją apie tai, ką daryti apdorojimo metu, rasite skyriuje [Apdorojimo stebėjimas](monitoring-the-processing.md).
