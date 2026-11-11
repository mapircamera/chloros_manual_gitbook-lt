# Mono kameros ir augmenijos indeksai

## Viena kamera = viena juosta

**M3M**kamera yra Bayer**M3C** modelio monochrominė versija: monochrominis IMX265 jutiklis, už kurio yra vienas siaurajuostis interferencinis filtras. Modelio pavadinime nurodytas juostos pavadinimas — `M3M-<lens>-F<wavelength>`, pvz., `M3M-L87-F685` (Chloros programoje rodomas kaip `LATT-M3M-L87-F685`). Jutiklis pateikia **vieną pilkosios skalės juostą** be „Bayer“ mozaikos: nėra ko demozikuoti, nėra kanalų tarpusavio persikreipimo, kurį reikėtų atskirti, ir nereikia nustatyti baltos spalvos balanso.

Pasekmės, kurias verta žinoti prieš planuojant monochromatinę sistemą:

* **Spinduliavimas ir atspindys yra visiškai apibrėžti kiekvienam juostos kanalui.**Tai yra radiometriniai žemėlapiai pagal juostas, taigi viena „M3M“ kamera generuoja kalibruotą „float32“ spinduliavimą (W/m²/sr/nm) ir „uint16“ atspindį (`32768` = ρ 1,0) lygiai taip pat, kaip tai daro „M3C“ juosta. Mono kadruose yra**identiška** jutiklio atsako matrica — 3×3 atskyrimo nereikia ir jis netaikomas.
* **Viena mono kamera negali apskaičiuoti augmenijos indekso.** NDVI, NDRE ir kitiems indeksams reikia mažiausiai dviejų juostų. Norint apskaičiuoti indeksus naudojant mono įrangą, reikia sujungti kelias M3M kameras — žr. toliau.
* M3M kameros perduoda srautą **Mono12** (12 bitų: 2 baitai/pikselis perdavimo linijoje arba 1,5 baitai/pikselis, kai matrica naudoja [suspakuotą 12 bitų perdavimą](arrays.md#packed-12-bit-transport)), o tai svarbu [matricos pralaidumo planavimui](arrays.md#bandwidth-how-many-cameras-fit).

## Ką Chloros praleidžia mono režimu — ir kaip apie tai praneša

Spalvų apdorojimo grandinės etapai tiesiog netaikomi vienos juostos jutikliui. Chloros **juos praleidžia, pateikdamas vienos eilutės pranešimą**, o ne rodo klaidą, ir vis tiek juos vykdo įprastai bet kuriai M3C (Bayer) kamerai toje pačioje sesijoje:

| Etapas | Mono (M3M) elgsena | M3C elgsena |
| --- | --- | --- |
| Demosaikas / „debayer“ | Praleista — „`debayered`“ eksporto lygis yra 1 kanalo pilkosios skalės vaizdas. | 3 kanalų demosaikas. |
| Baltos spalvos balansas (`lattice white-balance`) | Praleista su vienos eilutės pranešimu. | Vykdoma įprastai. |
| Spalvų profilis (`lattice color-profile`) | Praleista su vienos eilutės pranešimu. | Vykdoma įprastai. |
| Saturacija/kontrastas (`lattice color`) | Praleista su vienos eilutės pranešimu. | Veikia normaliai. |
| Spektrinio persipynimo atskyrimas | Tapatus (be 3×3 matricos). | Taikoma 3×3 matrica kiekvienai kamerai. |
| Spinduliavimas / atspindys | **Veikia** — kiekvienam juostos kanalui, visiškai kalibruota. | Veikia kiekvienam juostos kanalui. |

GUI taiko tą patį filtravimą: vienos kameros atveju nustatymų lange, skirtame kiekvienai kamerai, paslėptos tik RGB eilutės (baltos spalvos balansas, gama, spalvų profilis, sodrumas, kontrastas, kanalų atskyrimas), o tiesioginė histograma fiksuota viename **MONO** pėdsake. Visą sluoksnių sujungimo procesą apibrėžia modelio eilutėje esantis žymuo „`M3M`“, kuris GUI / SDK-e rodomas kaip „`is_mono`“.

## Indeksams reikia ≥ 2 juostų: suderinimas → sujungimas → indeksavimas

Mono indekso darbo eiga visada susideda iš tų pačių trijų žingsnių:

1. **Suderinti** — nukreipkite kelias M3M kameras į skirtingus bangos ilgius (pvz., F650 „Red “ ir F850 „NIR “), sujunkite jas kaip [daugiakamerinį masyvą](arrays.md) ir leiskite Chloros apskaičiuoti kamerų tarpusavio registracijos deformaciją.
2. **Sukrauti** — suderinti kadrai tampa vienu daugiajuostiniu vaizdu (kiekviena kamera prisideda viena pavadinta juosta).
3. **Indeksas** — įvertinkite indekso formulę visose steko juostose, pasirinktinai atvaizduodami ją per LUT.

GUI sąsajoje visa ši grandinė yra **„Combined Cameras“**masyvo rodymo režimas: tiesioginis kompozicinis vaizdas jau yra suderintas, o masyvo indekso skaičiuoklė (žemiau) apibrėžia formulę, pagal kurią jis atvaizduojamas. Užfiksuoti eksportai gali būti iškraipyti taip, kad atitiktų tą patį suderinimą, naudojant užfiksavimo parinktį**„Aligned“**.

## Indekso skaičiuoklė

Indekso skaičiuoklė sudaro indekso išraišką, naudojamą tiesioginiame vaizde ir kiekvienos kameros indekso eksportuose. Tai yra viena bendra sąsaja, atidaroma iš dviejų vietų skirtuke „Cameras“ šoninėje juostoje:

* **Kiekvienai kamerai**— Tiesioginis peržiūros langas →**Indeksas** (tik RGN/OCN/NGB „Bayer“ kameros; atskira monochromatinė kamera neturi indekso valdymo, nes vienas juostos kanalas negali sukurti indekso).
* **Kiekvienam masyvui**— masyvo nustatymai → Tiesioginis peržiūros langas →**Indeksas**ratukas. Tai yra monochromatinis kelias: juostų sąrašas apima**visas masyvą sudarančias kameras**, taigi monochromatinė pora čia prisideda savo dviem juostomis.

<!-- SCREENSHOT-NEEDED: Index Calculator pane opened for a combined array of two mono cameras (e.g. F650 + F850): band chips row showing the two bands with wavelength labels, the operator buttons, the expression textarea containing "(NIR - Red) / (NIR + Red)", the green "Valid expression" banner, the LUT controls (Apply LUT checked, Level 7-stop, Min 0.2 / Max 1), and the live histogram with p2/p98 percentile lines. -->

Jo valdymo elementai, iš viršaus į apačią:

* **Juostų žetonai** („Juostos — spustelėkite, kad pridėtumėte į išraišką“) — po vieną mygtuką kiekvienai prieinamai juostai, pažymėtą spalvos pavadinimu + bangos ilgiu nm (pasikartojantys spalvų pavadinimai išskiriami, pvz., „Spalva 850“). Spustelėjus įterpiamas juostos žetonas į kursoriaus vietą. Juostos iš kamerų, kurios negali generuoti spinduliavimo pagal juostas (RGB/FRGB), yra išfiltruojamos.
* **Operatoriaus ir funkcijos mygtukai** — `+ - * / ( ) ^ ,` ir `abs() sqrt() log() log10() exp() min() max() pow()`.
* **Išraiškos tekstinis laukas** — laisvai įvedama formulė; vietos laikiklyje rodoma klasikinė NDVI forma `(NIR - Red) / (NIR + Red)`. Virš jo esantis tik skaitymo režimu veikiantis žetonizuotas peržiūros langas atvaizduoja juostų piktogramas, skaičius, o nežinomus žetonus pažymi vėliavėlėmis.
* **Teisingumo juosta**— pilka „Tuščia — indeksas nebus taikomas“; žalia „Teisinga išraiška“; raudona su konkrečia analizės klaida (nežinoma juosta, dviprasmiška juosta, kurią fiksuoja kelios kameros, trūkstama skliaustelė, …); arba gintarinė, kai išraiška yra teisinga, bet**konstanta** (pvz., `X/X` arba NDVI vardiklis, įvestas kaip `−` vietoj `+`) — konstanta visą kadrą paverčia viena spalva.
* Atskiras gintarinis įspėjimas pasirodo, jei pritaikytas išraiškos teisingas, bet **gyvas kadras yra vienalytis** (plokščia arba prisotinta scena) — histogramos susiliejimas aptinkamas už jus.
* **Taikyti LUT**(pagal numatytuosius nustatymus įjungta; išjungta = pilkosios skalės ištempimas),**Lygis**2/3/5/7 stopai (pagal numatytuosius nustatymus 7 stopai) ir**Min. / Maks.**įvestys, esančios abipus gradiento juostos. Min numatyta reikšmė yra**0,2**— ji priartina spalvų rampą prie augmenijai reikšmingo diapazono, o mažesnės reikšmės perduodamos kaip pilkosios skalės; nustatykite Min į −1, kad būtų pasiektas visas indekso diapazonas (mygtukas**Reset** atkuria diapazoną nuo −1 iki +1). Max numatyta reikšmė yra 1.
* **Tikrojo laiko histograma**, rodanti indekso pasiskirstymą — kvadratinės šaknies mastelio juostos, gintarinės p2/p98 procentilių linijos, balta mediana ir už ribų esančių verčių rodmenys („◀ N% < lo“ / „hi < N% ▶“), kurios virš 1 % tampa gintarinės spalvos, nurodydamos, kad reikia išplėsti „Min/Max“ langą.
* **Taikyti** pritaiko išraišką tiesioginiam srautui; LUT koregavimai taikomi tiesiogiai, nespausdami mygtuko „Taikyti“. Atidarius projektą, išraiškos ir LUT nustatymai išsaugomi kartu su projektu ir atkuriami jį vėl atidarius; jei projekto nėra, jie išlieka tik sesijos metu.

<!-- SCREENSHOT-NEEDED: Combined-array live tile rendering NDVI from a mono pair through the default 7-stop LUT, with the array name pill and fps readout visible — the result of applying the expression from the previous screenshot. -->



## CLI kelias

Tas pats „align“ → „stack“ → „index“ grandinė, kurią galima valdyti skriptais nuo pradžios iki pabaigos:

```bash
chloros-cli lattice align-calibrate --serials SN_RED,SN_NIR --save align.json
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`align-calibrate` naudoja prijungtą masyvą, kuriame laikomos jo kameros, arba pats jas atidaro vienam vykdymo ciklui. `index --live` visada pats atidaro kameras ([laikiną masyvą](../reference/cli-reference.md#multi-camera-transient-sessions)) ir neturi `--serials`: jis atidaro visas kameras tinkle. Taigi atleiskite visas kameras, kurias Chloros laiko prieš tai – GUI, `cam-connect`, `array-connect` (`chloros-cli lattice array-disconnect --all`): antrą kartą atidarius užlaikytą kamerą, gali būti sustabdytas jos turėtojo srautas arba sugriūti užkulisinė sistema. Kamera, kuri nepateikia jokio kadro, sukelia vykdymo nesėkmę, ir nei stekas, nei indeksas nėra įrašomi.

`--channel` susieja išankstinio nustatymo simbolius su steko juostų pavadinimais. Dvi taisyklės padės išvengti nesėkmingo vykdymo:

* **Simboliai yra jautrūs didžiosioms ir mažosioms raidėms** ir turi tiksliai atitikti išankstinio nustatymo kanalų pavadinimus — išankstiniuose nustatymuose naudojamos mažosios raidės (NDVI nustatymai yra `red`, `nir`; žr. `--list-presets`). `--channel red=Red_660` veikia; `--channel RED=660` nesėkmingas, rodo klaidą „`channel_map missing entries`“.
* Juostos pusėje turi būti nurodyta juosta iš suderinto rinkinio (`lattice align-info --profile align.json` pateikia jų sąrašą). Neprisijungusiam režimui taip pat tinka nuo 0 prasidedantys juostų indeksai, pvz., `--channel red=0 --channel nir=1`.

„`lattice index`“ taip pat veikia neprisijungus prie interneto su išsaugotu suderintu daugiajuostiniu TIFF failu. Kiekviena forma veikia serveryje, todėl jis turi veikti ir būti prisijungęs prie mokamo „Chloros+“ lygio; santykinis `--input` arba `--output` nustatomas pagal aplanką, iš kurio vykdote komandą:

```bash
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn
```

### Indekso nustatymai

`lattice index --preset` (ir skirtuko „Image“ [Index/LUT sandbox](../image-viewer-gui/index-lut-sandbox.md), kuris naudoja tą patį variklį) pateikia šiuos **22 nustatymus**:

`NDVI, GNDVI, BNDVI, NDRE, ENDVI, SAVI, OSAVI, MSAVI, EVI, EVI2, CVI, MSR, TDVI, LAI, GLI, NGRDI, VARI, TGI, EXG, CIRE, CIGREEN, NDWI`

Paleiskite „`chloros-cli lattice index --list-presets`“, kad peržiūrėtumėte kiekvieno nustatymo formulę ir kanalų simbolius, o „`--list-gradients`“ – norėdami peržiūrėti galimus spalvų gradientus. Pasirinktinėms formulėms naudokite „`--formula EXPR`“ su ta pačia sintakse kaip ir indekso skaičiuoklėje. Atkreipkite dėmesį, kad šis nustatymų sąrašas yra skirtas būtent „LATTICE“ indeksavimo varikliui — importuotų vaizdų apdorojimo išskleidžiamajame meniu „Project Settings“ pateikiamas kitas sąrašas (žr. [Daugiaspektrinių indeksų formulės](../project-settings/multispectral-index-formulas.md)).

Išsamus žymių rinkinys (`--output-format`, `--vmin/--vmax/--percentile`, `--bg-mode`, `--live` suderinimo ir deformavimo reguliatoriai ir kt.) aprašytas [CLI žinyne § Indeksai / Vegetacijos matematika](../reference/cli-reference.md#index--vegetation-maths); SDK atitikmenys pateikiami [SDK žinyne](../reference/sdk-reference.md).

## Indekso produktų fiksavimas iš vienos kameros masyvo

Prijungus masyvą ir pritaikius indekso išraišką, `array-capture` (arba GUI funkcija **„Capture All“**) išsaugo kiekvienos kameros eksporto lygius *ir* indekso atvaizdavimą — `--index`/`--no-index` įjungia šią funkciją per CLI, o numatytasis nustatymas apima visus taikytinus lygius. Vienos kameros indėlis į kiekvieną įrašymo grupę yra jos vienas juostos lygis neapdorotų duomenų / debayeringo (pilkosios skalės) / spinduliavimo / atspindžio lygiuose, taip pat bendras sujungto indekso kompozitas, kai masyvas veikia sujungtame režime. Žr. [Daugiakameriniai masyvai § Fiksavimas](arrays.md#capturing-monitoring-vs-analysis).
