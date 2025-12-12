---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Daugiaspektrinio indekso formulės

Toliau pateiktose indekso formulėse naudojamas Survey3 filtro vidutinio pralaidumo diapazonų derinys:

<table><thead><tr><th align="center">Survey3 filtro spalva</th><th width="196.199951171875" align="center">Survey3 filtro pavadinimas</th><th width="159.800048828125" align="center">Pralaidumo diapazonas (FWHM)</th><th align="center">Vidutinis perdavimo koeficientas</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468–483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476–512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543–558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598–640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653–668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712–735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798–848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835–865 nm</td><td align="center">850 nm</td></tr></tbody></table>Naudojant šias formules, pavadinimas gali baigtis „\_1“ arba „\_2“, kas atitinka, kuris NIR filtras, NIR1 arba NIR2, buvo naudojamas.

***

## EVI – patobulintas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudoti su MODIS duomenimis kaip NDVI patobulinimas, optimizuojant augmenijos signalą didelio lapų ploto indekso (LAI). Jis yra naudingiausias regionuose, kur LAI yra didelis, o NDVI gali būti prisotintas. Jis naudoja mėlynosios spalvos atspindžio sritį, kad pakoreguotų dirvožemio fono signalus ir sumažintų atmosferos įtaką, įskaitant aerozolio sklaidą.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI vertės turėtų būti nuo 0 iki 1 augmenijos pikseliams. Ryškūs elementai, tokie kaip debesys ir balti pastatai, kartu su tamsiomis savybėmis, tokiomis kaip vanduo, gali sukelti anomalų pikselių verčių EVI vaizde. Prieš kuriant EVI vaizdą, turėtumėte užmaskuoti debesis ir ryškius elementus atspindžio vaizde ir, jei norite, nustatyti pikselių verčių ribą nuo 0 iki 1.

_Nuoroda: Huete, A., et al. „MODIS augmenijos indeksų radiometrinės ir biofizikinės charakteristikos apžvalga“. Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 – Miškų dangos indeksas 1

Šis indeksas atskiria miškų lajas nuo kitų augmenijos tipų, naudojant daugiaspektrinius atspindžio vaizdus, kurie apima raudoną kraštinę juostą.

$$
FCI1 = Red * RedEdge
$$

Miškingos vietovės turės mažesnes FCI1 vertes dėl mažesnio medžių atspindžio ir šešėlių buvimo lajos viduje.

_Nuoroda: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miško dangos indeksai daugiaspektriams vaizdams“. Fotogrametrinė inžinerija ir nuotolinis stebėjimas 84.8 (2018): 505–512._

***

## FCI2 – Miško dangos indeksas 2

Šis indeksas atskiria miško lajos nuo kitų augmenijos tipų, naudojant daugiaspektrinius atspindžio vaizdus, kuriuose nėra raudonos krašto juostos.

$$
FCI2 = Red * NIR
$$

Miškingos vietovės turės mažesnes FCI2 vertes dėl mažesnio medžių atspindžio ir šešėlių buvimo lajos viduje.

_Nuoroda: Becker, Sarah J., Craig S.T. Daughtry ir Andrew L. Russ. „Patikimi miškų dangos indeksai daugiaspektriams vaizdams“. Fotogrametrinė inžinerija ir nuotolinis stebėjimas 84.8 (2018): 505–512._

***

## GEMI – Pasaulinis aplinkos stebėjimo indeksas

Šis netiesinis augmenijos indeksas naudojamas pasaulinei aplinkos stebėsenai iš palydovinių vaizdų ir bandoma koreguoti atmosferos poveikį. Jis panašus į NDVI, bet yra mažiau jautrus atmosferos poveikiui. Jam daro įtaką plikas dirvožemis, todėl jo nerekomenduojama naudoti retų arba vidutiniškai tankių augmenijų vietovėse.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Kur:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Nuoroda: Pinty, B., ir M. Verstraete. GEMI: netiesinis indeksas pasaulinei augmenijai stebėti iš palydovų. Vegetation 101 (1992): 15-20._

***

## GARI - Green Atmosferos atsparumo indeksas

Šis indeksas yra jautresnis įvairioms chlorofilo koncentracijoms ir mažiau jautrus atmosferos poveikiui nei NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama konstanta yra svertinė funkcija, kuri priklauso nuo aerozolio sąlygų atmosferoje. ENVI naudoja 1,7 vertę, kuri yra rekomenduojama Gitelson, Kaufman ir Merzylak (1996, 296 puslapis).

_Nuoroda: Gitelson, A., Y. Kaufman ir M. Merzylak. „Green kanalo naudojimas nuotolinio stebėjimo sistemoje EOS-MODIS, skirtoje pasaulio augmenijai stebėti.“ Nuotolinis aplinkos stebėjimas 58 (1996): 289–298._

***

## GCI – Green chlorofilo indeksas

Šis indeksas naudojamas įvairių augalų rūšių lapų chlorofilo kiekiui įvertinti.

$$
GCI = {NIR \over Green} - 1
$$

Platus NIR ir žalios bangos ilgis leidžia geriau prognozuoti chlorofilo kiekį, tuo pačiu užtikrinant didesnį jautrumą ir geresnį signalo ir triukšmo santykį.

_Nuoroda: Gitelson, A., Y. Gritz ir M. Merzlyak. „Ryšiai tarp lapų chlorofilo kiekio ir spektrinio atspindžio bei algoritmai neardomam chlorofilo kiekiui aukštesniųjų augalų lapuose įvertinti“. Journal of Plant Physiology 160 (2003): 271–282._

***

## GLI – Green Lapų indeksas

Šis indeksas iš pradžių buvo sukurtas naudoti su skaitmenine RGB kamera kviečių dangos matavimui, kur raudoni, žali ir mėlyni skaitmeniniai skaičiai (DN) svyruoja nuo 0 iki 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI reikšmės svyruoja nuo -1 iki +1. Neigiamos reikšmės reiškia dirvožemį ir negyvas savybes, o teigiamos reikšmės reiškia žalius lapus ir stiebus.

_Nuoroda: Louhaichi, M., M. Borman ir D. Johnson. „Erdvėje lokalizuota platforma ir aerofotografija, skirta dokumentuoti ganymo poveikį kviečiams.“ Geocarto International 16, Nr. 1 (2001): 65-70._

***

## GNDVI - Green Normalizuotas augmenijos skirtumo indeksas

Šis indeksas yra panašus į NDVI, išskyrus tai, kad jis matuoja žalią spektrą nuo 540 iki 570 nm, o ne raudoną spektrą. Šis indeksas yra jautresnis chlorofilo koncentracijai nei NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Nuoroda: Gitelson, A., ir M. Merzlyak. „Aukštesniųjų augalų lapų chlorofilo koncentracijos nuotolinis matavimas“. Advances in Space Research 22 (1998): 689–692._

***

## GOSAVI – Green Optimizuotas dirvožemio koreguotas augmenijos indeksas

Šis indeksas iš pradžių buvo sukurtas naudojant spalvotą infraraudonąją fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis yra panašus į OSAVI, tačiau žalią juostą pakeičia raudona.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Nuoroda: Sripada, R., et al. „Kukurūzų azoto poreikio sezono metu nustatymas naudojant spalvotą infraraudonąją fotografiją iš oro“. Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## GRVI – Green Santykio augmenijos indeksas

Šis indeksas yra jautrus fotosintezės greičiui miško lajos, nes žalios ir raudonos spalvos atspindžiai yra stipriai veikiami lapų pigmentų pokyčių.

$$
GRVI = {NIR \over Green }
$$

_Nuoroda: Sripada, R., et al. „Aerial Color Infrared Photography for Determining Early In-season Nitrogen Requirements in Corn“ (Oro spalvų infraraudonųjų spindulių fotografija, skirta nustatyti ankstyvojo sezono azoto poreikį kukurūzams). Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - Green Dirvožemio koreguotas augmenijos indeksas

Šis indeksas buvo sukurtas naudojant spalvotą infraraudonąją fotografiją, siekiant prognozuoti azoto poreikį kukurūzams. Jis yra panašus į SAVI, tačiau žalią juostą pakeičia raudona.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Nuoroda: Sripada, R., et al. „Kukurūzų azoto poreikio sezono metu nustatymas naudojant spalvotą infraraudonąją fotografiją iš oro.“ Daktaro disertacija, Šiaurės Karolinos valstybinis universitetas, 2005 m._

***

## LAI – Lapų ploto indeksas

Šis indeksas naudojamas lapijos dangai įvertinti ir derliaus augimui bei derlingumui prognozuoti. ENVI apskaičiuoja žalią LAI naudodamas šią empirinę formulę iš Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Kur EVI yra:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Aukštos LAI vertės paprastai svyruoja nuo maždaug 0 iki 3,5. Tačiau, kai vaizde yra debesys ir kiti ryškūs elementai, kurie sukuria prisotintus pikselius, LAI vertės gali viršyti 3,5. Idealiu atveju prieš kuriant LAI vaizdą reikėtų užmaskuoti debesis ir ryškius elementus vaizde.

_Nuoroda: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ir A. Thomsen. „Oro daugiaspektriniai duomenys, skirti lapų ploto indeksui, azoto koncentracijai ir fotosintezės efektyvumui žemės ūkyje kiekybiškai įvertinti“. Remote Sensing of Environment 81, nr. 2-3 (2002): 179-193._

***

## LCI – lapų chlorofilo indeksas

Šis indeksas naudojamas chlorofilo kiekiui aukštesniuose augaluose įvertinti, kurie yra jautrūs chlorofilo absorbcijos sukeltiems atspindžio pokyčiams.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Nuoroda: Datt, B. „Eukalipto lapų vandens kiekio nuotolinis stebėjimas“. Augalų fiziologijos žurnalas 154, nr. 1 (1999): 30–36._

***

## MNLI – modifikuotas nelinijinis indeksas

Šis indeksas yra netiesinio indekso (NLI) patobulinimas, įtraukiant dirvožemio koreguotą augmenijos indeksą (SAVI), siekiant atsižvelgti į dirvožemio foną. ENVI naudoja 0,5 vertės lajos fono koregavimo koeficientą (_L_).

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Nuoroda: Yang, Z., P. Willis ir R. Mueller. „Pagerinto AWIFS vaizdo juostos santykio poveikis pasėlių klasifikavimo tikslumui“. Pecora 17 nuotolinio stebėjimo simpoziumo (2008 m.) medžiaga, Denveris, Koloradas._

***

## MSAVI2 – modifikuotas dirvožemio koreguotas augmenijos indeksas 2

Šis indeksas yra paprastesnė Qi ir kt. (1994) pasiūlyto MSAVI indekso versija, kuri patobulina dirvožemio koreguotą augmenijos indeksą (SAVI). Jis sumažina dirvožemio triukšmą ir padidina augmenijos signalo dinaminį diapazoną. MSAVI2 yra pagrįstas indukciniu metodu, kuris nenaudoja pastovios _L_ vertės (kaip SAVI), kad būtų išryškintas sveikas augalas.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Nuoroda: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ir S. Sorooshian. „Modifikuotas dirvožemio koreguotas augmenijos indeksas“. Nuotolinis aplinkos stebėjimas 48 (1994): 119–126._

***

## NDRE – normalizuotas skirtumas RedEdge

Šis indeksas yra panašus į NDVI, tačiau lygina kontrastą tarp NIR ir RedEdge, o ne Red, kuris dažnai anksčiau aptinka augmenijos stresą.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI – normalizuotas augmenijos skirtumo indeksas

Šis indeksas yra sveikos, žalios augmenijos matas. Normalizuoto skirtumo formulės ir chlorofilo didžiausio sugėrimo bei atspindžio regionų naudojimo derinys užtikrina jo patikimumą įvairiomis sąlygomis. Tačiau jis gali būti prisotintas tankios augmenijos sąlygomis, kai LAI tampa didelis.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Šio indekso vertė svyruoja nuo -1 iki 1. Įprastas žalios augmenijos intervalas yra nuo 0,2 iki 0,8.

_Nuoroda: Rouse, J., R. Haas, J. Schell ir D. Deering. Augmenijos sistemų stebėjimas Didžiosiose lygumose naudojant ERTS. Trečiasis ERTS simpoziumas, NASA (1973): 309–317._

***

## NLI – netiesinis indeksas

Šis indeksas remiasi prielaida, kad daugelio augmenijos indeksų ir paviršiaus biofizinių parametrų santykis yra netiesinis. Jis linearizuoja santykius su paviršiaus parametrais, kurie paprastai yra netiesiniai.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Nuoroda: Goel, N. ir W. Qin. „Lapijos struktūros įtaka įvairių augmenijos indeksų ir LAI bei Fpar santykiams: kompiuterinė simuliacija.“ Remote Sensing Reviews 10 (1994): 309–347._

***

## OSAVI – optimizuotas dirvožemio koreguotas augmenijos indeksas

Šis indeksas pagrįstas dirvožemio koreguotu augmenijos indeksu (SAVI). Jis naudoja standartinę 0,16 vertę lajos fono koregavimo koeficientui. Rondeaux (1996) nustatė, kad ši vertė užtikrina didesnį dirvožemio variavimą nei SAVI esant mažam augmenijos dangui, tuo pačiu parodydama didesnį jautrumą augmenijos dangui, didesniam nei 50 %. Šis indeksas geriausiai tinka naudoti vietovėse, kuriose augmenija yra palyginti reta ir dirvožemis matomas per lajos dangą.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Nuoroda: Rondeaux, G., M. Steven ir F. Baret. „Dirvožemio koreguotų augmenijos indeksų optimizavimas“. Nuotolinis aplinkos stebėjimas 55 (1996): 95–107._

***

## RDVI – Renormalizuotas augmenijos skirtumo indeksas

Šis indeksas naudoja skirtumą tarp artimosios infraraudonosios ir raudonosios bangų ilgių kartu su NDVI, kad būtų išryškinti sveiki augalai. Jis yra nejautrus dirvožemio ir saulės matymo geometrijos poveikiui.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Nuoroda: Roujean, J., ir F. Breon. „Augmenijos absorbuojamo PAR įvertinimas pagal dvikrypčio atspindžio matavimus.“ Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI – dirvožemio pakoreguotas augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jis slopina dirvožemio pikselių poveikį. Jis naudoja lajos fono koregavimo koeficientą _L_, kuris yra augmenijos tankio funkcija ir dažnai reikalauja išankstinių žinių apie augmenijos kiekį. Huete (1988) siūlo optimalų _L_=0,5 vertę, kad būtų atsižvelgta į pirmos eilės dirvožemio fono pokyčius. Šis indeksas geriausiai tinka naudoti vietovėse, kuriose augmenija yra palyginti reta ir dirvožemis matomas per lają.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Nuoroda: Huete, A. „Dirvožemio koreguotas augmenijos indeksas (SAVI).“ Nuotolinis aplinkos stebėjimas 25 (1988): 295–309._

***

## TDVI – transformuotas skirtumo augmenijos indeksas

Šis indeksas yra naudingas stebint augmenijos dangą miesto aplinkoje. Jis nesaturuoja kaip NDVI ir SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Nuoroda: Bannari, A., H. Asalhi ir P. Teillet. „Transformuotas skirtumo augmenijos indeksas (TDVI) augmenijos dangos kartografavimui“ Geomokslų ir nuotolinio stebėjimo simpoziumo IGARSS &#x27;02, IEEE International, 5 tomas (2002) medžiagoje._

***

## VARI – matomas atmosferos poveikiui atsparus indeksas

Šis indeksas pagrįstas ARVI ir naudojamas augmenijos daliai vaizde, kuri yra mažai jautri atmosferos poveikiui, įvertinti.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Nuoroda: Gitelson, A., et al. „Augmenija ir dirvožemio linijos matomame spektriniame erdvėje: koncepcija ir technika augmenijos dalies nuotoliniam įvertinimui. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI – Platus dinaminis diapazonas augmenijos indeksas

Šis indeksas yra panašus į NDVI, tačiau jame naudojamas svertinis koeficientas (_a_), siekiant sumažinti skirtumą tarp artimosios infraraudonosios ir raudonosios spinduliuotės įtakos NDVI. WDRVI yra ypač veiksmingas scenose, kuriose augmenijos tankis yra vidutinis arba didelis, kai NDVI viršija 0,6. NDVI linkęs išsilyginti, kai augmenijos dalis ir lapų ploto indeksas (LAI) didėja, o WDRVI yra jautresnis platesniam augmenijos dalių diapazonui ir LAI pokyčiams.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Svorio koeficientas (_a_) gali būti nuo 0,1 iki 0,2. Henebry, Viña ir Gitelson (2004) rekomenduoja vertę 0,2.

_Nuorodos_

_Gitelson, A. „Platus dinaminis augmenijos indeksas, skirtas nuotoliniam augmenijos biofizinių charakteristikų kiekybiniam vertinimui“. Journal of Plant Physiology 161, Nr. 2 (2004): 165–173._

_Henebry, G., A. Viña ir A. Gitelson. „Platus dinaminis augmenijos indeksas ir jo potencialus naudingumas spragų analizei.“ Spragų analizės biuletenis 12: 50–56._
