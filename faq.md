---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# DUK

<details>

<summary>Ar galiu apdoroti vaizdus iš kamerų, kurios nėra MAPIR prekės ženklo, naudodamas Chloros?</summary>

Ne, Chloros palaiko tik MAPIR kamerų vaizdų apdorojimą — Survey3 ir „LATTICE“ serijų. Daugiau informacijos rasite [palaikomų kamerų modelių sąraše](supported-cameras.md). „MAPIR Cloud“ siūlome kitų kamerų vaizdų apdorojimą, pilną sąrašą rasite [čia](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Ar Chloros palaiko „LATTICE“ kameras?</summary>

Taip. Chloros 1.3.0 versija visapusiškai palaiko „LATTICE M3C“ ir „M3M“ kamerų modulius: **valdymą realiuoju laiku**— aptikimą, prisijungimą, peržiūrą ir vaizdo įrašymą per GUI skirtuką „Cameras“ (`chloros-cli lattice`) arba Python SDK, įskaitant sinchronizuotus kelių kamerų masyvus su PTP laiko sinchronizavimu — ir**visapusišką radiometrinį apdorojimą** užfiksuotų vaizdų (neapdoroti duomenys → debayeringas → spinduliavimas → atspindys → indeksas). Žr. [Palaikomos kameros](supported-cameras.md) ir [„LATTICE“ vadovą](lattice/README.md).

</details>

<details>

<summary>Ar galiu kalibruoti savo vaizdus pagal atspindžio koeficientą be kalibravimo taikinio?</summary>

**Survey3:** Ne. Jei nekalibravimo taikinio nuotrauka nebuvo užfiksuota tuo pačiu metu, kai buvo fotografuojami kiti vaizdai, negalėsite susieti vaizdo pikselių verčių su žinomu atspindžio procentu. Jei taip pat neįtrauksite MAPIR šviesos jutiklio duomenų, aplinkos šviesos spektras nebus išmatuotas, o atspindžio rezultatai nebus tikslūs.

**LATTICE:** Taip. Atspindžio koeficientą galima susieti su žemyn nukreiptu spinduliavimo intensyvumu, išmatuotu DAQ šviesos jutikliu, o ne plokštele (ρ = π·L/E). Kai kadre *yra* kokybės užtikrinimo reikalavimus atitinkantis taikinys, jis pagal numatytuosius nustatymus tampa absoliučiu etalonu (`--reflectance-source auto`), išskyrus atvejus, kai jis atitinka reikalavimus su žymekliu – tuomet taikinys ir DAQ grandinės konkuruoja dėl išmatuoto balo. Viena išimtis: „F988 atspindžio koeficientas kalibruojamas naudojant kadre esantį atspindžio plokštelę: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros taiko jūsų naujausią plokštelės užfiksavimą ir išlaiko jį tarp plokštelės stebėjimų.“ Žr. [Kalibravimo taikiniai](calibration-targets.md).

</details>

<details>

<summary>Ar man reikia DAQ šviesos jutiklio?</summary>

Ne, jei matuojate spinduliavimą: „LATTICE“ spinduliavimo eksportai gaunami remiantis kiekvienos kameros gamykliniu radiometriniu kalibravimu, todėl nereikia nei DAQ jutiklio, nei taikinio. **Atspindžio**matavimui reikalingas aplinkos šviesos etalonas – tai gali būti DAQ šviesos jutiklio žemyn nukreiptas matavimas arba kadre esantis kalibravimo taikinys. DAQ jutiklis leidžia gauti kalibruotą atspindžio koeficientą**net neįdedant jokių plokščių į kadrą**. Įrašyti `.daq` failai automatiškai susiejami su jūsų vaizdais pagal laiko žymą. Žr. [Kalibravimo taikiniai](calibration-targets.md) ir [CLI nuorodą](reference/cli-reference.md).

</details>

<details>

<summary>Ar galiu naudoti Chloros su AI asistentu (Claude, ChatGPT ir pan.)?</summary>

Taip — šis vadovas ir CLI/SDK yra pritaikyti šiam tikslui:

* Visas vadovo rodyklė pateikiama adresu `https://mapir.gitbook.io/chloros/llms.txt`, kad AI asistentai galėtų rasti kiekvieną puslapį.
* Kiekvieno puslapio neapdorotas „Markdown“ kodas yra prieinamas jo mažosiomis raidėmis parašyto puslapio URL, prie kurio pridėta `.md` (pavyzdžiui, `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI žinynas](reference/cli-reference.md) ir [SDK žinynas](reference/sdk-reference.md) yra parengti didelių kalbos modelių (LLM) naudojimui: juose pateikiami tikslūs parametrai, numatytieji nustatymai, išėjimo semantika ir komandos, kurias galima nukopijuoti ir įklijuoti.

Kaip nukreipti savo asistentą į Chloros, skaitykite skyriuje [AI asistentai](ai-assistants.md).

</details>

<details>

<summary>Kur patenka mano apdoroti išvesties failai?</summary>

Rezultatai išsaugomi projekto aplanke, sugrupuoti pagal kamerą, o po to – pagal failo formatą:

```
<project>/<camera-folder>/<format-folder>/<Product>_Images/
```

* **kameros aplankas** — „`LATT-<sensor>-<lens>-F<filter>`“ skirtas „LATTICE“, „`<model>_<filter>`“ (pvz., „`Survey3N_RGN`“) skirtas Survey3
* **formato aplankas** — „`tiff16`“, „`tiff8`“, „`png8`“, `jpg8` arba `tiff32`
* **produktų aplankai** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (visada po `tiff32`), `<INDEX>_Index_Images/`
* **`clamp_masks/`** — tik „LATTICE“ multispektrinė informacija: kaukės pagal pikselius šalia produktų aplankų, žr. toliau**Eksportuoti failai išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas, o ne failo pavadinimo priesaga.**Naudojant CLI, projekto aplankas sukuriama jūsų numatytame projekto aplanke (`Documents/MAPIR/Chloros Projects`, jei jo nekeičiate), nebent nurodytumėte `-o`; santykinis `-o` laikomas santykiniu atžvilgiu į aplanką, iš kurio vykdote komandą. Atkreipkite dėmesį, kad komandos `chloros-cli process` vykdymas, kurio metu buvo užklausti produktai, bet nė vienas nebuvo įrašytas, išveda `Processing finished but wrote no image products.` ir**baigiasi nelygaus nulio rezultatu**, todėl skriptai gali tai aptikti. Žr. [Išvesties vaizdo formatai](output-image-formats.md) ir [CLI žinyną](reference/cli-reference.md).

</details>

<details>

<summary>Kas yra „clamp_masks“ aplankai ir *_clampmask.tif failai?</summary>

Nauja funkcija versijoje 1.3.0. Daugiaspektrinės „LATTICE M3C“ kameros spinduliavimo ir atspindžio produktai atlieka 3×3 juostų atskyrimą, o ten, kur juostoje gaunama neigiama reikšmė, Chloros tą reikšmę apriboja iki nulio. Produktas, kuriame yra bent vienas apribotas pikselis, gauna vienos juostos „`uint8`“ kaukę, kuri žymi, kurie pikseliai ir juostos buvo apriboti (bitas 0 = produkto R kanalas, bitas 1 = G, bitas 2 = B). Kaukės išsaugomos „`clamp_masks/`“ aplanke **šalia** produkto aplankų — pavyzdžiui, „`tiff16/clamp_masks/Reflectance_Calibrated_Images/<name>_clampmask.tif`“ — o produkto `Chloros:UnmixClampMask` XMP žymė nurodo jo kaukę (arba nurodo `none`).

Tai nėra scenos vaizdai: „Chloros“ importavimo programa jų neįtraukia, o kadangi jie niekada nėra laikomi produkto aplanke, galite perduoti produkto aplanką „Pix4D“ ar „Metashape“ tokiu, koks jis yra. Žr. [Fiksavimo kaukės](output-image-formats.md#clamp-masks).

</details>

<details>

<summary>Kodėl Chloros rodo pranešimą „Projekto aplankas yra per gilus šiam kompiuteriui“?</summary>

Tai pasitaiko tik Windows sistemoje. Windows iš pradžių yra išjungtas ilgų kelių rodymas, todėl joks failo kelias negali būti ilgesnis nei 260 simbolių. Chloros produktai yra išdėstyti iki maždaug 130 simbolių žemiau projekto aplanko, todėl Chloros prieš apdorojimą atmeta gilesnį projekto aplanką – užuot paleidęs programą ir nieko neįrašęs, kaip galėjo nutikti iki 1.3.0 versijos. Pranešime nurodytas ilgiausias tinkamas projekto aplankas.

Išspręskite problemą vienu iš šių būdų:

* Naudokite trumpesnį projekto aplanką (**Darbinis katalogas** projekto nustatymuose arba „`-o`“ / „`chloros-cli set-project-folder`“ komandinės eilutės sąsajoje (CLI)).
* Įjunkite Windows ilgus kelius ir iš naujo paleiskite Chloros. Tai atlieka Chloros diegimo programos parinktis **Įjungti Windows ilgus kelius** (pagal numatytuosius nustatymus pažymėta); jei ją atžymėjote, paleiskite diegimo programą iš naujo.

Žr. [Apdorojimo pradžia](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).

</details>

<details>

<summary>Ar galiu redaguoti savo vaizdus prieš apdorojimą Chloros?</summary>

Ne. Chloros laiko, kad įvesties duomenys nebuvo pakeisti. Nepakeiskite failų pavadinimų.

</details>

<details>

<summary>Ar galiu nustatyti savo „MAPIR Survey3“ kameras į automatinę ekspoziciją ir apdoroti vaizdus Chloros programoje?</summary>

Ne. Survey3 vaizdų duomenų rinkiniai turi turėti fiksuotą/užfiksuotą ekspoziciją, taigi negalima naudoti automatinio užrakto greičio ar automatinio ISO. Visi to paties kameros modelio vaizdai turi turėti identišką užrakto greitį ir ISO (ekspoziciją).

„LATTICE“ kameroms šis apribojimas netaikomas: Chloros ekspoziciją reguliuoja realiuoju laiku („Smart AE“), o kiekvienas kadras įrašo faktiškai naudotą ekspoziciją ir stiprinimą, į kuriuos atsižvelgia radiometrinė apdorojimo grandinė.

</details>

<details>

<summary>Ar Chloros gali apdoroti ar analizuoti ortomozaikinius vaizdus?</summary>

Ne. Palaikomi tik atskiri MAPIR fotoaparato vaizdai, o ne sujungti vaizdai, pavyzdžiui, ortomozaikinis žemėlapis.

</details>

<details>

<summary>Kaip galiu pagreitinti Chloros tikslų aptikimo etapą?</summary>

Failų naršyklės lentelėje iš anksto pažymėjus tikslinius vaizdus dešinėje skiltyje, Chloros bus nurodyta ieškoti kalibravimo tikslų tik tuose vaizduose, o tai žymiai pagreitins apdorojimą.

</details>

<details>

<summary>Jei ketinu įkelti savo vaizdus į „<a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud“,</a> ar turėčiau juos apdoroti Chloros prieš įkeliant?</summary>

Jei planuojate įkelti vaizdus į mūsų internetinę apdorojimo platformą [„MAPIR Cloud“](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), prieš įkeliant jų neredaguokite. „Cloud“ atliks visą tą patį apdorojimą ir dar daugiau.

</details>

<details>

<summary>Ar MAPIR kada nors palaikys X funkciją? Labai norėčiau, kad MAPIR siūlytų X.</summary>

Mums visada įdomu gauti atsiliepimus apie mūsų produktus. Jei pastebėjote problemą, susijusią su mūsų produktais, arba turite pasiūlymų, kaip galėtume juos patobulinti, prašome [SUSISIEKTI SU MUMIS](https://www.mapir.camera/community/contact) ir pasidalinti savo mintimis. Didžiąją dalį mūsų mokslinių tyrimų ir plėtros veiklos lemia mūsų klientų svarbiausių poreikių įvertinimas.

</details>

<details>

<summary>Ar Chloros veikia Linux sistemoje?</summary>

Taip! Chloros 1.3.0 palaiko Linux amd64 (x86_64) ir arm64 (NVIDIA Jetson JetPack 6) per `.deb` paketus. CLI ir Python SDK yra visiškai palaikomi Linux sistemoje, įskaitant tiesioginį „LATTICE“ kameros ir DAQ jutiklių valdymą. Linux sistemai nėra grafinės vartotojo sąsajos (GUI) – visa sąveika vyksta per [CLI](CLI.md) arba [Python SDK](api-python-sdk.md). Išsamią informaciją rasite [Linux apžvalgoje](linux/linux-overview.md).

</details>

<details>

<summary>Ar galiu paleisti Chloros „NVIDIA Jetson“ platformoje?</summary>

Taip! Chloros palaiko „NVIDIA Jetson“ **„Orin“ serijos** įrenginius — „Orin Nano“, „Orin NX“ ir „AGX Orin“ — su „JetPack 6“. Senesni modeliai („Jetson Nano“, „TX2“, „Xavier NX“) negali paleisti „JetPack 6“ ir nėra palaikomi dabartiniame pakete. Chloros automatiškai nustato jūsų „Jetson“ modelį ir optimizuoja jo apdorojimo strategiją. „Jetson“ pakete naudojama „JetPack 6“ CUDA versija „PyTorch“, todėl apdorojimas vyksta „Jetson“ GPU. Įrengimo ir diegimo instrukcijas rasite [„NVIDIA Jetson“ vadove](linux/nvidia-jetson-guide.md).

</details>

<details>

<summary>Ar Chloros automatiškai optimizuoja veikimą pagal mano aparatinę įrangą?</summary>

Taip! Chloros turi [dinaminio skaičiavimo pritaikymo](processing-architecture/dynamic-compute-adaptation.md) funkciją, kuri automatiškai aptinka jūsų CPU, GPU, RAM ir (Jetson įrenginiuose) temperatūros jutiklius. Tada ji pasirenka optimalų apdorojimo būdą — nuo `GPU_PARALLEL`, skirto GPU su didele atmintimi, iki `GPU_SINGLE`, skirto mažesniems GPU, ir iki `CPU_PARALLEL`, skirto sistemoms be CUDA GPU, kurį gali naudoti „PyTorch“. Rankinis konfigūravimas nereikalingas.

</details>

<details>

<summary>Kas yra 4-siūlų apdorojimo srautas?</summary>

Chloros naudoja 4-siūlų konvejerinę architektūrą „Chloros+“ vartotojams: 1-asis srautas (aptikimas) įkelia vaizdus ir aptinka kalibravimo taškus, 2-asis srautas (kalibravimas) apskaičiuoja atspindžio kalibravimą, 3-asis srautas (apdorojimas) atlieka GPU pagreitintą „debayering“ ir indekso skaičiavimą, o 4-asis srautas (eksportavimas) įrašo išvesties failus. Siekiant maksimalaus našumo, keli vaizdai vienu metu gali būti apdorojami skirtinguose sruogose. Daugiau informacijos rasite skyriuje [Apdorojimo srautas](processing-architecture/processing-pipeline.md).

</details>

<details>

<summary>Kaip atlikti Chloros įdiegimo diagnostiką?</summary>

Naudokite komandą `selftest`, kad atliktumėte 7 žingsnių „smoke test“: versija, prievadų prieinamumas, užkulisio paleidimas, API ryšys (`/api/test`), sistemos informacija (`/api/system-info` — GPU/CUDA/PyTorch), triukšmo šalinimo modelio buvimas ir CUDA bei triukšmo šalinimo parengtis:

```bash
chloros-cli selftest
```

Tai ypač naudinga Linux sistemose, siekiant patikrinti GPU ir CUDA nustatymus. „Jetson“ įrenginyje `CUDA: False` paprastai reiškia, kad trūksta „JetPack 6“ CUDA ir cuDNN bibliotekų: jas įdiekite naudodami komandą `sudo apt install nvidia-jetpack`.

</details>
