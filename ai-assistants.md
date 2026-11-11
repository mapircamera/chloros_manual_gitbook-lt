# Chloros naudojimas su AI asistentais

Šis vadovas skirtas dviem auditorijoms: žmonėms ir AI asistentams, su kuriais žmonės vis dažniau dirba. Kiekviename puslapyje pateikiamos tikslios reikšmės, numatytieji nustatymai ir komandos, kurias galima nukopijuoti ir įklijuoti, kad asistentas (Claude, ChatGPT, Copilot, kodavimo agentas ir kt.) galėtų iš pirmo karto parašyti veikiančią Chloros automatizaciją.

Chloros versija: **

1.3.0**. CLI / SDK platformos: Windows 10/11 x64 ir Linux (x86_64 / „Jetson“ aarch64).

## Ką pateikti savo asistentui

| Išteklis | URL | Kam skirtas |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Kompiuteriui suprantamas visų šio vadovo puslapių indeksas. |
| **CLI žinynas** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | Išsamus „`chloros-cli`“ komandų sąrašas: visos komandos, žymės, numatytieji parametrai, išėjimo kodai ir išvesties aplanko taisyklės. Parengta LLM naudojimui. |
| **SDK nuoroda** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Išsamus „`chloros_sdk`“ Python API: klasės, parašai, išimtys ir praktiniai pavyzdžiai. Parengta LLM naudojimui. |
| **Bet kuris puslapis kaip neapdorotas „Markdown“** | pridėkite `.md` prie puslapio URL | pvz., `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` grąžina puslapį kaip neapdorotą „Markdown“ — idealiai tinka įklijuoti į konteksto langą arba gauti iš agento. |

Nuorodos vadove: [CLI žinynas](reference/cli-reference.md) · [SDK žinynas](reference/sdk-reference.md).

{% hint style="info" %}
Abu žinyno puslapiai yra savarankiški: asistentas, perskaitęs vieną iš jų, nereikia likusios žinyno dalies, kad parašytų teisingą scenarijų.
{% endhint %}

## Leiskite savo asistentui tiesiogiai paleisti Chloros: `chloros-mcp`

Visuose aukščiau pateiktuose pavyzdžiuose jūsų asistentas *rašo* scenarijų arba komandą. Naudojant `chloros-mcp`, asistentas, kuris palaiko [Model Context Protocol](https://modelcontextprotocol.io) (MCP), pavyzdžiui, „Claude Desktop“, „Claude Code“ ar „Cursor“, pats valdo Chloros. Jūs paprašote paprasta kalba, o jis iškvietia serverio įrankius:

* **Apdoroti vaizdus:** sukurti projektą, importuoti užfiksuotų vaizdų aplanką, pasirinkti nustatymus, apdoroti ir pateikti ataskaitą apie tai, ką eksportavo vykdymas.
* **Valdyti aparatūrą:** prijungti „LATTICE“ kameras, matuoklių masyvus ir „DAQ“ šviesos jutiklius, peržiūrėti vaizdą, nustatyti ekspoziciją ir spalvas, fotografuoti, įrašyti ir atnaujinti programinę įrangą.

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.
```

Serverio įrankiai apima tai, ką atlieka [Python SDK](api-python-sdk.md). Jis įdiegiamas kartu su SDK, todėl paprastai nieko kito įdiegti nereikia: Windows sistemoje Chloros diegimo programa įdeda `chloros-mcp.exe` į jūsų Python `Scripts` aplanką, o Linux `.deb` įdeda `chloros-mcp` į `/usr/bin`.

**„Claude“ kodas:**

```bash
claude mcp add chloros -- chloros-mcp
```

**„Claude Desktop“:**įtraukite šį įrašą į „`claude_desktop_config.json`“ (atidarykite meniu**„Nustatymai“ > „Kūrėjas“ > „Redaguoti konfigūraciją“**), tada iš naujo paleiskite „Claude Desktop“.**„Cursor“** naudoja tą patį įrašą „`~/.cursor/mcp.json`“.

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

Jei klientas praneša, kad serveris nepavyko paleisti, tikriausiai `chloros-mcp` nėra tame `PATH`, kurį jis mato: vietoj to nurodykite pilną kelią (`where chloros-mcp` Windows sistemoje, `which chloros-mcp` Linux sistemoje).

Tame pačiame kompiuteryje turi būti įdiegta Chloros programa, o jūs turite būti prisijungę ten, programoje arba per `chloros-cli login`, pasirinkę bet kurį mokamą „Chloros+“ planą („Copper“ ar aukštesnį). Serveris negali prisijungti už jus.

{% hint style="info" %}
Kliento nustatymas kiekvienai programai, parinktys, kiekvienas įrankis ir vykdymo elgsena: [MCP serveris (AI asistentai)](reference/sdk-reference.md#mcp-server-ai-assistants) SDK žinyne.
{% endhint %}

## Komandų pavyzdžiai

Nukopijuokite, užpildykite `<placeholders>` ir įklijuokite į savo asistentą.

### 1. Skrydžio aplanko apdorojimas į NDVI

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. Stebėkite užfiksuotų duomenų katalogą paketiniu būdu

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. Prijunkite „LATTICE“ matricos sistemą ir atlikite fiksavimą

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. DAQ šviesos jutiklio spektrų įrašymas

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
DAQ komandų eilutės scenarijų vykdymas visada vyksta per `daq pool-*` šeimą (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). Kitos „`daq`“ pakomandos, kurias gali sugalvoti jūsų asistentas, nėra prieinamos išleistose versijose ir sukelia klaidą.
{% endhint %}

## Kodėl AI parašyti skriptai gerai veikia su Chloros

Kiekvienas iš šių pavyzdžių yra realus, patikrintas Chloros 1.3.0 elgesys — jie pašalina klasikinį kompiuterio parašytos automatizacijos gedimų režimą:

* **Nereikia sudėtingų nustatymų.**SDK „smart-connect“ pagalbinės funkcijos (`connect_camera`, `connect_array`, `connect_daq_sensor`) ir apdorojimo įėjimo taškai (`ChlorosLocal`, `process_folder`)**automatiškai paleidžia vietinį backendą**. Sukurtam skriptui nereikia atidarytos GUI ar rankiniu būdu paleisto serverio — jam reikia tik įdiegtos darbalaukio arba CLI paketo versijos.
* **Visas procesas vyksta vienu iškvietimu.** `chloros_sdk.process_folder("path", indices=["NDVI"])` nuo pradžios iki pabaigos atlieka importą → kalibravimą → atspindžio matavimą → indekso eksportą. Mažesnis paviršiaus plotas reiškia mažiau vietų, kuriose sukurtas scenarijus gali suklysti.
* **Vykdymo ciklai be išvesties atlieka savidiagnostiką.** Po `process()` vykdymo ciklo santrauka pridedama prie rezultato, o kiekviena apdorojimo užuomina (pvz., *kodėl* vykdymas nesukūrė išvesties) taip pat iš naujo pateikiamas kaip Python `UserWarning` — taigi net ir skriptas, kuris niekada netikrina rezultatų žodyno, pateikia diagnozę.
* **CLI'as aiškiai rodo klaidą.**`chloros-cli process` vykdymas, kuris prašė rezultatų, bet jų neužrašė, išspausdina `Processing finished but wrote no image products.` ir**baigiasi nelygiu nuliui kodu**, todėl apvalkalo skriptai ir CI tai aptinka paprastai patikrindami baigimo kodą. Sėkmingi vykdymai praneša apie `Image products written: N`, o jei buvo įrašytos kaukės – dar ir apie `(plus M clamp masks)`. Įrašymo komandos taip pat nurodo kiekvieną failą, kurio nepavyko įrašyti, ir baigia veikimą su kodu `1`.
* **Santykiniai keliai reiškia tai, ko tikisi skriptas.** Naudojant vietinį backendą, santykinis išvesties kelias (`-o out/` CLI, `output_dir="out/"` SDK) nustatomas pagal aplanką, iš kurio vykdoma komanda ar skriptas.

Viena asimetrija, kurią asistentas turėtų žinoti: SDK komandos „`process()`“ sąmoningai **nesukelia** išimties, kai vykdymo rezultatas yra nulinis — vietoj to ji pateikia ataskaitą per santrauką/patarimus. Jei Python procesų grandinė turi sustoti, kai vykdymo rezultatas yra tuščias, patikrinkite santrauką (taip daroma 2-ajame recepte).

## Įspėjimai

* **Reikalingas „Chloros+“ prisijungimas.**CLI ir SDK reikalauja**mokamo** „Chloros+“ lygio, kuris užtikrinamas serverio pusėje: užklausos baigiasi klaida `401 AUTH_REQUIRED`, jei nesate prisijungę, ir `403 PLAN_UPGRADE_REQUIRED`, jei naudojate nemokamą lygį. Prieš paleidžiant sukurtus skriptus, kiekviename kompiuteryje vieną kartą paleiskite komandą `chloros-cli login`. Žr. [„Chloros+“ prisijungimas](chloros+-login.md).
* **„Capture“ komandos valdo realią įrangą.** `lattice` / `daq` / `project` komandos, SDK sesijos objektai ir `chloros-mcp` įrankiai jungiasi prie fizinių kamerų ir jutiklių, perduoda duomenis iš jų ir juos aktyvuoja. Prieš pirmąjį paleidimą peržiūrėkite sukurtą skriptą ir paleiskite jį priežiūrint aparatinę įrangą.
* **Atlikite išvesties atrankinę patikrą.** Prieš skelbdami rezultatus, patikrinkite produkto aplankus ir keletą pikselių verčių. Visų pirma, atspindžio TIFF failai yra masteliuojami pagal šaltinį — perskaitykite `Chloros:PixelScale` XMP žymą (LATTICE failai ją turi: 32768 = 1,0 atspindys; Survey3 failai jos neturi ir naudoja 65535 = 1,0), o ne remkitės prielaida apie daliklį. Abiejuose informaciniuose puslapiuose tai aprašyta skyriuje „Atspindžio pikselių skaitymas“.
* **Smulkūs niuansai, dėl kurių gali sutrikti generuojamas kodas:**`pool-record` rašo į**pagrindinio kompiuterio** failų sistemą (numatyta reikšmė – `~/Documents/DAQ Live View/`); kompiuteriuose su keliais tinklo sąsajų įrenginiais geriau rinktis „`daq pool-connect --eth-host <ip-or-hostname>`“, o ne automatinį aptikimą; visur, kur pasirodo galinio serverio URL, naudokite „`http://127.0.0.1:5000`“ (niekada „`localhost`“); skriptas, kuris naršo projekto išvesties medį, turėtų praleisti `clamp_masks/` aplankus (juose esantys `*_clampmask.tif` failai yra kaukės, o ne vaizdai — žr. [Išvesties vaizdų formatai](output-image-formats.md#clamp-masks)); o Windows sistemoje, kurioje išjungti ilgi keliai, projekto aplankų pavadinimai turi būti trumpi — projekto aplankas, kurio pavadinimas ilgesnis nei maždaug 130 simbolių, atmetamas dar prieš pradedant apdorojimą.
