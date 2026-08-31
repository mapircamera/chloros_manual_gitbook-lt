# „Chloros“ naudojimas su AI asistentais

Šis vadovas skirtas dviem tikslinėms grupėms: žmonėms ir AI asistentams, kuriais žmonės vis dažniau naudojasi savo darbe. Kiekviename puslapyje pateikiamos tikslios reikšmės, numatytieji nustatymai ir komandos, kurias galima nukopijuoti ir įklijuoti, kad asistentas (Claude, ChatGPT, Copilot, kodavimo agentas ir kt.) jau iš pirmo karto galėtų parašyti veikiančią Chloros automatizaciją.

Chloros versija: **

1.2.0**. CLI/SDK platformos: Windows 10/11 x64 ir Linux (x86_64 / Jetson aarch64).

## Ką perduoti savo asistentui

| Išteklis | URL | Kam skirtas |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Kompiuteriui suprantamas visų šio vadovo puslapių indeksas. |
| **CLI Nuoroda** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | Išsamus `chloros-cli` komandų aprašas: visos komandos, žymės, numatytieji nustatymai, išėjimo kodai ir išvesties aplanko taisyklės. Parengta LLM naudojimui. |
| **SDK nuoroda** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Išsamus `chloros_sdk`, Python, API: klasės, parašai, išimtys ir pavyzdžiai. Parašyta LLM naudojimui. |
| **Bet kuris puslapis kaip neapdorotas „Markdown“** | pridėkite `.md` prie puslapio URL | pvz. `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` grąžina puslapį kaip neapdorotą „Markdown“ tekstą — idealiai tinka įklijuoti į konteksto langą arba gauti iš agento. |

Nuorodos vadove: [CLI Nuoroda](reference/cli-reference.md) · [SDK Nuoroda](reference/sdk-reference.md).

{% hint style="info" %}
Abu nuorodų puslapiai yra savarankiški: asistentas, perskaitęs vieną iš jų, nereikia likusios vadovo dalies, kad parašytų teisingą scenarijų.
{% endhint %}

## Paruošti komandų pavyzdžiai

Nukopijuokite, užpildykite `<placeholders>` ir įklijuokite į savo asistentą.

### 1. Apdorokite skrydžių aplanką į NDVI

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. Stebėkite „captures“ katalogą paketiniu režimu

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. Prijunkite LATTICE matricos ir užfiksuokite

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. Įrašykite DAQ šviesos jutiklio spektrus

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
DAQ komandų eilutės scenarijų vykdymas visada vyksta per `daq pool-*` šeimą (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). Kitos „`daq`“ pakomandos, kurias jūsų asistentas gali sugalvoti, nėra prieinamos išleistose versijose ir sukelia klaidą.
{% endhint %}

## Kodėl AI parašyti scenarijai gerai veikia su „Chloros“

Kiekvienas iš šių pavyzdžių yra realus, patikrintas „Chloros 1.2.0“ elgesys — jie pašalina klasikinį mašinos parašytos automatizacijos gedimų režimą:

* **Nereikia sudėtingų nustatymų.**„SDK“ „smart-connect“ pagalbiniai moduliai (`connect_camera`, `connect_array`, `connect_daq_sensor`) ir apdorojimo įėjimo taškai (`ChlorosLocal`, `process_folder`)**automatiškai paleidžia vietinį užkulisinį modulį**. Sukurtam scenarijui nereikia atidarytos grafinės vartotojo sąsajos ar rankiniu būdu paleisto serverio – reikia tik įdiegto „desktop/CLI“ paketo.
* **Visas apdorojimo procesas vyksta vienu komandos vykdymu.** `chloros_sdk.process_folder("path", indices=["NDVI"])` nuo pradžios iki pabaigos atlieka importavimą → kalibravimą → atspindžio matavimą → rodiklio eksportavimą. Mažesnis paviršiaus plotas reiškia mažiau vietų, kuriose sukurtas skriptas gali suklysti.
* **Vykdymo ciklai be rezultatų atlieka savidiagnostiką.** Po „`process()`“ vykdymo ciklo santrauka pridedama prie rezultato, o kiekviena apdorojimo užuomina (pvz., *kodėl* ciklas nesukūrė rezultatų) taip pat pakartotinai pateikiamas kaip Python `UserWarning` — taigi net skriptas, kuris niekada netikrina rezultatų žodyno, pateikia diagnozę.
* **CLI žlugsta su dideliu triukšmu.**`chloros-cli process` vykdymas, kuris prašė rezultatų, bet jų neišrašė, išspausdina `Processing finished but wrote no image products.` ir**baigiasi nulinio kodo**, todėl apvalkalo skriptai ir CI jį aptinka paprastai patikrindami baigimo kodą. Sėkmingai įvykdyti vykdymai praneša apie „`Image products written: N`“.

Viena asimetrija, kurią asistentas turėtų žinoti: SDK komandos `process()` sąmoningai **nesukelia** išimties, kai vykdymo metu negaunama jokių rezultatų — vietoj to ji pateikia ataskaitą per santrauką / patarimus. Jei Python procesų grandinė turi sustoti vykdant tuščią vykdymą, patikrinkite santrauką (taip daro 2-asis receptas).

## Įspėjimai

* **Reikalingas prisijungimas su Chloros+.**CLI ir SDK reikalauja**mokamo** Chloros+ paketas, o tai užtikrinama serverio pusėje: užklausos su `401 AUTH_REQUIRED` žlugsta, jei nesate prisijungę, o su `403 PLAN_UPGRADE_REQUIRED` – nemokamo paketo atveju. Prieš paleidžiant sukurtus skriptus, kiekviename kompiuteryje vieną kartą paleiskite komandą `chloros-cli login`. Žr. [Chloros+ Prisijungimas](chloros+-login.md).
* **Užfiksavimo komandos valdo realią įrangą.** Komandos „`lattice`“ / „`daq`“ / „`project`“ ir sesijos objektai „SDK“ jungiasi prie fizinių kamerų ir jutiklių, perduoda jų duomenis ir juos suaktyvina. Prieš pirmąjį paleidimą peržiūrėkite sukurtą scenarijų ir paleiskite jį prižiūrint aparatūrą.
* **Atlikite išvesties atrankinę patikrą.** Prieš skelbdami rezultatus, patikrinkite produkto aplankus ir keletą pikselių verčių. Visų pirma, atspindžio TIFF failai masteliami pagal šaltinį — perskaitykite `Chloros:PixelScale` XMP žymę (LATTICE: 32768 = 1,0 atspindžio koeficientas; Survey3: 65535), o ne darant prielaidą apie daliklį. Abu informaciniai puslapiai tai aprašo skyriuje „Atspindžio pikselių skaitymas“.
* **Smulkūs niuansai, dėl kurių kyla problemų su generuojamu kodu:**`pool-record` rašo į**pagrindinio kompiuterio** failų sistemą (numatyta reikšmė – `~/Documents/DAQ Live View/`); kompiuteriuose su keliais tinklo sąsajų įrenginiais geriau rinktis `daq pool-connect --eth-host <ip-or-hostname>`, o ne automatinį aptikimą; ir visur, kur pasirodo galinis kompiuteris „URL“, naudokite „`http://127.0.0.1:5000`“ (niekada „`localhost`“).
