# „LATTICE“ kameros

„LATTICE“ – tai „MAPIR“ modulinė daugiaspektrinė kamerų sistema, skirta vaizdams žemės ūkio ir mokslo srityse fiksuoti. Kiekviena „LATTICE“ kamera pagrįsta „Sony IMX265“ globalinio užrakto jutikliu (**3,1 MP, 3,45 µm pikseliai**) ir jungiasi per „Ethernet“ kaip**„GigE Vision“** įrenginys.

„Chloros 1.2.0“ valdo „LATTICE“ kameras realiuoju laiku – aptikimą, tiesioginį peržiūrėjimą, vaizdo fiksavimą ir sinchronizuotus daugiakamerinius masyvus – iš trijų sąsajų:

| Sąsaja    | Kur                                                          | Platformos                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | Chloros šoninės juostos skirtukas **„Kameros“**                         | Windows 10/11 x64                                        |
| CLI        | `chloros-cli lattice` komandų šeima                           | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |
| Python SDK | `chloros_sdk.connect_camera()` / `chloros_sdk.connect_array()` | Windows 10/11 x64, Linux x86_64, Linux aarch64 (Jetson) |

> **Ieškote įrangos?**Kameros moduliai, objektyvai, filtrai ir juostos, rėmeliai ir tvirtinimo elementai, kabeliai, PoE ir trigerių laidai aprašyti [**„LATTICE“ vartotojo vadove**](https://mapir.gitbook.io/lattice-camera). Šiame skyriuje aptariamas kamerų valdymas naudojant Chloros.

„LATTICE“ įrašai yra standartiniai `.tif`/`.tiff` failai, o Chloros juos visada apdoroja pradedant nuo neapdorotų įrašų. Visą komandą ir paviršių rasite [CLI žinyne](../reference/cli-reference.md) ir [SDK žinyne](../reference/sdk-reference.md), kur rasite išsamią komandą ir API paviršių.

## Dvi jutiklių konfigūracijos

| Konfigūracija | Jutiklis       | Filtras                                | Ką pateikia viena kamera                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| „Bayer“ spalvota | trigubas juostinis filtras                |**Trys kalibruotos juostos iš vienos ekspozicijos**                 |
| **M3M**| Monochrominė   | vienas siaurajuostis interferencinis filtras |**Viena kalibruota juosta**; indeksams gauti sujunkite kelias M3M kameras |

Kadangi M3M kamera yra monochrominė su vienu filtru, kiekvienas diapazonas gauna savo ekspoziciją. M3C kamera apima visus tris savo diapazonus su viena jutiklio ekspozicija.

## Modelio eilutės ir pavadinimai

Kiekviena kamera savo identifikatorių saugo „GenICam“ `DeviceUserID` kaip modelio eilutę:

```
<sensor>-<lens>-F<filter>       e.g.  M3C-L41-FRGN,  M3M-L87-F450
```

Chloros rodo ją su priešdėliu `LATT-` (pavyzdžiui, `LATT-M3M-L87-F450`). Ta pati eilutė „`LATT-…`“ įrašoma į kiekvieno eksporto EXIF žymą „`Model`“ ir naudojama kaip fotoaparato išvesties aplanko pavadinimas apdorotuose projektuose.

| Komponentas | Vertės                                                   | Reikšmė                                                                                            |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Jutiklis    | `M3C` / `M3M`                                            | „Bayer“ spalvotas / nespalvotas                                                                          |
| Objektyvas    | `L41` / `L87`                                            | Skaičius reiškia **horizontalų matymo kampą laipsniais**: L41 = siauras (41°), L87 = platus (87°)    |
| Filtras    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) arba `F<nm>` (M3M) | Žr. [Filtrai ir spektriniai diapazonai](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Modelio eilutė lemia viską tolesniuose etapuose: Chloros nustato jutiklio profilį, juostų išdėstymą ir gamyklinį kalibravimą pagal `DeviceUserID` + `DeviceSerialNumber`. Nereikia nieko konfigūruoti kiekvienai kamerai atskirai — žr. [Kamerų prijungimas](connecting.md).

## Filtrai ir juostos

Juostų centrai, FWHM kraštai ir visas 23 SKU M3M katalogas yra produkto specifikacijos, todėl jie pateikiami aparatinės įrangos vadove: [**Filtrai ir spektrinės juostos**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Kas svarbu programinės įrangos atžvilgiu: filtro kodas modelio eilutėje nulemia, kokius produktus Chloros gali sukurti. RGB filtro kameros (`FRGB`) generuoja tik debayered ir peržiūros produktus — spinduliavimas ir atspindys pagal juostas nėra reikšmingi plačiajuosčio sensoriaus atveju, todėl Chloros juos praleidžia ir apie tai praneša. Kiekvienas kitas filtras pateikia visą spinduliavimo → atspindžio → indekso grandinę.

## Radiometrinis kalibravimas iš pirmo žvilgsnio

Kiekviena „LATTICE“ kamera gamykloje yra atskirai kalibruojama pagal NIST atsekamą grandinę ir pristatoma su kiekvienai kamerai skirtu sertifikatu. Ką tai apima, kaip tai matuojama ir kokį tikslumą galite nurodyti, rasite aparatinės įrangos vadove: [**Gamyklinė radiometrinė kalibracija**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Programinės įrangos atžvilgiu svarbu tai, kad Chloros nustato tinkamą kalibravimą, kai kamera prisijungia, ir įtvirtina taikomus koeficientus kiekviename eksportuojamame faile — žr. [Kamerų prijungimas](connecting.md).

## Šiame skyriuje

* [Kamerų prijungimas](connecting.md) — automatinis aptikimas, GUI prijungimo dialogo langas, CLI/SDK ekvivalentai, taip pat kaip nustatoma gamyklinė kalibracija (kameros pakete ar debesyje), kai kamera prisijungia.

Kitos „LATTICE“ temos — kamerų nustatymai ir valdymas realiuoju laiku, fiksavimo režimai, kelių kamerų masyvai bei mono (M3M) apdorojimas ir indeksai — aptariamos atskiruose šio vadovo skyriuose, o išsamus komandų sąrašas pateikiamas [CLI nuorodoje](../reference/cli-reference.md) ir [SDK nuorodoje](../reference/sdk-reference.md).
