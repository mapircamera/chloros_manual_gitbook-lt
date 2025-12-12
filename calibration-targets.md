---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibravimo tikslai

MAPIR siūlo įvairius kalibravimo tikslus, kad būtų galima padengti įvairias taikymo sritis. Kompaktiškas T4-R50, matomas žemiau, turi 4 plokštes, kurių šviesos atspindys buvo išmatuotas nuo 250 iki 2500 nm.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 difuziniai etaloniniai taškai turi šias atspindžio kreives, [duomenys atsisiųsti čia](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 atspindžio koeficientas :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 atspindžio koeficientas :: 400–1000 nm</p></figcaption></figure>Žiūrėdami atspindžio grafiką, matote, kad vertės yra bangos ilgis (x ašis) ir atspindžio procentas (y ašis). Kai užfiksuojame kalibravimo taikinio vaizdą, tada sukuriame ryšį tarp pikselio vertės ir atspindžio procento spektre, kuriam jautrūs kiekvienos kameros jutiklio juostos.

Tai reiškia, kad kiekvienam vaizdui, kurį užfiksuojate mūsų kameromis, galite naudoti mūsų atspindžio tikslų nuotrauką, pvz., [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) arba [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125), kad kalibruotumėte vaizdus pagal atspindį. Kalibravus kiekvienas vaizdo pikselis yra lygus atspindžio procentui.

Jei kalibruotus vaizdus išvedate Chloros kaip tipišką JPG arba TIFF, atspindžio procentas apskaičiuojamas dalijant pikselio vertę iš vaizdo formato bitų gylio. Taigi, JPG atveju dalijama iš 255, o TIFF atveju – iš 65 535. Taip pat galite pasirinkti PERCENT formato išvestį Chloros, tada kiekvienas pikselis bus nuo 0,0 iki 1,0 proc. (0 % iki 100 % atspindžio). Turėkite omenyje, kad kai kurios vaizdo programos nepriima procentinių (plaukiojančiojo kablelio) vaizdų, be to, jie užima daug vietos atmintyje.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
