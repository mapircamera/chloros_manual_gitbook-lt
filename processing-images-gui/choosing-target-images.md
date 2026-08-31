# Tikslinės nuotraukos pasirinkimas

Pažymėjus, kuriose nuotraukose yra kalibravimo taškai, programa „Chloros“ tiksliai žinos, kur jų ieškoti. Jei stulpelyje „Target“ pažymėta bent viena nuotrauka, programa „Chloros“ nuskaito **tik pažymėtas nuotraukas** — taigi, pažymėdami taškus, ne tik pagreitinate apdorojimą, bet ir užtikrinate, kad tyrimo nuotraukos nebūtų klaidingai palaikytos taškais.

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

## Kodėl reikia pažymėti tikslinius vaizdus?

### Pažymėjimas kontroliuoja nuskaitymą

Kai stulpelyje „Tikslas“ pažymite konkrečius vaizdus:

* „Chloros“ nuskaito tik pažymėtus vaizdus, ieškodama tikslų
* Tikslo aptikimas užbaigiamas daug greičiau
* Tyrimo vaizdai negali sukelti klaidingų tikslų aptikimų

Jei **nėra** pažymėta nė viena nuotrauka, „Chloros“ grįžta prie visų projekto nuotraukų nuskaitymo:

* Tikslo aptikimo algoritmai taikomi kiekvienai nuotraukai
* Be reikalo tikrinamos šimtai ar tūkstančiai nuotraukų
* Apdorojimas trunka žymiai ilgiau, ypač dirbant su dideliais duomenų rinkiniais

{% hint style="success" %}
**Greičio padidinimas**: Pažymėjus 2–3 vaizdus su tikslais 500 vaizdų duomenų rinkinyje, tikslų aptikimo laikas gali sutrumpėti nuo 30 ir daugiau minučių iki mažiau nei 1 minutės.
{% endhint %}

***

## Kaip pažymėti tikslų vaizdus

### 1 žingsnis: Nustatykite tikslų vaizdus

Peržiūrėkite importuotus vaizdus failų naršyklėje ir nustatykite, kuriuose vaizduose yra kalibravimo tikslai.

**Dažni atvejai:*** **Tikslas prieš fotografavimą**: užfiksuotas prieš pradedant sesiją
* **Tikslas po fotografavimo**: užfiksuotas baigus sesiją
* **Tikslai lauke**: tikslai, esantys fotografavimo zonoje
* **Keli tikslai**: 2–3 tikslų vaizdai per sesiją (rekomenduojama)

### 2 žingsnis: Patikrinkite stulpelį „Target“ („Tikslas“) <img src="../.gitbook/assets/image (33).png" alt="" data-size="original">

Kiekvienam vaizdui, kuriame yra kalibravimo tikslas:

1. Suraskite vaizdą „File Browser“ lentelėje
2. Suraskite stulpelį **„Target“** („Tikslas“) (dešiniausias stulpelis)
3. Pažymėkite žymės langelį stulpelyje „Target“ („Tikslas“) prie to vaizdo
4. Pakartokite šiuos veiksmus su visais vaizdais, kuriuose yra taikiniai

### 3 žingsnis: Patikrinkite savo pasirinkimą

Prieš apdorojimą dar kartą patikrinkite:

* [ ] Visi vaizdai su kalibravimo taikiniais yra pažymėti
* [ ] Nė vienas vaizdas, kuriame nėra taikinio, nėra netyčia pažymėtas
* [ ] Pažymėtuose vaizduose taikiniai yra aiškiai matomi

***

## LATTICE: Kalibravimo taškai yra neprivalomi, kai DAQ atlieka įrašymą

LATTICE multispektrinių kamerų atveju kadre esantis kalibravimo taškas yra **vienas iš dviejų** galimų atspindžio etalonų:

* **Kadre esantis taikinys**: kai pažymėtas taikinio vaizdas atitinkChloros kokybės (QA) reikalavimus, taikinys tampa**absoliučiu atspindžio etalonu** aplinkiniams vaizdams.
* **DAQ žemyn nukreiptas spinduliavimas**: kai taikinio nėra (arba QA neatitinka reikalavimų), „Chloros“ atspindžio koeficientą apskaičiuoja remdamasis DAQ šviesos jutiklio žemyn nukreipto spinduliavimo intensyvumu (ρ = π·L/E). Jei jūsų užfiksuoti vaizdai patenka į „`.daq`“ arba „DAQ-M `.csv`“ įrašą, gausite kalibruotą atspindžio koeficientą**visiškai be jokių etaloninių vaizdų**.

Šis automatinis veikimas yra numatytasis. „CLI“ / „SDK“ tai atitinka „`--reflectance-source auto`“; taip pat galite priverstinai nustatyti „`target`“ (griežtas — be DAQ pakeitimo) arba „`daq`“ (DAQ-autoritetingas). Žr. [„CLI“ nuorodą](../reference/cli-reference.md#per-product-export-toggles-lattice-multispectral).

**„LATTICE“ taikinių geometrijos**: be klasikinio plokščiųjų taikinių aptikimo, naudojamo „Survey3“, „LATTICE“ apdorojimas palaiko**„ArUco“ pažymėtus taikinius**,**fiksuotų ROI taikinius**ir**juostinius taikinius**, konfigūruojamus pagal projektą. Kiekvieno vieneto**išmatuoti** taikinio atspindžio skenai gali būti pateikiami pagal serijos numerį (CLI: `--target-reflectance-dir`, po vieną `<serial>.csv` kiekvienam taikinio vienetui), o kaip atsarginis variantas naudojami nominalūs T3/T4P spektrai.

{% hint style="info" %}
**F988 modulis**: F988 atspindžio koeficientas kalibruojamas naudojant vietoje esančią atspindžio plokštelę: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl „Chloros“ naudoja jūsų naujausią plokštelės užfiksuotą duomenų rinkinį ir išlaiko jį tarp plokštelės matavimų. Jei F988 modulis apdorojamas tik naudojant DAQ, „Chloros“ atmeta DAQ pagrįstą atspindžio koeficientą tam juostos diapazonui (praleidimo priežastis `dls-uncalibrated-band-988`) — palaikomas plokštelės darbo srautas.
{% endhint %}

***

## Geriausios praktikos, susijusios su tiksliniais vaizdais

### Tikslinio vaizdo fiksavimo gairės

**Laikas:**

* Fiksuokite tikslinius vaizdus iškart prieš fiksavimo sesiją ir jos metu
* Esant toms pačioms apšvietimo sąlygoms, kaip ir jūsų DAQ šviesos jutiklio
* Geriausių rezultatų pasiekimui tikslinį vaizdą idealiu atveju fiksuokite kuo dažniau. Priešingu atveju, šviesos jutiklio duomenys bus naudojami kalibravimui koreguoti laikui bėgant.

**Kameros padėtis:**

* Laikykite kamerą virš tikslo taip, kad jis būtų centruotas ir užimtų apie 40–60 % vaizdo centro.
* Laikykite kamerą lygiagrečiai su objekto paviršiumi arba tiesiai virš jo (nadir)

**Apšvietimas:**

* Toks pat aplinkos apšvietimas, kaip ir jūsų DAQ šviesos jutiklio
* Venkite šešėlių ant objekto paviršių
* Neužstokite šviesos šaltinio savo kūnu, transporto priemone ar augmenija
* Debesuota diena užtikrina nuosekliausius rezultatus

**Objekto būklė:**

* Laikykite taikinio plokštes švarias ir sausas
* Visos taikinio plokštės (pvz., visos 4 ant T4) turi būti aiškiai matomos ir netrukdomos
* Jei įmanoma, taikiniai turi būti statmenai / vertikaliai šviesos šaltiniui

### Kiek taikinio vaizdų?

**Mažiausiai:**1 taikinio nuotrauka per sesiją.**Rekomenduojama:** 3–5 taikinio nuotraukos per sesiją.**Geriausios praktikos tvarkaraštis:**

* 3–5 nuotraukos, padarytos netrukus po to, kai šviesos jutiklis pradeda įrašyti
* Norėdami gauti geriausius rezultatus, tarp atskirų kadrų keiskite kameros padėtį
* Pasirinktinai: periodiškai sesijos viduryje, jei apšvietimo sąlygos nuolat keičiasi

***

## Darbas su keliomis kameromis

### Dviejų kamerų konfigūracijos

Jei vienu metu naudojate dvi „MAPIR“ kameras (pvz., Survey3W RGN + Survey3N OCN):

1. Užfiksuokite taikinio vaizdus **abiem kameromis** tuo pačiu metu
2. Abiem kameroms naudokite **tą patį fizinį taikinį**

3. Failų naršyklėje pažymėkite taikinio vaizdus**abiem kamerų tipams**

4. „Chloros“ naudos atitinkamus taikinius kiekvienos kameros kalibravimui

### Stulpelis „Kameros modelis“

Stulpelis **„Kameros modelis“** padeda nustatyti, kurie vaizdai buvo užfiksuoti kuria kamera:

* Survey3W\_RGN
* Survey3N\_OCN
* LATT-M3M-L41-F550
* LATT-M3C-L87-FRGN
* ir kt.

Naudokite šią skiltį, kad patikrintumėte, ar savo projekte pažymėjote tikslus kiekvienam kameros tipui.

***

## Tikslo aptikimo nustatymai

### Aptikimo jautrumo reguliavimas

Jei „Chloros“ netinkamai aptinka jūsų tikslus, pakoreguokite šiuos nustatymus [Projekto nustatymuose](adjusting-project-settings.md):**Mažiausias kalibravimo mėginio plotas (px):*** **Numatytasis**: 25 pikseliai
* **Padidinkite**, jei gaunami klaidingi aptikimai dėl smulkių artefaktų
* **Sumažinkite**, jei tikslai neaptinkami**Minimalus tikslų sugrupavimas (0–100):*** **Numatytasis**: 60
* **Padidinkite**, jei tikslai suskaidomi į kelis aptikimus
* **Sumažinkite**, jei tikslai su spalvų variacijomis nėra visiškai aptinkami

{% hint style="info" %}
**„CLI“ patarimas**: `chloros-cli process` palaiko tuos pačius reguliatorius (`--min-target-size`, `--target-clustering`), o jo vėliavėlė `--target`/`--targets` pažymi visą įvesties aplanką kaip skirtą tik tikslų skydeliui. Žr. [„CLI“ žinyną](../reference/cli-reference.md).
{% endhint %}

***

## Dažnos problemos su tiksliniais vaizdais

### Problema: Tikslai neaptikti

**Galimos priežastys:**

* Tiksliniai vaizdai nepažymėti failų naršyklėje
* Tikslas kadre per mažas (&lt; 30 % vaizdo)
* Blogas apšvietimas (šešėliai, atspindžiai)
* Per griežti tikslo aptikimo nustatymai

**Sprendimai:**

1. Patikrinkite, ar stulpelyje „Tikslas“ pažymėti teisingi vaizdai
2. Peržiūrėkite tikslo vaizdo kokybę peržiūros lange
3. Jei kokybė prasta, iš naujo nufotografuokite tikslus
4. Prireikus pakoreguokite tikslų aptikimo nustatymus

### Problema: klaidingas tikslų aptikimas

**Galimos priežastys:**

* Balti pastatai, transporto priemonės arba žemės danga klaidingai palaikomi tikslais
* Ryškūs plotai augmenijoje
* Per mažas aptikimo jautrumas

**Sprendimai:**

1. Pažymėkite tik tikruosius taikinio vaizdus — nuskaityti bus tik pažymėti vaizdai
2. Padidinkite minimalų kalibravimo mėginio plotą
3. Padidinkite minimalų taikinio grupavimo koeficientą
4. Užtikrinkite, kad taikinio vaizduose būtų matomas tik taikinys (kuo mažiau foninių trukdžių)

***

## Patikrinimo kontrolinis sąrašas

Prieš pradėdami apdorojimą, patikrinkite savo pasirinktus taikinių vaizdus:

* [ ] Bent 1 pažymėtas taikinių vaizdas per sesiją (arba, jei naudojate „LATTICE“, „`.daq`/`.csv`“ įrašas, apimantis sesiją)
* [ ] Visiems tikslo vaizdams pažymėti tikslo stulpelio žymimieji langeliai
* [ ] Tikslo vaizdai užfiksuoti per tą patį laikotarpį kaip ir tyrimas
* [ ] Tikslai aiškiai matomi peržiūroje, kai ant jų spustelėjama
* [ ] Kiekviename tikslo vaizde matomi visi kalibravimo skydeliai
* [ ] Ant tikslų nėra šešėlių ar kliūčių
* [ ] Naudojant dvi kameras: tikslai pažymėti abiejų tipų kamerose

***

## Apdorojimas be tikslų

### LATTICE: su DAQ įrašu

Jei DAQ šviesos jutiklis užfiksavo žemyn nukreiptą spinduliuotę per jūsų LATTICE fiksavimus, tikslai nereikalingi:

1. Importuokite failą `.daq` (arba DAQ-M `.csv`) su vaizdais
2. Palikite stulpelį „Tikslas“ nepažymėtą
3. Atspindžio koeficientas automatiškai apskaičiuojamas pagal DAQ žemyn nukreiptą atskaitos spinduliavimą
4. Spinduliavimui niekada nereikia taikinio ar DAQ duomenų — jis gaunamas vien tik iš kameros gamyklinio radiometrinio kalibravimo

### Apdorojimas be jokių etalonų

Taip pat galite apdoroti duomenis be taikinio ir be DAQ:

1. Palikite visas stulpelio „Target“ žymės langelius nepažymėtus
2. **Išjunkite** „Atspindžio kalibravimas / baltos spalvos balansas“ projekto nustatymuose – tuomet taikinio aptikimas bus visiškai praleistas
3. Vigneto korekcija vis tiek bus taikoma
4. Rezultatas nebus kalibruotas pagal absoliučią atspindžio vertę (LATTICE multispektrinė programa vis tiek eksportuoja debayered, peržiūros ir spinduliavimo produktus)

{% hint style="warning" %}
**Nerekomenduojama „Survey3“ moksliniams tyrimams**: Be atspindžio kalibravimo „Survey3“ pikselių reikšmės atspindi tik santykinį ryškumą, o ne mokslinius atspindžio matavimus. Norėdami gauti tikslius ir pakartojamus rezultatus, naudokite kalibravimo taikinius (arba, „LATTICE“ atveju, DAQ šviesos jutiklį).
{% endhint %}

***

## Tolimesni veiksmai

Pažymėję tikslinį vaizdą:

1. **Patikrinkite nustatymus** – žr. [Projekto nustatymų koregavimas](adjusting-project-settings.md)
2. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)
3. **Stebėkite apdorojimo eigą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)

Daugiau informacijos apie pačius kalibravimo taikinius rasite skyriuje [Kalibravimo taikiniai](../calibration-targets.md).
