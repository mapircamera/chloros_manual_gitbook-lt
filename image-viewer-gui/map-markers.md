# Žemėlapio žymekliai

Skirtuke „Žemėlapis“ jūsų nuotraukos pagal jų GPS koordinates atvaizduojamos interaktyviame 2D žemėlapyje. Tai suteikia geografinę fotografavimo sesijos apžvalgą ir yra greičiausias būdas, iškart po importavimo, pašalinti nuotraukas, kurių nenorite apdoroti.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Kaip patekti į skirtuką „Žemėlapis“

1. Atidarykite arba sukurkite projektą Chloros
2. Importuokite nuotraukas, kuriose yra GPS metaduomenų
3. Kairiajame šoniniame meniu spustelėkite skirtuką **„Žemėlapis“** <img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line">
4. Žemėlapyje kiekvienos nuotraukos GPS vietoje bus rodomas žymeklis

{% hint style="info" %}
**Reikalingas GPS**: žemėlapyje rodomi tik tie vaizdai, kurių EXIF metaduomenyse yra GPS koordinatės. Vaizdas be koordinatų vis tiek lieka projekte ir apdorojamas įprastai — tiesiog neturi žymės.
{% endhint %}

***

## Nuotraukų redagavimas skirtuke „Žemėlapis“

Skirtuke **„Žemėlapis“**<img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line"> yra tie patys mygtukai „Pridėti“ <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> ir „Pašalinti“ <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">, kaip ir skirtuke [**„Failų naršyklė“**](../processing-images-gui/adding-files-to-a-project.md) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">. Jame rodomas tas pats projekto failų sąrašas su geografinėmis stulpeliais:

| Stulpelis        | Turinys                                                           |
| ------------- | ------------------------------------------------------------------ |
| **Pavadinimas** | Failo pavadinimas, koks jis buvo išimtas iš fotoaparato                             |
| **Platuma**  | Dešimtainiai laipsniai, šeši skaičiai po kablelio                                |
| **Ilguma** | Dešimtainiai laipsniai, šeši skaičiai po kablelio                                |
| **Aukštis**  | Metrai, vienas skaičius po kablelio — `-`, jei nuotraukoje nėra aukščio duomenų |

{% hint style="info" %}
Spustelėkite bet kurį stulpelio antraštę, kad surūšiuotumėte pagal ją; spustelėkite dar kartą, kad pakeistumėte tvarką.
{% endhint %}

{% hint style="warning" %}
**Aukštis yra aukštis virš jūros lygio, o ne virš žemės paviršiaus.** Ši vertė gaunama iš vaizdo EXIF `GPSAltitude` žymės, kuri nurodo vidutinį jūros lygį. Tai nėra skrydžio aukštis virš reljefo, todėl Chloros iš jo neapskaičiuos žemės paviršiaus atvaizdo atstumo — virš lauko, esančio 300 m virš jūros lygio, dronas, esantis 100 m AGL aukštyje, čia užfiksuos maždaug 400 m. Naudokite šį stulpelį, kad nustatytumėte išskirtinius atvejus ir patvirtintumėte nuoseklų skrydžio aukštį, o ne kaip AGL matavimą.
{% endhint %}

***

## Vaizdų žymekliai

Kiekvienas vaizdas su GPS duomenimis gauna žymeklį jo koordinatėse.

### Žymių rodymas

* Žymės yra tiksliose kiekvieno kadro užfiksuotose koordinatėse
* Artimai viena kitos esančios žymės, sumažinus vaizdą, gali vizualiai persidengti — padidinkite vaizdą, kad jas atskirtumėte
* Pasirinktos ir paryškintos žymės rodomos virš kitų

### Peržiūra, užvedus pelę

* **Užveskite pelę** ant bet kurio žymeklio, kad atsirastų to vaizdo miniatiūra su jo failo pavadinimu
* **Spustelėkite**žymę, kad pažymėtumėte nuotrauką ir**prisegtumėte** iškylančią langą — jis išliks, kol spustelėsite kitur. Kol langas yra prisegtas, užvedus pelę ant kitų žymių jis nebus paslėptas
* Tai greitas būdas rasti vieną konkretų kadrą didelėje sesijoje neišeinant iš žemėlapio

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption><p>Skirtuke „Žemėlapis“ pavaizduojami visi projekte esantys vaizdai su geografinėmis žymėmis</p></figcaption></figure>### Super-priartinimas

{% hint style="success" %}
**SUPER-PRIARTINIMAS**: kai pasiekiate didžiausią priartinimą, kuriam plytelių teikėjas turi vaizdus, tolesnis priartinimas ne sustos, o padidins plyteles, todėl galėsite atskirti žymes, kurios yra beveik viena ant kitos.
{% endhint %}

* Super-zoom įsijungia tik tada, kai esate **pasiekę** tiekėjo nustatytą maksimalų priartinimą tai vietovei ir plytelės jau yra visiškai įkeltos. Jei priartinimas mažesnis, jis veikia įprastai
* Priartinimo diapazonas yra nuo **1× iki 32×** virš tiekėjo nustatyto maksimumo
* Kampelyje esantis indikatorius rodo dabartinį „super-zoom“ lygį procentais, o šalia jo esantis mygtukas **×** vienu paspaudimu grąžina jus į įprastą priartinimą
* Atitolinant vaizdas visada grįžta į patį žemėlapį, todėl niekada negalite įstrigti „super-zoom“ režime
* Mastelio keitimas ir peržiūros slinktis esant super-masteliui perkelia gautą poslinkį atgal į žemėlapį, todėl į ne centrinę sritį, į kurią persikėlėte, toliau prašoma plytelių, o ne atsiranda tuščia vieta
* Žymekliai piešiami kaip vektoriniai elementai, o ne rasterizuojami, todėl jie išlieka ryškūs bet kuriame super-mastelio lygyje

***

## Žemėlapio plytelių teikėjai

{% hint style="success" %}
**Automatinis pasirinkimas**: Chloros pasirenka plytelių paslaugą, kuri siūlo geriausią priartinimo lygį nepriklausomai nuo jūsų vaizdų buvimo vietos. Bet kuriuo metu galite perjungti rankiniu būdu.
{% endhint %}

| Tiekėjas        | Pastabos                                                                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **„Google Maps“** | Platus pasaulinis aprėptis; palaiko visus keturis plytelių tipus                                                                                                            |
| **„Esri ArcGIS“**| Tam tikruose regionuose dažnai siūlo didesnės skiriamosios gebos aerofotografijas. „Esri“ nepasiūlo**„Terrain“** plytelių tipo, todėl, kai pasirinkta „Esri“, šio tipo mygtukas yra neaktyvus |***

## Žemėlapio plytelių tipai

Pasirinkite žemėlapio sluoksnio tipą naudodami mygtukus (iš kairės į dešinę):

![](&lt;../.gitbook/assets/image (14).png&gt;)

| Tipas                 | Rodo                                                                |
| -------------------- | -------------------------------------------------------------------- |
| **Reljefas**          | Aukščio šešėliavimas su žemėlapio detalėmis (keliai, pavadinimai). Tik „Google“       |
| **Žemėlapis**              | Standartiniai gatvių žemėlapio plytelės — mažiausio pralaidumo variantas              |
| **Palydovinis**        | Išsamūs palydoviniai vaizdai be užrašų — variantas, reikalaujantis didžiausio duomenų srauto |
| **Hibridinis** (numatyta) | Palydoviniai vaizdai su ant jų nubrėžtais keliais ir užrašais                |

Atidarius skirtuką „Žemėlapis“, pasirenkamas variantas **Hibridinis**. Jūsų pasirinkimas taikomas ir keičiant paslaugų teikėją, jei šis tai palaiko.***

## Navigacija žemėlapyje

* **Mastelio keitimas**: pelės ratukas arba mastelio keitimo mygtukai žemėlapyje
* **Perkėlimas**: spustelėkite ir vilkite
* **Visas ekranas**: visą ekraną užimantis valdiklis išplečia žemėlapį į visą langą***

## Naudojimo atvejai

### Skrydžio maršruto peržiūra

* Vienu žvilgsniu peržiūrėkite drono sesijos aprėpties zoną
* Nustatykite spragas, kur buvo praleistas skrydis
* Patikrinkite, ar skrydis vyko pagal suplanuotą maršrutą

### Antžeminės apžvalgos peržiūra

* Peržiūrėkite, kaip pasiskirstę antžeminiai vaizdai
* Nustatykite kalibravimo taškų rėmelius atsižvelgiant į apžvalgos plotą
* Nuspręskite, kur reikia papildomų vaizdų

### Kokybės kontrolė

* Raskite nuotraukas, užfiksuotas netikėtoje vietoje, ir pašalinkite jas prieš apdorojimą
* Rūšiuokite pagal aukštį, kad nustatytumėte kadrą, užfiksuotą netinkamame aukštyje, arba tokį, kuriame GPS signalas buvo silpnas
* Palyginkite nuotraukų vietas su lauko užrašais

***

## Problemų sprendimas

### Nerodomi žymekliai

**Galimos priežastys**

* Nuotraukose nėra GPS metaduomenų
* Fotografavimo metu fotoaparate buvo išjungtas GPS
* EXIF duomenys buvo pašalinti kitos programinės įrangos prieš importuojant

**Ką daryti**: patikrinkite, ar fotoaparate įjungtas GPS, ir iš naujo importuokite originalius failus. Galite patikrinti, ar konkretus failas turi koordinates, ieškant jo skirtuke „Žemėlapis“ esančioje failų lentelėje — nuotraukai be koordinatų ten nėra eilutės.

### Žymekliai yra netinkamoje vietoje

**Galimos priežastys**: prastas palydovinis signalas fotografavimo metu arba GPS nukrypimas sesijos metu.**Ką daryti**: tai yra fotografavimo metu kilusi problema, o ne kažkas, ką Chloros galėtų ištaisyti po fakto. Norėdami užtikrinti tikslumą, naudokite PPK/RTK GPS darbo eigą – žr. nustatymą**Taikyti PPK pataisas** [Projekto nustatymuose](../project-settings/project-settings.md).

### Žemėlapis tuščias arba plytelės nebeįkeliamos

Plytelių teikėjai yra internetinės paslaugos. Jei plytelės nustoja atsisiųsti, patikrinkite kompiuterio tinklo ryšį, tada pabandykite pakeisti teikėją. Jei buvote labai priartinę vaizdą, paspauskite **×** atstatymo mygtuką, kad grįžtumėte į įprastą priartinimo lygį, ir leiskite žemėlapiui iš naujo paprašyti plytelių.***

## Susiję puslapiai

* [**Vaizdų tinklelis**](image-grid.md) — tas pats vaizdų rinkinys, kaip ir miniatiūros
* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) — vieno vaizdo peržiūra išsamiai
* [**Failų pridėjimas prie projekto**](../processing-images-gui/adding-files-to-a-project.md) — šioje kortelėje esantys mygtukai, skirti failams pridėti ir pašalinti
