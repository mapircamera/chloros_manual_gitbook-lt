# Atspindžio darbo eigos

DAQ šviesos jutiklis paverčia radiometrinius vaizdus atspindžiu. Yra dvi skirtingos darbo eigos:

1. **Vieno jutiklio** — vienas DAQ matuoja žemyn nukreiptą spinduliavimo intensyvumą, o kamera fiksuoja vaizdą; Chloros padalina kameros spinduliavimo intensyvumą iš to etaloninio dydžio.
2. **Dviejų jutiklių** — du DAQ jutikliai, vienas stebintis dangų, o kitas — objektą, sukuria spektrinę atspindžio kreivę realiuoju laiku be kameros dalyvavimo.

## Vienas jutiklis + kamera (žemyn nukreiptas etalonas)

DAQ veikia kaip žemyn nukreiptos šviesos jutiklis (DLS): kamera matuoja į viršų nukreiptą spinduliavimą **L**(W/m²/sr/nm), DAQ matuoja žemyn nukreiptą spinduliavimą**E** (W/m²/nm), o Chloros apskaičiuoja atspindžio koeficientą kiekvienam juostos diapazonui pagal formulę:

> ρ = π · L / E

DAQ rodmenys visada **suderinti su ekspozicijos laiko žyma** — būtent todėl DAQ ir kameros naudoja bendrą PTP sinchronizuotą laikrodį (žr. [DAQ-E tinklas ir laiko sinchronizavimas](ethernet-ptp.md)). Lauko darbams pritaikykite „Sunshine“ kosinuso dangtelį ir jį teisingai deklaruokite; dangtelio deklaracija tiesiogiai keičia E vertę (žr. [Dangtelių profiliai ir kalibruotas diapazonas](caps-and-range.md)). Atliekant kiekybinius matavimus, nepamirškite prietaiso charakteristikos: kiekybinis spinduliavimo intensyvumas apskaičiuojamas remiantis ne mažiau kaip 15 sekundžių matavimų vidurkiu.

### Tiesioginis vaizdo fiksavimas

Susiekite DAQ su kamera skirtuke „Cameras“: kiekvienos kameros nustatymų skydelyje yra išskleidžiamasis meniu **„Light Sensor“**, kuriame išvardyti visi prijungti DAQ (DAQ-U/M/E) iš skirtuko „Light Sensors“; sinchronizuoto masyvo atveju visam masyvui pasirinktas „Light Sensor“ nustatymas perduodamas kiekvienam nariui (atskiros kameros vis tiek gali pakeisti šį nustatymą). Susiejus, jutiklio spektrai perduodami į kameros DLS lizdą, o atspindžio eksportai dalijami iš atitinkamo rodmens. Masyve narys, turintis savo jutiklį, naudoja savo rodmenis — savo juostoms tiesioginiame kompozite, eksportuojamiems duomenims ir automatinei ekspozicijai — o kiti nariai naudoja masyvo rodmenis.

<!-- SCREENSHOT-NEEDED: Cameras tab per-camera settings panel showing the "Light Sensor" dropdown open, with a connected DAQ sensor listed and selected. -->


Dvi savybės, kurias verta žinoti:

* **Nėra susieto DAQ → atspindžio duomenys atmetami, o ne suklastojami.** Chloros atmeta atspindžio rezultatą ir užregistruoja praleidimo priežastį, o ne tyliai grąžina prastesnį rezultatą.
* **Naudojamas rodmuo išsaugomas.** Kiekvienam atspindžio kadrui faktiškai pritaikytas DAQ rodmuo įrašomas kaip „`.daq`“ failas įrašų „`daq/`“ aplanke, todėl įrašą vėliau galima apdoroti iš naujo ([Įrašymas ir .daq formatas](recording.md)). Šalia esantis `.daq.cameras.json` failas nurodo kameras, kurias jis aprašo, o pakartotinis apdorojimas suderina jį tik su tų kamerų vaizdais.

### Įrašytų vaizdų apdorojimas

Skrydžio pabaigoje atliekant apdorojimą, sesijos metu įrašykite `.daq` failą ir išsaugokite jį kartu su vaizdais — apdorojimo grandinė automatiškai nustato pagal laiko žymes suderintus žemyn nukreiptus vaizdus, o pirmą kartą naudojant iš „MAPIR “ debesies atsisiunčia bet kokius trūkstamus gamyklinius kalibravimus. GUI įrašai automatiškai pridedami prie atidaryto projekto, kai jie baigiasi.

Atspindžio etalonas pasirenkamas apdorojimo metu — „`--reflectance-source`“ arba „`chloros-cli process`“, arba atspindžio šaltinio nustatymas GUI projekto nustatymuose:

| Vertė | Elgsena |
| --- | --- |
| `auto` (numatyta) | Absoliučiu etalonu laikomas kokybės užtikrinimo (QA) reikalavimus atitinkantis kalibravimo taikinys kadre; DAQ spinduliavimas žemyn (ρ = π·L/E) naudojamas kaip atsarginis variantas. Tikslas, kuris atitinka kokybės užtikrinimo reikalavimus su žymekliu (nukritęs skydelis arba neveikianti juosta), nėra automatiškai teikiamas pirmenybė: tikslo, tikslo + DAQ ir tik DAQ grandinės yra bandomos pagal jų išmatuotą balą ant atidėtų skydelių. |
| `daq` | DAQ yra lemiamas |
| `target` | Griežtas rėmo viduje esantis taikinys; DAQ nepakeičiamas |

Tikslo darbo eigą rasite skyriuje [Kalibravimo tikslai](../calibration-targets.md), o [skyrių „LATTICE“](../lattice/README.md) bei [CLI nuorodą](../reference/cli-reference.md), kur rasite visą apdorojimo procesą. Skaitant eksportuotus atspindžio pikselius, naudokite pažymėtą skalę („LATTICE“: 32768 = ρ 1,0, „XMP“ `Chloros:PixelScale`; Survey3: 65535) — žr. [Išvesties vaizdo formatai](../output-image-formats.md).

### Juostos, esančios už DAQ kalibruoto diapazono ribų

DAQ radiometriškai kalibruotas diapazonas yra ~374–974 nm. Chloros atmeta DAQ pagrįstą atspindžio duomenis bet kuriam kameros juostos diapazonui, kurio mažiau nei pusė spektrinio svorio patenka į tą intervalą, nurodydama praleidimo priežastį `dls-uncalibrated-band-<nm>`. Iš parduodamų modelių tai turi įtakos tik F988: F988 atspindžio koeficientas kalibruojamas naudojant atspindžio plokštelę, esančią vaizdo kadre: juosta yra už DAQ šviesos jutiklio kalibruoto diapazono ribų, todėl Chloros taiko jūsų naujausią plokštelės užfiksavimą ir išlaiko jį tarp plokštelės matavimų. Jei „F988“ kamera veikia tik DAQ režimu, Chloros atmeta DAQ pagrįstą atspindžio koeficientą tam diapazonui, nurodydamas praleidimo priežastį `dls-uncalibrated-band-988` — palaikomas tik plokštelės naudojimo būdas.

## Dvigubas jutiklis (aplinkos + objekto)

Du DAQ jutikliai – bet kuri pora, nepriklausomai nuo perdavimo būdo – suteikia atspindžio spektrą realiuoju laiku be kameros: vienas jutiklis nukreiptas į dangų (**Aplinkos šviesos šaltinis**), kitas – į objektą (**Objekto skaitytuvas**), o Chloros apskaičiuoja pagal bangos ilgį:

> R(λ) = objektas(λ) / aplinka(λ)

(lygus nuliui, kai aplinkos šviesos intensyvumas ≤ 0).

### Vartotojo sąsajoje

Kai abu jutikliai prijungti skirtuke „Šviesos jutikliai“, atidarykite jutiklio pridėjimo langą (mygtukas „+“ ant diagramos plytelių tinklelinio vaizdo režime) ir pasirinkite **Sujungti aplinkos šviesą + objektą**. Išskleidžiamuosiuose meniu „Aplinkos šviesos šaltinis“ ir „Objekto skaitytuvas“ pasirinkite du jutiklius ir spustelėkite „Sukurti“. Grupė pasirodo kaip atskiras diagramos langas ir kaip šoninės juostos eilutė su žaliu ženklu**REF**.

<!-- SCREENSHOT-NEEDED: The add-sensor overlay's "Combine Ambient + Object" panel with two connected DAQ sensors selected in the Ambient Light Source and Object Scanner dropdowns, Create button enabled. -->

<!-- SCREENSHOT-NEEDED: A live Apparent Reflectance chart from an Ambient+Object DAQ pair in list view, with the vegetation-index table visible below the chart (NDVI etc. showing live values). -->


Po atspindžio diagramos (sąrašo peržiūra) esanti tiesioginė **augmenijos indeksų lentelė** apskaičiuoja indeksus iš kreivės, naudodama juostų centrus: mėlyną 450 / žalią 550 / raudoną 670 / NIR 800 nm. Visada rodomi santykiais pagrįsti indeksai, kurie panaikina absoliučią skalę (NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR), visada rodomi; indeksai, kuriems reikalingas absoliutus atspindžio koeficientas (EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI), rodomi tik tada, kai abu jutikliai yra kalibruoti pagal galią.

### „Aparentinis“ ir „santykinis“ — ženklinimo taisyklė

Chloros dviejų jutiklių išvestį žymi pagal tai, ką jutiklių pora iš tikrųjų gali pateikti:

| Jutiklių pora | Ženklas |
| --- | --- |
| Abu jutikliai kalibruoti — įkeltas gamyklinis kalibravimo rinkinys | **Aparentinis atspindžio koeficientas** |
| Jei bent vienas jutiklis nekalibruotas | **Santykinis atspindžio koeficientas** |

Visi trys modeliai yra radiometriniai: įkėlus jutiklio gamyklinį kalibravimo paketą, jo spektrai yra absoliučios vertės W/m²/nm, taigi kalibruotų jutiklių pora atitinka absoliutų akivaizdųjį atspindžio koeficientą — transportas to nenustato. Jutiklis, vis dar perduodantis neapdorotus skaičiavimo duomenis (nėra prieinamo paketo), sumažina rezultato tikslumą iki santykinės kreivės (spektro forma išlieka teisinga). Abu jutikliai turėtų turėti teisingai deklaruotus ribinius dydžius ([Ribinių dydžių profiliai ir kalibruotas diapazonas](caps-and-range.md)).

### Naudojant Python


Bendroje SDK sąsajoje nėra specialaus dviejų jutiklių iškvietimo: atidarykite dvi sesijas su „`chloros_sdk.connect_daq_sensor()`“ ir patys palyginkite jų „`latest()`“ spektrus, taikydami tą pačią ženklinimo konvenciją. (Dviejų jutiklių įrašymo įrankis taip pat egzistuoja „MAPIR “ vidinėje tiesioginės aparatinės įrangos sąsajoje; išsamumo dėlei jis nurodytas [CLI nuorodoje](../reference/cli-reference.md) — jis nėra įtrauktas į pateiktą CLI; aukščiau aprašytas GUI darbo srautas yra palaikomas veikimo būdas.)
