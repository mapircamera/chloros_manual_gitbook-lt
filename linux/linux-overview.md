# Linux apžvalga

Chloros 1.3.0 versija užtikrina natyvią Linux paramą **CLI**ir**Python SDK** — daugiaspektrinių vaizdų apdorojimą be grafinės sąsajos, taip pat „LATTICE“ kamerų ir DAQ šviesos jutiklių valdymą realiuoju laiku — Linux darbo stotyse, serveriuose ir „NVIDIA Jetson“ kraštinių įrenginiuose.

{% hint style="info" %}
**Linux sistemoje nėra darbalaukio GUI.**Chloros darbalaukio GUI skirta tik Windows sistemai. Linux vartotojai su Chloros sąveikauja per [CLI](../CLI.md) ir [Python SDK](../api-python-sdk.md). `.deb` į jūsų programų meniu įtraukia**Chloros CLI** įrašą — jis tiesiog atidaro terminalo emuliatorių, kuriame veikia `chloros-cli`.
{% endhint %}

***

## Platformų palaikymo lentelė

| Funkcija | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Darbalaukio GUI** | Taip | Netaikoma | Ne | Ne |
| **Vartotojo sąsaja CLI** (`chloros-cli`) | Taip | Taip | Taip | Taip |
| **Python SDK** (`chloros-sdk`) | Taip | Taip | Taip | Taip |
| **Vaizdo apdorojimo procesas** | Taip | Taip | Taip | Taip |
| **„LATTICE“ kamerų valdymas (tiesioginis)** | Taip (skirtukas „Kameros“) | Taip (`chloros-cli lattice`, SDK) | Taip | Taip |
| **DAQ šviesos jutikliai (tiesioginis režimas)** | Taip (skirtukas „Šviesos jutikliai“) | Taip (`chloros-cli daq pool-*`, SDK) | Taip | Taip |
| **PTP laiko sinchronizavimas (pagrindinis kompiuteris yra „grandmaster“)** | Taip | Taip (`chloros-cli time-sync`) | Taip | Taip |
| **GPU pagreitinimas (CUDA)** | Taip | Taip | Taip | Taip (JetPack 6) |
| **„Texture Aware“ debayer** (reikalingas CUDA GPU) | Taip („Chloros+“) | Taip („Chloros+“) | Taip („Chloros+“) | Taip („Chloros+“) |
| **Dinaminis skaičiavimo pritaikymas** | Taip | Taip | Taip | Taip |
| **Backend kaip sistemos paslauga** (`chloros-backend@<user>.service`) | Ne | Ne | Taip (pasirenkama) | Taip (pasirenkama) |
| **Atnaujinimas vietoje** (`chloros-cli update`) | Ne (paleiskite diegimo programą) | Ne (paleiskite diegimo programą) | Taip | Taip |

***

## Palaikomos architektūros

| Architektūra | Aprašymas | Paketas |
| --- | --- | --- |
| **amd64 (x86_64)** | Standartiniai staliniai kompiuteriai / serverių procesoriai (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | ARM procesoriai — NVIDIA Jetson Orin šeima („JetPack 6“) | `chloros_<version>_arm64_jp6.deb` |

## Palaikomos Linux distribucijos

* **„Ubuntu 22.04 LTS“ arba naujesnė** (amd64)
* **„Debian 12“ arba naujesnė** (amd64)
* **NVIDIA JetPack 6** (arm64 — „Jetson Orin“ platformos)

***

## Ką gauna Linux vartotojai

* **Chloros CLI** — išsami komandinės eilutės sąsaja, skirta paketiniam apdorojimui, automatizavimui ir scenarijų kūrimui
* **Chloros Python SDK** — programinė Python sąsaja, skirta mokslinių tyrimų procesų grandinėms ir individualiems įrankiams kurti; ją įdiegia „`.deb`“ sistema „`python3`“ (versijai pritaikytas „wheel“ failas „virtualenvs“ yra `/usr/lib/chloros/sdk/`; „PyPI“ skirtas kompiuteriams be Chloros paketo)
* **„LATTICE“ kamerų valdymas** — „LATTICE“ kamerų ir sinchronizuotų daugiakamerinių sistemų aptikimas, prijungimas, konfigūravimas ir vaizdo fiksavimas naudojant „`chloros-cli lattice`“ ir SDK; „`.deb`“ apima „Arena SDK“ vykdymo aplinką, kurios reikia kameroms
* **DAQ šviesos jutiklių valdymas** — prijunkite DAQ-U/M/E jutiklius, transliuokite kalibruotus spektrus ir įrašykite „`.daq`“ failus per „`chloros-cli daq pool-*`“ ir SDK
* **PTP laiko sinchronizavimas** — Chloros užkulisinė programa paleidžia PTP „grandmaster“, kuriam pavaldžios „LATTICE“ kameros ir DAQ-E jutikliai; patikrinkite jį naudodami „`chloros-cli time-sync`“ ir užtikrinkite jo veikimą be vartotojo sąsajos naudodami „`chloros-backend@<user>.service`“ „systemd“ modulį (žr. [Linux diegimas](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Projektų automatizavimas** — paleiskite išsaugotus projektus be vartotojo sąsajos naudodami „`chloros-cli project`“ ir SDK „`open_project`“
* **GPU pagreitinimas** — CUDA pagreitintas apdorojimas „NVIDIA“ GPU (stacionarių kompiuterių GPU „amd64“ platformoje ir „Jetson“ GPU su „JetPack 6“)
* **Dinaminis skaičiavimo pritaikymas** — automatinis aparatinės įrangos aptikimas ir apdorojimo strategijos pasirinkimas, su „`CHLOROS_STRATEGY`“ perrašymo funkcija kaip ekspertine išeitimi
* **Visos apdorojimo funkcijos** — tas pats apdorojimo procesas kaip Windows: kalibravimas, vinjetės korekcija, augmenijos indeksai ir visi eksporto formatai
* **„Chloros+“ funkcijos** — daugiasiūlis (konvejerinis) apdorojimas, „Texture Aware“ debayeris (CUDA GPU) ir pasirinktiniai indeksai, naudojant mokamą „Chloros+“ planą

## Ko neturi Linux vartotojai

* **Darbalaukio GUI** — nėra grafinės sąsajos; visa sąveika vyksta per CLI arba Python SDK
* **Vaizdų peržiūros programa** — nėra interaktyvios vaizdų peržiūros programos, tinklelinio vaizdo ar žemėlapio žymių
* **Vizualus projektų valdymas** — projektai kuriami ir valdomi naudojant CLI komandas ir SDK iškvietimus (pati įranga — kameros, jutikliai, duomenų surinkimas — lieka visiškai valdoma iš terminalo)

***

## Licencijos reikalavimai

Prieigai prie CLI ir SDK reikalingas **mokamas „Chloros+“ planas — „Copper“ ar aukštesnis**(„Copper“, „Bronze“, „Silver“, „Gold“). Nemokamame**„Iron“** plane nėra prieigos prie CLI/SDK. Šis apribojimas taikomas per backendą, ne tik per CLI:

| Situacija | Backendo atsakymas |
| --- | --- |
| Neprisijungęs | `401` su `error_code: AUTH_REQUIRED` |
| Prisijungta nemokamoje „Iron“ pakopoje | `403` su `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` veikia bet kuriame pakete — tai vienintelis maršrutas, kuriam netaikomas filtras — todėl atmetimo priežastis visada matoma.

***

## Pradžia Linux

1. **Įdiekite Chloros** — „`.deb`“ diegimui žr. [Linux diegimas](linux-installation.md)
2. **Patikrinkite** — `chloros-cli --version` išveda `Chloros CLI 1.3.0`; `chloros-cli selftest` paleidžia 7 žingsnių diagnostiką
3. **Patikrinkite Python SDK** — `.deb` jį įdiegia: `python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"` (virtualiame aplinkos konteineriuje – `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`)
4. **Prisijunkite** — `chloros-cli login your@email.com 'your-password'` (vieną kartą kiekviename kompiuteryje; atnaujinus paketą prisijungimo duomenys išlieka, o pašalinus paketą jie ištrinami)
5. **Apdorokite savo pirmąjį duomenų rinkinį** — `chloros-cli process ~/datasets/flight001`

Dėl „NVIDIA Jetson“ žr. specialų [„NVIDIA Jetson“ vadovą](nvidia-jetson-guide.md), kuriame pateikiama informacija apie platformai būdingą konfigūraciją, šilumines savybes ir diegimą praktikoje.

***

## Tolimesni veiksmai

* [Įdiegimas Linux sistemoje](linux-installation.md) — išsamus įdiegimo aprašymas, failų vietos ir trikčių šalinimas „amd64“ ir „arm64“ architektūroms
* [„NVIDIA Jetson“ vadovas](nvidia-jetson-guide.md) — „Jetson“ specifinis nustatymas, atminties ir šiluminių savybių aprašymas, diegimas praktikoje
* [CLI: Komandinė eilutė](../CLI.md) — CLI vadovas
* [API: Python SDK](../api-python-sdk.md) — SDK vadovas
* [CLI žinynas](../reference/cli-reference.md) ir [SDK žinynas](../reference/sdk-reference.md) — išsamūs 1.3.0 versijos komandų ir API sąrašai
* [Dinaminis skaičiavimo pritaikymas](../processing-architecture/dynamic-compute-adaptation.md) — kaip Chloros prisitaiko prie jūsų aparatinės įrangos

{% hint style="info" %}
**Šio vadovo skaitymas programiškai.** Kiekvienas puslapis taip pat pateikiamas kaip neapdorotas „Markdown“ formatas savo atskirame URL, pridėjus `.md` (pavyzdžiui, `https://mapir.gitbook.io/chloros/linux/linux-installation.md`), o viso vadovo rodyklė paskelbta [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt).
{% endhint %}
