# „Linux“ apžvalga

Chloros 1.2.0 versija užtikrina natyvią Linux paramą **CLI**ir**Python SDK** — daugiaspektrinių vaizdų apdorojimą be monitoriaus, taip pat tiesioginį „LATTICE“ kamerų ir DAQ šviesos jutiklių valdymą — „Linux“ darbo stotyse, serveriuose ir „NVIDIA Jetson“ kraštinių įrenginiuose.

{% hint style="info" %}
**Linux neturi darbalaukio GUI.**Chloros darbalaukio GUI veikia tik Windows. Linux vartotojai su Chloros sąveikauja per [CLI](../CLI.md) ir [Python SDK](../api-python-sdk.md). „`.deb`“ iš tiesų įtraukia**Chloros CLI** įrašą į jūsų programos meniu — jis tiesiog atidaro terminalo emuliatorių, kuriame veikia `chloros-cli`.
{% endhint %}

***

## Platformų palaikymo lentelė

| Funkcija | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / „Jetson“ (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Stalinio kompiuterio grafinė vartotojo sąsaja** | Taip | Netaikoma | Ne | Ne |
| **CLI** (`chloros-cli`) | Taip | Taip | Taip | Taip |
| **Python SDK** (`chloros-sdk`) | Taip | Taip | Taip | Taip |
| **Vaizdo apdorojimo grandinė** | Taip | Taip | Taip | Taip |
| **„LATTICE“ kamerų valdymas (tiesioginis)** | Taip (skirtukas „Kameros“) | Taip (`chloros-cli lattice`, SDK) | Taip | Taip |
| **DAQ šviesos jutikliai (tiesioginis vaizdas)** | Taip (skirtukas „Šviesos jutikliai“) | Taip (`chloros-cli daq pool-*`, SDK) | Taip | Taip |
| **PTP laiko sinchronizavimas (pagrindinis kompiuteris yra „grandmaster“)** | Taip | Taip (`chloros-cli time-sync`) | Taip | Taip |
| **GPU pagreitinimas (CUDA)** | Taip | Taip | Taip | Taip (JetPack 6) |
| **Tekstūrą atpažįstantis debayeris** | Taip (Chloros+) | Taip (Chloros+) | Taip (Chloros+) | Taip (Chloros+) |
| **Dinaminis skaičiavimo pritaikymas** | Taip | Taip | Taip | Taip |
| **Foninė programa kaip sistemos paslauga** (`chloros-backend.service`) | Ne | Ne | Taip (pasirenkama) | Taip (pasirenkama) |
| **Atnaujinimas vietoje** (`chloros-cli update`) | Ne (paleiskite diegimo programą) | Ne (paleiskite diegimo programą) | Taip | Taip |***

## Palaikomos architektūros

| Architektūra | Aprašymas | Paketas |
| --- | --- | --- |
| **amd64 (x86_64)** | Standartiniai staliniai kompiuteriai ir serverių procesoriai (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | ARM procesoriai — „NVIDIA Jetson Orin“ šeima | `chloros_<version>_arm64_jp6.deb` (JetPack 6 versija) |

## Palaikomos Linux distribucijos

* **„Ubuntu 22.04 LTS“ ar naujesnė versija** (amd64)
* **„Debian 12“ ar naujesnė versija** (amd64)
* **„NVIDIA JetPack 6“** (arm64 — „Jetson Orin“ platformos)***

## Ką gauna Linux vartotojai

* **Chloros CLI** — išsami komandinės eilutės sąsaja, skirta paketiniam apdorojimui, automatizavimui ir scenarijų kūrimui
* **Chloros Python SDK** — programinė Python sąsaja, skirta mokslinių tyrimų procesų grandinėms ir individualiems įrankiams (įdiegiama iš PyPI, taip pat įtraukta į `.deb` paketą kaip versijai atitinkantis „wheel“ failas)
* **„LATTICE“ kamerų valdymas** — „LATTICE“ kamerų ir sinchronizuotų daugiakamerinių sistemų aptikimas, prijungimas, konfigūravimas ir vaizdo fiksavimas naudojant „`chloros-cli lattice`“ ir „SDK“; `.deb` pakete yra „Arena SDK“ vykdymo aplinka, reikalinga kameroms
* **DAQ šviesos jutiklių valdymas** — prijunkite DAQ-U/M/E jutiklius, transliuokite kalibruotus spektrus ir įrašykite `.daq` failus per `chloros-cli daq pool-*` ir SDK
* **PTP laiko sinchronizavimas** — „Chloros“ užkulisinė programa paleidžia PTP „grandmaster“, kuriam pavaldžios „LATTICE“ kameros ir DAQ-E jutikliai; patikrinkite ją naudodami „`chloros-cli time-sync`“, ir užtikrinkite jo veikimą be monitoriaus naudodami „systemd“ modulį „`chloros-backend.service`“ (žr. [„Linux“ įdiegimą](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Projektų automatizavimas** — paleiskite išsaugotus projektus be vartotojo sąsajos naudodami „`chloros-cli project`“ ir „SDK“ modulį „`open_project`“
* **GPU pagreitinimas** — CUDA pagreitintas apdorojimas „NVIDIA“ GPU (stacionariuose kompiuteriuose ir „Jetson“)
* **Dinaminis skaičiavimo pritaikymas** — automatinis aparatinės įrangos aptikimas ir apdorojimo strategijos pasirinkimas, su „`CHLOROS_STRATEGY`“ perrašymo funkcija kaip ekspertiniu atsarginiu sprendimu
* **Visos apdorojimo funkcijos** — tas pats apdorojimo procesas kaip ir Windows: kalibravimas, vinjetės korekcija, augmenijos indeksai ir visi eksporto formatai
* **Chloros+ funkcijos** — daugiasriegis (konvejerinis) apdorojimas, „Texture Aware“ debayeris ir pasirinktiniai indeksai, naudojant mokamą Chloros+ planą

## Ko Linux vartotojai negauna

* **Stalinio kompiuterio grafinė vartotojo sąsaja** — nėra grafinės sąsajos; visas sąveikavimas vyksta per „CLI“ arba „Python“ „SDK“
* **Vaizdų peržiūros programa** — nėra interaktyvios vaizdų peržiūros programos, tinklelinio vaizdo ar žymių žemėlapyje
* **Vizualus projektų valdymas** — projektai kuriami ir valdomi naudojant CLI komandas ir SDK iškvietimus (pačią įrangą — kameras, jutiklius, vaizdo fiksavimą — galima visiškai valdyti iš terminalo)***

## Licencijos reikalavimai

Prieigai prie CLI ir SDK reikalingas **mokamas Chloros+ lygis — „Copper“ ar aukštesnis**(„Copper“, „Bronze“, „Silver“, „Gold“). Nemokamas**„Iron“** lygis nesuteikia prieigos prie CLI/SDK. Šis apribojimas taikomas serverio pusėje, ne tik CLI:

| Situacija | Serverio atsakymas |
| --- | --- |
| Neprisijungęs | `401` su `error_code: AUTH_REQUIRED` |
| Prisijungęs prie nemokamo „Iron“ lygio | `403` su `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` veikia bet kuriame lygyje — tai vienintelis maršrutas, kuriam netaikomas filtras — todėl atmetimo priežastis visada matoma.

***

## Pradžia su Linux

1. **Įdiekite Chloros** — `.deb` diegimo instrukcijas rasite [Linux diegimo vadove](linux-installation.md)
2. **Patikrinkite** — `chloros-cli --version` išspausdina `Chloros CLI 1.2.0`; `chloros-cli selftest` paleidžia 7 žingsnių diagnostiką
3. **Įdiekite Python ir SDK** (pasirinktinai) — `pip install chloros-sdk`
4. **Prisijunkite** — `chloros-cli login your@email.com 'your-password'` (vieną kartą kiekviename kompiuteryje ir dar kartą po kiekvieno programinės įrangos atnaujinimo)
5. **Apdorokite pirmąjį duomenų rinkinį** — `chloros-cli process ~/datasets/flight001`

Dėl „NVIDIA Jetson“ žr. specialų [„NVIDIA Jetson“ vadovą](nvidia-jetson-guide.md), kuriame pateikiama informacija apie platformai būdingą konfigūraciją, šilumines savybes ir diegimą praktikoje.

***

## Tolimesni veiksmai

* [Linux diegimas](linux-installation.md) — išsamus diegimas, failų vietos ir trikčių šalinimas „amd64“ ir „arm64“ platformose
* [„NVIDIA Jetson“ vadovas](nvidia-jetson-guide.md) — „Jetson“ specifinis nustatymas, atminties ir šiluminių savybių aprašymas, diegimas praktikoje
* [CLI : Komandinė eilutė](../CLI.md) — CLI vadovas
* [API : Python SDK](../api-python-sdk.md) — „SDK“ vadovas
* [CLI nuoroda](../reference/cli-reference.md) ir [SDK nuoroda](../reference/sdk-reference.md) — išsamūs komandų/API sąrašai versijai 1.2.0
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — kaip Chloros prisitaiko prie jūsų aparatinės įrangos

{% hint style="info" %}
**Šio vadovo skaitymas programiniu būdu.** Kiekvienas puslapis taip pat pateikiamas kaip neapdorotas „Markdown“ formatas savo atskirame URL bei `.md` (pavyzdžiui, `https://mapir.gitbook.io/chloros/linux/linux-installation.md`), o viso vadovo rodyklė skelbiama adresu [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt).
{% endhint %}
