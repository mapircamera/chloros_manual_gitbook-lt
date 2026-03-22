# Vartotojo sąsaja: Navigacija

Pirmą kartą paleidus programas „Chloros“ ir „Chloros“ (naršyklė), bus paleistas jų foninis procesas. Kai jis bus paruoštas, kairėje viršuje pasirodys pagrindinio meniu piktograma <img src=".gitbook/assets/image (1) (1) (1).png" alt="" data-size="line"> .

<figure><img src=".gitbook/assets/header.JPG" alt=""><figcaption></figcaption></figure>

Viršutinėje antraštėje iš kairės į dešinę yra:

### <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> Pagrindinis meniu

<figure><img src=".gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Iš pagrindinio meniu galite:

* **Naujas projektas** — sukurti naują projektą
* **Atidaryti projektą** — atidaryti esamą projektą
* **Atidaryti projekto aplanką** — atidaryti projekto aplanką failų naršyklėje
* **Pridėti failus** — pridėti atskirus vaizdo failus į esamą projektą _(matoma po to, kai projektas yra atidarytas)_
* **Pridėti aplanką** — pridėti vaizdų aplanką prie esamo projekto _(matoma atidarius projektą)_
* **Pradėti apdorojimą / Sustabdyti apdorojimą** — pradėti arba sustabdyti vaizdų apdorojimo procesą _(įjungiama pridėjus failus)_

{% hint style="info" %}
**Tik Windows**: Chloros darbalaukio GUI yra prieinama Windows. Linux vartotojai turėtų matyti [CLI](CLI.md) ir [Python SDK](api-python-sdk.md) dokumentaciją apie apdorojimą be grafinės sąsajos.
{% endhint %}

### <img src=".gitbook/assets/image (2) (1).png" alt="" data-size="line"> Paleisti/Pradėti mygtukas

Kai įjungtas, apdorojimo pradžios mygtukas paleidžia vaizdo apdorojimo procesą.

### <img src=".gitbook/assets/image (4).png" alt="" data-size="line"> Pažangos juosta <img src=".gitbook/assets/image (5).png" alt="" data-size="line">Nemokamame Chloros režime, kuriame visi failai apdorojami paeiliui, pažangos juosta rodo 2 etapus: „Tikslo aptikimas“ ir „Apdorojimas“.

Mokamame Chloros+ licencijuotame režime, kuriame visi failai apdorojami vienu metu, pažangos juosta rodo 4 etapus: „Aptikimas“, „Analizė“, „Kalibravimas“, „Eksportavimas“. Jei užvesite pelės žymeklį ant Chloros+ pažangos juostos, atsivers išplėstinis 4 etapų pažangos juostos skydelis, kad galėtumėte stebėti procesą. Spustelėjus viršutinę pažangos juostą, išskleidžiamas skydelis bus užfiksuotas, o spustelėjus dar kartą – atblokuotas.

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

## Šoninis meniu

Kairiajame šoniniame meniu yra įvairios piktogramos, su kuriomis galite sąveikauti:

#### <img src=".gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> [Projekto nustatymai](project-settings/project-settings.md)

Skirtuke „Projekto nustatymai“ galite koreguoti bendruosius projekto ir apdorojimo nustatymus. Juos reikia sureguliuoti prieš pradedant apdoroti failus.

#### <img src=".gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> Failų naršyklė

Pridėkite failus/aplankus ir pašalinkite failus iš projekto. Duplikatai ignoruojami. Pažymėkite tikslinio vaizdo langelį tikslinio vaizdo stulpelyje, ir apdorojimas tikrins tik pažymėtus vaizdus, taip žymiai pagreitindamas apdorojimo laiką. Naudokite „Vaizdas/Metaduomenys“ perjungiklį, kad perjungtumėte tarp pasirinktų vaizdų miniatiūrų tinklelio peržiūros ir išsamios metaduomenų lentelės.

#### <img src=".gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> [Vaizdų peržiūros programa](image-viewer-gui/opening-an-image-full-screen.md)

Kai pagrindinėje vaizdų peržiūros programoje paspaudžiate vaizdą, jis atidaromas visame ekrane skirtuke „Vaizdų peržiūros programa“.

#### <img src=".gitbook/assets/image (7).png" alt="" data-size="line"> [Žemėlapis](image-viewer-gui/map-markers.md)

Peržiūrėkite savo vaizdus interaktyviame 2D žemėlapyje pagal jų GPS koordinates. Palaiko „Google Maps“ ir ESRI plytelių teikėjus, automatiškai pasirinkdamas geriausią paslaugą jūsų vietovei. Užveskite pelę ant žymių, kad pamatytumėte vaizdų miniatiūrų peržiūras.

#### <img src=".gitbook/assets/icon_log.JPG" alt="" data-size="line"> Debugavimo žurnalas

Jei kyla problemų, peržiūrėkite žurnalą, kuriame yra išspausdinti debugavimo duomenys. Nukopijuokite / atsisiųskite žurnalą ir nusiųskite jį [MAPIR palaikymo tarnybai](https://www.mapir.camera/community/contact), kad gautumėte pagalbą.

#### <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> [Vartotojo prisijungimas](chloros+-login.md)

Vartotojo prisijungimo šoninė juosta leidžia prisijungti prie savo Chloros+ paskyros, kad galėtumėte naudotis išplėstinėmis funkcijomis. Taip pat galite peržiūrėti dabartinę programos versiją bei nustatyti Chloros GUI ir CLI rodomų tekstų kalbą.
