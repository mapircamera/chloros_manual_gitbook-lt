---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Atsisiųsti

Atsisiųskite naujausią Chloros versiją ir pradėkite dirbti su daugiaspektrinių vaizdų apdorojimu.

### Sistemos reikalavimai

#### Windows


| Reikalavimas          | Minimalūs                                              | Rekomenduojami                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Operacinė sistema** | Windows 10 (64 bitų)                                  | Windows 11 (64 bitų)                                  |
| **Procesorius**        | „Intel Core i5“ arba lygiavertis                          | „Intel Core i7“ arba galingesnis                              |
| **Atmintis (RAM)**     | 8 GB                                                  | 16 GB arba daugiau                                         |
| **Vaizdo plokštė**    | Suderinama su „DirectX 11“                                | „NVIDIA“ vaizdo plokštė su 4 GB ar daugiau VRAM                            |
| **Saugykla**          | 6 GB laisvos vietos                                       | SSD su 10 GB ar daugiau laisvos vietos                            |
| **Ekranas**          | 1920x1080                                            | 2560x1440 ar didesnė skiriamoji geba                                  |
| **Internetas**         | Reikalingas \[pasirinktinai] „Chloros+“ licencijos aktyvavimui | Reikalingas \[pasirinktinai] „Chloros+“ licencijos aktyvavimui |

#### Linux amd64 (x86_64)

| Reikalavimai       | Minimalūs                    | Rekomenduojami               |
| ----------------- | -------------------------- | ------------------------- |
| **Distribucija**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **Procesorius**     | x86\_64 (Intel/AMD)        | Intel Core i7 ar geresnis   |
| **Atmintis (RAM)**  | 8 GB                        | 16 GB ar daugiau              |
| **Vaizdo plokštė** | Nereikalinga (apdorojama procesoriumi)      | „NVIDIA“ vaizdo plokštė su 4 GB ar daugiau VRAM |
| **Saugykla**       | 2 GB laisvos vietos             | SSD su 10 GB+ laisvos vietos       |
| **Python**        | Python 3.7+ (SDK)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Reikalavimai      | Minimalūs                      | Rekomenduojami                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platforma**     | „NVIDIA Jetson“ su „JetPack 6“ | „Jetson Orin NX“ 16 GB arba „AGX Orin“ |
| **Atmintis (RAM)** | 8 GB (bendra GPU/CPU)         | 16 GB+ bendra                    |
| **Saugykla**      | 2 GB laisvos vietos               | NVMe SSD su 10 GB+ laisvos vietos        |
| **Python**       | Python 3.7+ (SDK)        | Python 3.10+                    |

{% hint style="info" %}
**GPU pagreitinimas**: „Chloros+“ vartotojai, turintys „NVIDIA“ GPU, gali naudoti CUDA pagreitinimą, kad apdorojimas vyktų žymiai greičiau. Tai veikia su „NVIDIA“ staliniais GPU Windows ir Linux amd64 sistemose, taip pat „NVIDIA Jetson“ su „JetPack 6“. „Chloros+“ vartotojai taip pat gauna daugiasiūlį apdorojimą, užtikrinantį maksimalų greitį.
{% endhint %}

***

## Atsisiųskite Chloros


### Naujausia stabili versija: 1.3.0 (2026 m. lapkričio 11 d.)

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Atsisiųskite Chloros skirtą Windows (.exe)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Atsisiųskite Chloros skirtą Linux amd64 (.deb)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Atsisiųskite Chloros skirtą Linux arm64 / Jetson (.deb)</a>


#### Windows diegimo programa (GUI + CLI + Backend)

* **Failo tipas**: .exe (Windows diegimo programa)

**Įdiegimo žingsniai:**

1. Atsisiųskite aukščiau nurodytą failą „.exe“
2. Dukart spustelėkite diegimo programą, kad pradėtumėte diegimą
3. Vykdykite diegimo vedlio nurodymus
4. Pasirinkite diegimo katalogą (numatyta: „`C:\Program Files\Chloros\`“)
5. Puslapyje **„Pasirinkite papildomas užduotis“**palikite pažymėtas abi**„Sistemos nustatymų“** parinktis (rekomenduojama — žr. toliau)
6. Baigkite diegimą ir paleiskite Chloros arba „Chloros CLI “ 7. Prisijunkite naudodami savo [„MAPIR Cloud Chloros+“ paskyrą](https://cloud.mapir.camera/pricing) (arba tęskite su nemokama versija)

**Sistemos nustatymų parinktys** (abi pažymėtos pagal numatytuosius nustatymus):

| Parinktis, kaip nurodyta diegimo programoje | Ką ji daro | Atsisakymas tylaus diegimo |
| --- | --- | --- |
| **Įjungti ilgus Windows kelius** (leidžia projektų aplankams būti giliau nei 260 simbolių; rekomenduojama) | Nustato visam „Windows “ kompiuteriui taikomą `LongPathsEnabled` parametrą į 1. Windows šią funkciją pateikia išjungtą, todėl joks failo kelias negali būti ilgesnis nei 260 simbolių; Chloros produktai užima vietą iki maždaug 130 simbolių žemiau projekto aplanko, taigi, kai ilgieji keliai išjungti, gilesnis projekto aplankas nepriimamas. Įsigalioja programoms, paleistoms po įdiegimo. Šis nustatymas išlieka įjungtas ir po programos pašalinimo, nes kitos programos gali juo remtis. | `/MERGETASKS="!longpaths"` |
| **Išskirti Chloros užkurtį iš „Microsoft Defender“ nuskaitymo** (žymiai greitesnis vaizdo įrašymas; rekomenduojama) | Įdiegimo aplanke pridedamas „Microsoft Defender“ proceso išimtis „`chloros-backend.exe`“, todėl realaus laiko apsauga nenuskaito kiekvieno failo, kurį užkurtis įrašo, nepriklausomai nuo to, kur jis įrašomas. Esant dideliam kadrų fiksavimo dažniui, nuskaitymas sudaro apie 30 % kadrų fiksavimo dažnio (77,7 prieš 52,7 kadrų per sekundę, kai nuolat fiksuojama iš 9 kamerų). Jokia kita programa nėra išimta, o išimtis pašalinama išdiegus programą. Jei „Defender“ nėra arba yra užblokuotas pagal politiką, šis žingsnis tyliai nepavyksta ir diegimas tęsiasi. | `/MERGETASKS="!defenderexclusion"` |

Tylioji diegimo procedūra (`/SILENT` arba `/VERYSILENT`) taiko abi parinktis, nebent atsisakytumėte jų, nurodydami rodomą reikšmę `/MERGETASKS`.

{% hint style="success" %}
Diegimo programa vieną kartą įtraukia CLI aplanką (`C:\Program Files\Chloros\cli\`) į jūsų sistemą `PATH` — pakartotinai diegiant ar atnaujinant, dubliuojamas įrašas nebebus pridedamas, o anksčiau diegimo programų pridėti dubliuojami įrašai bus pašalinti. Įdiegus programą, atidarykite naują terminalą, kad galėtumėte naudoti „`chloros-cli`“. Diegimo programa taip pat kiekviename tinklo profilyje prideda dvi Windows užkardos taisykles, skirtas užkulisiniam moduliui: **Chloros užkulisiai (UDP-In)**, skirti kamerų srautams, PTP, mDNS ir DAQ-E duomenų srautui, bei**Chloros užkulisiai (TCP-In)**, apriboti vietiniu potinkliu, skirti DAQ-E programinės įrangos atnaujinimui, kai jutiklis prisijungia prie šio kompiuterio, kad surinktų vaizdą. Abi taisyklės pašalinamos išdiegus programą.
{% endhint %}

#### Linux amd64 (.deb paketas — CLI + užkulisiai)

* **Failo tipas**: .deb (Debian/Ubuntu paketas)
* **Architektūra**: x86\_64 (amd64)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

#### Linux arm64 — „NVIDIA Jetson“ (.deb paketas — CLI + Backend)

* **Failo tipas**: .deb (JetPack 6)
* **Architektūra**: aarch64 (arm64)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

Naudokite faktiškai atsisiųstą failo pavadinimą ir palikite `./`, kad `apt` įdiegtų tą failą, o ne ieškotų paketo pagal pavadinimą. Įdiekite naudodami `apt install`, o ne su „`dpkg -i`“: „apt“ taip pat įdiegia Python SDK reikalavimus, kuriuos paketas rekomenduoja, o ne reikalauja, o „`dpkg -i`“ juos praleidžia. Nuo 1.3.0 versijos užkulisinė programa ir CLI yra įdiegiamos išpakuotos pagal „`/usr/lib/chloros/`“, todėl jos paleidžiamos be išankstinio išpakavimo.

„Jetson“ paketas naudoja „JetPack 6“ CUDA versiją „PyTorch“, todėl apdorojimas, įskaitant „Texture Aware“ debayerį, vyksta „Jetson“ GPU. Jis naudoja „CUDA“ ir „cuDNN“ bibliotekas, kurias įdiegia „JetPack 6“ (`sudo apt install nvidia-jetpack` atvaizde, kuriame jų trūksta).

Išsamias diegimo instrukcijas rasite [Linux diegimo vadove](linux/linux-installation.md), o „Jetson“ specifinius nurodymus – [„NVIDIA Jetson“ vadove](linux/nvidia-jetson-guide.md).

#### Python SDK (visos platformos)

Kiekvienas diegimo paketas apima atitinkamą „`chloros_sdk`“ „wheel“, todėl SDK versija visada atitinka įdiegtą GUI/CLI/backend. Windows sistemoje diegimo programa jį automatiškai įdiegia į jūsų sistemos Python. Linux sistemoje „`.deb`“ įdiegia SDK, skirtą pačiai „`python3`“ sistemai (į „`/usr/lib/python3/dist-packages`“), todėl „`import chloros_sdk`“ veikia iškart po įdiegimo, be „pip“ ir be tinklo. Tik „virtualenv“, kuris nemato sistemos paketų, reikalauja, kad į jį būtų įdiegtas pridedamas „wheel“:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Kompiuteriuose, kuriuose veikia tik „pip“ (neįdiegtas Chloros paketas; tokiu atveju SDK valdo užkurtį kitame kompiuteryje), SDK taip pat yra „PyPI“:

```bash
pip install chloros-sdk
```

Dokumentaciją rasite [API: Python SDK](api-python-sdk.md) ir [SDK žinyne](reference/sdk-reference.md).

{% hint style="info" %}
**Linux vartotojai**: Paketas „`.deb`“ įdiegia CLI, užkurtį ir Python SDK. Linux sistemai nėra grafinės vartotojo sąsajos (GUI) — visa sąveika vyksta per CLI arba SDK.
{% endhint %}

***

## Papildomi ištekliai

### Python SDK


Kūrėjams ir automatizavimo procesams skirtas Chloros Python SDK yra pridedamas prie kiekvieno diegimo paketo: Windows diegimo programa jį įdiegia į jūsų Python, o Linux `.deb` jį įdiegia sistemos `python3` reikmėms. Kompiuteryje, kuriame nėra Chloros paketo (t. y. kuriame valdomas kito kompiuterio užkulisinis modulis), jį įdiekite iš „PyPI“:

```bash
pip install chloros-sdk
```

**Dokumentacija**: [API: Python SDK](api-python-sdk.md)

**Reikalavimai**: valdomas Chloros backend (jį įdiegia Windows diegimo programa arba Linux `.deb` paketas) ir „Chloros+“ prisijungimo duomenys mokamame lygyje

***

## Kas įtraukta

### Windows diegimo programa

* ✅ **Chloros GUI** – visapusiška grafinė sąsaja
* ✅ **Chloros CLI** – komandinės eilutės sąsaja (reikalinga „Chloros+“ licencija)
* ✅ **Chloros užkulisiai** – apdorojimo variklis
* ✅ **Python SDK** – įdiegiamas į jūsų Python, jei toks yra (reikalinga „Chloros+“ licencija)
* ✅ **Kamerų profiliai** – iš anksto sukonfigūruoti MAPIR kamerų šablonai

### Linux.deb paketas

* ✅ **Chloros CLI** – komandinės eilutės sąsaja (reikalinga „Chloros+“ licencija)
* ✅ **Chloros Backend** – apdorojimo variklis
* ✅ **Python SDK** – įdiegiamas sistemai `python3` (reikalinga „Chloros+“ licencija)
* ✅ **Kamerų profiliai** – iš anksto sukonfigūruoti MAPIR kamerų šablonai
* ❌ Nėra grafinės vartotojo sąsajos (GUI) — Linux veikia tik be grafinės sąsajos, tik per CLI ir SDK


### Python SDK (PyPI, kompiuteriams be Chloros paketo)

* ✅ **Chloros SDK** – Python API (reikalinga „Chloros+“ licencija)

***

## Atnaujinkite į „Chloros+“

Atrakinkite išplėstines funkcijas su „Chloros+“ prenumerata:

* 🚀 **Daugiasiūlis apdorojimas** – apdorokite vaizdus lygiagrečiai
* ⚡ **GPU (CUDA) pagreitinimas** - Pasinaudokite „NVIDIA“ GPU galia
* 💻 **Prieiga per CLI** – automatizuokite naudodami komandinės eilutės įrankius
* 🐍 **Python SDK** – programinė prieiga prie API

* 📱 **Keli įrenginiai** – naudokite 2–10 ir daugiau įrenginių (priklausomai nuo plano)
* **🐻 Išplėstinis tekstūrą atpažįstantis debayerio metodas** – aukštos kokybės, kraštus atpažįstantis debayeris, derinamas su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayerio triukšmą.
* 🧮 **Pasirinktinės formulės** – kurkite pasirinktinius multispektrinius indeksus

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Peržiūrėkite „Chloros+“ planus ir kainas</a></p>

***

## Pagalba diegiant

### Problemų sprendimas

**Diegimas nepavyksta, rodomas klaidos pranešimas:**

* Įsitikinkite, kad turite administratoriaus teises
* Laikinai išjunkite antivirusinę programinę įrangą
* Patikrinkite, ar atitinkate minimalius sistemos reikalavimus

**Programa nepaleidžiama (Windows):**

* Patikrinkite, ar įdiegta „Windows 10/11“ (64 bitų)
* Atnaujinkite vaizdo plokštės tvarkykles
* Windows įvykių peržiūroje patikrinkite klaidos detales
* Susisiekite su palaikymo tarnyba ir pateikite klaidų žurnalus

**CLI nepaleidžiama (Linux):**

* Patikrinkite, ar paketas „`.deb`“ įdiegtas teisingai: „`dpkg -l | grep chloros`“
* Patikrinkite leidimus: „`sudo chmod +x /usr/bin/chloros-cli`“
* Vykdykite diagnostiką: „`chloros-cli selftest`“
* Patikrinkite, ar netrūksta bibliotekų: `ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"` (nuo versijos 1.3.0 `/usr/lib/chloros/chloros-backend` yra nedidelis paleidimo scenarijus; pati programa yra `/usr/lib/chloros/backend/`)

**Licencijos aktyvavimo problemos:**

* Įsitikinkite, kad interneto ryšys veikia
* Patikrinkite prisijungimo duomenis adresu [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Patikrinkite, ar ugniasienė neblokuoja Chloros

* Išsamias instrukcijas rasite [„Chloros+“ prisijungimo puslapyje](chloros+-login.md)

### Pagalba

Reikia pagalbos diegiant ar konfigūruojant?

* 📧 **El. paštas**: info@mapir.camera
* 🌐 **Svetainė**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentacija**: [Pradžios vadovas](./)
* ❓ **DUK**: [Dažnai užduodami klausimai](faq.md)

***

## Programinės įrangos atnaujinimai

Chloros tikrina, ar yra atnaujinimų, praneša, kai pasirodo nauja versija, ir pateikia nuorodą į šį atsisiuntimo puslapį — atnaujinate paleisdami naują pasirašytą diegimo programą. Jūsų nustatymai, projektai ir prisijungimo duomenys išlieka po atnaujinimų. Linux ir „Jetson“ sistemose „`chloros-cli update`“ patikrina, ar yra naujesnė versija, ir siūlo atsisiųsti bei įdiegti atitinkamą „`.deb`“ (ši komanda skirta tik Linux sistemai).

***

## Keitimų žurnalas**Versija 1.3.0 (naujausia, 2026 m. lapkričio 11 d.)**— išsamų sąrašą rasite skyriuje**„Chloros

1.3.0 naujovės“** [Pradžios](./) puslapyje.**Versija 1.2.0**— visą sąrašą rasite skyriuje**Chloros 1.2.0 naujovės** puslapyje [Pradžia](./).

<details>

<summary>Versija 1.0.5</summary>

**Išleidimo data: 2026 m. vasario 10 d.**

**Naujos funkcijos*** **„Texture Aware“ debayerio metodas \[tik „Chloros+“] —** „Texture Aware“ naudoja aukštos kokybės, kraštus atpažįstantį debayerį, derinamą su AI/ML triukšmo šalinimo modeliu, kuris pašalina beveik visą debayerio triukšmą.
* **T4P kalibravimo taikiniai dabar palaikomi*** **Spartesnis „Chloros+“ apdorojimas GPU, geresnis atminties valdymas**

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

**Klaidų taisymai*** Pagerintas Chloros diegimo palaikymas kompiuteriuose, kuriuose naudojama ne anglų kalba

</details>

<details>

<summary>Versija 1.0.3</summary>

**Išleidimo data: 2025 m. gruodžio 20 d.**

**Naujos funkcijos*** Pirmasis paleidimas

**Patobulinimai*** Pirmasis paleidimas

**Klaidų taisymai*** Pirmasis paleidimas

**Žinomos problemos*** Pirmasis paleidimas

</details>

***

## Licencinė sutartis**Nuosavybinė programinė įranga** – Autorinės teisės (c) 2026 m. MAPIR Inc.

Draudžiama neteisėtai naudoti, platinti ar keisti programą.

**Nemokama versija**: Skirta asmeniniam ir komerciniam naudojimui su funkcionalumo apribojimais**„Chloros+“**: Licencija, pagrįsta prenumerata, suteikianti prieigą prie išplėstinių funkcijų ir komercinio diegimo galimybių
