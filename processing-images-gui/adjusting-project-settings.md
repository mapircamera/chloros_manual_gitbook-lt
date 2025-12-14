# Projekto nustatymų keitimas

Prieš apdorodami vaizdus, svarbu sukonfigūruoti projekto nustatymus, kad jie atitiktų jūsų darbo eigos reikalavimus. Projekto nustatymų <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> skydelis suteikia išsamią kontrolę kalibravimo, apdorojimo parinkčių, daugiaspektrinių indeksų ir eksporto formatų atžvilgiu.

## Prieiga prie projekto nustatymų

1. Atidarykite savo projektą Chloros
2. Spustelėkite **Projekto nustatymai** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> piktogramą kairėje šoninėje juostoje
3. Projekto nustatymų skydelyje rodomos visos konfigūracijos parinktys

{% hint style=&quot;info&quot; %}
**Nustatymai yra automatiškai išsaugomi** kartu su projektu. Atidarius projektą iš naujo, visi nustatymai yra atkurti.
{% endhint %}

***

## Greitas bendrų darbo eigų nustatymas

### Numatytieji nustatymai (rekomenduojami daugumai vartotojų)

Tipiniams MAPIR Survey3 kameros darbo srautams puikiai tinka numatytieji nustatymai:

* ✅ **Vignette korekcija**: įjungta
* ✅ **Atspindžio kalibravimas**: įjungtas (reikia MAPIR tikslų vaizdų)
* ✅ **Debayer metodas**: Aukšta kokybė (greičiau)
* ✅ **Eksporto formatas**: TIFF (16 bitų)

Tiesiog importuokite savo vaizdus ir pradėkite apdoroti naudodami šiuos numatytuosius nustatymus.

***

## Projekto nustatymų apžvalga

Projekto nustatymų skydelis suskirstytas į keletą kategorijų. Toliau pateikta kiekvienos sekcijos santrauka. Išsamią dokumentaciją rasite [Projekto nustatymuose](../project-settings/project-settings.md).

### Tikslo aptikimas

Kontroliuoja, kaip Chloros identifikuoja kalibravimo tikslus jūsų vaizduose.

**Pagrindiniai nustatymai:**

* **Minimalus kalibravimo pavyzdžio plotas**: tikslo aptikimo dydžio riba (numatyta: 25 pikseliai)
* **Minimalus tikslų grupuojimas**: tikslų regionų grupuojimo panašumo riba (numatyta: 60)

**Kada reguliuoti:**

* Padidinkite pavyzdžio plotą, jei gaunate klaidingus aptikimus.
* Sumažinkite, jei tikslai neaptinkami.
* Reguliuokite grupuojimą, jei tikslai yra suskaidomi į kelis aptikimus.

### Apdorojimas

Pagrindiniai vaizdo apdorojimo ir kalibravimo parametrai.

**Pagrindiniai nustatymai:**

* **Vignette korekcija**: kompensuoja objektyvo patamsėjimą kraštuose ✅ Rekomenduojama
* **Atspindžio kalibravimas**: normalizuoja vertes naudojant kalibravimo tikslus ✅ Rekomenduojama
* **Debayer metodas**: algoritmas RAW konvertavimui į 3 kanalų multispektralinį
* **Minimalus pakalibravimo intervalas**: laikas tarp kalibravimo tikslų naudojimo (0 = naudoti visus)

**Išplėstiniai nustatymai:**

* **Šviesos jutiklio laiko juostos poslinkis**: PPK laiko sinchronizavimui (numatyta: 0)
* **Taikyti PPK pataisas**: naudoja GPS/ekspozicijos kontaktų duomenis iš .daq failų
* **Ekspozicijos kontaktas 1/2**: priskiria kameras ekspozicijos kontaktams dviejų kamerų konfigūracijoms

### Indeksas (daugiabangiai indeksai)

Nustatykite, kokius augmenijos indeksus skaičiuoti ir eksportuoti.

**Kaip pridėti indeksus:**

1. Spustelėkite mygtuką **„Pridėti indeksą“**
2. Išskleidžiamajame meniu pasirinkite indeksą (NDVI, NDRE, GNDVI ir kt.)
3. Nustatykite vizualizavimo parametrus (LUT spalvas, verčių diapazonus)
4. Pridėkite kelis indeksus, jei reikia

**Populiarūs indeksai:**

* **NDVI**: bendra augmenijos būklė (dažniausiai naudojamas)
* **NDRE**: ankstyvas streso nustatymas su RedEdge
* **GNDVI**: jautrus chlorofilo koncentracijai
* **OSAVI**: gerai veikia su matoma dirva
* **EVI**: didelio lapų ploto indekso (LAI) regionai

**Pasirinktinės formulės (tik Chloros+):**

* Sukurkite individualias daugiaspektrinių indeksų formules
* Naudokite juostos matematiką su visais vaizdo kanalais
* Išsaugokite individualias formules pakartotiniam naudojimui

Visus galimus indeksus ir formules rasite [Daugiaspektrinių indeksų formulės](../project-settings/multispectral-index-formulas.md).

### Eksportavimas

Kontroliuoja išvesties failo formatą ir kokybę.

**Galimi formatai:**

* **TIFF (16 bitų)**: rekomenduojamas GIS ir moksliniams tyrimams (0–65 535 diapazonas)
* **TIFF (32 bitai, procentai)**: plaukiojančiojo kablelio atspindžio vertės (0,0–1,0 diapazonas)
* **PNG (8 bitai)**: be nuostolių suspaudimas vizualizavimui (0–255 diapazonas)
* **JPG (8 bitai)**: mažiausi failai, suspaudimas su nuostoliais (0–255 diapazonas)

***

## Nustatymų išsaugojimas ir įkėlimas

### Projekto šablono išsaugojimas

Sukurkite pakartotinai naudojamus šablonus nuosekliam darbo srautui:

1. Nustatykite visus norimus parametrus projekto nustatymų skydelyje.
2. Nusileiskite iki skyriaus **„Išsaugoti projekto šabloną“** apačioje.
3. Įveskite aprašomąjį šablono pavadinimą (pvz., „Survey3N\_RGN\_Agriculture“).
4. Spustelėkite išsaugojimo piktogramą.

**Privalumai:**

* Taikykite identiškus nustatymus keliuose projektuose.
* Dalinkitės konfigūracijomis su komandos nariais.
* Užtikrinkite nuoseklumą kartojamose apklausose.

### Šablono įkėlimas į naują projektą

Kuriant naują projektą:

1. Pagrindiniame meniu pasirinkite **„Naujas projektas“**.
2. Pasirinkite parinktį **„Įkelti iš šablono“**.
3. Pasirinkite išsaugotą šabloną.
4. Visi nustatymai bus pritaikyti automatiškai.

### Darbinis katalogas

Nustatymas **„Išsaugoti projekto aplanką“** nurodo, kur pagal numatytuosius nustatymus kuriamos naujos projektai:

* **Numatytasis vieta**: `C:\Users\[Username]\Chloros Projects`
* **Pakeisti vietą**: spustelėkite redagavimo piktogramą ir pasirinkite naują aplanką
* **Kada keisti**:
  * Tinklo diskas komandos bendradarbiavimui
  * Kitas diskas su daugiau saugojimo vietos
  * Aplankų struktūra suskirstyta pagal metus/klientus

***

## PPK (Post-Processed Kinematic) nustatymas

Jei naudojate MAPIR DAQ įrašymo įrenginius su GPS tiksliai geografinės vietos nustatymui:

### Privalomi reikalavimai

* MAPIR DAQ su GPS (GNSS) moduliu
* .daq žurnalo failas su ekspozicijos kontaktų įrašais
* Kamera, prijungta prie DAQ ekspozicijos kontaktų fiksavimo sesijos metu

### Konfigūracijos žingsniai

1. Įdėkite .daq žurnalo failą į savo projekto aplanką.
2. Projekto nustatymuose pažymėkite langelį **„Taikyti PPK pataisas“**.
3. Jei reikia, nustatykite **„Šviesos jutiklio laiko juostos poslinkį“** (numatyta reikšmė: 0 UTC).
4. Priskirkite kameras ekspozicijos kontaktams:
   * **Viena kamera**: Automatiškai priskirta kontaktui 1
   * **Dvi kameros**: Rankiniu būdu priskirkite kiekvieną kamerą tinkamam kontaktui

**Ekspozicijos kontaktų priskyrimas:**

* **Ekspozicijos kontaktas 1**: Išskleidžiamajame meniu pasirinkite kameros modelį
* **Ekspozicijos kontaktas 2**: Pasirinkite antrą kamerą arba „Nenaudoti“
* Ta pati kamera negali būti priskirta abiem kontaktams

{% hint style=&quot;warning&quot; %}
**Svarbu**: Ekspozicijos kaiščiai turi būti teisingai priskirti atitinkamoms kameroms. Neteisingas priskyrimas lems neteisingus geografinės vietos duomenis.
{% endhint %}

***

## Išplėstiniai scenarijai

### Daugiakameriniai projektai

Apdorojant vaizdus iš kelių MAPIR kamerų viename projekte:

1. Chloros automatiškai aptinka kiekvieną kameros modelį
2. Kiekvienai kamerai priskiriamas atitinkamas apdorojimo profilis
3. PPK: rankiniu būdu priskirkite kiekvienai kamerai tinkamą ekspozicijos kontaktą
4. Visos kameros naudoja tą patį eksporto formatą ir indeksus

**Pavyzdys**: Survey3W RGN + Survey3N OCN dviguba kamera

### Laiko tarpo arba kelių datų tyrimai

Pakartotiniams to paties ploto tyrimams laikui bėgant:

1. Sukurkite šabloną su standartiniais nustatymais.
2. Kiekvieną sesiją naudokite nuoseklią kalibravimo tikslo konfigūraciją.
3. Kiekvieną datą apdorokite kaip atskirą projektą.
4. Naudokite identiškus nustatymus, kad gautumėte palyginamus rezultatus.
5. Eksportuokite tuo pačiu formatu laiko analizei.

### Didelės duomenų bazės

Projektams su daug vaizdų (500+):

* Apsvarstykite galimybę suskirstyti į mažesnius projektus pagal datą arba plotą.
* Naudokite Chloros+ lygiagretų apdorojimą, kad gautumėte greitesnius rezultatus.
* Apsvarstykite CLI arba API galimybę, jei norite automatizuoti partijas.
* Nustatykite minimalų pakalibravimo intervalą, kad sutrumpintumėte tikslo aptikimo laiką.

***

## Nustatymų tikrinimas

Prieš pradėdami apdoroti, peržiūrėkite šiuos pagrindinius nustatymus:

* [ ] Kameros modelis teisingai aptiktas failų naršyklėje
* [ ] Įjungta vinjetės korekcija
* [ ] Įjungtas atspindžio kalibravimas
* [ ] Importuotas bent vienas kalibravimo tikslo vaizdas
* [ ] Pridėti norimi multispektriniai indeksai
* [ ] Eksporto formatas, tinkamas jūsų darbo eigai
* [ ] Konfigūruoti PPK nustatymai (jei naudojate .daq su ekspozicijos įvykiais)

***

## Tolimesni veiksmai

Kai nustatymai sukonfigūruoti:

1. **Pažymėkite kalibravimo tikslo vaizdus** – žr. [Tikslo vaizdų pasirinkimas](choosing-target-images.md)
2. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)
3. **Stebėkite pažangą** – žr. [Apdorojimo stebėjimas](monitoring-the-processing.md)

Išsamią informaciją apie visus galimus nustatymus rasite [Projekto nustatymai](../project-settings/project-settings.md) informacinėje dokumentacijoje.
