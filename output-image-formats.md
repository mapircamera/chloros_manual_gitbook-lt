---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Išvesties vaizdo formatai

Chloros eksportuoja apdorotus produktus keturiais failų formatais. Pasirinkite formatą projekto nustatymuose (GUI), naudodami `--format` (CLI) arba `export_format` (SDK). CLI ir SDK priima tiksliai žemiau nurodytas eilutes.

| Formato eilutė | Plėtinys | Pikselių tipas | Pikselių diapazonas | Pastabos |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(numatyta)* | `.tif` | uint16 skaitmeninis skaičius | 0 – 65535 | Rekomenduojama fotogrametrijai / GIS. |
| `TIFF (32-bit, Percent)` | `.tif` | float32 | 0,0 – 1,0 | 1,0 = 100 % atspindžio koeficientas. Kai kurios programos negali skaityti TIFF failų su slankiojo kablelio skaičiais; failai yra didesni. |
| `PNG (8-bit)` | `.png` | „uint8“ skaitmeninis skaičius | 0 – 255 | Suspaudimas be nuostolių, tinka peržiūrai internete ir vizualizavimui. |
| `JPG (8-bit)` | `.jpg` | uint8 skaitmeninis skaičius | 0 – 255 | Suspaudimas su nuostoliais, mažiausi failai. |

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

Kameros aplankas yra `LATT-<sensor>-<lens>-F<filter>`, jei naudojama „LATTICE“, ir `<model>_<filter>` (pvz., `Survey3N_RGN`), jei naudojama „Survey3“. **Kiekvienas eksportuotas produktas išlaiko šaltinio failo pavadinimą — produktą identifikuoja aplankas, o ne failo pavadinimo priesaga.** Visas taisykles rasite skyriuje [Kur atsiduria išvesties duomenys](reference/cli-reference.md) „CLI“ žinyne.

## „LATTICE“ produktai (fiksavimo ir eksporto lygiai)

Vienas „LATTICE“ neapdorotas kadras vienu praėjimu išskirstomas į visus prašomus produktus. Kiekvienam produkto tipui yra skirtas atskiras perjungiklis (GUI žymės langeliai arba CLI, `--debayered` / `--preview` / `--radiance` / `--reflectance`, pagal numatytuosius nustatymus visi įjungti):

| Lygis | Turinys | Duomenų tipas |
| --- | --- | --- |
| `raw` | Tiesiogiai iš jutiklio gauti „Bayer“ duomenys (monochromatinės kameros: vienas dažnių juostos). Apdorojimas visada prasideda nuo neapdorotų duomenų. | Kaip užfiksuota |
| `debayered` | Linijinis demosaikas — 3 kanalai M3C, 1 kanalas pilkosios skalės M3M. | Linijinis DN |
| `radiance` | Absoliutus spektrinis spinduliavimas, gautas iš visos radiometrinės grandinės, matuojamas **W/m²/sr/nm**. Visada užrašoma kaip 32 bitų TIFF (`tiff32/Radiance_Images/`), nepriklausomai nuo pasirinktos eksporto formos. | float32 |
| `reflectance` | Atspindžio koeficientas ρ, kur **DN 32768 = ρ 1,0 (100 %)**, su rezervu iki ρ 2,0. Suderinama su „Pix4D“. | uint16 |
| `preview` | Ekranui paruoštas atvaizdavimas: RGB = baltos spalvos balansas + gama; multispektrinis = netikrųjų spalvų išplėtimas. | 8 bitų ekranas |

## Atspindžio pikselių verčių skaitymas

Atspindžio koeficientas saugomas kaip sveikasis skaitmeninis skaičius, o **DN, reiškiantis ρ = 1,0 (100 % atspindžio koeficientas), priklauso nuo šaltinio kameros**:

| Šaltinio kamera | ρ = 1,0 yra DN | Kaip nustatyti |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezervas iki ρ 2,0) | Failą žymi XMP žymė `Chloros:PixelScale=32768`. |
| Survey3 | `65535` (apribota esant ρ 1,0) | Nėra `Chloros:*` XMP žymių — šis trūkumas yra požymis. |

**Nuskaitykite `Chloros:PixelScale` XMP žymą ir padalinkite iš jos**, o ne laikykite, kad tai yra konstanta. Žymė apibrėžta uint16 srityje, todėl ji išlieka `32768` visose išvesties formatuose, kuriuose keičiamas mastelis — pirmiausia normalizuokite saugomą duomenų tipą atgal į uint16 (×257 iš 8 bitų, ×65535 iš float32).

{% hint style="warning" %}
**Vienu atveju mastelis netaikomas pagal projektą.** Kai 8 bitų šaltinio įrašas (BayerRG8) įrašomas kaip 8 bitų TIFF, apdorojimo grandinė apriboja reikšmes iki 0–255, o ne perskaičiuoja, todėl failui netaikomas joks mastelis — Chloros čia sąmoningai praleidžia `Chloros:PixelScale`. Jei LATTICE atspindžio faile nėra šios žymos, nedarykite prielaidos apie mastelį; vietoj to eksportuokite iš naujo 16 bitų arba 32 bitų formatu.
{% endhint %}

Išsamias taisykles (įskaitant su „MicaSense“ suderinamas žymes) rasite skyriuje **„Atspindžio pikselių skaitymas“** [CLI nuorodoje](reference/cli-reference.md).
