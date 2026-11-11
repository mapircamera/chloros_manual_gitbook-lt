---
metaLinks: {}
---

# Pradžia

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>

Chloros – tai [MAPIR](https://www.mapir.camera) sukurta programinė įranga, skirta apdoroti daugiaspektrinius vaizdus, valdyti MAPIR įrangą realiuoju laiku ir įrašyti jutiklių duomenis. Chloros 1.3.0 versija palaiko visą MAPIR produktų šeimą:

* **Survey3 kameros** — apdoroja RAW+JPG vaizdus į kalibruotus atspindžio ir augmenijos indekso žemėlapius. Žr. [Palaikomos kameros](supported-cameras.md).
* **„LATTICE“ kameros** — prijunkite „GigE“ daugiaspektrinius kamerų modulius realiuoju laiku, po vieną arba kaip sinchronizuotus daugiakamerinius masyvus: peržiūrėkite, užfiksuokite ir apdorokite į kalibruotus spinduliavimo ir atspindžio produktus. Žr. [„LATTICE“ skyrių](lattice/README.md).
* **DAQ šviesos jutikliai** — DAQ-U (USB), DAQ-M (Bluetooth) bei DAQ-E ir DAQ-E-S (Ethernet) spektriniai jutikliai: kalibruoti spektrai realiuoju laiku, `.daq` įrašai ir žemyn nukreiptas apšvietimas atspindžio apdorojimui. Žr. [DAQ skyrių](daq/README.md).

{% hint style="success" %}
**Kas naujo Chloros 1.3.0 versijoje**: AI asistentai gali valdyti Chloros per `chloros-mcp`, o CLI, SDK ir `chloros-mcp` siūlo tas pačias funkcijas. „LATTICE“ indeksavimo produktai patobulinti, visų jutiklių masyvai veikia greičiau ir tinka bet kokiam tinklui, o užkulisinė sistema toliau veikia tarp komandų. Žiūrėkite žemiau pateiktą naujovių sąrašą, o diegimo programas galite [atsisiųsti](download.md).
{% endhint %}

{% hint style="info" %}
**Naudojate Chloros su AI asistentu?** Šis vadovas skirtas būtent tam. Nurodykite savo asistentui:

* `https://mapir.gitbook.io/chloros/llms.txt` — kompiuteriui suprantamas kiekvieno puslapio indeksas.
* Bet kurį puslapį kaip neapdorotą „Markdown“ — pridėkite `.md` prie jo URLo (pvz., `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI žinynas](reference/cli-reference.md) ir [SDK žinynas](reference/sdk-reference.md) — išsamūs, tikslių verčių žinynai, skirti LLM naudojimui.

Pavyzdinis užklausos tekstas: *„Perskaitykite https://mapir.gitbook.io/chloros/reference/cli-reference.md,, tada parašykite skriptą, kuris prisijungtų ir apdorotų aplanką ~/flights/flight_001 į atspindžio + NDVI GeoTIFF failus.“*

Arba leiskite jam tiesiogiai paleisti Chloros: `chloros-mcp`, MCP serveris, įdiegtas kartu su SDK, veikia su „Claude Desktop“, „Claude Code“, „Cursor“ ir kitais MCP klientais.

Išsamus vadovas: [Chloros naudojimas su AI asistentais](ai-assistants.md).
{% endhint %}

***

## Kas naujo Chloros 1.3.0 versijoje

* **AI asistentai gali valdyti Chloros.** „`chloros-mcp`“ – MCP serveris, įdiegtas su Python SDK – leidžia „Claude Desktop“, „Claude Code“, „Cursor“ ir kitiems MCP klientams apdoroti vaizdus bei valdyti „LATTICE“ kameras, matricas ir DAQ šviesos jutiklius. Žr. [Chloros naudojimas su AI asistentais](ai-assistants.md).
* **CLI, SDK ir `chloros-mcp` siūlo tas pačias funkcijas.** Saugumo sumetimais CLI lieka tik prisijungimas, DAQ-E konfigūravimas ir tiesioginiai DAQ serveriai. Kai kuriuos 1.2.0 versijos scenarijus reikia pakeisti: žr. skyrių **„Pakeitimai 1.3.0 versijoje“** [CLI žinyno](reference/cli-reference.md) ir [SDK žinyno](reference/sdk-reference.md) viršuje.
* **Patobulinti „LATTICE“ indeksų produktai.** NDVI ir kiti indeksų produktai, eksportuojami iš „LATTICE“ įrašų, yra patobulinti visuose apdorojimo režimuose.
* **Windows diegimo programos parinktys.**Skyriuje**„Sistemos nustatymai“**diegimo programa siūlo parinktis**„Įjungti ilgus Windows kelius“**ir**„Išskirti Chloros užkurtį iš „Microsoft Defender“ nuskaitymo“**; abi parinktys pažymėtos pagal numatytuosius nustatymus. Pirmoji parinktis leidžia projekto aplankams būti giliau nei 260 simbolių; antroji užkerta kelią realaus laiko nuskaitymui sulėtinti kameros įrašymą (maždaug 30 % įrašymo greičio esant dideliems greičiams). Dabar diegimo programa taip pat tik vieną kartą įtraukia CLI aplanką į „`PATH`“ ir pašalina ankstesnių diegimo programų paliktus pasikartojančius įrašus. Žr. [Atsisiųsti](download.md).
* **Giliai įsikūrę projekto aplankai atmetami iš karto.** Kai Windows ilgieji keliai yra išjungti (Windows numatytasis nustatymas), projekto aplankas, kurio kelias viršija 260 simbolių ribą, atmetamas dar prieš pradedant vykdyti bet kokias operacijas, kartu nurodant ilgiausią tinkamą projekto aplanką ir kaip įjungti ilguosius kelius. Anksčiau tokio vykdymo metu nebuvo įrašomi jokie produktai. Įjungus ilgus kelius, produktai, esantys labai giliuose aplankuose, dabar išlaiko savo metaduomenis (GPS ir XMP žymes).
* **Kaukių apribojimas.** Kai atskiriant „LATTICE“ spinduliavimo ar atspindžio produkto juostas gaunama neigiama juostos reikšmė, ta reikšmė apribojama iki nulio, o `clamp_masks/` aplanke, esančiame šalia produktų aplankų, esanti kaukė pagal pikselius fiksuoja, kurie pikseliai ir juostos buvo paveikti. Produktas savo kaukę pavadina `Chloros:UnmixClampMask` žymėje. Tiesioginiai vaizdai dabar apkarpomi lygiai taip pat, kaip ir eksportuojant. Žr. [Išvesties vaizdo formatai](output-image-formats.md).
* **Produktai apibūdina patys save.** Indekso produktai turi savo apdorojimo žymes (`Chloros:ProcessingLevel=index`, indekso išraišką ir informaciją, ar jie buvo apskaičiuoti pagal spinduliavimą, ar atspindį), o ne šaltinio neapdorotų duomenų žymes. Survey3 produktai kiekviename apdorojimo režime turi Chloros apdorojimo žymes. „LATTICE“ neapdoroti ir debayeringo būdu apdoroti failai išlaiko jutiklio pradinę orientaciją ir įrašo savo orientaciją XMP; vaizdų peržiūros programa juos pasuka tik rodymo tikslais.
* **Linux paketai pateikiami neišpakuoti.** Užkulisinė programa ir CLI įdiegiamos kaip aplankai po „`/usr/lib/chloros/`“, todėl jos nebeišpakuojasi kiekvieną kartą paleidžiant. „x86_64“ aplinkoje užkulisinė programa nebeužtrunka apie 100 sekundžių paleidžiant, o „`.deb`“ dydis yra maždaug perpus mažesnis nei anksčiau. Pridėta „ExifTool“ dabar veikia švarioje „Ubuntu“ instaliacijoje. „`.deb`“ dabar taip pat įdiegia Python SDK sistemai „`python3`“ (įdiekite jį naudodami „`sudo apt install ./chloros_1.3.0_<arch>.deb`“), padidina branduolio lizdo priėmimo buferius, kurių reikia kameros srautams, ir prideda užkulisinę paslaugą, veikiančią jūsų vartotojo vardu. „Jetson“ paketas, `chloros_1.3.0_arm64_jp6.deb`, sukurtas „JetPack 6“ ir apdoroja duomenis „Jetson“ GPU naudodamas „JetPack“ CUDA versiją „PyTorch“. Linux sistemoje kiekvienas eksportuotas vaizdas dabar pasiekia diską vieną kartą (įrašomas ir pažymimas atmintyje, tada nukopijuojamas į vietą): „Jetson“ įrenginyje, kur tempą nulemia diskas, 288 vaizdų „LATTICE“ užduotis trunka 14 minučių, o ne 24. Žr. [Linux diegimas](linux/linux-installation.md).
* **„LATTICE“ OCN kameros saulės šviesoje nuskaito tiksliau.** Spinduliavimas, atspindys ir indeksai po juostų atskyrimo gauna greitą korekciją, todėl pilkas taikinys saulėje nuskaitomas kaip beveik neutralus NDVI. RGN kameros lieka nepakitusios.
* **Klaidų pranešimai programoje.** Klaidų piktograma, esanti „Debug Log“ skirtuko viršuje, siunčia MAPIR aprašymą, iki 10 vaizdų, žurnalus ir sistemos duomenis. Žr. [GUI: Navigacija](navigation.md).
* **LATTICE masyvai.**Masyvai pagal numatytuosius nustatymus išlaiko visą jutiklio duomenų srautą, o suspaustas 12 bitų perdavimas (`--packed`, numatytasis `auto`) padidina viso jutiklio kadrų dažnį maždaug trečdaliu; masyvas veikia bet kuriame tinkle tuo kadrų dažniu, kurį leidžia tinklas (lėtas ryšys sumažina kadrų dažnį, o ne atmeta masyvą ar sumažina jo kadrų skaičių); masyvas, kurio kameros buvo išjungtos ir vėl įjungtos, atsistato savaime, vėl įjungiant PTP; automatinė ekspozicija nebesumažina to dažnio esant silpnam apšvietimui (ji užpildo laikotarpį, tada padidina stiprinimą); Nuolatinis vaizdo fiksavimas neatsilieka nuo greitų masyvų;**„Highlights“** valdymas, skirtas RGB kameroms; o SDK funkcijos `ArraySession` įgyja `grab()`, `frame_stream()` ir `continuous()`. Žr. [skyrių „LATTICE“](lattice/README.md).
* **Stabilesnė automatinė ekspozicija.** Ekspozicija išlieka pastovi esant pastoviam apšvietimui ir stabilizuojasi per kelias sekundes po pokyčio; nedideli šviesūs plotai nebetamsina vaizdo; nustatant ekspoziciją rankiniu būdu, stiprinimas paliekamas automatiniame režime; o kamera, kuri nustoja reaguoti esant ryškiai saulei, išlaiko ekspozicijos lygį, atitinkantį apšvietimą, kurį nuskaito šviesos jutiklis arba, jei jo nėra, kameros pačios kadrų duomenys. Žr. [Kameros nustatymus](lattice/camera-settings.md).
* **Šviesos jutikliai.** **„DAQ-E-S“ palaikymas:** jo fiksuotas difuzorius nereikalauja dangtelio korekcijos, o kiekvienas matavimas atspindi įrenginio pasvirimą, pasukimą ir pakreipimą. Įraše nurodoma, ar dangtelis buvo pasirinktas, ar numatytas, o apdorojimo metu pateikiamas įspėjimas apie numatytąjį: „DAQ-U“, „DAQ-M“ ir „DAQ-E“ tiekiami be dangtelio, todėl nurodykite, koks dangtelis yra uždėtas. Tiesioginiai `.daq` įrašai išlieka net ir nutraukus maitinimą (išsaugokite `.daq-journal` failą kartu su jo `.daq`), o DAQ-E, kuris nustoja perduoti duomenis, vėl prijungiamas su įraše pažymėta pertrauka. Projektas, kuriame saugomi tik šviesos jutiklių įrašai, juos išvardija ir apdoroja per programėlę bei CLI, o įrašas, padarytas naudojant „`chloros_scripts`“, sąraše nurodomas kaip DAQ, kuriuo jis buvo padarytas, su jūsų nustatomu apribojimu. Kiekvienas įrašymas išsaugo DAQ rodmenį, kurį naudojo. Žr. [DAQ skyrių](daq/README.md).
* **Apdorojimas.**„LATTICE“ kadrai pradedami apdoroti aptikus tikslą. Tikslo ir DAQ nuorodos nustatomos kiekvienam dažnių juostai atskirai. Perkėlus projektą, jo failai susiejami iš naujo. 8 bitų įrašų eksportai yra 8 bitų, naudojant naują**TIFF (8 bitų)** formatą. Survey3 vykdymas be kalibravimo taikinio išsaugomas kaip atsarginis produktas (`Vignette_Corrected_Images` arba `Sensor_Response_Images`), o ne kaip atspindžio vaizdas. Atspindžio vaizdo DLS žymės kiekvienam juostos diapazonui visada priklauso pačiam kadrui. „Texture Aware“ funkcija netaikoma „LATTICE“ įrašams, kuriems visada taikomas standartinis „debayer“.
* **CLI ir SDK.** Santykiniai išvesties keliai dabar nukreipiami į tą aplanką, iš kurio paleidžiate komandą ar skriptą (anksčiau įdiegta užkulisinė programa juos spręsdavo savo įdiegimo aplanke ir nesėkmingai). Fonas toliau veikia tarp komandų ir skriptų ir pats sustabdo savo veiklą po 30 minučių nenaudojimo; tai valdo `chloros-cli backend status` ir `backend stop`. Įrašymo komandos nurodo kiekvieną failą, kurio nepavyko įrašyti, ir baigia veikimą su kodu 1, o pasikartojantys įrašymai sustabdomi, kai diskas užpildomas. Sėkmingai įvykdžius „`process`“, pranešama apie „`Image products written: N (plus M clamp masks)`“. Windows sistemoje SDK „`ChlorosLocal`“ / `process_folder` suranda įdiegtą užkurtį (ir naudoja tą, kuris jau veikia), o `read_image_audit_tags` veikia iš paprasto `pip install`.

***

## Kas naujo Chloros 1.2.0 versijoje

* **Kameros valdymas realiuoju laiku — naujas skirtukas „Kameros“.** Prijunkite „LATTICE“ kameras po vieną arba kaip sinchronizuotus kelių kamerų rinkinius (PTP laiko sinchronizavimas, aparatinės įrangos suaktyvinamas vaizdo fiksavimas), su tiesioginio peržiūros vaizdo perdangomis, histogramomis pagal dažnių juostas, išmaniuoju automatiniu ekspozicijos nustatymu, tiesioginiu indeksų skaičiuokliu ir kameros programinės įrangos atnaujinimais programoje.
* **Šviesos jutikliai — naujas skirtukas „Šviesos jutikliai“.** Prijunkite DAQ-U (USB), DAQ-M (Bluetooth) ir DAQ-E (Ethernet) jutiklius; peržiūrėkite kalibruotus spektrus realiuoju laiku (W/m²/nm), įrašykite `.daq` failus į savo projektą, pasirinkite „cap-correction“ profilius ir atnaujinkite DAQ-E programinę įrangą per tinklą.
* **Fiksavimo režimai ir įrašymo įrenginiai.** Vienkartinis / Nuolatinis / Intervalinis fiksavimas bei tik neapdorotų duomenų „Greičiausias fiksavimas“ režimas; galimybė kiekvienam projektui atskirai pasirinkti, kokias kameras ir eksporto tipus generuoja funkcija „Fiksuoti viską“; masyvo įrašymo įrenginiai, skirti stebėjimo kokybės indeksuotam vaizdo įrašui ir analizės kokybės neapdorotų duomenų serijoms su neprisijungus prie interneto kuriamomis vaizdo įrašų versijomis.
* **„LATTICE“ apdorojimo grandinė.** Importuokite „LATTICE“ fiksavimo aplankus ir kiekvieną neapdorotą kadrą išskirstykite į debayeringo, peržiūros, „float32“ spinduliavimo (W/m²/sr/nm) ir atspindžio produktus su perjungimo galimybėmis kiekvienam produktui atskirai. Atspindžio koeficientas gali būti gaunamas iš kadre esančio kalibravimo taikinio arba DAQ duomenų; eksportuojamiems duomenims taikomas masyvo suderinimas; trūkstama gamyklinė kalibracija automatiškai atsisiunčiama pagal kameros serijos numerį.
* **Projektai įsimena įrangą.** Prijungtos kameros ir šviesos jutikliai išsaugomi kartu su projektu (`cameras.json` / `sensors.json`) ir, vėl atidarius projektą, vėl prisijungia su išsaugotais nustatymais. Žr. [GUI: Projektai](projects.md).
* **Vaizdų peržiūros programos atnaujinimai.** Kursoriaus pikselių / indekso rodymas su teisingu atspindžio mastelio pritaikymu kiekvienam failui, sluoksnių histogramos, GSD grupavimo slankiklis, tinklelio režimai „Per Trigger“ / „Per Camera“, „LATTICE“ produktų peržiūros ir indekso / LUT „sandbox“ eksportavimas į diską.
* **Žymiai išplėstos CLI ir SDK galimybės.** Naujos komandų šeimos: `lattice`, `daq pool-*`, `project` ir `time-sync`; naujos `process` parinktys (`--input-level`, perjungikliai pagal produktą, `--reflectance-source`, masyvo išlyginimo žymės); SDK „smart-connect“ identifikatoriai (`connect_camera` / `connect_array` / `connect_daq_sensor`), kurie automatiškai paleidžia užkulisinę programą; `open_project()` automatizavimas; SDK ratas yra pridedamas prie diegimo programų ir paskelbtas „PyPI“ kaip `chloros-sdk`.
* **Aiški klaidų semantika.** „`chloros-cli process`“ vykdymas, kuris paprašė produktų, bet neišrašė nė vieno, dabar aiškiai rodo klaidą ir baigiasi nulinės vertės kodu; sėkmingi vykdymai praneša, kiek vaizdo produktų buvo išrašyta.
* **Naujas išvesties išdėstymas.** Produktai patenka į `<project>/<camera>/<format>/<Product>_Images/` aplankus ir išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas, o ne failo pavadinimo priesaga. Žr. [Išvesties vaizdų formatai](output-image-formats.md).
* **Daugiau įvesties šaltinių, planų ir kalbų.** Įvesties palaikymas iš `.dng`; visos 38 sąsajos kalbos visiškai užpildytos; aparatūros apribojimai pagal planą, leidžiantys nemokamai (be prisijungimo) naudoti iki 4 kamerų ir 2 šviesos jutiklių.
* **Patikimumas.** Funkcija „Stop Processing“ (Apdorojimo sustabdymas) sklandžiai užbaigia procesą, pateikdama išsamią vykdymo santrauką; daugiakameriniai projektai eksportuoja kiekvienos kameros duomenis, o diegimo programos atnaujinimai nebepriverčia jus atsijungti.***

Chloros yra prieinama 3 programinės įrangos platformose:

## Chloros: darbalaukio GUI programa

Atskiras langas su visomis funkcijomis, įskaitant skirtukus „Kameros“ ir „Šviesos jutikliai“ (realiu laiku). _Tik „Windows“ sistemai._

## [Chloros CLI: Komandinės eilutės sąsaja](CLI.md)

Komandinės eilutės paketinis apdorojimas ir tiesioginės komandos „`lattice`“, „`daq pool-*`“, „`project`“ bei „`time-sync`“. Puikiai tinka automatizavimui, skriptų kūrimui ir darbui be grafinės sąsajos. Prieinama **Windows, Linux amd64 ir Linux arm64 (NVIDIA Jetson)** sistemose. _Norint naudotis CLI, reikalingas mokamas „Chloros+“ planas._

## [Chloros API: Python SDK](api-python-sdk.md)

Programinė Python sąsaja, skirta automatizavimui ir individualiems darbo srautams: visos apdorojimo grandinės apdorojimas, tiesioginės kameros / matricos sesijos, DAQ jutiklių sesijos ir išsaugotų projektų automatizavimas. Įdiegiama kartu su darbalaukio / CLI paketu, taip pat paskelbta kaip `pip install chloros-sdk`. _Norint naudotis API, reikalingas mokamas „Chloros+“ paketas._

***

## Palaikomos platformos

| Platforma | GUI | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11 (x64)** | Taip | Taip | Taip |
| **Linux amd64 (x86_64)** | Ne | Taip | Taip |
| **Linux arm64 (NVIDIA Jetson)** | Ne | Taip | Taip |

Linux diegimo instrukcijas rasite skyriuje [Linux ir „Edge Computing“](linux/linux-overview.md).

***

## Pradėkite per tris žingsnius

1. **Įdiegti** — atsisiųskite ir paleiskite jūsų platformai skirtą diegimo programą. Žr. [Atsisiųsti](download.md).
2. **Prisijunkite (neprivaloma, jei naudojate GUI)** — GUI apdoroja vaizdus nemokamai be paskyros. [„Chloros+“ prisijungimas](chloros+-login.md) suteikia galimybę naudotis lygiagrečiu apdorojimu, GPU pagreitinimu, didesniais įrenginių apribojimais ir prieiga prie CLI bei SDK.
3. **Sukurkite savo pirmąjį projektą** — atidarykite Chloros, sukurkite [Naują projektą](projects.md), [pridėkite savo vaizdus](processing-images-gui/adding-files-to-a-project.md) ir [pradėkite apdorojimą](processing-images-gui/starting-the-processing.md). Jei norite valdyti realaus laiko įrangą, atidarykite skirtuką „Kameros“ arba „Šviesos jutikliai“ — žr. [GUI: Navigacija](navigation.md).

***

## „Chloros+“

Nors Chloros daugeliui užduočių yra nemokamas, galbūt norėsite daugiau. Tuomet jums pravers mokama „Chloros+“ licencija. Turėdami „Chloros+“ licenciją, galėsite naudotis tokiomis naujomis funkcijomis kaip:

* **Daugiasiūlis apdorojimas**: žymiai pagreitinkite vaizdų apdorojimą didesniuose projektuose, vienu metu apdorodami vaizdus per apdorojimo grandinę.
* **GPU (CUDA) pagreitinimas**: pasinaudokite šiuolaikinėmis didesnės talpos GPU atminties galimybėmis, kad dar labiau pagreitintumėte vaizdų apdorojimo grandinę. Geriausiems rezultatams pasiekti rekomenduojame 4 GB ar daugiau VRAM.
* **Chloros+**[**CLI**](CLI.md) **Prieiga**: paleiskite „Chloros+“ iš komandinės eilutės, kad automatizuotumėte ir integruotumėte į savo programinę įrangą. Prieinama bet kuriame mokamame lygyje; taikoma serverio pusėje.
* **Chloros+**[**API**](api-python-sdk.md) **Prieiga:** paleiskite „Chloros+“ iš Python, kad galėtumėte valdyti programiškai, užtikrinant sklandžią integraciją su jūsų tyrimų procesais, duomenų analizės darbo srautais ir individualiomis programomis. Prieinama bet kuriame mokamame plane; taikoma serverio pusėje.
* **Didesni aparatūros apribojimai**: vienu metu prijunkite daugiau kamerų ir šviesos jutiklių. Be prisijungimo GUI leidžia prijungti iki 4 kamerų ir 2 DAQ šviesos jutiklių; mokamose planuose abu apribojimai padidinami:

| Planas | Kameros | DAQ šviesos jutikliai |
| --- | --- | --- |
| „Iron“ (nemokamas, be prisijungimo) | 4 | 2 |
| „Copper“ / „Bronze“ | 6 | 3 |
| „Silver“ | 10 | 6 |
| „Gold“ | 20 | 12 |

* **Naudojimas keliuose įrenginiuose**: kiekviena „Chloros+“ licencija leidžia užregistruoti 2 ar daugiau įrenginių. Naudokite savo „MAPIR Cloud“ paskyrą, kad valdytumėte užregistruotus įrenginius. Atnaujinkite savo „Chloros+“ licenciją, kad galėtumėte prijungti daugiau įrenginių.
* **Išplėstinis tekstūrą atpažįstantis debayerio metodas:** aukštos kokybės, kraštus atpažįstantis debayeris, derinamas su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayerio triukšmą.
* **Pasirinktinės multispektrinių indeksų formulės:** įveskite pasirinktinius multispektrinius indeksus į Chloros rastro skaičiuokles, skirtas tiek apdorojimui, tiek vaizdų peržiūros bandymo aplinkai.
* **Linux ir „Edge Computing“:** paleiskite Chloros Linux x86_64 ir ARM64 platformose, įskaitant „NVIDIA Jetson“, skirtose lauko ir „edge“ apdorojimui. Žr. [Linux apžvalgą](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">„Chloros+“ kainos ir registracija</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.3.0 build so the banner prints "Chloros CLI 1.3.0". -->
