---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Išvesties vaizdo formatai

Chloros eksportuoja apdorotus produktus keturiais failų formatais. Pasirinkite formatą projekto nustatymuose (GUI), naudodami `--format` (CLI) arba `export_format` (SDK). CLI ir SDK priima tikslias žemiau pateiktas eilutes.

| Formato eilutė | Plėtinys | Pikselių tipas | Pikselių diapazonas | Pastabos |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(numatyta)* | `.tif` | uint16 skaitmeninis skaičius | 0 – 65535 | Rekomenduojama fotogrametrijai / GIS. |
| `TIFF (8-bit)` | `.tif` | uint8 skaitmeninis skaičius | 0 – 255 | Skirta visiškai 8 bitų įrašams, kurie siūlo tik 8 bitų formatus: Chloros nepadidina bitų gylio. |
| `TIFF (32-bit, Percent)` | `.tif` | float32 | 0,0 – 1,0 | 1,0 = 100 % atspindžio koeficientas. Kai kurios programos negali skaityti TIFF failų su slankiuoju kableliu; failai yra didesni. |
| `PNG (8-bit)` | `.png` | uint8 skaitmeninis skaičius | 0 – 255 | Suspaudimas be nuostolių, tinkamas peržiūrai internete ir vizualizavimui. |
| `JPG (8-bit)` | `.jpg` | „uint8“ skaitmeninis skaičius | 0 – 255 | Suspaudimas su nuostoliais, mažiausi failai. |

## Kur saugomi išvesties failai

Produktai įrašomi į projekto aplanką, sugrupuoti pagal kamerą, o po to – pagal failo formatą:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera (model+lens+filter)
    ├── tiff16/                          # follows --format: tiff16, tiff8, png8, jpg8, or tiff32
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one <INDEX>_Index_Images/ folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

„LATTICE“ kameros aplankas yra `LATT-<sensor>-<lens>-F<filter>`, o Survey3 – `<model>_<filter>` (pvz., `Survey3N_RGN`). **Kiekvienas eksportuotas produktas išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas, o ne failo pavadinimo priesaga.** Visas taisykles rasite skyriuje [Kur saugomi išvesties failai](reference/cli-reference.md) CLI žinyne.

## „LATTICE“ produktai (fiksavimo ir eksporto lygiai)

Vienas „LATTICE“ neapdorotas kadras vienu praėjimu išskaidomas į visus užsakytus produktus. Kiekvienam produkto tipui yra skirtas atskiras perjungiklis (GUI žymės langeliai arba CLI: `--debayered` / `--preview` / `--radiance` / `--reflectance`, visi pagal numatytuosius nustatymus įjungti):

| Lygis | Turinys | Duomenų tipas |
| --- | --- | --- |
| `raw` | Tiesiogiai iš jutiklio gauti „Bayer“ duomenys (monochromatinės kameros: vienas dažnių juostos). Apdorojimas visada prasideda nuo neapdorotų duomenų. | Kaip užfiksuota |
| `debayered` | Linijinis demozėjimas — 3 kanalai M3C, 1 kanalas pilkosios skalės M3M: pašalinamas kalibravimo paketo tamsos poslinkis, tada atliekamas kraštų atpažįstantis demozėjimas be linijiškumo korekcijos, plokščio lauko ar atskyrimo. Eksportavimas `process` įrašo tą patį failą, kurį užfiksavimas išsaugo šiame lygyje, bet kuriame apdorojimo režime. | Linijinis DN |
| `radiance` | Absoliutus spektrinis spinduliavimas iš visos radiometrinės grandinės, matuojamas **W/m²/sr/nm**. Visada įrašomas kaip 32 bitų TIFF (`tiff32/Radiance_Images/`), nepriklausomai nuo pasirinktos eksporto formos. | float32 |
| `reflectance` | Atspindžio koeficientas ρ, kur **DN 32768 = ρ 1,0 (100 %)**, su rezervu iki ρ 2,0. Parengta „Pix4D“. | uint16 |
| `preview` | Ekranui paruoštas atvaizdas: RGB = baltos spalvos balansas + gama; multispektrinis = netikrų spalvų ištempimas. | 8 bitų ekranas |

### Orientacija

`raw` ir `debayered` failai visada išlaiko jutiklio **natūralią** orientaciją; kameros apvertimo/pasukimo nustatymas taikomas tik kitiems produktams (peržiūrai, spinduliavimui, atspindžiamumui, indeksui). Masyvo įrašai išlaiko kiekvieno elemento failų natūralią orientaciją, nebent nurodytumėte orientaciją (`--orient` per CLI, `orient=True` per SDK). Kiekviename faile XMP formatu įrašoma jo pikseliams būdinga orientacija — `ChlorosRotation`, `ChlorosFlipHorizontal`, `ChlorosFlipVertical` — o `ChlorosOrientationApplied` nurodo, ar orientaciją perteikia patys pikseliai (`false` neapdorotuose ir debayeringo apdorotuose failuose). Chloros vaizdų peržiūros programa „raw“ ir „debayered“ failus konvertuoja pagal tą įrašą tik rodymo tikslais; patys failai niekada nėra perrašomi. Failas be įrašo (sukurtas prieš 1.3.0 versiją arba kitu įrankiu) rodomas toks, koks yra išsaugotas.

## Apribojimo kaukės

Daugiaspektrinės M3C kameros spinduliavimo ir atspindžio produktai apdorojami taikant 3×3 juostų atskyrimą. Jei atskyrimo metu juostoje gaunama neigiama reikšmė, ji apribojama iki nulio (tiesioginiame vaizde apribojimas vyksta lygiai taip pat). Nauja 1.3.0 versijos funkcija: produktas, kuriame bent vienas pikselis buvo apribotas, gauna papildomą **apribojimo kaukę**, kuri tiksliai nurodo, kurie pikseliai ir juostos:

* **Kur:**`clamp_masks/` aplanke**šalia** produktų aplankų, bet niekada jų viduje: `tiff16/Reflectance_Calibrated_Images/x.tif` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif` ir `tiff32/Radiance_Images/x.tif` → `tiff32/clamp_masks/Radiance_Images/x_clampmask.tif`.
* **Ką:** vienos juostos `uint8` formato TIFF failą, sukurtą remiantis pačio produkto pikselių tinkleliu (įskaitant orientaciją, tiriamąją sritį (ROI), pikselių sujungimą ir masyvo suderinimą). 0 bitas (`mask & 1`) nustatomas ten, kur produkto R kanalo juosta buvo apkarpyta, 1 bitas (`& 2`) – G kanale, 2-asis bitas (`& 4`) – B kanale. Kiekvienos juostos apkarpytų pikselių dalis taip pat pažymėta produkte kaip `Chloros:BandRUnmixClampFraction`, `Chloros:BandGUnmixClampFraction` ir `Chloros:BandBUnmixClampFraction`.
* **Kaip tai nurodyta produkte:** XMP žymėje `Chloros:UnmixClampMask` nurodytas kaukės kelias, atsižvelgiant į produkto aplanką (`../clamp_masks/<product folder>/<name>_clampmask.tif`, visada atskirta `/`), `none`, kai nebuvo apkarpytas nė vienas pikselis (kaukė nerašoma), arba `unavailable`, kai kaukės nebuvo galima įrašyti. Produktas be žymės niekada nevykdė atskyrimo (RGB ir monochromatinės kameros, neapdoroti ir debayeringo lygiai).

Kadangi nė viename produkto aplanke nėra kaukės, galite perduoti produkto aplanką, pvz., `Reflectance_Calibrated_Images/`, į „Pix4D“, „Metashape“ ar kitą fotogrametrijos programinę įrangą tokį, koks yra, įskaitant pakatalogius — perduokite produkto aplanką, o ne virš jo esantį formato aplanką. „Chloros“ importavimo funkcija praleidžia „clamp“ kaukes. CLI jas skaičiuoja atskirai: `Image products written: N (plus M clamp masks)`.

## Indekso produktai

Kiekvienas indeksas patenka į savo atskirą „`<INDEX>_Index_Images/`“ aplanką (pvz., „`NDVI_Index_Images/`“).

* **Sveikųjų skaičių formatai**(`TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)`) saugo**linijinį kiekvieno vaizdo ištempimą**, o ne −1…1 atvaizdavimą: DN 0 yra to kadro mažiausia indekso reikšmė, o pilnas mastelis – didžiausia. Failas turi `Chloros:IndexScaling=linear_minmax`, `Chloros:IndexMin` ir `Chloros:IndexMax`, taigi ir `value = IndexMin + DN / full_scale × (IndexMax − IndexMin)`. Kadangi kiekvienas kadras turi savo galinius taškus, kadrus reikia lyginti pagal vertes, o ne pagal DN.
* **`TIFF (32-bit, Percent)`** saugo pačias indeksų vertes kaip „float32“ be perskalavimo — tai geriausias pasirinkimas kiekybiniams skrydžio duomenų tyrimams.

Nauja 1.3.0 versijos funkcija: indeksinis produktas apibūdina **patį save**, o ne kartoja šaltinio neapdorotų duomenų žymes. Jo GPS ir kameros žymės yra nukopijuojamos iš produkto, iš kurio jis buvo apskaičiuotas (atspindžio produkto, jei toks yra), o tada:

| Žymė | Vertė |
| --- | --- |
| `Chloros:ProcessingLevel` | `index` (todėl failas niekada nėra iš naujo importuojamas kaip užfiksuotas vaizdas) |
| `Chloros:IndexApplied` / `Chloros:IndexExpression` | `True` / naudota formulė |
| `Chloros:IndexLevel` | `reflectance` arba `radiance` — iš ko buvo apskaičiuotas indeksas (nėra, jei nėra nė vieno iš jų) |
| `Chloros:IlluminationInvariant` | `True`, jei indeksas apskaičiuotas pagal atspindžio koeficientą, `False` — pagal spinduliavimo intensyvumą arba jutiklio rodmenis |
| `Chloros:ChlorosPipelineVersion` | Chloros versija, kuria buvo sukurtas indeksas |

`Chloros:PixelScale` pašalintas iš indekso produktų — naudokite aukščiau nurodytus `IndexMin` / `IndexMax`. Išsami atkūrimo instrukcija pateikta skyriuje „Sveikųjų skaičių indeksuotas rasteris yra kontrasto išplėtimas kiekvienam atskirai nuotraukai“ [CLI nuorodoje](reference/cli-reference.md).

## Atspindžio pikselių verčių skaitymas

Atspindžio koeficientas saugomas kaip sveikasis skaitmeninis skaičius, o **DN, reiškiantis ρ = 1,0 (100 % atspindžio koeficientas), priklauso nuo šaltinio kameros**:

| Šaltinio kamera | ρ = 1,0 atitinka DN | Kaip nustatyti |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezervas iki ρ 2,0) | Failą pažymi XMP žymė `Chloros:PixelScale=32768`. |
| Survey3 | `65535` (apribota iki ρ 1,0) | Nėra žymos `Chloros:PixelScale` — jos nebuvimas yra požymis. (Nuo 1.3.0 versijos Survey3 produktai kiekviename apdorojimo režime turi kitų `Chloros:*` apdorojimo žymių, pvz., `Chloros:CalibrationApplied`.) |

**Perskaitykite `Chloros:PixelScale` XMP žymą ir padalinkite iš jos**, o ne laikykite ją pastovia. Žymė apibrėžta „uint16“ srityje, todėl ji išlieka `32768` visose išvesties formatuose, kuriuose atliekamas mastelio keitimas — pirmiausia normalizuokite saugomą duomenų tipą atgal į „uint16“ (×257 iš 8 bitų, ×65535 iš „float32“).

{% hint style="warning" %}
**8 bitų įrašai.** 8 bitų įrašo eksportuojami duomenys yra 8 bitų. Jo atspindžio koeficientas yra perskaičiuojamas ir pažymimas `Chloros:PixelScale=32768`, todėl mastelis išlieka; 8 bitų DN produktai ir atspindžio failai, sukurti iki versijos 1.3.0, yra apriboti iki 0–255 ir neturi mastelio žymos. Jei LATTICE atspindžio faile nėra šios žymos, nedarykite prielaidų dėl mastelio; vietoj to eksportuokite iš naujo 16 bitų arba 32 bitų formatu.
{% endhint %}

Išsamias taisykles (įskaitant su „MicaSense“ suderinamus žymeklius) rasite skyriuje **„Atspindžio pikselių skaitymas“** [CLI žinyne](reference/cli-reference.md).
