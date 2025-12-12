# Failų pridėjimas prie projekto

Sukūrę arba atidarę projektą Chloros, kitas žingsnis yra pridėti daugiaspektrinius vaizdus, kad galėtumėte pradėti apdorojimą. Failų naršyklė<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> skirtukas leidžia lengvai importuoti vaizdus ir tvarkyti duomenų rinkinį.

## Prieiga prie failų naršyklės

1. Atidarykite arba sukurkite projektą Chloros
2. Spustelėkite **Failų naršyklė** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> piktogramą kairėje šoninėje juostoje
3. Failų naršyklės skydelyje bus rodomas jūsų projekto failų sąrašas

{% patarimas style=&quot;info&quot; %}
**Palaikomi failų tipai**: Chloros palaiko RAW+JPG ir JPG vaizdo failus iš MAPIR Survey3W ir Survey3N fotoaparatų. Rekomenduojama naudoti tik RAW+JPG.
{% endhint %}

***

## Vaizdų pridėjimas prie projekto

Yra du pagrindiniai būdai, kaip pridėti vaizdus prie projekto:

### 1 metodas: failų pridėjimas

Naudokite šią parinktį, jei norite importuoti atskirus vaizdo failus arba nedidelį failų rinkinį.

1. Spustelėkite mygtuką **„Pridėti failus“** viršuje failų naršyklės skydelyje.
2. Pereikite į aplanką, kuriame yra jūsų vaizdai.
3. Pasirinkite vieną ar kelis vaizdo failus (norėdami pasirinkti kelis failus, laikykite nuspaudę klavišą **Ctrl**).
4. Spustelėkite **„Atidaryti“**, kad importuotumėte pasirinktus failus.

### 2 metodas: aplanko pridėjimas

Naudokite šią parinktį, jei norite importuoti visus vaizdus iš aplanko iš karto.

1. Spustelėkite mygtuką **„Pridėti aplanką“** failų naršyklės skydelio viršuje.
2. Pereikite prie aplanko, kuriame yra jūsų fotografavimo sesijos vaizdai, ir jį pasirinkite.
3. Spustelėkite **„Pasirinkti aplanką“**, kad importuotumėte visus palaikomus vaizdus iš to aplanko.

***

## Failų naršyklės lentelės supratimas

Kai vaizdai importuojami, jie rodomi lentelėje su šiomis stulpeliais:

### Miniatiūra

* Mažas kiekvieno vaizdo peržiūros langelis.
* Spustelėkite miniatiūrą, kad pagrindiniame peržiūros lange būtų rodomas visas vaizdas.

### Failo pavadinimas

* Originalus failo pavadinimas iš fotoaparato.
* Laikosi fotoaparato pavadinimų suteikimo taisyklių (pvz., IMG\_0001.RAW).

### Laiko žyma

* Vaizdo užfiksavimo data ir laikas.
* Išgauta iš vaizdo EXIF metaduomenų.
* Naudojamas PPK sinchronizavimui ir kalibravimo tikslo aptikimui

### Fotoaparato modelis

* Automatiškai aptiktas fotoaparato ir filtro konfigūracija
* Pavyzdžiai: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* Naudojamas teisingiems apdorojimo profiliams taikyti

### Tikslo stulpelis (žymės langelis)

* Pažymėkite šį langelį, jei vaizduose yra kalibravimo tikslai
* Žymiai pagreitina tikslo aptikimą apdorojimo metu
* Daugiau informacijos rasite skyriuje [Tikslo vaizdų pasirinkimas](choosing-target-images.md)

***

## Failų tvarkymas projekte

### Failų šalinimas

Norėdami pašalinti nepageidaujamus vaizdus iš savo projekto:

1. Pasirinkite vieną ar kelis vaizdus failų naršyklės lentelėje
2. Spustelėkite mygtuką **„Pašalinti pasirinktus“**
3. Patvirtinkite pašalinimą (failai nėra ištrinami iš disko, tik pašalinami iš projekto)

### Rūšiavimas ir filtravimas

* **Rūšiuoti pagal stulpelį**: spustelėkite bet kurį stulpelio antraštę, kad surūšiuotumėte vaizdus
* **Rūšiuoti pagal laiko žymą**: naudinga chronologinei fotografavimo sekai tvarkyti.
* **Fotoaparato modelio filtras**: jei naudojate kelis fotoaparatus, sugrupuokite vaizdus pagal fotoaparato tipą.

***

## Vaizdo peržiūra

### Visas vaizdas

Spustelėkite bet kurį vaizdo miniatiūrą failų naršyklėje, kad jis būtų rodomas pagrindiniame peržiūros lange:

1. Vaizdas rodomas peržiūros lango centre.
2. Naudokite mastelio reguliavimo mygtukus, kad peržiūrėtumėte vaizdo detales.
3. Naršykite tarp vaizdų naudodami rodyklių klavišus

### Greitas naršymas

* **Ankstesnis vaizdas**: spustelėkite kairę rodyklę arba paspauskite klavišą ←
* **Kitas vaizdas**: spustelėkite dešinę rodyklę arba paspauskite klavišą →
* **Padidinti/sumažinti**: naudokite pelės ratuką arba mastelio mygtukus
* **Perkelti**: padidintą vaizdą spustelėkite ir vilkite

***

## Dvigubų failų tvarkymas

Chloros automatiškai aptinka ir ignoruoja dubliuojamus failus:

* Failai su identiškais pavadinimais yra praleidžiami
* Apsaugo nuo atsitiktinio dvigubo apdorojimo
* Aptikus dubliuojamus failus, rodomas įspėjamasis pranešimas

{% hint style=&quot;warning&quot; %}
**Svarbu**: prieš importuodami nepakeiskite ir nemodifikuokite originalių vaizdo failų. Chloros tinkamam apdorojimui naudoja originalius failų pavadinimus ir metaduomenis.
{% endhint %}

***

## Mišrios kamerų duomenų rinkmenos

Jei jūsų projekte yra vaizdai iš kelių MAPIR kamerų:

1. Chloros automatiškai aptinka kiekvieną kameros modelį.
2. Kiekvienas kameros tipas apdorojamas naudojant atitinkamą kalibravimo profilį.
3. Failų naršyklė rodo kameros modelį stulpelyje „Kameros modelis“.
4. Apdorojant taikomi teisingi nustatymai kiekvienam kameros tipui.

**Pavyzdinis scenarijus**: Survey3W RGN + Survey3N OCN dvigubos kameros konfigūracija.

***

## Geriausia praktika

### Tvarkykite prieš importuodami

* Kalibravimo tikslinius vaizdus laikykite toje pačioje aplankoje kaip ir tyrimo vaizdus
* Išsaugokite originalią aplanko struktūrą iš savo kameros/SD kortelės
* Viename projekte nemaišykite duomenų rinkinių iš skirtingų sesijų

### Failų pavadinimai

* Išsaugokite originalius kameros failų pavadinimus (IMG\_0001.RAW ir pan.)
* Nepervardykite failų prieš importavimą
* Originaliuose pavadinimuose yra svarbių metaduomenų

### Kalibravimo tiksliniai vaizdai

* Visada įtraukite 1–2 kalibravimo tikslinius vaizdus per sesiją.
* Užfiksuokite tikslinius vaizdus prieš ir po užfiksavimo sesijos.
* Pastatykite tikslinius vaizdus tokiomis pačiomis apšvietimo sąlygomis kaip užfiksavimo srityje.
* Pažymėkite tikslinius vaizdus naudodami žymės langelį „Tikslas“, kad pagreitintumėte apdorojimą.

***

## Dažnos problemos ir sprendimai

### Vaizdai neatsiranda po importavimo

**Galimos priežastys:**

* Nepalaikomas failo formatas (tik RAW+JPG ir JPG iš MAPIR fotoaparatų)
* Vaizdai yra iš ne MAPIR fotoaparatų (žr. [Palaikomi fotoaparatai](../supported-cameras.md))
* Failo sugadinimas arba neišsamus perkėlimas iš SD kortelės

**Sprendimas**: Patikrinkite failo formatą ir fotoaparato modelio suderinamumą.

### Fotoaparato modelis neaptiktas

**Galimos priežastys:**

* Modifikuoti EXIF metaduomenys.
* Vaizdai redaguoti išorinėje programinėje įrangoje.
* Neužbaigtas failų perkėlimas.

**Sprendimas**: Pakartotinai importuokite originalius, nemodifikuotus failus iš fotoaparato/SD kortelės.

### Trūksta laiko žymų

**Galimos priežastys:**

* Netinkamai nustatytas fotoaparato laikrodis
* Išorinė programinė įranga pašalino EXIF duomenis

**Sprendimas**: Patikrinkite, ar fotoaparato laiko nustatymai buvo teisingi fotografavimo metu

***

## Tolimesni veiksmai

Kai failai bus importuoti:

1. **Peržiūrėkite failų sąrašą** – įsitikinkite, kad visi vaizdai įkelti teisingai
2. **Patikrinkite fotoaparato modelius** – patikrinkite, ar fotoaparatas nustatytas teisingai
3. **Pažymėkite tikslinį vaizdą** – žr. [Tikslinio vaizdo pasirinkimas](choosing-target-images.md)
4. **Nustatykite parametrus** – konfigūruokite apdorojimo parinktis [Projekto nustatymai](adjusting-project-settings.md)
5. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)

Išsami informacija apie projekto konfigūraciją pateikta skyriuje [Projekto nustatymų koregavimas](adjusting-project-settings.md).
