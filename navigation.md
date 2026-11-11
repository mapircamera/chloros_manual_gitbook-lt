# Vartotojo sąsaja: Navigacija

Pirmą kartą paleidus Chloros, paleidžiamas jo apdorojimo foninis procesas. Kai foninis procesas pasirengęs, kairiajame viršutiniame kampe pasirodo pagrindinio meniu piktograma <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">, o kairiajame šoniniame meniu atsiranda skirtukai „Kameros“ ir „Šviesos jutikliai“ (iki tol jie yra užblokuoti).

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Viršutinėje antraštėje iš kairės į dešinę yra:

### <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> Pagrindinis meniu

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>Iš pagrindinio meniu galite:

* **Naujas projektas**— sukurti naują projektą. Jei turite išsaugotų projekto šablonų, atsiranda išskleidžiamas meniu**Pasirinkti šabloną**, kad naujas projektas būtų pradėtas pagal šablono nustatymus.
* **Atidaryti projektą**— atidaryti esamą projektą. Sąraše yra mygtukas**Atidaryti projekto aplanką**, kuris atidaro projektų aplanką jūsų failų naršyklėje.
* **Kopijuoti projektą** — nukopijuokite šiuo metu atidarytą projektą su nauju pavadinimu (siūlomas laisvas pavadinimas, pvz., „MyProject (2)“) ir atidarykite kopiją. _(matoma atidarius projektą)_
* **Pridėti failus** — pridėti atskirus vaizdo failus į esamą projektą _(matoma atidarius projektą)_
* **Pridėti aplanką** — pridėti vieną ar daugiau vaizdų aplankų į esamą projektą _(matoma atidarius projektą)_
* **Pradėti apdorojimą / Sustabdyti apdorojimą** — pradėti arba sustabdyti vaizdų apdorojimo procesą _(įjungiama po failų pridėjimo)_
* **Prijungti prie kameros** — pereiti į [skirtuką „Kameros“](lattice/), kad prijungtumėte „LATTICE“ kamerą arba matricos sistemą. Veikia ir neatidarius projekto.
* **Prisijungti prie šviesos jutiklio** — pereiti į [Šviesos jutikliai skirtuką](daq/), kad prijungtumėte DAQ šviesos jutiklį. Veikia ir neatidarius projekto.

{% hint style="info" %}
**Tik Windows**: Chloros darbalaukio grafinė vartotojo sąsaja (GUI) prieinama Windows sistemoje. Linux vartotojai, norėdami atlikti apdorojimą be grafinės sąsajos, turėtų susipažinti su [CLI](CLI.md) ir [Python SDK](api-python-sdk.md) dokumentacija.
{% endhint %}

### <img src=".gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">

Mygtukas „Paleisti“ / „Pradėti“

Kai šis mygtukas įjungtas, mygtukas „Pradėti apdorojimą“ paleidžia vaizdo apdorojimo procesą.

### <img src=".gitbook/assets/image (4).png" alt="" data-size="line">

Vykdymo juosta <img src=".gitbook/assets/image (5).png" alt="" data-size="line">



Nemokamame Chloros režime, kuriame visi failai apdorojami paeiliui, vykdymo juosta rodo 2 etapus: objekto aptikimą ir apdorojimą.

Mokamame „Chloros+“ licencijuotame režime, kuriame visi failai apdorojami vienu metu, vykdymo juosta rodo 4 etapus: aptikimą, analizę, kalibravimą ir eksportavimą. Jei užvesite pelės žymeklį ant „Chloros+“ pažangos juostos, atsivers išplėstinis 4 etapų pažangos juostos skydelis, kad galėtumėte stebėti procesą. Spustelėjus viršutinę pažangos juostą, išskleidžiamas skydelis bus užfiksuotas, o spustelėjus dar kartą – atblokuotas.

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

## Šoninis meniu

Kairiajame šoniniame meniu yra įvairios piktogramos, su kuriomis galima sąveikauti, išdėstytos tokia tvarka iš viršaus į apačią:

#### <img src=".gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> [Projekto nustatymai](project-settings/project-settings.md)

Skirtuke „Projekto nustatymai“ galite koreguoti bendruosius projekto ir apdorojimo nustatymus. Nustatykite juos prieš pradėdami apdoroti failus.

#### <img src=".gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> Failų naršyklė

Pridėkite failus ir aplankus bei pašalinkite failus iš projekto. Dubliuojami failai ignoruojami. Pažymėkite langelį prie bet kurio tikslinio vaizdo stulpelyje, ir apdorojimo metu bus tikrinami tik pažymėti vaizdai, o tai žymiai pagreitins apdorojimo laiką. Naudokite perjungiklį „Vaizdas/Metaduomenys“, kad perjungtumėte tarp pasirinktų vaizdų miniatiūrų tinklelio peržiūros ir išsamios metaduomenų lentelės.

#### <img src=".gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> [Vaizdų peržiūros programa](image-viewer-gui/opening-an-image-full-screen.md)

Kai pagrindiniame vaizdų peržiūros lange paspaudžiate ant vaizdo, jis atidaromas visame ekrane skirtuke „Vaizdų peržiūros programa“.

#### <img src=".gitbook/assets/image (3) (1).png" alt="" data-size="line"> [Žemėlapių peržiūros programa](image-viewer-gui/map-markers.md)

Peržiūrėkite savo vaizdus interaktyviame 2D žemėlapyje pagal jų GPS koordinates. Palaiko „Google Maps“ ir ESRI plytelių teikėjus, automatiškai parinkdamas jūsų vietovei tinkamiausią paslaugą. Užveskite pelę ant žymių, kad pamatytumėte nuotraukų miniatiūrų peržiūrą.

#### <img src=".gitbook/assets/image (17).png" alt="" data-size="line"> [Kameros](lattice/)

Prijunkite ir valdykite „LATTICE“ kameras tiesiogiai — po vieną arba kaip sinchronizuotus kelių kamerų rinkinius. Šiame skirtuke rodomos tiesioginio peržiūros plytelės su perdangomis ir histogramomis, nustatymai kiekvienai kamerai ir kiekvienam masyvui, taip pat įrašymo nustatymai, kuriais pasirenkama, kurias kameras ir eksporto tipus naudoja funkcija „Įrašyti viską“. Prieinama, kai serverinė dalis bus paruošta; išsamų vadovą rasite [„LATTICE“ skyriuje](lattice/).

#### <img src=".gitbook/assets/image (23).png" alt="" data-size="line"> [Šviesos jutikliai](daq/)

Prijunkite DAQ šviesos jutiklius — DAQ-U (USB), DAQ-M (Bluetooth) ir DAQ-E (Ethernet) — ir peržiūrėkite jų kalibruotus spektro grafikus realiuoju laiku, išreikštus W/m²/nm. Čia galite įrašyti `.daq` failus į atidarytą projektą, pervardyti jutiklius, pasirinkti ribų koregavimo profilius ir atnaujinti DAQ-E programinę įrangą. Funkcija bus prieinama, kai bus parengta serverio infrastruktūra; išsamų aprašymą rasite [DAQ skyriuje](daq/).

#### <img src=".gitbook/assets/icon_log.JPG" alt="" data-size="line"> Debugavimo žurnalas

Jei kyla problemų, peržiūrėkite žurnalą, kuriame pateikiami debugavimo įrašai. Nukopijuokite arba atsisiųskite žurnalą ir nusiųskite jį [MAPIR techniniam palaikymui](https://www.mapir.camera/community/contact), kad gautumėte pagalbą.

Nauja 1.3.0 versijoje: klaidų piktograma žurnalo skirtuko viršuje (**Pranešti apie klaidą**) siunčia MAPIR ataskaitą su jūsų aprašymu, iki 10 vaizdų, Chloros žurnalais ir sistemos duomenimis. Norėdami ją išsiųsti, turite būti prisijungę. Žr. [Klaidos pranešimas iš žurnalo skirtuko](processing-images-gui/monitoring-the-processing.md#reporting-a-bug-from-the-log-tab).

#### <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> [Vartotojo prisijungimas](chloros+-login.md)

Šoninė juosta „Vartotojo prisijungimas“ leidžia jums prisijungti prie savo „Chloros+“ paskyros, kad galėtumėte naudotis išplėstinėmis funkcijomis. Taip pat galite peržiūrėti dabartinę programos versiją bei nustatyti Chloros grafinės vartotojo sąsajos ir CLI rodomo teksto kalbą.
