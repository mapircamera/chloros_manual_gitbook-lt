# „LATTICE“ kameros

„LATTICE“ – tai MAPIR modulinė multispektrinė kamerų sistema, skirta žemės ūkio ir moksliniams vaizdams fiksuoti. Kiekviena „LATTICE“ kamera pagaminta naudojant „Sony IMX265“ globalinio užrakto jutiklį (**3,1 MP, 3,45 µm pikseliai**) ir prijungiama per Ethernet kaip**„GigE Vision“** įrenginys.

Chloros 1.3.0 valdo „LATTICE“ kameras realiuoju laiku — paiešką, tiesioginį peržiūrėjimą, vaizdo fiksavimą ir sinchronizuotus kelių kamerų masyvus — iš trijų sąsajų:

| Sąsaja    | Kur                                                          | Platformos                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | Chloros šoninės juostos skirtukas **„Kameros“**                         | Windows 10/11 x64                                        |
| CLI        | „`chloros-cli lattice`“ komandų šeima                           | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |
| Python SDK | „`chloros_sdk.connect_camera()`“ / „`chloros_sdk.connect_array()`“ | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |

> **Ieškote įrangos?**Kameros moduliai, objektyvai, filtrai ir juostos, rėmeliai ir tvirtinimo elementai, kabeliai, PoE ir trigerio laidai aprašyti [**„LATTICE“ vartotojo vadove**](https://mapir.gitbook.io/lattice-camera). Šiame skyriuje aptariamas kamerų valdymas iš Chloros.

„LATTICE“ įrašai yra standartiniai `.tif`/`.tiff` failai, o Chloros juos visada apdoroja pradedant nuo neapdorotų įrašų. Visą komandų ir API sąrašą rasite [CLI žinyne](../reference/cli-reference.md) ir [SDK žinyne](../reference/sdk-reference.md).

## Dvi jutiklių konfigūracijos

| Konfigūracija | Jutiklis       | Filtras                                | Ką pateikia viena kamera                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| „Bayer“ spalvota | trigubas juostinis filtras                |**Trys kalibruotos juostos iš vienos ekspozicijos**                 |
| **M3M**| Monochrominė   | vienas siaurajuostis interferencinis filtras |**Viena kalibruota juosta**; indeksams gauti sujunkite kelias M3M kameras |

Kadangi M3M kamera yra monochrominė ir turi vieną filtrą, kiekvienai juostai skiriama atskira ekspozicija. M3C kamera visus tris savo diapazonus aprėpia vienu jutiklio ekspozicijos kadru.

## Modelio eilutės ir pavadinimai

Kiekviena kamera savo identifikatorių saugo „GenICam“ sistemoje kaip modelio eilutę:

```
<sensor>-<lens>-F<filter>       e.g.  M3C-L41-FRGN,  M3M-L87-F450
```

Chloros jį rodo su priešdėliu „`LATT-`“ (pavyzdžiui, „`LATT-M3M-L87-F450`“). Ta pati eilutė „`LATT-…`“ įrašoma į kiekvieno eksportuojamo failo EXIF žymą „`Model`“ ir naudojama kaip fotoaparato išvesties aplanko pavadinimas apdorotuose projektuose.

| Komponentas | Vertės                                                   | Reikšmė                                                                                            |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Jutiklis    | `M3C` / `M3M`                                            | „Bayer“ spalvotas / nespalvotas                                                                          |
| Objektyvas | `L41` / `L87`                                            | Skaičius reiškia **horizontalų matymo kampą laipsniais**: L41 = siauras (41°), L87 = platus (87°)    |
| Filtras    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) arba `F<nm>` (M3M) | Žr. [Filtrai ir spektriniai juostos](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Modelio eilutė lemia viską tolesniuose etapuose: Chloros nustato jutiklio profilį, juostų išdėstymą ir gamyklinį kalibravimą pagal `DeviceUserID` + `DeviceSerialNumber`. Nereikia nieko konfigūruoti kiekvienai kamerai atskirai — žr. [Kamerų prijungimas](connecting.md).

## Filtrai ir juostos

Juostų centrai, FWHM kraštai ir visas 23 SKU M3M katalogas yra produkto specifikacijos, todėl jos pateikiamos aparatinės įrangos vadove: [**Filtrai ir spektrinės juostos**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Kas svarbu programinės įrangos atžvilgiu: filtro kodas modelio eilutėje nulemia, kokius produktus Chloros gali sukurti. RGB filtrų kameros (`FRGB`) pateikia tik debayeringo ir peržiūros produktus — spinduliavimas ir atspindys pagal juostą nėra reikšmingi plačiajuosčiam jutikliui, todėl Chloros juos praleidžia ir apie tai informuoja. Kiekvienas kitas filtras pateikia visą grandinę: spinduliavimas → atspindys → indeksas.

## Radiometrinis kalibravimas iš pirmo žvilgsnio

Kiekviena „LATTICE“ kamera gamykloje yra individualiai kalibruojama pagal NIST atsekamą grandinę ir pristatoma su kiekvienai kamerai skirtu sertifikatu. Ką tai apima, kaip tai matuojama ir kokį tikslumą galite nurodyti, rasite aparatinės įrangos vadove: [**Gamyklinis radiometrinis kalibravimas**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Kalbant apie programinę įrangą, svarbu tai, kad Chloros nustato tinkamą kalibravimą, kai kamera prisijungia, ir įtvirtina taikomus koeficientus kiekviename eksporte — žr. [Kamerų prijungimas](connecting.md).

## Šiame skyriuje

* [Kamerų prijungimas](connecting.md) — automatinis aptikimas, GUI prijungimo dialogas, CLI / SDK ekvivalentai ir kaip nustatoma gamyklinė kalibracija (kameros pakete ar debesyje), kai kamera prisijungia.
* [Kameros nustatymai](camera-settings.md) — visi „Kameros“ skirtuko valdikliai: kamerų sąrašas, nustatymų langas kiekvienai kamerai ir masyvo nustatymų langas (įskaitant ROI redaktorių ir suderinimą).
* [Įrašymo nustatymai ir režimai](capture.md) — „Įrašyti viską“, pavienis / nepertraukiamas / intervalinis įrašymas, eksporto tipai ir įrašų aplanko struktūra.
* [Daugiakameriniai masyvai](arrays.md) — masyvo prijungimo vedlys, sinchronizavimo kabelio zondas, pralaidumas, PTP ir tiesioginis masyvo vaizdas.
* [Mono kameros ir augmenijos indeksai](mono-indices.md) — M3M kameros ir suderinimo → sluoksnių sujungimo → indeksavimo darbo eiga.

Išsamus komandų sąrašas pateikiamas [CLI žinyne](../reference/cli-reference.md) ir [SDK žinyne](../reference/sdk-reference.md).
