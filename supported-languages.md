# Palaikomos kalbos

Chloros užtikrina visapusišką sąsajos palaikymą **38 pasaulio kalbomis**, todėl ji prieinama vartotojams visame pasaulyje. Galite akimirksniu keisti kalbas visose sąsajose: darbalaukyje, naršyklėje, CLI ir Python SDK.

Chloros palaiko šias kalbas:

| # | Kalba | Pavadinimas | CLI kodas |
|---|----------|-------------|----------|
| 1 | 🇺🇸 Anglų | English | `en` |
| 2 | 🇪🇸 Ispanų | Español | `es` |
| 3 | 🇵🇹 Portugalų | Português | `pt` |
| 4 | 🇫🇷 Prancūzų | Français | `fr` |
| 5 | 🇩🇪 Vokiečių | Deutsch | `de` |
| 6 | 🇮🇹 Italų | Italiano | `it` |
| 7 | 🇯🇵 Japonų | 日本語 | `ja` |
| 8 | 🇰🇷 Korėjiečių | 한국어 | `ko` |
| 9 | 🇨🇳 Kinų (supaprastinta) | 简体中文 | `zh` |
| 10 | 🇹🇼 Kinų (tradicinė) | 繁體中文 | `zh-TW` |
| 11 | 🇷🇺 Rusų | Русский | `ru` |
| 12 | 🇳🇱 Olandų | Nederlands | `nl` |
| 13 | 🇸🇦 Arabų | العربية | `ar` |
| 14 | 🇵🇱 Lenkų | Polski | `pl` |
| 15 | 🇹🇷 Turkų | Türkçe | `tr` |
| 16 | 🇮🇳 Hindi | हिंदी | `hi` |
| 17 | 🇮🇩 Indoneziečių | Bahasa Indonesia | `id` |
| 18 | 🇻🇳 Vietnamo | Tiếng Việt | `vi` |
| 19 | 🇹🇭 Tailandiečių | ไทย | `th` |
| 20 | 🇸🇪 Švedų | Svenska | `sv` |
| 21 | 🇩🇰 Danų | Dansk | `da` |
| 22 | 🇳🇴 Norvegų | Norsk | `no` |
| 23 | 🇫🇮 Suomų | Suomi | `fi` |
| 24 | 🇬🇷 Graikų | Ελληνικά | `el` |
| 25 | 🇨🇿 Čekų | Čeština | `cs` |
| 26 | 🇭🇺 Vengrų | Magyar | `hu` |
| 27 | 🇷🇴 Rumunų | Română | `ro` |
| 28 | 🇺🇦 Ukrainiečių | Українська | `uk` |
| 29 | 🇧🇷 Brazilijos portugalų | Português Brasileiro | `pt-BR` |
| 30 | 🇭🇰 Kantono | 粵語 | `zh-HK` |
| 31 | 🇲🇾 Malajų | Bahasa Melayu | `ms` |
| 32 | 🇸🇰 Slovakų | Slovenčina | `sk` |
| 33 | 🇧🇬 Bulgarų | Български | `bg` |
| 34 | 🇭🇷 Kroatų | Hrvatski | `hr` |
| 35 | 🇱🇹 Lietuvių | Lietuvių | `lt` |
| 36 | 🇱🇻 Latvių | Latviešu | `lv` |
| 37 | 🇪🇪 Estų | Eesti | `et` |
| 38 | 🇸🇮 Slovėnų | Slovenščina | `sl` |

## Kaip pakeisti kalbą

### Chloros darbalaukyje / naršyklėje

1. Atidarykite programos nustatymus
2. Pereikite į kalbos pasirinkimo meniu
3. Iš sąrašo pasirinkite pageidaujamą kalbą
4. Sąsaja bus atnaujinta iš karto

### Chloros CLI

Naudokite komandą `language`, kad peržiūrėtumėte arba pakeistumėte CLI sąsajos kalbą:

```bash
# View current language
chloros-cli language

# Change to Spanish
chloros-cli language es

# Change to Chinese (Simplified)
chloros-cli language zh

# Change to Brazilian Portuguese
chloros-cli language pt-BR

# List all available languages
chloros-cli language --list
```

Daugiau informacijos rasite [CLI dokumentacijoje](CLI.md).

### Chloros Python SDK

Inicijuodami SDK, nustatykite kalbos parametrą, kad pranešimai ir išvestys būtų rodomi jūsų pageidaujama kalba.

## Aprėptis

Visos 38 kalbos yra visiškai palaikomos:

* **Chloros Desktop** – visiškas GUI vertimas
* **Chloros Browser** – visomis kalbomis pateikiama žiniatinklio sąsaja
* **Chloros CLI** – komandinės eilutės sąsaja ir išvesties pranešimai
* **Chloros Python SDK** – API pranešimai ir dokumentacija

Kalbų palaikymas užtikrina, kad vartotojai visame pasaulyje galėtų efektyviai dirbti savo gimtąja kalba be jokių kliūčių.
