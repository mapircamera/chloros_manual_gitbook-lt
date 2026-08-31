---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Daugiaspektrinių indeksų formulės

Toliau pateiktose indeksų formulėse naudojamas Survey3 filtro vidutinio pralaidumo intervalų derinys:

<table><thead><tr><th align="center">Survey3 filtro spalva</th><th width="196.199951171875" align="center">Survey3 filtro pavadinimas</th><th width="159.800048828125" align="center">Pralaidumo diapazonas (FWHM)</th><th align="center">Vidutinė pralaidumo vertė</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB – Blue</td><td align="center">468–483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN – Cyan</td><td align="center">476–512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543–558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN – Orange</td><td align="center">598–640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN – Red</td><td align="center">653–668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712–735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN – NIR1</td><td align="center">798–848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR – NIR2</td><td align="center">835–865 nm</td><td align="center">850 nm</td></tr></tbody></table>Naudojant šias formules, pavadinimas gali baigtis „\_1“ arba „\_2“, o tai reiškia, kad buvo naudojamas filtras NIR, NIR1 arba NIR2.

„LATTICE M3C“ (Bayer trijų juostų) kameroms tas pats indeksavimo variklis naudoja M3C filtro juostas:

| M3C filtras | 1 juosta (centras/FWHM) | Juosta 2 (centras/FWHM) | Juosta 3 (centras/FWHM) |
| --- | --- | --- | --- |
| FRGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | Red 625 nm / 30 nm |
| FRGN | Red 660 nm / 21 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |
| FOCN | Orange 615 nm / 21 nm | Cyan 490 nm / 38 nm | NIR 808 nm / 14 nm |
| FNGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |

„LATTICE M3M“ kameros yra vienos juostos (viena siaurajuostė filtras kiekvienai kamerai), todėl atskirai paimtam M3M vaizdui daugiabandžiai indeksai neskaičiuojami. Norėdami apskaičiuoti indeksus naudojant M3M, sujunkite dvi ar daugiau kamerų į suderintą daugiabandį vaizdų rinkinį ir naudokite „LATTICE“ indeksų skaičiavimo variklį (`chloros-cli lattice index` arba GUI sąsajos tiesioginį indeksų skaičiuoklį).

***

## Kur veikia kiekvienas indekso pavadinimas

Chloros turi **tris** indeksų paviršius, o jų iš anksto nustatyti sąrašai nėra identiški. Šiame skyriuje galite patikrinti, ar pavadinimas veiks ten, kur ketinate jį naudoti.

| Kur esate | Kuris sąrašas taikomas | Skaičius |
| --- | --- | --- |
| Projekto nustatymai → Indeksas → Pridėti indeksą (GUI) | Paviršius 1 | 27 |
| Vaizdų peržiūros programa [Indekso/LUT bandymų aplinka](../image-viewer-gui/index-lut-sandbox.md) (GUI) | Paviršius 1 | 27 |
| `chloros-cli process --indices NDVI,NDRE` | Paviršius 2 | 22 |
| SDK `process_folder(indices=[...])` | Paviršius 2 | 22 |
| `chloros-cli lattice index --preset` | Paviršius 3 | 22 (kitas 22) |
| Skirtukas „Kameros“ – tiesioginis indeksų skaičiuoklis | Paviršius 3 | 22 (kitas 22) |

„Surface 1“ ir „Surface 2“ dirba su **vienu vaizdu iš vienos kameros**, naudodami simbolių lizdus `x`/`y`/`z`(/`a`), priskirtus tos kameros filtro kanalams. „Surface 3“ apdoroja**suderintą daugiabandį vaizdų rinkinį** — kelias „LATTICE“ kameras, suderintas į vieną kubą — ir nurodo kanalus mažosiomis raidėmis.

### 1. GUI projekto nustatymai / „Image Viewer“ sandbox išskleidžiamasis meniu — 27 formulės

Išskleidžiamajame meniu jos išvardytos tokia tvarka (tai įterpimo tvarka, o ne abėcėlė):

`NDVI, GNDVI, CVI, ENDVI, EVI, MSR, OSAVI, TDVI, LAI, FCI1, FCI2, GARI, GCI, GEMI, GLI, GOSAVI, GRVI, GSAVI, LCI, MNLI, MSAVI2, NDRE, NLI, RDVI, SAVI, VARI, WDRVI`

GUI sąsajoje kameros filtrų kanalus vilkite į formulės juostų lizdus, todėl bet kuri formulė gali būti naudojama su bet kokiu juostų priskyrimu, kurį palaiko jūsų kamera. Jūsų išsaugotos pasirinktinės formulės pridedamos po šio sąrašo.

**Penkios tik GUI skirtos** formulės — tų, kurių sąrašas CLI/SDK `--indices` nepriima — yra įgyvendintos taip:

| Tik GUI nustatymas | Formulė (kaip įgyvendinta) | Langeliai |
| --- | --- | --- |
| FCI1 | `x*y` | x, y |
| FCI2 | `x*y` | x, y |
| GARI | `(y-(x-1.7*(z-a)))/(y+(x-1.7*(z-a)))` | x, y, z, a (keturi lizdai) |
| GEMI | `((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5))*(1-0.25*((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5)))-((x-0.125)/(1-x))` | x, y |
| LCI | `(y-x)/(y+z)` | x, y, z |

Kiekvienam iš jų numatytas atitikimas pateikiamas atskirame skyriuje toliau šioje puslapyje (pavyzdžiui, GARI numato, kad x=Green, y=NIR, z=Blue, a = Red). GARI yra vienintelė formulė Chloros, kurioje naudojamas ketvirtasis lizdas.

### 2. CLI / SDK `--indices` pavadinimų išplėtimas — 22 išankstiniai nustatymai

Parinktis `chloros-cli process --indices` (ir parametras „SDK `indices`“) priima šiuos išankstinių nustatymų pavadinimus:

`NDVI, GNDVI, NDRE, OSAVI, SAVI, MSAVI2, EVI, MSR, TDVI, LAI, GCI, GRVI, GSAVI, GOSAVI, NLI, MNLI, RDVI, WDRVI, CVI, ENDVI, GLI, VARI`

{% hint style="warning" %}
**Nežinomi indeksų pavadinimai yra tyliai praleidžiami.** Pavadinimas, kuris nėra šiame sąraše (įskaitant penkias tik GUI skirtas formules `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` ir bet kokią vartotojo sukurtą formulę, kurią išsaugojote GUI) yra pašalinamas, tik pateikiant pranešimą žurnale — vykdymas tęsiasi be to indekso, o pats vykdymas vis tiek praneša apie sėkmę. Pranešimas atspausdinamas taip:

```
[INDEX_EXPAND] skipping unknown preset 'LCI'; known: ['CVI', 'ENDVI', 'EVI', ...]
```

Pavadinimai lyginami neatsižvelgiant į didžiųjų ir mažųjų raidžių skirtumą, pašalinus tarpelius, taigi „`ndvi`“, „`NDVI`“ ir „` NDVI `“ yra tas pats nustatymas. Išankstinis nustatymas taip pat praleidžiamas, jei jam reikalingas dažnių diapazonas, kurio jūsų fotoaparato filtras nepalaiko.
{% endhint %}

Tikslios įgyvendintos formulės (simboliai „`x`“, „`y`“ ir „`z`“ yra juostų lizdai; numatytasis priskyrimas pateikiamas pagal kiekvieną nustatymą):

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
| GSAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Green, NIR |
| GOSAVI | `(y-x)/(y+x+0.16)` | RGN | Green, NIR |
| NLI | `((y*y)-x)/((y*y)+x)` | RGN | Red, NIR |
| MNLI | `((y*y-x)*(1+0.5))/((y*y)+x+0.5)` | RGN | Red, NIR |
| RDVI | `(y-x)/sqrt(y+x)` | RGN | Red, NIR |
| WDRVI | `(0.2*y-x)/(0.2*y+x)` | RGN | Red, NIR |
| CVI | `(z/y)/(x/y)` | RGB | Red, Green, Blue |
| ENDVI | `((x+y)-(2*z))/((x+y)+(2*z))` | RGB | Red, Green, Blue |
| GLI | `((y-x)+(y-z))/((2*y)+x+z)` | RGB | Red, Green, Blue |
| VARI | `(y-x)/(y+x-z)` | RGB | Red, Green, Blue |

#### Kaip iš anksto nustatytas pavadinimas virsta juostų pozicijomis

Kai perduodate paprastą pavadinimą, pvz., `NDVI`, Chloros turi nuspręsti, kurį failo kanalą skaito kiekvienas simbolis. Tam naudojama ši lentelė, kurioje filtro kodas susiejamas su kiekvieno kanalo indekso pozicija masyve:

| Filtro kodas | Kanalas → masyvo indeksas |
| --- | --- |
| OCN | Orange 0, Cyan 1, NIR 2 (`Red` laikomas Orange sinonimu, taip pat 0) |
| RGN | Red 0, Green 1, NIR 2 |
| NGB | NIR 0, Green 1, Blue 2 |
| RGB | Red 0, Green 1, Blue 2 |
| RE | RE 0 |
| NIR | NIR 0 |

Išankstinio nustatymo **numatytoji filtras** („Numatytoji filtras“ stulpelis aukščiau) naudojamas, kai projekte yra vaizdų su tuo filtru. Jei jų nėra, Chloros nuskaito projekte iš tikrųjų esančius filtrus `RGN, OCN, NGB, RGB, RE, NIR` nurodyta tvarka ir pasirenka pirmąjį, kuris gali pateikti visus kanalus, kurių reikia išankstiniam nustatymui. Jei tokio , nustatymas to vykdymo metu atmetamas. Štai kodėl `NDVI`, taikytas duomenų rinkiniui, kuriame yra tik OCN, vis tiek duoda priimtiną rezultatą — jis susiejamas su OCNOrange ir NIR pozicijomis.

LATTICE M3C modelio eilutėse filtras nurodomas su priešdėliu `F` (`LATT-M3C-L41-FRGN`), tačiau priešdėlis pašalinamas, kai filtro kodas nuskaitomas iš vaizdo, todėl „FRGN“ kamera atpažįsta per viršutinę `RGN` eilutę ir nereikalauja jokio specialaus apdorojimo.

### 3. „LATTICE“ indeksavimo variklis (`lattice index --preset`, „Live Index Calculator“) — 22 iš anksto nustatyti parametrai

„LATTICE“ variklis veikia su suderintais daugiabandžiais vaizdų rinkiniais (tiesioginiais masyvais arba eksportuotais daugiabandžiais TIFF failais) ir naudoja mažosiomis raidėmis rašomus kanalų pavadinimus (`red`, `green`, `blue`, `red_edge`, `nir`). Jo nustatymų sąrašas skiriasi nuo dviejų pirmiau minėtų:

| Nustatymas | Formulė | Kanalai |
| --- | --- | --- |
| NDVI | `(nir - red) / (nir + red)` | raudonas, NIR |
| GNDVI | `(nir - green) / (nir + green)` | žalia, NIR |
| BNDVI | `(nir - blue) / (nir + blue)` | mėlyna, NIR |
| NDRE | `(nir - red_edge) / (nir + red_edge)` | raudona\_kraštas, nir |
| ENDVI | `((nir + green) - 2*blue) / ((nir + green) + 2*blue)` | mėlyna, žalia, nir |
| SAVI | `1.5 * (nir - red) / (nir + red + 0.5)` | raudona, nir |
| OSAVI | `1.5 * (nir - red) / (nir + red + 0.16)` | raudona, infraraudonasis |
| MSAVI | `(2*nir + 1 - sqrt((2*nir + 1)**2 - 8*(nir - red))) / 2` | raudona, infraraudonasis |
| EVI | `2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)` | mėlyna, raudona, NIR |
| EVI2 | `2.5 * (nir - red) / (nir + 2.4*red + 1)` | raudona, NIR |
| CVI | `(nir / green) - (red / green)` | raudona, žalia, NIR |
| MSR | `((nir/red) - 1) / (sqrt(nir/red) + 1)` | raudona, NIR |
| TDVI | `sqrt((nir - red) / (nir + red) + 0.5)` | raudona, NIR |
| LAI | `3.618 * ((nir - red) / (nir + 6*red - 7.5*green + 1)) - 0.118` | raudona, žalia, NIR |
| GLI | `(2*green - red - blue) / (2*green + red + blue)` | raudona, žalia, mėlyna |
| NGRDI | `(green - red) / (green + red)` | raudona, žalia |
| VARI | `(green - red) / (green + red - blue)` | raudona, žalia, mėlyna |
| TGI | `green - 0.39*red - 0.61*blue` | raudona, žalia, mėlyna |
| EXG | `2*green - red - blue` | raudona, žalia, mėlyna |
| CIRE | `(nir / red_edge) - 1` | raudona\_kraštas, nir |
| CIGREEN | `(nir / green) - 1` | žalia, nir |
| NDWI | `(green - nir) / (green + nir)` | žalia, nir |

Paleiskite komandą „`chloros-cli lattice index --list-presets`“, kad išspausdintumėte šią lentelę iš savo įdiegtos versijos, o komandą „`--list-gradients`“ – norėdami peržiūrėti galimus spalvų gradientus. Kanalų simboliai yra jautrūs didžiosioms ir mažosioms raidėms ir turi atitikti išankstinio nustatymo pavadinimus mažosiomis raidėmis (pvz., `--channel red=Red_660 --channel nir=NIR_850`).

***

## CVI

Kaip įgyvendinta grafinėje vartotojo sąsajoje ir išankstinių nustatymų sąraše CLI/SDK, CVI yra santykių santykio formulė:

$$
CVI = {(z / y) \over (x / y)}
$$

su numatytuoju RGB kanalų susiejimu x=Red, y = Green, z = Blue. Vartotojo sąsajoje galite bet kurį savo kameros kanalą perkelti į x/y/z lizdus. Atkreipkite dėmesį, kad „LATTICE“ indeksavimo variklio išankstinis nustatymas „`CVI`“ naudoja kitą formulę, „`(NIR / Green) - (Red / Green)`“ — patikrinkite aukščiau pateiktas lenteles, kad rastumėte jūsų naudojamą paviršių.

***

## ENDVI – patobulintas normalizuotas augmenijos skirtumo indeksas

Šis indeksas, be NIR ir žalios spalvos kanalų, naudoja mėlynąjį kanalą ir yra populiarus tarp NGB filtruotų kamerų, kuriose mėlynoji juosta pakeičia raudonąją.

$$
ENDVI = {(NIR + Green) - (2 * Blue) \over (NIR + Green) + (2 * Blue)}
$$

Įgyvendinimas atliekamas naudojant simbolinę formulę `((x+y)-(2*z))/((x+y)+(2*z))` — priskirkite savo kamerosNIR ir Green kanalus x/y lizdams, o Blue – z (jei kamera yra NGB: x=NIR, y=Green, z=Blue).

***

## EVI – patobulintas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudoti su MODIS duomenimis kaip patobulinta NDVI versija, optimizuojant augmenijos signalą srityse, kuriose didelis lapų ploto indeksas (LAI). Jis ypač naudingas regionuose, kur LAI vertės yra didelės, nes ten NDVI gali būti prisotintas. Jis naudoja mėlynosios spinduliuotės sritį, siekiant pakoreguoti dirvožemio fono signalus ir sumažinti atmosferos įtaką, įskaitant aerozolių sklaidą.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI reikšmės augmenijos pikseliuose turėtų būti nuo 0 iki 1. Šviesūs objektai, tokie kaip debesys ir balti pastatai, taip pat tamsūs objektai, tokie kaip vanduo, gali sukelti anomalines pikselių reikšmes EVI vaizde. Prieš kurdami EVI vaizdą, atspindžio vaizde turėtumėte užmaskuoti debesis ir ryškius objektus, o pasirinktinai – nustatyti pikselių verčių ribą nuo 0 iki 1.

_Šaltinis: Huete, A. ir kt. „MODIS augmenijos indeksų radiometrinių ir biofizinių savybių apžvalga“. „Remote Sensing of Environment“ 83 (2002): 195–213._

***

## FCI1 – Miškų dangos indeksas 1

_Tik GUI — nėra prieinamas kaip CLI/SDK `--indices` išankstinis nustatymas._

Šis indeksas atskiria miško lajos nuo kitų augmenijos tipų, naudodamas daugiaspektrinius atspindžio vaizdus, kuriuose yra raudonojo krašto juosta.

$$
FCI1 = Red * RedEdge
$$

Miškingose vietovėse FCI1 reikšmės bus mažesnės dėl mažesnio medžių atspindžio ir šešėlių buvimo medžių lajos viduje.

_Šaltinis: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miškų dangos indeksai daugiaspektriniams vaizdams“. „Photogrammetric Engineering &amp; Remote Sensing“ 84.8 (2018): 505–512._

***

## FCI2 – Miško dangos indeksas 2

_Tik GUI — nėra prieinamas kaip CLI/SDK `--indices` išankstinis nustatymas._

Šis indeksas atskiria miško lajos nuo kitų augmenijos tipų, naudojant multispektrinius atspindžio vaizdus, kuriuose nėra raudonojo krašto juostos.

$$
FCI2 = Red * NIR
$$

Miškingose vietovėse FCI2 reikšmės bus mažesnės dėl mažesnio medžių atspindžio ir šešėlių buvimo medžių vainikuose.

_Šaltinis: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miškų dangos indeksai daugiaspektriniams vaizdams.“ „Photogrammetric Engineering &amp; Remote Sensing“ 84.8 (2018): 505–512._

***

## GEMI – Pasaulinis aplinkos stebėjimo indeksas

_Tik GUI — nėra prieinamas kaip CLI/SDK `--indices` išankstinis nustatymas._

Šis nelinijinis augmenijos indeksas naudojamas pasaulinei aplinkos stebėsenai remiantis palydoviniais vaizdais ir siekia koreguoti atmosferos poveikį. Jis panašus į NDVI, tačiau yra mažiau jautrus atmosferos poveikiui. Jam įtakos turi plikas dirvožemis, todėl nerekomenduojama jo naudoti vietovėse, kuriose augmenija yra reta arba vidutiniškai tanki.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Kur:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Šaltinis: Pinty, B., ir M. Verstraete. GEMI: netiesinis indeksas pasaulinei augmenijai stebėti iš palydovų. „Vegetation 101“ (1992): 15–20._

***

## GARI – Green atmosferos poveikiui atsparus indeksas

_Tik GUI — nėra prieinamas kaip CLI/SDK `--indices` išankstinis nustatymas._

Šis indeksas yra jautresnis plačiam chlorofilo koncentracijų diapazonui ir mažiau jautrus atmosferos poveikiui nei NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama konstanta yra svorio funkcija, priklausanti nuo aerozolių sąlygų atmosferoje. ENVI naudoja vertę 1,7, kuri yra Gitelsono, Kaufmano ir Merzylako (1996, 296 psl.) rekomenduojama vertė.

_Šaltinis: Gitelson, A., Y. Kaufman ir M. Merzylak. „Green kanalo naudojimas pasaulinės augmenijos nuotolinio stebėjimo duomenims iš EOS-MODIS.“ „Remote Sensing of Environment“ 58 (1996): 289–298._

***

## GCI – Green chlorofilo indeksas

Šis indeksas naudojamas lapų chlorofilo kiekiui įvairių augalų rūšių įvertinti.

$$
GCI = {NIR \over Green} - 1
$$

Naudojant plačią NIR ir žaliųjų bangų ilgį, galima geriau prognozuoti chlorofilo kiekį, tuo pačiu užtikrinant didesnį jautrumą ir aukštesnį signalo ir triukšmo santykį.

_Nuoroda: Gitelson, A., Y. Gritz ir M. Merzlyak. „Ryšiai tarp lapų chlorofilo kiekio ir spektrinio atspindžio bei algoritmai, skirti neardomajam chlorofilo vertinimui aukštesniųjų augalų lapuose.“ *Journal of Plant Physiology* 160 (2003): 271–282._

***

## GLI – Green Lapų indeksas

Šis indeksas iš pradžių buvo sukurtas naudoti su skaitmenine RGB kamera kviečių dangos matavimui, kur raudoni, žali ir mėlyni skaitmeniniai skaičiai (DN) svyruoja nuo 0 iki 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI reikšmės svyruoja nuo -1 iki +1. Neigiamos reikšmės atspindi dirvožemį ir negyvosios gamtos elementus, o teigiamos reikšmės – žalius lapus ir stiebus.

_Šaltinis: Louhaichi, M., M. Borman ir D. Johnson. „Erdviškai lokalizuota platforma ir aerofotografija kviečių ganymo poveikio dokumentavimui“. „Geocarto International“ 16, Nr. 1 (2001): 65–70._

***

## GNDVI – Green Normalizuotas augmenijos skirtumo indeksas

Šis indeksas yra panašus į NDVI, išskyrus tai, kad jis matuoja žaliąjį spektrą nuo 540 iki 570 nm, o ne raudonąjį spektrą. Šis indeksas yra jautresnis chlorofilo koncentracijai nei NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Šaltinis: Gitelson, A., ir M. Merzlyak. „Chlorofilo koncentracijos aukštesniųjų augalų lapuose nuotolinis matavimas“. „Advances in Space Research“ 22 (1998): 689–692._

***

## GOSAVI – Green Optimizuotas dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudojant spalvotą infraraudonųjų spindulių fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis panašus į OSAVI, tačiau žalią juostą pakeičia raudona.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Šaltinis: Sripada, R. ir kt. „Kukurūzų azoto poreikio nustatymas auginimo sezono metu naudojant spalvotąją infraraudonąją aerofotografiją“. Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## GRVI – Green santykio vegetacijos indeksas

Šis indeksas jautriai reaguoja į fotosintezės intensyvumą miško lajos sluoksnyje, nes žalios ir raudonos spalvų atspindžio koeficientams didelę įtaką daro lapų pigmentų pokyčiai.

$$
GRVI = {NIR \over Green }
$$

_Šaltinis: Sripada, R. ir kt. „Spalvota infraraudonųjų spindulių aerofotografija kukurūzų azoto poreikiui sezono pradžioje nustatyti“. „Agronomy Journal“ 98 (2006): 968–977._

***

## GSAVI – Green Dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudojant spalvotąją infraraudonąją fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis panašus į SAVI, tačiau žalios juostos duomenys pakeisti raudonos juostos duomenimis.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Šaltinis: Sripada, R., ir kt. „Kukurūzų azoto poreikių nustatymas auginimo sezono metu naudojant spalvotąją infraraudonąją aerofotografiją“. Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## LAI – Lapų ploto indeksas

Šis indeksas naudojamas lapijos dangos apimčiai įvertinti bei pasėlių augimui ir derlingumui prognozuoti. ENVI apskaičiuoja žaliąjį LAI naudodama šią empirinę formulę, pateiktą Boegh ir kt. (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Kur EVI yra:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Didelės LAI vertės paprastai svyruoja nuo maždaug 0 iki 3,5. Tačiau, kai vaizde yra debesys ir kiti ryškūs elementai, dėl kurių susidaro prisotinti pikseliai, LAI reikšmės gali viršyti 3,5. Idealiu atveju prieš kuriant LAI vaizdą reikėtų iš vaizdo pašalinti debesis ir ryškius elementus.

_Šaltinis: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ir A. Thomsen. „Iš lėktuvo surinkti multispektriniai duomenys lapų ploto indekso, azoto koncentracijos ir fotosintezės efektyvumo žemės ūkyje kiekybiniam vertinimui.“ „Remote Sensing of Environment“ 81, Nr. 2–3 (2002): 179–193._

***

## LCI – Lapų chlorofilo indeksas

_Tik GUI — nėra prieinamas kaip CLI/SDK `--indices` išankstinis nustatymas._

Šis indeksas naudojamas aukštesniųjų augalų chlorofilo kiekiui įvertinti; jis jautrus atspindžio pokyčiams, kuriuos sukelia chlorofilo absorbcija.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Šaltinis: Datt, B. „Eukalipto lapų vandens kiekio nuotolinis matavimas“. „Journal of Plant Physiology“ 154, nr. 1 (1999): 30–36._

***

## MNLI – modifikuotas nelinijinis indeksas

Šis indeksas yra nelinijinio indekso (NLI) patobulinimas, į kurį įtrauktas dirvožemiui pritaikytas augmenijos indeksas (SAVI), siekiant atsižvelgti į dirvožemio foną. ENVI naudoja 0,5 vertės lajos fono koregavimo koeficientą (_L_).

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Šaltinis: Yang, Z., P. Willis ir R. Mueller. „Juostų santykio patobulinto AWIFS vaizdo įtaka pasėlių klasifikavimo tikslumui.“ Pecora 17 nuotolinio stebėjimo simpoziumo medžiaga (2008), Denveris, Koloradas._

***

## MSAVI2 – Modifikuotas dirvožemiu pakoreguotas augmenijos indeksas 2

Šis indeksas yra paprastesnė Qi ir kt. (1994) pasiūlyto indekso MSAVI versija, kuri patobulina dirvožemiu pakoreguotą augmenijos indeksą (SAVI). Jis sumažina dirvožemio triukšmą ir padidina augmenijos signalo dinaminį diapazoną. MSAVI2 pagrįstas indukciniu metodu, kuris nenaudoja pastovios _L_ vertės (kaip SAVI), siekiant išryškinti sveiką augmeniją.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Šaltinis: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ir S. Sorooshian. „A Modified Soil Adjusted Vegetation Index“ („Modifikuotas dirvožemiu pakoreguotas augmenijos indeksas“). „Remote Sensing of Environment“ 48 (1994): 119–126._

***

## MSR – modifikuotas paprastasis santykis

Šis indeksas yra modifikuotas paprastasis NIR/Red santykis, sukurtas siekiant linearizuoti jo ryšį su biofizikiniais parametrais, ir yra jautresnis nei NDVI esant didesniam augmenijos tankiui.

$$
MSR = {(NIR / Red) - 1 \over \sqrt{NIR / Red} + 1}
$$

_Šaltinis: Chen, J. „Evaluation of Vegetation Indices and a Modified Simple Ratio for Boreal Applications“ („Augmenijos indeksų ir modifikuoto paprastojo santykio vertinimas borealinėms taikmenoms“).“ Canadian Journal of Remote Sensing 22 (1996): 229–242._

***

## NDRE – normalizuotas skirtumas RedEdge

Šis indeksas yra panašus į NDVI, tačiau lygina NIR ir RedEdge kontrastą vietoj Red, kuris dažnai anksčiau aptinka augmenijos stresą.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI – Normalizuotas augmenijos skirtumo indeksas

Šis indeksas rodo sveiką, žalią augmeniją. Dėl normalizuoto skirtumo formulės ir chlorofilo didžiausio sugerties bei atspindžio sričių panaudojimo jis yra patikimas įvairiomis sąlygomis. Tačiau tankios augmenijos sąlygomis, kai LAI vertė tampa didelė, jis gali pasiekti prisotinimą.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Šio indekso vertė svyruoja nuo -1 iki 1. Įprastas žaliųjų augalų intervalas yra nuo 0,2 iki 0,8.

_Šaltinis: Rouse, J., R. Haas, J. Schell ir D. Deering. „Vegetacijos sistemų stebėjimas Didžiosiose lygumose naudojant ERTS“. Trečiasis ERTS simpoziumas, NASA (1973): 309–317._

***

## NLI – nelinijinis indeksas

Šis indeksas remiasi prielaida, kad ryšys tarp daugelio augmenijos indeksų ir paviršiaus biofizinių parametrų yra nelinijinis. Jis linearizuoja ryšius su paviršiaus parametrais, kurie paprastai yra nelinijiniai.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Šaltinis: Goel, N. ir W. Qin. „Lapijos struktūros įtaka ryšiams tarp įvairių augmenijos indeksų bei LAI ir Fpar: kompiuterinė simuliacija.“ „Remote Sensing Reviews“ 10 (1994): 309–347._

***

## OSAVI – Optimizuotas dirvožemiui pritaikytas augmenijos indeksas

Šis indeksas pagrįstas dirvožemiui pritaikytu augmenijos indeksu (SAVI). Jame naudojama standartinė 0,16 vertė lajos fono koregavimo koeficientui. Rondeaux (1996) nustatė, kad ši vertė užtikrina didesnį dirvožemio variabilumą nei SAVI esant menkai augmenijai, tuo pačiu parodydama didesnį jautrumą augmenijos dangai, didesnei nei 50 %. Šis indeksas geriausiai tinka vietovėse su palyginti retu augalų dangalu, kur dirvožemis matomas pro lajos viršų.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Šaltinis: Rondeaux, G., M. Steven ir F. Baret. „Optimization of Soil-Adjusted Vegetation Indices.“ Remote Sensing of Environment 55 (1996): 95–107._

***

## RDVI – renormalizuotas augmenijos skirtumo indeksas

Šis indeksas, kartu su NDVI, naudoja artimosios infraraudonosios ir raudonosios bangų ilgių skirtumą, siekdamas išryškinti sveiką augmeniją. Jis nėra jautrus dirvožemio ir saulės matymo geometrijos poveikiui.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Šaltinis: Roujean, J., ir F. Breon. „Vegetacijos sugertos PAR vertinimas remiantis dvikrypčio atspindžio matavimais.“ „Remote Sensing of Environment“ 51 (1995): 375–384._

***

## SAVI – Dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jis slopina dirvožemio pikselių poveikį. Jame naudojamas lajos fono koregavimo koeficientas _L_, kuris priklauso nuo augmenijos tankio ir dažnai reikalauja išankstinių žinių apie augmenijos kiekį. Huete (1988) siūlo optimalų _L_=0,5 dydį, siekiant atsižvelgti į pirmojo laipsnio dirvožemio fono svyravimus. Šį indeksą geriausia taikyti vietovėse su palyginti retu augmenijos sluoksniu, kur dirvožemis matomas pro medžių lają.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Šaltinis: Huete, A. „Dirvožemiu pakoreguotas augmenijos indeksas (SAVI).“ „Remote Sensing of Environment“ 25 (1988): 295–309._

***

## TDVI – transformuotas augmenijos skirtumo indeksas

Šis indeksas yra naudingas stebint augmenijos dangą miesto aplinkoje. Jis nesusisotina kaip NDVI ir SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Šaltinis: Bannari, A., H. Asalhi ir P. Teillet. „Transformuotas augmenijos skirtumo indeksas (TDVI) augmenijos dangos kartografavimui“ iš „Geomokslų ir nuotolinio stebėjimo simpoziumo, IGARSS &#x27;02, IEEE International, 5 tomas (2002)“._

***

## VARI – Matomasis atmosferos poveikiui atsparus indeksas

Šis indeksas pagrįstas ARVI ir naudojamas augmenijos daliai vaizde įvertinti, esant mažam jautrumui atmosferos poveikiui.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Šaltinis: Gitelson, A. ir kt. „Vegetation and Soil Lines in Visible Spectral Space: A Concept and Technique for Remote Estimation of Vegetation Fraction“. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI – plačios dinaminės srities augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jame naudojamas svorio koeficientas (_a_), siekiant sumažinti artimųjų infraraudonųjų ir raudonųjų signalų indėlių į NDVI skirtumus. WDRVI yra ypač veiksmingas vaizduose, kuriuose augmenijos tankis yra vidutinis araukštas augmenijos tankis, kai NDVI viršija 0,6. NDVI paprastai stabilizuojasi, kai augmenijos dalis ir lapų ploto indeksas (LAI) didėja, tuo tarpu WDRVI yra jautresnis platesniam augmenijos dalies intervalui ir LAI pokyčiams.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Svorio koeficientas (_a_) gali svyruoti nuo 0,1 iki 0,2. Henebry, Viña ir Gitelson (2004) rekomenduoja naudoti 0,2 vertę.

_Literatūra_

_Gitelson, A. „Plataus dinaminio diapazono augmenijos indeksas, skirtas augmenijos biofizinių charakteristikų nuotoliniam kiekybiniam vertinimui“. „Journal of Plant Physiology“ 161, Nr. 2 (2004): 165–173._

_Henebry, G., A. Viña ir A. Gitelson. „Plataus dinaminio diapazono vegetacijos indeksas ir jo potencialus pritaikymas spragų analizei.“ *Gap Analysis Bulletin* 12: 50–56._
