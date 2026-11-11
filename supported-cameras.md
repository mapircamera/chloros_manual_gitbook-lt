---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/supported-cameras
---

# Palaikomos kameros

Chloros apdoroja vaizdus iš dviejų MAPIR kamerų serijų **visose platformose** (Windows, Linux amd64 ir Linux arm64/„Jetson“):

* **Survey3** — Survey3W (plačiakampės) ir Survey3N (siaurakampės) kameros. Įvestis: `RAW+JPG`.
* **LATTICE**— M3C ir M3M multispektrinių kamerų moduliai. Įvestis: `.tif`/`.tiff` įrašai. „LATTICE“ kameras taip pat galima**valdyti realiuoju laiku** iš Chloros — per GUI skirtuką „Cameras“ (Windows) arba `chloros-cli lattice` / Python SDK (Windows ir Linux) — įskaitant sinchronizuotus kelių kamerų masyvus. Žr. [„LATTICE“ vadovą](lattice/).

Apdorojimo grandinė taip pat priima `.dng` įvesties failus.

## Survey3

<table data-header-hidden><thead><tr><th width="156">Gamintojas</th><th width="250">Kameros modelis</th><th width="138">Filtro modelis</th><th width="187">Vaizdo tipas</th></tr></thead><tbody><tr><td><strong>Gamintojas</strong></td><td><strong>Fotoaparato modelis</strong></td><td><strong>Filtro modelis</strong></td><td><strong>Vaizdo tipas</strong></td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>OCN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RE</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NIR</td><td>RAW+JPG, JPG</td></tr></tbody></table>## LATTICE

„LATTICE“ serija – tai modulinė multispektrinė kamerų sistema, sukurta remiantis „Sony IMX265“ globalinio užrakto jutikliu (3,1 MP, 3,45 µm pikseliai). Kiekviena kamera savo identifikatorių saugo kaip modelio eilutę:

```
<sensor>-<lens>-F<filter>        e.g.  M3C-L41-FRGN,  M3M-L87-F550
```

Chloros ją rodo su priešdėliu „`LATT-`“ (pavyzdžiui, „`LATT-M3M-L41-F550`“), o modelio eilutė lemia viską tolesniuose etapuose — jutiklio profilis, juostų išdėstymas ir kalibravimas nustatomi automatiškai; nereikia nieko konfigūruoti kiekvienai kamerai atskirai. Objektyvo numeris reiškia **horizontalų matymo kampą laipsniais**: „`L41`“ = siauras 41°, „`L87`“ = platus 87°.

Yra dvi jutiklio konfigūracijos:

| Konfigūracija | Jutiklis      | Filtro tipas                           | Juostų skaičius vienai kamerai                                                        |
| ------------- | ----------- | ------------------------------------- | ----------------------------------------------------------------------- |
| **M3C**       | „Bayer“ spalvotas | Trigubas juostinis filtras                       | 3 spektriniai diapazonai iš vienos ekspozicijos                                 |
| **M3M**       | Monochrominis  | Vienas siaurajuostis interferencinis filtras | 1 kalibruotas diapazonas — sujunkite kelias M3M kameras augmenijos indeksams gauti |

### M3C (Bayer) filtro parinktys

| Filtras | Juostos (pavadinimas @ centras nm / FWHM nm)       |
| ------ | ---------------------------------------- |
| `FRGB` | Blue 475/30 · Green 550/30 · Red 625/30  |
| `FRGN` | Red 660/21 · Green 550/30 · NIR 850/30   |
| `FOCN` | Orange 618,4/46 · Cyan 494,1/40 · NIR 821,6/54 (išmatuota — žr. žemiau) |
| `FNGB` | Blue 475/30 · Green 550/30 · NIR 850/30  |

`FOCN` atveju šios reikšmės yra **išmatuotas** filtro pusės maksimumo juostos plotis (Chloros 1.3.0 ir vėlesnėse versijose; ankstesnėse versijose buvo naudojamos techninių duomenų lape nurodytos reikšmės 615/21 · 490/38 · 808/14). Juostų pavadinimai išlieka tokie patys: `Orange_615`, `Cyan_490` ir `NIR_808`, o išmatuotas centras ir plotis yra tie, kuriuos Chloros įrašo į kiekvieną kalibruotą eksportą.

### M3M (mono) filtrų katalogas — 23 SKU

F-skaičius yra SKU žymė; išmatuotas juostos plotis (įrašytas į kiekvieną kalibruotą eksportą) yra kiekvienos partijos filtro skenavimo duomenys:

| SKU    | Centras (nm, išmatuotas) | FWHM kraštai (nm) | Plotis (nm) |
| ------ | --------------------- | --------------- | ---------- |
| F385   | 379,4                 | 367–392         | 25         |
| F405   | 403,9                 | 390–417         | 27         |
| F450   | 443,7                 | 430–458         | 28         |
| F485   | 489,7                 | 478–502         | 24         |
| F520   | 519,9                 | 504–536         | 32         |
| F550   | 548,4                 | 531–566         | 35         |
| F590   | 589,0                 | 570–608         | 38         |
| F615   | 623,8                 | 614–634         | 20         |
| F632   | 633,4                 | 616–651         | 35         |
| F650   | 651,1                 | 636–666         | 30         |
| F685   | 686,2                 | 675–698         | 23         |
| F715   | — (nominalus)           | 706–724         | 18         |
| F725   | 725.2                 | 712–738         | 26         |
| F750   | 746.0                 | 729–763         | 34         |
| F780   | 775.1                 | 754–796         | 42         |
| F808   | 810.3                 | 789–832         | 43         |
| F832   | 826,1                 | 810–843         | 33         |
| F850   | 846,5                 | 828–865         | 37         |
| F880   | — (nominalus)           | 867–893         | 26         |
| F905   | — (nominalus)           | 892–920         | 28         |
| F940   | 940,6                 | 923–958         | 35         |
| F950   | 945,1                 | 929–961         | 32         |
| F988 † | 985,3                 | 968–1003        | 35         |

_„Juostų kraštai matuojami kaip pusės maksimumo plotis, remiantis „MAPIR “ atliktais kiekvienos partijos filtrų skenavimais — tie patys dydžiai, kuriuos Chloros įrašo į kiekvieną kalibruotą eksportą.“_ „— (nominalus)“ = partijos skenavimas dar neatliktas; tų SKU atveju nurodytas centras yra SKU numeris, o plotis – gamintojo pateiktas skaičius.

† „F988 atspindžio koeficientas kalibruojamas naudojant atspindžio plokštelę, esančią matymo lauke: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros taiko jūsų naujausią plokštelės nuskaitymo duomenį ir išlaiko jį tarp plokštelės matavimų.“ Žr. [Kalibravimo taikiniai](calibration-targets.md).

Dėl kameros valdymo realiuoju laiku, matricos, tinklo nustatymų ir radiometrinio apdorojimo grandinės žr. [„LATTICE“ vadovą](lattice/).
