# Įrašymo nustatymai ir režimai

Įrašymas skirtuke „Kameros“ valdomas vienu raudonu mygtuku **„Įrašyti viską“**ir vienu langeliu**„Įrašymo nustatymai“**, kuriame nustatoma, kokį rezultatą duos šis mygtukas: kurios kameros dalyvaus, kokius eksporto tipus kiekviena kamera išsaugos ir ar užraktas bus suaktyvintas vieną kartą, nepertraukiamai ar tam tikrais intervalais. Šiame puslapyje aprašomas visas procesas – konfigūracija, pats fotografavimas, kur failai išsaugomi diske ir kaip juos vėliau perdirbti į kalibruotus produktus. Pačios kameros ir masyvo valdymo funkcijos yra [Kameros nustatymai](camera-settings.md).

{% hint style="info" %}
**Fiksavimui būtina atidaryti projektą.** Funkcijos „Fiksuoti viską“ ir nustatymų ratukas „Fiksavimo nustatymai“ yra išjungti, kol projektas nėra atidarytas („Sukurkite arba atidarykite projektą, kad būtų galima išsaugoti fiksavimus“). Kiekvienas fiksavimas išsaugomas projekto aplanke, esančiame `captures/`.
{% endhint %}

## Langas „Įrašymo nustatymai“

Jį atidarykite naudodami **ratuką šalia „Įrašyti viską“**šoninės juostos kamerų sąraše arba mygtuką**„Atidaryti įrašymo nustatymus…“**, esantį bet kurio kameros nustatymų lango apačioje. Antraštėje rašoma „Įrašymo nustatymai“ su mygtuku ← „Atgal“.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->

Jūsų čia pasirinkimai – įtrauktos kameros, žymės pagal tipą ir įrašymo režimas – išsaugomi **kiekvienam projektui** ir atkuriami, kai jį vėl atidarote.

### Įrašymo režimai

Trijų režimų mygtukai skydelio viršuje:

| Režimas | Ką daro | Papildomi nustatymai (numatyti) |
| --- | --- | --- |
| **Vienkartinis** *(numatyta)* | Vienas įrašymas iš visų pasirinktų kamerų. | — |
| **Nuolatinis**| Nepertraukiamas fiksavimas iki sustabdymo sąlygos. | Sustabdyti pagal**fiksavimų skaičių** (numatyta reikšmė 1) *arba* **fiksavimo trukmę** (numatyta reikšmė 10 s; vienetai: sekundės / minutės / valandos / dienos). |
| **Intervalas**(laiko tarpas) | Serijos pagal laikmatį. |**Nuotraukų skaičius / intervalas**(numatyta reikšmė 1) ·**Kas**N vienetų (numatyta reikšmė 5 s) ·**Per** N vienetų (numatyta reikšmė 1 m). |

Nuolatinio arba intervalinio režimo metu mygtukas „Užfiksuoti viską“ veikimo metu tampa mygtuku **„Sustabdyti (N)“**, skaičiuojant užfiksuotus kadrus, kai jie pasirodo.

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->

### Kamerų ir eksporto tipų pasirinkimas

Šio lango pagalbos tekstas viską apibendrina: pasirinkite, kokias kameras ir eksporto tipus naudoja funkcija „Capture All“ — pagal numatytuosius nustatymus viskas įjungta, o pasirinkimai išsaugomi kartu su šiuo projektu.

* Mygtukai **„Pasirinkti visus“ / „Neatrinkti nė vieno“** vienu metu perjungia kiekvienos kameros įtraukimo žymimąjį langelį.
* **Masinio eksporto tipų perjungikliai**(dvi mygtukų eilės):**Visi „Raw“ / Visi „Debayered“ / Visi „Preview“ / Visi „Radiance“ / Visi „Reflectance“ / Visi „Index“**. Kiekvienas mygtukas turi tris būsenas: žalia ✓ = įjungta visose kamerose, kurios tai palaiko; gintarinė – = įjungta kai kuriose; pilka = neišjungta. Perjungimo mygtukas neveikia, jei nė viena prijungta kamera nepalaiko to tipo. Visi jie tampa pilki, kai įjungta funkcija „Fastest Capture“.
* **Eilutės pagal kameras**: žymės langelis „Įtraukti“ ir išskleidžiamas (▸/▾) sąrašas su tos kameros taikomais eksporto tipais bei atskirais žymės langeliais. Eilutėje rodomas įjungtų tipų skaičius, pvz., „4/6“.

### Eksporto tipai ir kameros, kuriose jie palaikomi

Yra šeši eksporto tipai: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Kiekvienos kameros eilutėje rodomi tik tie tipai, kurie jai taikomi:

| Eksporto tipas | Turinys | RGB (FRGB) | „Bayer“ multispektrinis (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | „Bayer“ mozaika (mono: viena juosta) tiesiai iš jutiklio | ✓ | ✓ | ✓ |
| **Debayered** | Linijinis demozėjimas (mono: 1 kanalas pilkosios skalės) | ✓ | ✓ | ✓ |
| **Peržiūra** | Pilna vaizdo apdorojimo grandinė (baltos spalvos balansas + gama pagal fotoaparato profilį; daugiaspektrinė: dirbtinių spalvų išplėtimas) | ✓ | ✓ | ✓ |
| **Spinduliavimas** | float32 W/m²/sr/nm per visą radiometrinę grandinę | — (nėra) | ✓ | ✓ |
| **Atspindžio koeficientas** | uint16 ρ (32768 = 1,0) | — (nėra) | ✓ — rodomas tik tada, kai kamera turi DAQ šviesos jutiklį (savo arba paveldėtą iš matricos) | tas pats kaip daugiaspektrinis |
| **Indeksas** | Augmenijos indeksas (LUT) atvaizdavimas | — | ✓ — reikalauja, kad kameroje būtų įjungtas, ne tuščias indekso išraiška, ir neteikiamas kombinuotų masyvų nariams (masyvas turi vieną bendrą indeksą) | — (indeksui reikia ≥2 juostų; žr. [Mono kameros ir augmenijos indeksai](mono-indices.md)) |

Spinduliavimas ir atspindys niekada nesiūlomi RGB kameroms — spinduliavimas pagal „Bayer“ matricą nėra reikšmingas plačiajuosčio fotometrinio jutiklio atveju.

### Greičiausias įrašymas

Perjungiklis **⚡ Greičiausias fiksavimas — tik RAW**(įjungtas — oranžinis) pakeičia visus eksporto pasirinkimus į**tik RAW** — taip pat prideda nemokamą kombinuoto indekso kompoziciją masyvams — todėl kadras išsaugomas kuo greičiau: spinduliavimo stiprumo/atspindžio koeficiento/ekrano skaičiavimai fiksavimo metu visiškai praleidžiami.

{% hint style="info" %}
**`.daq` vis tiek išsaugomas.** Kai priskiriamas šviesos jutiklis, funkcija „Greičiausias fiksavimas“ vis tiek įrašo DAQ žemyn nukreiptą rodmenį šalia neapdorotų kadrų — taigi spinduliavimo, atspindžio ir indekso produktai gali būti sukurti vėliau, pakartotinai apdorojant (žr. [Fiksavimų pakartotinis apdorojimas](#re-processing-captures-into-calibrated-products)). Be to, „Fastest Capture“ nepažeidžia jūsų pažymėtų žymimųjų langelių pasirinkimų: išjunkite šią funkciją, ir jie vėl atsiras.
{% endhint %}

### Valdymo elementai pagal masyvą

Kiekvienam prijungtam masyvui skirta atskira grupės kortelė langelyje:

* **Varnelė „Įtraukti“** (trijų būsenų visų narių atžvilgiu) ir masyvo pavadinimas su jo rodymo režimu: „(sujungtas | atskiras)“.
* Varnelė **„Suderinta“**(pagal numatytuosius nustatymus**įjungta**): eksportuojami elementai pritaikomi prie masyvo suderinimo profilio, todėl eksportai yra suderinti pagal pikselius tarp kamerų. Neapdoroti duomenys lieka neiškreipti, tačiau jų metaduomenyse yra įrašytas transformacijos duomenys. (Pats profilis apskaičiuojamas [matricos nustatymų lange](camera-settings.md#alignment-co-registration-combined-only).)
* Kamerų eilučių elementai yra įterpti į kortelę.

Masyvo kortelėje taip pat yra du įrašymo įrenginiai. Juos galima laikyti **stebėjimo ir analizės** įrenginiais:

| Įrašymo įrenginys | Klasė | Ką įrašo |
| --- | --- | --- |
| **● Įrašyti indeksinį vaizdo įrašą / ■ Sustabdyti įrašymą** *(tik sujungtuose masyvuose)* | **Stebėjimas** | Tiesioginis sujungto indekso kompozicinis vaizdo įrašas 10 kadrų per sekundę dažniu — 8 bitai, peržiūros skiriamoji geba, įtrauktas LUT. Reikia atidaryto projekto ir tiesioginio vaizdo transliacijos. Rodo kadrus ir praėjusį laiką įrašymo metu. |
| **⦿ Įrašyti neapdorotą seriją / ■ Sustabdyti neapdorotą seriją** *(bet kuri matrica)* | **Analizė**| Neapdoroti „Bayer“ kadrai tiesioginiu įrašymo dažniu (be apdorojimo), taip pat kiekvieno kadro manifestas ir `.daq` rodmenys, į `captures/bursts/`. Po serijos pasirodo mygtukas**Sukurti vaizdo įrašą**: jis seriją apdoroja neprisijungus prie interneto į kalibruotą vaizdo įrašą — sujungtą indeksą ir (arba) kiekvienos kameros spinduliavimą / atspindį / indeksą — bei pasirinktinius TIFF failus. Sujungto indekso kūrimas prasideda automatiškai, kai sustabdote seriją.

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

|## „Capture All“ (Užfiksuoti viską) eiga

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->

Paspauskite **„Capture All“** šoninės juostos kamerų sąraše:

1. Kiekviena įtraukta, matoma, nepertraukta kamera fiksuoja vaizdą pagal pasirinktus eksporto tipus. **Matricos suveikia kaip vienas sinchronizuotas trigeris** (viena sinchronizuota grupė, apimanti visus narius — žr. [Daugiakamerines matricas](arrays.md)); atskiros kameros fiksuoja atskirai.
2. Paslėptos (akies) arba pristabdytos kameros praleidžiamos. Masyvas visiškai blokuojamas tik tada, kai *visi* jo nariai yra paslėpti arba pristabdyti.
3. Kiekvieną kartą, kai priskiriamas šviesos jutiklis, atitinkamas DAQ žemyn nukreiptas rodmuo išsaugomas kaip `.daq` failas kartu su vaizdais — net ir tuo atveju, kai įrašoma tik neapdorota medžiaga — todėl radiometriniai produktai visada gali būti gauti vėliau.
4. Mygtukas rodo realaus laiko pažangą — „Fiksuojama… baigta/iš viso“ — o nepertraukiamojo/intervalinio režimo metu tampa **„Stop (N)“**. Kiekvienam fiksavimo elementui nustatytas 300 s laiko limitas.
5. Baigus skrydžio etapą, rezultatų langelyje rodomas pranešimas **„Išsaugota N failų“**arba**„Išsaugota N, F nepavyko“**, taip pat „(S paslėpta/pauzuota/praleista)“, jei kameros buvo praleistos.

## Kur saugomi įrašai

Įrašai saugomi atidarytame projekte `<project>/captures/`. Kiekvienas eksporto tipas patenka į **savo pakatalogį**, todėl daugiapakopio įrašymo metu tipai niekada nesimaišo:

```
<project>/captures/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when Index is selected
├── composite/     array foreground/background live-view composite, when produced
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── *.daq          the downwelling reading matched to the capture
```

* `<ts>` yra įrašo laiko žyma, o `<serial>` – kameros serijos numeris. Atskiri įrašai pavadinami `capture_<ts>_SN<serial>_<level>`; masyvo įrašai, gauti iš vieno sinchronizuoto trigerio, pavadinami `sync_<ts>_SN<serial>_<level>` ir **visoms grupės kameroms priskiriamas vienas laiko žymeklis** (lygio priesaga nebepridedama, kai kamera išsaugo tik vieną lygį).
* **Viena ypatybė, kurią reikia žinoti:** ekrano lygis saugomas aplanke, pavadintame „`preview/`“, o failų pavadinimuose išlieka „`_display`“ — aplankas ir priesaga skiriasi tik tam lygiui.
* Nežinomi lygiai perkeliami į aplanką, pavadintą jų pačių pavadinimu; jei nepavyksta sukurti pakatalogo, failas įrašomas į „captures“ šaknį, o ne prarandamas.
* „Capture“ TIFF failai pagal numatytuosius nustatymus suspaudžiami be praradimų (DEFLATE) ir visus kalibravimo bei apdorojimo metaduomenis saugo **failo XMP viduje** — įrašai yra savaime aprašomi, be jokių papildomų failų, išskyrus „`.daq`“ skaitymo failą.

Tai tas pats išdėstymas, kurį „`chloros-cli lattice capture`“ / „`array-capture`“ įrašo į savo „`-o`“ katalogą — aprašyta [„CLI“ nuorodoje § Kaip atrodo įrašų aplankas](../reference/cli-reference.md#what-a-captures-folder-looks-like).

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, a .daq file at the root, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Įrašų perdirbimas į kalibruotus produktus

Užfiksuoti neapdoroti kadrai ir išsaugotas `.daq` yra viskas, ko reikia apdorojimo procesui — būtent todėl „Fastest Capture“ yra saugus naudoti realiame darbe.

* **GUI**: pridėkite užfiksuotų kadrų aplanką prie projekto ([Failų pridėjimas prie projekto](../processing-images-gui/adding-files-to-a-project.md)) ir apdorokite kaip įprasta.
* **CLI**: nukreipkite `process` į**užfiksuotų kadrų šaknį**:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` paprastai importuoja tik jūsų nurodytą aplanką, tačiau jei tame aplanke nėra vaizdų, o yra pakatalogiai, programa automatiškai pereina į juos — taigi pakatalogiai ir **`.daq`**šakninio aplanko failai surenkami vienu kartu. Kiekvienas įrašas importuojamas kaip**vienas vaizdas**, prie kurio kiti lygiai pridedami kaip peržiūros režimai, o ne kaip atskiras vaizdas kiekvienam lygiui.

Taip pat galima tiesiogiai pavadinti lygio pakatalogį (pvz., `…/captures/raw/`), tačiau pagrindiniai „`.daq`“ failai lieka neįtraukti — nukopijuokite juos kartu, kai iš naujo gaunate radiometrinį produktą iš „`raw/`“, kitaip laiko žymos atitikimas neturės su kuo susieti.

{% hint style="warning" %}
**Apdorojimas visada prasideda nuo `raw`.**Kiekviename įraše neapdorotas kadras yra apdorojimo grandinės šaltinis; `debayered`, `radiance`, `reflectance` ir `preview` pateikiami kaip peržiūros režimai, tačiau niekada nėra grąžinami atgal į apdorojimo grandinę — pakartotinis išvestinio produkto apdorojimas vėl pritaikytų vinjetę, spalvas ir spinduliavimo skaičiavimus, kurie jau yra įtraukti į jo pikselius, todėl Chloros atmetamas, o ne apdorojamas du kartus. `index/` ir `composite/` atvaizdai iš viso neapdorojami (jie yra išvesties duomenys, o ne užfiksuoti vaizdai). „Captures“ aplankas, išsaugotas**be** neapdorotų duomenų importo, rodomas įprastai, tačiau „`process`“ jį praleidžia ir apie tai praneša; „`--input-level {raw,debayered,processed}`“ yra sąmoningai numatytas išeities variantas, priverčiantis nustatyti įėjimo tašką. Tikslius praleidimo pranešimus rasite [CLI nuorodoje](../reference/cli-reference.md#what-a-captures-folder-looks-like).
{% endhint %}

Dar du elgesio atvejai, kuriuos verta žinoti kuriant perdirbimo scenarijus:

* `chloros-cli process` vykdymas, kurio metu buvo prašoma produktų, bet **nebuvo užrašyta jokių vaizdo produktų, baigiasi akivaizdžia nesėkme ir išeina su nulinės vertės rezultatu** — jūs niekada negausite tylaus tuščio vykdymo. Sėkmingi vykdymai praneša apie produktų skaičių. (Sąmoningas vykdymas, skirtas tik metaduomenims, vis tiek laikomas sėkmingu.)
* Pakartotinai importuoti apdoroti eksportai niekada neužima neapdorotų duomenų lizdo — originalūs neapdoroti duomenys visada lieka apdorojimo grandinės šaltiniu.

## CLI ekvivalentai

Viskas, kas pateikta šiame puslapyje, gali būti valdoma be grafinės sąsajos. GUI įrašymo režimai tiesiogiai atitinka `chloros-cli lattice array-capture`:

| GUI | CLI |
| --- | --- |
| Vienkartinis | `chloros-cli lattice array-capture` |
| Nuolatinis | `array-capture --continuous [--count N] [--duration S]` |
| Intervalinis | `array-capture --interval S [--duration S]` |
| Greičiausias fiksavimas | `array-capture --fastest` |
| Suderintas žymimasis langelis | `--aligned / --no-aligned` |
| Eksporto tipo žymimieji langeliai | `--processing LEVEL` arba `--levels L1,L2,…` (numatyta reikšmė `all`) |
| Vaizdo įrašo indeksas | `chloros-cli lattice array-record` |
| Neapdorotų serijos kadrų įrašymas / Vaizdo įrašo sudarymas | `chloros-cli lattice array-burst` / `array-build-video` |

Išsamios žymių lentelės, „Smart-AE“ nustatytos ekspozicijos parinktis (`--smart`) ir pastovaus greičio modelis aprašyti skyriuje [CLI Nuoroda § Įrašymo režimai, įrašymo įrenginiai ir neprisijungus atliekamas perdirbimas](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).
