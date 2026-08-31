# Apdorojimo grandinė „

Chloros“ 1.2.0 naudoja 4 siūlų apdorojimo grandinę, veikiančią kaip etapais suskirstyta surinkimo linija. Kiekvienas siūlas tvarko atskirą darbo eigos etapą, todėl vienu metu keli vaizdai gali būti apdorojami skirtinguose etapuose.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

***

## Apdorojimo grandinės architektūra

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Kiekvienas vaizdas nuosekliai pereina per visus keturis sriegius. Naudojant „Chloros+“ daugiasriegių apdorojimą, keli vaizdai vienu metu užima skirtingus sriegius – kol 3-iasis sriegis apdoroja vieną vaizdą, 1-asis sriegis gali aptikti kitą, 2-asis sriegis kalibruoti dar vieną, o 4-asis sriegis įrašyti užbaigtą vaizdą į diską.

Pažanga pranešama pagal kiekvieną srautą ir perduodama per „Server-Sent Events“ (užkulisinė sistema juos skelbia `/api/events`). „CLI“ tiesioginiame pažangos ekrane keturi etapai pažymėti kaip **Aptikimas, Analizė, Apdorojimas, Eksportavimas**.***

## Sriegių detalės

### 1-asis sriegis: Aptikimas

**Tikslas**: Įkelti vaizdus ir aptikti kalibravimo taškus.

* Skaito vaizdo failus iš disko — „Survey3“ `.raw`+`.jpg` poras, „LATTICE“ `.tif`/`.tiff` kadrus ir `.dng`
* Išskiria EXIF metaduomenis (GPS, fotoaparato modelį, laiko žymes, ekspoziciją)
* Aptinka kalibravimo taškus: „ArUco“ žymėtus taškų geometrijos modelius „LATTICE“ užfiksuotuose vaizduose ir klasikinį plokštelinį detektorių „Survey3“ kalibravimo taškų nuotraukose
* Rezultatai: vaizdo duomenys + metaduomenys + taškų aptikimo rezultatai

Pagrindinai įvesties/išvesties ir procesoriaus našumą ribojantis procesas.

### Srautas 2: Kalibravimas

**Tikslas**: Apskaičiuoti kalibravimo parametrus pagal aptiktus taikinius.

* Apskaičiuoja atspindžio kalibravimo koeficientus iš taikinių vaizdų
* Apskaičiuoja vinjetės korekcijos parametrus
* Nustato kalibravimo kreives kiekvienam juostos diapazonui
* Rezultatai: kalibravimo parametrai kiekvienam vaizdui

Tai yra su procesoriaus našumu susijęs skaičiavimo srautas. 3-iasis srautas jo laukia, kai įjungtas atspindžio kalibravimas, todėl jo koeficientai yra parengti prieš pradedant apdoroti bet kurį vaizdą.

### Srautas 3: Apdorojimas (GPU)

**Tikslas**: Taikyti korekcijas ir apskaičiuoti augmenijos indeksus.**Tai yra skaičiavimais labiausiai apkrautas srautas.*** **Debayering**: konvertuoja RAW Bayer duomenis į daugiakanalius vaizdus
  * Standartinis (greitas, vidutinė kokybė) — numatytasis, `--debayer standard`
  * Atsižvelgiantis į tekstūrą (lėtas, aukščiausia kokybė) — tik „Chloros+“, `--debayer texture-aware`, naudoja AI/ML triukšmo šalinimo modelį
  * „LATTICE mono“ (M3M) vaizdai yra vienos juostos: jiems praleidžiami demosaiko ir baltos spalvos balanso etapai (su vienos eilutės žurnalo pranešimu), o visi toje pačioje serijoje esantys M3C/Bayer vaizdai vis tiek juos gauna
* **Vignette korekcija**: visame vaizde taiko objektyvo vignette korekciją
* **Atspindžio kalibravimas**: taiko kalibravimo koeficientus, kad konvertuotų į atspindžio vertes
* **Indeksų skaičiavimas**: apskaičiuoja augmenijos indeksus (NDVI, NDRE, GNDVI, …)
* Rezultatai: apdoroti vaizdo duomenys, paruošti eksportui

Šis procesas labiausiai pasinaudoja GPU pagreitinimu, ir būtent šį procesą optimizuoja [dinaminis skaičiavimo pritaikymas](dynamic-compute-adaptation.md).

### 4-asis procesas: Eksportavimas

**Tikslas**: Įrašyti apdorotus vaizdus į diską.

* Įrašo išvesties failus pasirinktu formatu — `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`
* Įterpia metaduomenis į išvesties failus (GPS, laiko žymes, apdorojimo parametrus)
* Išvesties failus suskirsto projekto aplanke kaip „`<camera>/<format>/<Product>_Images/`“ — pavyzdžiui, „`LATT-M3M-L41-F550/tiff16/Reflectance_Calibrated_Images/`“. **Eksportuoti failai išlaiko šaltinio failo pavadinimą; produktą identifikuoja aplankas.**
* LATTICE vaizdų atveju vienas šaltinio kadras gali būti suskaidytas į kelis produktus (Debayered, Preview, Radiance, Reflectance, Index), kurių kiekvienas saugomas atskirame produkto aplanke
* Rezultatai: galutiniai failai diske

Daugiausia įvesties/išvesties apkrautasis procesas — SSD saugykla jį pastebimai pagerina.

***

## Veikimo principas: vykdytojai

3-iajame procese darbas su kiekvienu vaizdu lygiagrečiai atliekamas naudojant „Python“ standartinį `concurrent.futures`:

* **GPU strategijos**(`GPU_SINGLE`, `GPU_PARALLEL`) naudoja `ProcessPoolExecutor` su**spawn** pradžios metodą — kiekvienas darbininkas yra atskiras procesas su savo CUDA kontekstu (`fork` paveldėtų tėvo inicijuotą CUDA būseną ir sugadintų vaikus)
* **`CPU_PARALLEL`** naudoja `ThreadPoolExecutor` — „NumPy“ ir „OpenCV“ atleidžia GIL, todėl pakanka siūlų
* „Jetson“ įrenginiai su 8 GB ar mažiau bendros RAM visiškai praleidžia vykdytoją ir apdoroja duomenis pačiame procese, nuosekliai
* „Texture Aware“ GPU su mažiau nei 7 GB VRAM taip pat veikia nuosekliai — triukšmo šalinimo modelis negali tilpti daugiau nei vieną kartą „

Chloros“ nenaudoja jokių trečiųjų šalių paskirstytųjų sistemų (pavyzdžiui, „Ray“). Kaip pasirenkama strategija ir darbuotojų skaičius, žr. [Dinaminis skaičiavimo pritaikymas](dynamic-compute-adaptation.md).

***

## Sekcinis ir konvejerinis apdorojimas

### Laisvasis režimas (sekcinis)

Nemokamoje „Chloros“ versijoje vaizdai apdorojami **po vieną**, nuosekliai per visus keturis etapus:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI nemokamajame režime rodo supaprastintą pažangos juostą; jos nuoseklūs etapai nurodomi kaip **Tikslo aptikimas**, o po to –**Apdorojimas**.

### „Chloros“ režimas (konvejerinis)

Turint „Chloros“ licenciją, visi keturi srautai veikia **vienu metu** su skirtingais vaizdais:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI pažangos juosta rodo keturis etapus; užveskite pelę ant jos, kad pamatytumėte kiekvieno srauto pažangą. „CLI“ programoje tie patys keturi etapai rodomi tiesiogiai kaip **Aptikimas, Analizė, Apdorojimas, Eksportavimas**.

{% hint style="info" %}
**Viena etiketė, du pavadinimai.** „CLI“ 3-iąjį etapą vadina _Apdorojimas_. Backend&#x27;o „premium“ režimo pažangos srautas — tas, kurį atvaizduoja GUI pažangos juosta — tą patį etapą vadina _Kalibravimas_. Tai tas pats procesas, atliekantis tą patį darbą (3-iasis procesas: „debayer“, korekcijos, indeksai).
{% endhint %}

{% hint style="success" %}
**Apdorojimas konvejeriu naudojant „Chloros“+** gali būti 3–5 kartus greitesnis už nuoseklųjį apdorojimą, priklausomai nuo jūsų aparatinės įrangos ir duomenų rinkinio dydžio. Didžiausias greičio padidėjimas pasiekiamas sistemose su greitais GPU ir SSD.
{% endhint %}

***

## 4-ojo srauto eksporto pažanga

Eksporto srautas turi savo pažangos stebėjimo sistemą, kurią galite tikrinti atskirai:**CLI:**

```bash
chloros-cli export-status
```

**SDK:**

```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

Apdorojimas baigtas, kai 4-asis sriegis pasiekia 100 %.

{% hint style="info" %}
**Vykdymas, kurio metu nerašomi jokie vaizdai, laikomas nesėkme.**Sėkmingai užbaigus, `chloros-cli process` praneša, kiek vaizdų produktų buvo įrašyta (`Image products written: N`). Jei buvo užklausti produktai, bet**nė vienas**nebuvo įrašytas — tik `project.json` ir `calibration_data.json` — „CLI“ išspausdina `Processing finished but wrote no image products.` ir**baigia veikimą su nelygiu nuliui rezultatu**, nurodydama projekto aplanką ir įprastas priežastis (įvesties aplankas nebuvo atpažintas kaip įrašymo šaltinis — patikrinkite išdėstymą ir `--input-level` — arba nė vienas iš prašytų produktų nebuvo taikytinas toms kameroms). Skriptai gali remtis išėjimo kodu.
{% endhint %}

***

## Ryšys su dinamine skaičiavimo adaptacija

[Dinaminė skaičiavimo adaptacija](dynamic-compute-adaptation.md) pirmiausia veikia **3-iąjį srautą (apdorojimą)**:

* **`GPU_PARALLEL`**: 3-iasis srautas vienu metu apdoroja kelis vaizdus per GPU, naudodamas `fused_gpu` apdorojimo grandinę
* **`GPU_SINGLE`**: 3-iasis srautas serijizuoja prieigą prie GPU naudodamas semaforą, o darbo procesai vykdo sutampančius įvesties/išvesties veiksmus, naudodami `fused_gpu` arba atminties tausojantį `tiled_gpu` srautą
* **`CPU_PARALLEL`**: 3-iasis sriegis naudoja CPU pagrįstą apdorojimą su daugiasriegiu lygiagretiškumu

3-iojo srauto GPU atminties paskirstymas taip pat didėja, kai 1-asis ir 2-asis srautai baigia veiklą — žr. [Dinaminis GPU atminties paskirstymas](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Tolimesni veiksmai

* [Dinaminis skaičiavimo pritaikymas](dynamic-compute-adaptation.md) — kaip „Chloros“ pasirenka optimalią strategiją jūsų aparatinei įrangai
* [„NVIDIA Jetson“ vadovas](../linux/nvidia-jetson-guide.md) — platformai būdingas apdorojimo srauto elgesys „Jetson“
* [Apdorojimo stebėjimas](../processing-images-gui/monitoring-the-processing.md) — pažangos stebėjimas per grafinę vartotojo sąsają
* [„CLI“ nuoroda](../reference/cli-reference.md) — `process`, `export-status`, išėjimo kodai ir išvesties išdėstymas
