---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Atsisiųsti

Atsisiųskite naujausią „Chloros“ versiją ir pradėkite dirbti su daugiaspektrinių vaizdų apdorojimu.

### Sistemos reikalavimai

#### Windows

| Reikalavimas          | Minimalūs                                              | Rekomenduojami                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Operacinė sistema** | Windows 10 (64 bitų)                                  | Windows 11 (64 bitų)                                  |
| **Procesorius**        | Intel Core i5 arba lygiavertis                          | Intel Core i7 arba geresnis                              |
| **Atmintis (RAM)**     | 8 GB                                                  | 16 GB ar daugiau                                         |
| **Vaizdo plokštė**    | Suderinama su DirectX 11                                | NVIDIA GPU su 4 GB ar daugiau VRAM                            |
| **Saugykla**          | 6 GB laisvos vietos                                       | SSD su 10 GB ar daugiau laisvos vietos                            |
| **Ekranas**          | 1920x1080                                            | 2560x1440 ar didesnė                                  |
| **Internetas**         | Reikalingas \[pasirinktinai] Chloros+ licencijos aktyvacijai | Reikalingas \[pasirinktinai] Chloros+ licencijos aktyvacijai |

#### Linux amd64 (x86\_64)

| Reikalavimas       | Minimalus                    | Rekomenduojamas               |
| ----------------- | -------------------------- | ------------------------- |
| **Distribucija**  | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+             |
| **Procesorius**     | x86\_64 (Intel/AMD)        | Intel Core i7 ar geresnis   |
| **Atmintis (RAM)**  | 8 GB                        | 16 GB ar daugiau              |
| **Vaizdo plokštė** | Nėra (apdorojama procesoriumi)      | NVIDIA GPU su 4 GB+ VRAM |
| **Saugykla**       | 2 GB laisvos vietos             | SSD su 10 GB+ laisvos vietos       |
| **Python**        | Python 3.7+ (skirta SDK)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Reikalavimas      | Minimalus                      | Rekomenduojamas                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platforma**     | NVIDIA Jetson su JetPack 6 | Jetson Orin NX 16 GB arba AGX Orin |
| **Atmintis (RAM)** | 8 GB (bendra GPU/CPU)         | 16 GB+ bendra                    |
| **Saugykla**      | 2 GB laisvos vietos               | NVMe SSD su 10 GB+ laisvos vietos        |
| **Python**       | Python 3.7+ (skirta SDK)        | Python 3.10+                    |

{% hint style="info" %}
**GPU pagreitinimas**: Chloros+ naudotojai, turintys NVIDIA GPU, gali naudoti CUDA pagreitinimą, kad apdorojimas būtų žymiai spartesnis. Tai veikia tiek su Windows (stacionarių kompiuterių GPU), tiek su Linux (stacionarių kompiuterių GPU ir NVIDIA Jetson). Chloros+ vartotojai taip pat gauna daugiasiūlį apdorojimą, užtikrinantį maksimalų greitį.
{% endhint %}

***

## Atsisiųskite Chloros

### Naujausia stabili versija (2026 m. kovo 23 d.): Versija 1.1.0

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Atsisiųskite Chloros skirtą Windows (.exe)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Atsisiųskite Chloros skirtą Linux amd64 (.deb)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Atsisiųskite Chloros skirtą Linux arm64 / Jetson (.deb)</a>

#### Windows diegimo programa (GUI + CLI + Backend)

* **Failo tipas**: .exe (Windows diegimo programa)**Diegimo žingsniai:**

1. Atsisiųskite aukščiau nurodytą .exe failą
2. Dvigubai spustelėkite diegimo programą, kad pradėtumėte diegimą
3. Sekite diegimo vedlio nurodymus
4. Pasirinkite diegimo katalogą (numatyta: `C:\Program Files\[USER]\Chloros\`)
5. Baigę diegimą, paleiskite Chloros arba Chloros CLI
6. Prisijunkite naudodami savo [MAPIR Cloud Chloros+ paskyrą](https://cloud.mapir.camera/pricing) (arba tęskite su nemokama versija)

{% hint style="success" %}
Diegimo programa automatiškai įtraukia `chloros-cli` į jūsų sistemos PATH, kad būtų galima naudotis iš komandinės eilutės.
{% endhint %}

#### Linux amd64 (.deb paketas — CLI + Backend)

* **Failo tipas**: .deb (Debian/Ubuntu paketas)
* **Architektūra**: x86\_64 (amd64)

```bash
sudo dpkg -i chloros-amd64.deb
chloros-cli --version  # Verify installation
```

#### Linux arm64 — NVIDIA Jetson (.deb paketas — CLI + Backend)

* **Failo tipas**: .deb (JetPack 6)
* **Architektūra**: aarch64 (arm64)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
chloros-cli --version  # Verify installation
```

Išsamias diegimo instrukcijas rasite [Linux diegimo](linux/linux-installation.md) skyriuje, o su „Jetson“ susijusias gaires — [„NVIDIA Jetson“ vadove](linux/nvidia-jetson-guide.md).

#### Python SDK (visos platformos)

```bash
pip install chloros-sdk
```

Dokumentaciją rasite [API : Python SDK](api-python-sdk.md).

{% hint style="info" %}
**Linux vartotojai**: `.deb` paketas įdiegia CLI ir backend. Python SDK įdiegiamas atskirai per pip. Linux neturi grafinės vartotojo sąsajos — visa sąveika vyksta per CLI arba SDK.
{% endhint %}

***

## Papildomi ištekliai

### Python SDK

Kūrėjams ir automatizavimo darbo srautams įdiekite Chloros Python SDK:

```bash
pip install chloros-sdk
```

**Dokumentacija**: [API: Python SDK](api-python-sdk.md)**Reikalavimai**: turi būti įdiegta Chloros (Windows diegimo programa arba Linux `.deb` paketas), reikalingas Chloros+ licencijos prisijungimas***

## Kas įtraukta

### Windows diegimo programa

* ✅ **Chloros GUI** – visapusiška grafinė sąsaja
* ✅ **Chloros CLI** – Komandinės eilutės sąsaja (reikalinga Chloros+ licencija)
* ✅ **Chloros Backend** – Apdorojimo variklis
* ✅ **Kamerų profiliai** – iš anksto sukonfigūruoti MAPIR kamerų šablonai

### Linux .deb paketas

* ✅ **Chloros CLI** – Komandinės eilutės sąsaja (reikalinga Chloros+ licencija)
* ✅ **Chloros Backend** – apdorojimo variklis
* ✅ **Kamerų profiliai** – iš anksto sukonfigūruoti MAPIR kamerų šablonai
* ❌ Nėra GUI — Linux yra tik be grafinės sąsajos CLI/SDK

### Python SDK (pip, visos platformos)

* ✅ **Chloros SDK** - Python API (reikalinga Chloros+ licencija)***

## Atnaujinkite į Chloros+

Atrakinkite išplėstines funkcijas su Chloros+ prenumerata:

* 🚀 **Daugiagijis apdorojimas** – apdorokite vaizdus lygiagrečiai
* ⚡ **GPU (CUDA) pagreitinimas** – išnaudokite NVIDIA GPU galią
* 💻 **CLI prieiga** – automatizuokite naudodami komandinės eilutės įrankius
* 🐍 **Python SDK** – programinė API prieiga
* 📱 **Keli įrenginiai** – Naudokite 2–10 ir daugiau įrenginių (priklausomai nuo plano)
* **🐻 Išplėstinis tekstūrą atpažįstantis debayerio metodas** – aukštos kokybės kraštus atpažįstantis debayeris, suderintas su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayerio triukšmą.
* 🧮 **Pasirinktinės formulės** – Sukurkite pasirinktinius multispektrinius indeksus

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Peržiūrėti Chloros+ planus ir kainas</a></p>***

## Pagalba diegiant

### Trikčių šalinimas

**Diegimas nepavyksta, rodomas klaidos pranešimas:**

* Įsitikinkite, kad turite administratoriaus teises
* Laikinai išjunkite antivirusinę programinę įrangą
* Patikrinkite, ar atitinkate minimalius sistemos reikalavimus

**Programa nepaleidžiama (Windows):**

* Patikrinkite, ar įdiegta Windows 10/11 (64 bitų)
* Atnaujinkite grafikos tvarkykles
* Patikrinkite Windows įvykių peržiūrą, kad sužinotumėte klaidos detales
* Susisiekite su palaikymo tarnyba ir pateikite klaidų žurnalus

**CLI nepaleidžiama (Linux):**

* Patikrinkite, ar `.deb` paketas įdiegtas teisingai: `dpkg -l | grep chloros`
* Patikrinkite leidimus: `sudo chmod +x /usr/bin/chloros-cli`
* Vykdykite diagnostiką: `chloros-cli selftest`
* Patikrinkite, ar netrūksta bibliotekų: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Licencijos aktyvavimo problemos:**

* Įsitikinkite, kad interneto ryšys veikia
* Patikrinkite prisijungimo duomenis [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Patikrinkite, ar ugniasienė neblokuoja Chloros
* Išsamias instrukcijas rasite [Chloros+ Prisijungimas](chloros+-login.md)

### Pagalba

Reikia pagalbos diegiant ar konfigūruojant?

* 📧 **El. paštas**: info@mapir.camera
* 🌐 **Svetainė**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentacija**: [Pradžios vadovas](./)
* ❓ **DUK**: [Dažnai užduodami klausimai](faq.md)***

## Keitimų žurnalas

<details>

<summary>Versija 1.1.0 (Naujausia)</summary>

**Išleidimo data: 2026 m. kovo mėn.**

**Naujos funkcijos*** **Linux palaikymas** — Natūralus CLI ir SDK palaikymas Linux amd64 (x86\_64) ir arm64 (NVIDIA Jetson JetPack 6) platformoms. Įdiekite per `.deb` paketus.
* **NVIDIA Jetson palaikymas** — Optimizuotas apdorojimas Jetson Nano, Orin Nano, Orin NX ir AGX Orin kraštinių įrenginiams.
* **Dinaminis skaičiavimo pritaikymas** — Automatinis aparatinės įrangos aptikimas ir apdorojimo strategijos optimizavimas. Chloros prisitaiko prie jūsų aparatinės įrangos nuo „Jetson Nano“ iki daugiaprocesorės darbo stoties.
* **4 sriegių apdorojimo kanalas** — Vienu metu veikiantys aptikimo, kalibravimo, apdorojimo ir eksporto sriegiai su dinamišku GPU atminties paskirstymu.
* **Naujos CLI komandos** — `selftest` (sistemos diagnostika) ir `update` (Linux atnaujinimų valdymas).
* **Nauji CLI apdorojimo žymekliai** — `--debayer` (standartinis/atsižvelgiantis į tekstūrą), `--indices` (nurodyti indeksus), `--target` (pirmiausia ieškoti tikslinėje pakatalogėje, kad aptikimas būtų greitesnis).
* **Nauji GUI meniu elementai** — „Pridėti failus“, „Pridėti aplanką“ ir „Pradėti/Sustabdyti apdorojimą“ dabar pasiekiami iš pagrindinio meniu išskleidžiamojo sąrašo.**Patobulinimai**

* Tarpplatforminio užkulisio automatinis aptikimas (Windows ir Linux keliai)
* Patobulintas SDK `get_status()` su pažangos stebėjimu pagal kiekvieną srautą
* Naujos SDK išimtys: `ChlorosConfigurationError`, `ChlorosAuthenticationError`
* Šilumos valdymas ir prisitaikantis greičio ribojimas NVIDIA Jetson
* Automatinis atminties valdymas su OOM atsarginiu perėjimu prie mozaikinio GPU apdorojimo

</details>

<details>

<summary>Versija 1.0.5</summary>

**Išleidimo data: 2026 m. vasario 10 d.**

**Naujos funkcijos*** **Tekstūrą atpažįstantis debayerio metodas \[tik Chloros+] -** Tekstūrą atpažįstantis metodas naudoja aukštos kokybės kraštų atpažįstantį debayerį, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayerio triukšmą.
* **T4P kalibravimo tikslų palaikymas*** **Greitesnis Chloros+ GPU apdorojimas, geresnis atminties valdymas**

**Klaidų taisymai*** Visiškai nauja vartotojo sąsaja (GUI), dabar turėtų veikti visuose Windows kompiuteriuose.

</details>

<details>

<summary>Versija 1.0.4</summary>

**Išleidimo data: 2026 m. sausio 5 d.**

**Naujos funkcijos*** **Vaizdo/metaduomenų perjungimas**: Failų naršyklėje pridėtas perjungimas, leidžiantis peržiūrėti pasirinktų vaizdų metaduomenis lentelėje, o ne vaizdų tinklelyje
* **Vaizdų tinklelio mastelio slankiklis**: Naujas vartotojo sąsajos slankiklis, skirtas miniatiūrų dydžiui reguliuoti (taip pat palaiko CTRL + pelės ratuką)
* **Vaizdų tinklelio eksporto mygtukai**: Mygtukai viršutinėje eilutėje, skirti perjungti miniatiūras iš JPG į apdorotus eksportus (Tikslai, Atspindys, Indeksas, LUT)
* **Žemėlapio skirtukas**: naujas interaktyvus 2D žemėlapis, rodantis vaizdo GPS vietos žymes
  * Palaiko „Google Maps“ ir ESRI žemėlapio plyteles (automatiškai pasirenka geriausią plytelių paslaugą pagal mastelio lygio prieinamumą)
  * Pelės žymeklį užvedus ant žemėlapio žymių, rodomas miniatiūrų peržiūros langas

**Klaidų taisymai*** Pagerintas „Chloros“ įdiegimo palaikymas kompiuteriuose, kuriuose naudojama ne anglų kalba

</details>

<details>

<summary>Versija 1.0.3</summary>

**Išleidimo data: 2025 m. gruodžio 20 d.**

**Naujos funkcijos*** Pirmasis paleidimas

**Patobulinimai*** Pirmasis paleidimas

**Klaidų taisymai*** Pirmasis paleidimas

**Žinomos problemos*** Pirmasis paleidimas

</details>***

## Licencinė sutartis**Nuosavybinė programinė įranga** – Autorinės teisės (c) 2026 MAPIR Inc.

Draudžiama neteisėtai naudoti, platinti ar keisti.

**Nemokama versija**: Skirta asmeniniam ir komerciniam naudojimui su funkcionalumo apribojimais**Chloros+**: Licencija, pagrįsta prenumerata, skirta išplėstinėms funkcijoms ir komerciniam diegimui
