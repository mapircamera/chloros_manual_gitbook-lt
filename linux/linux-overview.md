# Linux apžvalga

Chloros 1.1.0 versija užtikrina natūralią Linux paramą **CLI**ir**Python SDK**, leidžiantį atlikti daugiaspektrinių vaizdų apdorojimą be monitoriaus Linux darbo stotyse, serveriuose ir NVIDIA Jetson kraštinių įrenginiuose.

{% hint style="info" %}
**Linux neturi GUI.** Chloros darbalaukio GUI yra prieinama tik Windows. Linux vartotojai sąveikauja su Chloros per [CLI](../CLI.md) ir [Python SDK](../api-python-sdk.md).
{% endhint %}

***

## Platformų palaikymo matrica

| Funkcija | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Stalinio kompiuterio GUI** | Taip | N/A | Ne | Ne |
| **CLI** | Taip | Taip | Taip | Taip |
| **Python SDK** | Taip | Taip | Taip | Taip |
| **GPU pagreitinimas (CUDA)** | Taip | Taip | Taip | Taip (JetPack 6) |
| **Tekstūrą atpažįstantis debayeris** | Taip (Chloros+) | Taip (Chloros+) | Taip (Chloros+) | Taip (Chloros+) |
| **Dinaminis skaičiavimo pritaikymas** | Taip | Taip | Taip | Taip |***

## Palaikomos architektūros

| Architektūra | Aprašymas | Įdiegimo būdas |
| --- | --- | --- |
| **amd64 (x86_64)** | Standartiniai staliniai/serverių procesoriai (Intel, AMD) | `.deb` paketas |
| **arm64 (aarch64)** | ARM pagrįsti procesoriai, daugiausia NVIDIA Jetson | `.deb` paketas (JetPack 6) |

## Palaikomos Linux distribucijos

* **Ubuntu 20.04+** (amd64)
* **Debian 11+** (amd64)
* **NVIDIA JetPack 6** (arm64 — „Jetson“ platformos)***

## Ką gauna Linux naudotojai

* **Chloros CLI** — Pilna komandinės eilutės sąsaja, skirta paketiniam apdorojimui, automatizavimui ir scenarijų kūrimui
* **Chloros Python SDK** — Programinė Python sąsaja (`pip install chloros-sdk`) integracijai į mokslinių tyrimų procesus ir pritaikytus įrankius
* **GPU pagreitinimas** — CUDA pagreitintas apdorojimas NVIDIA GPU (stacionariuose kompiuteriuose ir „Jetson“)
* **Dinaminis skaičiavimo pritaikymas** — Automatinis aparatinės įrangos aptikimas ir apdorojimo strategijos optimizavimas
* **Visos apdorojimo funkcijos** — Tas pats daugiaspektrinis apdorojimo procesas kaip ir Windows (kalibravimas, vinjetės korekcija, augmenijos indeksai, visi eksporto formatai)
* **Chloros+ funkcijos** — Daugiagijis apdorojimas, tekstūrą atpažįstantis debayer, pritaikyti indeksai (su Chloros+ licencija)

## Ko Linux vartotojai negauna

* **Darbalaukio GUI** — Nėra grafinės sąsajos; visa sąveika vyksta per CLI arba Python SDK
* **Vaizdų peržiūros programa** — Nėra interaktyvios vaizdų peržiūros programos, tinklelio peržiūros ar žemėlapio žymių
* **Vizualus projektų valdymas** — Projektai valdomi per CLI komandas ir SDK iškvietimus***

## Pradžia su Linux

1. **Įdiekite Chloros** — Žr. [Linux įdiegimas](linux-installation.md) dėl `.deb` paketo įdiegimo
2. **Įdiekite Python SDK** (pasirinktinai) — `pip install chloros-sdk`
3. **Aktyvuokite licenciją** — `chloros-cli login your@email.com 'password'`
4. **Apdorokite savo pirmąjį duomenų rinkinį** — `chloros-cli process ~/datasets/flight001`

„NVIDIA Jetson“ naudotojams rekomenduojame peržiūrėti specialų [„NVIDIA Jetson“ vadovą](nvidia-jetson-guide.md), kuriame pateikta platformai skirta konfigūracija ir optimizavimas.

***

## Tolimesni veiksmai

* [Linux diegimas](linux-installation.md) — išsamios diegimo instrukcijos amd64 ir arm64
* [„NVIDIA Jetson“ vadovas](nvidia-jetson-guide.md) — „Jetson“ specifinis nustatymas, šilumos valdymas ir diegimas
* [CLI : Komandinė eilutė](../CLI.md) — Išsamus CLI žinynas
* [API : Python SDK](../api-python-sdk.md) — Išsamus SDK vadovas
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — Kaip Chloros prisitaiko prie jūsų aparatinės įrangos
