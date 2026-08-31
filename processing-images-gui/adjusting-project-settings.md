# Projekto nustatymų konfigūravimas

Prieš apdorojant vaizdus, svarbu sukonfigūruoti projekto nustatymus taip, kad jie atitiktų jūsų darbo eigos reikalavimus. Skirtuke „Projekto nustatymai“ (<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">) galima visapusiškai valdyti kalibravimą, apdorojimo parinktis, multispektrinius indeksus ir eksporto formatus.

## Kaip pasiekti projekto nustatymus

1. Atidarykite savo projektą Chloros
2. Kairiajame šoniniame meniu spustelėkite piktogramą **„Projekto nustatymai“** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">
3. Skirtuke „Projekto nustatymai“ rodomos visos konfigūracijos parinktys

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption><p>Projekto nustatymų skydelis — rodymas, taikinio aptikimas ir apdorojimas</p></figcaption></figure>{% hint style="info" %}
**Nustatymai išsaugomi automatiškai** kartu su projektu. Kai vėl atidarote projektą, visi nustatymai atkuriami.
{% endhint %}

***

## Greitas nustatymas dažniausiai naudojamiems darbo srautams

### Numatytieji nustatymai (rekomenduojami daugumai vartotojų)

Numatytieji nustatymai puikiai tinka tipiniams Survey3 ir LATTICE darbo srautams:

* ✅ **Vignette korekcija**: Įjungta
* ✅ **Atspindžio kalibravimas / baltos spalvos balansas**: Įjungta (naudojami MAPIR taikiniai ir/arba DAQ šviesos jutiklio duomenys)
* ✅ **„Debayer“ metodas**: Standartinis (greitas, vidutinės kokybės)
* ✅ **Eksporto formatas**: TIFF (16 bitų)
* ✅ **Visi eksporto produktai**: Įjungta (LATTICE automatiškai išskirsto duomenis į debayered, peržiūros, spinduliavimo ir atspindžio vaizdus)

Tiesiog importuokite savo vaizdus ir pradėkite apdorojimą naudodami šiuos numatytuosius nustatymus.

***

## Projekto nustatymų apžvalga

Projekto nustatymų skydelis suskirstytas į toliau nurodytus skyrius. Du papildomi skyriai — **DAQ šviesos jutiklis**ir**Matricos suderinimas** — pasirodo automatiškai, jei jūsų projekte yra atitinkami failai. Išsamią dokumentaciją rasite [Projekto nustatymuose](../project-settings/project-settings.md).

### Rodymas

* **Vaizdų miniatiūrų skiriamoji geba**: vaizdų tinklelio miniatiūrų skiriamoji geba. Parinktys:**Numatytasis (512 px)**,**1024 px**,**2048 px**,**Pilna skiriamoji geba**. Tik rodymui — niekada neturi įtakos apdorojimui. Didesnės reikšmės atrodo ryškiau priartinus, tačiau įkeliamos lėčiau.

### Tikslo aptikimas

Nustato, kaip Chloros atpažįsta kalibravimo tikslus jūsų vaizduose.

**Pagrindiniai nustatymai:*** **Mažiausias kalibravimo mėginio plotas (px)**: Dydžio riba taikinių aptikimui (numatyta reikšmė:**25**, diapazonas 0–10000)
* **Mažiausias taikinių grupavimas (0–100)**: Panašumo riba, reikalinga tikslų sričių grupavimui (numatyta:**60**)**Kada reguliuoti:**

* Padidinkite imties plotą, jei gaunami klaidingi aptikimai
* Sumažinkite, jei tikslai neaptinkami
* Reguliuokite grupavimą, jei tikslai suskaidomi į kelis aptikimus

{% hint style="info" %}
Šie parametrai yra išblukinti, kai išjungta funkcija **Atspindžio kalibravimas / baltos spalvos balansas** — kai ji išjungta, tikslų aptikimas visiškai neveikia.
{% endhint %}

### Apdorojimas

Pagrindiniai vaizdo apdorojimo ir kalibravimo parametrai.

**Pagrindiniai nustatymai:*** **Vignette korekcija**: Kompensuoja objektyvo tamsėjimą kraštuose ✅ Rekomenduojama
* **Atspindžio kalibravimas / baltos spalvos balansas**: Kalibruoja vaizdus naudojant aptiktus taikinius (Survey3) ir (arba) DAQ šviesos jutiklio duomenis (LATTICE) ✅ Rekomenduojama
* **„Debayer“ metodas**: algoritmas, skirtas RAW failų konvertavimui į 3 kanalų multispektrinius
* **Minimalus pakartotinio kalibravimo intervalas**: minimalus laikas sekundėmis tarp kalibravimo taškų naudojimo (numatyta reikšmė:**0** = naudoti visus, diapazonas 0–3600)**Nekalibruoti atsarginiai produktai:**Kai kadro atspindžio kalibravimas neįmanomas (nėra taikinio arba kalibravimas išjungtas), jis eksportuojamas kaip vienas iš dviejų atsarginių produktų —**kiekvienam vykdymo ciklui egzistuoja tik vienas iš šios poros**, pasirinktas pagal „Vignette“ korekcijos jungiklį:

* **Eksportuoti jutiklio atsaką**: įrašo `Sensor_Response_Images` — naudojama, kai „Vignette“ korekcija yra**išjungta*** **Eksportuoti su „Vignette“ korekcija**: įrašo `Vignette_Corrected_Images` — naudojama, kai „Vignette“ korekcija yra**įjungta**Neaktyvus žymimasis langelis yra išblukintas. Pašalinus žymę iš aktyviojo langelio, tas failas iš viso nebus įrašomas.**„LATTICE“ eksporto produktai** (rodomi kiekvienam projektui; jie taikomi „LATTICE“ užfiksuotoms nuotraukoms):

* **Eksportuoti be „debayeringo“**: linijinis vaizdas be „debayeringo“ (`Debayered_Images`). Taikoma RGB ir multispektriniams moduliams.
* **Eksportuoti peržiūrą**: ekrano peržiūra (`Preview_Images`). RGB = baltos spalvos balansas (DAQ šviesos šaltinis, jei yra, kitaip – pilkosios aplinkos) + gama; daugiaspektriniai = netikrų spalvų išplėtimas.
* **Spinduliavimo eksportavimas**: „float32“ spektrinis spinduliavimas (`Radiance_Images`, W/m²/sr/nm). Tik daugiaspektriniai moduliai — netaikoma RGB pagrindiniams failams.
* ****Eksportuoti atspindžio koeficientą**: „uint16“ atspindžio koeficientas (`Reflectance_Calibrated_Images`, DN 32768 = ρ 1,0), kai `.daq` žemyn nukreiptas matavimas arba kadre esantis taikinys užima visą kadrą. Tik daugiaspektriniai moduliai.

Visi keturi yra **įjungti pagal numatytuosius nustatymus**— vienas importuotas „LATTICE“ neapdorotas kadras vienu apdorojimo etapu išskirstomas į visus įjungtus ir taikytinus produktus. Žymės langelis**Eksportuoti atspindžio koeficientą** yra išblukęs, kai atspindžio kalibravimas yra išjungtas. Nustatymai, kurių neįmanoma pakeisti dėl viršutinio jungiklio, visada yra išblukinti, o šalia jų rodomas paaiškinimas, nurodantis, kurį jungiklį reikia pakeisti.**Išplėstiniai nustatymai:*** **Šviesos jutiklio laiko juostos poslinkis**: Valandos nuo UTC, skirtos šviesos jutiklio laiko suderinimui (pagal numatytuosius nustatymus: 0, diapazonas nuo −12 iki +12)
* **Taikyti PPK pataisas**: naudoja GPS/ekspozicijos žymių duomenis iš `.daq` failų (numatyta: išjungta)
* **Ekspozicijos žymės 1/2**: priskiria kameras ekspozicijos žymėms dviejų kamerų konfigūracijose

{% hint style="info" %}
**„LATTICE“ įvesties lygis nustatomas automatiškai.** „LATTICE“ užfiksuoti kadrai savo apdorojimo lygį perduoda XMP metaduomenyse, o apdorojimas visada prasideda nuo neapdoroto kadro – vartotojo sąsajoje nereikia nieko konfigūruoti. (Žymė CLI `--input-level` yra skirta pažengusiems vartotojams, norintiems perrašyti nustatymus, kai įrašai neturi metaduomenų; žr. [CLI nuorodą](../reference/cli-reference.md).)
{% endhint %}

### Debayerio metodas

Šiuo metu „Chloros“ siūlome 2 debayerio metodus:

#### Standartinis (greitas, vidutinė kokybė)

Standartinis debayeris apdoroja greitai, bet rodo debayerio spalvinį triukšmą, dėl ko vaizdai tampa mažiau tikslūs ir triukšmingesni.

#### Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė) \[Tik Chloros+]

Metodas „Atsižvelgiantis į tekstūrą“ naudoja aukštos kokybės, kraštus atpažįstantį „debayering“ metodą, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą „debayering“ triukšmą. Šiam modeliui veikti reikalinga GPU atmintis (VRAM): turint **7 GB ar daugiau VRAM**, jis gali apdoroti kelis vaizdus vienu metu; jei VRAM mažiau nei 7 GB, jis apdoroja po vieną vaizdą (žymiai lėčiau). Žr. [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md).

{% hint style="info" %}
**„LATTICE“ užfiksuotuose vaizduose visada naudojamas standartinis demosaic.** „LATTICE“ nėra apmokyto „Texture Aware“ modelio, todėl ši parinktis nesiūloma „LATTICE“ vaizdams — tačiau to paties projekto Survey3 vaizduose ją vis dar galima naudoti.
{% endhint %}

### Indeksai (daugiaspektriniai indeksai)

Nustatykite, kokius augmenijos indeksus skaičiuoti ir eksportuoti. Vartotojo sąsajos išskleidžiamajame meniu siūloma **27 iš anksto apibrėžtų indeksų formulių**.**Kaip pridėti indeksus:**

1. Spustelėkite mygtuką**„Pridėti indeksą“**

2. Išskleidžiamajame meniu pasirinkite indeksą (NDVI, NDRE, GNDVI ir pan.)
3. Nustatykite vizualizavimo parametrus (LUT spalvas, verčių intervalus)
4. Prireikus pridėkite kelis indeksus

**Populiariausi indeksai:*** **NDVI**: Bendras augmenijos sveikumas (dažniausiai naudojamas)
* **NDRE**: Ankstyvas streso nustatymas naudojant RedEdge
* **GNDVI**: Jautrus chlorofilo koncentracijai
* **OSAVI**: Gerai tinka, kai dirvožemis matomas
* **EVI**: Regionai su dideliu lapų ploto indeksu (LAI)**Pasirinktinės formulės:**

* Kurkite pasirinktines multispektrinių indeksų formules, taikydami juostų skaičiavimus visiems vaizdo kanalams
* Išsaugokite pasirinktines formules, kad galėtumėte jas naudoti pakartotinai
* Pasirinktinės formulės yra „Chloros+“ funkcija; jos prieinamumas priklauso nuo jūsų plano lygio

Visus galimus indeksus ir formules — įskaitant tai, kurie pavadinimai yra tik GUI, o kurie taip pat veikia „CLI“/„SDK“ — rasite [Daugiaspektrinių indeksų formulėse](../project-settings/multispectral-index-formulas.md).

### Eksportavimas

Nustato išvesties failo formatą.

**Galimi formatai**(nustatymas:**Kalibruoto vaizdo formatas**, numatytasis**TIFF (16 bitų)**):

* **TIFF (16 bitų)**: rekomenduojama GIS ir mokslinei analizei
* **TIFF (32 bitai, procentais)**: slankiojo kablelio reikšmės
* **PNG (8 bitų)**: be nuostolių suspaudimas vizualizavimui
* **JPG (8 bitų)**: mažiausi failai, suspaudimas su nuostoliais

Išvesties failai įrašomi projekto aplanke, sugrupuoti pagal kamerą ir formatą: `<project>/<camera>/<format>/<Product>_Images/`. „Radiance“ **visada** įrašomas kaip „float32“ į aplanką `tiff32`, nepriklausomai nuo šio nustatymo. Eksportuoti failai išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas. Visą išvesties medį rasite skyriuje [Apdorojimo užbaigimas](finishing-the-processing.md).

{% hint style="warning" %}
**Atspindžio koeficientų skaitymas**: DN, reiškiantis, kad ρ = 1,0, priklauso nuo šaltinio kameros — „LATTICE“ naudoja 32768 (pažymėta kaip XMP `Chloros:PixelScale`), o „Survey3“ naudoja 65535. Verčiau perskaitykite žymę, o ne laikykitės prielaidos, kad reikšmė yra pastovi. Žr. [Išvesties vaizdo formatai](../output-image-formats.md).
{% endhint %}

### DAQ šviesos jutiklis

Šiame skyriuje išvardyti visi jūsų projekte esantys DAQ žemyn nukreiptų spindulių failai (`.daq` / `.csv`), po vieną eilutę kiekvienam failui, kuriuose nurodytas jutiklio modelis, failo pavadinimas ir tam failui taikoma difuzoriaus **ribos** korekcija.

* **Ribos perrašymas (visi failai)**: vienas išskleidžiamas meniu, galiojantis visam projektui.**Auto** (numatyta reikšmė) naudoja kiekviename faile užregistruotą ribą — jei nieko nebuvo užregistruota, daroma prielaida, kad buvo saulėta, nes visi MAPIR DAQ siunčiami su saulės korektoriumi. Pasirinkus ribą, ji pakeičia visus failus: neapdoroti įrašai koreguojami pagal ją, o įrašai, kuriuose jau yra riba, perorientuojami (užfiksuota korekcija panaikinama, taikoma pasirinkta riba).
* Eilutėse rodomi įspėjimai, kai užregistruota riba buvo laikoma koncentratoriaus numatyta, o ne operatoriaus patvirtinta, ir kai pasirinktai ribai nėra profilio tam įrenginio modeliui (tuo atveju perrašymas tam failui atmetamas).

„Šviesos jutikliai“ skirtuke atlikti DAQ įrašai automatiškai pridedami prie atidaryto projekto, o importuoti `.daq` / `.csv` failai čia pasirodo iškart po to, kai jie pridedami.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p>Apatiniai projekto nustatymai — indeksas, eksporto formatas, skyrius „DAQ šviesos jutikliai“ ir projekto šablono / aplanko valdymo elementai</p></figcaption></figure>### Masyvo suderinimas

Šis skyrius rodomas **tik** tada, kai bent viename projekto vaizde yra modulio suderinimo transformacija, kurią LATTICE masyvai pažymi fiksavimo metu (`Chloros:Alignment*` XMP). Čia rodoma, kiek vaizdų turi žymes ir kuri kamera yra etalonas, su šiais valdikliais:

* **Taikyti matricos suderinimą** (numatyta: įjungta): kiekvieną apdorotą produktą (debayering / peržiūra / spinduliavimas / atspindys / indeksas) iškraipyti pagal bendrą matricos etaloninę geometriją. Išjungta = eksportuoti pagal natūralią jutiklio geometriją.
* **Apkarpyti iki bendro persidengimo** (numatyta: įjungta): suderintus eksportuojamus duomenis apkarpyti iki srities, kurią dalijasi visi moduliai, kad kiekviena juosta užimtų tą patį plotą. Išjungus išlaikomas visas jutiklio vaizdas (juoda užpildyta sritis už šaltinio ribų).
* **Perimatuavimas**:**Bilinearinis (lygus, numatytasis)**,**Artimiausias (išsaugo tikslias reikšmes)**— be pikselių tarpusavio maišymo, skirta griežtai radiometrinei analizei — arba**Kubinis (aštriausias)**.***

## Nustatymų išsaugojimas ir įkėlimas

### Projekto šablono išsaugojimas

Sukurkite pakartotinai naudojamus šablonus, kad darbo eiga būtų nuosekli:

1. Nustatykite visus norimus parametrus skydelyje „Projekto nustatymai“
2. Nuslinkite į apačioje esantį skyrių **„Išsaugoti projekto šabloną“**

3. Įveskite apibūdinantį šablono pavadinimą (pvz., „Survey3N\_RGN\_Agriculture“)
4. Spustelėkite išsaugojimo piktogramą

**Privalumai:**

* Taikykite identiškus nustatymus keliuose projektuose
* Dalinkitės konfigūracijomis su komandos nariais
* Užtikrinkite nuoseklumą kartojamose apklausose

### Šablono įkėlimas į naują projektą

Kuriant naują projektą:

1. Pagrindiniame meniu pasirinkite **„Naujas projektas“**

2. Pasirinkite projekto šabloną pasirinkimo lange (jei toks yra)
3. Visi šablono nustatymai bus automatiškai pritaikyti

### Darbo katalogas

**„Darbo katalogas“** nustatymas nurodo, kur pagal numatytuosius nustatymus kuriamos naujos projektai:

* **Numatytasis katalogas**: `C:\Users\[Username]\Chloros Projects`
* **Pakeisti katalogą**: Spustelėkite redagavimo piktogramą ir pasirinkite naują aplanką
* **Bendrai naudojama su CLI**: `chloros-cli` naudoja tą patį numatytąjį projektų aplanko nustatymą
* **Kada keisti**:
  * Tinklo diskas komandiniam bendradarbiavimui
  * Kitas diskas su didesne saugojimo talpa
  * Tvarkinga aplankų struktūra pagal metus / klientą

***

## PPK (Post-Processed Kinematic) nustatymas

Jei naudojate „MAPIR“ DAQ įrašymo įrenginius su GPS tiksliai geografinei vietai nustatyti:

### Būtinos sąlygos

* „MAPIR“ DAQ su GPS (GNSS) moduliu
* .daq įrašų failas su ekspozicijos kontaktų įrašais
* Kameros prijungimas prie DAQ ekspozicijos kontaktų įrašymo sesijos metu

### Konfigūravimo žingsniai

1. Įdėkite .daq žurnalo failą į savo projekto aplanką
2. Projekto nustatymuose pažymėkite langelį **„Taikyti PPK pataisas“**

3. Jei reikia, nustatykite**„Šviesos jutiklio laiko juostos nuokrypį“** (numatyta reikšmė: 0 UTC)
4. Priskirkite kameras ekspozicijos kontaktams:
   * **Viena kamera**: automatiškai priskiriama 1 kontaktui
   * **Dvi kameros**: kiekvieną kamerą rankiniu būdu priskirkite atitinkamam kontaktui**Ekspozicijos kontaktų priskyrimas:*** **Ekspozicijos kontaktas 1**: išskleidžiamajame meniu pasirinkite kameros modelį
* **Ekspozicijos kontaktas 2**: pasirinkite antrąją kamerą arba „Nenaudoti“
* Viena ir ta pati kamera negali būti priskirta abiem kontaktams

{% hint style="warning" %}
**Svarbu**: Ekspozicijos kontaktiniai taškai turi būti teisingai priskirti atitinkamoms kameroms. Neteisingas priskyrimas lems klaidingus geolokacijos duomenis.
{% endhint %}

***

## Išplėstiniai scenarijai

### Daugiakameriniai projektai

Apdorojant vaizdus iš kelių MAPIR kamerų viename projekte:

1. Chloros automatiškai atpažįsta kiekvieną kameros modelį (tiek Survey3, tiek LATTICE)
2. Kiekvienai kamerai priskiriami atitinkami apdorojimo profiliai, o kiekvienai kamerai sukuriama atskira išvesties aplankų struktūra
3. PPK: Rankiniu būdu priskirkite kiekvieną „Survey3“ kamerą teisingam ekspozicijos kontaktui
4. Visos kameros naudoja tą patį eksporto formatą ir indeksus

**Pavyzdžiai**: Survey3W, RGN + Survey3N, OCN dviejų kamerų įranga, arba „LATTICE“ matrica, kurioje pagrindinė kamera „RGB“ derinama su siaurajuosčio dažnio moduliais

### Laiko tarpo arba kelių datų tyrimai

Jei tą pačią teritoriją ketinate tirti pakartotinai per tam tikrą laikotarpį:

1. Sukurkite šabloną su standartiniais nustatymais
2. Kiekvieną sesiją naudokite nuoseklų kalibravimo taikinio nustatymą
3. Kiekvieną datą apdorokite kaip atskirą projektą
4. Naudokite identiškus nustatymus, kad gautumėte palyginamus rezultatus
5. Eksportuokite tuo pačiu formatu laiko analizei

### Didelės duomenų rinkmenų apimtys

Projektams su daugybe vaizdų (500+):

* Apsvarstykite galimybę suskirstyti į mažesnius projektus pagal datą ar teritoriją
* Naudokite „Chloros+“ lygiagretų apdorojimą, kad gautumėte greitesnius rezultatus
* Apsvarstykite „CLI“ arba „API“ naudojimą automatizuotam paketiniam apdorojimui
* Nustatykite minimalų pakartotinio kalibravimo intervalą, kad sutrumpintumėte taikinio aptikimo laiką

***

## Nustatymų patikrinimas

Prieš pradėdami apdorojimą, patikrinkite šiuos pagrindinius nustatymus:

* [ ] Kameros modelis teisingai atpažintas failų naršyklėje
* [ ] Įjungta vinjetės korekcija
* [ ] Įjungtas atspindžio kalibravimas
* [ ] Survey3 atveju: importuotas ir patikrintas bent vienas kalibravimo taikinio vaizdas; LATTICE atveju: yra taikinys ir (arba) `.daq` žemyn nukreiptas įrašas
* [ ] Pridėti norimi multispektriniai indeksai
* [ ] Eksporto formatas, tinkamas jūsų darbo eigai
* [ ] Nustatyti PPK parametrai (jei naudojamas .daq failas su ekspozicijos įvykiais)

***

## Tolimesni veiksmai

Kai nustatysite parametrus:

1. **Pažymėkite kalibravimo taikinio vaizdus** – žr. [Taikinio vaizdų pasirinkimas](choosing-target-images.md)
2. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)
3. **Stebėkite apdorojimo eigą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)

Išsamią informaciją apie visus galimus nustatymus rasite [Projekto nustatymai](../project-settings/project-settings.md) žinyno dokumentacijoje.
