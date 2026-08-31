---
metaLinks: {}
---

# Įvadas

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>

Chloros

yra programinė įranga iš [MAPIR

](https://www.mapir.camera), skirta apdoroti daugiaspektrinius vaizdus, valdyti „MAPIR

“ įrangą realiuoju laiku ir įrašyti jutiklių duomenis. „Chloros

“ 1.2.0 versija palaiko visą „MAPIR

“ produktų šeimą:

* **„Survey3

“ kameros** — apdoroja RAW+JPG vaizdus į kalibruotus atspindžio ir augmenijos indekso žemėlapius. Žr. [Palaikomos kameros](supported-cameras.md).
* **„LATTICE“ kameros** — prijunkite „GigE“ multispektrinius kamerų modulius tiesiogiai, po vieną arba kaip sinchronizuotus kelių kamerų masyvus: peržiūrėkite, fiksuokite ir apdorokite į kalibruotus spinduliavimo ir atspindžio produktus. Žr. [skyrių apie „LATTICE“](lattice/README.md).
* **DAQ šviesos jutikliai** — DAQ-U (USB), DAQ-M (Bluetooth) ir DAQ-E (Ethernet) spektriniai jutikliai: kalibruoti spektrai realiuoju laiku, `.daq` įrašai ir žemyn nukreiptas apšvietimas atspindžio apdorojimui. Žr. [DAQ skyrių](daq/README.md).

{% hint style="success" %}
**Kas naujo „Chloros

“ 1.2.0 versijoje**: „LATTICE“ kamerų ir matricos valdymas realiuoju laiku, DAQ šviesos jutiklių integravimas, fiksavimo režimai ir įrašymo įrenginiai, pilnas „LATTICE“ radiometrinio apdorojimo procesas, projektų automatizavimas iš „CLI

“ / „SDK

“ ir daug daugiau. Žemiau rasite naujovių sąrašą, o [atsisiųskite](download.md) pakeitimų žurnalą.
{% endhint %}

{% hint style="info" %}
**Naudojate „Chloros

“ su AI asistentu?** Šis vadovas sukurtas būtent tam. Nurodykite savo asistentui:

* `https://mapir.gitbook.io/chloros/llms.txt` — kompiuteriui suprantamą kiekvieno puslapio indeksą.
* Bet kurį puslapį kaip neapdorotą „Markdown“ — pridėkite „`.md`“ prie jo „URL

“ (pvz., „`https://mapir.gitbook.io/chloros/reference/cli-reference.md`“).
* [„CLI

“ nuoroda](reference/cli-reference.md) ir [„SDK

“ nuoroda](reference/sdk-reference.md) — išsamūs, tikslių verčių nuorodų puslapiai, parašyti LLM naudojimui.

Pavyzdinis nurodymas: *„Perskaitykite https://mapir.gitbook.io/chloros/reference/cli-reference.md,, tada parašykite skriptą, kuris prisijungtų ir apdorotų aplanką ~/flights/flight_001 į atspindžio +NDVI

GeoTIFF failus.“*

Išsamus vadovas: [„Chloros

“ naudojimas su AI asistentais](ai-assistants.md).
{% endhint %}

***

## Kas naujo „Chloros

“ 1.2.0 versijoje

* **Kameros valdymas realiuoju laiku — naujas skirtukas „Kameros“.** Prijunkite „LATTICE“ kameras po vieną arba kaip sinchronizuotus kelių kamerų rinkinius (PTP laiko sinchronizavimas, aparatinės įrangos suaktyvintas fiksavimas) su tiesioginio peržiūros vaizdo perdangomis, histogramomis pagal dažnių juostas, išmaniuoju automatiniu ekspozicijos nustatymu, tiesioginiu indeksų skaičiuokliu ir kameros programinės įrangos atnaujinimais programoje.
* **Šviesos jutikliai — naujas skirtukas „Šviesos jutikliai“.** Prijunkite DAQ-U (USB), DAQ-M (Bluetooth) ir DAQ-E (Ethernet) jutiklius; peržiūrėkite kalibruotus spektrus realiuoju laiku (W/m²/nm), įrašykite `.daq` failus į savo projektą, pasirinkite apšvietimo korekcijos profilius ir atnaujinkite DAQ-E programinę įrangą per tinklą.
* **Fiksavimo režimai ir įrašymo įrenginiai.** Vienkartinis / Nuolatinis / Intervalinis fiksavimas bei tik neapdorotų duomenų „Greičiausias fiksavimas“ režimas; galimybė kiekvienam projektui atskirai pasirinkti, kokias kameras ir eksporto tipus generuoja funkcija „Fiksuoti viską“; masyvų įrašymo įrenginiai, skirti stebėjimo kokybės indeksuotam vaizdo įrašui ir analizės kokybės neapdorotų duomenų serijoms su neprisijungus prie interneto kuriamomis vaizdo įrašų versijomis.
* **„LATTICE“ apdorojimo grandinė.** Importuokite „LATTICE“ fiksavimo aplankus ir kiekvieną neapdorotą kadrą išskirstykite į debayeringo, peržiūros, „float32“ spinduliavimo (W/m²/sr/nm) ir atspindžio produktus su perjungimo galimybėmis kiekvienam produktui atskirai. Atspindžio koeficientas gali būti gaunamas iš kadre esančio kalibravimo taikinio arba iš DAQ duomenų srauto; eksportuojamiems duomenims taikomas masyvo suderinimas; trūkstama gamyklinė kalibracija automatiškai atsisiunčiama pagal kameros serijos numerį.
* **Projektai įsimena įrangą.** Prijungtos kameros ir šviesos jutikliai išsaugomi kartu su projektu (`cameras.json` / `sensors.json`) ir, vėl atidarius projektą, vėl prisijungia su išsaugotais nustatymais. Žr. [GUI: Projektai](projects.md).
* **Vaizdų peržiūros programos atnaujinimai.** Kursoriaus pikselių / indekso rodymas su teisingu atspindžio mastelio pritaikymu kiekvienam failui, sluoksnių histogramos, GSD grupavimo slankiklis, tinklelio režimai „Per Trigger“ / „Per Camera“, „LATTICE“ produktų peržiūros ir indekso / LUT „sandbox“ eksportavimas į diską.
* **„CLI

“ ir „SDK

“ – žymiai išplėstos.** Naujos komandų grupės: `lattice`, `daq pool-*`, `project` ir `time-sync`; naujos „`process`“ parinktys („`--input-level`“ – perjungikliai kiekvienam produktui, „`--reflectance-source`“ – masyvo suderinimo žymės); „SDK

“ „smart-connect“ rankenos (`connect_camera` / `connect_array` / `connect_daq_sensor`), kurios automatiškai paleidžia foninę sistemą; `open_project()` automatizavimas; „SDK

“ ratas yra pridedamas prie diegimo programų ir paskelbtas PyPI kaip `chloros-sdk`.
* **Aiški klaidų semantika.** „`chloros-cli process`“ vykdymas, kuris paprašė produktų, bet neišrašė nė vieno, dabar aiškiai rodo klaidą ir baigiasi nulinės vertės kodu; sėkmingi vykdymai praneša, kiek vaizdų produktų buvo išrašyta.
* **Naujas išvesties išdėstymas.** Produktai patenka į `<project>/<camera>/<format>/<Product>_Images/` aplankus ir išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas, o ne failo pavadinimo priesaga. Žr. [Išvesties vaizdų formatai](output-image-formats.md).
* **Daugiau įvesties šaltinių, planų ir kalbų.** `.dng` įvesties palaikymas; visos 38 sąsajos kalbos visiškai užpildytos; aparatūros apribojimai pagal planą, leidžiantys nemokamai (be prisijungimo) naudoti iki 4 kamerų ir 2 šviesos jutiklių.
* **Patikimumas.** Funkcija „Stop Processing“ (Apdorojimo sustabdymas) sklandžiai baigia veikimą, pateikdama išsamią vykdymo santrauką; daugiakameriniai projektai eksportuoja kiekvienos kameros duomenis, o diegimo programos atnaujinimai nebeatsijungia jūsų iš sistemos.***

„Chloros

“ yra prieinama 3 programinės įrangos versijose:

##Chloros

: darbalaukio GUI programa

Atskiras langas su visomis funkcijomis, įskaitant skirtukus „Live Cameras“ (Kameros realiuoju laiku) ir „Light Sensors“ (Šviesos jutikliai). _Tik „Windows“ sistemai._

## [Chloros

CLI

: Komandinės eilutės sąsaja](CLI.md)

Komandinės eilutės paketinis apdorojimas ir tiesioginės komandos „`lattice`“, „`daq pool-*`“, „`project`“ bei „`time-sync`“. Puikiai tinka automatizavimui, skriptų kūrimui ir darbui be grafinės sąsajos. Prieinama **„Windows

“, „Linux

“ amd64 ir „Linux

“ arm64 (NVIDIA Jetson)**. _Norint naudotis CLI, reikalingas mokamas „Chloros

+“ lygis._

## [Chloros

„API

“:Python

SDK

](api-python-sdk.md)

Programinė „Python

“ sąsaja, skirta automatizavimui ir individualiems darbo srautams: visos apdorojimo grandinės apdorojimas, tiesioginės kameros / matricos sesijos, DAQ jutiklių sesijos ir išsaugotų projektų automatizavimas. Įdiegiama su „desktop/CLI

“ paketu, taip pat paskelbta kaip „`pip install chloros-sdk`“. _Norint naudotis API, reikalingas mokamas „Chloros

+“ lygis._

***

## Palaikomos platformos

| Platforma | GUI |CLI

|Python

SDK

|
| --- | --- | --- | --- |
| **„Windows

“ 10/11 (x64)** | Taip | Taip | Taip |
| **„Linux

“ amd64 (x86_64)** | Ne | Taip | Taip |
| **Linux

arm64 (NVIDIA Jetson)** | Ne | Taip | Taip |

„Linux

“ diegimo instrukcijas rasite skyriuje [Linux

ir „Edge Computing“](linux/linux-overview.md).

***

## Pradėkite per tris žingsnius

1. **Įdiekite** — atsisiųskite ir paleiskite jūsų platformai skirtą diegimo programą. Žr. [Atsisiųsti](download.md).
2. **Prisijunkite (neprivaloma, jei naudojate GUI)** — GUI apdoroja vaizdus nemokamai be paskyros. [Chloros

+ prisijungimas](chloros+-login.md) suteikia galimybę naudotis lygiagrečiu apdorojimu, GPU pagreitinimu, didesniais įrenginių apribojimais ir prieiga prie „CLI

“ / „SDK

“.
3. **Sukurkite savo pirmąjį projektą** — atidarykiteChloros

, sukurkite [Naują projektą](projects.md), [pridėkite savo vaizdus](processing-images-gui/adding-files-to-a-project.md) ir [pradėkite apdorojimą](processing-images-gui/starting-the-processing.md). Jei norite valdyti realią įrangą, atidarykite skirtuką „Kameros“ arba „Šviesos jutikliai“ — žr. [GUI: Navigacija](navigation.md).

***

##Chloros

+

Nors „Chloros

“ daugeliui užduočių galima naudoti nemokamai, galbūt jums prireiks daugiau galimybių. Tokiu atveju jums gali praversti mokama „Chloros

“ licencija. Turėdami „Chloros

“ licenciją, galėsite naudotis tokiomis naujomis funkcijomis kaip:

* **Daugiasiūlis apdorojimas**: žymiai pagreitinkite vaizdų apdorojimą didesniuose projektuose, vienu metu apdorodami vaizdus per apdorojimo grandinę.
* **GPU (CUDA) pagreitinimas**: pasinaudokite šiuolaikinėmis didesnės talpos GPU atminties galimybėmis, kad dar labiau pagreitintumėte vaizdų apdorojimo procesą. Norint pasiekti geriausių rezultatų, rekomenduojame naudoti 4 GB ar daugiau VRAM.
* **„Chloros

“**[**CLI**](CLI.md) **prieiga**: paleiskite „Chloros

“ iš komandinės eilutės, kad automatizuotumėte ir integruotumėte į savo programinę įrangą. Prieinama bet kuriame mokamame pakete; taikoma serverio pusėje.
* **Chloros

+**[**API**](api-python-sdk.md) **Prieiga:** paleiskite komandą „Chloros

+“ išPython

, kad galėtumėte valdyti programiškai, užtikrinant sklandžią integraciją su jūsų tyrimų procesais, duomenų analizės darbo srautais ir individualiomis programomis. Prieinama bet kuriame mokamame pakete; taikoma serverio pusėje.
* **Didesni aparatinės įrangos apribojimai**: vienu metu prijunkite daugiau kamerų ir šviesos jutiklių. Be prisijungimo grafinė vartotojo sąsaja leidžia prijungti iki 4 kamerų ir 2 DAQ šviesos jutiklių; mokamose paslaugų pakopose abu apribojimai padidinami:

| Planas | Kameros | DAQ šviesos jutikliai |
| --- | --- | --- |
| „Iron“ (nemokamas, be prisijungimo) | 4 | 2 |
| „Copper“ / „Bronze“ | 6 | 3 |
| „Silver“ | 10 | 6 |
| „Gold“ | 20 | 12 |

* **Naudojimas keliuose įrenginiuose**: kiekviena „Chloros

+“ licencija leidžia užregistruoti 2 ar daugiau įrenginių. Naudokite savo „MAPIR

Cloud“ paskyrą, kad valdytumėte užregistruotus įrenginius. Norėdami naudoti daugiau įrenginių, atnaujinkite savo „Chloros

+“ licenciją.
* **Išplėstinis tekstūrą atpažįstantis debayerio metodas:** aukštos kokybės, kraštus atpažįstantis debayeris, suderintas su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayerio triukšmą.
* **Pasirinktinės multispektrinių indeksų formulės:** įveskite pasirinktinius multispektrinius indeksus „Chloros

“ rastro skaičiuoklėse, skirtose tiek apdorojimui, tiek vaizdų peržiūros bandymo aplinkai.
* **„Linux

“ ir „Edge Computing“:** paleiskite „Chloros

“ „Linux

“ x86\_64 ir ARM64 platformose, įskaitant „NVIDIA Jetson“, skirtose lauko ir „edge“ apdorojimui. Žr. [„Linux

“ apžvalgą](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ Kainos ir registracija</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.2.0 build so the banner prints "Chloros CLI 1.2.0". -->
