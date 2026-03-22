---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibravimo etalonai

MAPIR siūlo įvairius kalibravimo etalonus, pritaikytus įvairioms taikymo sritims. Žemiau pavaizduotas kompaktiškas modelis T4-R50 susideda iš 4 plokščių, kurių šviesos atspindžio koeficientas buvo išmatuotas 250–2 500 nm diapazone.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 difuziniai etaloniniai taikiniai turi šias atspindžio kreives, [duomenis galima atsisiųsti čia](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 atspindžio koeficientas :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 atspindžio koeficientas :: 400–1000 nm</p></figcaption></figure>T4P difuziniai etaloniniai taikiniai turi šias atspindžio kreives, [duomenis galima atsisiųsti čia](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR T4P atspindžio koeficientas :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR T4P atspindžio koeficientas :: 400–1000 nm</p></figcaption></figure>Žiūrėdami atspindžio grafiką, matote, kad vertės yra bangos ilgis (x ašis) prieš atspindžio procentą (y ašis). Kai užfiksuojame kalibravimo taikinio vaizdą, tada sukuriame ryšį tarp pikselio vertės ir atspindžio procento, tame spektre, kuriam jautrūs kiekvienos kameros jutiklio juostos.

Tai reiškia, kad su kiekvienu vaizdu, kurį užfiksuojate mūsų kameromis, galite naudoti mūsų atspindžio taškų nuotrauką, pvz., [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) arba [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125), kad kalibruotumėte vaizdus pagal atspindį. Kalibravus kiekvienas vaizdo taškas atitinka atspindžio procentą.

Jei kalibruotus vaizdus išsaugosite Chloros kaip įprastą JPG arba TIFF, atspindžio procentas bus apskaičiuojamas padalijant pikselio vertę iš vaizdo formato bitų gylio. Taigi, JPG atveju dalykite iš 255, o TIFF atveju – iš 65 535. Taip pat galite pasirinkti PERCENT formato išvestį Chloros, ir tada kiekvieno pikselio reikšmė bus nuo 0,0 iki 1,0 (0 % iki 100 % atspindžio). Tik turėkite omenyje, kad kai kurios vaizdo programos nepriima procentinių (plaukiojančiojo kablelio) vaizdų, be to, jie užima daug vietos saugykloje.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
