# Failų pridėjimas į projektą

Sukūrę arba atidarę projektą Chloros, kitas žingsnis – pridėti daugiaspektrinius vaizdus, kad būtų galima pradėti apdorojimą. Naudodami skirtuką „Failų naršyklė“<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> skirtukas leidžia lengvai importuoti vaizdus ir tvarkyti duomenų rinkinį.

## Prieiga prie failų naršyklės

1. Atidarykite arba sukurkite projektą Chloros
2. Spustelėkite **Failų naršyklė** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> piktogramą kairėje šoninėje juostoje
3. Failų naršyklės skydelyje bus rodomas jūsų projekto failų sąrašas

{% hint style="info" %}
**Palaikomi failų tipai**: Chloros palaiko RAW+JPG ir JPG vaizdų failus iš MAPIR, Survey3W ir Survey3N fotoaparatų. Rekomenduojama naudoti tik RAW+JPG.
{% endhint %}

***

## Vaizdų pridėjimas į projektą

Yra du pagrindiniai būdai pridėti vaizdus į projektą:

### 1 metodas: Pridėti failus

Naudokite šią parinktį, norėdami importuoti atskirus vaizdo failus arba nedidelį failų rinkinį.

1. Spustelėkite mygtuką **„Pridėti failus“** <img src="../.gitbook/assets/image.png" alt="" data-size="line"> mygtuką viršuje failų naršyklės skydelyje
2. Pereikite į aplanką, kuriame yra jūsų vaizdai
3. Pasirinkite vieną ar daugiau vaizdo failų (laikydami nuspaudę **Ctrl** klavišą, galite pasirinkti kelis failus)
4. Spustelėkite **„Atidaryti“**, kad importuotumėte pasirinktus failus

### 2 metodas: Aplanko pridėjimas

Naudokite šią parinktį, jei norite vienu metu importuoti visus vaizdus iš aplanko.

1. Spustelėkite mygtuką **„Pridėti aplanką“** <img src="../.gitbook/assets/image (1).png" alt="" data-size="line"> viršuje esančią mygtuką
2. Pereikite į aplanką, kuriame yra jūsų fotografavimo sesijos vaizdai, ir jį pasirinkite
3. Spustelėkite **„Pasirinkti aplanką“**, kad importuotumėte visus palaikomus vaizdus iš to aplanko***

## Failų naršyklės lentelės supratimas

Kai vaizdai importuojami, jie rodomi lentelėje su šiomis skiltimis:

### Failo pavadinimas

* Originalus failo pavadinimas iš fotoaparato
* Išlaiko fotoaparato pavadinimų sudarymo taisykles (pvz., IMG\_0001.RAW)

### Laiko žyma

* Vaizdo užfiksavimo data ir laikas
* Išgauta iš vaizdo EXIF metaduomenų
* Naudojama PPK sinchronizavimui ir kalibravimo taško aptikimui

### Fotoaparato modelis

* Automatiškai nustatytas fotoaparato ir filtro konfigūracija
* Pavyzdžiai: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* Naudojama teisingiems apdorojimo profiliams taikyti

### Tikslo stulpelis (varnelė)

* Pažymėkite šį langelį, jei vaizduose yra kalibravimo taškai
* Tai žymiai pagreitina taškų aptikimą apdorojimo metu
* Daugiau informacijos rasite skyriuje [Tikslo vaizdų pasirinkimas](choosing-target-images.md)

### Vaizdo metaduomenų peržiūra

Paspaudus perjungimo mygtuką viršutiniame dešiniajame kampe virš lentelės, pasirinktų vaizdų metaduomenys bus rodomi vaizdų tinklelyje.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Failų tvarkymas projekte

### Failų pašalinimas

Norėdami pašalinti nepageidaujamus vaizdus iš savo projekto:

1. Pasirinkite vieną ar kelis vaizdus lentelėje „Failų naršyklė“
2. Spustelėkite mygtuką **„Pašalinti pasirinktus“** <img src="../.gitbook/assets/image (2).png" alt="" data-size="line"> mygtuką
3. Patvirtinkite pašalinimą (failai nėra ištrinami iš disko, tik pašalinami iš projekto)

### Rūšiavimas ir filtravimas

* **Rūšiavimas pagal stulpelį**: Spustelėkite bet kurį stulpelio antraštę, kad surūšiuotumėte vaizdus
* **Rūšiavimas pagal laiko žymą**: Naudinga chronologinėms fotografijų sekcijoms tvarkyti
* **Fotoaparato modelio filtras**: Sugrupuokite vaizdus pagal fotoaparato tipą, jei naudojate kelis fotoaparatus***

## Vaizdo peržiūra

### Visas vaizdas

Spustelėkite bet kurią vaizdo miniatiūrą failų naršyklėje, kad ji būtų rodoma pagrindinėje peržiūros srityje:

1. Vaizdas pasirodo centrinėje peržiūros srityje
2. Naudokite mastelio valdymo elementus, kad apžiūrėtumėte vaizdo detales
3. Pereikite tarp vaizdų naudodami rodyklių klavišus

### Greita navigacija

* **Ankstesnis vaizdas**: Spustelėkite kairę rodyklę arba paspauskite ← klavišą
* **Kitas vaizdas**: Spustelėkite dešinę rodyklę arba paspauskite klavišą →
* **Padidinimas/sumažinimas**: Naudokite pelės ratuką arba mastelio keitimo mygtukus
* **Perkėlimas**: Spustelėkite ir vilkite vaizdą, kai jis yra padidintas***

## Duplikuotų failų tvarkymas

Chloros automatiškai aptinka ir ignoruoja duplikuotus failus:

* Failai su identiškais pavadinimais yra praleidžiami
* Apsaugo nuo netyčinio dvigubo apdorojimo
* Aptikus dubliatus, rodomas įspėjamasis pranešimas

{% hint style="warning" %}
**Svarbu**: Prieš importuojant nepakeiskite ir nemodifikuokite originalių vaizdo failų. Chloros tinkamam apdorojimui naudoja originalius failų pavadinimus ir metaduomenis.
{% endhint %}

***

## Mišrūs kamerų duomenų rinkiniai

Jei jūsų projekte yra vaizdų iš kelių MAPIR kamerų:

1. Chloros automatiškai aptinka kiekvieną kameros modelį
2. Kiekvienas kameros tipas apdorojamas naudojant atitinkamą kalibravimo profilį
3. Failų naršyklė rodo kameros modelį stulpelyje „Kameros modelis“
4. Apdorojant kiekvienam kameros tipui taikomi teisingi nustatymai

**Pavyzdinis scenarijus**: Survey3W RGN + Survey3N OCN dviejų kamerų konfigūracija***

## Geriausia praktika

### Tvarkykite prieš importuojant

* Laikykite kalibravimo taikinio vaizdus toje pačioje aplankoje kaip ir tyrimo vaizdus
* Išsaugokite originalią kameros / SD kortelės aplankų struktūrą
* Nevienoje projekto dalyje nemaišykite skirtingų sesijų duomenų rinkinių

### Failų pavadinimai

* Išsaugokite originalius kameros failų pavadinimus (IMG\_0001.RAW ir pan.)
* Nepervardinkite failų prieš importuojant
* Originaliuose pavadinimuose yra svarbių metaduomenų

### Kalibravimo taikinio vaizdai

* Visada įtraukite 1–2 kalibravimo taikinio vaizdus per sesiją
* Užfiksuokite taikinius prieš ir po fotografavimo sesijos
* Pastatykite taikinius tokiomis pačiomis apšvietimo sąlygomis kaip ir fotografavimo zona
* Pažymėkite taikinio vaizdus naudodami žymės langelį „Target“, kad pagreitintumėte apdorojimą

***

## Dažnos problemos ir sprendimai

### Vaizdai neatsiranda po importavimo

**Galimos priežastys:**

* Nepalaikomas failo formatas (tik RAW+JPG ir JPG iš MAPIR fotoaparatų)
* Vaizdai yra iš ne MAPIR fotoaparatų (žr. [Palaikomi fotoaparatai](../supported-cameras.md))
* Sugadintas failas arba nepilnas perkėlimas iš SD kortelės

**Sprendimas**: Patikrinkite failo formato ir fotoaparato modelio suderinamumą

### Neaptiktas fotoaparato modelis

**Galimos priežastys:**

* Pakeisti EXIF metaduomenys
* Nuotraukos redaguotos išorinėje programinėje įrangoje
* Nepilnas failų perkėlimas

**Sprendimas**: Pakartotinai importuokite originalius, nepakeistus failus iš fotoaparato/SD kortelės

### Trūksta laiko žymų

**Galimos priežastys:**

* Netinkamai nustatytas fotoaparato laikrodis
* Išorinė programinė įranga pašalino EXIF duomenis

**Sprendimas**: Patikrinkite, ar fotografavimo metu fotoaparato laiko nustatymai buvo teisingi***

## Tolimesni veiksmai

Kai failai bus importuoti:

1. **Peržiūrėkite failų sąrašą** – įsitikinkite, kad visi vaizdai įkelti teisingai
2. **Patikrinkite fotoaparatų modelius** – įsitikinkite, kad fotoaparatai buvo teisingai atpažinti
3. **Pažymėkite tikslinius vaizdus** – žr. [Tikslinių vaizdų pasirinkimas](choosing-target-images.md)
4. **Nustatykite parametrus** – sukonfigūruokite apdorojimo parinktis [Projekto nustatymuose](adjusting-project-settings.md)
5. **Pradėkite apdorojimą** – žr. [Apdorojimo pradžia](starting-the-processing.md)

Išsamią informaciją apie projekto konfigūraciją rasite skyriuje [Projekto nustatymų koregavimas](adjusting-project-settings.md).
