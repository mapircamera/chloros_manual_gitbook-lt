# Tikslinės nuotraukos pasirinkimas

Nuotraukų, kuriose yra kalibravimo tikslai, žymėjimas yra labai svarbus žingsnis, kuris žymiai pagreitina Chloros apdorojimo procesą. Iš anksto pasirinkdami tikslinės nuotraukas, jūs pašalinate būtinybę Chloros nuskaityti kiekvieną nuotrauką jūsų duomenų rinkinyje, ieškodami kalibravimo tikslų.

## Kodėl reikia žymėti tikslinės nuotraukas?

### Apdorojimo greitis

Nežymint tikslinės nuotraukos, Chloros turi:

* Nuskaityti kiekvieną projekto nuotrauką
* Vykdyti tikslinės nuotraukos nustatymo algoritmus kiekvienoje nuotraukoje
* Be reikalo patikrinti šimtus ar tūkstančius nuotraukų

**Rezultatas**: apdorojimas gali užtrukti žymiai ilgiau, ypač didelių duomenų rinkinių atveju.

### Su pažymėtais tiksliniais vaizdais

Kai pažymite konkrečius vaizdus stulpelyje „Tikslas“:

* Chloros nuskaito tik pažymėtus vaizdus, ieškodamas tikslų
* Tikslo aptikimas užtrunka daug trumpiau
* Bendras apdorojimo laikas žymiai sutrumpėja

{% hint style=&quot;success&quot; %}
**Greitis pagerėja**: Pažymėjus 2–3 tikslo vaizdus 500 vaizdų duomenų rinkinyje, tikslo aptikimo laikas gali sutrumpėti nuo 30+ minučių iki mažiau nei 1 minutės.
{% endhint %}

***

## Kaip pažymėti tikslinius vaizdus

### 1 žingsnis: Nustatykite tikslinius vaizdus

Peržiūrėkite importuotus vaizdus failų naršyklėje ir nustatykite, kurie vaizdai yra kalibravimo tikslai.

**Dažni scenarijai:**

* **Prieš fotografavimą užfiksuotas tikslas**: užfiksuotas prieš pradedant sesiją.
* **Po fotografavimo užfiksuotas tikslas**: užfiksuotas po sesijos pabaigos.
* **Tikslai lauke**: tikslai, esantys fotografavimo srityje.
* **Keli tikslai**: 2–3 tiksliniai vaizdai per sesiją (rekomenduojama).

### 2 žingsnis: patikrinkite tikslo stulpelį

Kiekvienam vaizdui, kuriame yra kalibravimo tikslas:

1. Raskite vaizdą failų naršyklės lentelėje.
2. Raskite stulpelį **Tikslas** (dešiniausias stulpelis).
3. Pažymėkite žymės langelį stulpelyje „Tikslas“ šiam vaizdui.
4. Pakartokite šiuos veiksmus visiems vaizdams, kuriuose yra tikslai.

### 3 žingsnis: patikrinkite savo pasirinkimą

Prieš apdorojant, dar kartą patikrinkite:

* [ ] Visi vaizdai su kalibravimo tikslais yra pažymėti
* [ ] Nė vienas vaizdas be tikslo nėra pažymėtas netyčia
* [ ] Tikslai yra aiškiai matomi pažymėtuose vaizduose

***

## Geriausia tikslo vaizdų praktika

### Tikslo fiksavimo gairės

**Laikas:**

* Fiksuokite tikslo vaizdus prieš pat fiksavimo sesiją ir jos metu
* Tose pačiose apšvietimo sąlygose, kaip ir jūsų DAQ šviesos jutiklis
* Idealiu atveju, norint gauti geriausius rezultatus, tikslų vaizdus fiksuokite kuo dažniau. Kitaip, šviesos jutiklio duomenys bus naudojami kalibravimui reguliuoti laikui bėgant.

**Kameros padėtis:**

* Laikykite kamerą virš tikslo taip, kad jis būtų centre ir užimtų apie 40–60 % vaizdo centro.
* Laikykite kamerą lygiagrečiai/nadir su tikslo paviršiumi

**Apšvietimas:**

* Tokios pačios aplinkos apšvietimo sąlygos kaip jūsų DAQ šviesos jutiklio.
* Venkite šešėlių ant tikslo paviršių.
* Neuždenkite šviesos šaltinio savo kūnu, transporto priemone ar augmenija.
* Debesuotas oras užtikrina nuosekliausius rezultatus.

**Tikslo būklė:**

* Laikykite tikslo plokštes švarias ir sausas.
* Visos 4 plokštės turi būti aiškiai matomos ir neužstotos.
* Tikslai turi būti statūs/nadiriai šviesos šaltiniui, jei įmanoma.

### Kiek tikslo vaizdų?

**Minimalus skaičius:** 1 tikslo vaizdas per sesiją. **Rekomenduojamas skaičius:** 3–5 tikslo vaizdai per sesiją.

**Geriausia praktika:**

* 3–5 vaizdai, užfiksuoti netrukus po to, kai šviesos jutiklis pradėjo įrašyti
* Norėdami gauti geriausius rezultatus, tarp kadrų pasukite kamerą
* Pasirinktinai: periodiškai sesijos viduryje, jei apšvietimo sąlygos nuolat keičiasi

***

## Darbas su keliomis kameromis

### Dviejų kamerų konfigūracijos

Jei naudojate dvi MAPIR kameras vienu metu (pvz., Survey3W RGN + Survey3N OCN):

1. Užfiksuokite tikslo vaizdus **abiem kameromis** tuo pačiu metu.
2. Naudokite **tą patį fizinį tikslą** abiem kameroms.
3. Pažymėkite tikslo vaizdus **abiem kamerų tipams** failų naršyklėje.
4. Chloros naudos atitinkamus tikslus kiekvienos kameros kalibravimui.

### Kameros modelio stulpelis

**Kameros modelio** stulpelis padeda nustatyti, kurie vaizdai buvo užfiksuoti kuria kamera:

* Survey3W\_RGN
* Survey3N\_OCN
* Survey3W\_RGB
* ir t. t.

Naudokite šį stulpelį, kad patikrintumėte, ar pažymėjote tikslus kiekvienam kameros tipui savo projekte.

***

## Tikslo aptikimo nustatymai

### Aptikimo jautrumo reguliavimas

Jei Chloros netinkamai aptinka jūsų tikslus, reguliuokite šiuos nustatymus [Projekto nustatymai](adjusting-project-settings.md):

**Minimalus kalibravimo pavyzdžio plotas:**

* **Numatytasis**: 25 pikseliai
* **Padidinkite**, jei gaunate klaidingus aptikimus dėl mažų artefaktų
* **Sumažinkite**, jei tikslai nėra aptinkami

**Minimalus tikslų grupuojimas:**

* **Numatytasis**: 60
* **Padidinkite**, jei tikslai yra suskaidomi į kelis aptikimus
* **Sumažinkite**, jei tikslai su spalvų variacijomis nėra visiškai aptinkami

***

## Dažnos tikslo vaizdo problemos

### Problema: neaptinkami tikslai

**Galimos priežastys:**

* Tikslo vaizdai nepažymėti failų naršyklėje
* Tikslas per mažas kadre (&lt; 30 % vaizdo)
* Blogas apšvietimas (šešėliai, atspindžiai)
* Per griežti tikslo aptikimo nustatymai

**Sprendimai:**

1. Patikrinkite, ar tikslo stulpelyje pažymėti teisingi vaizdai
2. Peržiūrėkite tikslo vaizdo kokybę peržiūroje
3. Jei kokybė prasta, vėl užfiksuokite tikslus
4. Jei reikia, pakoreguokite tikslo aptikimo nustatymus

### Problema: klaidingi tikslo aptikimai

**Galimos priežastys:**

* Balti pastatai, transporto priemonės ar žemės danga klaidingai palaikomi tikslais
* Ryškūs plotai augmenijoje
* Per mažas aptikimo jautrumas

**Sprendimai:**

1. Pažymėkite tik tikrus tikslo vaizdus, kad apribotumėte aptikimo apimtį
2. Padidinkite minimalų kalibravimo mėginio plotą
3. Padidinkite minimalų tikslo klasterizavimo vertę
4. Užtikrinkite, kad tikslo vaizduose būtų rodomas tik tikslas (minimalus fono triukšmas)

***

## Patikrinimo kontrolinis sąrašas

Prieš pradėdami apdorojimą, patikrinkite savo tikslo vaizdų pasirinkimą:

* [ ] Bent 1 tikslo vaizdas pažymėtas per sesiją
* [ ] Tikslo stulpelio žymės pažymėtos visiems tikslo vaizdams
* [ ] Tikslo vaizdai užfiksuoti per tą patį laikotarpį kaip ir tyrimas
* [ ] Tikslai aiškiai matomi peržiūroje, kai spustelėjami
* [ ] Visi 4 kalibravimo skydeliai matomi kiekviename tikslo vaizde
* [ ] Ant tikslų nėra šešėlių ar kliūčių.
* [ ] Dvigubos kameros atveju: tikslai pažymėti abiejų tipų kameroms.

***

## Apdorojimas be tikslų

### Apdorojimas be kalibravimo tikslų

Nors tai nerekomenduojama moksliniams darbams, galite apdoroti be tikslų:

1. Palikite visus tikslų stulpelių žymimuosius langelius nepažymėtus.
2. **Išjunkite** „Atspindžio kalibravimą“ projekto nustatymuose.
3. Vignette korekcija vis tiek bus taikoma
4. Išvestis nebus kalibruojama pagal absoliučią atspindžio koeficiento vertę

{% hint style=&quot;warning&quot; %}
**Nerekomenduojama**: be atspindžio koeficiento kalibravimo pikselių vertės atspindi tik santykinį ryškumą, o ne mokslinius atspindžio koeficiento matavimus. Norėdami gauti tikslius, pakartojamus rezultatus, naudokite kalibravimo tikslus.
{% endhint %}

***

## Tolimesni veiksmai

Pažymėję tikslinės nuotraukas:

1. **Peržiūrėkite nustatymus** – žr. [Projekto nustatymų koregavimas](adjusting-project-settings.md)
2. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)
3. **Stebėkite pažangą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)

Daugiau informacijos apie kalibravimo tikslus rasite [Kalibravimo tikslai](../calibration-targets.md).
