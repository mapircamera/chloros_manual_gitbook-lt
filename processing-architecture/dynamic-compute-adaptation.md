# Dinaminis skaičiavimo pritaikymas

Chloros 1.1.0 versijoje įdiegta pažangi aparatinės įrangos atpažinimo ir automatinio apdorojimo strategijos pasirinkimo funkcija. Apdorojimo variklis prisitaiko prie jūsų aparatinės įrangos – nuo „Jetson Nano“ iki darbo stoties su keliais GPU – be jokio rankinio konfigūravimo.

***

## Kaip tai veikia

Kai paleidžiate Chloros, jis automatiškai atlieka jūsų sistemos profiliavimą:

1. **Aptinka operacinę sistemą** — Windows arba Linux
2. **Nustato procesoriaus branduolius ir bendrą RAM**

3.**Aptinka GPU buvimą** — NVIDIA CUDA pajėgumą, VRAM, modelį
4. **Nustato „Jetson“ modelį** (jei taikoma) — per `/proc/device-tree/model`
5. **Patikrina šiluminius jutiklius** (Jetson) — temperatūrą atsižvelgiančiam apdorojimui
6. **Pasirenka optimalų skaičiavimo strategiją** — remiantis visa aptikta aparatine įranga
7. **Automatiškai konfigūruoja darbininkų skaičių, konvejerio tipą ir atminties paskirstymą**Rezultatas yra išsaugomas talpykloje, todėl vėlesni paleidimai prasideda greičiau. Jei aparatinė įranga pasikeičia (pvz., pridedamas GPU), Chloros atlieka naują profiliavimą kitą kartą paleidžiant.***

## Skaičiavimo strategijos

Chloros pasirenka vieną iš trijų skaičiavimo strategijų, atsižvelgdamas į jūsų aparatinę įrangą:

| Strategija | Reikalingas GPU | Darbininkai | Konvejeris | Tinkamiausia |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`** | Taip (12 GB+ VRAM arba 16 GB+ bendrai naudojama) | 3–4 | `fused_gpu` | Stacionarūs GPU su 12 GB+, „Jetson Orin NX“ 16 GB, „AGX Orin“ |
| **`GPU_SINGLE`** | Taip (&lt; 12 GB VRAM) | 1–3 | `tiled_gpu` | Pradinio lygio GPU, „Jetson Nano“, „Orin Nano“ |
| **`CPU_PARALLEL`** | Ne | branduoliai – 1 | `cpu_fallback` | Sistemos be NVIDIA GPU |

### Konvejerio tipai

* **`fused_gpu`** — Pilnas GPU apdorojimo kelias. Visos debayer, korekcijos ir indeksavimo operacijos atliekamos GPU per vieną sujungtą ciklą. Didžiausias našumas, tačiau reikalauja daugiau VRAM.
* **`tiled_gpu`** — Atminties tausojantis GPU kelias. Apdoroja vaizdus plytelių pavidalu, kad tilptų į ribotą GPU atmintį. Mažesnis našumas, tačiau veikia įrenginiuose su ribota atmintimi.
* **`cpu_fallback`** — Apdorojimas tik CPU, naudojant daugiasiūlį lygiagretumą. Naudojamas, kai nėra NVIDIA GPU.***

## Platformos specifinis elgesys

| Platforma | Strategija | Darbininkai | Konvejeris | Pastabos |
| --- | --- | --- | --- | --- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (serializuotas) | Atminties tausojantis režimas, apdoroja po vieną vaizdą |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` (lygiagretus) | Rekomenduojamas krašto įrenginys — tikras lygiagretus GPU apdorojimas |
| **Jetson AGX Orin 64 GB** | `GPU_PARALLEL` | 4 | `fused_gpu` (lygiagretus) | Maksimalus krašto įrenginio našumas |
| **Stalinis kompiuteris su 8 GB GPU** | `GPU_SINGLE` | 3 | `tiled_gpu` | Geras stalinio kompiuterio našumas su atminties taupiais plytelių blokais |
| **Stalinis kompiuteris su 12 GB+ GPU** | `GPU_PARALLEL` | 3–4 | `fused_gpu` | Optimalus stalinio kompiuterio našumas |
| **Tik CPU sistema** | `CPU_PARALLEL` | branduoliai – 1 | `cpu_fallback` | Nereikalingas GPU, naudoja ThreadPool |

{% hint style="info" %}
**Jetson suvienodinta atmintis**: Jetson įrenginiai dalijasi GPU ir CPU atmintimi. Jetson Orin NX 16 GB rodo ~15,3 GB VRAM, tačiau tai yra ta pati fizinė RAM, kurią naudoja OS ir CPU procesai. Chloros į tai atsižvelgia nustatydamas atminties paskirstymo ribas.
{% endhint %}

***

## Dinaminis GPU atminties paskirstymas

Chloros naudoja [4 sriegių apdorojimo vamzdyną](processing-pipeline.md):

* **1 sriegis** (Aptikimas) — Vaizdo įkėlimas, EXIF analizė, objekto aptikimas
* **2 sriegis** (Kalibravimas) — Atspindžio kalibravimo skaičiavimas
* **3-iasis srautas** (Apdorojimas) — GPU debayer, vinjetės korekcija, indekso skaičiavimas
* **4-asis srautas** (Eksportavimas) — Failų rašymas, metaduomenų įterpimas

Kai ankstesni srauto srautai baigia savo darbą (pvz., visi vaizdai buvo aptikti), jų GPU atminties paskirstymas atlaisvinamas ir **perdalinamas likusiems aktyviems srautams**. Tai reiškia, kad 3-iasis srautas (GPU intensyvus etapas) gauna vis daugiau atminties, kai procesas pažengia į priekį, taip pagerindamas našumą labiausiai skaičiavimų reikalaujančioms užduotims.

### Skirstymo etapai

| Etapas | Aktyvūs srautai | GPU atminties paskirstymas |
| --- | --- | --- |
| **Ankstyvasis** | 1, 2, 3, 4 | Padalinta tarp visų srautų |
| **Ankstyvasis vidurinis** | 2, 3, 4 | 1-ojo srauto atmintis perskirstoma |
| **Vėlyvasis vidurinis** | 3, 4 | 1-ojo ir 2-ojo srautų atmintis skiriama 3-iajam ir 4-ajam |
| **Vėlyvasis** | 3 arba 4 | Maksimali atmintis likusiam srautui |***

## Tekstūrų atpažinimo apdorojimas

Tekstūrų atpažinimo debayer metodas (tik Chloros+) naudoja žymiai daugiau GPU atminties nei standartinis metodas dėl AI/ML triukšmo šalinimo modelio:

* Sistemos su **&lt; 7 GB VRAM** yra priverstos naudoti sinchroninį apdorojimo ciklą tekstūrų atpažinimo režimui (po vieną vaizdą)
* Sistemos su **7 GB ir daugiau VRAM** gali apdoroti tekstūrą atsižvelgiant į tekstūrą vienu metu, tačiau su mažesniu darbininkų skaičiumi, palyginti su standartiniu režimu***

## Šilumos valdymas (Jetson)

Jetson įrenginiai turi šiluminius apribojimus, ypač uždarose arba ore esančiose aplinkose. Chloros stebi GPU ir CPU temperatūras ir automatiškai reguliuoja apdorojimą:

| Temperatūra | Reakcija |
| --- | --- |
| **&lt; 70 °C** | Įprastas veikimas — pilnas greitis |
| **70 °C** (Įspėjimas) | Sumažinti partijos dydį |
| **80 °C** (Kritinis) | Agresyvus greičio ribojimas — sumažinti lygiagretumą ir darbininkų skaičių |
| **90°C** (Išjungimas) | Visiškai sustabdyti GPU apdorojimą |

Temperatūros stebėjimui Jetson platformose naudojamas `tegrastats`. Stacionariose sistemose su pakankamu aušinimu terminis apribojimas retai suveikia.

***

## Atminties apkrovos valdymas

Chloros stebi sistemos atminties apkrovą apdorojimo metu:

* **Atminties riba**: 85 % panaudojimas sukelia konservatyvų elgesį
* **OOM sumažinimas**: Jei įvyksta atminties trūkumo įvykis, paskirstymas sumažinamas 25 % (0,75x daugiklis)
* **Pipeline grįžimas**: Esant dideliam atminties apkrovimui, konvejeris automatiškai perjungiamas iš `fused_gpu` į `tiled_gpu`
* **Rekomendacijos dėl keitimo**: „Jetson“ įrenginyje Chloros įspėja, jei keitimo vietos nepakanka jūsų duomenų rinkinio dydžiui***

## Skaičiavimo pritaikymo stebėjimas

### CLI būsenos išvestis

Pradėjus apdorojimą, CLI rodo aptiktą aparatinės įrangos profilį:

```

Chloros CLI 1.1.0
Platform: Linux aarch64 (Jetson Orin NX 16GB)
Strategy: GPU_PARALLEL | Workers: 3 | Pipeline: fused_gpu
CUDA: Available | GPU Memory: 15.3 GB (shared)
```

### Sistemos diagnostika

Paleiskite `chloros-cli selftest`, kad pamatytumėte pilną aparatinės įrangos profilį ir patikrintumėte skaičiavimo pajėgumus:

```bash
chloros-cli selftest
```

Tai patikrina CUDA prieinamumą, GPU atmintį, triukšmo šalinimo modelius ir galinio įrenginio ryšį.

***

## Tolimesni veiksmai

* [Apdorojimo srautas](processing-pipeline.md) — 4 sriegių srauto architektūros supratimas
* [„NVIDIA Jetson“ vadovas](../linux/nvidia-jetson-guide.md) — „Jetson“ specifinis diegimas ir optimizavimas
* [CLI : Komandinė eilutė](../CLI.md) — Išsamus CLI žinynas
