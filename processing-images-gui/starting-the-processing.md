# Apdorojimo pradžia

Kai importavote vaizdus, pažymėjote kalibravimo taškus ir sukonfigūravote projekto nustatymus, galite pradėti apdorojimą. Šiame puslapyje pateikiamos instrukcijos, kaip paleisti Chloros apdorojimo procesą.

## Parengiamojo apdorojimo patikrinimo sąrašas

Prieš spustelėdami mygtuką „Pradėti“, patikrinkite, ar viskas paruošta:

* [ ] **Failai importuoti** – Visi vaizdai rodomi failų naršyklėje
* [ ] **Pažymėti tiksliniai vaizdai** – Patikrinta, ar stulpelyje „Tiksliniai“ pažymėti kalibravimo vaizdai (arba importuotas `.daq` įrašas, skirtas LATTICE)
* [ ] **Aptikti kamerų modeliai** – stulpelyje „Kameros modelis“ rodomos teisingos kameros
* [ ] **Nustatymai sukonfigūruoti** – peržiūrėti ir pakoreguoti projekto nustatymai
* [ ] **Pasirinkti indeksai** – pridėti norimi multispektriniai indeksai (jei reikia)
* [ ] **Pasirinkta eksporto forma** – jūsų darbo eigai tinkama išvesties forma

{% hint style="info" %}
**Patarimas**: Prieš pradedant apdorojimą, peržiūrėkite keletą vaizdų failų naršyklėje, kad įsitikintumėte, jog jie įkelti teisingai.
{% endhint %}

***

## Apdorojimo pradžia

### Raskite „Pradėti“ mygtuką

„Pradėti/Paleisti“ mygtukas yra viršutinėje Chloros antraštės juostoje:

* Vieta: lango viršutinėje vidurinėje dalyje
* Piktograma: **„Paleisti/Pradėti“ mygtukas** <img src="../.gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">
* Būklė: mygtukas yra aktyvus (šviečia), kai programa pasirengusi apdoroti

### Spustelėkite, kad pradėtumėte

1. Spustelėkite **„Paleisti/Pradėti“ mygtuką** viršutinėje juostoje
2. Apdorojimas prasideda iš karto
3. Apdorojimo metu mygtukas tampa **„Sustabdyti“** mygtuku
4. Progreso juosta atnaujinama, rodydama apdorojimo būseną

{% hint style="success" %}
**Apdorojimas pradėtas**: Paspaudus mygtuką, „Chloros“ automatiškai atlieka visus apdorojimo etapus – taikinio aptikimą, debayeringą, kalibravimą, indeksų skaičiavimą ir eksportavimą. Jis automatiškai nustato, ar jūsų projektas yra Survey3, LATTICE ar mišrus, ir kiekvienai kamerai taiko tinkamą apdorojimo seką.
{% endhint %}

***

## Apdorojimo režimų apžvalga

„Chloros“ veikia dviem skirtingais apdorojimo režimais, priklausomai nuo jūsų licencijos:

### Nemokamas režimas (sekcinis apdorojimas)

**Prieinamas visiems vartotojams**

**Kaip veikia:**

* Vaizdus apdoroja po vieną, nuosekliai
* Vienos sriegės veikimas
* Mažesnis atminties naudojimas

**Progreso juosta rodo 2 etapus:**

1.**Tikslo aptikimas** – kalibravimo tikslų paieška
2. **Apdorojimas** – kalibravimo taikymas ir vaizdų eksportavimas**Apdorojimo trukmė:**

* Žymiai lėtesnis nei Chloros+ lygiagretusis režimas
* Tinka mažiems ir vidutiniams duomenų rinkiniams (&lt; 200 vaizdų)

### Chloros+ režimas (lygiagretusis apdorojimas)

**Reikalinga Chloros+ licencija**

**Kaip tai veikia:**

* Vienu metu apdoroja kelis vaizdus naudodamas [4 sriegių apdorojimo grandinę](../processing-architecture/processing-pipeline.md)
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) paleidimo pradžioje automatiškai parenka optimalią strategiją jūsų aparatinei įrangai
* GPU (CUDA) pagreitinimas naudojant „NVIDIA“ vaizdo plokštes (stacionariuose kompiuteriuose ir „Jetson“)
* **Darbininkų skaičius prisitaiko prie aparatinės įrangos**: GPU strategijos paleidžia**1–4 vienu metu veikiančius darbininkus** (skaičius priklauso nuo VRAM – „Jetson“ su maža atmintimi paleidžia 1, o 12 GB ir daugiau turinti stalinio kompiuterio GPU – iki 4); tik CPU pagrįstos sistemos paleidžia po vieną darbininką kiekvienam fiziniam branduoliui, atėmus vieną**Pažangos juosta rodo 4 etapus** (atitinkančius 4 konvejerio sriegius):

1. **Aptikimas** (1-asis sriegis) – kalibravimo taškų paieška
2. **Analizė** (2-asis sriegis) – vaizdo metaduomenų tikrinimas ir kalibravimo skaičiavimas
3. **Kalibravimas** (sąlytis Nr. 3) – „debayering“, vinjetės korekcija, kalibravimas, indekso skaičiavimas
4. **Eksportavimas** (sąlytis Nr. 4) – apdorotų vaizdų ir indeksų išsaugojimas**Kaip naudotis pažangos juosta:*** **Užveskite pelės žymeklį** ant juostos, kad pamatytumėte išsamų 4 etapų išskleidžiamąjį skydelį
* **Spustelėkite** pažangos juostą, kad išskleidžiamasis skydelis liktų fiksuotas
* **Spustelėkite dar kartą**, kad atšaldytumėte ir paslėptumėte skydelį**Apdorojimo laikas:**

* Žymiai greitesnis nei nemokamasis režimas
* GPU pagreitinimas dar labiau padidina greitį

{% hint style="info" %}
**Chloros+ Greitis**: Dideliems duomenų rinkiniams lygiagretus apdorojimas gali būti 5–10 kartų greitesnis nei nuoseklusis režimas. 500 vaizdų projektas, kurio apdorojimas nemokamame režime trunka 2 valandas, naudojant „Chloros+“ gali būti užbaigtas per 15–20 minučių.
{% endhint %}

***

## Kas vyksta apdorojimo metu

### 1 etapas: Tikslo aptikimas

**Ką daro Chloros:**

* Nuskaito vaizdus, kuriuos pažymėjote stulpelyje „Tikslas“ (visus vaizdus, jei nė vienas nėra pažymėtas)
* Nustato kalibravimo plokštes kiekviename objekte
* Išskiria atspindžio vertes iš objekto plokščių
* Įrašo objektų laiko žymes kalibravimo planavimui

**Trukmė:** 1–30 sekundžių (su pažymėtais objektais), 5–30+ minučių (be pažymėjimų)

### 2 etapas: „Debayering“ (RAW konversija)

**Ką atlieka Chloros:**

* Konvertuoja RAW „Bayer“ modelio duomenis į pilnus 3 kanalų vaizdus (LATTICE mono moduliai lieka vienkanaliniai — jiems „debayering“ praleidžiamas, o žurnale paliekama atitinkama pastaba)
* Taiko pasirinktą demosaicingo algoritmą
* Išsaugo maksimalią vaizdo kokybę ir detalumą

**Trukmė:** Priklauso nuo vaizdų skaičiaus ir CPU/GPU greičio

### 3 etapas: Kalibravimas

**Ką atlieka Chloros:*** **Vignette korekcija**: Pašalina objektyvo tamsėjimą kraštuose
* **Atspindžio kalibravimas**: normalizuoja naudojant tikslinės atspindžio reikšmes ir (arba) DAQ duomenis apie žemyn nukreiptą spinduliavimą
* Taiko korekcijas visose juostose/kanaluose
* Naudoja kiekvienam vaizdui tinkamą kalibravimo etaloną, remdamasis laiko žyma

**Trukmė:** didžioji dalis apdorojimo laiko

### 4 etapas: Indeksų skaičiavimas

**Ką atlieka Chloros:**

* Apskaičiuoja sukonfigūruotus multispektrinius indeksus (NDVI, NDRE ir kt.)
* Taiko juostų matematinius skaičiavimus kalibruotiems vaizdams
* Sukuria indeksinius vaizdus kiekvienam pasirinktam indeksui

**Trukmė:** Kelios sekundės vienam vaizdui

### 5 etapas: Eksportavimas

**Ką atlieka Chloros:**

* Išsaugo apdorotus vaizdus pasirinktu formatu
* **LATTICE išskirstymas**: kiekvienas neapdorotas „LATTICE“ kadras vienu veiksmu eksportuojamas kaip kiekvienas įjungtas produktas — be „bayeringo“, peržiūra, spinduliavimas (visada „float32“), atspindys
* Įrašo failus į projekto išvesties medį: `<project>/<camera>/<format>/<Product>_Images/`
* **Išsaugo šaltinio failo pavadinimą** — produktą identifikuoja aplankas, priesaga nepridedama**Trukmė:** priklauso nuo eksporto formato ir failo dydžio***

## Apdorojimo veikimas

### Automatinis apdorojimo procesas

Pradėjus veikti, visas procesas vyksta automatiškai:

* Vartotojo įsikišimas nereikalingas
* Visi sukonfigūruoti etapai vykdomi paeiliui
* Vykdymo pažanga rodoma realiuoju laiku
* Eksportuoti failai įrašomi į diską, kai tik baigiami — galite atidaryti baigtus rezultatus, kol apdorojimas tęsiasi

### Kompiuterio išteklių naudojimas apdorojimo metu

**Laisvasis režimas:**

* Santykinai mažas procesoriaus (CPU) apkrovimas (vienos sriegio)
* Kompiuteris lieka reaguojantis kitoms užduotims
* Saugu sumažinti „Chloros“ langą ir dirbti kitose programose

**Chloros+ lygiagretusis režimas:**

* Didelis procesoriaus (CPU) apkrovimas visoje strategijos darbininkų grupėje
* Naudojant GPU pagreitinimą: didelė GPU apkrova
* Apdorojimo metu kompiuteris gali reaguoti lėčiau
* Venkite paleisti kitas užduotis, intensyviai apkraunančias procesorių

{% hint style="warning" %}
**Našumo patarimas**: Norėdami pasiekti geriausią Chloros+ našumą, uždarykite kitas programas ir leiskite Chloros naudoti visus sistemos išteklius.
{% endhint %}

### Apdorojimo negalima pristabdyti (tačiau jį galima saugiai sustabdyti)

* Kartą pradėtas apdorojimas negali būti pristabdytas ir vėliau atnaujintas
* Paspaudus **„Stop“**, vykdymas bus tvarkingai sustabdytas jau po pirmojo paspaudimo
* Prieš sustabdymą jau eksportuoti produktai išlieka diske
* Sustabdytas vykdymas tiksliai praneša, ką pavyko užbaigti (žr. `[RUN-SUMMARY]` eilutes žurnale)
* Naujas apdorojimo ciklas paleidžia apdorojimo grandinę nuo pradžių

**Planavimo patarimas:** Labai didelių projektų atveju apsvarstykite galimybę apdoroti duomenis partijomis arba naudoti CLI, kad galėtumėte geriau kontroliuoti procesą.***

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

1. Suraskite **„Stop“ mygtuką“** (apdorojimo metu jis pakeičia mygtuką „Pradėti“)
2. Spustelėkite jį vieną kartą — juostoje bus rodomas užrašas **„Sustabdoma...“**, kol bus užbaigtas apdorojamas vaizdas
3. Apdorojimas baigsis galutinai sustabdytu būsenos, o žurnale bus išspausdintas tikslus `[RUN-SUMMARY]` ataskaita apie tai, kas buvo užbaigta

### Kada sustabdyti

**Pagrįstos priežastys sustabdyti:**

* Paaiškėjo, kad buvo naudojami neteisingi nustatymai
* Pamiršote pažymėti tikslinį vaizdą
* Importuoti neteisingi vaizdai
* Sistema veikia pernelyg lėtai arba nereaguoja

**Po sustabdymo:**

* Prieš sustabdymą eksportuoti produktai lieka diske
* Peržiūrėkite ir ištaisykite visas problemas, prireikus pakoreguokite nustatymus
* Iš naujo paleiskite apdorojimą — apdorojimo ciklas prasideda nuo pradžių

***

## Apdorojimo trukmės įvertinimai

Faktinė apdorojimo trukmė labai skiriasi priklausomai nuo:

* Vaizdų skaičiaus
* Vaizdų skiriamosios gebos
* Įvesties formato (RAW ar JPG)
* Apdorojimo režimo (Free ar Chloros+)
* Procesoriaus greičio ir branduolių skaičiaus
* Vaizdo plokštės (GPU) prieinamumo (tik Chloros+)
* Apskaičiuotinų indeksų skaičių
* Įjungtų eksporto produktų skaičių (LATTICE)

### Apytikriai apskaičiuota trukmė (Chloros+, 12 MP vaizdai, šiuolaikinis procesorius)

| Vaizdų skaičius | „Free“ režimas | Chloros+ (procesorius) | Chloros+ (grafikos procesorius) |
| ----------- | --------- | -------------- | -------------- |
| 50 vaizdų   | 15–20 min | 5–8 min        | 3–5 min        |
| 100 nuotraukų  | 30–40 min | 10–15 min      | 5–8 min        |
| 200 vaizdų  | 1–1,5 val. | 20–30 min.      | 10–15 min.      |
| 500 vaizdų  | 2–3 val.   | 45–60 min.      | 20–30 min.      |
| 1000 vaizdų | 4–6 val.   | 1,5–2 val.      | 40–60 min.      |

{% hint style="info" %}
**Pirmasis paleidimas**: Pirminis apdorojimas gali užtrukti ilgiau, nes Chloros kuria talpyklas ir profilius. Vėlesnis panašių duomenų rinkinių apdorojimas bus greitesnis.
{% endhint %}

***

## Dažnos problemos paleidimo metu

### Įjungimo mygtukas neaktyvus (pilkos spalvos)

**Galimos priežastys:**

* Nėra importuotų vaizdų
* Fono sistema dar nėra visiškai paleista
* Vis dar vyksta ankstesnis apdorojimas
* Projektas nėra visiškai įkeltas

**Sprendimai:**

1. Palaukite, kol fono sistema visiškai įsikraus (patikrinkite pagrindinio meniu piktogramą)
2. Patikrinkite, ar vaizdai importuoti failų naršyklėje
3. Jei mygtukas tebėra išjungtas, paleiskite „Chloros“ iš naujo
4. Patikrinkite „Debug Log“ dėl klaidų pranešimų

### Apdorojimas prasideda, bet iš karto nutrūksta

**Galimos priežastys:**

* Projekte nėra tinkamų vaizdų
* Sugadinti vaizdų failai
* Nepakanka vietos diske
* Nepakanka atminties (RAM)

**Sprendimai:**

1. Patikrinkite, ar „Debug Log“ (<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">) nėra klaidų pranešimų
2. Patikrinkite, ar yra laisvos vietos diske
3. Pabandykite apdoroti mažesnį vaizdų rinkinį
4. Patikrinkite, ar vaizdai nėra sugadinti

### Vykdymas baigtas, bet vaizdai neįrašyti

Vykdymas, kurio metu buvo prašoma vaizdų produktų, bet nė vienas nebuvo įrašytas, laikomas **nesėkme, o ne sėkme** — Chloros apie tai aiškiai praneša:

* GUI žurnale išspausdinamas pranešimas `[RUN-SUMMARY]`, nurodantis galimą priežastį — nebuvo importuota jokių vaizdų, neaptiktas tikslas arba visi užsakyti produktai buvo praleisti kaip netaikytini (pvz., prašoma spinduliavimo/atspindžio duomenų iš kamerų, kurios palaiko tik RGB)
* CLI ekvivalentas (`chloros-cli process`) išveda `Processing finished but wrote no image products.` ir **baigia veikimą su nelygaus nulio rezultatu**, todėl skriptai gali jį aptikti
* Tyčinis vykdymas tik su metaduomenimis (visi eksporto produktai išjungti, be indeksų) vis tiek laikomas sėkmingu

Visą semantiką rasite [CLI nuorodoje](../reference/cli-reference.md#a-run-that-writes-no-images-fails).

### Įspėjimas „Nerasta tikslų“

**Galimos priežastys:**

* Pamiršta pažymėti vaizdus su tikslais
* Vaizduose su tikslais nėra matomų tikslų
* Tikslo aptikimo nustatymai pernelyg griežti

**Sprendimai:**

1. Peržiūrėkite [Tikslo vaizdų pasirinkimas](choosing-target-images.md)
2. Pažymėkite atitinkamus vaizdus stulpelyje „Tikslas“
3. Patikrinkite, ar pažymėtuose vaizduose tikslai yra matomi
4. Jei reikia, pakoreguokite tikslų aptikimo nustatymus

***

## Patarimai sėkmingam apdorojimui

### Prieš pradedant

1. **Pirmiausia išbandykite su nedideliu vaizdų rinkiniu** – apdorokite 10–20 vaizdų, kad patikrintumėte nustatymus
2. **Patikrinkite laisvą vietą diske** – užtikrinkite, kad būtų 2–3 kartus daugiau laisvos vietos nei duomenų rinkinio dydis (daugiau, jei įjungti visi „LATTICE“ produktai)
3. **Uždarykite nereikalingas programas** – atlaisvinkite sistemos išteklius
4. **Patikrinkite tikslų vaizdus** – peržiūrėkite pažymėtus tikslus, kad įsitikintumėte dėl kokybės
5. **Išsaugokite projektą** – projektas išsaugomas automatiškai, tačiau rekomenduojama išsaugoti jį rankiniu būdu

### Apdorojimo metu

1. **Venkite sistemos miego režimo** – išjunkite energijos taupymo režimus
2. **Laikykite „Chloros“ priešakyje** – arba bent jau matomą užduočių juostoje
3. **Kartais stebėkite pažangą** – tikrinkite, ar nėra įspėjimų ar klaidų
4. **Nekraukite kitų išteklių reikalaujančių programų** – ypač dirbant su „Chloros+“ lygiagretaus apdorojimo režimu

### „Chloros+“ GPU pagreitinimas

Jei naudojate „NVIDIA“ GPU pagreitinimą:

1. Atnaujinkite „NVIDIA“ tvarkykles iki naujausios versijos
2. Įsitikinkite, kad GPU turi 4 GB ar daugiau VRAM (7 GB ar daugiau, jei vienu metu atliekamas „Texture Aware“ debayeringas)
3. Uždarykite daug išteklių reikalaujančias programas (žaidimus, vaizdo redagavimo programas)
4. Stebėkite GPU temperatūrą (užtikrinkite tinkamą aušinimą)

***

## Tolimesni veiksmai

Pradėjus apdorojimą:

1. **Stebėkite apdorojimo eigą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)
2. **Laukite, kol apdorojimas bus baigtas** – apdorojimas vyksta automatiškai
3. **Peržiūrėkite rezultatus** – žr. [Apdorojimo užbaigimas](finishing-the-processing.md)

Informaciją apie tai, ką daryti apdorojimo metu, rasite skyriuje [Apdorojimo stebėjimas](monitoring-the-processing.md).
