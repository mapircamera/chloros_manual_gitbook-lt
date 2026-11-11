# Įrašymo nustatymai ir režimai

Įrašymas skirtuke „Kameros“ valdomas vienu raudonu mygtuku **„Įrašyti viską“**ir vienu langeliu**„Įrašymo nustatymai“**, kuriame nustatoma, kokį rezultatą duos šis mygtukas: kurios kameros dalyvaus, kokius eksporto tipus kiekviena kamera išsaugos ir ar užraktas bus suaktyvintas vieną kartą, nepertraukiamai ar tam tikrais intervalais. Šiame puslapyje aprašomas visas procesas – konfigūracija, pats fotografavimas, kur failai išsaugomi diske ir kaip juos vėliau perdirbti į kalibruotus produktus. Pačios kameros ir masyvo valdymo funkcijos yra [Kameros nustatymuose](camera-settings.md).

{% hint style="info" %}
**Fiksavimui būtinas atidarytas projektas.** Funkcijos „Užfiksuoti viską“ ir nustatymų ratukas „Užfiksavimo nustatymai“ yra išjungti, kol projektas nėra atidarytas („Sukurkite arba atidarykite projektą, kad būtų galima išsaugoti užfiksuotus vaizdus“). Kiekvienas užfiksuotas vaizdas išsaugomas projekto aplanke, esančiame `captures/`.
{% endhint %}

## Užfiksavimo nustatymų langas

Jį atidarykite naudodami **ratuką šalia „Užfiksuoti viską“**šoninės juostos kamerų sąraše arba mygtuką**„Atidaryti užfiksavimo nustatymus…“** bet kurio kameros nustatymų lango apačioje. Antraštėje rašoma „Užfiksavimo nustatymai“ su mygtuku ← „Atgal“.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->



Jūsų čia pasirinkimai — įtrauktos kameros, žymės pagal tipą ir įrašymo režimas — išsaugomi **kiekvienam projektui** ir atkuriami, kai jį vėl atidarote.

### Įrašymo režimai

Trijų režimų mygtukai skydelio viršuje:

| Režimas | Ką daro | Papildomi nustatymai (numatyti) |
| --- | --- | --- |
| **Vienkartinis** *(numatyta)* | Vienas įrašymas iš visų pasirinktų kamerų. | — |
| **Nuolatinis**| Įrašai atliekami vienas po kito, kol įvyksta sustabdymo sąlyga. | Sustabdyti pagal**nuotraukų skaičių** (numatyta reikšmė 1) *arba* **fotografavimo trukmę** (numatyta reikšmė 10 s; vienetai: sekundės / minutės / valandos / dienos). |
| **Intervalas**(laiko tarpas) | Serijos pagal laikmatį. |**Nuotraukų skaičius per intervalą**(numatyta reikšmė 1) ·**Kas**N vienetų (numatyta reikšmė 5 s) ·**Per** N vienetų (numatyta reikšmė 1 m). |

Nuolatinio arba intervalinio režimo metu mygtukas „Užfiksuoti viską“ veikimo metu tampa mygtuku **„Sustabdyti (N)“**, skaičiuojant kadrus, kai jie pasiekia sistemą. Pilnas diskas sustabdo veikimą (žr. [„Užfiksuoti viską“ eigą](#the-capture-all-flow)).**Nuolatinis režimas masyvuose veikia vidiniame serveryje (nauja funkcija nuo 1.3.0 versijos).** Kai kiekviena sąraše esanti kamera priklauso masyvui (atskiros kameros paslėptos arba išskirtos), „Continuous“ nebeatsiunčia po vieną užklausą kiekvienam užfiksavimui: Chloros

užkulisiai patys vykdo užfiksavimo ciklą, įrašydami vieną sinchronizuotą grupę, kol parenka kitą, o mygtukas tikrina jų skaičių. Jei sąraše yra atskirų kamerų, Chloros

užfiksuoja duomenis po vieną, kaip ir anksčiau.

{% hint style="warning" %}
**Greitas įrašymas Windows

sistemoje: Chloros

užkulisį reikia išskirti iš „Microsoft Defender“ skenavimo.** „MAPIR

“ testuose nustatyta, kad kiekvieno failo, kurį įrašo didelės spartos įrašymo funkcija, skenavimas realiuoju laiku atima apie 30 % įrašymo spartos (šimtai failų per sekundę esant mažam ROI). Windows

diegimo programa siūlo parinktį **„Išskirti Chloros

backendą iš „Microsoft Defender“ nuskaitymo (žymiai greitesnis kameros fiksavimas; rekomenduojama)“**, kuri pagal numatytuosius nustatymus yra pažymėta, o pašalinant programą ši išimtis panaikinama. Jei šią parinktį atžymėjote, paleiskite diegimo programą iš naujo su pažymėta parinktimi arba pridėkite „Defender“ proceso išimtį „`chloros-backend.exe`“ (Chloros

“ diegimo aplanko „`backend`“ aplanke). Kai „Defender“ naudoja vieną ar daugiau procesoriaus branduolių, kol vyksta nuolatinis masyvo vaizdo įrašymas, Chloros

užkulisinė programa tai vieną kartą užregistruoja savo žurnale, nurodydama tuos pačius du sprendimus.
{% endhint %}

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->




### Kamerų ir eksporto tipų pasirinkimas

Šio lango pagalbos tekstas viską apibendrina: pasirinkite, kokias kameras ir eksporto tipus generuoja funkcija „Capture All“ („Užfiksuoti viską“) — pagal numatytuosius nustatymus viskas yra įjungta, o pasirinkimai išsaugomi kartu su šiuo projektu.

* Mygtukai **„Pasirinkti viską“ / „Neatrinkti nieko“** vienu metu perjungia kiekvienos kameros įtraukimo žymės langelį.
* **Masiniai eksporto tipo perjungikliai**(dvi mygtukų eilės):**Visi „Raw“ / Visi „Debayered“ / Visi „Preview“ / Visi „Radiance“ / Visi „Reflectance“ / Visi „Index“**. Kiekvienas iš jų turi tris būsenas: žalia ✓ = įjungta visose kamerose, kurios tai palaiko, gintarinė – = įjungta kai kuriose, pilka = nė vienoje. Perjungiklis yra išjungtas, jei nė viena prijungta kamera nepalaiko to tipo. Visi jie tampa pilki, kai įjungta funkcija „Fastest Capture“.
* **Eilutės pagal kameras**: žymės langelis „Įtraukti“ ir išskleidžiamas (▸/▾) sąrašas su tos kameros taikomais eksporto tipais bei atskirais žymės langeliais. Eilutėje rodomas įjungtų elementų skaičius, pvz., „4/6“.

### Eksporto tipai ir kameros, kuriose jie prieinami

Yra šeši eksporto tipai: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Kiekvienos kameros eilutėje rodomi tik tie tipai, kurie jai taikomi:

| Eksporto tipas | Turinys | RGB

(FRGB) | „Bayer“ multispektrinis (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | „Bayer“ mozaika (mono: viena juosta) tiesiai iš jutiklio | ✓ | ✓ | ✓ |
| **Debayered** | Linijinis demozėjimas: iš „raw“ pašalinamas kalibravimo paketo tamsos poslinkis (DSNU), tada atliekamas kraštų atpažįstantis demozėjimas — be linijiškumo korekcijos, plokščio lauko ar atskyrimo. Išsaugoma kaip 3 kanalų RGB

TIFF

(mono: 1 kanalas pilkosios skalės) | ✓ | ✓ | ✓ |
| **Peržiūra** | Visas vaizdo apdorojimo grandinė (baltos spalvos balansas + gama pagal fotoaparato profilį, taip pat RGB

fotoaparato „Highlights“ nustatymas; multispektrinis: dirbtinių spalvų išplėtimas) | ✓ | ✓ | ✓ |
| **Spinduliavimas** | „float32“ W/m²/sr/nm per visą radiometrinę grandinę | — (nėra) | ✓ | ✓ |
| **Atspindžio koeficientas** | „uint16“ ρ (32768 = 1,0) | — (nėra siūloma) | ✓ — rodoma tik tada, kai kamera turi DAQ šviesos jutiklį (savo arba paveldėtą iš masyvo) | tas pats kaip daugiaspektrinė |
| **Indeksas** | Augmenijos indeksas (LUT) atvaizdavimas | — | ✓ — reikalauja, kad kameroje būtų įjungtas, ne tuščias indekso išraiška, ir nesiūloma kombinuotų masyvų nariams (masyvas turi vieną bendrą indeksą) | — (indeksui reikia ≥2 juostų; žr. [Mono kameros ir augmenijos indeksai](mono-indices.md)) |

RGB

kameroms niekada nesiūlomas spinduliavimas ir atspindys — spinduliavimas pagal „Bayer“ sistemą nėra reikšmingas plačiajuosčio fotometrinio jutiklio atveju.

### Greičiausias fiksavimas

Perjungiklis **⚡ Greičiausias fiksavimas — tik RAW**(įjungtas — oranžinis) pakeičia visus eksporto pasirinkimus į**tik RAW** — taip pat nemokamą kombinuotų indeksų kompoziciją masyvams — todėl kadras išsaugomas kuo greičiau: spinduliavimo/atspindžio/rodymo skaičiavimai fiksavimo metu visiškai praleidžiami.

{% hint style="info" %}
**`.daq` vis tiek išsaugomas.** Kai priskiriamas šviesos jutiklis, „Greičiausias fiksavimas“ vis tiek įrašo DAQ žemyn nukreiptą rodmenį (į fiksatų aplanką „`daq/`“) — taigi spinduliavimo, atspindžio ir indekso produktai gali būti sukurti vėliau, pakartotinai apdorojant (žr. [Užfiksuotų duomenų pakartotinis apdorojimas](#re-processing-captures-into-calibrated-products)). „Fastest Capture“ taip pat nepažeidžia jūsų pažymėtų žymimųjų langelių pasirinkimų: išjunkite šią funkciją, ir jie vėl atsiras.
{% endhint %}

### Valdymo elementai pagal masyvą

Kiekvienam prijungtam masyvui skirta atskira grupės kortelė lango dalyje:

* **Varnelė „Įtraukti“** (trijų būsenų visų narių atžvilgiu) ir masyvo pavadinimas su jo rodymo režimu: „(sujungtas | atskiras)“.
* Varnelė **„Suderinta“**(pagal numatytuosius nustatymus**įjungta**): narių eksportai pritaikomi prie masyvo suderinimo profilio, todėl eksportai yra suderinti pikselių lygiu tarp kamerų. Neapdoroti duomenys lieka neiškreipti, tačiau jų metaduomenyse išsaugomas transformacijos parametras. (Pats profilis apskaičiuojamas [matricos nustatymų lange](camera-settings.md#alignment-co-registration).) Matricos narių failai išsaugomi pagal kiekvienos kameros natūralią orientaciją, o bet kokie „Veidrodžio atspindžio“ / „Pasukimo“ nustatymai įrašomi į failą, o ne taikomi.
* Elementų kamerų eilutės yra įterptos į kortelę.

Masyvo kortelėje taip pat yra du įrašymo įrenginiai. Juos galima laikyti **stebėjimo ir analizės** įrenginiais:

| Įrašymo įrenginys | Lygis | Ką įrašo |
| --- | --- | --- |
| **● Įrašyti indeksinį vaizdo įrašą / ■ Sustabdyti įrašymą** *(tik sujungti masyvai)* | **Stebėjimas** | Tiesioginis sujungto indekso kompozicinis vaizdo įrašas 10 kadrų per sekundę dažniu — 8 bitai, peržiūros skiriamoji geba, įtrauktas LUT. Reikia atidaryto projekto ir tiesioginio transliavimo vaizdo. Rodo kadrus ir praėjusį laiką įrašymo metu. |
| **⦿ Įrašyti neapdorotą seriją / ■ Sustabdyti neapdorotų kadrų seriją** *(bet kuri matrica)* | **Analizė**| Neapdoroti „Bayer“ kadrai tiesioginio įrašymo dažniu (be apdorojimo), taip pat kiekvieno kadro manifestas ir `.daq` rodmenys, įrašomi į `captures/bursts/`. Po serijos pasirodo mygtukas**Sukurti vaizdo įrašą**: jis seriją apdoroja neprisijungus prie interneto į kalibruotą vaizdo įrašą — sujungtą indeksą ir (arba) kiekvienos kameros spinduliavimą / atspindį / indeksą — bei pasirinktinius TIFF failus. Sujungto indekso kūrimas prasideda automatiškai, kai sustabdote seriją. |

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

## „Capture All“ (Užfiksuoti viską) procesas

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->



Paspauskite **„Capture All“** šoninės juostos kamerų sąraše:

1. Kiekviena įtraukta, matoma ir nepašaldyta kamera fiksuoja vaizdą pagal pasirinktus eksporto tipus. **Matricos suveikia kaip vienas sinchronizuotas trigeris** (viena sinchronizuota grupė, apimanti visus narius — žr. [Daugiakamerės matricos](arrays.md)); atskiros kameros fiksuoja vaizdą individualiai.
2. Paslėptos (akies) arba sustabdytos kameros praleidžiamos. Masyvas visiškai blokuojamas tik tada, kai *visi* jo nariai yra paslėpti arba sustabdyti.
3. Kai priskiriamas šviesos jutiklis, atitinkamas DAQ žemyn nukreiptas matavimas išsaugomas kaip `.daq` failas įrašų `daq/` aplanke — net ir tuo atveju, kai įrašai yra tik neapdoroti — todėl radiometriniai produktai visada gali būti gauti vėliau.
4. Mygtukas rodo realaus laiko pažangą — „Fiksuojama… baigta/iš viso“ — o nepertraukiamojo/intervalinio režimo metu tampa **„Stop (N)“**. Kiekvienam fiksavimo elementui nustatytas 300 s laiko limitas.
5. Baigus ciklą, rezultatų langelyje pasirodo pranešimas **„Išsaugota N failų“**arba**„Išsaugota N, F nepavyko“**, taip pat „(S paslėpta/pauzuota/praleista)“, jei kameros buvo praleistos. Jei diskas užsipildo, procesas sustabdomas ir pranešama**„Sustabdyta: diskas užpildytas. Išsaugota N, F nepavyko“** — kiekvienas failas, kurio nepavyko įrašyti, skaičiuojamas kaip nepavykęs.

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
├── clamp_masks/   radiance/…_clampmask.tif, reflectance/…, index/… — unmix clamp masks,
│                  one subfolder per product folder, only when a band was clamped
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── daq/           the downwelling reading(s) matched to each capture (.daq), each with a
                   .daq.cameras.json naming the cameras it describes
```

* `<ts>` yra įrašo laiko žyma, o `<serial>` – kameros serijos numeris. Atskiri įrašai pavadinami `capture_<ts>_SN<serial>_<level>`; masyvo įrašai, gauti iš vieno sinchronizuoto trigerio, pavadinami `sync_<ts>_SN<serial>_<level>` ir **visoms grupės kameroms priskiriamas vienas laiko žymos kodas** (lygio priesaga nebepridedama, kai kamera išsaugo tik vieną lygį).
* **Viena ypatybė, kurią reikia žinoti:** ekrano lygis saugomas aplanke, pavadintame „`preview/`“, o failų pavadinimuose išlieka „`_display`“ — aplankas ir priesaga skiriasi tik tam lygiui.
* Nežinomi lygiai automatiškai perkeliami į aplanką, pavadintą jų pačių pavadinimu; jei nepavyksta sukurti pakatalogo, failas įrašomas į „captures“ šakninį katalogą, o ne prarandamas.
* Užfiksuoti TIFF failai pagal numatytuosius nustatymus suspaudžiami be nuostolių (DEFLATE) ir visus kalibravimo bei apdorojimo metaduomenis saugo **failo XMP viduje** — įrašai yra savaime aprašomi, be jokių papildomų failų, išskyrus „`.daq`“ įrašą (ir jo „`.daq.cameras.json`“).
* **Fiksavimo kaukės (nauja funkcija versijoje 1.3.0).**Ten, kur 3×3 spektrinis atskyrimas turėjo fiksuoti juostą iki 0 bent viename pikselyje, spinduliavimo, atspindžio ir indekso produktai gauna kaukę kiekvienam pikseliui, `clamp_masks/<product folder>/<product name>_clampmask.tif` (bitas 0 = juosta produkto R kanale, bitas 1 = G, bitas 2 = B); produkto `Chloros:UnmixClampMask` XMP žymė nurodo į ją. Kaukės saugomos**šalia** produkto aplankų, niekada jų viduje, todėl tokį produkto aplanką kaip `reflectance/` galima perduoti fotogrametrijos programinei įrangai tokiu, koks jis yra. „Chloros

“ importavimo funkcija kaukes praleidžia.
* **Orientacija (1.3.0).** Atskiros kameros „Preview“, „Radiance“, „Reflectance“ ir „Index“ produktai pasukami pagal jos „Mirror/Rotation“ nustatymą; „Raw“ ir „Debayered“ lieka tokie, kokius juos nuskaitė jutiklis. Visi masyvo narių failai išlieka nepakeisti. Kiekviename faile šis nustatymas įrašomas į XMP, todėl pakartotinio apdorojimo metu jis taikomas taip pat.
* **Kuriai kamerai priklauso „`.daq`“ (1.3.0).** Apdorojimo metu užfiksuoto vaizdo „`.daq`“ atitikmuo randamas tik tarp vaizdų iš tų kamerų, kurių pavadinimai nurodyti jo „`.daq.cameras.json`“ laukelyje. Todėl atskira kamera, neturinti savo šviesos jutiklio, nebeperima kitos kameros rodmenų — priskirkite jai jutiklį arba įrašykite jį skirtuke „Šviesos jutikliai“. Šviesos jutiklio įrašas (ir bet kuris `.daq` be to failo, įskaitant senesnius įrašus) vis dar tinka kiekvienai kamerai.

Tai tas pats išdėstymas, kurį `chloros-cli lattice capture` / `array-capture` įrašo į savo `-o` katalogą — aprašyta [CLI

žinyne § Kaip atrodo įrašų aplankas](../reference/cli-reference.md#what-a-captures-folder-looks-like).

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, the daq/ and clamp_masks/ folders, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Užfiksuotų kadrų perdirbimas į kalibruotus produktus

Užfiksuoti neapdoroti kadrai ir išsaugotas `.daq` yra viskas, ko reikia apdorojimo procesui — būtent todėl „Fastest Capture“ yra saugus naudoti realiame darbe.

* **GUI**: pridėkite užfiksuotų kadrų aplanką prie projekto ([Failų pridėjimas prie projekto](../processing-images-gui/adding-files-to-a-project.md)) ir apdorokite kaip įprasta.
* **CLI**: nukreipkite `process` į **užfiksuotų kadrų šaknį**:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` paprastai importuoja tik jūsų nurodytą aplanką, tačiau jei tame aplanke nėra vaizdų, o yra pakatalogiai, jis automatiškai nusileidžia į juos — taigi pakatalogiai ir aplankas `daq/` yra surenkami vienu kartu. Kiekvienas įkėlimas importuojamas kaip **vienas vaizdas**, prie kurio kiti lygiai pridedami kaip peržiūros režimai, o ne kaip po vieną vaizdą kiekvienam lygiui.

Tiesioginis lygio pakatalogio pavadinimo nurodymas (pvz., `…/captures/raw/`) taip pat veikia, tačiau palieka aplanką „`daq/`“ – nukopijuokite jį kartu, kai-iš `raw/` gaunate radiometrinį produktą, kitaip laiko žymos atitikimas neturės su kuo susieti.

{% hint style="warning" %}
**Apdorojimas visada prasideda nuo `raw`.** Kiekviename įraše neapdorotas kadras yra apdorojimo grandinės šaltinis; `debayered`, `radiance`, `reflectance`, o `preview` pateikiami kaip peržiūros režimai, tačiau niekada nėra grąžinami atgal į apdorojimo grandinę — pakartotinis išvestinio produkto apdorojimas vėl pritaikytų vinjetę, spalvą ir spinduliavimo skaičiavimus, kurie jau yra įtraukti į jo pikselius, todėl Chloros

atsisako tokio apdorojimo, o ne atlieka dvigubą apdorojimą. `index/` ir `composite/` atvaizdai visiškai neapdorojami (jie yra išvesties duomenys, o ne užfiksuoti vaizdai). „Captures“ aplankas, išsaugotas **be** neapdorotų duomenų importo, rodomas įprastai, tačiau „`process`“ jį praleidžia ir apie tai praneša; „`--input-level {raw,debayered,processed}`“ yra sąmoningai numatytas išeities variantas, priverčiantis nustatyti įėjimo tašką. Tikslius praleidimo pranešimus rasite [CLI

žinyne](../reference/cli-reference.md#what-a-captures-folder-looks-like).
{% endhint %}

Dar du elgesio atvejai, kuriuos verta žinoti kuriant pakartotinio apdorojimo scenarijus:

* `chloros-cli process` vykdymas, kurio metu buvo prašoma sukurti produktus, bet **nebuvo sukurta jokių vaizdo produktų, baigiasi akivaizdžia nesėkme ir išeina su nelygiu nuliui rezultatu** — jūs niekada negausite tylaus tuščio vykdymo. Sėkmingi vykdymai praneša apie produktų skaičių. (Sąmoningas vykdymas, skirtas tik metaduomenims, vis tiek laikomas sėkmingu.)
* Pakartotinai importuoti apdoroti eksportai niekada neužima neapdoroto įrašo vietos — originalus neapdorotas įrašas visada lieka apdorojimo grandinės šaltiniu.

## CLI

ekvivalentai

Viską, kas pateikta šiame puslapyje, galima valdyti be grafinės sąsajos. GUI įrašymo režimai tiesiogiai atitinka `chloros-cli lattice array-capture`:

| GUI | CLI

|
| --- | --- |
| Vienkartinis | `chloros-cli lattice array-capture` |
| Nuolatinis | `array-capture --continuous [--count N] [--duration S]` |
| Intervalinis | `array-capture --interval S [--duration S]` |
| Greičiausias fiksavimas | `array-capture --fastest` |
| Žymės langelis „Suderinta“ | `--aligned / --no-aligned` |
| Eksporto tipo žymimieji langeliai | `--processing LEVEL` arba `--levels L1,L2,…` (numatyta reikšmė `all`) |
| Vaizdo įrašo indeksas | `chloros-cli lattice array-record` |
| Neapdorotų serijos kadrų įrašymas / Vaizdo įrašo sudarymas | `chloros-cli lattice array-burst` / `array-build-video` |

Išsamios žymių lentelės, „Smart-AE“ fiksuoto įrašymo parinktis (`--smart`) ir pastovaus greičio modelis aprašyti skyriuje [CLI

nuoroda § Įrašymo režimai, įrašymo įrenginiai ir neprisijungus atliekamas perdirbimas](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).
