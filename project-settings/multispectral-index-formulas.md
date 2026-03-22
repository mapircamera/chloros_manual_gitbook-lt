---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Daugiaspektrinių indeksų formulės

Toliau pateiktose indeksų formulėse naudojami vidutinio pralaidumo intervalai, apimantys Survey3 filtro spektrą:

<table><thead><tr><th align="center">Survey3 filtro spalva</th><th width="196.199951171875" align="center">Survey3 filtro pavadinimas</th><th width="159.800048828125" align="center">Pralaidumo diapazonas (FWHM)</th><th align="center">Vidutinė pralaidumo vertė</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB – Blue</td><td align="center">468–483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN– Cyan</td><td align="center">476–512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543–558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN – Orange</td><td align="center">598–640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN – Red</td><td align="center">653–668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712–735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798–848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835–865 nm</td><td align="center">850 nm</td></tr></tbody></table>Naudojant šias formules, pavadinimas gali baigtis „\_1“ arba „\_2“, o tai atitinka, kuris NIR filtras, NIR1 arba NIR2, buvo naudojamas.

***

## EVI – patobulintas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudoti su MODIS duomenimis kaip NDVI patobulinimas, optimizuojant augmenijos signalą vietovėse, kuriose yra didelis lapų ploto indeksas (LAI). Jis yra naudingiausias regionuose, kur LAI vertės yra didelės, o NDVI gali būti prisotintas. Jis naudoja mėlynosios atspindžio sritį, kad pakoreguotų dirvožemio fono signalus ir sumažintų atmosferos įtaką, įskaitant aerozolio sklaidą.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI vertės augmenijos pikseliams turėtų būti nuo 0 iki 1. Ryškūs objektai, tokie kaip debesys ir balti pastatai, kartu su tamsiais objektais, tokiais kaip vanduo, gali sukelti anomalų pikselių verčių atsiradimą EVI vaizde. Prieš kuriant EVI vaizdą, turėtumėte iš atspindžio vaizdo pašalinti debesis ir ryškius objektus, o pasirinktinai nustatyti pikselių verčių ribą nuo 0 iki 1.

_Nuoroda: Huete, A., ir kt. „MODIS augmenijos indeksų radiometrinių ir biofizinių charakteristikų apžvalga“. „Remote Sensing of Environment“ 83 (2002): 195–213._

***

## FCI1 – Miškų dangos indeksas 1

Šis indeksas atskiria miško lajos nuo kitų augmenijos tipų, naudodamas daugiaspektrinius atspindžio vaizdus, kuriuose yra raudonojo krašto juosta.

$$
FCI1 = Red * RedEdge
$$

Miškingose vietovėse FCI1 vertės bus mažesnės dėl mažesnio medžių atspindžio ir šešėlių buvimo lajos viduje.

_Šaltinis: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miškų dangos indeksai daugiaspektriniams vaizdams.“ Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505–512._

***

## FCI2 - Miško dangos indeksas 2

Šis indeksas atskiria miško lają nuo kitų augmenijos tipų, naudodamas daugiaspektrinius atspindžio vaizdus, kuriuose nėra raudonojo krašto juostos.

$$
FCI2 = Red * NIR
$$

Miškingose vietovėse FCI2 reikšmės bus mažesnės dėl mažesnio medžių atspindžio ir šešėlių buvimo lajos viduje.

_Šaltinis: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miškų dangos indeksai daugiaspektriniams vaizdams.“ Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505–512._

***

## GEMI - Pasaulinis aplinkos stebėjimo indeksas

Šis nelinijinis augmenijos indeksas naudojamas pasaulinei aplinkos stebėjimui pagal palydovinius vaizdus ir siekia koreguoti atmosferos poveikį. Jis panašus į NDVI, tačiau yra mažiau jautrus atmosferos poveikiui. Jam įtakos turi plika dirva; todėl jo nerekomenduojama naudoti vietovėse, kuriose augmenija yra reta arba vidutiniškai tanki.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Kur:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Nuoroda: Pinty, B., ir M. Verstraete. GEMI: netiesinis indeksas pasaulinei augmenijai stebėti iš palydovų. Vegetation 101 (1992): 15–20._

***

## GARI – Green: atmosferos poveikiui atsparus indeksas

Šis indeksas yra jautresnis plačiam chlorofilo koncentracijų diapazonui ir mažiau jautrus atmosferos poveikiui nei NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama konstanta yra svorio funkcija, priklausanti nuo aerozolių sąlygų atmosferoje. ENVI naudoja vertę 1,7, kuri yra rekomenduojama Gitelson, Kaufman ir Merzylak (1996, 296 puslapis).

_Nuoroda: Gitelson, A., Y. Kaufman ir M. Merzylak. „Green kanalo naudojimas pasaulinės augmenijos nuotolinėje stebėsenoje iš EOS-MODIS.“ Remote Sensing of Environment 58 (1996): 289–298._

***

## GCI – Green chlorofilo indeksas

Šis indeksas naudojamas įvairių augalų rūšių lapų chlorofilo kiekiui įvertinti.

$$
GCI = {NIR \over Green} - 1
$$

Platus NIR ir žaliųjų bangų ilgis leidžia geriau prognozuoti chlorofilo kiekį, tuo pačiu užtikrinant didesnį jautrumą ir aukštesnį signalo ir triukšmo santykį.

_Šaltinis: Gitelson, A., Y. Gritz ir M. Merzlyak. „Ryšiai tarp lapų chlorofilo kiekio ir spektrinio atspindžio bei algoritmai neardomajam chlorofilo vertinimui aukštesniųjų augalų lapuose.“ Journal of Plant Physiology 160 (2003): 271–282._

***

## GLI – Green Lapų indeksas

Šis indeksas iš pradžių buvo sukurtas naudoti su skaitmenine RGB kamera kviečių dangos matavimui, kur raudoni, žali ir mėlyni skaitmeniniai skaičiai (DN) svyruoja nuo 0 iki 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI reikšmės svyruoja nuo -1 iki +1. Neigiamos reikšmės atspindi dirvožemį ir negyvosios gamtos elementus, o teigiamos reikšmės – žalius lapus ir stiebus.

_Nuoroda: Louhaichi, M., M. Borman ir D. Johnson. „Erdviškai lokalizuota platforma ir aerofotografija kviečių ganymo poveikio dokumentavimui“. Geocarto International 16, Nr. 1 (2001): 65–70._

***

## GNDVI – Green Normalizuotas augmenijos skirtumo indeksas

Šis indeksas yra panašus į NDVI, išskyrus tai, kad jis matuoja žalią spektrą nuo 540 iki 570 nm, o ne raudoną spektrą. Šis indeksas yra jautresnis chlorofilo koncentracijai nei NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Šaltinis: Gitelson, A., ir M. Merzlyak. „Aukštesniųjų augalų lapų chlorofilo koncentracijos nuotolinis matavimas.“ Advances in Space Research 22 (1998): 689–692._

***

## GOSAVI – Green Optimizuotas dirvožemiui pritaikytas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudojant spalvotą infraraudonąją fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis panašus į OSAVI, tačiau raudoną juostą pakeičia žalia.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Šaltinis: Sripada, R., et al. „Kukurūzų azoto poreikio sezono metu nustatymas naudojant spalvotą infraraudonąją aerofotografiją.“ Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## GRVI – Green santykio augmenijos indeksas

Šis indeksas yra jautrus fotosintezės intensyvumui miško lajos sluoksnyje, nes žalios ir raudonos spalvų atspindžius stipriai veikia lapų pigmentų pokyčiai.

$$
GRVI = {NIR \over Green }
$$

_Nuoroda: Sripada, R., et al. „Aerial Color Infrared Photography for Determining Early In-season Nitrogen Requirements in Corn.“ Agronomy Journal 98 (2006): 968–977._

***

## GSAVI – Green Dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudojant spalvotąją infraraudonąją fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis yra panašus į SAVI, tačiau raudoną juostą pakeičia žalia.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Nuoroda: Sripada, R., et al. „Kukurūzų azoto poreikio nustatymas auginimo sezono metu naudojant spalvotą infraraudonąją aerofotografiją.“ Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## LAI – Lapų ploto indeksas

Šis indeksas naudojamas lapijos dangos įvertinimui bei pasėlių augimo ir derlingumo prognozavimui. ENVI apskaičiuoja žaliąjį LAI naudodamas šią empirinę formulę iš Boegh ir kt. (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Kur EVI yra:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Aukštos LAI vertės paprastai svyruoja nuo maždaug 0 iki 3,5. Tačiau, kai vaizde yra debesys ir kiti ryškūs elementai, dėl kurių susidaro prisotinti pikseliai, LAI vertės gali viršyti 3,5. Idealiu atveju prieš kuriant LAI vaizdą reikėtų užmaskuoti debesis ir ryškius elementus.

_Nuoroda: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ir A. Thomsen. „Oro multispektriniai duomenys lapų ploto indekso, azoto koncentracijos ir fotosintezės efektyvumo žemės ūkyje kiekybiniam vertinimui“. Remote Sensing of Environment 81, nr. 2–3 (2002): 179–193._

***

## LCI – Lapų chlorofilo indeksas

Šis indeksas naudojamas aukštesniųjų augalų chlorofilo kiekiui įvertinti, jis jautrus atspindžio pokyčiams, kuriuos sukelia chlorofilo absorbcija.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Nuoroda: Datt, B. „Eukalipto lapų vandens kiekio nuotolinis stebėjimas.“ Journal of Plant Physiology 154, nr. 1 (1999): 30–36._

***

## MNLI – modifikuotas nelinijinis indeksas

Šis indeksas yra nelinijinio indekso (NLI) patobulinimas, į kurį įtrauktas dirvožemiui pritaikytas augmenijos indeksas (SAVI), siekiant atsižvelgti į dirvožemio foną. ENVI naudoja 0,5 vertės lajos fono koregavimo koeficientą (_L_).

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Nuoroda: Yang, Z., P. Willis ir R. Mueller. „Band-Ratio Enhanced AWIFS Image to Crop Classification Accuracy“ („Juostų santykio patobulinto AWIFS vaizdo įtaka pasėlių klasifikavimo tikslumui“). Pecora 17 nuotolinio stebėjimo simpoziumo (2008 m.) medžiaga, Denveris, Koloradas._

***

## MSAVI2 – Modifikuotas dirvožemiu pakoreguotas augmenijos indeksas 2

Šis indeksas yra paprastesnė Qi ir kt. (1994) pasiūlyto MSAVI indekso versija, kuri patobulina dirvožemiu pakoreguotą augmenijos indeksą (SAVI). Jis sumažina dirvožemio triukšmą ir padidina augmenijos signalo dinaminį diapazoną. MSAVI2 pagrįstas indukciniu metodu, kuris nenaudoja pastovios _L_ vertės (kaip SAVI), siekiant išryškinti sveiką augmeniją.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Nuoroda: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ir S. Sorooshian. „A Modified Soil Adjusted Vegetation Index.“ Remote Sensing of Environment 48 (1994): 119–126._

***

## NDRE – Normalizuotas skirtumas RedEdge

Šis indeksas yra panašus į NDVI, tačiau lygina NIR ir RedEdge kontrastą vietoj Red, kuris dažnai anksčiau aptinka augmenijos stresą.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI – Normalizuotas augmenijos skirtumo indeksas

Šis indeksas yra sveikos, žalios augmenijos matas. Normalizuoto skirtumo formulės ir didžiausio chlorofilo sugerties bei atspindžio sričių naudojimo derinys užtikrina jo patikimumą įvairiomis sąlygomis. Tačiau jis gali pasiekti sotį tankios augmenijos sąlygomis, kai LAI tampa didelis.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Šio indekso vertė svyruoja nuo -1 iki 1. Įprastas žaliųjų augalų intervalas yra nuo 0,2 iki 0,8.

_Šaltinis: Rouse, J., R. Haas, J. Schell ir D. Deering. „Vegetacijos sistemų stebėjimas Didžiosiose lygumose naudojant ERTS“. Trečiasis ERTS simpoziumas, NASA (1973): 309–317._

***

## NLI – nelinijinis indeksas

Šis indeksas remiasi prielaida, kad ryšys tarp daugelio vegetacijos indeksų ir paviršiaus biofizinių parametrų yra nelinijinis. Jis linearizuoja ryšius su paviršiaus parametrais, kurie paprastai yra nelinijiniai.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Nuoroda: Goel, N. ir W. Qin. „Lapijos struktūros įtaka ryšiams tarp įvairių augmenijos indeksų ir LAI bei Fpar: kompiuterinė simuliacija.“ Remote Sensing Reviews 10 (1994): 309–347._

***

## OSAVI – Optimizuotas dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas pagrįstas dirvožemiu pakoreguotu augmenijos indeksu (SAVI). Jis naudoja standartinę 0,16 vertę lajos fono koregavimo koeficientui. Rondeaux (1996) nustatė, kad ši vertė užtikrina didesnį dirvožemio variacijų atspindėjimą nei SAVI esant menkai augmenijai, tuo pačiu parodydama didesnį jautrumą augmenijai, viršijančiai 50 %. Šis indeksas geriausiai tinka vietovėse su palyginti reta augmenija, kur dirvožemis matomas pro lajos viršų.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Šaltinis: Rondeaux, G., M. Steven ir F. Baret. „Optimization of Soil-Adjusted Vegetation Indices.“ Remote Sensing of Environment 55 (1996): 95–107._

***

## RDVI – Renormalizuotas augmenijos skirtumo indeksas

Šis indeksas naudoja artimosios infraraudonosios ir raudonosios bangų ilgių skirtumą kartu su NDVI, siekiant išryškinti sveiką augmeniją. Jis yra nejautrus dirvožemio ir saulės matymo geometrijos poveikiui.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Nuoroda: Roujean, J., ir F. Breon. „Augalijos sugertos PAR vertinimas pagal dvikrypčio atspindžio matavimus.“ Remote Sensing of Environment 51 (1995): 375–384._

***

## SAVI – Dirvožemiu pakoreguotas augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jis slopina dirvožemio pikselių poveikį. Jis naudoja lajos fono koregavimo koeficientą _L_, kuris yra augmenijos tankio funkcija ir dažnai reikalauja išankstinių žinių apie augmenijos kiekį. Huete (1988) siūlo optimalų _L_=0,5 vertę, siekiant atsižvelgti į pirmojo laipsnio dirvožemio fono svyravimus. Šis indeksas geriausiai tinka vietovėse su palyginti retu augmenijos tankumu, kur dirvožemis matomas pro lajos viršų.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Nuoroda: Huete, A. „A Soil-Adjusted Vegetation Index (SAVI).“ Remote Sensing of Environment 25 (1988): 295–309._

***

## TDVI – transformuotas skirtumų augmenijos indeksas

Šis indeksas yra naudingas stebint augmenijos dangą miesto aplinkoje. Jis nesusisotina kaip NDVI ir SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Nuoroda: Bannari, A., H. Asalhi ir P. Teillet. „Transformuotas augmenijos skirtumo indeksas (TDVI) augmenijos dangos kartografavimui“ Geomokslų ir nuotolinio stebėjimo simpoziumo, IGARSS &#x27;02, IEEE International, 5 tomas (2002 m.), medžiagoje._

***

## VARI – Matomas atmosferos poveikiui atsparus indeksas

Šis indeksas pagrįstas ARVI ir naudojamas augmenijos daliai vaizde įvertinti, esant mažam jautrumui atmosferos poveikiui.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Nuoroda: Gitelson, A. ir kt. „Augmenijos ir dirvožemio linijos matomajame spektriniame erdvėje: koncepcija ir technika augmenijos dalies nuotoliniam įvertinimui“. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI – Platus dinaminis augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jame naudojamas svorio koeficientas (_a_), siekiant sumažinti artimosios infraraudonosios ir raudonosios spinduliuotės indėlių į NDVI skirtumą. WDRVI yra ypač veiksmingas vaizduose, kuriuose augmenijos tankis yra vidutinis ar didelis, kai NDVI viršija 0,6. NDVI paprastai stabilizuojasi, kai augmenijos dalis ir lapų ploto indeksas (LAI) didėja, tuo tarpu WDRVI yra jautresnis platesniam augmenijos dalių diapazonui ir LAI pokyčiams.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Svorio koeficientas (_a_) gali svyruoti nuo 0,1 iki 0,2. Henebry, Viña ir Gitelson (2004) rekomenduoja vertę 0,2.

_Nuorodos_

_Gitelson, A. „Plataus dinaminio diapazono augmenijos indeksas augmenijos biofizinių charakteristikų nuotoliniam kiekybiniam vertinimui“. Journal of Plant Physiology 161, Nr. 2 (2004): 165–173._

_Henebry, G., A. Viña ir A. Gitelson. „Plataus dinaminio diapazono augmenijos indeksas ir jo potencialus naudingumas atotrūkio analizei.“ Gap Analysis Bulletin 12: 50–56._
