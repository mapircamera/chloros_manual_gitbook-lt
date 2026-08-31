# Atspindžio matavimo darbo eigos

DAQ šviesos jutiklis paverčia radiometrinius vaizdus atspindžio duomenimis. Yra dvi skirtingos darbo eigos:

1. **Vieno jutiklio** — vienas DAQ matuoja žemyn nukreiptą spinduliavimo intensyvumą, o kamera fiksuoja vaizdą; Chloros padalina kameros spinduliavimo intensyvumą iš to etaloninio dydžio.
2. **Dviejų jutiklių** — du DAQ jutikliai, vienas stebintis dangų, o kitas — objektą, generuoja spektrinės atspindžio kreivę realiuoju laiku be kameros dalyvavimo.

## Vienas jutiklis + kamera (žemyn nukreiptas etalonas)

DAQ veikia kaip žemyn nukreiptos šviesos jutiklis (DLS): kamera matuoja į viršų nukreiptą spinduliavimą **L**(W/m²/sr/nm), DAQ matuoja žemyn nukreiptą spinduliavimą**E** (W/m²/nm), o Chloros apskaičiuoja atspindžio koeficientą kiekvienam juostos diapazonui pagal formulę:

> ρ = π · L / E

DAQ rodmenys visada **suderinti su ekspozicijos laiko žyma** — būtent todėl DAQ ir kameros naudoja bendrą PTP sinchronizuotą laikrodį (žr. [DAQ-E tinklas ir laiko sinchronizavimas](ethernet-ptp.md)). Prie lauko darbo pritaikykite „Sunshine“ kosinuso dangtelį ir jį teisingai deklaruokite; dangtelio deklaracija tiesiogiai keičia E vertę (žr. [Dangtelių profiliai ir kalibruotas diapazonas](caps-and-range.md)). Atliekant kiekybinius matavimus, nepamirškite prietaiso charakteristikos: kiekybinis spinduliavimo intensyvumas apskaičiuojamas remiantis ne mažiau kaip 15 sekundžių matavimų vidurkiu.

### Tiesioginis vaizdo fiksavimas

„Cameras“ skirtuke susiekite DAQ su kamera: kiekvienos kameros nustatymų skydelyje yra išskleidžiamasis meniu **„Light Sensor“**, kuriame išvardyti visi prijungti DAQ (DAQ-U/M/E) iš „Light Sensors“ skirtuko; sinchronizuoto masyvo atveju visam masyvui pasirinktas „Light Sensor“ nustatymas taikomas kiekvienam nariui (atskiros kameros vis tiek gali perrašyti šį nustatymą). Susiejus, jutiklio spektrai perduodami į kameros DLS lizdą, o eksportuojami atspindžio duomenys dalijami iš atitinkančio rodmens.

<!-- SCREENSHOT-NEEDED: Cameras tab per-camera settings panel showing the "Light Sensor" dropdown open, with a connected DAQ sensor listed and selected. -->

Dvi savybės, kurias verta žinoti:

* **Nėra susieto DAQ → atspindžio duomenys atmetami, o ne suklastojami.** Chloros atmeta atspindžio duomenis ir užregistruoja praleidimo priežastį, o ne tyliai grąžina prastesnius duomenis.
* **Naudotas rodmuo išsaugomas.** Kiekvienam atspindžio kadrui faktiškai pritaikytas DAQ rodmuo įrašomas kaip „`.daq`“ priedas šalia vaizdo, todėl įrašą vėliau galima apdoroti iš naujo ([Įrašymas ir .daq formatas](recording.md)).

### Įrašytų vaizdų apdorojimas

Skrydžio pabaigos apdorojimui sesijos metu įrašykite „`.daq`“ ir išsaugokite jį kartu su vaizdais — apdorojimo grandinė automatiškai nustato laiko žymos atitikmenį turinčią žemyn nukreiptą spinduliuotę, pirmą kartą naudojant iš MAPIR debesies atsisiųsdamas trūkstamus gamyklinius kalibravimo duomenis. GUI įrašai automatiškai pridedami prie atidaryto projekto, kai jie baigiasi.

Atspindžio etalonas pasirenkamas apdorojimo metu — „`--reflectance-source`“ arba „`chloros-cli process`“, arba atspindžio šaltinio nustatymas GUI projekto nustatymuose:

| Vertė | Elgsena |
| --- | --- |
| `auto` (numatyta) | Absoliučiu etalonu laikomas kokybės užtikrinimo reikalavimus atitinkantis kalibravimo taikinys kadre; atsarginis variantas – DAQ žemyn nukreiptas spinduliavimas (ρ = π·L/E) |
| `daq` | DAQ yra autoritetingas |
| `target` | Griežtas kadre esantis taikinys; DAQ nepakeičia |

Žr. [Kalibravimo taikiniai](../calibration-targets.md), kur pateikiami taikinio darbo srautai, ir [skyrių „LATTICE“](../lattice/README.md) bei [CLI nuoroda](../reference/cli-reference.md), kur rasite visą apdorojimo procesą. Skaitant eksportuotus atspindžio pikselius, naudokite nurodytą mastelį (LATTICE: 32768 = ρ 1,0, XMP `Chloros:PixelScale`; Survey3: 65535) — žr. [Išvesties vaizdo formatai](../output-image-formats.md).

### Juostos, esančios už DAQ kalibruoto diapazono ribų

DAQ radiometriškai kalibruotas diapazonas yra ~374–974 nm. Chloros atmeta DAQ pagrįstą atspindžio koeficientą bet kuriam kameros diapazonui, kurio spektrinė svarba tame intervale sudaro mažiau nei pusę, nurodydamas praleidimo priežastį `dls-uncalibrated-band-<nm>`. Iš visų parduodamų modelių tai turi įtakos tik F988: F988 atspindžio koeficientas kalibruojamas naudojant atspindžio plokštelę, esančią vaizdo kadre: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros naudoja jūsų naujausią plokštelės užfiksuotą duomenų rinkinį ir išlaiko jį tarp plokštelės matavimų. Jei „F988“ kamera veikia tik DAQ režimu, kodas Chloros atmeta DAQ pagrįstą atspindžio koeficientą tam juostos diapazonui, nurodydamas praleidimo priežastį `dls-uncalibrated-band-988` — palaikomas sprendimas yra plokštelės naudojimo darbo eiga.

## Dvigubas jutiklis (aplinkos + objekto)

Du DAQ jutikliai – bet kokia pora, nepriklausomai nuo transporto priemonės – suteikia atspindžio spektrą realiuoju laiku be kameros: vienas jutiklis nukreiptas į dangų (**Aplinkos šviesos šaltinis**), kitas – į objektą (**Objekto skaitytuvas**), o Chloros apskaičiuoja pagal bangos ilgį:

> R(λ) = objektas(λ) / aplinka(λ)

(lygus nuliui, kai aplinkos šviesos intensyvumas ≤ 0).

### Vartotojo sąsajoje

Kai abu jutikliai prijungti skirtuke „Šviesos jutikliai“, atidarykite jutiklio pridėjimo langą (mygtukas „+“ ant diagramos plytelių tinklelinio vaizdo režime) ir pasirinkite **„Sujungti aplinkos šviesą + objektą“**. Išskleidžiamuosiuose meniu „Aplinkos šviesos šaltinis“ ir „Objekto skaitytuvas“ pasirinkite du jutiklius ir spustelėkite „Sukurti“. Grupė pasirodo kaip atskiras diagramos langas ir kaip šoninės juostos eilutė su žaliu ženklu**REF**.

<!-- SCREENSHOT-NEEDED: The add-sensor overlay's "Combine Ambient + Object" panel with two connected DAQ sensors selected in the Ambient Light Source and Object Scanner dropdowns, Create button enabled. -->

<!-- SCREENSHOT-NEEDED: A live Apparent Reflectance chart from an Ambient+Object DAQ pair in list view, with the vegetation-index table visible below the chart (NDVI etc. showing live values). -->

Po atspindžio diagramos (sąrašo peržiūra) esanti tiesioginė **vegetacijos indekso lentelė** apskaičiuoja indeksus pagal kreivę, naudodama juostų centrus: mėlyna 450 / žalia 550 / raudona 670 / NIR 800 nm. Santykiais pagrįsti indeksai, kurie panaikina absoliučią skalę (NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR), visada rodomi; indeksai, kuriems reikalingas absoliutus atspindžio koeficientas (EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI) rodomi tik tada, kai abu jutikliai yra kalibruoti pagal galią.

### Atrodomasis ir santykinis atspindys — ženklinimo taisyklė

Chloros žymi dviejų jutiklių išvestį pagal tai, ką jutiklių pora iš tikrųjų gali nurodyti:

| Jutiklių pora | Žymėjimas |
| --- | --- |
| Abu jutikliai kalibruoti — įkeltas gamyklinis kalibravimo rinkinys | **Aparatinis atspindžio koeficientas** |
| Bet kuris jutiklis nekalibruotas | **Santykinis atspindžio koeficientas** |

Visi trys modeliai yra radiometriniai: įkėlus jutiklio gamyklinį kalibravimo rinkinį, jo spektrai yra absoliučios vertės W/m²/nm, taigi kalibruotų jutiklių poros santykis atitinka absoliutų akivaizdųjį atspindžio koeficientą — transportas jo nenustato. Jutiklis, vis dar perduodantis neapdorotus skaičiavimo duomenis (nėra prieinamo kalibravimo rinkinio), sumažina rezultato tikslumą iki santykinės kreivės (spektro forma išlieka teisinga). Abu jutikliai turėtų turėti teisingai nurodytus ribinius dydžius ([Ribinių dydžių profiliai ir kalibruotas diapazonas](caps-and-range.md)).

### Iš Python

Sujungtoje „SDK“ paviršiaus srityje nėra specialaus dviejų jutiklių iškvietimo: atidarykite dvi sesijas su „`chloros_sdk.connect_daq_sensor()`“ ir patys palyginkite jų „`latest()`“ spektrus, taikydami tą pačią ženklinimo konvenciją. (Dviejų jutiklių įrašymo įrankis taip pat yra MAPIR vidiniame tiesioginiame aparatinės įrangos paviršiuje, nurodytame [CLI nuorodoje](../reference/cli-reference.md) išsamumo dėlei — ji nėra įtraukta į pristatytą CLI; aukščiau aprašytas GUI darbo srautas yra palaikomas tiesioginis būdas.)
