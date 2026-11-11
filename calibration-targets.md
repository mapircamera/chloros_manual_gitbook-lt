---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibravimo taikiniai

MAPIR siūlo įvairius kalibravimo taikinius, pritaikytus įvairioms taikymo sritims. Žemiau pavaizduotas kompaktiškas „T4-R50“ modelis susideda iš 4 plokščių, kurių šviesos atspindžio koeficientas buvo išmatuotas 250–2 500 nm diapazone.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 difuziniai etalonai turi tokias atspindžio kreives, [duomenis galite atsisiųsti čia](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 atspindžio koeficientas :: 250–2 500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 atspindžio koeficientas :: 400–1 000 nm</p></figcaption></figure>T4P difuziniai etaloniniai taikiniai turi tokias atspindžio kreives, [duomenis galima atsisiųsti čia](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR T4P atspindžio koeficientas :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR T4P atspindžio koeficientas :: 400–1000 nm</p></figcaption></figure>Žiūrėdami atspindžio grafiko, matote, kad vertės yra pateiktos kaip bangos ilgis (x ašis) prieš atspindžio procentą (y ašis). Kai užfiksuojame kalibravimo taikinio vaizdą, tada nustatome ryšį tarp pikselio vertės ir atspindžio procento, tame spektre, kuriam jautrūs kiekvienos kameros jutiklio juostos.

Tai reiškia, kad su kiekvienu vaizdu, kurį užfiksuojate mūsų kameromis, galite naudoti mūsų atspindžio taikinių nuotrauką, pavyzdžiui, [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) arba [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125), kad kalibruotumėte vaizdus pagal atspindžio koeficientą. Kalibravus kiekvienas vaizdo pikselis atitinka atspindžio koeficiento procentinę vertę.

Kalbant apie **Survey3** išvestį: jei kalibruotus vaizdus išsaugote Chloros

programoje kaip įprastus JPG arba TIFF

failus, atspindžio procentas apskaičiuojamas pikselio vertę padalijus iš vaizdo formato bitų gylio. Taigi, JPG atveju dalykite iš 255, o TIFF

atveju – iš 65 535. Taip pat galite pasirinkti Chloros

išvesties formatą „PERCENT“, tuomet kiekvieno pikselio reikšmė bus nuo 0,0 iki 1,0 (nuo 0 % iki 100 % atspindžio). Tiesiog turėkite omenyje, kad kai kurios vaizdo apdorojimo programos nepriima procentinių (plaukiojančio kablelio) vaizdų, be to, jie užima daug vietos saugykloje.

{% hint style="info" %}
**„LATTICE“ atspindžio koeficientas naudoja kitokį pikselių mastelį.** „LATTICE“ atspindžio koeficientas saugomas taip, kad DN 32768 = 100 % atspindžio koeficientas (o ne 65535), ir kiekviename faile yra XMP žymė „`Chloros:PixelScale`“, nurodanti jo mastelį. Perskaitykite žymą ir padalinkite iš jos, o ne laikykite, kad mastelis yra pastovus — žr. [Išvesties vaizdo formatai](output-image-formats.md).
{% endhint %}

## Kalibravimo taikiniai su „LATTICE“ kameromis

Naudojant „LATTICE“ kameras, atspindžio kalibravimo taikinys yra **neprivalomas**: Chloros

vietoj to gali susieti atspindį su žemyn nukreiptu spinduliavimo intensyvumu, išmatuotu DAQ šviesos jutikliu (ρ = π·L/E). Šis atskaitos taškas pasirenkamas projekto atspindžio šaltinio nustatymu, kuris nustatomas naudojant `chloros-cli process --reflectance-source` (grafinėje vartotojo sąsajoje nėra šio parametro valdymo ir naudojama projekte išsaugota reikšmė, pagal numatytuosius nustatymus – `auto` — žr. [Projekto nustatymus](project-settings/project-settings.md)):

| Reikšmė | Elgsena |
| --- | --- |
| `auto` *(numatyta)* | Kokybės užtikrinimo (QA) reikalavimus atitinkantis kadre esantis taikinys yra **absoliutus etalonas**; jei taikinio nėra arba QA nepavyksta, Chloros

grįžta prie DAQ žemyn nukreipto srauto padalijimo. Tikslas, kuris išlaiko kokybės patikrinimą su žymekliu (nukrito skydelis arba juosta neveikia), nėra automatiškai teikiamas pirmenybė: tikslo, tikslo+DAQ ir tik DAQ grandinės bandomos pagal jų išmatuotą balą ant atidėtų skydelių. |
| `target` | Tik tikslas — be DAQ pakeitimo. |
| `daq` | DAQ yra lemiamas — žemyn nukreiptas matavimas visada yra atskaitos taškas. |

Papildomi „LATTICE“ taikinio elgesio variantai:

* **Taikinio geometrijos** — palaikomi „ArUco“ žymėti skydai, fiksuoto ROI skydai ir juostiniai taikiniai; geometrija nustatoma pagal projekto taikinio konfigūraciją.
* **Kiekvieno vieneto išmatuoti taikinio duomenys** — `--target-reflectance-dir DIR` nurodo katalogą, kuriame saugomi kiekvieno vieneto išmatuoti taikinio atspindžio skenai (`<serial>.csv`, ieškoma pagal taikinio vieneto serijos numerį arba QR kodą). Jei taikinys neprasiskverbiamas, Chloros

grįžta prie nominalių T3/T4P spektrų.
* **Laikinis įtvirtinimas** — aptiktas taikinys kalibruoja aplink jį esančius kadrus ir išlaikomas tarp taikinio pastebėjimų.

Išsami vėliavėlių semantika ir pavyzdžiai pateikiami [CLI

žinyne](reference/cli-reference.md) (žr. „Eksporto perjungikliai pagal produktą“).

### F988

„F988 atspindžio koeficientas kalibruojamas naudojant vaizdo kadre esantį atspindžio plokštelę: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros

taiko jūsų naujausią plokštelės užfiksuotą duomenų rinkinį ir išlaiko jį tarp plokštelės stebėjimų.“

Jei F988 vykdomas naudojant tik DAQ kalibravimą, Chloros

atmeta DAQ pagrįstą atspindžio koeficientą tam juostos diapazonui ir nurodo priežastį (praleidimo priežastis `dls-uncalibrated-band-988`); palaikomas darbas su plokštele.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
