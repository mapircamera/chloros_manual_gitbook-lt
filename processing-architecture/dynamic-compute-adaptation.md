# Dinaminis skaičiavimo pritaikymas

Chloros 1.3.0 naudoja aparatinės įrangos atpažinimą ir automatinį apdorojimo strategijos pasirinkimą. Apdorojimo variklis prisitaiko prie jūsų aparatinės įrangos – nuo „Jetson Orin Nano“ iki darbo stoties su keliais GPU – be jokio rankinio konfigūravimo.

***

## Kaip tai veikia

Paleidus Chloros, programa atlieka jūsų sistemos profiliavimą:

1. **Aptinka operacinę sistemą** — Windows arba Linux
2. **Nustato procesoriaus branduolius ir bendrą RAM atminties kiekį**

3.**Aptinka GPU buvimą** — „NVIDIA CUDA“ palaikymą, VRAM, modelį
4. **Nustato „Jetson“ modelį** (jei taikoma) — per „`/proc/device-tree/model`“
5. **Patikrina temperatūros jutiklius** (Jetson) — apdorojimui atsižvelgiant į temperatūrą
6. **Pasirenka skaičiavimo strategiją** — remiantis visa aptikta aparatine įranga
7. **Automatiškai konfigūruoja darbininkų skaičių, apdorojimo grandinės tipą ir atminties paskirstymą**

Aptiktas profilis sesijos metu išsaugomas atmintyje ir diske, todėl vėlesni paleidimai prasideda greičiau:

| Platforma | Išsaugotas profilis |
| --- | --- |
| **Linux / „Jetson“** | `~/.config/chloros/system_config.json` (atsižvelgia į `XDG_CONFIG_HOME`) |
| **Windows** | `%LOCALAPPDATA%\Chloros\config\system_config.json` |

Ištrinkite tą failą, kad priverstumėte sistemą atlikti naują aptikimą — tai naudinga po GPU arba papildomos RAM atminties įdiegimo. Chloros taip pat automatiškai atlieka pakartotinį aptikimą, jei talpyklą užrašė nesuderinama senesnė versija.

***

## Skaičiavimo strategijos

Chloros pasirenka vieną iš trijų skaičiavimo strategijų, atsižvelgdamas į jūsų aparatinę įrangą:

| Strategija | Pasirenkama, kai | Darbininkai | Vykdytojas | Konvejeris |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`**| CUDA GPU, pranešantis apie**12 GB+ VRAM**(naudojant „Jetson“ suvienodintą atmintį, taip pat reikalinga 12 GB+ bendros dalinamosios RAM) | `min(4, ⌊(VRAM − 0.5GB) × 0.9 ÷ 3.5GB⌋)`, mažiausiai 2 —**ribojama iki 2 „Jetson“** | Darbininkų procesai (atskiri CUDA kontekstai) | `fused_gpu` |
| **`GPU_SINGLE`**| CUDA GPU su**2–12 GB VRAM**| 3 (įvesties/išvesties sutapimas; prieiga prie GPU serijizuojama semaforo pagalba).**1 (sekcinis) „Jetson“ įrenginiuose su mažiau nei 12 GB RAM** | Darbiniai procesai (atskiri CUDA kontekstai); sekcinis procesas įrenginiuose su mažai RAM | `fused_gpu` / `tiled_gpu` |
| **`CPU_PARALLEL`** | Be CUDA GPU arba su mažiau nei 2 GB VRAM | `max(2, physical cores − 1)` | `ThreadPoolExecutor` | `cpu_fallback` |

`GPU_PARALLEL` darbininko formulės pavyzdžiai: 12 GB VRAM → 2 darbininkai, 16 GB → 3 darbininkai, 20 GB ir daugiau → 4 darbininkai. Kiekvienam darbininkui skiriama 3,5 GB – tai išmatuotas „Texture Aware“ užduoties poreikis, neviršijantis 90 % VRAM, likusios po 0,5 GB rezervo.

{% hint style="info" %}
**GPU strategijoms reikalingas „PyTorch“, galintis naudoti CUDA.** Kompiuteris, kurio „PyTorch“ neturi tinkamo CUDA įrenginio, gauna `CPU_PARALLEL`, nepriklausomai nuo jo GPU. „Jetson“ 1.3.0 paketas naudoja „JetPack 6“ CUDA versiją „PyTorch“, todėl „Jetson“ atitinka reikalavimus, kai tik įdiegiamos „JetPack“ CUDA ir „cuDNN“ bibliotekos. „Jetson“ su mažiau nei 12 GB RAM visada apdoroja po vieną vaizdą vienu metu, vykdymo metu (užregistruojama kaip `GPU_SINGLE`).
{% endhint %}

GPU strategijos vykdo darbininkų procesų grupę, kur kiekvienas procesas yra atskiras operacinės sistemos procesas su savo „CUDA“ kontekstu, kurį Chloros paleidžia vėl paleisdamas savo pačios įdiegtą užkulisinę programą (ne per „Python “ `multiprocessing`, kurio darbininkų paleidimas neveikia paketo viduje). CPU strategija naudoja siūlų grupę (Python „`ThreadPoolExecutor`“). Chloros nenaudoja jokių trečiųjų šalių paskirstytųjų sistemų (pavyzdžiui, „Ray“).

### Apdorojimo grandinių tipai

* **`fused_gpu`** — Visiškai GPU pagrįstas apdorojimo kelias. „Debayer“, korekcijos ir indeksavimo operacijos atliekamos GPU per vieną sujungtą ciklą. Didžiausias pralaidumas, reikalauja daugiausia VRAM.
* **`tiled_gpu`** — Atminties tausojantis GPU apdorojimo kelias. Vaizdus apdoroja dalimis, kad tilptų į ribotą GPU atmintį. Mažesnis pralaidumas, tačiau veikia įrenginiuose su ribota atmintimi.
* **`cpu_fallback`** — Apdorojimas tik CPU, naudojant daugiasiūlį lygiagretumą. Naudojamas, kai nėra prieinamo „NVIDIA“ GPU, ir kaip paskutinė išeitis, kai abu GPU apdorojimo keliai nepavyksta.

Vykdymo metu taikoma atsarginė grandinė visada yra tokia: `fused_gpu` → `tiled_gpu` → `cpu_fallback`.

***

## Rankinis strategijos perrašymas

Nustatykite aplinkos kintamąjį `CHLOROS_STRATEGY`, kad priverstinai taikytumėte konkrečią strategiją — tai ekspertų išeitis, kai automatinis nustatymas pasirenka jūsų situacijai netinkamą variantą (pavyzdžiui, norint palikti GPU laisvą kitiems darbams):

```bash
# Valid values: CPU_PARALLEL, GPU_SINGLE, GPU_PARALLEL
CHLOROS_STRATEGY=CPU_PARALLEL chloros-cli process ~/datasets/flight001
```

Kintamasis atpažįstamas neatsižvelgiant į didžiųjų ir mažųjų raidžių skirtumą; viskas, kas nėra vienas iš trijų pavadinimų, ignoruojama, o automatinis nustatymas vyksta įprastai. Perrašymo atveju Chloros vis tiek parenka darbininkų skaičių už jus:

| Perrašymas | Naudojamas darbininkų skaičius |
| --- | --- |
| `CPU_PARALLEL` | `max(2, physical cores − 1)` |
| `GPU_SINGLE` | 3 |
| `GPU_PARALLEL` | `min(4, physical cores)` |

Pakeitimas „`GPU_SINGLE`“ arba „`GPU_PARALLEL`“ kompiuteryje, kuriame nėra tinkamo CUDA įrenginio, atmetamas, o žurnale atsiranda eilutė „`[STRATEGY]`“, o vykdymui naudojamas „`CPU_PARALLEL`“.

Geriau nustatyti šį parametrą kiekvienam komandos vykdymui atskirai, o ne nuolat, kad įprasti vykdymai ir toliau automatiškai prisitaikytų.

***

## Platformos specifinis elgesys

| Platforma | Strategija | Darbininkai | Konvejeris | Pastabos |
| --- | --- | --- | --- | --- |
| **Jetson Orin Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sekcinis) | Atminties taupymo režimas, po vieną vaizdą |
| **Jetson Orin NX 8 GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sekcinis) | Mažiau nei 12 GB bendros RAM priverčia apdoroti sekciniu būdu |
| **Jetson Orin NX 16 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (lygiagretus) | Rekomenduojamas krašto įrenginys — „Jetson“ apribotas iki 2 darbininkų |
| **„Jetson AGX Orin“ 32–64 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (lygiagretus) | Maksimalus krašto įrenginio našumas (taip pat „Jetson“ apribotas iki 2 darbininkų) |
| **Stalinis kompiuteris su 8 GB GPU** | `GPU_SINGLE` | 3 | `fused_gpu` / `tiled_gpu` | 3 darbininkai persidengia įvesties/išvesties operacijose, o semaforas serijina prieigą prie GPU |
| **Stacionarus kompiuteris su 12 GB ir daugiau GPU** | `GPU_PARALLEL` | 2–4 | `fused_gpu` (vienu metu) | Optimalus stalinio kompiuterio našumas: 12 GB → 2 darbininkai, 16 GB → 3, 20 GB ir daugiau → 4 |
| **Tik CPU sistema** | `CPU_PARALLEL` | fiziniai branduoliai − 1 (min. 2) | `cpu_fallback` | GPU nereikalingas, naudoja siūlų rezervą |

{% hint style="info" %}
**„Jetson“ suvienodinta atmintis**: „Jetson“ įrenginiai dalijasi GPU ir CPU atmintimi. „Jetson Orin NX“ su 16 GB atminties rodo ~15,3 GB VRAM, tačiau tai yra ta pati fizinė RAM, kurią naudoja operacinė sistema ir CPU procesai. Būtent todėl „Jetson“ įrenginiai su 16 GB ir daugiau atitinka `GPU_PARALLEL` reikalavimus kaip ir staliniai kompiuteriai su 12 GB ir daugiau GPU, tačiau jiems taikomas 2 darbininkų apribojimas — GPU, darbininkų procesai ir jų CUDA kontekstai kiekvienam darbininkui naudoja tą patį bendrą rezervą.
{% endhint %}

### GPU biudžetas pagal VRAM (diskretiški GPU)

„x86_64“ kompiuteriuose su diskretišku „NVIDIA“ GPU aptikta VRAM taip pat nustato, kiek plokštė gali pasisavinti apdorojimo išteklių ir kokio dydžio gali būti partijos:

| Aptikta VRAM | GPU biudžeto riba | Partijos dydžio daugiklis |
| --- | --- | --- |
| **8 GB ir daugiau** | 90 % | ×2,0 |
| **6–8 GB** | 85 % | ×1,75 |
| **3,5–6 GB** | 80 % | ×1,5 |
| **2–3,5 GB** | 75 % | ×1,25 |
| **Mažiau nei 2 GB** | 70 % | ×1,0 |

Diskretiški GPU sistemai rezervuoja tik 0,5 GB, nes jie nesidalija sistemos RAM. „Jetson“ profiliai rezervuoja žymiai daugiau ir nustato mažesnę ribą — žr. [„NVIDIA Jetson“ vadovą](../linux/nvidia-jetson-guide.md#per-model-gpu-budget).

***

## Dinaminis GPU atminties paskirstymas

Chloros naudoja [4 sriegių apdorojimo grandinę](processing-pipeline.md):

* **1-asis sriegis** (aptikimas) — vaizdo įkėlimas, EXIF duomenų analizė, objekto aptikimas
* **2-asis sriegis** (kalibravimas) — atspindžio kalibravimo skaičiavimai
* **3-iasis srautas** (apdorojimas) — GPU „debayer“, vinjetės korekcija, indekso skaičiavimas
* **4-asis srautas** (eksportavimas) — failų rašymas, metaduomenų įterpimas

1-asis, 2-asis ir 4-asis srautai naudoja nedaug GPU išteklių; 3-iasis srautas naudoja daug išteklių. Kai ankstesni proceso srautai baigia darbą, jų GPU ištekliai **perskirstomi likusiems aktyviems srautams**, todėl 3-iasis srautas, procesui progresuojant, gauna vis daugiau atminties.

### Paskirstymo etapai

| Etapas | Aktyvūs srautai | GPU atminties paskirstymas |
| --- | --- | --- |
| **Ankstyvasis** | 1, 2, 3, 4 | Paskirstoma visiems srautams, didžioji dalis tenka 3-iajam srautui |
| **Vidurinis-ankstyvasis** | 2, 3, 4 | 1-ojo srauto dalis perskirstoma |
| **Vidurinis-vėlyvasis** | 3, 4 | 1 ir 2 sriegių dalys atitenka 3 ir 4 |
| **Vėlyvasis** | 3 arba 4 | Paskutinis aktyvus sriegis gauna maksimalų paskirstymą |

Šiuos skaičius reglamentuoja dvi taisyklės:

* Sriegiui, kuris yra **vienintelis** aktyvus, skiriamas jo profilio maksimalus paskirstymas.
* Kai aktyvi yra daugiau nei viena *didelės apkrovos* GPU užduotis, kiekvienos didelės apkrovos užduoties bazinis paskirstymas padalijamas tarp jų (niekada nesumažinant žemiau nustatyto minimumo).

Vykdymo metu faktiškai naudojama vertė yra **mažesnė** iš platformos profilio paskirstymo ir GPU atminties monitoriaus realaus laiko rekomendacijos, taigi užimta plokštė visada laimi prieš optimistinį profilį.***

## Tekstūrą atpažįstantis apdorojimas

„Texture Aware“ debayeris (**tik „Chloros+“** — `--debayer texture-aware`) naudoja AI/ML triukšmo šalinimo modelį, kuriam vienam kopijavimui reikia maždaug 1,75 GB VRAM FP16 formatu, todėl jis naudoja žymiai daugiau GPU atminties nei standartinis metodas:

* Sistemos su **mažiau nei 7 GB VRAM**apdoroja „Texture Aware“**sinchronine kilpa, po vieną vaizdą** — keli modelio kopijos netelpa, o darbininkų grupė tik padidintų konkurenciją
* Sistemos su **7 GB ir daugiau VRAM** gali apdoroti „Texture Aware“ lygiagrečiai, nors ir su mažesniu darbininkų skaičiumi, palyginti su „Standard“
* **„Jetson“** sistemose „Texture Aware“ visada priskiriamas vienam darbininkui
* Jei **nėra tinkamo CUDA GPU**, vykdymo metu naudojamas standartinis „debayer“, o užkulisiai užregistruoja vieną eilutę „`[DEBAYER] … needs a CUDA GPU …`“, paaiškinančią priežastį
* Mažos galios „Jetson“ įrenginiuose („Nano“, „Orin Nano“) CLI, kai prašoma naudoti „Texture Aware“, taiko GPU dažnio apribojimą — žr. [„NVIDIA Jetson“ vadovą](../linux/nvidia-jetson-guide.md#gpu-frequency-cap-for-texture-aware-on-nano-and-orin-nano)

***

## Šilumos valdymas („Jetson“)

„Jetson“ įrenginiai turi šiluminius apribojimus, ypač uždarose arba ore esančiose diegimo vietose. Chloros stebi „Jetson“ integruotus temperatūros jutiklius ir automatiškai reguliuoja partijų dydžius:

| Temperatūra | Reakcija |
| --- | --- |
| **< 70 °C** | Įprastas veikimas — pilnas greitis |
| **70 °C** (Įspėjimas) | Partijos dydis palaipsniui mažinamas (nuo 100 % iki 50 % esant temperatūrai nuo 70 °C iki 80 °C) |
| **80 °C** (Kritinis) | Agresyvus našumo ribojimas (nuo 50 % iki 0 % temperatūrai svyruojant nuo 80 °C iki 90 °C) |
| **90 °C** (Išjungimas) | Visiškai sustabdomas GPU apdorojimas |

Stacionariuose kompiuteriuose su pakankamu aušinimu terminis greičio ribojimas retai kada įsijungia.

***

## Atminties apkrovos valdymas

Chloros apdorojimo metu nuolat stebi GPU atmintį ir reaguoja trimis lygiais.

**Partijos dydžio nustatymas.** Partija prasideda nuo 8 vaizdų, padaugintų iš platformos daugiklio, nurodyto aukščiau pateiktose lentelėse. Tada Chloros patikrina laisvą VRAM, 20 % jos rezervuoja „PyTorch“ vidinėms reikmėms ir numato maždaug 100 MB GPU atminties vienam 12 MP vaizdui — partijos dydis yra mažesnis iš dviejų: atminties nustatytas limitas arba platformos bazinis dydis. Jis niekada nesumažėja žemiau 1.

**Prevencinis mažinimas.**Viršijus**85 % VRAM panaudojimą**, partijų dydžiai sumažinami dar prieš įvykstant bet kokiam gedimui.**Paskirstymo mažinimas pagal kiekvieną srautą.** Didėjant realiajam panaudojimui, kiekvieno srauto GPU biudžetas mažinamas: ×0,75, kai panaudojimas viršija 80 %, ×0,5 – kai viršija 90 %. Stebėjimo ribos yra 70 % (konservatyvi), 85 % (įprasta veikimo riba) ir 95 % (OOM rizika).**OOM atsitraukimas ir atkūrimas.** Jei vis dėlto įvyksta atminties trūkumo įvykis:

* partijos dydis **sumažinamas perpus**, o po kiekvieno iš eilės pasikartojančio atminties trūkumo – dar perpus; kiekviena vėlesnė sėkmingai įvykdyta partija šią baudą sumažina vienu laipsniu
* aktyvių sriegių paskirstymai sumažinami iki 70 % jų dabartinės vertės, o paskirstytojas pereina prie konservatyvios strategijos, kurią vėl sušvelnina po sėkmingų paskirstymų serijos
* esant dideliam apkrovimui, apdorojimo grandinė pereina iš `fused_gpu` į `tiled_gpu`, o kraštutiniu atveju – į `cpu_fallback`

**Pagrindinio kompiuterio RAM (Jetson).** Prieš apdorojimą CLI, remdamasi vaizdų skaičiumi ir „debayer“ režimu, įvertina didžiausią priimančiojo kompiuterio atminties poreikį ir įspėja, jei RAM bei failais pagrįsta keitimo atmintis gali būti nepakankama, pateikdama tikslias komandas keitimo atminties pridėjimui — žr. [„NVIDIA Jetson“ vadovą](../linux/nvidia-jetson-guide.md#swap-warning-and-recommendations).

***

## Skaičiavimo prisitaikymo stebėjimas

### Sistemos diagnostika

`chloros-cli selftest` yra greičiausias būdas patikrinti, ką mato skaičiavimo sluoksnis:

```bash
chloros-cli selftest
```

Jo 7 patikrinimai apima versiją, prievadų prieinamumą, užpakalinės dalies paleidimą, `/api/test`, sistemos informaciją, triukšmo šalinimo modelio buvimą ir CUDA bei triukšmo šalinimo parengtį. 5-asis patikrinimas tiesiogiai išveda aparatinės įrangos eilutę:

```
      GPU: NVIDIA RTX A4000, CUDA: True, PyTorch: 2.7.0
```

7-asis patikrinimas išspausdina „`CUDA: <bool>, Denoiser: <bool>`“ — abu turi būti teisingi, kad „Texture Aware“ apskritai būtų galima naudoti.

### Užkulisio žurnalai

Strategija ir darbininkų skaičius pasirenkami vidiniame modulyje kiekvieno vykdymo pradžioje — nėra jokio CLI-pranešimo, kuris juos skelbtų. Kai kas nors elgiasi netikėtai (GPU kelio grįžimas prie atsarginio varianto, atminties trūkumas (OOM), triukšmo šalinimo modulis, kuris neįsikrauna), tai atsispindi tos sesijos vidinio modulio žurnale:

| Platforma | Žurnalų vieta |
| --- | --- |
| **Linux / „Jetson“** | `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` (vienas failas kiekvienam paleidimui) |
| **Linux, per CLI paleistas backend** | taip pat `~/.chloros/backend.log` |
| **Windows** | `%LOCALAPPDATA%\Chloros\logs\` |

### Vykdymo eiga realiuoju laiku

Vykdymo metu CLI rodo realaus laiko pažangą pagal kiekvieną srautą (aptikimas, analizė, apdorojimas, eksportavimas), perduodamą per „Server-Sent Events“ — tai praktiškas būdas įvertinti, ar 3-iasis srautas yra kliūtis. Žr. [Apdorojimo srautas](processing-pipeline.md).

***

## Tolimesni veiksmai

* [Apdorojimo srautas](processing-pipeline.md) — 4 siūlų srauto architektūros supratimas
* [„NVIDIA Jetson“ vadovas](../linux/nvidia-jetson-guide.md) — „Jetson“ specifinis diegimas ir optimizavimas
* [CLI: Komandinė eilutė](../CLI.md) — CLI vadovas
* [CLI nuoroda](../reference/cli-reference.md) — Išsamus 1.3.0 versijos komandų sąrašas
