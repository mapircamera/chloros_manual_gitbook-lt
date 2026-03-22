# Projekto nustatymų konfigūravimas

Prieš pradedant apdoroti vaizdus, svarbu sukonfigūruoti projekto nustatymus taip, kad jie atitiktų jūsų darbo eigos reikalavimus. Projekto nustatymų <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> skydelis suteikia išsamią kontrolę kalibravimo, apdorojimo parinkčių, multispektrinių indeksų ir eksporto formatų atžvilgiu.

## Prieiga prie projekto nustatymų

1. Atidarykite savo projektą Chloros
2. Spustelėkite **Projekto nustatymai** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> piktogramą kairėje šoninėje juostoje
3. Projekto nustatymų skydelyje rodomos visos konfigūracijos parinktys

{% hint style="info" %}
**Nustatymai automatiškai išsaugomi** kartu su projektu. Kai vėl atidarote projektą, visi nustatymai atkuriami.
{% endhint %}

***

## Greitas nustatymas įprastiems darbo srautams

### Numatytieji nustatymai (rekomenduojami daugumai vartotojų)

Tipiniams MAPIR Survey3 fotoaparatų darbo srautams puikiai tinka numatytieji nustatymai:

* ✅ **Vignette korekcija**: Įjungta
* ✅ **Atstumo kalibravimas**: Įjungta (reikalingi MAPIR tikslų vaizdai)
* ✅ **Debayer metodas**: Standartinis (Greitas, Vidutinė kokybė)
* ✅ **Eksporto formatas**: TIFF (16 bitų)

Tiesiog importuokite savo vaizdus ir pradėkite apdorojimą naudodami šiuos numatytuosius nustatymus.

***

## Projekto nustatymų apžvalga

Projekto nustatymų skydelis suskirstytas į keletą kategorijų. Toliau pateikta kiekvieno skirsnio santrauka. Išsamią dokumentaciją rasite [Projekto nustatymuose](../project-settings/project-settings.md).

### Taikinio aptikimas

Nustato, kaip Chloros identifikuoja kalibravimo taikinius jūsų vaizduose.

**Pagrindiniai nustatymai:*** **Minimalus kalibravimo mėginio plotas**: Dydžio riba taikinio aptikimui (numatyta: 25 pikseliai)
* **Minimalus taikinio grupavimas**: Panašumo riba taikinio sričių grupavimui (numatyta: 60)**Kada reguliuoti:**

* Padidinkite mėginio plotą, jei gaunami klaidingi aptikimai
* Sumažinkite, jei tikslai nėra aptinkami
* Reguliuokite grupavimą, jei tikslai yra suskaidomi į kelis aptikimus

### Apdorojimas

Pagrindiniai vaizdo apdorojimo ir kalibravimo parametrai.

**Pagrindiniai nustatymai:*** **Vignette korekcija**: Kompensuoja objektyvo tamsėjimą kraštuose ✅ Rekomenduojama
* **Atstumo kalibravimas**: Normalizuoja vertes naudojant kalibravimo tikslus ✅ Rekomenduojama
* **Debayer metodas**: Algoritmas, skirtas konvertuoti RAW į 3 kanalų multispektrinį vaizdą
* **Minimalus pakartotinio kalibravimo intervalas**: Laikas tarp kalibravimo taškų naudojimo (0 = naudoti visus)**Išplėstiniai nustatymai:*** **Šviesos jutiklio laiko juostos poslinkis**: Skirtas PPK laiko sinchronizavimui (numatyta reikšmė: 0)
* **Taikyti PPK korekcijas**: Naudoja GPS/ekspozicijos kontaktų duomenis iš .daq failų
* **Ekspozicijos kontaktas 1/2**: Priskiria kameras ekspozicijos kontaktams dviejų kamerų konfigūracijose

### Debayerio metodas

Šiuo metu Chloros siūlome 2 debayerio metodus:

#### Standartinis (greitas, vidutinė kokybė)

Standartinis debayeringas apdoroja greitai, bet rodo debayeringo spalvų triukšmą, dėl to vaizdai tampa mažiau tikslūs ir triukšmingesni.

#### Tekstūros atpažinimas (Lėtas, Aukščiausia kokybė) \[Tik Chloros+]

Tekstūros atpažinimas naudoja aukštos kokybės kraštų atpažinimo debayeringą kartu su AI/ML triukšmo pašalinimo modeliu, kuris pašalina beveik visą debayeringo triukšmą. „Texture Aware“ modeliui veikti reikalinga GPU atmintis (VRAM). Rekomenduojame jį naudoti, jei turite &gt;4 GB VRAM, kad apdorojimas vyktų greičiau.

### Indeksas (daugiaspektriniai indeksai)

Nustatykite, kuriuos augmenijos indeksus skaičiuoti ir eksportuoti.

**Kaip pridėti indeksus:**

1. Spustelėkite mygtuką**„Pridėti indeksą“**

2. Išskleidžiamajame meniu pasirinkite indeksą (NDVI, NDRE, GNDVI ir kt.)
3. Nustatykite vizualizacijos parametrus (LUT spalvas, verčių intervalus)
4. Prireikus pridėkite kelis indeksus

**Populiarūs indeksai:*** **NDVI**: Bendras augmenijos sveikumas (dažniausiai naudojamas)
* **NDRE**: Ankstyvas streso nustatymas kartu su RedEdge
* **GNDVI**: Jautrus chlorofilo koncentracijai
* **OSAVI**: Gerai veikia su matomu dirvožemiu
* **EVI**: Regionai su dideliu lapų ploto indeksu (LAI)**Pasirinktinės formulės (tik Chloros+):**

* Sukurkite pasirinktines daugiaspektrinių indeksų formules
* Naudokite juostų skaičiavimus su visais vaizdo kanalais
* Išsaugokite pasirinktines formules pakartotiniam naudojimui

Visus galimus indeksus ir formules rasite [Daugiaspektrinių indeksų formulėse](../project-settings/multispectral-index-formulas.md).

### Eksportavimas

Nustato išvesties failo formatą ir kokybę.

**Galimi formatai:*** **TIFF (16 bitų)**: Rekomenduojama GIS ir mokslinei analizei (0–65 535 diapazonas)
* **TIFF (32 bitai, procentais)**: Plaukiojančiojo kablelio atspindžio vertės (0,0–1,0 diapazonas)
* **PNG (8 bitai)**: be nuostolių suspaudimas vizualizavimui (0–255 diapazonas)
* **JPG (8 bitai)**: mažiausi failai, suspaudimas su nuostoliais (0–255 diapazonas)***

## Nustatymų išsaugojimas ir įkėlimas

### Projekto šablono išsaugojimas

Sukurkite pakartotinai naudojamus šablonus, kad darbo eiga būtų nuosekli:

1. Nustatykite visus norimus parametrus skydelyje „Project Settings“ (Projekto nustatymai)
2. Nuslinkite į apačioje esantį skyrių **„Save Project Template“** (Išsaugoti projekto šabloną)
3. Įveskite apibūdinantį šablono pavadinimą (pvz., „Survey3N\_RGN\_Agriculture“)
4. Spustelėkite išsaugojimo piktogramą

**Privalumai:**

* Taikykite identiškus nustatymus keliuose projektuose
* Dalinkitės konfigūracijomis su komandos nariais
* Užtikrinkite nuoseklumą kartojamose apklausose

### Įkelti šabloną į naują projektą

Kuriant naują projektą:

1. Pagrindiniame meniu pasirinkite **„Naujas projektas“**

2. Pasirinkite parinktį**„Įkelti iš šablono“**

3. Pasirinkite išsaugotą šabloną
4. Visi nustatymai bus pritaikyti automatiškai

### Darbo katalogas

Nustatymas **„Išsaugoti projekto aplanką“** nurodo, kur pagal numatytuosius nustatymus kuriamos naujos projektai:

* **Numatytasis vietos**: `C:\Users\[Username]\Chloros Projects`
* **Pakeisti vietą**: Spustelėkite redagavimo piktogramą ir pasirinkite naują aplanką
* **Kada keisti**:
  * Tinklo diskas komandiniam bendradarbiavimui
  * Kitas diskas su daugiau saugojimo vietos
  * Tvarkinga aplankų struktūra pagal metus/klientą

***

## PPK (Post-Processed Kinematic) nustatymas

Jei naudojate MAPIR DAQ įrašymo įrenginius su GPS tiksliai geografinei vietai nustatyti:

### Privalomi reikalavimai

* MAPIR DAQ su GPS (GNSS) moduliu
* .daq žurnalo failas su ekspozicijos kontaktų įrašais
* Kamera, prijungta prie DAQ ekspozicijos kontaktų įrašymo sesijos metu

### Konfigūracijos žingsniai

1. Įdėkite .daq žurnalo failą į savo projekto aplanką
2. Projekto nustatymuose pažymėkite langelį **„Taikyti PPK pataisas“**

3. Jei reikia, nustatykite**„Šviesos jutiklio laiko juostos nuokrypį“** (numatyta reikšmė: 0 UTC)
4. Priskirkite kameras ekspozicijos kontaktams:
   * **Viena kamera**: automatiškai priskiriama 1 kontaktui
   * **Dvi kameros**: rankiniu būdu priskirkite kiekvieną kamerą tinkamam kontaktui**Ekspozicijos kontaktų priskyrimas:*** **Ekspozicijos kontaktas 1**: išskleidžiamajame meniu pasirinkite kameros modelį
* **Ekspozicijos kontaktas 2**: pasirinkite antrą kamerą arba „Nenaudoti“
* Viena ir ta pati kamera negali būti priskirta abiem kontaktams

{% hint style="warning" %}
**Svarbu**: Ekspozicijos kontaktai turi būti teisingai priskirti atitinkamoms kameroms. Neteisingas priskyrimas lems neteisingus geografinės vietos duomenis.
{% endhint %}

***

## Išplėstiniai scenarijai

### Daugiakameriniai projektai

Apdorojant vaizdus iš kelių MAPIR kamerų viename projekte:

1. Chloros automatiškai aptinka kiekvienos kameros modelį
2. Kiekvienai kamerai priskiriamas atitinkamas apdorojimo profilis
3. PPK: rankiniu būdu priskirkite kiekvieną kamerą teisingam ekspozicijos kontaktui
4. Visos kameros naudoja tą patį eksporto formatą ir indeksus

**Pavyzdys**: Survey3W RGN + Survey3N OCN dviejų kamerų įranga

### Laiko tarpo arba kelių datų tyrimai

Pakartotiniems to paties ploto tyrimams laikui bėgant:

1. Sukurkite šabloną su standartiniais nustatymais
2. Kiekvieną sesiją naudokite nuoseklią kalibravimo taikinio konfigūraciją
3. Apdorokite kiekvieną datą kaip atskirą projektą
4. Naudokite identiškus nustatymus, kad gautumėte palyginamus rezultatus
5. Eksportuokite tuo pačiu formatu laiko analizei

### Didelės duomenų bazės

Projektams su daugybe vaizdų (500+):

* Apsvarstykite galimybę suskirstyti į mažesnius projektus pagal datą arba teritoriją
* Naudokite Chloros+ lygiagretų apdorojimą, kad gautumėte greitesnius rezultatus
* Apsvarstykite CLI arba API, jei norite automatizuoti partijų apdorojimą
* Nustatykite minimalų pakartotinio kalibravimo intervalą, kad sutrumpintumėte taikinio aptikimo laiką

***

## Nustatymų tikrinimas

Prieš pradėdami apdorojimą, peržiūrėkite šiuos pagrindinius nustatymus:

* [ ] Kameros modelis teisingai aptiktas failų naršyklėje
* [ ] Įjungta vinjetės korekcija
* [ ] Įjungtas atspindžio kalibravimas
* [ ] Importuotas bent vienas kalibravimo taikinio vaizdas
* [ ] Pridėti norimi multispektriniai indeksai
* [ ] Eksporto formatas, tinkamas jūsų darbo eigai
* [ ] Nustatyti PPK parametrai (jei naudojate .daq su ekspozicijos įvykiais)

***

## Tolimesni veiksmai

Kai nustatymai sukonfigūruoti:

1. **Pažymėkite kalibravimo taikinio vaizdus** – žr. [Taikinio vaizdų pasirinkimas](choosing-target-images.md)
2. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)
3. **Stebėkite pažangą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)

Išsamią informaciją apie visus galimus nustatymus rasite [Projekto nustatymai](../project-settings/project-settings.md) informacinėje dokumentacijoje.
