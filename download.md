---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Atsisiųsti

Atsisiųskite naujausią „Chloros“ versiją, kad galėtumėte pradėti dirbti su daugiaspektrinių vaizdų apdorojimu.

### Sistemos reikalavimai

#### Windows

| Reikalavimas          | Minimalūs                                              | Rekomenduojami                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Operacinė sistema** | Windows 10 (64 bitų)                                  | Windows 11 (64 bitų)                                  |
| **Procesorius**        | „Intel Core i5“ arba lygiavertis                          | „Intel Core i7“ arba galingesnis                              |
| **Atmintis (RAM)**     | 8 GB                                                  | 16 GB ar daugiau                                         |
| **Vaizdo plokštė**    | Suderinama su „DirectX 11“                                | „NVIDIA“ vaizdo plokštė su 4 GB ar daugiau VRAM                            |
| **Saugykla**          | 6 GB laisvos vietos                                       | SSD su 10 GB ar daugiau laisvos vietos                            |
| **Ekranas**          | 1920x1080                                            | 2560x1440 ar didesnė skiriamoji geba                                  |
| **Internetas**         | Reikalingas \[pasirinktinai] Chloros+ licencijos aktyvavimui | Reikalingas \[pasirinktinai] Chloros+ licencijos aktyvavimui |

#### Linux amd64 (x86_64)

| Reikalavimas       | Minimalūs                    | Rekomenduojami               |
| ----------------- | -------------------------- | ------------------------- |
| **Distribucija**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **Procesorius**     | x86\_64 (Intel/AMD)        | Intel Core i7 ar geresnis   |
| **Atmintis (RAM)**  | 8 GB                        | 16 GB ar daugiau              |
| **Vaizdo plokštė** | Nereikalinga (apdorojimas procesoriuje)      | NVIDIA vaizdo plokštė su 4 GB+ VRAM |
| **Saugykla**       | 2 GB laisvos vietos             | SSD su 10 GB+ laisvos vietos       |
| **Python**        | Python 3.7+ (skirta SDK)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Reikalavimas      | Minimalus                      | Rekomenduojamas                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platforma**     | „NVIDIA Jetson“ su „JetPack 6“ | „Jetson Orin NX“ 16 GB arba „AGX Orin“ |
| **Atmintis (RAM)** | 8 GB (bendra GPU/CPU)         | 16 GB+ bendra                    |
| **Saugykla**      | 2 GB laisvos vietos               | NVMe SSD su 10 GB+ laisvos vietos        |
| **Python**       | Python 3.7+ (skirta SDK)        | Python 3.10+                    |

{% hint style="info" %}
**GPU spartinimas**: „Chloros+“ naudotojai, turintys „NVIDIA“ GPU, gali naudoti „CUDA“ spartinimą, kad apdorojimas vyktų žymiai greičiau. Tai veikia tiek su Windows (stacionarių kompiuterių GPU), tiek su Linux (stacionarių kompiuterių GPU ir „NVIDIA Jetson“). „Chloros+“ vartotojai taip pat gauna daugiasiūlį apdorojimą, užtikrinantį maksimalų greitį.
{% endhint %}

***

## Atsisiųskite „Chloros“

### Naujausia stabili versija: 1.2.0

<!-- NOLAN: replace installer links + release date for 1.2.0 — the three download buttons below still point at the 1.1.0 Google Drive files, and the release date needs to be added to the heading above. -->



### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Atsisiųskite „Chloros“ skirtą „Windows“ (.exe)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Atsisiųskite Chloros, skirtą Linux amd64 (.deb)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Atsisiųskite Chloros, skirtą Linux arm64 / Jetson (.deb)</a>

#### Windows diegimo programa (GUI + CLI + Backend)

* **Failo tipas**: .exe (Windows diegimo programa)**Įdiegimo žingsniai:**

1. Atsisiųskite aukščiau nurodytą .exe failą
2. Dukart spustelėkite diegimo programą, kad pradėtumėte diegimą
3. Vykdykite diegimo vedlio nurodymus
4. Pasirinkite diegimo katalogą (numatyta: `C:\Program Files\MAPIR\Chloros\`)
5. Baigkite diegimą ir paleiskite „Chloros“ arba „Chloros“ „CLI“
6. Prisijunkite naudodami savo [„MAPIR Cloud Chloros+“ paskyrą](https://cloud.mapir.camera/pricing) (arba tęskite su nemokama versija)

{% hint style="success" %}
Diegimo programa automatiškai įtraukia `chloros-cli` į jūsų sistemos PATH aplinką, kad būtų galima naudotis komandinės eilutės funkcijomis.
{% endhint %}

#### Linux amd64 (.deb paketas — CLI + Backend)

* **Failo tipas**: .deb (Debian/Ubuntu paketas)
* **Architektūra**: x86_64 (amd64)

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

Išsamias diegimo instrukcijas rasite [Linux diegimo vadove](linux/linux-installation.md), o su „Jetson“ susijusius nurodymus — [„NVIDIA Jetson“ vadove](linux/nvidia-jetson-guide.md).

#### Python SDK (visos platformos)

Kiekvienoje diegimo programoje yra pridedamas atitinkamas „`chloros_sdk`“ ratas, todėl „SDK“ versija visada atitinka įdiegtą GUI/„CLI“/backend. Windows diegimo programa jį automatiškai įdiegia į jūsų sistemos Python; Linux versijoje `.deb` įdiegia „wheel“ į `/usr/lib/chloros/sdk/` ir išveda diegimo komandą:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Kompiuteriams, kuriuose veikia tik „pip“ (neįdiegtas Chloros paketas), SDK taip pat yra „PyPI“:

```bash
pip install chloros-sdk
```

Žr. [API : Python SDK](api-python-sdk.md) ir [SDK nuorodą](reference/sdk-reference.md), kur rasite dokumentaciją.

{% hint style="info" %}
**Linux vartotojai**: `.deb` paketas įdiegia CLI ir užkurtį. Linux neturi grafinės vartotojo sąsajos — visa sąveika vyksta per CLI arba SDK.
{% endhint %}

***

## Papildomi ištekliai

### Python SDK

Kūrėjams ir automatizavimo darbo srautams įdiekite „Chloros“, „Python“ ir „SDK“:

```bash
pip install chloros-sdk
```

**Dokumentacija**: [API: Python SDK](api-python-sdk.md)**Reikalavimai**: turi būti įdiegta „Chloros“ (įdiegimo programa „Windows“ arba paketas „Linux `.deb`“), reikalingas prisijungimas su „Chloros+“ licencija***

## Kas įtraukta

### „Windows“ diegimo programa

* ✅ **Chloros GUI** – visapusiška grafinė sąsaja
* ✅ **Chloros CLI** – komandinės eilutės sąsaja (reikalinga Chloros+ licencija)
* ✅ **Chloros Backend** – apdorojimo variklis
* ✅ **Kamerų profiliai** – iš anksto sukonfigūruoti MAPIR kamerų šablonai

### Linux .deb paketas

* ✅ **Chloros CLI** – Komandinės eilutės sąsaja (reikalinga Chloros+ licencija)
* ✅ **Chloros užkulisiai** – apdorojimo variklis
* ✅ **Kameros profiliai** – iš anksto sukonfigūruoti MAPIR kamerų šablonai
* ❌ Nėra grafinės vartotojo sąsajos — Linux yra tik be grafinės sąsajos CLI/SDK

### Python SDK (PIP, visos platformos)

* ✅ **Chloros, SDK** – Python, API (reikalinga Chloros+ licencija)***

## Atnaujinkite į Chloros+

Atrakinkite išplėstines funkcijas su Chloros+ prenumerata:

* 🚀 **Daugiasiūlis apdorojimas** – apdorokite vaizdus lygiagrečiai
* ⚡ **GPU (CUDA) pagreitinimas** – išnaudokite „NVIDIA“ GPU galią
* 💻 **Prieiga prie CLI** – automatizuokite naudodami komandinės eilutės įrankius
* 🐍 **Python SDK** – programinis API prieiga
* 📱 **Keli įrenginiai** – Naudokite 2–10 ir daugiau įrenginių (priklausomai nuo plano)
* **🐻 Išplėstinis tekstūrą atpažįstantis „debayer“ metodas** – aukštos kokybės, kraštus atpažįstantis „debayer“, derinamas su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą „debayer“ triukšmą.
* 🧮 **Pasirinktinės formulės** – kurkite pasirinktinius multispektrinius indeksus

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Peržiūrėkite „Chloros+“ planus ir kainas</a></p>***

## Pagalba diegiant

### Problemų sprendimas

**Diegimas nepavyksta, rodomas šis pranešimas apie klaidą:**

* Įsitikinkite, kad turite administratoriaus teises
* Laikinai išjunkite antivirusinę programinę įrangą
* Patikrinkite, ar atitinkate minimalius sistemos reikalavimus

**Programa nepaleidžiama (Windows):**

* Patikrinkite, ar įdiegta Windows 10/11 (64 bitų) versija
* Atnaujinkite grafikos tvarkykles
* Patikrinkite „Windows“ įvykių peržiūrą, kad sužinotumėte klaidos detales
* Susisiekite su technine pagalba ir pateikite klaidų žurnalus

**„CLI“ nepaleidžiama (Linux):**

* Patikrinkite, ar `.deb` paketas įdiegtas teisingai: `dpkg -l | grep chloros`
* Patikrinkite leidimus: `sudo chmod +x /usr/bin/chloros-cli`
* Vykdykite diagnostiką: `chloros-cli selftest`
* Patikrinkite, ar netrūksta bibliotekų: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Licencijos aktyvavimo problemos:**

* Įsitikinkite, kad interneto ryšys veikia
* Patikrinkite prisijungimo duomenis adresu [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Patikrinkite, ar ugniasienė neblokuoja Chloros
* Išsamias instrukcijas rasite [Chloros+ Prisijungimas](chloros+-login.md)

### Pagalba

Reikia pagalbos įdiegant ar konfigūruojant?

* 📧 **El. paštas**: info@mapir.camera
* 🌐 **Svetainė**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentacija**: [Pradžios vadovas](./)
* ❓ **DUK**: [Dažnai užduodami klausimai](faq.md)***

## Programinės įrangos atnaujinimai

Chloros tikrina, ar yra atnaujinimų, praneša, kai pasirodo nauja versija, ir pateikia nuorodą į šį atsisiuntimo puslapį — atnaujinate paleisdami naują pasirašytą diegimo programą. Jūsų nustatymai ir projektai išlieka po atnaujinimų. Naudojant „Linux“ ir „Jetson“, „`chloros-cli update`“ tikrina, ar yra naujesnė versija, ir siūlo atsisiųsti bei įdiegti atitinkamą „`.deb`“ (ši komanda skirta tik Linux versijai).

***

## Keitimų sąrašas**Versija 1.2.0 (naujausia)**— išsamų funkcijų sąrašą rasite skyriuje**Kas naujo Chloros 1.2.0 versijoje** [Pradžios](./) puslapyje.

<details>

<summary>Versija 1.0.5</summary>

**Išleidimo data: 2026 m. vasario 10 d.**

**Naujos funkcijos*** **Tekstūrą atpažįstantis debayerio metodas \[tik Chloros+] —** „Texture Aware“ naudoja aukštos kokybės, kraštus atpažįstantį debayerį, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayerio triukšmą.
* **T4P kalibravimo taškų palaikymas*** **Spartesnis „Chloros+“ GPU apdorojimas, geresnis atminties valdymas**

**Klaidų taisymai*** Visiškai nauja vartotojo sąsaja (GUI), dabar turėtų veikti visuose Windows kompiuteriuose.

</details>

<details>

<summary>Versija 1.0.4</summary>

**Išleidimo data: 2026 m. sausio 5 d.**

**Naujos funkcijos*** **Vaizdo/metaduomenų perjungimas**: Failų naršyklėje pridėtas perjungiklis, leidžiantis peržiūrėti pasirinktų vaizdų metaduomenis lentelėje, o ne vaizdų tinklelyje
* **Vaizdų tinklelio mastelio slankiklis**: Naujas vartotojo sąsajos slankiklis, skirtas miniatiūrų dydžiui reguliuoti (taip pat palaiko „CTRL“ + pelės ratuką)
* **Vaizdų tinklelio eksporto mygtukai**: viršutinėje eilutėje esantys mygtukai, skirti perjungti miniatiūras iš JPG į apdorotus eksportus („Targets“, „Reflectance“, „Index“, „LUT“)
* **Skirtukas „Žemėlapis“**: naujas interaktyvus 2D žemėlapis, rodantis vaizdų GPS vietos žymes
  * Palaiko „Google Maps“ ir ESRI žemėlapių plyteles (automatiškai pasirenka geriausią plytelių paslaugą, atsižvelgiant į mastelio lygio prieinamumą)
  * Užvedus pelę ant žemėlapio žymių, rodomas miniatiūrų peržiūros langas

**Klaidų taisymai*** Pagerintas „Chloros“ diegimas kompiuteriuose, kuriuose naudojama ne anglų kalba

</details>

<details>

<summary>Versija 1.0.3</summary>

**Išleidimo data: 2025 m. gruodžio 20 d.**

**Naujos funkcijos*** Pirmasis paleidimas

**Patobulinimai*** Pirmasis paleidimas

**Klaidų taisymai*** Pirmasis paleidimas

**Žinomos problemos*** Pirmasis paleidimas

</details>***

## Licencinė sutartis**Nuosavybinė programinė įranga** – Autorinės teisės (c) 2026 m. „MAPIR Inc.“

Draudžiama neteisėtai naudoti, platinti ar keisti programą.

**Nemokama versija**: Skirta asmeniniam ir komerciniam naudojimui su funkcionalumo apribojimais**Chloros+**: Licencija, pagrįsta prenumerata, suteikianti prieigą prie išplėstinių funkcijų ir komercinio diegimo galimybių
