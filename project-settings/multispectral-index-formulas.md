---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Daugiaspektrinių indeksų formulės

Toliau pateiktose indeksų formulėse naudojamas Survey3 filtrų vidutinio pralaidumo intervalų derinys:

<table><thead><tr><th align="center">Survey3 filtro spalva</th><th width="196.199951171875" align="center">Survey3 filtro pavadinimas</th><th width="159.800048828125" align="center">Pralaidumo diapazonas (FWHM)</th><th align="center">Vidutinė pralaidumo vertė</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468–483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476–512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543–558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598–640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653–668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re-RedEdge</td><td align="center">712–735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798–848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835–865 nm</td><td align="center">850 nm</td></tr></tbody></table>

Naudojant šias formules, pavadinimas gali baigtis „\_1“ arba „\_2“, o tai atitinka, kuris NIR filtras – NIR1 ar NIR2 – buvo naudojamas.

„LATTICE M3C“ (Bayer trijų juostų) kamerų atveju tas pats indeksavimo variklis naudoja M3C filtro juostas:

| M3C filtras | 1 juosta (centras/FWHM) | 2 juosta (centras/FWHM) | 3 juosta (centras/FWHM) |
| --- | --- | --- | --- |
| FRGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | Red 625 nm / 30 nm |
| FRGN | Red 660 nm / 21 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |
| FOCN | Orange 615 nm / 21 nm | Cyan 490 nm / 38 nm | NIR 808 nm / 14 nm |
| FNGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |

„LATTICE M3M“ kameros yra vienos juostos (viena siaurajuostė filtras kiekvienai kamerai), todėl daugiajuostiniai indeksai neskaičiuojami vienam „M3M“ vaizdui. Norėdami apskaičiuoti indeksus naudojant M3M, sujunkite dvi ar daugiau kamerų į suderintą daugiabandį vaizdų rinkinį ir naudokite „LATTICE“ indeksų skaičiavimo variklį (`chloros-cli lattice index` arba GUI sąsajos tiesioginį indeksų skaičiuoklį).

***

## Kur veikia kiekvienas indekso pavadinimas

Chloros turi **tris** indeksų paviršius, o jų iš anksto nustatyti sąrašai nėra identiški. Šiame skyriuje galite patikrinti, ar pavadinimas veiks ten, kur ketinate jį naudoti.

| Kur esate | Kuris sąrašas taikomas | Skaičius |
| --- | --- | --- |
| Projekto nustatymai → Indeksas → Pridėti indeksą (grafinė vartotojo sąsaja) | Paviršius 1 | 27 |
| Vaizdų peržiūros programa [Indeksas/LUT bandymų aplinka](../image-viewer-gui/index-lut-sandbox.md) (grafinė vartotojo sąsaja) | Paviršius 1 | 27 |
| `chloros-cli process --indices NDVI,NDRE` | „Surface 2“ | 22 |
| SDK `process_folder(indices=[...])` | „Surface 2“ | 22 |
| `chloros-cli lattice index --preset` | Paviršius 3 | 22 (kitas 22) |
| Skirtukas „Kameros“ – tiesioginis indekso skaičiuoklis | Paviršius 3 | 22 (kitas 22) |

„Surface 1“ ir „Surface 2“ dirba su **vienu vaizdu iš vienos kameros**, naudodami simbolių lizdus `x`/`y`/`z`(/`a`), susietus su tos kameros filtro kanalais. Paviršius 3 apdoroja**suderintą daugiabandį vaizdų rinkinį** — kelias „LATTICE“ kameras, suderintas į vieną kubą — ir nurodo kanalus mažosiomis raidėmis.

### 1. GUI projekto nustatymai / Vaizdų peržiūros programos „sandbox“ išskleidžiamasis meniu — 27 formulės

Išskleidžiamajame meniu jos išvardytos tokia tvarka (tai įterpimo tvarka, o ne abėcėlė):

`NDVI, GNDVI, CVI, ENDVI, EVI, MSR, OSAVI, TDVI, LAI, FCI1, FCI2, GARI, GCI, GEMI, GLI, GOSAVI, GRVI, GSAVI, LCI, MNLI, MSAVI2, NDRE, NLI, RDVI, SAVI, VARI, WDRVI`

GUI sąsajoje kameros filtro kanalus perkelkite į formulės juostų lizdus, todėl bet kurią formulę galima naudoti su bet kokiu juostų priskyrimu, kurį palaiko jūsų kamera. Jūsų išsaugotos pasirinktinės formulės pridedamos po šio sąrašo.

**Penkios tik GUI skirtos** formulės — tos, kurių CLI/SDK sąrašas „`--indices`“ nepriima — yra įgyvendintos taip:

| Tik GUI skirtas nustatymas | Formulė (kaip įgyvendinta) | Langeliai |
| --- | --- | --- |
| FCI1 | `x*y` | x, y |
| FCI2 | `x*y` | x, y |
| GARI | `(y-(x-1.7*(z-a)))/(y+(x-1.7*(z-a)))` | x, y, z, a (keturi lizdai) |
| GEMI | `((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5))*(1-0.25*((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5)))-((x-0.125)/(1-x))` | x, y |
| LCI | `(y-x)/(y+z)` | x, y, z |

Kiekvienam iš jų numatytas atitikimas pateikiamas atskirame skyriuje toliau šioje puslapyje (pavyzdžiui, GARI tikisi, kad x = Green, y = NIR, z = Blue, a = Red). GARI yra vienintelė formulė Chloros programoje, kurioje naudojama ketvirtoji vieta.

### 2. CLI / SDK `--indices` pavadinimų išplėtimas — 22 išankstiniai nustatymai

Parinktis `chloros-cli process --indices` (ir SDK parametras `indices`) priima šiuos išankstinių nustatymų pavadinimus:

`NDVI, GNDVI, NDRE, OSAVI, SAVI, MSAVI2, EVI, MSR, TDVI, LAI, GCI, GRVI, GSAVI, GOSAVI, NLI, MNLI, RDVI, WDRVI, CVI, ENDVI, GLI, VARI`

{% hint style="warning" %}
**Nežinomi indeksų pavadinimai yra tyliai praleidžiami.** Pavadinimas, kurio nėra šiame sąraše (įskaitant penkias tik GUI skirtas formules `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` ir bet kokios vartotojo sukurtos formulės, kurias išsaugojote GUI) yra praleidžiami, tik pateikiant pranešimą žurnale — vykdymas tęsiamas be to indekso, o pats vykdymas vis tiek praneša apie sėkmę. Pranešimas pateikiamas taip:

```
[INDEX_EXPAND] skipping unknown preset 'LCI'; known: ['CVI', 'ENDVI', 'EVI', ...]
```

Pavadinimai lyginami neatsižvelgiant į didžiųjų ir mažųjų raidžių skirtumą, pašalinus tarpelius, taigi „`ndvi`“, „`NDVI`“ ir „` NDVI `“ yra tas pats išankstinis nustatymas. Išankstinis nustatymas taip pat praleidžiamas, jei jam reikalingas juostos plotis, kurio jūsų fotoaparato filtras nepalaiko.
{% endhint %}

Tikslios įgyvendintos formulės (simboliai `x`/`y`/`z` yra juostų lizdai; numatytasis priskyrimas pateikiamas pagal kiekvieną nustatymą):

| Nustatymas | Formulė (kaip įgyvendinta) | Numatytasis filtras | Langeliai (x, y, z) |
| --- | --- | --- | --- |
| NDVI | `(y-x)/(y+x)` | RGN | Red, NIR |
| GNDVI | `(y-x)/(y+x)` | RGN | Green, NIR |
| NDRE | `(y-x)/(y+x)` | RE | RE, NIR |
| OSAVI | `(y-x)/(y+x+0.16)` | RGN | Red, NIR |
| SAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Red, NIR |
| MSAVI2 | `(2*y+1-sqrt((2*y+1)*(2*y+1)-8*(y-x)))/2` | RGN | Red, NIR |
| EVI | `2.5*(y-x)/(y+6*x-7.5*z+1)` | RGN | Red, NIR, Blue |
| MSR | `((y/x)-1)/(sqrt(y/x)+1)` | RGN | Red, NIR |
| TDVI | `1.5*(y-x)/sqrt(y*y+x+0.5)` | RGN | Red, NIR |
| LAI | `3.618*(2.5*(y-x)/(y+6*x-7.5*z+1))-0.118` | RGN | Red, NIR, Blue |
| GCI | `(y/x)-1` | RGN | Green, NIR |
| GRVI | `y/x` | RGN | Green, NIR |
| GSAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Žalia (Green), NIR |
| GOSAVI | `(y-x)/(y+x+0.16)` | RGN | Žalia (Green), NIR |
| NLI | `((y*y)-x)/((y*y)+x)` | RGN | Red, NIR |
| MNLI | `((y*y-x)*(1+0.5))/((y*y)+x+0.5)` | RGN | Red, NIR |
| RDVI | `(y-x)/sqrt(y+x)` | RGN | Red, NIR |
| WDRVI | `(0.2*y-x)/(0.2*y+x)` | RGN | Red, NIR |
| CVI | `(z/y)*(x/y)` | RGN | Red, Green, NIR |
| ENDVI | `((x+y)-(2*z))/((x+y)+(2*z))` | NGB | NIR, Green, Blue |
| GLI | `((y-x)+(y-z))/((2*y)+x+z)` | RGB | Red, Green, Blue |
| VARI | `(y-x)/(y+x-z)` | RGB | 

Kai pateikiate paprastą pavadinimą, pvz., „`NDVI`“, Chloros turi nuspręsti, kurį failo kanalą skaito kiekvienas simbolis. Ji naudoja šią lentelę, kurioje filtro kodas susiejamas su kiekvieno kanalo masyvo pozicija:

| Filtro kodas | Kanalas → masyvo indeksas |
| --- | --- |
| OCN | Orange 0, Cyan 1, NIR 2 (`Red` pripažįstamas kaip Orange aliasas, taip pat 0) |
| RGN | Red 0, Green 1, NIR 2 |
| NGB | NIR 0, Green 1, Blue 2 |
| RGB | Red 0, Green 1, Blue 2 |
| RE | RE 0 |
| NIR | NIR 0 |

Išankstinio nustatymo **numatytoji filtras** (aukščiau esanti stulpelyje „Default filter“) naudojamas, kai projekte yra vaizdų su tuo filtru. Jei tokių nėra, Chloros nuskaito projekte faktiškai esančius filtrus tvarka `RGN, OCN, NGB, RGB, RE, NIR` ir pasirenka pirmąjį, kuris gali užtikrinti visus nustatymui reikalingus kanalus. Jei nė vienas negali, nustatymas to vykdymo metu atmetamas. Būtent todėl „`NDVI`“, prašomas duomenų rinkinyje, kuriame yra tik „OCN“, vis tiek duoda priimtiną rezultatą — jis susiejamas su „OCN“ Orange ir NIR pozicijomis.

„LATTICE M3C“ modelio eilutėse filtras nurodomas su priešdėliu „`F`“ (`LATT-M3C-L41-FRGN`), tačiau priešdėlis pašalinamas, kai filtro kodas nuskaitomas iš vaizdo, todėl „FRGN“ kamera atpažįsta per aukščiau esančią eilutę „`RGN`“ ir nereikalauja jokio specialaus tvarkymo.

### 3. „LATTICE“ indeksavimo variklis (`lattice index --preset`, tiesioginis indeksų skaičiuoklis) — 22 išankstiniai nustatymai

„LATTICE“ variklis veikia su suderintais daugiabandžiais vaizdų rinkiniais (tiesioginiai masyvai arba eksportuoti daugiabandžiai TIFF failai) ir naudoja mažosiomis raidėmis rašomus kanalų pavadinimus (`red`, `green`, `blue`, `red_edge`, `nir`). Jo išankstinių nustatymų sąrašas skiriasi nuo dviejų pirmiau minėtų:

| Nustatymas | Formulė | Kanalai |
| --- | --- | --- |
| NDVI | `(nir - red) / (nir + red)` | raudonas, NIR |
| GNDVI | `(nir - green) / (nir + green)` | žalia, NIR |
| BNDVI | `(nir - blue) / (nir + blue)` | mėlyna, NIR |
| NDRE | `(nir - red_edge) / (nir + red_edge)` | raudonos krašto, NIR |
| ENDVI | `((nir + green) - 2*blue) / ((nir + green) + 2*blue)` | mėlyna, žalia, NIR |
| SAVI | `1.5 * (nir - red) / (nir + red + 0.5)` | raudona, NIR |
| OSAVI | `(nir - red) / (nir + red + 0.16)` | raudona, NIR |
| MSAVI | `(2*nir + 1 - sqrt((2*nir + 1)**2 - 8*(nir - red))) / 2` | raudona, NIR |
| EVI | `2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)` | mėlyna, raudona, NIR |
| EVI2 | `2.5 * (nir - red) / (nir + 2.4*red + 1)` | raudona, NIR |
| CVI | `nir * red / green**2` | raudona, žalia, NIR |
| MSR | `((nir/red) - 1) / (sqrt(nir/red) + 1)` | raudona, NIR |
| TDVI | `1.5 * (nir - red) / sqrt(nir**2 + red + 0.5)` | raudona, NIR |
| LAI | `3.618 * (2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)) - 0.118` | mėlyna, raudona, NIR |
| GLI | `(2*green - red - blue) / (2*green + red + blue)` | raudona, žalia, mėlyna |
| NGRDI | `(green - red) / (green + red)` | raudona, žalia |
| VARI | `(green - red) / (green + red - blue)` | raudona, žalia, mėlyna |
| TGI | `green - 0.39*red - 0.61*blue` | raudona, žalia, mėlyna |
| „EXG“ | `2*green - red - blue` | raudona, žalia, mėlyna |
| CIRE | `(nir / red_edge) - 1` | raudona\_kraštas, NIR |
| CIGREEN | `(nir / green) - 1` | žalia, NIR |
| NDWI | `(green - nir) / (green + nir)` | žalia, nir |

Paleiskite komandą `chloros-cli lattice index --list-presets`, kad išspausdintumėte šią lentelę iš įdiegtos versijos, o komandą `--list-gradients` – norėdami peržiūrėti galimus spalvų perėjimus. Kanalų simboliai yra jautrūs didžiosioms ir mažosioms raidėms ir turi atitikti išankstinio nustatymo pavadinimus mažosiomis raidėmis (pvz., `--channel red=Red_660 --channel nir=NIR_850`).

***

## CVI – chlorofilo vegetacijos indeksas

Šis indeksas jautriai reaguoja į lapų chlorofilo kiekį plačiame medžių lajos tankumo diapazone. Jis naudoja NIR, raudoną ir žalią juostas.

$$
CVI = NIR * {Red \over Green^{2}}
$$

GUI ir CLI/SDK nustatymų sąraše tai yra simbolio formulė `(z/y)*(x/y)`, kur x = Red, y = Green, z = NIR (RGN kameros juostos); „LATTICE“ indekso variklio išankstinis nustatymas yra `nir * red / green**2`. Kiekvienam paviršiui apskaičiuojamas tas pats indeksas.

_Šaltinis: Vincini, M., E. Frazzi ir P. D&#x27;Alessio. „Plataus dažnių diapazono lapų chlorofilo augmenijos indeksas medžių lajos mastu“. „Precision Agriculture“ 9 (2008): 303–319._

***

## ENDVI – patobulintas normalizuotas skirtumo augmenijos indeksas

Šis indeksas naudoja mėlynąjį kanalą kartu su NIR ir žaliuoju, ir yra populiarus tarp kamerų su NGB filtru, kuriose mėlynoji juosta pakeičia raudonąją.

$$
ENDVI = {(NIR + Green) - (2 * Blue) \over (NIR + Green) + (2 * Blue)}
$$

Įgyvendinimas atliekamas pagal simbolinę formulę `((x+y)-(2*z))/((x+y)+(2*z))` — priskirkite savo kameros NIR ir Green kanalus x/y lizdams, o Blue – z (NGB kameros atveju: x=NIR, y=Green, z=Blue).

***

## EVI – patobulintas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudoti su MODIS duomenimis kaip NDVI patobulinimas, optimizuojant augmenijos signalą vietovėse, kuriose didelis lapų ploto indeksas (LAI). Jis yra naudingiausias regionuose su dideliu LAI, kur NDVI gali būti prisotintas. Jis naudoja mėlynosios atspindžio sritį, siekdamas pakoreguoti dirvožemio fono signalus ir sumažinti atmosferos poveikį, įskaitant aerozolių sklaidą.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI vertės augmenijos pikseliuose turėtų būti nuo 0 iki 1. Šviesūs objektai, tokie kaip debesys ir balti pastatai, taip pat tamsūs objektai, tokie kaip vanduo, gali sukelti anomalų pikselių verčių atsiradimą EVI vaizde. Prieš kuriant EVI vaizdą, reikėtų iš atspindžio vaizdo pašalinti debesis ir ryškius objektus, o pasirinktinai – nustatyti pikselių verčių ribą nuo 0 iki 1.

_Šaltinis: Huete, A. ir kt. „MODIS augmenijos indeksų radiometrinių ir biofizinių charakteristikų apžvalga“. „Remote Sensing of Environment“ 83 (2002): 195–213._

***

## FCI1 – Miškų dangos indeksas 1

_Tik GUI — nėra prieinamas kaip CLI/SDK nustatymas „`--indices`“._

Šis indeksas atskiria miško lajos nuo kitų augmenijos tipų, naudodamas daugiaspektrinius atspindžio vaizdus, kuriuose yra raudonojo krašto juosta.

$$
FCI1 = Red * RedEdge
$$

Miškingose vietovėse FCI1 vertės bus mažesnės dėl mažesnio medžių atspindžio ir šešėlių buvimo medžių lajos viduje.

_Šaltinis: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miško dangos indeksai daugiaspektriniams vaizdams“. „Photogrammetric Engineering &amp; Remote Sensing“ 84.8 (2018): 505–512._

***

## FCI2 – 2-asis miško dangos indeksas

_Tik GUI – nėra prieinamas kaip CLI/SDK „`--indices`“ nustatymas._

Šis indeksas atskiria miško lajos nuo kitų augmenijos tipų, naudodamas daugiaspektrinius atspindžio vaizdus, kuriuose nėra raudonojo krašto juostos.

$$
FCI2 = Red * NIR
$$

Miškingose vietovėse FCI2 vertės bus mažesnės dėl mažesnio medžių atspindžio ir šešėlių buvimo medžių lajos viduje.

_Šaltinis: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miško dangos indeksai daugiaspektriniams vaizdams“. „Photogrammetric Engineering &amp; Remote Sensing“ 84.8 (2018): 505–512._

***

## GEMI – Pasaulinis aplinkos stebėjimo indeksas

_Tik GUI – nėra prieinamas kaip CLI/SDK `--indices` nustatymas._

Šis nelinijinis augmenijos indeksas naudojamas pasaulinei aplinkos stebėsenai remiantis palydoviniais vaizdais ir siekia koreguoti atmosferos poveikį. Jis panašus į NDVI, tačiau yra mažiau jautrus atmosferos poveikiui. Jam įtakos turi plikas dirvožemis; todėl jo nerekomenduojama naudoti vietovėse, kuriose augmenija yra reta arba vidutiniškai tanki.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Kur:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Šaltinis: Pinty, B., ir M. Verstraete. GEMI: netiesinis indeksas pasaulinei augmenijai stebėti iš palydovų. „Vegetation“ 101 (1992): 15–20._

***

## GARI – Green Atmosferos poveikiui atsparus indeksas

_Tik GUI – nėra prieinamas kaip CLI/SDK išankstinis nustatymas `--indices`._

Šis indeksas yra jautresnis plačiam chlorofilo koncentracijų diapazonui ir mažiau jautrus atmosferos poveikiui nei NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama konstanta yra svorio funkcija, priklausanti nuo aerozolių sąlygų atmosferoje. ENVI naudoja vertę 1,7, kuri yra rekomenduojama Gitelson, Kaufman ir Merzylak (1996, 296 psl.).

_Šaltinis: Gitelson, A., Y. Kaufman ir M. Merzylak. „Žaliojo kanalo naudojimas globalios augmenijos nuotolinėje stebėsenoje naudojant EOS-MODIS“. „Remote Sensing of Environment“ 58 (1996): 289–298._

***

## GCI – žaliosios chlorofilo indeksas (Green Chlorophyll Index)

Šis indeksas naudojamas įvairių augalų rūšių lapų chlorofilo kiekiui įvertinti.

$$
GCI = {NIR \over Green} - 1
$$

Platus NIR ir žaliosios bangų ilgių diapazonas leidžia geriau prognozuoti chlorofilo kiekį, tuo pačiu užtikrinant didesnį jautrumą ir aukštesnį signalo ir triukšmo santykį.

_Šaltinis: Gitelson, A., Y. Gritz ir M. Merzlyak. „Ryšiai tarp lapų chlorofilo kiekio ir spektrinio atspindžio bei algoritmai, skirti neardomajam chlorofilo vertinimui aukštesniųjų augalų lapuose.“ „Journal of Plant Physiology“ 160 (2003): 271–282._

***

## GLI – žaliųjų lapų indeksas (Green Leaf Index)

Šis indeksas iš pradžių buvo sukurtas naudoti su skaitmenine RGB kamera kviečių dangos matavimui, kur raudonos, žalios ir mėlynos spalvų skaitmeniniai dydžiai (DN) svyruoja nuo 0 iki 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI vertės svyruoja nuo -1 iki +1. Neigiamos vertės atspindi dirvožemį ir negyvosios gamtos elementus, o teigiamos vertės – žalius lapus ir stiebus.

_Šaltinis: Louhaichi, M., M. Borman ir D. Johnson. „Erdviškai orientuota platforma ir aerofotografija kviečių ganymo poveikio dokumentavimui“. Geocarto International 16, Nr. 1 (2001): 65–70._

***

## GNDVI – normalizuotas žalios spalvos augmenijos skirtumo indeksas (Green)

Šis indeksas yra panašus į NDVI, išskyrus tai, kad jis matuoja žaliąjį spektrą nuo 540 iki 570 nm, o ne raudonąjį spektrą. Šis indeksas yra jautresnis chlorofilo koncentracijai nei NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Šaltinis: Gitelson, A., ir M. Merzlyak. „Chlorofilo koncentracijos aukštesniųjų augalų lapuose nuotolinis matavimas“. „Advances in Space Research“ 22 (1998): 689–692._

***

## GOSAVI – žaliai optimizuotas, dirvožemiui pritaikytas augmenijos indeksas (Green Optimized Soil Adjusted Vegetation Index)

Šis indeksas iš pradžių buvo sukurtas naudojant spalvotą infraraudonąją fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis panašus į OSAVI, tačiau raudoną juostą pakeičia žalia.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Šaltinis: Sripada, R. ir kt. „Kukurūzų azoto poreikio nustatymas auginimo sezono metu naudojant spalvotąją infraraudonąją aerofotografiją“. Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## GRVI – žalios spalvos santykio vegetacijos indeksas (Green Ratio Vegetation Index)

Šis indeksas jautriai reaguoja į fotosintezės intensyvumą miško lajos sluoksnyje, nes žalios ir raudonos spalvų atspindžio koeficientams didelę įtaką daro lapų pigmentų pokyčiai.

$$
GRVI = {NIR \over Green }
$$

_Šaltinis: Sripada, R. ir kt. „Spalvota infraraudonųjų spindulių aerofotografija, skirta nustatyti ankstyvąjį azoto poreikį kukurūzams auginimo sezono metu“. „Agronomy Journal“ 98 (2006): 968–977._

***

## GSAVI – žalios dirvos pakoreguotas augmenijos indeksas (Green Soil Adjusted Vegetation Index)

Šis indeksas iš pradžių buvo sukurtas naudojant spalvotąją infraraudonąją fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis panašus į SAVI, tačiau raudoną juostą pakeičia žalia.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Šaltinis: Sripada, R. ir kt. „Kukurūzų azoto poreikio sezono metu nustatymas naudojant spalvotąją infraraudonąją aerofotografiją“. Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## LAI – lapų ploto indeksas

Šis indeksas naudojamas lapijos dangos apimčiai įvertinti bei pasėlių augimui ir derlingumui prognozuoti. ENVI apskaičiuoja žaliąjį LAI naudodama šią empirinę formulę, pateiktą Boegh ir kt. (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Kur EVI yra:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Aukštos LAI vertės paprastai svyruoja nuo maždaug 0 iki 3,5. Tačiau, kai vaizde yra debesys ir kiti ryškūs elementai, dėl kurių susidaro prisotinti pikseliai, LAI vertės gali viršyti 3,5. Idealiu atveju prieš kurdami LAI vaizdą turėtumėte iš vaizdo pašalinti debesis ir ryškius elementus.

_Šaltinis: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ir A. Thomsen. „Iš lėktuvo gauti multispektriniai duomenys lapų ploto indekso, azoto koncentracijos ir fotosintezės efektyvumo žemės ūkyje kiekybiniam vertinimui.“ „Remote Sensing of Environment“ 81, nr. 2–3 (2002): 179–193._

***

## LCI – lapų chlorofilo indeksas

_Tik GUI — nėra prieinamas kaip CLI/SDK `--indices` nustatymas._

Šis indeksas naudojamas aukštesniųjų augalų chlorofilo kiekiui įvertinti; jis jautrus atspindžio pokyčiams, kuriuos sukelia chlorofilo absorbcija.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Šaltinis: Datt, B. „Eukalipto lapų drėgmės kiekio nuotolinis matavimas“. „Journal of Plant Physiology“ 154, nr. 1 (1999): 30–36._

***

## MNLI – modifikuotas nelinijinis indeksas

Šis indeksas yra nelinijinio indekso (NLI) patobulinimas, į kurį įtrauktas dirvožemiui pritaikytas augmenijos indeksas (SAVI), siekiant atsižvelgti į dirvožemio foną. ENVI naudoja lajos fono koregavimo koeficiento (_L_) vertę 0,5.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Šaltinis: Yang, Z., P. Willis ir R. Mueller. „Juostų santykio patobulinto AWIFS vaizdo įtaka pasėlių klasifikavimo tikslumui.“ „Pecora 17“ nuotolinio stebėjimo simpoziumo medžiaga (2008), Denveris, Koloradas._

***

## MSAVI2 – modifikuotas dirvožemiui pritaikytas augmenijos indeksas 2

Šis indeksas yra paprastesnė MSAVI indekso versija, kurią pasiūlė Qi ir kt. (1994 m.), kuris patobulina dirvožemiui pritaikytą augmenijos indeksą (SAVI). Jis sumažina dirvožemio triukšmą ir padidina augmenijos signalo dinaminį diapazoną. MSAVI2 pagrįstas indukciniu metodu, kuris nenaudoja pastovios _L_ vertės (kaip SAVI atveju), siekiant išryškinti sveiką augmeniją.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Šaltinis: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ir S. Sorooshian. „A Modified Soil Adjusted Vegetation Index.“ „Remote Sensing of Environment“ 48 (1994): 119–126._

***

## MSR – modifikuotas paprastasis santykis

Šis indeksas yra modifikuotas paprastasis NIR/Red santykis, sukurtas siekiant linearizuoti jo ryšį su biofizikiniais parametrais; jis yra jautresnis nei NDVI esant didesniam augmenijos tankiui.

$$
MSR = {(NIR / Red) - 1 \over \sqrt{NIR / Red} + 1}
$$

_Šaltinis: Chen, J. „Vegetacijos indeksų ir modifikuoto paprasto santykio vertinimas borealinėse vietovėse.“ „Canadian Journal of Remote Sensing“ 22 (1996): 229–242._

***

## NDRE – Normalizuotas skirtumas „RedEdge“

Šis indeksas yra panašus į NDVI, tačiau lygina NIR ir RedEdge kontrastą vietoj Red, todėl dažnai anksčiau aptinka augmenijos stresą.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI – Normalizuotas skirtumas augmenijos indeksas

Šis indeksas rodo sveiką, žalią augmeniją. Dėl normalizuoto skirtumo formulės ir chlorofilo didžiausio sugėrimo bei atspindžio sričių naudojimo jis yra patikimas įvairiomis sąlygomis. Tačiau jis gali pasiekti prisotinimą tankios augmenijos sąlygomis, kai LAI vertė tampa didelė.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Šio indekso vertė svyruoja nuo -1 iki 1. Įprastas žalią augmeniją atitinkantis intervalas yra nuo 0,2 iki 0,8.

_Šaltinis: Rouse, J., R. Haas, J. Schell ir D. Deering. „Vegetacijos sistemų stebėjimas Didžiosiose lygumose naudojant ERTS“. Trečiasis ERTS simpoziumas, NASA (1973): 309–317._

***

## NLI – nelinijinis indeksas

Šis indeksas remiasi prielaida, kad ryšys tarp daugelio augmenijos indeksų ir paviršiaus biofizinių parametrų yra nelinijinis. Jis linijizuoja ryšius su paviršiaus parametrais, kurie paprastai yra nelinijiniai.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Šaltinis: Goel, N., ir W. Qin. „Lapijos struktūros įtaka ryšiams tarp įvairių augmenijos indeksų bei LAI ir Fpar: kompiuterinė simuliacija.“ „Remote Sensing Reviews“ 10 (1994): 309–347._

***

## OSAVI – optimizuotas dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas pagrįstas dirvožemiu pakoreguotu augmenijos indeksu (SAVI). Jame naudojama standartinė 0,16 vertė lajos fono koregavimo koeficientui. Rondeaux (1996) nustatė, kad ši vertė užtikrina didesnį dirvožemio variabilumą nei SAVI esant menkai augmenijos dangai, tuo pačiu parodydama didesnį jautrumą augmenijos dangai, viršijančiai 50 %. Šis indeksas geriausiai tinka vietovėse su palyginti retu augmenijos sluoksniu, kur pro lajos viršūnę matomas dirvožemis.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Šaltinis: Rondeaux, G., M. Steven ir F. Baret. „Optimization of Soil-Adjusted Vegetation Indices.“ Remote Sensing of Environment 55 (1996): 95–107._

***

## RDVI – renormalizuotas skirtuminis augmenijos indeksas

Šis indeksas naudoja artimosios infraraudonosios ir raudonosios bangų ilgių skirtumą kartu su NDVI, siekiant išryškinti sveiką augmeniją. Jis yra nejautrus dirvožemio ir saulės stebėjimo geometrijos poveikiui.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Šaltinis: Roujean, J., ir F. Breon. „Augalijos sugertos PAR vertinimas remiantis dvikrypčio atspindžio matavimais“. „Remote Sensing of Environment“ 51 (1995): 375–384._

***

## SAVI – dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jis slopina dirvožemio pikselių poveikį. Jame naudojamas lajos fono koregavimo koeficientas _L_, kuris priklauso nuo augmenijos tankio ir dažnai reikalauja išankstinių žinių apie augmenijos kiekį. Huete (1988) siūlo optimalų _L_=0,5 dydį, siekiant atsižvelgti į pirmojo laipsnio dirvožemio fono svyravimus. Šis indeksas geriausiai tinka vietovėse su palyginti retu augmenijos tankumu, kur dirvožemis matomas pro lajos viršų.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Šaltinis: Huete, A. „A Soil-Adjusted Vegetation Index (SAVI)“. „Remote Sensing of Environment“ 25 (1988): 295–309._

***

## TDVI – transformuotas skirtumų augmenijos indeksas

Šis indeksas yra naudingas stebint augmenijos dangą miesto aplinkoje. Jis nesusisotina kaip NDVI ir SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Šaltinis: Bannari, A., H. Asalhi ir P. Teillet. „Transformuotas skirtumo augmenijos indeksas (TDVI) augmenijos dangos kartografavimui“. Įtraukta į „Geoscience and Remote Sensing Symposium, IGARSS &#x27;02, IEEE International“ konferencijos medžiagą, 5 tomas (2002)._

***

## VARI – matomojo spektro atmosferos poveikiui atsparus indeksas

Šis indeksas pagrįstas ARVI ir naudojamas augmenijos daliai vaizde įvertinti, mažai reaguojant į atmosferos poveikį.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Šaltinis: Gitelson, A., ir kt. „Vegetacija ir dirvožemio linijos matomosios spektro erdvėje: koncepcija ir metodika, skirta augmenijos dalies nuotoliniam įvertinimui“. „International Journal of Remote Sensing“ 23 (2002): 2537–2562._

***

## WDRVI – plataus dinaminio diapazono augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jame naudojamas svorio koeficientas (_a_), siekiant sumažinti artimosios infraraudonosios ir raudonosios spinduliuotės indėlių į NDVI skirtumą. WDRVI yra ypač veiksmingas vaizduose, kuriuose augmenijos tankis yra vidutinis ar didelis, kai NDVI viršija 0,6. NDVI paprastai stabilizuojasi, kai didėja augmenijos dalis ir lapų ploto indeksas (LAI), tuo tarpu WDRVI yra jautresnis platesniam augmenijos dalių diapazonui ir LAI pokyčiams.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Svorio koeficientas (_a_) gali svyruoti nuo 0,1 iki 0,2. Henebry, Viña ir Gitelson (2004) rekomenduoja vertę 0,2.

_Literatūra_

_Gitelson, A. „Plataus dinaminio diapazono augmenijos indeksas, skirtas augmenijos biofizinių charakteristikų nuotoliniam kiekybiniam vertinimui.“ *Journal of Plant Physiology* 161, Nr. 2 (2004): 165–173._

_Henebry, G., A. Viña ir A. Gitelson. „Plataus dinaminio diapazono vegetacijos indeksas ir jo potencialus pritaikymas spragų analizei.“ „Gap Analysis Bulletin“ 12: 50–56._
