# Mono kameros ir augmenijos indeksai

## Viena kamera = viena juosta

**M3M**kamera yra „Bayer“**M3C**modelio monochrominė versija: joje yra monochrominis IMX265 jutiklis, uždengtas vienu siaurajuosčiu interferenciniu filtru. Modelio pavadinime nurodytas juostos pavadinimas — `M3M-<lens>-F<wavelength>`, pvz., `M3M-L87-F685` (rodoma kaip Chloros arba `LATT-M3M-L87-F685`). Jutiklis užtikrina**vieną pilkosios skalės juostą** be „Bayer“ mozaikos: nereikia atlikti demozajavimo, pašalinti kanalų tarpusavio trukdžių ir nustatyti baltos spalvos balanso.

Pasekmės, kurias verta žinoti prieš planuojant monochromatinę sistemą:

* **Spinduliavimas ir atspindys yra visiškai apibrėžti kiekvienam juostos diapazonui.**Tai yra radiometriniai žemėlapiai pagal juostą, taigi viena „M3M“ kamera generuoja kalibruotą „float32“ spinduliavimą (W/m²/sr/nm) ir „uint16“ atspindį (`32768` = ρ 1,0) lygiai taip pat, kaip tai daro „M3C“ juosta. Mono kadruose yra**identiška** jutiklio atsako matrica — 3×3 atskyrimas nereikalingas ir netaikomas.
* **Viena mono kamera negali apskaičiuoti augmenijos indekso.** NDVI, NDRE ir panašios kameros reikalauja mažiausiai dviejų juostų. Norint apskaičiuoti indeksus naudojant mono įrangą, reikia sujungti kelias M3M kameras — žr. toliau.
* M3M kameros perduoda srautą **Mono12** (12 bitų, 2 baitai/pikselis perdavimo linijoje), o tai svarbu [matricos pralaidumo planavimui](arrays.md#bandwidth-the-rules-of-thumb).

## Ką Chloros praleidžia vienos juostos atveju – ir kaip apie tai praneša

Spalvų apdorojimo grandinės etapai tiesiog netaikomi vienos juostos jutikliui. Chloros **juos praleidžia, pateikdamas vienos eilutės pranešimą**, o ne rodo klaidą, ir vis tiek juos normaliai vykdo bet kuriai M3C (Bayer) kamerai toje pačioje sesijoje:

| Etapas | Mono (M3M) elgsena | M3C elgsena |
| --- | --- | --- |
| Demosaikas / debayeris | Praleista — `debayered` eksporto lygis yra 1 kanalo pilkosios skalės vaizdas. | 3 kanalų demosaikas. |
| Baltos spalvos balansas (`lattice white-balance`) | Praleista su vienos eilutės pranešimu. | Vykdoma įprastai. |
| Spalvų profilis (`lattice color-profile`) | Praleista su vienos eilutės pranešimu. | Vykdoma įprastai. |
| Saturacija/kontrastas (`lattice color`) | Praleista su vienos eilutės pranešimu. | Veikia normaliai. |
| Spektrinio persipynimo atskyrimas | Tapatybė (be 3×3 matricos). | Taikoma 3×3 matrica kiekvienai kamerai atskirai. |
| Šviesos srautas / atspindys | **Veikia** — kiekvienai juostai atskirai, visiškai kalibruota. | Veikia kiekvienai juostai atskirai. |

GUI taiko tą patį filtravimą: vienos kameros atveju nustatymų lange, skirtame kiekvienai kamerai, paslėptos eilutės, skirtos tik RGB (baltos spalvos balansas, gama, spalvų profilis, sodrumas, kontrastas, kanalų padalijimas), o tiesioginis histogramos vaizdas yra fiksuotas prie vienos **MONO** kreivės. Visą steką skiriantis veiksnys yra modelio eilutėje esantis `M3M` žymuo, GUI/SDK rodomas kaip `is_mono`.

## Indeksams reikia ≥ 2 juostų: suderinimas → sukrovimas → indeksavimas

Mono indeksavimo darbo eiga visada susideda iš tų pačių trijų žingsnių:

1. **Suderinti** — nukreipti kelias M3M kameras į skirtingus bangos ilgius (pvz., F650 „Red“ ir F850 „NIR“), sujunkite jas į [daugiakamerinį masyvą](arrays.md) ir leiskite Chloros apskaičiuoti kamerų tarpusavio registracijos deformaciją.
2. **Sluoksnis** — suderinti kadrai tampa vienu daugiabandžiu vaizdu (kiekviena kamera prisideda viena pavadinta juosta).
3. **Indeksas** — įvertinkite indekso formulę per sluoksnio juostas, pasirinktinai atvaizduodami ją per LUT.

Grafinėje vartotojo sąsajoje visa ši grandinė atitinka masyvo rodymo režimą **„Combined Cameras“**: tiesioginis kompozicinis vaizdas jau yra suderintas, o masyvo indekso skaičiuoklė (žr. žemiau) apibrėžia formulę, pagal kurią jis atvaizduojamas. Įrašyti eksportai gali būti iškraipyti taip, kad atitiktų tą patį suderinimą, naudojant įrašymo parinktį**„Aligned“**.

## Indekso skaičiuoklė

Indekso skaičiuoklė sukuria indekso išraišką, naudojamą tiesioginiame vaizde ir eksportuojant indeksą pagal kiekvieną kamerą. Tai vienas bendras langas, atidaromas iš dviejų vietų skirtuko „Kameros“ šoninėje juostoje:

* **Kiekvienai kamerai atskirai**— Tiesioginis peržiūros langas →**Indeksas** (tik RGN/OCN/NGB „Bayer“ kameros; viena monochromatinė kamera neturi indekso valdymo, nes vienas dažnių juostos negali sukurti indekso).
* **Kiekvienam masyvui**— masyvo nustatymai → Tiesioginis peržiūros langas →**Indeksas**(ratukas). Tai yra monochromatinis kelias: dažnių juostų sąrašas apima**visas masyvą sudarančias kameras**, taigi monochromatinė pora čia prisideda savo dviem dažnių juostomis.

<!-- SCREENSHOT-NEEDED: Index Calculator pane opened for a combined array of two mono cameras (e.g. F650 + F850): band chips row showing the two bands with wavelength labels, the operator buttons, the expression textarea containing "(NIR - Red) / (NIR + Red)", the green "Valid expression" banner, the LUT controls (Apply LUT checked, Level 7-stop, Min 0.2 / Max 1), and the live histogram with p2/p98 percentile lines. -->

Jos valdymo elementai, iš viršaus į apačią:

* **Juostų žymės** („Juostos — spustelėkite, kad pridėtumėte į išraišką“) — po vieną mygtuką kiekvienai prieinamai juostai, pažymėtą spalvos pavadinimu + bangos ilgiu nm (pasikartojantys spalvų pavadinimai išskiriami, pvz., „Spalva 850“). Spustelėjus įterpiamas juostos žetonas ties žymekliu. Juostos iš kamerų, kurios negali generuoti spinduliavimo pagal juostą (RGB/FRGB), yra išfiltruojamos.
* **Operatorių ir funkcijų mygtukai** — `+ - * / ( ) ^ ,` ir `abs() sqrt() log() log10() exp() min() max() pow()`.
* **Išraiškos tekstinis laukas** — laisvai įvedama formulė; vietos laikiklis rodo klasikinę NDVI formą `(NIR - Red) / (NIR + Red)`. Virš jo esantis tik skaitymo režimu veikiantis žetonizuotas peržiūros langas atvaizduoja juostų fragmentus, skaičius, o nežinomi žetonai pažymėti vėliavėlėmis.
* **Teisingumo juosta**— pilka „Tuščia — indeksas nebus taikomas“; žalia „Teisinga išraiška“; raudona su konkrečia sintaksės klaida (nežinoma juosta, dviprasmiška juosta, užfiksuota keliomis kameromis, trūksta skliaustų, …); arba gintarinė, kai išraiška yra teisinga, bet**pastovi** (pvz., `X/X`, arba NDVI vardiklis, įvestas kaip `−` vietoj `+`) — konstanta visą kadrą paverčia viena spalva.
* Atskiras gintarinis įspėjimas pasirodo, jei pritaikytas išraiška yra teisinga, bet **gyvas kadras yra vienodas** (plokščia arba prisotinta scena) — histogramos susiliejimas aptinkamas už jus.
* **Taikyti LUT**(pagal numatytuosius nustatymus įjungta; išjungta = pilkosios skalės ištempimas),**Lygis**2/3/5/7 stopai (pagal numatytuosius nustatymus 7 stopai) ir**Min. / Maks.**įvestys, esančios abipus gradiento juostos. Min numatyta reikšmė yra**0,2**— ji priartina spalvų gradientą prie augmenijai aktualaus diapazono, o mažesnės reikšmės perduodamos kaip pilkosios skalės; nustatykite Min į −1, kad būtų pasiektas visas indekso diapazonas (mygtukas**Reset** atkuria diapazoną nuo −1 iki +1). Max numatyta reikšmė yra 1.
* **Tikrojo laiko histograma**, rodanti indekso pasiskirstymą — kvadratinės šaknies masteliu nustatytos juostos, gintarinės p2/p98 procentilių linijos, balta mediana ir už diapazono ribų esančių verčių rodmenys („◀ N% &lt; lo“ / „hi &lt; N% ▶“), kurios virš 1 % tampa gintarinės spalvos, nurodydamos, kad reikia išplėsti „Min/Max“ langą.
* **Taikyti**pritaiko išraišką tiesioginiam srautui; LUT koregavimai taikomi tiesiogiai, nespausdami „Taikyti“. Išraiškos sąmoningai yra**tik sesijos** — jos neišsaugomos tarp sesijų.

<!-- SCREENSHOT-NEEDED: Combined-array live tile rendering NDVI from a mono pair through the default 7-stop LUT, with the array name pill and fps readout visible — the result of applying the expression from the previous screenshot. -->

## CLI kelias

Tas pats suderinimo → steko → indekso grandinė, kurią galima programuoti nuo pradžios iki pabaigos:

```bash
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` susieja preseto simbolius su steko juostų pavadinimais. Dvi taisyklės padės išvengti nesėkmingo vykdymo:

* **Simboliai yra jautrūs didžiosioms ir mažosioms raidėms** ir turi tiksliai atitikti išankstinio nustatymo kanalų pavadinimus — išankstiniuose nustatymuose naudojamos mažosios raidės (NDVI yra `red`,`nir`; patikrinkite `--list-presets`). `--channel red=Red_660` veikia; `--channel RED=660` sukelia klaidą „`channel_map missing entries`“.
* Juostos pusėje turi būti nurodyta juosta iš suderinto steko (`lattice align-info --profile align.json` pateikia jų sąrašą). Neprisijungusiam režimui taip pat tinka nuo 0 prasidedantys juostų indeksai, pvz., `--channel red=0 --channel nir=1`.

`lattice index` taip pat veikia visiškai neprisijungus prie interneto, naudojant išsaugotą suderintą daugiajuostinį TIFF:

```bash
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn
```

### Indekso nustatymai

`lattice index --preset` (ir skirtuko „Vaizdas“ funkcija [Indekso/LUT smėlio dėžė](../image-viewer-gui/index-lut-sandbox.md), kuri naudoja tą patį variklį) pateikia šiuos **22 nustatymus**:

`NDVI, GNDVI, BNDVI, NDRE, ENDVI, SAVI, OSAVI, MSAVI, EVI, EVI2, CVI, MSR, TDVI, LAI, GLI, NGRDI, VARI, TGI, EXG, CIRE, CIGREEN, NDWI`

Paleiskite `chloros-cli lattice index --list-presets`, kad peržiūrėtumėte kiekvieno nustatymo formulę ir kanalų simbolius, o `--list-gradients` – norėdami peržiūrėti galimus spalvų gradientus. Naudojant pasirinktines formules, naudokite `--formula EXPR` su ta pačia sintakse kaip ir indekso skaičiuoklėje. Atkreipkite dėmesį, kad šis išankstinių nustatymų sąrašas yra skirtas būtent „LATTICE“ indeksavimo varikliui — importuotų vaizdų apdorojimo išskleidžiamajame meniu „Projekto nustatymai“ pateikiamas kitas sąrašas (žr. [Daugiaspektrinės indeksų formulės](../project-settings/multispectral-index-formulas.md)).

Visas žymių rinkinys (`--output-format`, `--vmin/--vmax/--percentile`, `--bg-mode`, `--live` suderinimo ir deformacijos reguliatoriai, ir kt.) aprašytas [CLI žinyne, skyriuje „Indeksai / Augmenijos matematika“](../reference/cli-reference.md#index--vegetation-maths); SDK ekvivalentai pateikti [SDK nuorodoje](../reference/sdk-reference.md).

## Indekso produktų fiksavimas iš mono masyvo

Prijungus masyvą ir pritaikius indekso išraišką, `array-capture` (arba GUI mygtukas **„Capture All“**) išsaugo kiekvienos kameros eksporto lygius *ir* indekso atvaizdavimą — `--index`/`--no-index` įjungia šią funkciją CLI, o numatytasis nustatymas apima visus taikytinus lygius. Vienos kameros indėlis į kiekvieną įrašymo grupę yra jos vienas juostos lygis neapdorotų duomenų / debayered (pilkosios skalės) / spinduliavimo / atspindžio lygiuose, taip pat bendras sujungto indekso kompozitas, kai masyvas veikia sujungtame režime. Žr. [Daugiakameros masyvai § Įrašymas](arrays.md#capturing-monitoring-vs-analysis).
