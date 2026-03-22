# Apdorojimo grandinė

Chloros 1.1.0 naudoja 4 sriegių apdorojimo grandinę, veikiančią kaip etapinė surinkimo linija. Kiekvienas sriegis tvarko atskirą apdorojimo darbo eigos etapą, todėl vienu metu galima apdoroti kelis vaizdus skirtinguose etapuose.

***

## Grandinės architektūra

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Kiekvienas vaizdas eina per visus keturis sriegius eilės tvarka. Naudojant Chloros+ daugiasiūlį apdorojimą, keli vaizdai gali būti skirtinguose sriegiuose vienu metu — kol 3-iasis sriegis apdoroja vieną vaizdą, 1-asis sriegis gali aptikti kitą, 2-asis sriegis gali kalibruoti dar vieną, o 4-asis sriegis gali įrašyti anksčiau apdorotą vaizdą į diską.

***

## Sriegių detalės

### 1-asis srautas: aptikimas

**Tikslas**: įkelti vaizdus ir aptikti kalibravimo taškus.

* Skaito vaizdo failus iš disko (RAW, JPG)
* Išskiria EXIF metaduomenis (GPS, fotoaparato modelis, laiko žymos, ekspozicija)
* Aptinka ArUco kalibravimo taškus pažymėtuose vaizduose
* Rezultatai: vaizdo duomenys + metaduomenys + taškų aptikimo rezultatai

Tai pirmiausia įvesties/išvesties ir procesoriaus našumą ribojantis srautas.

### 2-asis srautas: Kalibravimas

**Tikslas**: Apskaičiuoti kalibravimo parametrus iš aptiktų taškų.

* Apskaičiuoja atspindžio kalibravimo koeficientus iš taškų vaizdų
* Apskaičiuoja vinjetės korekcijos parametrus
* Nustato kalibravimo kreives kiekvienam juostos dažniui
* Rezultatai: kalibravimo parametrai kiekvienam vaizdui

Tai yra su procesoriaus našumu susijęs skaičiavimo procesas.

### 3-iasis procesas: Apdorojimas (GPU)

**Tikslas**: Taikyti korekcijas ir apskaičiuoti augmenijos indeksus.**Tai yra labiausiai skaičiavimų reikalaujantis procesas.*** **Debayering**: konvertuoja RAW Bayer modelio duomenis į daugiakanalius vaizdus
  * Standartinis (greitas, vidutinės kokybės) — numatytasis
  * Tekstūros atpažinimas (lėtas, aukščiausios kokybės) — tik Chloros+, naudoja AI/ML triukšmo šalinimą
* **Vignette korekcija**: taiko objektyvo vignette korekciją visame vaizde
* **Atstumo kalibravimas**: Taiko kalibravimo koeficientus, kad konvertuotų į atstumo vertes
* **Indekso skaičiavimas**: Apskaičiuoja augmenijos indeksus (NDVI, NDRE, GNDVI ir kt.)
* Rezultatai: apdoroti vaizdo duomenys, paruošti eksportui

Šis procesas labiausiai naudoja GPU pagreitinimą. [Dinaminio skaičiavimo pritaikymo](dynamic-compute-adaptation.md) sistema pirmiausia optimizuoja šio proceso veikimą.

### 4-asis srautas: Eksportavimas

**Tikslas**: Įrašyti apdorotus vaizdus į diską.

* Įrašo išvesties failus pasirinktu formatu (TIFF 16 bitų, TIFF 32 bitų %, PNG, JPG)
* Įterpia EXIF metaduomenis į išvesties failus (GPS, laiko žymes, apdorojimo parametrus)
* Suskirsto išvestį į fotoaparato modelio pakatalogius
* Išvestis: galutiniai failai diske

Tai pirmiausia yra su įvesties/išvesties operacijomis susijęs srautas. SSD saugykla žymiai pagerina 4-ojo srauto našumą.

***

## Sekcinis ir konvejerinis apdorojimas

### Nemokamas režimas (sekcinis)

Nemokamoje Chloros versijoje vaizdai apdorojami **po vieną**, nuosekliai per visus keturis etapus:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI pažangos juosta rodo 2 etapus: tikslo aptikimą ir apdorojimą.

### Chloros+ režimas (konvejerinis)

Turint Chloros+ licenciją, visi keturi srautai veikia **vienu metu** su skirtingais vaizdais:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI pažangos juosta rodo 4 etapus: aptikimą, analizę, kalibravimą, eksportavimą. Užveskite pelę ant pažangos juostos, kad pamatytumėte kiekvieno srauto pažangą.

{% hint style="success" %}
**Pipelininis apdorojimas su Chloros+** gali būti 3–5 kartus greitesnis nei nuoseklusis apdorojimas, priklausomai nuo jūsų aparatinės įrangos ir duomenų rinkinio dydžio. Didžiausias pagreitis pasiekiamas sistemose su greitais GPU ir SSD.
{% endhint %}

***

## 4-ojo srauto eksporto pažanga

Chloros 1.1.0 versijoje eksporto srautas (4-asis srautas) turi savo atskirą pažangos stebėjimo funkciją. Eksporto pažangą galite stebėti atskirai:**CLI:**
```bash
chloros-cli export-status
```

**SDK:**
```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

Apdorojimas baigtas, kai 4-asis srautas pasiekia 100 %.

***

## Ryšys su dinamine skaičiavimo adaptacija

[Dinaminio skaičiavimo pritaikymo](dynamic-compute-adaptation.md) sistema pirmiausia veikia **3-iąjį srautą (apdorojimą)**:

* **`GPU_PARALLEL`** strategija: 3-iasis srautas vienu metu apdoroja kelis vaizdus per GPU, naudodamas `fused_gpu` srautą
* **`GPU_SINGLE`** strategija: 3-iasis srautas apdoroja po vieną vaizdą, naudodamas atminties tausojantį `tiled_gpu` vamzdyną
* **`CPU_PARALLEL`** strategija: 3-iasis srautas naudoja CPU pagrįstą apdorojimą su daugiasiūliu lygiagretiškumu

3-iojo srauto GPU atminties paskirstymas taip pat dinamiškai keičiasi, kai 1-asis ir 2-asis srautai baigia darbą — žr. [Dinaminis GPU atminties paskirstymas](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Tolimesni veiksmai

* [Dinaminis skaičiavimo pritaikymas](dynamic-compute-adaptation.md) — Kaip Chloros pasirenka optimalią strategiją jūsų aparatinei įrangai
* [„NVIDIA Jetson“ vadovas](../linux/nvidia-jetson-guide.md) — Platformos specifinis srauto elgesys „Jetson“
* [Apdorojimo stebėjimas](../processing-images-gui/monitoring-the-processing.md) — GUI pažangos stebėjimas
