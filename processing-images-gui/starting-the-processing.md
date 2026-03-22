# Apdorojimo pradžia

Įkėlus vaizdus, pažymėjus kalibravimo taškus ir sukonfigūravus projekto nustatymus, galite pradėti apdorojimą. Šiame puslapyje pateikiami nurodymai, kaip paleisti Chloros apdorojimo procesą.

## Apdorojimo prieš apdorojimą patikrinimo sąrašas

Prieš spustelėdami mygtuką „Pradėti“, patikrinkite, ar viskas paruošta:

* [ ] **Failai importuoti** – Visi vaizdai rodomi failų naršyklėje
* [ ] **Tiksliniai vaizdai pažymėti** – Tikslinėje skiltyje pažymėti kalibravimo vaizdai
* [ ] **Kamerų modeliai aptikti** – Skiltyje „Kamerų modeliai“ rodomos teisingos kameros
* [ ] **Nustatymai sukonfigūruoti** – Projekto nustatymai peržiūrėti ir pakoreguoti
* [ ] **Pasirinkti indeksai** – Pridėti norimi multispektriniai indeksai (jei reikia)
* [ ] **Pasirinkta eksporto forma** – Jūsų darbo eigai tinkama išvesties forma

{% hint style="info" %}
**Patarimas**: Prieš apdorojimą peržiūrėkite keletą vaizdų failų naršyklėje, kad įsitikintumėte, jog jie įkelti teisingai.
{% endhint %}

***

## Apdorojimo pradžia

### Raskite „Pradėti“ mygtuką

„Pradėti/Paleisti“ mygtukas yra viršutinėje Chloros antraštės juostoje:

* Vieta: lango viršutinėje vidurinėje dalyje
* Piktograma: **„Paleisti/Pradėti“ mygtukas** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
* Būklė: mygtukas įjungtas (šviečia), kai pasirengta apdoroti

### Spustelėkite, kad pradėtumėte

1. Spustelėkite **„Paleisti/Pradėti“ mygtuką** viršutinėje juostoje
2. Apdorojimas prasideda iš karto
3. Apdorojimo metu mygtukas tampa neaktyvus (pilkos spalvos)
4. Atnaujinamas pažangos juostos rodiklis, rodantis apdorojimo būklę

{% hint style="success" %}
**Apdorojimas pradėtas**: Paspaudus mygtuką, Chloros automatiškai atlieka visus apdorojimo etapus – tikslo aptikimą, debayeringą, kalibravimą, indekso skaičiavimą ir eksportavimą.
{% endhint %}

***

## Apdorojimo režimų supratimas

Chloros veikia dviem skirtingais apdorojimo režimais, priklausomai nuo jūsų licencijos:

### Nemokamas režimas (sekcinis apdorojimas)

**Prieinamas visiems vartotojams**

**Kaip veikia:**

* Apdoroja vaizdus po vieną, paeiliui
* Vienos sriegio operacija
* Mažesnis atminties naudojimas

**Progreso juosta rodo 2 etapus:**

1.**Tikslo aptikimas** – kalibravimo tikslų paieška
2. **Apdorojimas** – kalibravimo taikymas ir vaizdų eksportavimas**Apdorojimo trukmė:**

* Daug lėtesnis nei Chloros+ lygiagretaus apdorojimo režimas
* Tinka mažoms ir vidutinio dydžio duomenų bazėms (&lt; 200 vaizdų)

### Chloros+ režimas (lygiagretus apdorojimas)

**Reikalinga Chloros+ licencija**

**Kaip tai veikia:**

* Apdoroja kelis vaizdus vienu metu naudodamas [4-siūlų apdorojimo vamzdyną](../processing-architecture/processing-pipeline.md)
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) automatiškai parenka optimalią strategiją jūsų aparatinei įrangai
* GPU (CUDA) pagreitinimas su NVIDIA vaizdo plokštėmis (stacionariu kompiuteriu ir „Jetson“)
* Skalė nuo „Jetson Nano“ (1 darbininkas) iki stacionaraus kompiuterio su 12 GB+ GPU (3–4 darbininkai)

**Progreso juosta rodo 4 etapus** (atitinkančius 4 konvejerio sriegius):

1. **Aptikimas** (1 sriegis) – Kalibravimo taškų paieška
2. **Analizė** (2 sriegis) – Vaizdo metaduomenų tikrinimas ir kalibravimo skaičiavimas
3. **Kalibravimas** (3 sriegis) – GPU debayeringas, vinjetės korekcija, indekso skaičiavimas
4. **Eksportavimas** (4-asis srautas) – apdorotų vaizdų ir indeksų išsaugojimas**Progreso juostos sąveika:*** **Pajudinkite pelę** virš juostos, kad pamatytumėte išsamų 4 etapų išskleidžiamąjį skydelį
* **Spustelėkite** progreso juostą, kad išskleidžiamasis skydelis liktų fiksuotas
* **Spustelėkite dar kartą**, kad atšaldytumėte ir paslėptumėte skydelį**Apdorojimo laikas:**

* Žymiai greitesnis nei nemokamas režimas
* Priklauso nuo CPU branduolių skaičiaus
* GPU pagreitis dar labiau padidina greitį

{% hint style="info" %}
**Chloros+ greitis**: Lygiagretusis apdorojimas gali būti 5–10 kartų greitesnis nei nuoseklusis režimas, kai dirbama su dideliais duomenų rinkiniais. 500 vaizdų projektas, kuris nemokamame režime trunka 2 valandas, su Chloros+ gali būti užbaigtas per 15–20 minučių.
{% endhint %}

***

## Kas vyksta apdorojimo metu

### 1 etapas: Tikslo aptikimas

**Ką daro Chloros:**

* Nuskaito pažymėtus tikslo vaizdus (arba visus vaizdus, jei nė vienas nepažymėtas)
* Nustato 4 kalibravimo plokštes kiekviename taikinyje
* Išskiria atspindžio vertes iš taikinio plokščių
* Įrašo taikinio laiko žymes kalibravimo planavimui

**Trukmė:** 1–30 sekundžių (su pažymėtais taikiniais), 5–30+ minučių (nepažymėtais)

### 2 etapas: Debayering (RAW konversija)

**Ką daro Chloros:**

* Konvertuoja RAW Bayer modelio duomenis į pilnus RGB vaizdus
* Taiko aukštos kokybės demosaicing algoritmą
* Išsaugo maksimalią vaizdo kokybę ir detalumą

**Trukmė:** Priklauso nuo vaizdų skaičiaus ir procesoriaus greičio

### 3 etapas: Kalibravimas

**Ką daro Chloros:*** **Vignette korekcija**: pašalina objektyvo tamsėjimą kraštuose
* **Atstumo kalibravimas**: normalizuoja naudojant tikslinės atspindžio vertes
* Taiko korekcijas visose juostose/kanaluose
* Naudoja tinkamą kalibravimo tikslą kiekvienam vaizdui pagal laiko žymą

**Trukmė:** Didžioji dalis apdorojimo laiko

### 4 etapas: Indekso skaičiavimas

**Ką daro Chloros:**

* Apskaičiuoja sukonfigūruotus multispektrinius indeksus (NDVI, NDRE ir kt.)
* Taiko juostų skaičiavimus kalibruotiems vaizdams
* Sukuria indeksų vaizdus kiekvienam pasirinktam indeksui

**Trukmė:** Kelios sekundės vienam vaizdui

### 5 etapas: Eksportavimas

**Ką daro Chloros:**

* Išsaugo kalibruotus vaizdus pasirinktu formatu
* Eksportuoja indeksų vaizdus su sukonfigūruotomis LUT spalvomis
* Įrašo failus į kameros modelio pakatalogius
* Išsaugo originalius failų pavadinimus su priesagomis

**Trukmė:** Priklauso nuo eksporto formato ir failo dydžio***

## Apdorojimo veikimas

### Automatinis apdorojimo procesas

Pradėjus veikti, visas procesas vyksta automatiškai:

* Vartotojo įsikišimas nereikalingas
* Visi sukonfigūruoti žingsniai vykdomi paeiliui
* Pažangos atnaujinimai rodomi realiuoju laiku

### Kompiuterio naudojimas apdorojimo metu

**Laisvasis režimas:**

* Santykinai mažas procesoriaus naudojimas (vienos sriegio)
* Kompiuteris lieka reaguojantis kitoms užduotims
* Saugu sumažinti Chloros langą ir dirbti kitose programose

**Chloros+ Lygiagretusis režimas:**

* Didelis procesoriaus naudojimas (daugiagijis, iki 16 branduolių)
* Su GPU pagreitinimu: didelis GPU naudojimas
* Kompiuteris gali reaguoti lėčiau apdorojimo metu
* Venkite paleisti kitas daug procesoriaus resursų reikalaujančias užduotis

{% hint style="warning" %}
**Našumo patarimas**: Norėdami pasiekti geriausią Chloros+ našumą, uždarykite kitas programas ir leiskite Chloros naudoti visus sistemos išteklius.
{% endhint %}

### Apdorojimo negalima sustabdyti

**Svarbūs apribojimai:**

* Pradėjus apdorojimą, jo negalima sustabdyti
* Galite atšaukti apdorojimą, bet pažanga bus prarasta
* Daliniai rezultatai nėra išsaugomi
* Atšaukus reikia pradėti iš naujo

**Planavimo patarimas:** Labai dideliems projektams apsvarstykite apdorojimą partijomis arba CLI naudojimą, kad galėtumėte geriau kontroliuoti procesą.***

## Apdorojimo stebėjimas

Kol vyksta apdorojimas, galite:

* **Stebėti pažangos juostą** – matyti bendrą užbaigtumo procentą
* **Peržiūrėti dabartinį etapą** – aptikimas, analizė, kalibravimas arba eksportavimas
* **Patikrinti žurnalo skirtuką** – peržiūrėti išsamius apdorojimo pranešimus ir įspėjimus
* **Peržiūrėti užbaigtus vaizdus** – kai kurie eksportuojami failai gali pasirodyti apdorojimo metu

Išsamią informaciją apie stebėjimą rasite skyriuje [Apdorojimo stebėjimas](monitoring-the-processing.md).

***

## Apdorojimo atšaukimas

Jei reikia sustabdyti apdorojimą:

### Kaip atšaukti

1. Suraskite **„Stop/Cancel“ mygtuką** (apdorojimo metu jis pakeičia „Start“ mygtuką)
2. Spustelėkite „Stop“ mygtuką
3. Apdorojimas sustos iš karto
4. Daliniai rezultatai bus atmesti

### Kada atšaukti

**Pagrįstos priežastys atšaukti:**

* Supratote, kad buvo naudojami neteisingi nustatymai
* Pamiršote pažymėti tikslinius vaizdus
* Importuoti neteisingi vaizdai
* Sistema veikia per lėtai arba nereaguoja

**Po atšaukimo:**

* Peržiūrėkite ir ištaisykite visas problemas
* Prireikus pakoreguokite nustatymus
* Pradėkite apdorojimą iš naujo
* Norėdami užtikrinti sklandžiausią veikimą, visiškai uždarykite Chloros ir paleiskite iš naujo

{% hint style="warning" %}
**Nėra dalinių rezultatų**: Atšaukus visos pažangos duomenys ištrinami. Chloros neišsaugo iš dalies apdorotų vaizdų.
{% endhint %}

***

## Apdorojimo trukmės įvertinimai

Faktinė apdorojimo trukmė labai skiriasi priklausomai nuo:

* Vaizdų skaičiaus
* Vaizdo skiriamosios gebos
* Įvesties formato (RAW ar JPG)
* Apdorojimo režimo (Nemokamas vs Chloros+)
* Procesoriaus greitis ir branduolių skaičius
* GPU prieinamumas (tik Chloros+)
* Apskaičiuotinų indeksų skaičius
* Eksporto formato sudėtingumas

### Apytikriai apskaičiuoti laikai (Chloros+, 12 MP vaizdai, šiuolaikinis procesorius)

| Vaizdų skaičius | Nemokamas režimas | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 vaizdų   | 15–20 min. | 5–8 min.        | 3–5 min.        |
| 100 vaizdų  | 30–40 min. | 10–15 min.      | 5–8 min.        |
| 200 vaizdų  | 1–1,5 val. | 20–30 min.      | 10–15 min.      |
| 500 vaizdų  | 2–3 val.   | 45–60 min.      | 20–30 min.      |
| 1000 vaizdų | 4–6 val.   | 1,5–2 val.      | 40–60 min.      |

{% hint style="info" %}
**Pirmasis paleidimas**: Pradinis apdorojimas gali užtrukti ilgiau, nes Chloros kuria talpyklas ir profilius. Vėlesnis panašių duomenų rinkinių apdorojimas bus greitesnis.
{% endhint %}

***

## Dažnos problemos paleidžiant

### Paleidimo mygtukas neaktyvus (pilkos spalvos)

**Galimos priežastys:**

* Įkelti vaizdai
* Užkulisiai dar nėra visiškai paleisti
* Ankstesnis apdorojimas vis dar vyksta
* Projektas nėra visiškai įkeltas

**Sprendimai:**

1. Palaukite, kol užkulisiai visiškai paleis (patikrinkite pagrindinio meniu piktogramą)
2. Patikrinkite, ar vaizdai įkelti failų naršyklėje
3. Jei mygtukas lieka neaktyvus, paleiskite Chloros iš naujo
4. Patikrinkite „Debug Log“ dėl klaidų pranešimų

### Apdorojimas prasideda, bet iš karto žlunga

**Galimos priežastys:**

* Projekte nėra tinkamų vaizdų
* Sugadinti vaizdų failai
* Nepakanka vietos diske
* Nepakanka atminties (RAM)

**Sprendimai:**

1. Patikrinkite „Debug Log“ <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> dėl klaidų pranešimų
2. Patikrinkite, ar yra laisvos vietos diske
3. Pabandykite apdoroti mažesnį vaizdų rinkinį
4. Patikrinkite, ar vaizdai nėra sugadinti

### Įspėjimas „Nerasta tikslų“

**Galimos priežastys:**

* Pamiršote pažymėti tikslinius vaizdus
* Tiksliniai vaizdai neturi matomų tikslų
* Tikslo aptikimo nustatymai pernelyg griežti

**Sprendimai:**

1. Peržiūrėkite [Tikslo vaizdų pasirinkimas](choosing-target-images.md)
2. Pažymėkite atitinkamus vaizdus stulpelyje „Tikslas“
3. Patikrinkite, ar pažymėtuose vaizduose tikslai yra matomi
4. Jei reikia, pakoreguokite tikslo aptikimo nustatymus

***

## Patarimai sėkmingam apdorojimui

### Prieš pradedant

1. **Pirmiausia išbandykite su nedideliu vaizdų rinkiniu** – apdorokite 10–20 vaizdų, kad patikrintumėte nustatymus
2. **Patikrinkite laisvą vietą diske** – užtikrinkite, kad būtų 2–3 kartus daugiau laisvos vietos nei duomenų rinkinio dydis
3. **Uždarykite nereikalingas programas** – atlaisvinkite sistemos išteklius
4. **Patikrinkite tikslinį vaizdą** – peržiūrėkite pažymėtus tikslus, kad įsitikintumėte dėl kokybės
5. **Išsaugokite projektą** – projektas išsaugomas automatiškai, tačiau rekomenduojama išsaugoti jį rankiniu būdu

### Apdorojimo metu

1. **Venkite sistemos miego režimo** – išjunkite energijos taupymo režimus
2. **Laikykite Chloros priešakyje** – arba bent jau matomą užduočių juostoje
3. **Kartais stebėkite pažangą** – tikrinkite, ar nėra įspėjimų ar klaidų
4. **Nekraukite kitų resursų reikalaujančių programų** – ypač naudojant Chloros+ lygiagretaus apdorojimo režimą

### Chloros+ GPU pagreitinimas

Jei naudojate NVIDIA GPU pagreitinimą:

1. Atnaujinkite NVIDIA tvarkykles iki naujausios versijos
2. Įsitikinkite, kad GPU turi 4 GB ar daugiau VRAM
3. Uždarykite GPU išteklius intensyviai naudojančias programas (žaidimus, vaizdo redagavimo programas)
4. Stebėkite GPU temperatūrą (užtikrinkite tinkamą aušinimą)

***

## Tolimesni veiksmai

Pradėjus apdorojimą:

1. **Stebėkite pažangą** – Žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)
2. **Palaukite, kol apdorojimas bus baigtas** – apdorojimas vyksta automatiškai
3. **Peržiūrėkite rezultatus** – žr. [Apdorojimo užbaigimas](finishing-the-processing.md)

Informaciją apie tai, ką daryti apdorojimo metu, rasite [Apdorojimo stebėjimas](monitoring-the-processing.md).
