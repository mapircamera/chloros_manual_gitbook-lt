---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# DUK

<details>

<summary>Ar galiu apdoroti vaizdus iš kamerų, kurios nėra MAPIR prekės ženklo, naudodamas Chloros?</summary>

Ne, „Chloros“ palaiko tik „MAPIR“ kamerų vaizdų apdorojimą — „Survey3“ ir „LATTICE“ serijų. Daugiau informacijos rasite [palaikomų kamerų modelių sąraše](supported-cameras.md). „MAPIR Cloud“ siūlome kitų kamerų vaizdų apdorojimą, pilną sąrašą rasite [čia](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Ar „Chloros“ palaiko „LATTICE“ kameras?</summary>

Taip. „Chloros“ 1.2.0 versija visapusiškai palaiko „LATTICE M3C“ ir „M3M“ kamerų modulius: **valdymas realiuoju laiku**— atradimas, prisijungimas, peržiūra ir vaizdo įrašymas iš GUI skirtuko „Kameros“, `chloros-cli lattice` arba Python SDK, įskaitant sinchronizuotus kelių kamerų masyvus su PTP laiko sinchronizavimu — ir**visišką radiometrinį užfiksuotų vaizdų apdorojimą** (neapdoroti duomenys → debayeringas → spinduliavimas → atspindys → indeksas). Žr. [Palaikomos kameros](supported-cameras.md) ir [„LATTICE“ vadovą](lattice/README.md).

</details>

<details>

<summary>Ar galiu kalibruoti savo vaizdus pagal atspindžio koeficientą be kalibravimo taikinio?</summary>

**Survey3:** Ne. Jei kartu su vaizdais be kalibravimo taikinio nebus užfiksuotas kalibravimo taikinio vaizdas, negalėsite susieti vaizdo pikselių verčių su žinomu atspindžio procentu. Jei taip pat neįtrauksite MAPIR šviesos jutiklio duomenų, aplinkos šviesos spektras nebus išmatuotas, o atspindžio rezultatai bus netikslūs.**LATTICE:** Taip. Atspindžio koeficientą galima susieti su žemyn nukreiptu spinduliavimo intensyvumu, išmatuotu DAQ šviesos jutikliu, o ne panelės (ρ = π·L/E). Kai kadre *yra* kokybės užtikrinimo reikalavimus atitinkantis taikinys, jis pagal numatytuosius nustatymus tampa absoliučiu etalonu (`--reflectance-source auto`). Viena išimtis: „F988 atspindžio koeficientas kalibruojamas naudojant kadre esantį atspindžio plokštelę: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros taiko jūsų naujausią plokštelės užfiksuotą duomenį ir išlaiko jį tarp plokštelės stebėjimų.“ Žr. [Kalibravimo taikiniai](calibration-targets.md).

</details>

<details>

<summary>Ar man reikia DAQ šviesos jutiklio?</summary>

Ne, jei matuojate spinduliavimą: „LATTICE“ spinduliavimo eksportai gaunami remiantis kiekvienos kameros gamykliniu radiometriniu kalibravimu, todėl nereikia nei DAQ jutiklio, nei etalono. **Atspindžio**matavimui reikalingas aplinkos šviesos etalonas – tai gali būti DAQ šviesos jutiklio matavimas žemyn nukreiptos šviesos atžvilgiu arba kadre esantis kalibravimo etalonas. DAQ jutiklis leidžia gauti kalibruotą atspindžio koeficientą**net neįrengiant jokių plokščių kadre**. Įrašyti `.daq` failai automatiškai susiejami su jūsų vaizdais pagal laiko žymą. Žr. [Kalibravimo taikiniai](calibration-targets.md) ir [CLI etaloną](reference/cli-reference.md).

</details>

<details>

<summary>Ar galiu naudoti Chloros su AI asistentu (Claude, ChatGPT ir pan.)?</summary>

Taip — šis vadovas ir CLI/SDK yra pritaikyti šiam tikslui:

* Visas vadovo rodyklė pateikiama adresu `https://mapir.gitbook.io/chloros/llms.txt`, kad AI asistentai galėtų rasti kiekvieną puslapį.
* Kiekvieno puslapio neapdorotas „Markdown“ kodas yra prieinamas jo mažosiomis raidėmis pavadintame puslapyje „URL“, prie kurio pridėtas „`.md`“ (pavyzdžiui, „`https://mapir.gitbook.io/chloros/reference/cli-reference.md`“).
* [CLI nuoroda](reference/cli-reference.md) ir [SDK nuoroda](reference/sdk-reference.md) yra parengtos LLM naudojimui: tikslios žymės, numatytieji nustatymai, išėjimo semantika ir komandos, kurias galima nukopijuoti ir įklijuoti.

Žr. [AI asistentai](ai-assistants.md), kaip nukreipti savo asistentą į Chloros.

</details>

<details>

<summary>Kur patenka mano apdoroti išvesties failai?</summary>

Rezultatai išsaugomi projekto aplanke, sugrupuoti pagal kamerą, o po to pagal failo formatą:

```
<project>/<camera-folder>/<format-folder>/<Product>_Images/
```

* **kameros aplankas** — `LATT-<sensor>-<lens>-F<filter>` skirtas „LATTICE“, `<model>_<filter>` (pvz., `Survey3N_RGN`) skirtas Survey3
* **formato aplankas** — `tiff16`, `tiff8`, `png8`, `jpg8` arba `tiff32`
* **produktų aplankai** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (visada po `tiff32`), `<INDEX>_Index_Images/`**Eksportuoti failai išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas, o ne failo pavadinimo priesaga.**Naudojant CLI, projekto aplankas sukuriama šalia įvesties aplanko, nebent nurodytumėte `-o`. Atkreipkite dėmesį, kad `chloros-cli process` vykdymas, kurio metu buvo užklausti produktai, bet nė vienas nebuvo įrašytas, išspausdina `Processing finished but wrote no image products.` ir**baigiasi nelygiu nuliui rezultatu**, todėl skriptai gali jį aptikti. Žr. [Išvesties vaizdų formatai](output-image-formats.md) ir [CLI nuorodą](reference/cli-reference.md).

</details>

<details>

<summary>Ar galiu redaguoti savo vaizdus prieš apdorojimą Chloros?</summary>

Ne. Chloros daro prielaidą, kad įvesties duomenys nebuvo pakeisti. Nepakeiskite failų pavadinimų.

</details>

<details>

<summary>Ar galiu nustatyti savo MAPIR ir Survey3 fotoaparatus į automatinę ekspoziciją ir apdoroti vaizdus Chloros?</summary>

Ne. Survey3 vaizdų duomenų rinkiniai turi turėti fiksuotą/užfiksuotą ekspoziciją, taigi negalima naudoti automatinio užrakto greičio ar automatinio ISO. Visi to paties kameros modelio vaizdai turi turėti identišką užrakto greitį ir ISO (ekspoziciją).

„LATTICE“ kameroms šis apribojimas netaikomas: „Chloros“ reguliuoja jų ekspoziciją realiuoju laiku („Smart AE“), o kiekvienas užfiksuotas vaizdas įrašo faktiškai naudotą ekspoziciją ir stiprinimą, į kuriuos atsižvelgia radiometrinė apdorojimo grandinė.

</details>

<details>

<summary>Ar „Chloros“ gali apdoroti ar analizuoti ortomozaikinius vaizdus?</summary>

Ne. Palaikomi tik atskiri „MAPIR“ fotoaparato vaizdai, o ne sujungti vaizdai, pavyzdžiui, ortomozaikinis žemėlapis.

</details>

<details>

<summary>Kaip galima pagreitinti „Chloros“ tikslų aptikimo etapą?</summary>

Failų naršyklės lentelėje iš anksto pažymėjus tikslinius vaizdus dešinėje skiltyje, „Chloros“ bus nurodyta ieškoti kalibravimo tikslų tik tuose vaizduose, o tai žymiai pagreitins apdorojimą.

</details>

<details>

<summary>Jei ketinu įkelti savo vaizdus į <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">„MAPIR Cloud“,</a> ar turėčiau juos apdoroti „Chloros“ prieš įkeliant?</summary>

Jei planuojate įkelti į mūsų internetinę apdorojimo platformą [„MAPIR Cloud“](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), prieš įkeliant nuotraukų neredaguokite. „Cloud“ atliks visus tuos pačius apdorojimo veiksmus ir dar daugiau.

</details>

<details>

<summary>Ar „MAPIR“ kada nors palaikys X funkciją? Labai norėčiau, kad „MAPIR“ siūlytų X.</summary>

Mums visada įdomu gauti atsiliepimus apie mūsų produktus. Jei pastebėjote problemą, susijusią su mūsų produktais, arba turite pasiūlymų, kaip galėtume juos patobulinti, prašome [SUSISIEKITE SU MUMIS](https://www.mapir.camera/community/contact) ir pasidalinti savo mintimis. Didžiąją dalį mūsų mokslinių tyrimų ir plėtros veiklos lemia noras išklausyti svarbiausius klientų poreikius.

</details>

<details>

<summary>Ar „Chloros“ veikia su „Linux“?</summary>

Taip! „Chloros“ 1.2.0 versija palaiko „Linux“ amd64 (x86_64) ir arm64 (NVIDIA Jetson JetPack 6) per `.deb` paketus. CLI ir Python SDK yra visiškai palaikomi Linux, įskaitant tiesioginį „LATTICE“ kameros ir DAQ jutiklio valdymą. Linux neturi grafinės vartotojo sąsajos — visa sąveika vyksta per [CLI](CLI.md) arba [Python SDK](api-python-sdk.md). Daugiau informacijos rasite [Linux apžvalgoje](linux/linux-overview.md).

</details>

<details>

<summary>Ar galiu paleisti „Chloros“ „NVIDIA Jetson“ platformoje?</summary>

Taip! „Chloros“ palaiko „NVIDIA Jetson“ platformas, įskaitant „Jetson Nano“, „Orin Nano“, „Orin NX“ ir „AGX Orin“, kuriose veikia „JetPack 6“. „Chloros“ automatiškai nustato jūsų „Jetson“ modelį ir optimizuoja jo apdorojimo strategiją. Įrengimo ir diegimo instrukcijas rasite [„NVIDIA Jetson“ vadove](linux/nvidia-jetson-guide.md).

</details>

<details>

<summary>Ar „Chloros“ automatiškai optimizuoja veikimą pagal mano aparatinę įrangą?</summary>

Taip! Chloros turi [dinaminio skaičiavimo pritaikymo](processing-architecture/dynamic-compute-adaptation.md) funkciją, kuri automatiškai aptinka jūsų CPU, GPU, RAM ir (Jetson įrenginiuose) temperatūros jutiklius. Tada ji pasirenka optimalų apdorojimo būdą – nuo `GPU_PARALLEL` sistemose su didele atmintimi iki `GPU_SINGLE` įrenginiuose su ribotais ištekliais ir iki `CPU_PARALLEL` sistemose be „NVIDIA“ GPU. Rankinis konfigūravimas nereikalingas.

</details>

<details>

<summary>Kas yra 4-siūlų apdorojimo kanalas?</summary>

„Chloros“ naudoja 4-siūlų konvejerinę architektūrą „Chloros+“ vartotojams: 1-asis srautas (aptikimas) įkelia vaizdus ir aptinka kalibravimo taškus, 2-asis srautas (kalibravimas) apskaičiuoja atspindžio kalibravimą, 3-asis srautas (apdorojimas) atlieka GPU pagreitintą „debayering“ ir indekso skaičiavimą, o 4-asis srautas (eksportavimas) įrašo išvesties failus. Siekiant maksimalaus našumo, keli vaizdai vienu metu gali būti apdorojami skirtinguose srautuose. Daugiau informacijos rasite skyriuje [Apdorojimo srautas](processing-architecture/processing-pipeline.md).

</details>

<details>

<summary>Kaip atlikti Chloros įdiegimo diagnostiką?</summary>

Naudokite komandą „`selftest`“, kad atliktumėte 7 žingsnių „smoke“ testą: versijos patikrinimas, prievadų prieinamumas, užkuro paleidimas, „API“ ryšio patikrinimas (`/api/test`), sistemos informacija (`/api/system-info` — GPU/CUDA/PyTorch), triukšmo šalinimo modelio buvimas ir CUDA bei triukšmo šalinimo parengtis:

```bash
chloros-cli selftest
```

Tai ypač naudinga „Linux“/„Jetson“ sistemose, siekiant patikrinti GPU ir CUDA nustatymus.

</details>
