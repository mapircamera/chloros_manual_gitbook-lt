# Apdorojimo pradžia

Kai importavote vaizdus, pažymėjote kalibravimo tikslus ir konfigūravote projekto nustatymus, galite pradėti apdorojimą. Šiame puslapyje pateikiami nurodymai, kaip pradėti Chloros apdorojimo procesą.

## Apdorojimo prieš apdorojimą kontrolinis sąrašas

Prieš spustelėdami mygtuką „Pradėti“, patikrinkite, ar viskas paruošta:

* [ ] **Importuoti failai** – visi vaizdai rodomi failų naršyklėje
* [ ] **Pažymėti tiksliniai vaizdai** – tikslinėje skiltyje pažymėti kalibravimo vaizdai
* [ ] **Aptikti fotoaparatų modeliai** – stulpelyje „Fotoaparato modelis“ rodomi teisingi fotoaparatai
* [ ] **Konfigūruoti nustatymai** – peržiūrėti ir pakoreguoti projekto nustatymai
* [ ] **Pasirinkti indeksai** – pridėti norimi multispektriniai indeksai (jei reikia)
* [ ] **Pasirinktas eksporto formatas** – jūsų darbo eigai tinkamas išvesties formatas

{% hint style=&quot;info&quot; %}
**Patarimas**: Prieš apdorojimą spustelėkite keletą vaizdų failų naršyklėje, kad patikrintumėte, ar jie įkelti teisingai.
{% endhint %}

***

## Apdorojimo pradžia

### Raskite paleidimo mygtuką

Paleidimo/grojimo mygtukas yra viršutinėje Chloros juostoje:

* Vieta: lango viršutinė vidurinė dalis
* Piktograma: **Paleidimo/grojimo mygtukas** <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">
* Būklė: mygtukas įjungtas (šviesus), kai paruoštas apdorojimui

### Spustelėkite, kad pradėtumėte

1. Spustelėkite **Paleisti/Pradėti mygtuką** viršutinėje juostoje
2. Apdorojimas prasideda iš karto
3. Apdorojimo metu mygtukas tampa išjungtas (pilkos spalvos)
4. Atnaujinama pažangos juosta, rodanti apdorojimo būklę

{% hint style=&quot;success&quot; %}
**Apdorojimas pradėtas**: Paspaudus, Chloros automatiškai atlieka visus apdorojimo veiksmus – tikslo aptikimą, debayeringą, kalibravimą, indekso skaičiavimą ir eksportavimą.
{% endhint %}

***

## Apdorojimo režimų supratimas

Chloros veikia dviem skirtingais apdorojimo režimais, priklausomai nuo jūsų licencijos:

### Nemokamas režimas (eilinis apdorojimas)

**Prieinamas visiems vartotojams**

**Kaip veikia:**

* Apdoroja vaizdus po vieną, nuosekliai
* Vienos sriegio veikimas
* Mažesnis atminties naudojimas

**Pažangos juosta rodo 2 etapus:**

1. **Tikslo aptikimas** – kalibravimo tikslų nuskaitymas
2. **Apdorojimas** – kalibravimo taikymas ir vaizdų eksportavimas

**Apdorojimo laikas:**

* Daug lėtesnis nei Chloros+ lygiagretus režimas
* Tinka mažoms ir vidutinio dydžio duomenų rinkiniams (&lt; 200 vaizdų)

### Chloros+ režimas (lygiagretus apdorojimas)

**Reikalinga Chloros+ licencija**

**Kaip veikia:**

* Vienu metu apdoroja kelis vaizdus
* Daugiasiūlis veikimas (iki 16 lygiagrečių procesų)
* Naudoja kelis CPU branduolius
* Pasirenkamas GPU (CUDA) pagreitinimas su NVIDIA grafikos plokštėmis

**Pažangos juosta rodo 4 etapus:**

1. **Aptikimas** – kalibravimo tikslų paieška
2. **Analizė** – vaizdo metaduomenų tikrinimas ir duomenų srauto paruošimas
3. **Kalibravimas** – koregavimų ir kalibravimų taikymas
4. **Eksportavimas** – apdorotų vaizdų ir indeksų išsaugojimas

**Progreso juostos sąveika:**

* **Pereikite pelės žymekliu** per juostą, kad pamatytumėte išsamų 4 etapų išskleidžiamąjį skydelį
* **Spustelėkite** progreso juostą, kad išskleidžiamasis skydelis liktų toje vietoje
* **Spustelėkite dar kartą**, kad skydelis būtų atšaldytas ir paslėptas

**Apdorojimo laikas:**

* Žymiai greitesnis nei nemokamas režimas
* Prisitaiko prie CPU branduolių skaičiaus
* GPU pagreitinimas dar labiau padidina greitį

{% hint style=&quot;info&quot; %}
**Chloros+ Greitis**: Lygiagretus apdorojimas gali būti 5–10 kartų greitesnis nei nuoseklusis režimas dideliems duomenų rinkiniams. 500 vaizdų projektas, kuris nemokamu režimu trunka 2 valandas, su Chloros+ gali būti užbaigtas per 15–20 minučių.
{% endhint %}

***

## Kas vyksta apdorojimo metu

### 1 etapas: Tikslo aptikimas

**Ką daro Chloros:**

* Nuskaito pažymėtus tikslo vaizdus (arba visus vaizdus, jei nėra pažymėtų)
* Identifikuoja 4 kalibravimo skydelius kiekviename tiksle
* Išskiria atspindžio vertes iš tikslo skydelių
* Įrašo tikslo laiko žymes kalibravimo planavimui

**Trukmė:** 1–30 sekundžių (su pažymėtais tikslais), 5–30+ minučių (nepažymėtais)

### 2 etapas: Debayering (RAW konversija)

**Ką daro Chloros:**

* Konvertuoja RAW Bayer modelio duomenis į pilnus RGB vaizdus
* Taiko aukštos kokybės demosaicing algoritmą
* Išsaugo maksimalią vaizdo kokybę ir detales

**Trukmė:** priklauso nuo vaizdų skaičiaus ir CPU greičio

### 3 etapas: Kalibravimas

**Ką daro Chloros:**

* **Vignette korekcija**: pašalina objektyvo patamsėjimą kraštuose
* **Atspindžio kalibravimas**: normalizuoja naudojant tikslinio atspindžio vertes
* Taiko korekcijas visose juostose/kanaluose
* Naudoja tinkamą kalibravimo tikslą kiekvienam vaizdui pagal laiko žymą

**Trukmė:** didžioji dalis apdorojimo laiko

### 4 etapas: indekso skaičiavimas

**Ką daro Chloros:**

* Apskaičiuoja sukonfigūruotus multispektrinius indeksus (NDVI, NDRE ir kt.)
* Taiko juostų matematiką kalibruotiems vaizdams
* Sukuria indeksų vaizdus kiekvienam pasirinktam indeksui

**Trukmė:** Kelios sekundės vienam vaizdui

### 5 etapas: Eksportavimas

**Ką daro Chloros:**

* Išsaugo kalibruotus vaizdus pasirinktu formatu
* Eksportuoja indeksų vaizdus su konfigūruotomis LUT spalvomis
* Rašo failus į kameros modelio pakatalogius
* Išsaugo originalius failų pavadinimus su priesagomis

**Trukmė:** priklauso nuo eksporto formato ir failo dydžio

***

## Apdorojimo elgsena

### Automatinis apdorojimo procesas

Pradėjus, visas procesas vyksta automatiškai:

* Nereikia jokio vartotojo įsikišimo
* Visi sukonfigūruoti veiksmai vykdomi paeiliui
* Pažanga atnaujinama realiuoju laiku

### Kompiuterio naudojimas apdorojimo metu

**Laisvasis režimas:**

* Santykinai mažas procesoriaus naudojimas (vienos grandinės)
* Kompiuteris lieka reaguojantis kitoms užduotims
* Saugu sumažinti Chloros ir dirbti kitose programose

**Chloros+ Lygiagretus režimas:**

* Didelis CPU naudojimas (daugiagijis, iki 16 branduolių)
* Su GPU pagreičiu: didelis GPU naudojimas
* Kompiuteris apdorojimo metu gali būti mažiau reaguojantis
* Venkite pradėti kitas CPU intensyvias užduotis

{% hint style=&quot;warning&quot; %}
**Našumo patarimas**: Norėdami pasiekti geriausią Chloros+ našumą, uždarykite kitas programas ir leiskite Chloros naudoti visus sistemos išteklius.
{% endhint %}

### Apdorojimo negalima sustabdyti

**Svarbūs apribojimai:**

* Pradėjus apdorojimą, jo negalima sustabdyti
* Apdorojimą galima atšaukti, bet pažanga bus prarasta
* Daliniai rezultatai nėra išsaugomi
* Atšaukus apdorojimą, reikia pradėti iš naujo

**Planavimo patarimas:** labai dideliems projektams apsvarstykite apdorojimą partijomis arba CLI naudojimą, kad galėtumėte geriau kontroliuoti.

***

## Apdorojimo stebėjimas

Kol vyksta apdorojimas, galite:

* **Stebėti pažangos juostą** – matyti bendrą užbaigtumo procentą
* **Peržiūrėti dabartinį etapą** – aptikti, analizuoti, kalibruoti arba eksportuoti
* **Patikrinti žurnalo skirtuką** – peržiūrėti išsamius apdorojimo pranešimus ir įspėjimus
* **Peržiūrėti užbaigtus vaizdus** – kai kurie eksportuojami failai gali pasirodyti apdorojimo metu

Išsami informacija apie stebėjimą pateikta skyriuje [Apdorojimo stebėjimas](monitoring-the-processing.md).

***

## Apdorojimo atšaukimas

Jei reikia sustabdyti apdorojimą:

### Kaip atšaukti

1. Raskite **Sustabdyti/Atšaukti mygtuką** (apdorojimo metu pakeičia Pradėti mygtuką)
2. Spustelėkite Sustabdyti mygtuką
3. Apdorojimas bus nedelsiant sustabdytas
4. Daliniai rezultatai bus atmesti

### Kada atšaukti

**Pagrįstos priežastys atšaukti:**

* Supratote, kad buvo naudoti neteisingi nustatymai
* Pamiršote pažymėti tikslinį vaizdą
* Importuoti neteisingi vaizdai
* Sistema veikia per lėtai arba nereaguoja

**Atšaukus:**

* Peržiūrėkite ir ištaisykite visas problemas
* Pritaikykite nustatymus pagal poreikį
* Pradėkite apdorojimą iš naujo
* Norėdami užtikrinti sklandžiausią veikimą, visiškai uždarykite Chloros ir paleiskite iš naujo

{% hint style=&quot;warning&quot; %}
**Nėra dalinių rezultatų**: Atšaukus visos pažangos duomenys bus ištrinti. Chloros neišsaugo iš dalies apdorotų vaizdų.
{% endhint %}

***

## Apdorojimo laiko įvertinimai

Faktinis apdorojimo laikas labai skiriasi priklausomai nuo:

* Vaizdų skaičiaus
* Vaizdų skiriamosios gebos
* RAW ir JPG įvesties formato
* Apdorojimo režimo (nemokamas ir Chloros+)
* CPU greičio ir branduolių skaičiaus
* GPU prieinamumo (tik Chloros+)
* Apskaičiuotinų indeksų skaičiaus
* Eksporto formato sudėtingumo

### Apytikriai apskaičiuotas laikas (Chloros+, 12 MP vaizdai, modernus procesorius)

| Vaizdų skaičius | Nemokamas režimas | Chloros+ (procesorius) | Chloros+ (grafikos procesorius) |
| ----------- | --------- | -------------- | -------------- |
| 50 vaizdų   | 15–20 min. | 5–8 min.        | 3–5 min.        |
| 100 vaizdų  | 30–40 min. | 10–15 min.      | 5–8 min.        |
| 200 vaizdų  | 1–1,5 val. | 20–30 min.      | 10–15 min.      |
| 500 nuotraukų  | 2–3 val.   | 45–60 min.      | 20–30 min.      |
| 1000 nuotraukų | 4–6 val.   | 1,5–2 val.      | 40–60 min.      |

{% hint style=&quot;info&quot; %}
**Pirmasis paleidimas**: Pirminis apdorojimas gali užtrukti ilgiau, nes Chloros kuria talpyklas ir profilius. Vėlesnis panašių duomenų rinkinių apdorojimas bus greitesnis.
{% endhint %}

***

## Dažnos problemos paleidžiant

### Paleidimo mygtukas neveikia (pilkos spalvos)

**Galimos priežastys:**

* Neimportuoti vaizdai
* Backend nepradėjo veikti
* Ankstesnis apdorojimas vis dar vyksta
* Projektas nepilnai įkeltas

**Sprendimai:**

1. Palaukite, kol užpakalinė dalis bus visiškai inicializuota (patikrinkite pagrindinio meniu piktogramą)
2. Patikrinkite, ar vaizdai importuoti failų naršyklėje
3. Jei mygtukas lieka išjungtas, paleiskite Chloros iš naujo
4. Patikrinkite, ar Debug Log nėra klaidų pranešimų

### Apdorojimas prasideda, bet iš karto žlunga

**Galimos priežastys:**

* Projekte nėra tinkamų vaizdų
* Sugadinti vaizdų failai
* Nepakankama disko vieta
* Nepakankama atmintis (RAM)

**Sprendimai:**

1. Patikrinkite Debug Log <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> , ar nėra klaidų pranešimų
2. Patikrinkite, ar yra pakankamai vietos diske
3. Pabandykite apdoroti mažesnį vaizdų rinkinį
4. Patikrinkite, ar vaizdai nėra sugadinti

### Įspėjimas „Nerasta tikslų“

**Galimos priežastys:**

* Pamiršote pažymėti tikslinius vaizdus
* Tiksliniai vaizdai neturi matomų tikslų
* Tikslo aptikimo nustatymai yra pernelyg griežti

**Sprendimai:**

1. Peržiūrėkite [Tikslinės nuotraukos pasirinkimas](choosing-target-images.md)
2. Pažymėkite tinkamas nuotraukas stulpelyje „Tikslas“
3. Patikrinkite, ar tikslai matomi pažymėtose nuotraukose
4. Prireikus pakoreguokite tikslo aptikimo nustatymus

***

## Patarimai sėkmingam apdorojimui

### Prieš pradedant

1. **Pirmiausia išbandykite su nedideliu duomenų rinkiniu** – apdorokite 10–20 vaizdus, kad patikrintumėte nustatymus.
2. **Patikrinkite laisvą disko vietą** – užtikrinkite, kad būtų 2–3 kartus daugiau laisvos vietos nei duomenų rinkinio dydis.
3. **Uždarykite nereikalingas programas** – atlaisvinkite sistemos išteklius.
4. **Patikrinkite tikslinius vaizdus** – peržiūrėkite pažymėtus tikslinius vaizdus, kad užtikrintumėte kokybę.
5. **Išsaugokite projektą** – projektas išsaugomas automatiškai, bet geriausia išsaugoti rankiniu būdu.

### Apdorojimo metu

1. **Venkite sistemos miego režimo** – išjunkite energijos taupymo režimus.
2. **Laikykite Chloros pirmame plane** – arba bent jau matomą užduočių juostoje.
3. **Kartais stebėkite pažangą** – tikrinkite, ar nėra įspėjimų ar klaidų.
4. **Nekraukite kitų sunkių programų** – ypač Chloros+ lygiagretaus režimo atveju

### Chloros+ GPU pagreitinimas

Jei naudojate NVIDIA GPU pagreitinimą:

1. Atnaujinkite NVIDIA tvarkykles iki naujausios versijos
2. Įsitikinkite, kad GPU turi 4 GB+ VRAM
3. Uždarykite GPU intensyvias programas (žaidimus, vaizdo redagavimą)
4. Stebėkite GPU temperatūrą (užtikrinkite tinkamą aušinimą)

***

## Tolimesni veiksmai

Pradėjus apdorojimą:

1. **Stebėkite pažangą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)
2. **Palaukite, kol apdorojimas bus baigtas** – apdorojimas vyksta automatiškai.
3. **Peržiūrėkite rezultatus** – žr. [Apdorojimo užbaigimas](finishing-the-processing.md).

Informaciją apie tai, ką daryti apdorojimo metu, rasite [Apdorojimo stebėjimas](monitoring-the-processing.md).
