# Tikslo vaizdų pasirinkimas

Tikslo vaizdų pažymėjimas yra labai svarbus žingsnis, kuris žymiai pagreitina Chloros apdorojimo procesą. Iš anksto atrinkdami tikslo vaizdus, pašalinate būtinybę Chloros programai nuskaityti kiekvieną duomenų rinkinio vaizdą ieškant kalibravimo tikslų.

## Kodėl reikia pažymėti tikslo vaizdus?

### Apdorojimo greitis

Nežymint tikslinės nuotraukos, Chloros turi:

* Nuskaityti kiekvieną projekto nuotrauką
* Vykdyti tikslų aptikimo algoritmus kiekvienoje nuotraukoje
* Be reikalo patikrinti šimtus ar tūkstančius nuotraukų

**Rezultatas**: Apdorojimas gali užtrukti žymiai ilgiau, ypač dirbant su dideliais duomenų rinkiniais.

### Su pažymėtomis tikslinėmis nuotraukomis

Kai pažymite konkrečias nuotraukas stulpelyje „Tikslas“:

* Chloros tikrina tik pažymėtus vaizdus, ieškodamas tikslų
* Tikslo aptikimas užbaigiamas daug greičiau
* Bendras apdorojimo laikas žymiai sutrumpėja

{% hint style="success" %}
**Greičio padidėjimas**: Pažymėjus 2–3 tikslinius vaizdus 500 vaizdų duomenų rinkinyje, tikslo aptikimo laikas gali sutrumpėti nuo 30 ir daugiau minučių iki mažiau nei 1 minutės.
{% endhint %}

***

## Kaip pažymėti tikslų vaizdus

### 1 žingsnis: Nustatykite savo tikslų vaizdus

Peržiūrėkite importuotus vaizdus failų naršyklėje ir nustatykite, kuriuose vaizduose yra kalibravimo tikslai.

**Dažni scenarijai:*** **Tikslas prieš fotografavimą**: užfiksuotas prieš pradedant sesiją
* **Tikslas po fotografavimo**: užfiksuotas baigus sesiją
* **Tikslai lauke**: tikslai, esantys fotografavimo zonoje
* **Keli tikslai**: 2–3 tiksliniai vaizdai per sesiją (rekomenduojama)

### 2 žingsnis: Patikrinkite stulpelį „Tikslas“

Kiekvienam vaizdui, kuriame yra kalibravimo tikslas:

1. Suraskite vaizdą failų naršyklės lentelėje
2. Suraskite stulpelį **„Tikslas“** (dešiniausias stulpelis)
3. Pažymėkite langelį stulpelyje „Tikslas“ prie to vaizdo
4. Pakartokite šiuos veiksmus su visais vaizdais, kuriuose yra tikslai

### 3 žingsnis: Patikrinkite savo pasirinkimą

Prieš apdorojimą dar kartą patikrinkite:

* [ ] Visi vaizdai su kalibravimo tikslais yra pažymėti
* [ ] Nėra atsitiktinai pažymėtų vaizdų be tikslų
* [ ] Tikslai yra aiškiai matomi pažymėtuose vaizduose

***

## Geriausia praktika, susijusi su tikslų vaizdais

### Tikslo fiksavimo gairės

**Laikas:**

* Fiksuokite tikslų vaizdus iškart prieš ir per visą fiksavimo sesiją
* Esant toms pačioms apšvietimo sąlygoms kaip ir jūsų DAQ šviesos jutiklis
* Idealiu atveju, norint gauti geriausius rezultatus, fiksuokite tikslų vaizdus kuo dažniau. Kitaip, šviesos jutiklio duomenys bus naudojami kalibravimui koreguoti laikui bėgant.

**Kameros padėtis:**

* Laikykite kamerą virš tikslo taip, kad jis būtų centruotas ir užimtų apie 40–60 % vaizdo centro.
* Laikykite kamerą lygiagrečiai / vertikaliai tikslo paviršiui

**Apšvietimas:**

* Toks pats aplinkos apšvietimas kaip ir jūsų DAQ šviesos jutiklio
* Venkite šešėlių ant tikslų paviršių
* Neužstokite šviesos šaltinio savo kūnu, transporto priemone ar augmenija
* Debesuota diena užtikrina nuosekliausius rezultatus

**Tikslo būklė:**

* Laikykite tikslų plokštes švarias ir sausas
* Visos 4 plokštės turi būti aiškiai matomos ir neužstotos
* Jei įmanoma, tikslai turi būti statmenai / tiesiai virš šviesos šaltinio

### Kiek tikslų vaizdų?

**Minimaliai:**1 tikslo vaizdas per sesiją.**Rekomenduojama:** 3–5 tikslo vaizdai per sesiją.**Geriausios praktikos tvarkaraštis:**

* 3–5 vaizdai, užfiksuoti netrukus po to, kai šviesos jutiklis pradeda įrašyti
* Norėdami gauti geriausius rezultatus, keiskite kameros padėtį tarp kadrų
* Pasirinktinai: periodiškai sesijos viduryje, jei apšvietimo sąlygos nuolat keičiasi

***

## Darbas su keliomis kameromis

### Dviejų kamerų konfigūracijos

Jei vienu metu naudojate dvi MAPIR kameras (pvz., Survey3W RGN + Survey3N OCN):

1. Fiksuokite tikslinius vaizdus **abiem kameromis** tuo pačiu metu
2. Naudokite **tą patį fizinį tikslą** abiem kameroms
3. Pažymėkite tikslinius vaizdus **abiem kamerų tipams** failų naršyklėje
4. Chloros naudos atitinkamus tikslus kiekvienos kameros kalibravimui

### Kameros modelio stulpelis

**Kameros modelio** stulpelis padeda nustatyti, kurie vaizdai buvo užfiksuoti kuria kamera:

* Survey3W\_RGN
* Survey3N\_OCN
* Survey3W\_RGB
* ir t. t.

Naudokite šį stulpelį, kad patikrintumėte, ar savo projekte pažymėjote tikslus kiekvienam kameros tipui.

***

## Tikslo aptikimo nustatymai

### Aptikimo jautrumo reguliavimas

Jei Chloros netinkamai aptinka jūsų tikslus, pakoreguokite šiuos nustatymus [Projekto nustatymuose](adjusting-project-settings.md):**Mažiausias kalibravimo mėginio plotas:*** **Numatytasis**: 25 pikseliai
* **Padidinkite**, jei gaunami klaidingi aptikimai dėl mažų artefaktų
* **Sumažinkite**, jei tikslai nėra aptinkami**Mažiausias tikslų grupavimas:*** **Numatytasis**: 60
* **Padidinkite**, jei tikslai yra suskaidomi į kelis aptikimus
* **Sumažinkite**, jei tikslai su spalvų variacijomis nėra visiškai aptikti***

## Dažnos tikslų vaizdų problemos

### Problema: Tikslai neaptikti

**Galimos priežastys:**

* Tikslai nepažymėti failų naršyklėje
* Tikslas per mažas kadre (&lt; 30 % vaizdo)
* Blogas apšvietimas (šešėliai, atspindžiai)
* Per griežti tikslų aptikimo nustatymai

**Sprendimai:**

1. Patikrinkite, ar stulpelyje „Tikslas“ pažymėti teisingi vaizdai
2. Peržiūrėkite tikslo vaizdo kokybę peržiūros lange
3. Jei kokybė prasta, pakartotinai nufotografuokite tikslus
4. Jei reikia, pakoreguokite tikslo aptikimo nustatymus

### Problema: klaidingi tikslo aptikimai

**Galimos priežastys:**

* Balti pastatai, transporto priemonės arba žemės danga klaidingai palaikomi tikslais
* Ryškūs plotai augmenijoje
* Per mažas aptikimo jautrumas

**Sprendimai:**

1. Pažymėkite tik tikruosius tikslų vaizdus, kad apribotumėte aptikimo apimtį
2. Padidinkite minimalų kalibravimo mėginio plotą
3. Padidinkite minimalų tikslų grupavimo vertę
4. Užtikrinkite, kad tikslų vaizduose būtų matomas tik tikslas (kuo mažiau foninio triukšmo)

***

## Patikrinimo kontrolinis sąrašas

Prieš pradėdami apdorojimą, patikrinkite savo pasirinktus tikslų vaizdus:

* [ ] Pažymėtas bent 1 tikslų vaizdas per sesiją
* [ ] Visiems tikslų vaizdams pažymėti tikslų stulpelio žymimieji langeliai
* [ ] Tikslų vaizdai užfiksuoti per tą patį laikotarpį kaip ir tyrimas
* [ ] Tikslai aiškiai matomi peržiūroje, kai spustelėjama
* [ ] Visi 4 kalibravimo skydeliai matomi kiekviename tikslų vaizde
* [ ] Ant tikslų nėra šešėlių ar kliūčių
* [ ] Dviejų kamerų atveju: tikslai pažymėti abiejų kamerų tipams

***

## Apdorojimas be tikslų

### Apdorojimas be kalibravimo tikslų

Nors tai nerekomenduojama moksliniams darbams, galite apdoroti be tikslų:

1. Palikite visus tikslo stulpelio žymimuosius langelius nepažymėtus
2. **Išjunkite** „Atšvaito kalibravimą“ projekto nustatymuose
3. Vignette korekcija vis tiek bus taikoma
4. Rezultatas nebus kalibruotas pagal absoliutų atšvaito koeficientą

{% hint style="warning" %}
**Nerekomenduojama**: Be atspindžio kalibravimo pikselių vertės rodo tik santykinį ryškumą, o ne mokslinius atspindžio matavimus. Norėdami gauti tikslius ir pakartojamus rezultatus, naudokite kalibravimo taškus.
{% endhint %}

***

## Tolimesni veiksmai

Pažymėję taškų vaizdus:

1. **Peržiūrėkite nustatymus** – žr. [Projekto nustatymų koregavimas](adjusting-project-settings.md)
2. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)
3. **Stebėkite pažangą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)

Daugiau informacijos apie pačius kalibravimo taikinius rasite skyriuje [Kalibravimo taikinių](../calibration-targets.md).
