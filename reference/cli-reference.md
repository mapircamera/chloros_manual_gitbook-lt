# Chloros CLI vadovas

**Versija:**

1.3.0**Sukurta:**2026-07-29 19:19 ·**Atnaujinta:** 2026-11-06**Skaitytojai:** Optimizuota LLM naudojimui; suprantama žmogui.**Apimtis:** Visos vartotojui skirtos `chloros-cli` pakomandos su parinktimis ir pavyzdžiais, kuriuos galima nukopijuoti ir įklijuoti.

Šis dokumentas yra išsamus „`chloros-cli`“ komandinės eilutės įrankio, kuris pateikiamas kartu su „MAPIR Chloros“, vadovas. Jis sąmoningai parengtas išsamiai, kad LLM (arba žmogus) galėtų sudaryti bet kurį palaikomą darbo srautą iš žemiau pateiktų sąrašų, neperžiūrėdamas šaltinio kodo.

Jei jums reikia tik svarbiausių dalykų, pereikite prie:
- [Penkių minučių greitasis pradžios vadovas](#five-minute-quickstart)
- [„LATTICE“ kameros pirmojo prisijungimo darbo eiga](#lattice-camera-first-connect-workflow)
- [DAQ jutiklio pirmojo prisijungimo darbo eiga](#daq-sensor-first-connect-workflow)
- [„Smart-AE“ / „Smart-Capture“](#smart-ae--smart-capture)
- [Fiksavimo režimai, įrašymo įrenginiai ir apdorojimas neprisijungus](#capture-modes-recorders--offline-reprocess)

> **Kaip valdyti Chloros naudojant AI asistentą?** „Claude Desktop“, „Claude Code“, „Cursor“ ir kiti MCP klientai gali valdyti duomenų apdorojimą, „LATTICE“ kameras ir matricas bei DAQ šviesos jutiklius per `chloros-mcp`, MCP serverį, kuris įdiegiamas kartu su Python SDK (ir su Linux.deb, kaip nurodyta `/usr/bin`): `claude mcp add chloros -- chloros-mcp`. Nustatymai, parinktys ir įrankiai pateikiami SDK žinyno skyriuje [MCP serveris (AI asistentai)](sdk-reference.md#mcp-server-ai-assistants). Asistentas su komandinės eilutės aplinka taip pat gali tiesiogiai vykdyti kiekvieną šioje puslapyje nurodytą komandą.

> **Pakeista versijoje 1.3.0** (ką pastebi esamas 1.2.0 scenarijus ar darbo eiga).
>
> **Pakeitimai, kuriuos gali tekti atsižvelgti 1.2.0 scenarijuje:**
>
> - **Taikoma `--preset`.** `lattice capture`, `continuous`, `calibrate`, `multi-capture`, `multi-stream`, `align-calibrate`, `align-apply` ir `align-stream` buvo pašalinti 1.2 versijoje.0 ir veikė pagal pačios kameros nustatymus. Dabar jis nustato išankstinio nustatymo formatą, ekspoziciją, stiprinimą ir kadrų dažnį: `--preset high_speed` fiksuoja 8 bitų vaizdą 5 ms, 6 dB, 60 kadrų per sekundę. `lattice cam-connect --preset` taip pat išlaiko nustatymo formatą (1.2.0 versijoje kamera po nustatymo pritaikymo buvo nustatoma į 12 bitų). Žr. [Bendrosios parinktys](#common-options-shared-by-most-subcommands).
> - **„Bayer“ pikselių formatas M3M (mono) kameroje reiškia jos gylį.** Nustatymuose nurodyti „Bayer“ formatai, kurių mono jutiklis negali užfiksuoti: 1.2.0 versijoje kamera atmetė šį formatą ir išlaikė turimą, todėl „`--preset high_speed`“ ten nebuvo 8 bitų. Dabar tai yra to paties gylio „Mono“ formatas: „`high_speed`“ ir „`--pixel-format BayerRG8`“ suteikia „`Mono8`, o `high_quality`, `triggered` ir `BayerRG12` sudaro `Mono12`. Mišriame M3C + M3M masyve kiekviena kamera naudoja bendrą formatą savo šeimoje, o `network-analysis` / `analyze-array --pixel-format` kiekvieną kamerą priskiria tam tikram gylio lygiui. Tas pats galioja ir atvirkščiai: M3C, kuriai prašoma „Mono“ formato, naudoja to gylio „Bayer“ formatą, taigi masyvas, kurio pagrindinė kamera yra M3M, nebeperjungia savo M3C kamerų į „Mono“. Žr. [„Mono“ (M3M) vienos juostos kameros](#mono-m3m-single-band-cameras).
> - **`lattice array-capture` eksportai pagal numatytuosius nustatymus yra suderinti, kaip ir programos eksportai.** Kiekvieno nario `debayered`, `display`, `radiance`, `reflectance` ir indeksiniai failai yra iškraipomi pagal pagrindinės kameros kadrą ir apkarpyti iki narių persidengimo (mažesnės už jutiklį dėl kamerų poslinkių, `XMP-mapir:AlignmentJSON` `"applied": true`), taigi CLI užfiksuoti failai yra tokie patys kaip ir Chloros programėlės užfiksuoti tos pačios scenos failai; `raw` išlieka natūralaus dydžio. Anksčiau įrašas be `--aligned` kiekvieną failą išsaugodavo natūralaus dydžio, neiškreiptą. `--no-aligned` išlaiko šią funkciją. Tas pats numatytasis nustatymas taikomas `--smart`, `--continuous` ir `--interval`, taip pat SDK ir `chloros-mcp`. Žr. [`array-capture` režimai](#array-capture-modes).
> - **`lattice array-capture --compression` numatytasis nustatymas yra `none`.** Failai rašomi nesuspausti (apie 6,3 MB vienam pilnos raiškos kadrui), kad būtų pasiektas greičiausias nuolatinis įrašymo greitis; 1.2.0 versija rašė `deflate` (apie 4,1 MB). Norėdami gauti mažesnius failus, nurodykite „`--compression deflate`“. Abu variantai yra be nuostolių.
> - **PNG ir JPEG formatai priima tik „`display`“ / „`preview`“ ir indeksuotus produktus.** `--format png` arba `jpg` įrašymas su lygiu, kurio 8 bitų failas negali talpinti, atmetamas dar prieš pradedant įrašyti (HTTP 400, išėjimas `1`), taip pat atmetamas numatytasis `--processing all` su `--format png`/`jpg`. Žr. [Fiksuojamų duomenų eksporto lygiai](#capture-export-levels-the-all-default).
> - **`lattice dls` reikalauja `--mac`.**Nėra daugiau numatytojo MAC adreso (jis buvo vieno įrenginio adresas); `daq pool-discover --only ble` suranda jutiklio adresą.**`lattice dls --camera SERIAL`** yra naujas.
> - **Dėl daugiau gedimų išeinama iš `1`.** `process`, kai neįmanoma patikrinti vykdymo eksportų arba prarandamas vaizdas, nors kiti produktai yra įrašyti; užfiksavimo komanda su bet kuriuo failu, kurio nepavyko įrašyti (kiti failai vis tiek išsaugomi); `lattice continuous` ir `multi-stream`, kai diskas užpildytas; `array-burst` / `array-record`, kai įrašymo įrenginys praneša apie klaidą; `array-build-video`, kai prašytas produktas buvo praleistas arba nesukurtas kaip prašyta; `project burst` / `stream`, kai įrašymas nepavyksta iki galo; `project sensor log`, kai įraše nėra jokių duomenų; `lattice exposure --set`, `gain --set` ir `configure --set`, kai kamera atmeta reikšmę. `lattice network-analysis --json` išnyksta kartu su suvestine (`1` už `needs_force_slip`). Atvirkščiai: `daq pool-disconnect`, susijęs su jutikliu, kurio nebėra sąraše, užbaigia veikimą `0`X. Žr. [Išeitimo kodai](#exit-codes).
> - **Lėtas „`lattice array-connect`“ niekada nesibaigia klaida, kai masyvas yra prijungtas.** Jis laukia iki 600 s (180 s versijoje 1.2.0; `project connect` – 90 s), eigoje išspausdindamas užkulisio veiksmą, o iki to laiko neužbaigtas prisijungimas yra atšaukiamas užkulisio — kiekviena kamera grąžinama į tą pačią būseną, kokioje buvo rasta — taigi `Error:` (išėjimas `1`) reiškia, kad masyvas neprijungtas. Versijoje 1.2.0 prisijungimas iškart po kamerų įjungimo galėjo trukti ilgiau nei laukimo laikas ir prijungti masyvą po to, kai komanda nepavyko. Dabar „Ctrl+C“ atšaukia prisijungimą. Žr. [Nuolatiniai prisijungimai](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-connect` išlaiko visą jutiklį.** Jei nenaudojami `--width` / `--height` / `--binning`, kiekviena kamera nustatoma į visą jutiklį, o kadrų dažnis sumažėja iki to, ką gali užtikrinti šis kompiuteris ir tinklas; prisijungimo išvestyje pateikiamos greitesnės galimybės (kadrų sujungimas arba mažesnė ROI) su numatomais kadrų dažniais. 1.2.0 versijoje kadrai buvo suspausti arba sujungti, siekiant išlaikyti aukštą kadrų dažnį. Žr. [`array-connect` parinktis](#array-connect-options).
> - **Lėtas tinklas nebeblokuoja masyvo veikimo.** Kiekvienos kameros veikimas pritaikomas prie jai tenkančios tinklo pralaidumo dalies, o kadrų dažnis mažėja atitinkamai. Jei kamerų veikimo greičio negalima taip sulėtinti, masyvas veikia serijinio perdavimo režimu (`slip-emit-and-capture`) režimu: kiekvienas kadras pasiekia tikslą, tačiau kameros nėra eksponuojamos vienu metu. `--force-tier sim-capture-sim-emit` reikalauja vienalaikės ekspozicijos. Žr. [Matricos kadrų dažnis ir serijinio fotografavimo modelis](#array-fps--burst-model).
> - **`lattice network-analysis` / `analyze-array` nurodo, kokia „connect“ taisyklė taikoma**: be `--pixel-format` nustatomi „connect“ nustatyti formatai (Mono12 / BayerRG12, anksčiau buvo BayerRG8) ir sprendimas dėl [supakuoto 12 bitų](#packed-12-bit-transport---packed). Todėl ta pati komanda gali nurodyti kitokį kadrų dažnį nei 1.2.0 versijoje.
> - **Santykiniai keliai nurodomi pagal vietą, kurioje vykdote komandą.** `-o` ir kiti keliai, kuriuos atidaro užkulisinė programa (`--input`, `--burst-dir`, `--profile`, `--save`, `--mask`), recepto žingsnio `output`, o numatytasis `output/` iš `lattice index --live` nustatomi pagal aplanką, iš kurio paleidžiate `chloros-cli`. 1.2.0 versijoje užkulisinė sistema juos nustatydavo savo įdiegimo aplanke, o įdiegtoje versijoje tai baigdavosi klaida „Prieiga uždrausta“. Žr. [Konvencijos](#conventions).
> - **`lattice index` veikia užkulisinėje programoje bet kokia forma**, kaip ir kitos `lattice` komandos, todėl pirmiausia paleiskite užkulisinę programą. Neprisijungusio indekso (`--input`) apskaičiuoja užkulisinė programa, kuri nuskaito TIFF failą ir įrašo rezultatus šalia jo arba į `-o`, o „`--list-presets`“ / „`--list-gradients`“ išspausdina foninio proceso lenteles. Windows sistemoje dabar jos veikia: anksčiau Windows CLI į bet kokią „`lattice index`“ formą, įskaitant „`--live`“, atsakydavo „`Error: lattice_sdk not available`“. Linux sistemoje neprisijungus veikiantis indeksas anksčiau veikdavo CLI viduje be užkulisio ir be prisijungimo; dabar jam reikalingi abu, kaip ir kiekvienai kitai CLI komandai. Žr. [Indeksas / Augmenijos skaičiavimai](#index--vegetation-maths).
> - **Nauji failai šalia produktų.** Kiekvienas spinduliavimo, atspindžio ir indekso produktas, kuriame juostos atskyrimo procesas apribojo pikselį, gauna kaukę `clamp_masks/` aplanke šalia produkto aplanko, o spausdinimo ataskaitoje įrašoma eilutė `clamp mask:`. Žr. [Juostų atskyrimo apribojimo kaukė](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on).
> - **Kameros orientacija.** Įrašant vienos kameros vaizdą, failai „`raw`“ ir „`debayered`“ išsaugomi taip, kaip juos nuskaitė jutiklis, o kameros apvertimas / pasukimas įrašomas į XMP; „`process`“ tai pritaiko prie sukurtų produktų. Masyvo narių failai išlieka nepakeisti, nebent perduosite naująjį `--orient` (`array-capture`, `array-burst`, `project capture` / `burst` / `stream`). Žr. [Kameros orientacija](#kaip-atrodo-užfiksuotų-nuotraukų-aplankas).
> - **Eksporto lygio pavadinimas, kuris nėra vienas iš nurodytų, yra atmestas.** `lattice capture --processing` ir kiekvienas `--levels` sąrašas priima `raw`, `debayered`, `radiance`, `reflectance` arba `display` (arba aliasą, pvz., `preview`; `--processing` taip pat `all`). Bet koks kitas pavadinimas atmetamas prieš paliečiant kamerą (HTTP 400 `UNKNOWN_LEVEL`, išėjimas `1`); 1.2.0 išsaugojo `raw` ir užbaigė veiklą `0`.
> - **`daq pool-set-cap` reikalauja `--cap-id`.** Be jo komanda užsidaro `2`; 1.2.0 pašalino dangtelio korekciją, palikdama pliką jutiklį. `--cap-id none` prašo paprasto jutiklio.
> - **`lattice resolution --binning-mode` nebeegzistuoja.** Pikselių sujungimas visada yra „Vidurkis“; žymė vis dar priimama, išspausdina įspėjimą ir nieko nedaro. Žr. [Jutiklio derinimas](#sensor-tuning).
> - **`process --debayer texture-aware` atmetamas, jei naudojami „LATTICE“ įrašai.** Jei aplanke yra „LATTICE“ užfiksuotų vaizdų, `process` uždaro `1` po importavimo, nurodydamas, kiek jų yra, prieš pradėdamas bet kokį apdorojimą. „LATTICE“ užfiksuoti vaizdai visada apdorojami standartiniu demozėjimo būdu, todėl 1.2.0 versija priėmė šią parinktį ir ją ignoravo; Chloros programa taip pat jos nesiūlo šiems vaizdams. Žr. [`chloros-cli process`](#chloros-cli-process).
> - **Kameros grįžta į tą pačią būseną, kokioje buvo rastos — išskyrus atvejus, kai naudojamas `--keep-settings`.** `lattice array-disconnect`, kiekviena laikina komanda (`multi-capture` be masyvo, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`) bei `lattice continuous` grąžina kiekvieną kamerą į tą pačią būseną, kokioje ji buvo rasta: jos užrakto ir sinchronizavimo linijos laidus, PTP bei visus sesijos metu pakeistus kameros nustatymus (ekspoziciją ir stiprinimą, AE tikslą, ribas ir slopinimą, baltos spalvos balansą, juodos spalvos lygį, pikselių formatą, pikselių sujungimą ir ROI, apverstį, kadrų dažnį). `--keep-settings`, priešingai, išsaugo kiekvienos kamerosnustatymus į pačios kameros atmintį, todėl ji įsijungia su jais; `--clear-settings` atkuria gamyklinius tos atminties nustatymus. Bet kuriuo atveju kameros paliekamos veikti laisvai. Projektas, prisijungęs prie kamerų, nustato jas pagal savo išsaugotus nustatymus. Žr. [Kameros atmintis](#camera-memory----keep-settings----clear-settings).
> - **Kiekvienas režimas projektą išsaugo tuo pačiu būdu.** Bet koks projekto rėmuose prijungtos kameros, matricos ar šviesos jutiklio pakeitimas — atliktas per programėlę, CLI, SDK ar MCP serverį — išsaugomas to projekto `cameras.json` / `sensors.json`, o atidarius projektą bet kuriame režime, nustatomi jame esantys įrenginiai. Anksčiau projektą išsaugodavo tik programėlė, o jos kitas išsaugojimas perrašydavo viską kitą. Žr. [Vienas projekto įrašas, kiekvienas režimas](#one-project-record-every-mode).
> - **Iš kelių kamerų sukurti failai nurodo kiekvieną iš jų.** Daugiabandis rinkinys (`align-apply`, `align-stream`, `index --live --save-multiband`), iš kelių fotoaparatų juostų sudarytas tiesioginis indeksas ir masyvo užfiksavimo vaizdas `composite/` pateikia kiekvieno šaltinio fotoaparato serijos numerį, modelį ir kalibravimo paketą sąrašų pavidalu (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …), o EXIF `SerialNumber` pateikia serijinius numerius, sujungtus kableliais. Anksčiau juose nebuvo nurodyta jokia kamera arba buvo nurodytas tik pirmosios kameros serijos numeris. Žr. [Iš kelių kamerų sukurti failai](#files-made-from-several-cameras).
> - **Tik `raw` failas turi kameros kalibravimo žymes.** „MicaSense“ suderinamas rinkinys (`BlackLevel`, `RadiometricCalibration`, kiekvienam kanalui skirtas `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*` rinkiniai) bei `CalibrationJSON` duomenų blokas apibūdina jutiklio neapdorotus duomenis, todėl jie yra tik `raw` įrašymo faile. Visi kiti failai — įrašymo failai „`debayered`“, „`display`“, „`radiance`“, `reflectance` bei indeksų failai, `lattice continuous` ir `multi-stream` kadrai, ir kiekvienas `process` produktas — turi kameros identifikatorių (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...), o užfiksuotame kadre – paties rėmo duomenis (`XMP-mapir:FrameJSON`: ekspozicija, stiprinimas, temperatūra, pikselių formatas, užfiksavimo laikas). 1.2.0 versijoje jie taip pat turėjo neapdorotų duomenų rinkinį, kurį taikanti priemonė (Pix4D, Metashape) taikė pikseliams, kurie nėra neapdorotų verčių. `process` nustato tokio failo kalibravimą pagal jo serijinį numerį ir `CalBundleSha256`.
>
> **Kiti pakeitimai:**
>
> - **Matricos automatinis kadrų dažnis nustatomas pagal tai, ką pateikia matrica.** Dažnis, prarandantis daugiau nei 1 % savo suaktyvinimų, sumažinamas, vertinant per kelis ilgus stebėjimus, o ne vieną, o dažnis, nuo kurio jis buvo sumažintas, kartkartėmis bandomas iš naujo. Jūsų pačių nustatytas dažnis lieka nepakitęs.
> - **Automatinė ekspozicija tapo stabilesnė.** Ji išlaiko pastovų apšvietimą, prisitaiko per kelias sekundes po pokyčių, ignoruoja nedidelius peršvietimus ir atgaivina fotoaparatą, kuris nustoja reaguoti esant ryškiai saulei. Žr. [Kaip veikia automatinė ekspozicija](#how-auto-exposure-behaves).
> - **`process` skaičiuoja tik jūsų nuotraukas ir įrašus.** Pakartotinai paleidus programą su aplanku, kuriame yra projekto `camera_cal/`, tie kalibravimo failai nebebus skaičiuojami (pranešimas „Rasta 96 nuotraukų“ 90 nuotraukų rinkinyje), o projektas, kuris juos jau įtraukė, juos pašalins.
> - **`lattice multi-test` perima kamerų įjungimo būseną.** Jis įjungia kamerų PTP vykdymo metu, laukia, kol jų laikrodžiai susinchronizuos ir stabilizuosis prieš pirmąjį suaktyvinimą — kiekviena kamera sinchronizuojama 120 s, t. y. maždaug 2 minutes nuo įjungimo (`--ptp-settle SECONDS` jį nustato), nes ką tik sinchronizuotas laikrodis apie 2 minutes nukrypsta iki ~200 us — ir grąžina kiekvienos kameros PTP į tą būseną, kokią rado. Kameros, kurios vis dar yra sinchronizuotos iš ankstesnio bandymo, nebelaukiama iš naujo. Anksčiau PTP būdavo išjungiamas vieno komutatoriaus tinkle, kuriame neatsakydavo joks DAQ-E, o kiekvienos kameros laisvai veikiantis laikrodis būdavo traktuojamas kaip PTP laikrodis, kuris nesusinchronizuotų (`PTP is not locked`, kiekvienas paleidimas). Žr. [Daugiakamerinis režimas (trumpalaikės sesijos)](#multi-camera-transient-sessions).
> - **`.daq` įrašas nurodo, ar dangtelis buvo pasirinktas, ar numatytas.** „DAQ-U“, „DAQ-M“ ir „DAQ-E“ tiekiami be dangtelio, o Chloros negali matyti, ar jis yra uždėtas, todėl jutiklis, prijungtas be pavadinto dangtelio, pagal numatytuosius nustatymus gauna „`sunshine_cosine`“ – tai *prielaida*. Kiekviename Chloros įraše (schema 1.30) dabar šalia „`cap_id`“ saugomas „`cap_id_source`“: `operator`, jei dangteliui suteikėte pavadinimą (įskaitant `none`), ir `auto_default`, jei buvo naudojamas numatytasis. Apdorojant įrašą, kurio dangtelis buvo numatytas, o projekto DAQ dangtelio nustatymas paliktas „Auto“, išspausdina `[daq-dls] WARNING`, nurodantį failo pavadinimą: paprastas DAQ-U su saulės spindulių korekcija rodo maždaug 30 kartų didesnę vertę. **Pavadinkite įdėtą ribą** prisijungdami arba nustatykite projekto DAQ ribą, ir įspėjimas išnyks.
> - **Windows sistemoje nebėra „pip“ konsolės langų.** Kompiuteryje, kuriame įdiegtas Python, užkulisinė programa ir kiekvienas jos vaizdų apdorojimo procesas paleidimo metu vieną kartą paleisdavo „`pip`“, ir kiekvienas atidarydavo konsolės langą. Dabar to nebedaro.
> - **Tiesioginiai „`.daq`“ įrašai išlieka net ir nutraukus elektros tiekimą.**Įrašas rašomas su žurnalu ir visišku disko sinchronizavimu po kiekvieno įrašymo, už jutiklio, todėl lėtas diskas vėluoja tik failą, bet niekada – matavimų duomenis. Įrašas, sustabdytas įrašymo viduryje (dėl elektros tiekimo nutraukimo ar priverstinio uždarymo), gali palikti šalia savęs `<name>.daq-journal`:**sujunkite juos abu.** Chloros atkuria žurnalą pirmą kartą perskaitęs failą, todėl įrašas atidaromas su visais duomenimis, įrašytais iki sustabdymo (prarandama apie paskutines 3 sekundes); 1.2.0 versijoje elektros tiekimo nutraukimas netinkamu momentu galėjo padaryti visą failą neskaitomą („duomenų bazės disko atvaizdas yra netinkamos formos“).
> - **Įrašymas į užimtą diską nebeužstrigdo jutiklio.** `daq pool-record` surenka rodmenis į eilę ir įrašo juos atskirame sraute; rodmuo, kurio nepavyko įrašyti, įskaitomas (`daq pool-list` rodo `N write error(s)` su paskutine klaida), o ne sustabdo jutiklio veikimą (vienas įrašymas truko 8,4 s, kai keturios kameros išsaugodavo TIFF failus).
> - **`lattice multi-capture` be masyvo nebepraranda išsaugotų kadrų, kai pasibaigia trigerio laiko limitas.** Pasibaigus kameros trigerio laiko limitui (`SC_ERR_TIMEOUT -1011`, pastebėta su keturiomis kameromis, prijungtomis prie vieno 1 GbE komutatoriaus), veikimas baigiamas toje kadrų grupėje: jau išsaugotos grupės išvardijamos su `Saved:`, tada rodoma klaida, išėjimas `1`. 1.2.0 versijoje buvo rodomas HTTP 500 kodas be sąrašo, nors failai buvo diske.
> - **Programiškai valdomos kameros nebepraleidžia kas antrojo sužadinimo signalo.** Programa valdomos kameros dabar į eilę įtraukia sužadinimo signalą, kuris gaunamas jos duomenų nuskaitymo metu (arba Chloros laukia, kol ji bus pasirengusi), kaip tai jau darydavo kabeliniu būdu sinchronizuotos kameros.
> - **`daq pool-discover` nurodo DAQ-E-S `DAQ-E-S`** savo stulpelyje „`MODEL`“ (o „`--json`“ – „`model`“). Kiekvienam „Ethernet“ jutikliui buvo nurodyta „`DAQ-E`“; tas, kuris per maždaug 2 s neatsako į tapatybės patikrinimą, vis tiek yra įtrauktas į sąrašą kaip „`DAQ-E`“.
> - **Patobulinti LATTICE indeksų produktai** (NDVI ir kiti indeksai) iš `process`, visuose apdorojimo režimuose.
> - **OCN kameros: patobulinti spinduliavimo, atspindžio ir indeksų produktai esant saulės šviesai.** Po juostų atskyrimo taikoma parkų korekcija; RGN kameros lieka nepakitusios, o OCN reikšmės skiriasi nuo 1.2.0 versijos. Tai taikoma ir OCN įrašams, padarytiems naudojant ankstesnę versiją, jei jie apdorojami kompiuteryje, kuriame saugomi kalibravimo duomenys, naudoti įrašymo metu (Chloros išsaugo kameros kalibravimą, kai kamera yra prijungta).
> - **`lattice continuous` įrašo neapdorotą kadrą.** `tiff` vykdymo metu buvo įrašyti 3plokštumų kadrus, nuo kurių „`process`“ negali pradėti. Dabar jo kadrai yra „`raw`“ failas, kurį įrašo masyvo įrašymo programa: viena mozaikos plokštuma su pačio jutiklio reikšmėmis, nesuspausta, su kalibravimo paketu. Skriptas, kuris iš šių failų skaito tris plokštumas, perskaito tik vieną.
>
> - **Kiekvienas įrašymas išsaugo naudotą DAQ rodmenį, kaip tai daro GUI.** `lattice capture`, `lattice continuous` (po vieną `.daq` kiekvienam kadrui), `array-capture` ir `multi-capture` su prijungtu masyvu (ir SDK) įrašo `.daq` ir jo `.match.json` kiekvieną kartą, kai DAQ yra susietas arba įtrauktas į grupę, įskaitant ir tik neapdorotų duomenų fiksavimą; 1.2 versijoje0 versijoje neapdorotas arba debayeringuotas įrašas išsaugomas tik su `--force-daq`. `--no-daq` išjungia šią funkciją, `--daq-tolerance-ms MS` nustato atitikimo toleranciją tam įrašui, o naujasis `daq pool-bind` iš CLI susieja kamerą arba masyvą su DAQ. `.daq` užfiksuoti duomenys `process` suderinami tik su jame nurodytomis kameromis. Dabar įrašas laukia kito DAQ rodmens, kad galėtų sujungti rodmenis iš abiejų ekspozicijos pusių. Žr. [DAQ rodmenys, kuriuos išsaugo įrašymas](#the-daq-reading-a-capture-saves) ir [DAQ rodmenys, esantys toli nuo ekspozicijos](#a-daq-reading-far-from-the-exposure).
> - **`process --indices NAME`** prideda indeksą į projektą kiekvienos kameros juostoms, įskaitant naują projektą. Versijoje 1.2.0 pavadinimas, kurio dar nebuvo projekto sąraše, buvo ignoruojamas naudojant `Unknown index name(s) ignored`.
> - **`process` šalia tiesioginio masyvo:**nebepaleidžia iš naujo kameros ar DAQ įrenginius valdančio užkulisinio proceso ir laukia iki 15 s, kol baigsis vykstantis ciklas.**Ctrl+C** sustabdo ciklą užkulisiniame procese, o ne tik CLI. Žr. [`process` pastabas](#pastabos).
> - **Projekto aplankas, kurio kelias per ilgas, kad tilptų į „Windows“ 260 simbolių ribą**, iš karto atmetamas, pasirenkant ilgiausią aplanką, kuris tilptų, o ne vykdant operaciją, kuri nieko neįrašo. Windows diegimo programa dabar siūlo parinktį „Įjungti ilgus Windows kelius“ ir „Microsoft Defender“ išimtį užpakalinės dalies atžvilgiu; abi parinktys pažymėtos pagal numatytuosius nustatymus. Žr. [Windows (.exe)](#windows-exe).
> - **`lattice array-capture --continuous`** paleidžia pačios užkulisinės programos nuolatinį duomenų surinkimą: pažangos juostą kas 2 s, santrauką vietoj sąrašo pagal atskirus failus ir išėjimą su nelygiu nuliui kodu, jei yra neįrašytų failų arba užpildytas diskas. Žr. [`array-capture` režimai](#array-capture-modes).
> - **Pirmasis „`array-capture`“ po „`array-connect`“** laukia iki 20 s, kol gaus pirmąją masyvo kadrų grupę, o ne rodo klaidą „HTTP 503: Masyvas vis dar šyla“.
> - **Matricos ekspozicija pagal ROI:** mažas ROI nebeapriboja matricos elemento ekspozicijos iki jutiklio laisvojo veikimo laikotarpio (4,9 ms esant 768 × 304). Žr. [Ekspozicija matricos elemente](#exposure-on-an-array-member).
> - **Automatinė ekspozicija nebeapriboja matricos automatinio kadrų dažnio.** Kabeliu sinchronizuota matrica veikia tokiu dažniu, kokį leidžia tinklas ir kamerų duomenų nuskaitymas; automatinė ekspozicija užpildo tą laikotarpį, kurį suteikia tas dažnis, tada padidina stiprinimą. Rankinė ekspozicija, ilgesnė už šį laikotarpį (`lattice exposure --set`, `configure --set`), sulėtina suaktyvinimą, kad jis tilptų į šį laikotarpį, o ne būtų atmestas, ir dažnis atsistato, kai elementas grįžta į automatinį režimą. Žr. [Ekspozicija masyvo naryje](#exposure-on-an-array-member).
> - **Masyvai pateikia mažiau neišsamių kadrų grupių** ir rečiau rodo 503 klaidą („Nėra naujos sinchronizuotos kadrų grupės“): kiekvienos pavaldžiosios kameros paleidimo įvestis dabar yra apsaugota nuo trikdžių.
> - **Suspaustas 12 bitų perdavimas** (`--packed`, numatytasis `auto`) padidina viso jutiklio masyvo kadrų dažnį maždaug trečdaliu. Dabar taikomas `array-connect --packet-size` (versija 1.2.0 jį priėmė, bet ignoruodavo). Žr. [Suspakuotas 12 bitų perdavimas](#packed-12-bit-transport---packed).
> - **Matrica, kurios kamerų maitinimo ciklas atsinaujina savaime.** Po komutatoriaus maitinimo ciklo `array-connect` matrica-vėl atidaro kameras, kai jos atsako, ir vėl transliuoja su vėl įjungtu PTP; versijoje 1.2.0 jis visam laikui sustabdydavo veiklą ir reikėjo naujo `array-connect`. `array-status` praneša apie `ptp_enabled: False` (ir `ptp_off:`), kai kamera nuskaito, kad PTP išjungtas, o anksčiau pranešdavo apie nustatymą, su kuriuo buvo prijungtas masyvas. Žr. [Nuolatiniai ryšiai](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-record` įrašo, kai GUI nėra atidaryta.** Uždarius GUI sujungtą vaizdą, užkulisinė sistema pati atkuria vaizdą: masyvoNDVI arba indeksą, kurį perduodate su naujuoju `--expr`. Įrašas, kuris nerašo jokių kadrų, dabar baigiasi nesėkme (`no-frames: …`, išeina iš `1`). Anksčiau įrašymas vyko tik tada, kai buvo atidarytas GUI sujungtas vaizdas, o įrašymas, kuris nieko neįrašydavo, vis tiek užbaigdavo `0`. Žr. [`array-record`](#array-record--combined-index-videogif-monitoring-grade).
> - **Kamera, kurią Chloros jau laiko, antrą kartą neatidaroma.** Komanda, kuri pati ją atidarytų, vietoj to atsako HTTP 409 `CAMERA_HELD`, nurodydama, kas ją laiko, ir komandą, kuri ją atlaisvina. Tai apima ir kamerą, kurią šiuo metu naudoja kita komanda (pavyzdžiui, „`multi-test`“ arba „`multi-capture`“, atidarančios savo kameras, arba „`lattice capture`“): antroji komanda atmetama, kol pirmoji nebaigs veikti. Jei ją atidarytumėte dar kartą, tai galėtų sustabdyti turėtojo srautą arba sugadinti užkulisinę sistemą. Žr. [Nuolatiniai ryšiai](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **Laikinieji kelių kamerų komandos perduoda visų kamerų vaizdą arba nepavyksta.** `lattice multi-capture` be prijungto masyvo, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream` ir `index --live` išeina iš `1`, nurodydami kamerą, kuri neperdavė jokio kadro. Srautai praleidžia kadrų grupę, kurioje trūksta kameros, ir sustoja po trijų iš eilės; `multi-test` išspausdina eilutę už kiekvieną suveikimą, `(no frame)` nurodydamas, kur kamera nepristatė kadro; `align-stream` taip pat išeina iš `1` dėl užduoties klaidos. Tada kiekvienas vykdymo ciklas grąžina kiekvieną kamerą į tą būseną, kokią rado: su jos sužadinimo ir sinchronizavimo linijų laidyba, jos PTP (`PtpEnable`) ir visus vykdymo metu pakeistus kameros nustatymus — ekspoziciją ir stiprinimą, AE tikslą, ribas ir slopinimą, baltos spalvos balansą, juodos spalvos lygį, pikselių formatą, pikselių sujungimą ir ROI, apverstymus, kadrų dažnį — išskyrus atvejus, kai `--keep-settings` / `--clear-settings` (žr. [Kameros atmintis](#camera-memory----keep-settings----clear-settings)). Anksčiau šios komandos išsaugodavo visus gautus kadrus ir išeidavo iš `0` (`align-stream` išspausdindavo užduoties klaidą kaip `Note:`), ir palikdavo kiekvieną kamerą `TriggerMode` `On`. Jos taip pat nustatydavo kiekvienos kameros ekspoziciją pagal sceną prieš išsaugodamos kadrą (daugiausia kelias sekundes), o tikslinėje vietoje prijungtas masyvas kiekvieną kamerą paleidžia su šiais nustatymais: 40 multispektrinių, 60 RGB (nustato „`--brightness N`“), ir išlaiko šiuos parametrus visą veikimo laiką; taip pat elgiasi ir viena kamera Chloros, kurios `lattice capture` nevaldo. Anksčiau kiekvienos kameros pirmasis kadras būdavo fotografuojamas pagal pačios kameros ekspozicijos ribą, apie 100 ms, nepriklausomai nuo apšvietimo, ir dažnai būdavo apkarpytas. Žr. [Daugiakamerinis režimas (trumpalaikės sesijos)](#multi-camera-transient-sessions).
> - **Trumpalaikės daugiakamerinės komandos dalijasi tinklu taip, kaip tai daro „`array-connect`“, ir atidaro tik „`--serials`“.** Kiekvienai kamerai skiriama jos dalis pagrindinio ryšio kanalo pralaidumo, taigi keturios kameros veikia 1 Gbps ryšio kanale; anksčiau visos keturios kartu reikalavo 125 MB/s pralaidumo, visos naudodamos 1 GbE, ir kiekviename kadre prarandami paketai. Tinklas atsisako perduoti tokį duomenų masyvą, kurio negali pernešti, dar prieš pradedant bet kurios kameros transliaciją. Dabar „`--serials`“ pasirenka kameras, o pirmoji iš jų suaktyvina trigerį; anksčiau kiekviena komanda atidarydavo visas tinkle esančias kameras, nepriklausomai nuo to, ką nurodydavo „`--serials`“. Gavus pranešimą apie GVSP paketų praradimą, dabar tai aiškiai nurodoma, o ne kaltinamas M8 sinchronizavimo kabelis. Žr. [Daugiakamerinis režimas (trumpalaikės sesijos)](#multi-camera-transient-sessions).
> - **`lattice array-disconnect` palieka kameras veikti laisvai, kaip buvo nustatyta.** `TriggerMode`, `Off` ir viskas kita, kaip nustatyta prisijungimo metu — trigerio ir sinchronizavimo linijos nustatymai (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`), PTP ir sesijos metu pakeisti kameros nustatymai – taigi vienukameros komanda, skirta buvusiam nariui, iš karto gauna kadrus, o pagrindinė kamera nebėra palikta `TriggerSource`, `Software`. `--keep-settings` išsaugo sesijos nustatymus kiekvienos kameros atmintyje, o `--clear-settings` išvalo tą atmintį (žr. [Kameros atmintis](#camera-memory----keep-settings----clear-settings)). `lattice continuous` taip pat nustato, kad kamera, kurią jis aptinka įjungtą, bėgimo metu veiktų laisvojo bėgimo režimu (o po to grąžina ją į pradinę būseną), o bėgimas be rėmo baigiasi `1`.
> - **`lattice configure --get` / `lattice exposure`** išspausdina `note:` su kamerostikrąja ekspozicija arba stiprinimu, kol veikia automatinis režimas. Atmestas `lattice exposure --set` / `configure --set` nurodo diapazoną, kurį kamera priima, ir grąžina automatinį režimą, o ne išjungia automatinę ekspoziciją. Žr. [Jutiklio derinimas](#sensor-tuning).
> - **Nauji kameros valdymo elementai:**`lattice highlights` (parodo ryškiai baltus paviršius RGB-ekrano atvaizde) ir `lattice exposure --brightness-auto`.**`lattice multi-detect`** per vieną zondavimo ciklą suranda laidines kameras ir numato, kad pagrindinė kamera bus RGB.
> - **Naujos DAQ komandos:** `daq pool-bind`; `pool-enable-ptp`; o DAQ-E įrenginio nustatymams – `pool-info`, `pool-set-network`, `pool-set-auto-stream`, `pool-reboot` ir `pool-update-firmware`. Žr. [„DAQ-E įrenginio nustatymai“](#daq-e-device-settings).
> - **DAQ-E, kuris nustoja perduoti duomenis, yra iš naujo prijungiamas per užkulisinę sistemą.** Kai sujungto į grupę DAQ-E srautas nutyla (dėl maitinimo jungiklio išjungimo ir įjungimo, nutrūko kameros ryšys), serveris jį vėl prijungia su ankstesniais nustatymais, bandydamas prisijungti vis ilgesniais intervalais, kol jis neveikia. Įrašymas tęsiamas tame pačiame `.daq` faile, o pertrauka pažymėta faile, o `pool-record --stop` išvardija kiekvieną pertrauką. 1.2.0 versijoje jutiklis likdavo nutilęs iki `pool-disconnect` + `pool-connect`, o tai taip pat nutraukdavo įrašymą. Žr. [„DAQ-E, kuris nustoja perduoti duomenis“](#a-daq-e-that-stops-delivering).
> - **`time-sync status`** praneša, ar PTP „grandmaster“ paketai pasiekia tinklą (`health`), o jei nepasiekia – į stderr išvedamas `WARNING:`. Žr. [`time-sync`](#chloros-cli-time-sync).
> - **Linux .deb:** padidina branduolio lizdų priėmimo buferius, kurių reikia kameros srautams (esant branduolio numatytiesiems nustatymams, masyvai praranda kadrus ir kadrų dažnį), prideda užkulisinę paslaugą, veikiančią jūsų vartotojo vardu, o `lattice network` tikrina buferius. CLI niekada nestabdo paslaugos vykdomos užkulisinės programos, o atnaujinimas vėl paleidžia įjungtas užkulisines paslaugas. Žr. [Užkulisinės programos vykdymas kaip paslauga](#running-the-backend-as-a-service-headless) ir [Linux kompiuteriai — lizdo priėmimo buferiai](#linux-hosts--socket-receive-buffers).
> - **`lattice array-capture` nurodo, kai nepavyko suderinti:**kiekviena priežastis, dėl kurios įrašas buvo išsaugotas nesuderintas (nėra suderinimo profilio, elementai skirtingomis skiriamosiomis gebomis, masyvas nesinchronizuotas su kabeliu, rėmelis, į kurį profilio neįmanoma priskirti) išspausdinama kaip `Alignment:` eilutė.**`chloros-cli daq help`** yra `daq --help` (jis užbaigė `2`), o Linux pagalboje išvardytos tiesioginės aparatinės įrangos `daq` komandos , kurias apima šis kompiliavimas.
> - **Jetson:** paketas yra `chloros_1.3.0_arm64_jp6.deb`, skirtas „JetPack 6“, o jo GPU apdorojimas vyksta „Orin“ su „JetPack“ CUDA PyTorch. Žr. [Linux arm64 — Jetson](#linux-arm64--jetson-jetpack-6).

---

## Konvencijos

- Visų komandų priešdėlis yra `chloros-cli`. Windows sistemoje binarinis failas yra `chloros-cli.exe`; Linux/„Jetson“ – „`chloros-cli`“.
- Pasirinktiniai argumentai nurodomi kaip „`--flag`“. Privalomi poziciniai argumentai nurodomi be skliaustelių.
- Jei nurodyta numatytoji reikšmė, nepateikus žymės bus naudojama ta reikšmė.
- CLI yra HTTP klientas, veikiantis per Chloros užkurtį (serverį, esantį adresu `127.0.0.1:5000`, kurį taip pat naudoja darbalaukio programa). **Kokios komandos jį paleidžia:**
  - Pagrindinės komandos — `process`, `login`, `logout`, `status`, `export-status`, `time-sync` ir `selftest` — naudoja veikiantį backendą arba paleidžia jį, jei jo neveikia, ir vėl jį sustabdo, kai baigia darbą.
  - **Komandos `lattice`, `project` ir `daq` niekada jo nepaleidžia.** Joms reikalingas jau veikiantis foninis procesas: Windows darbalaukio programos, Linux [foninė paslauga](#running-the-backend-as-a-service-headless) arba tas, kurį paleidžiate patys (žr. [Foninio proceso paleidimas](#start-the-backend)). Jei jo nėra, jos išveda pranešimą „`Error: Chloros backend not reachable at http://127.0.0.1:5000`“ kartu su komanda, kuri jį paleidžia, ir užsidaro „`1`“.
- `CHLOROS_BACKEND_URL=<url>` nukreipia **`lattice`**,**`project`**ir**`daq pool-*`** į kitą užkulisį (`lattice network-analysis` nukreipia į `--backend-url`). Pagrindinės komandos sąmoningai fiksuoja `http://127.0.0.1:<port>` ir ignoruoja jį (IPv4 literatas leidžia išvengti „Windows“ `localhost`→`::1` ~2 s užklausos sulėtėjimo). Žr. [Aplinkos kintamieji](#environment-variables).
- **Santykiniai keliai nustatomi pagal aplanką, iš kurio paleidžiate „`chloros-cli`“.** Užkulisinis procesas atidaro išvesties ir įvesties aplankus, todėl prieš siunčiant kelią CLI jį paverčia absoliučiu: `-o` / `--output` (įskaitant numatytąjį, `output`), `--input`, `--burst-dir`, `--profile`, `--save` ir `--mask`, taip pat [recepto](#recipe-dsl) žingsnio `output` (numatytoji reikšmė – `output`), taip pat numatytasis `output` aplankas, esantis `lattice index --live`. Todėl `-o captures/` rašo į `<current folder>/captures/`, o išspausdinti keliai yra absoliutūs. `~` yra išplečiamas. Naudojant nuotolinį užpakalinį modulį (`CHLOROS_BACKEND_URL`, nurodantį kitą kompiuterį), keliai siunčiami taip, kaip įvesti, ir išsprendžiami tame kompiuteryje.
- Visiems SDK/CLI (kiekviename kompiuteryje vieną kartą paleiskite „`chloros-cli login`“; duomenys išsaugomi „`~/.chloros/`“).
- Pavyzdžiuose naudojami Linux keliai; Windows sistemoje „`/home/user/...`“ pakeiskite į „`C:/Users/.../...`“. „DAQ-U“serijinis prievadas Windows sistemoje yra `COM3` tipo, o Linux sistemoje – `/dev/ttyUSB0` / `/dev/ttyACM0`.
- Apžvalgose tarpusavyje nesuderinami žymekliai rašomi kaip `{--a | --b}`, o pasirinktinius – kaip `[...]`.

---

## Aukščiausio lygio apžvalga

```
chloros-cli [global options] COMMAND [command options]
```

### Bendrosios parinktys

| Žymė | Aprašymas |
| --- | --- |
| `--backend-exe PATH` | Perrašyti automatiškai aptiktą užpakalinės dalies vykdomąjį failą. |
| `--port N` | Užpakalinės dalies HTTP-prievadas (numatyta reikšmė: `5000`). Naudojamas tik pagrindinėms komandoms (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`); `lattice`, `project` ir `daq` naudoja `CHLOROS_BACKEND_URL` (numatytojo `http://127.0.0.1:5000`). |
| `-v, --verbose` | Įjungti išsamų išvesties režimą. |
| `--restart` | Priverstinis užpakalinės dalies paleidimas iš naujo (nutraukia visus veikiančius `backend_server.py` procesus). Užpakalinė dalis, kurią valdo „systemd“ paslauga (Linux .deb) niekada nėra sustabdomas: komanda vietoj to išveda `sudo systemctl restart <unit>` ir užsidaro `1`. |
| `--version` | Išspausdinti versiją (`Chloros CLI 1.3.0`). |
| `--help` | Rodyti aukščiausio lygio pagalbą. |

### Komandų rodyklė

| Komanda | Paskirtis |
| --- | --- |
| [`process`](#chloros-cli-process) | Apdoroti Survey3 arba „LATTICE“ užfiksuotų duomenų aplanką nuo pradžios iki pabaigos. |
| [`login`](#chloros-cli-login) | Autentifikuoti šį kompiuterį naudojant „Chloros+“ paskyrą. |
| [`logout`](#chloros-cli-logout) | Išvalyti talpyklos duomenis. |
| [`status`](#chloros-cli-status) | Rodyti dabartinę licencijos / autentifikavimo būseną. |
| [`export-status`](#chloros-cli-export-status) | „Live Thread-4“ eksporto eiga vykdant `process` komandą. |
| [`language`](#chloros-cli-language) | Nustatyti arba peržiūrėti CLI rodymo kalbą (palaikomos 38 kalbos). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Numatytasis projekto aplankas (bendras su GUI). |
| [`update`](#chloros-cli-update) | Patikrinti ir įdiegti Chloros atnaujinimus (Linux/„Jetson“). |
| [`selftest`](#chloros-cli-selftest) | Sistemos diagnostika + greitieji testai. |
| [`time-sync`](#chloros-cli-time-sync) | PTP „grandmaster“ būsena / valdymas. |
| [`lattice`](#chloros-cli-lattice) | „LATTICE“ kameros valdymas ir vaizdo fiksavimas (daugiau nei 45 papildomos komandos). |
| [`daq`](#chloros-cli-daq) | DAQ spektrinių jutiklių valdymas (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Atidaryti ir valdyti išsaugotą Chloros projektą (kameros + DAQ). |

---

## Įdiegimas

`chloros-cli` yra įtrauktas į Chloros darbalaukio diegimo paketą visose palaikomose platformose — atskiro CLI failo atsisiųsti nereikia. Įdiegus platformos paketą, `chloros-cli` bus pridėtas prie jūsų `PATH` kartu su darbalaukio programa ir jos valdomu užkulisiniu binariniu failu.

Naujausi atsisiuntimai: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Įdiegimo programa taip pat pateikia patogius paleidimo skriptus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`), kurie atidaro paruoštą naudoti CLI aplinką.

### Windows (.exe)

1. Atsisiųskite Windows diegimo programą iš atsisiuntimo puslapio.
2. Paleiskite `Chloros Installer 1.3.0.exe` ir sekite vedlio nurodymus. Numatytasis diegimo kelias yra `C:\Program Files\Chloros\` (CLI'as įsikuria `C:\Program Files\Chloros\cli\`, kurį diegimo programa įtraukia į PATH).
3. Atidarykite naują terminalą („`cmd.exe`“, „PowerShell“ arba Windows terminalą), kad būtų aptiktas atnaujintas „`PATH`“.

```powershell
chloros-cli --version
```

Diegimo programa automatiškai įtraukia `chloros-cli.exe` į jūsų sistemos `PATH` aplanką ir prideda „LATTICE“ kamerų tvarkykles. Pagrindinis komponentas yra `C:\Program Files\Chloros\backend\chloros-backend.exe` (jūsų diegimo aplanko `backend` aplankas), kurio nėra `PATH`.

**Sistemos nustatymai.** Diegimo programos puslapyje *Pasirinkite papildomas užduotis* skyriuje **Sistemos nustatymai**yra dvi parinktys, kurios abi**pagal numatytuosius nustatymus yra pažymėtos**:

| Parinktis (kaip nurodyta diegimo programoje) | Ką ji daro | Atsisakyti tylaus diegimo |
| --- | --- | --- |
| **Įjungti ilgus Windows kelius** (leidžia projektų aplankams būti giliau nei 260 simbolių; rekomenduojama) | Nustato visam kompiuteriui taikomą „Windows“ parametrą `LongPathsEnabled` į 1. Windows sistemoje šis parametras iš pradžių yra išjungtas, todėl joks kelias negali viršyti 260 simbolių; Chloros produktai yra išdėstyti maždaug 130 simbolių žemiau projekto aplanko, todėl giliau esantis projekto aplankas nepriimamas (žr. [Problemų sprendimas](#troubleshooting-pointers)). Įsigalioja programoms, paleistoms po įdiegimo. Išjungiant programą šis nustatymas paliekamas įjungtas, nes kitos programos gali juo remtis. | `/MERGETASKS="!longpaths"` |
| **Išskirti Chloros užkurtį iš „Microsoft Defender“ nuskaitymo**(žymiai greitesnis vaizdo įrašymas; rekomenduojama) | Įdiegimo aplanke prideda „Defender“**proceso** išimtį „`chloros-backend.exe`“, todėl realaus laiko apsauga nenuskaito kiekvieno failo, kurį užkurtis įrašo, nepriklausomai nuo to, kur jis jį įrašo. Jokia kita programa nėra išskiriama; išimtis pašalinama išdiegimo metu. Jei „Defender“ nėra arba yra užblokuotas pagal politiką, šis žingsnis tyliai nepavyksta ir diegimas tęsiasi. | `/MERGETASKS="!defenderexclusion"` |**Kodėl „Defender“ parinktis yra svarbi:** esant dideliam įrašymo greičiui, įrašytų failų tikrinimas realiuoju laiku sudaro apie 30 % įrašymo greičio (matuota atliekant nepertraukiamą įrašymą iš 9 kamerų: 77,7 įrašų per sekundę neįtraukta, 52,7 nuskaityta). `lattice array-capture --continuous` parodo pranešimą, kai nustato, kad „Defender“ naudoja vieną ar daugiau procesoriaus branduolių įrašymo metu; paleiskite diegimo programą iš naujo, pažymėję šią parinktį.

Tylusis diegimas (`/SILENT` arba `/VERYSILENT`) taiko abi parinktis, nebent jos būtų atšauktos, pvz., `"Chloros Installer 1.3.0.exe" /VERYSILENT /MERGETASKS="!defenderexclusion"`.

**Windows užkarda.** Diegimo programa visada prideda dvi įeinančio srauto taisykles „`chloros-backend.exe`“ kiekviename tinklo profilyje, o pašalinimo programa jas pašalina: „`Chloros Backend (UDP-In)`“ – kamerų srautams, PTP, mDNS ir DAQ-E duomenų srautui, bei `Chloros Backend (TCP-In)`, apribotą vietiniu potinkliu, skirtą DAQ-E programinės įrangos atnaujinimui, kai jutiklis vėl prisijungia prie šio kompiuterio, kad surinktų vaizdą (žr. [DAQ-E įrenginio nustatymai](#daq-e-device-settings)). Užkulisio HTTP API lieka susietas su `127.0.0.1`.

### Linux amd64 (.deb)

Skirta „Ubuntu 22.04 LTS“ ar naujesnėms versijoms / „Debian“ pagrįstoms x86_64 darbo stotims.

> **„Ubuntu 20.04“ nepalaikoma.** Paketo priklausomybių sąrašas sudarytas remiantis
> tuo, su kuo iš tikrųjų susiejamas užkulisinis modulis, ir tai apima `libc6 (>= 2.35)`;
> „focal“ pateikia „glibc 2.31“. `apt` atsisako įdiegti, užuot leidęs, kad įdiegimas žlugtų
> vykdymo metu. Sąraše taip pat nurodytas „`libgl1`“, kurį įkelia pridedamas „OpenCV“, o minimalioje
> serverio versijoje jo gali trūkti; „`apt install`“ jį įtraukia.

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
```

.deb failas įdiegia:
- `chloros-cli` į `/usr/bin/chloros-cli` ir `chloros-backend` (paleidžia užkurtį) iki `/usr/bin/chloros-backend`
- Kompiliuotą užkurtį ir CLI, išpakuotus į `/usr/lib/chloros/backend/` ir `/usr/lib/chloros/cli/`
- „LATTICE“ kamerų tvarkyklės
- Triukšmo šalinimo modeliai, kalibravimo paketai ir atnaujinimo kanalo konfigūracija
- Sistemos Python SDK, `python3` (`import chloros_sdk`). `apt install` taip pat turi savo reikalavimus: `python3-numpy` ir `python3-requests` (rekomenduojama, bet nebūtina: minėtos programos yra savarankiškos; `dpkg -i` jų neįtraukia, o diegimo išvestis nurodo, ko trūksta). „virtualenv“ aplinkoje: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`
– `/etc/sysctl.d/60-chloros-gige.conf`, kuris padidina branduolio lizdo priėmimo buferius kameros srautams iki 32 MB ir iš karto juos pritaiko (jei vertė jau yra nustatyta didesnė, ji paliekama nepakeista). Pašalinus paketą, failas taip pat pašalinamas. Žr. [Linux kompiuteriai — lizdo priėmimo buferiai](#linux-hosts--socket-receive-buffers).
- Dvi pasirinktinės „systemd“ paslaugos užpakalinės dalies veikimui, nė viena iš jų neįjungta (kitas skyrius)

#### Užpakalinės dalies paleidimas kaip paslaugos (be grafinės sąsajos)

Be paslaugos užpakalinė dalis (ir kartu su ja PTP „grandmaster“) veikia tik tol, kol ją paleidžia: „`chloros-backend`“ terminale, SDK skripte arba pagrindinė „`chloros-cli`“ komanda visą jo veikimo laiką. Norint, kad užkulisiai visada veiktų kompiuteryje be grafinės sąsajos, įjunkite paslaugą **savo vartotojo vardu**:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` paleidžia foninę programą kaip tą vartotoją. Jos įrašomi duomenys priklauso tam vartotojui, išsamus žurnalas yra jo `~/.cache/chloros/logs`, ir ji naudoja to vartotojo `chloros-cli login` (`~/.chloros`), todėl pirmiausia prisijunkite kaip tas vartotojas. PTP nereikia root teisių. Kaip ir kiekvienas įdiegtas backend, jis klausosi tik `127.0.0.1:5000`.

`chloros-backend.service` paleidžia backendą kaip **root**. Jis vis dar yra įdiegtas, kad kompiuteris, kuriame jis įjungtas, toliau veiktų, tačiau viskas, ką rašo „root“ užkulisinė programa, priklauso „root“, todėl vartotojas negali ištrinti savo pačių įrašų. Norėdami perjungti į savo vartotojo paskyrą:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

Išsamus „root“ paslaugos žurnalas yra `/var/log/chloros`, kurį gali skaityti `adm` grupės nariai (paslauga nustato `CHLOROS_LOG_DIR`; žr. [Aplinkos kintamieji](#environment-variables)). Įdiegus paketą, ekrane pasirodo komanda „`enable`“ su įrašytu jūsų vartotojo vardu, o taip pat ir perjungimo parinktis, kai „root“ paslauga yra įjungta.

**Įjunkite vieną užkulisinį modulį.** Vienu metu veikia tik vienas užpakalinis modulis. Antrasis įjungtas modulis (`chloros-backend.service` šalia `chloros-backend@<user>.service` arba dviejų vartotojų `@` moduliai) neįsijungia ir lieka `failed` su išėjimo statusu 98; „systemd“ jo nepaleidžia iš naujo. Jo „`systemctl status`“ nurodo priežastį: „`Another instance is already running. Exiting.`“, tada tai, kas sulaiko foninį procesą (5000 uostas arba užrakto failas ir vartotojas, kuriam jis priklauso), tada „`systemd will not restart <unit> (exit status 98)`“. Po perkrovimo gali veikti bet kuris iš šių vienetų. Norėdami perjungti, išjunkite tą, kurio nenaudojate, kaip nurodyta aukščiau.**CLI**niekada nestabdo paslaugos užkulisio.**`chloros-cli`**naudoja jį tokį, koks yra, kaip tai daro GUI ir SDK:**`process`** veikia ant jo, įkeldamas savo projektą vietoj paskutiniojo. Ten, kur CLI, kitaip tariant, iš naujo paleistų užkurtį (`--restart` arba `process`, kurio importas nieko nerado), jis pavadina modulį, išveda „`sudo systemctl restart <unit>`“ ir uždaro „`1`“, nes tokiu būdu sustabdytas foninis procesas liktų išjungtas. `GET /api/status` pavadina vienetą `service` (`null`, jei foniniame procese nevykdoma jokia paslauga).**Atnaujinimas ir pašalinimas.** Atnaujinant paketą sustabdomos visos užkulisinės paslaugos, tada vėl paleidžiamos visos įjungtos paslaugos, pirmiausia `@<user>` vienetai (tas, kuris buvo paleistas rankiniu būdu, bet niekada nebuvo įjungtas, lieka sustabdytas, kaip ir po perkrovimo). Jei įjungta daugiau nei viena paslauga, veikia tik viena, o kitos lieka nesėkmingos, kaip nurodyta aukščiau; atnaujinimo metu rodomi pranešimai „`Chloros: N backend units are enabled: …`“ ir „`Keep one enabled: sudo systemctl disable --now <unit>`“. Pašalinus paketą, paslaugos sustabdomos ir išjungiamos. Išvalant taip pat ištrinamas „`/var/log/chloros`“.

### Linux arm64 — „Jetson“ (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

Toks pat išdėstymas kaip ir „amd64 .deb“ pakete, įtraukti priėmimo buferiai ir [pagalbinės paslaugos](#running-the-backend-as-a-service-headless), skirtas „Jetson Orin“ / „Orin NX“ / „Orin Nano“ su **„JetPack 6“** (L4T R36). Jo „PyTorch“ yra „JetPack 6“ CUDA versija, skirta „Orin“, todėl GPU apdorojimas, įskaitant tekstūrą atpažįstantį „debayer“, vyksta „Jetson“ GPU. Pakuotėje nėra pačios CUDA: ji naudoja CUDA ir cuDNN bibliotekas, kurias įdiegia „JetPack 6“ (`sudo apt install nvidia-jetpack` atvaizde, kuriame jų trūksta). Jei CUDA GPU negalima naudoti, apdorojimas vyksta CPU, o tekstūrų atpažinimo funkcija naudoja standartinį debayerį (žr. [`process`](#chloros-cli-process)).

### Autentiškumo patvirtinimas vieną kartą kiekviename kompiuteryje

Kiekvienoje platformoje reikia vienkartinio prisijungimo prie „Chloros+“, kad veiktų SDK ir CLI iškvietimai:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Prisijungimo duomenys išsaugomi `~/.chloros/user_session.json`.

### Užduoties vykdymo aplinkos paleidimas

Komandos `lattice`, `project` ir `daq` bendrauja su jau veikiančiu užkulisiu; jos jo niekada nepaleidžia (žr. [Konvencijos](#conventions)). Naudodami jas, palikite vieną iš jų veikiantį:

| Platforma | Kaip |
| --- | --- |
| Windows | Atidarykite Chloros darbalaukio programą (jos foninė programa aptarnauja ir CLI), arba paleiskite komandą „`"C:\Program Files\Chloros\backend\chloros-backend.exe"`“ atskirame terminale (jūsų diegimo aplanko kataloge „`backend`“) ir palikite jį atidarytą. |
| Linux / „Jetson“ | Paleiskite „`chloros-backend`“ atskirame terminale arba užtikrinkite, kad viena kopija visada veiktų naudojant [pagrindinę paslaugą](#running-the-backend-as-a-service-headless). |

Vienu metu veikia tik viena užkulisinė paslauga; ja naudojasi visi klientai (grafinė vartotojo sąsaja, CLI, SDK skriptai). Pagrindinės komandos (`process`, `login`, `status`, …) taip pat jį naudoja ir paleidžia, jei nė vienas neveikia.

**Backend veikia tarp komandų**, todėl kita komanda vėl nemoka už jo paleidimą. Jis sustabdomas, kai atsijungiate (`chloros-cli logout`, nebent yra atidaryta Chloros programa arba vyksta vykdymas ar neprisijungusio režimo kompiliacija), kai paleidžiate `chloros-cli backend stop` arba savaime po 30 minučių, jei niekas jo nenaudoja: nėra komandos, skripto ar programėlės, nėra vykdomo užduoties ar neprisijungusios kompiliacijos, nėra prijungtos kameros ar šviesos jutiklio. `backend stop` atsisako, kol vyksta užduotis arba neprisijungusi kompiliacija, nurodydamas jos pavadinimą; `--force` jį sustabdo dabar, ir ta užduotis arba kompiliacija prarandama (serija išsaugoma, o `array-build-video` ją sukuria iš naujo). Nustatykite `CHLOROS_BACKEND_IDLE_MIN` prieš paleidžiant užkurtį, kad pakeistumėte 30 minučių trukmę (`0`: niekada); [užkurtio paslauga](#running-the-backend-as-a-service-headless) niekada nesustoja savaime. `chloros-cli backend status` rodo, ar kas nors veikia, kas jį naudoja ir ar vyksta kokia nors neprisijungus vykdoma kompiliacija. `process` atidaro savo projektą veikiančiame backend'e, nebent Chloros programa būtų atidaryta su projektu, kurį ji atidarytu projektu: tuomet ji atsisako, ir jūs turite dirbti iš programos arba pirmiausia uždaryti tą projektą. Projektas, kurį anksčiau paliko atidarytą „`process`“ arba SDK scenarijus, tiesiog pakeičiamas.

### Įdiegimo patikrinimas

```bash
chloros-cli --version           # prints "Chloros CLI 1.3.0"
chloros-cli selftest            # 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

`selftest` užsidaro tik tada, kai sėkmingai atlikti visi septyni patikrinimai. Paskutinis patikrinimas reikalauja CUDA GPU (NVIDIA GPU, arba „Jetson Orin“) ir triukšmo šalinimo modelių, todėl kompiuteryje, kuriame jo nėra, rodomas pranešimas „`Failed: CUDA + Denoiser`“ ir programa uždaroma „`1`“; ten viskas veikia, išskyrus tik GPU naudojamą tekstūrą atpažįstantį debayerį.

> **Reikalinga „Chloros+“ prenumerata.**CLI reikalauja aktyvaus „Chloros+“ plano.**Copper**yra pradinis „Chloros+“ lygis — kiekvienas mokamas „Chloros+“ lygis suteikia prieigą prie CLI/SDK; tik nemokamas**Iron** lygis jos nesuteikia. (Planų identifikatorių atitikmenys: `0`=Iron/free, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) Atnaujinkite adresu [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing).
>
> Šis apribojimas taikomas ne tik per CLI, bet ir serverio pusėje: užklausos su SDK/CLI žymėmis, pateiktos be apmokėto plano, atmetamos su kodu `403 PLAN_UPGRADE_REQUIRED`, nepriklausomai nuo to, ar jos siunčiamos iš `chloros-cli`, Python SDK ar savarankiškai sukurto HTTP kliento. Atsijungęs vartotojas vietoj to gauna klaidos kodą `401 AUTH_REQUIRED`. Prieiga veikia neprisijungus plano atidėjimo laikotarpiu (30 dienų per mėnesį, o metiniam planui – iki galiojimo pabaigos) ir nutraukiamas pasibaigus šiam laikotarpiui; `chloros-cli status` toliau veikia, kad būtų matoma priežastis (tai vienintelis SDK/CLI maršrutas, kuriam netaikomas pakopų apribojimas — `GET /api/license-status`).

---

## Penkių minučių greitasis pradžios vadovas

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI GNDVI OSAVI

# 3. The lattice / daq commands need a running backend: on Windows open the
#    Chloros desktop app; on Linux start one (or see "Start the Backend")
chloros-backend &

# 4. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 5. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 6. Read a spectrum from a connected DAQ-U (Windows: --port COM3)
chloros-cli daq pool-connect --port /dev/ttyUSB0
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

`process` (2 žingsnis) paleidžia savo veikimui skirtą užkulisinį procesą, jei toks dar neveikia, ir jį sustabdo, kai jis baigia veikti, todėl paleiskite jį nuo 4 žingsnio. Žr. [Fono proceso paleidimas](#start-the-backend).

---

## `chloros-cli process`

Apdorokite vaizdų aplanką per visą Chloros procesų grandinę (tikslo aptikimas → kalibravimas → vinjetė → atspindžio koeficientas → indekso eksportavimas).

### Apžvalga

```
chloros-cli process INPUT [OPTIONS]
```

### Poziciniai argumentai

| Argumentas | Aprašymas |
| --- | --- |
| `INPUT` | Kelias į įvesties aplanką, kuriame yra `.raw + .jpg` (Survey3), `.tif/.tiff` („LATTICE“) arba `.dng` failai. |

### Bendrosios parinktys

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `-o, --output PATH` | naujas aplankas, pavadintas pagal `-n`, numatytame projekto aplanke | Projekto aplankas. Projektas pavadinamas pagal jį, o aplankas, kuriame jau yra `project.json`, gauna pavadinimą `_1` / `_2`, o ne perrašomas. Numatytasis projekto aplankas yra tas, kurį pasirinko `set-project-folder` (arba GUI), kitaip – `Documents/MAPIR/Chloros Projects`. |
| `-n, --project-name NAME` | autom. (laiko žyma) | Projekto pavadinimas, naudojamas tik **be** `-o` (su `-o` projektui suteikiamas aplanko pavadinimas). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` naudoja „Chloros+“ neuroninį debayerį Survey3 vaizdams; lėtesnis, bet aukštesnės kokybės. Skirtas tik Survey3: „LATTICE“ vaizdams visada naudojamas standartinis demosaic, todėl, jei aplanke yra „LATTICE“ vaizdai, „`process`“ po importo atmeta „`texture-aware`“, pateikdamas klaidą, kurioje nurodoma, kiek jų yra, ir uždaro „`1` (Chloros programa jiems taip pat nesiūlo „Texture Aware“ funkcijos). Tam reikalingas CUDA GPU (NVIDIA GPU Windows / Linux x64 sistemose, „Orin“ GPU „Jetson“ sistemoje): jei jo nėra, vykdymas naudoja „`standard`“, o užkulisiai užregistruoja vieną „`[DEBAYER] … needs a CUDA GPU …`“ eilutę, paaiškinančią priežastį (parodyta su „`--verbose`“). |
| `--vignette / --no-vignette` | `--vignette` | Vignette korekcija. |
| `--reflectance / --no-reflectance` | `--reflectance` | Atspindžio kalibravimas (naudojamas plokštelinis etalonas, jei rastas, arba NIST serijinis kalibravimas „LATTICE“ atveju). „LATTICE“ multispektrinių duomenų atveju tai taip pat veikia kaip atspindžio **sandauga** — žr. [Eksporto perjungikliai pagal produktą](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | išjungta | Taikyti PPK GNSS pataisas iš „sidecar“ failų. |
| `--exposure-pin-1 CAMERA_MODEL` | nėra | Kameros modelis, prijungtas prie ekspozicijos išvadų 1, skirtas PPK laiko sinchronizavimui. |
| `--exposure-pin-2 CAMERA_MODEL` | nėra | Kameros modelis, prijungtas prie ekspozicijos išvadų 2, skirtas PPK laiko sinchronizavimui. |
| `--recal-interval SECONDS` | `0` | Mažiausias pakartotinio kalibravimo intervalas, sekundėmis (projekto *Mažiausias pakartotinio kalibravimo intervalas*). |
| `--timezone-offset HOURS` | `0` | Šviesos jutiklio laiko juostos nuokrypis nuo UTC, valandomis (projekto *Šviesos jutiklio laiko juostos nuokrypis*). |
| `--format FORMAT` | `TIFF (16-bit)` | Vienas iš `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros niekada nepadidina vaizdo bitų gylio: visų 8 bitų vaizdų atveju gilesnis formatas įrašomas kaip „`TIFF (8-bit)`“, ir tai nurodoma vieną kartą. Projekte, kuriame maišomi 8 bitų ir 12/16 bitų vaizdai, kiekvienas vaizdas eksportuojamas su savo bitų gilumu arba mažesniu. „Radiance“ visada yra 32 bitų plūduriuojančiojo kintamojo tipo. |
| `--indices NAME [NAME ...]` | nėra | Augmenijos indeksai pagal iš anksto nustatytą pavadinimą (`NDVI`, `GNDVI`, `OSAVI`, `SAVI`, `EVI`, `GCI`, …), neatsižvelgiant į didžiųjų ir mažųjų raidžių skirtumą. Kiekvienas indeksas pridedamas prie projekto pagal jo kameros juostas, įtraukiant naują projektą. Pavadinimas, kuris nėra iš anksto nustatytas arba kurio kameros juostos negali sukurti (pvz., „`NDRE`“ reikalauja RE ir NIR juostų, kurių nė viena kamera nepalaiko), yra nurodomas įspėjime ir negauna indeksinio vaizdo. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Priversti naudoti LATTICE TIFF failų apdorojimo grandinės pradžios tašką (Survey3.raw failams tai neturi įtakos). Taip pat tai yra išeitis, leidžianti apdoroti užfiksuotus duomenis, kuriuose **nėra „raw“** — žr. [Kaip atrodo užfiksuotų duomenų aplankas](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | įjungta | Išsiųsti linijinį debayeringo rezultatą (`Debayered_Images`). Žr. [Eksporto perjungikliai pagal produktą](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | įjungta | Išsiunčia ekrano peržiūrą (`Preview_Images`): RGB = baltos spalvos balansas (DAQ šviesos šaltinis, jei yra, kitaip – „gray-world“) + gama; multispektralus = netikrų spalvų ištempimas. |
| `--radiance / --no-radiance` | įjungta | Pateikia „float32“ spinduliavimo intensyvumą (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | LATTICE atspindžio produkto etalonas: `auto` = kokybės užtikrinimo (QA) reikalavimus atitinkantis kadro vidinis tikslas yra absoliutus etalonas (tikslas su QA žymekliu konkuruoja su DAQ pagal išmatuotą rezultatą), atsarginis variantas – DAQ žemyn nukreiptas spinduliavimas (ρ = π·L/E); `target` = griežtas (be DAQ pakeitimo); `daq` = DAQ-autoritetingas. Žr. [Eksporto perjungikliai pagal produktą](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | nėra | Kiekvieno vieneto **išmatuotų** tikslinio atspindžio skenų katalogas (`<serial>.csv`); jei trūksta duomenų, grįžtama prie nominalių T3/T4P spektrų. |
| `--array-alignment / --no-array-alignment` | įjungta | LATTICE masyvai: kiekvienam apdorotam produktui (debayeringas / peržiūra / spinduliavimas / atspindžio koeficientas / indeksas) taikomas modulių tarpusavio suderinimas, pažymėtas kiekvieno įrašo `Chloros:Alignment*` XMP faile. Vaizdams be žymių ši operacija netaikoma. |
| `--array-alignment-crop / --no-array-alignment-crop` | apkarpymas | Apkarpykite suderintus eksportuotus duomenis iki masyvo bendros persidengimo srities, kad visi moduliai dalytųsi vienu pėdsaku; `--no-…` išlaiko visą jutiklio plotą (juodas užpildymas už šaltinio ribų). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Perdiskretizavimas suderinimo iškraipymui. `nearest` išsaugo tikslius šaltinio DN (be radiometrinių verčių maišymo tarp pikselių). |

### Tikslo aptikimo parinktys

| Žymė | Aprašymas |
| --- | --- |
| `--min-target-size PIXELS` | Mažiausias kalibravimo tikslo dydis pikseliais detektoriui. |
| `--target-clustering 0-100` | Tikslo grupavimo procentas (numatyta reikšmė `60`). |
| `--target / --targets` | Kalibravimo taškų paieška tik įvesties `target` arba `targets` pakatalogiuose (greičiau). |

### Pavyzdžiai

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a project folder of your choice (the project is named after it)
chloros-cli process "C:/input" -o "C:/projects/Field_A_2026-05-26"

# Name the project and let it go to the default project folder
chloros-cli process "C:/input" -n "Field_A_2026-05-26"
```

### Eksporto perjungikliai pagal produktą (LATTICE multispektrinis)

LATTICE apdorojimas išskirstomas į **visus taikytinus produktus per vieną ciklą**. Keturi perjungikliai pagal tipą — `--debayered`, `--preview`, `--radiance`, `--reflectance` —**pagal numatytuosius nustatymus yra įjungti**; naudokite formą `--no-<type>`, jei norite išjungti vieną iš jų. RGB pagrindinės kameros visada perduoda tik debayered + peržiūrą (be spinduliavimo/atspindžio pagal juostas), todėl „`--radiance`“ ir „`--reflectance`“ joms neveikia. Šie perjungikliai ignoruojami Survey3 „`.raw`“ atveju (kuris laikosi standartinio atspindžio / tikslo kelio). *(Senasis `--radiometric-output {reflectance,radiance,sensor-response}` žymė buvo **pašalinta** ir pakeista šiais perjungikliais; `sensor-response` lygio nebėra.)*

| Produktas | Išvestis | Reikalingas DAQ žemyn nukreiptas srautas? |
| --- | --- | --- |
| `--debayered` | Linijinis demozėjimas (`Debayered_Images`): tas pats failas, kurį užfiksavimas išsaugo savo `debayered` lygyje — kalibravimo paketo tamsos poslinkis (DSNU) nuo neapdorotų duomenų, tada OpenCV kraštų atpažinimo demozėjimas; be linijiškumo LUT, plokščio lauko ar atskyrimo. | Ne. |
| `--preview` | Peržiūros ekrane (`Preview_Images`): RGB = „WB“ + gama; daugiaspektrinis = iškraipytų spalvų ištempimas. | Nr. |
| `--radiance` | „float32“ W/m²/sr/nm iš visos radiometrinės grandinės (`Radiance_Images`). | Nr. |
| `--reflectance` | uint16 atspindžio koeficientas ρ (`32768` = 1,0), Pix4D-ready. | **Taip**, išskyrus atvejus, kai jį fiksuoja kokybės patikrinimą išlaikęs kadre esantis taikinys (žr. toliau). |

`--reflectance-source` pasirenka atspindžio etaloną:**`auto`**(numatyta reikšmė) nustato kokybės užtikrinimo reikalavimus atitinkantį kadre esantį taikinį kaip**absoliutų etaloną**— prie taikinio pririštos empirinių linijų grandinės yra kryžmiškai vertinamos atidėtuose skydeliuose, o išmatuotas laimėtojas taikomas juosta po juostos: juosta, kurios DAQ negali kalibruoti arba kurios DAQ nuolydžio pritaikymas nepavyksta, perima paties taikinio liniją (`ReflectanceElBandMethods` nurodo, kuri) — tikslas, kuris išlaiko kokybės patikrinimą su žymekliu (išmestas skydelis, neveikianti juosta), taip pat konkuruoja su DAQ padalijimu pagal tą išmatuotą rezultatą, o kadras, kuriam jis pralaimi, pažymimas `ReflectanceChain=daq` — grįžtama prie DAQ žemyn nukreipto padalijimo (ρ = π·L/E), kai nėra jokio taikinio arba nepavyksta kokybės užtikrinimas;**`target`**yra griežtas (be DAQ pakeitimo);**`daq`**atsisako DAQ autoritetingo elgesio. Tikslo geometrija (ArUco / fiksuotas ROI / juosta) gaunama iš projekto tikslo konfigūracijos; `--target-reflectance-dir DIR` saugo kiekvieno vieneto**išmatuotus** skenavimus (`<serial>.csv`), surastus pagal tikslinio įrenginio serijinį numerį / QR kodą, o kaip atsarginį variantą naudoja nominalius T3/T4P spektrus.

DAQ atspindžio kelias automatiškai nustato **laiko žymos atitikmenį turintį žemyn nukreiptą spinduliavimą**iš įrašyto**`.daq`**(DAQ-U/M/E)**arba iš DAQ-M vidinio `.csv`**, rasto kartu su vaizdais. Jei kameros ar DAQ kalibravimo rinkinys nėra išsaugotas vietinėje talpykloje, apdorojimo grandinė**jį automatiškai atsisiunčia iš AWS** pirmą kartą naudojant (reikia vienkartinio interneto ryšio; išsaugoma `~/.chloros/`).**Kieno tai rodmenys.**`.daq`, kurį išsaugo įrašas, yra toje kameroje (-ose), kurią (-ias) jis apibūdina, užfiksuoti rodmenys — pavadinti šalia esančiame `<file>.daq.cameras.json` priede (`lattice capture`, `multi-capture`, `array-capture` ir GUI; neapdorotų kadrų serijoje kiekvieno elemento rodmenys nurodomi jos manifestu) — o `process` jį susieja**tik su tų kamerų vaizdais**. Todėl atskira kamera, neturinti savo rodmenų, nebegali naudotis kitos kameros DAQ; priskirkite jai DAQ arba įrašykite vieną. DAQ nuosavas įrašas (`daq pool-record` arba įrašas, pradėtas GUI), arba bet kuris `.daq` be to „sidecar“ (įskaitant senesnius įrašus), vis dar tinka visoms kameroms.

#### Atspindžio pikselių nuskaitymas (Pix4D / Metashape / jūsų pačių skriptai)

Atspindys saugomas kaip sveikasis skaičius DN, o **DN, reiškiantis ρ = 1,0, priklauso nuo šaltinio kameros**:

| Šaltinis | ρ = 1,0 yra | Kaip nustatyti |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezervas iki ρ 2,0) | XMP `Chloros:PixelScale=32768` yra įrašytas faile. |
| Survey3 | „`65535`“ (apribota iki ρ 1,0) | Nėra „`Chloros:*`“ XMP žymių — šis nebuvimas *ir yra* požymis. |

**Perskaitykite „`Chloros:PixelScale`“ ir padalinkite iš jo**, o ne laikykite, kad tai konstanta. Žymė apibrėžta „uint16“ srityje, todėl ji išlieka `32768` visose išvesties formatuose, kuriuose keičiamas mastelis — `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)` ir `TIFF (32-bit, Percent)` – visi yra savaime apibūdinami (pirmiausia normalizuokite saugomą duomenų tipą atgal į „uint16“: ×257 iš 8 bitų, ×65535 iš „float“).

> **Failai iš senesnių versijų negali turėti mastelio.** 8 bitų atspindžio TIFF failas yra perskaluojamas ir pažymimas `Chloros:PixelScale`, kaip ir kiti. Versijose iki 1.3.0 versijos *apkarpydavo* jį iki 0..255, taigi kiekviena reikšmė, didesnė už ρ≈0,008, buvo sulyginama iki 255, be to, buvo praleisti tiek `Chloros:PixelScale`, tiek `MicaSense:RadiometricCalibration` poros. **Jei LATTICE atspindžio faile nėra šio žymės, nedarykite prielaidų apie mastelį — apdorokite jį iš naujo**, o ne dalykite pikselių, kurių niekada nebuvo galima padalyti.

#### Indeksinių pikselių skaitymas (NDVI ir kiti augmenijos indeksai)

**Sveikųjų skaičių indeksinis rasteris yra kiekvieno vaizdo kontrasto ištempimas, o ne −1…1 žemėlapis.** `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)` ir `JPG (8-bit)` indeksų produktai — įskaitant 8 bitų TIFF, į kurį įrašomas 8 bitų vaizdas — saugo

```

DN = (value − IndexMin) / (IndexMax − IndexMin) × full_scale
```

kur `IndexMin` / `IndexMax` yra **to kadro mažiausia ir didžiausia baigtinės reikšmės indeksų vertės**. DN 0 nėra −1, o pilna skalė nėra +1.

Chloros pažymi abu galinius taškus, kad atvaizdavimas išliktų apkeičiamas:

| Žyma | Reikšmė |
| --- | --- |
| `Chloros:IndexScaling` | `linear_minmax` — žemiau pateikta konvencija |
| `Chloros:IndexMin` | indekso reikšmė, saugoma kaip DN 0 |
| `Chloros:IndexMax` | indekso reikšmė, saugoma kaip pilna skalė |

Perskaitykite juos ir apverskite, normalizuodami saugomą DN į 0…1 pagal saugomo dtype maksimalią vertę, kad viena išraiška apimtų visus sveikųjų skaičių formatus:

```python
import json, subprocess
import numpy as np, tifffile

tags = json.loads(subprocess.run(
    ["exiftool", "-j", "-XMP:all", path],
    capture_output=True, text=True).stdout)[0]

# float(): exiftool hands back a negative real as a quoted string, so
# IndexMin arrives as str on most real NDVI data while IndexMax is a number.
lo, hi = float(tags["IndexMin"]), float(tags["IndexMax"])

raw = tifffile.imread(path)                    # uint16 for TIFF (16-bit)
ndvi = lo + raw.astype(np.float64) / np.iinfo(raw.dtype).max * (hi - lo)
```

Skaitymui nereikia `-config pix4d.config` — „exiftool“ šias XMP savybes nuskaito pats. Konfigūracija reikalinga tik norint jas *įrašyti*.

> **`IndexMin == IndexMax`** reiškia, kad kiekvienas to kadro pikselis turėjo tą pačią indekso vertę. Rasteris užpildytas vidurine skalės verte ir nėra kuo dalyti — imkite galutinę vertę.

> **Jei nėra `Chloros:Index*` žymių, tai reiškia, kad nėra nurodyto atitikimo.** LUT eksportas yra spalvotas atvaizdas, kurio pikseliai yra gradientinės spalvos, priskirtos jūsų LUT slenksčio langui, o kadras be baigtinio indekso pikselio neturi nurodyti jokio diapazono. Nė vienu iš šių atvejų nereikia daryti prielaidos apie atitikmenį — taikoma ta pati taisyklė kaip ir trūkstant `PixelScale`.**Kadangi ištempimas taikomas kiekvienam atskirai vaizdui, du vieno skrydžio kadrai nėra palyginami pagal DN**— kiekvienas turi savo galinius taškus, todėl tas pats NDVI atitinka skirtingą DN skirtinguose vaizduose. Atliekant kiekybinį darbą su viso skrydžio duomenimis (zoninė statistika, fiksuotos ribos, laiko eilutės), eksportuokite**`TIFF (32-bit, Percent)`**: indeksiniai rasteriai ten įrašomi kaip**pačios indeksų reikšmės** tipo „float32“, be perskalavimo ir be žymių. Nepaisant formato pavadinimo, indeksiniai produktai nėra ×100 — tas mastelis taikomas tik sveikųjų skaičių tipo atspindžiams.

#### „Unmix“ apribojimo kaukė (kuriuose pikseliuose juosta buvo apribota)

3×3 spektrinis atskyrimas gali paversti juostą neigiamą — ten, kur šviesoje beveik nėra tos juostos, arba ten, kur scena nepatenka į sritį, apibūdintą kameros atskyrimo matricoje. Tai nėra tikroji reikšmė, todėl eksportuojant ji nustatoma kaip 0 — ir tiesioginiame vaizde rodomas tas pats 0. Juosta gali būti apribota keliuose pikseliuose arba beveik visuose; kaukė užfiksuoja bet kurį iš šių atvejų. `Chloros:Band{R,G,B}UnmixClampFraction` nurodo, kokioje kadro dalyje tai įvyko; apribojimo kaukė nurodo, **kuriuose pikseliuose**:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— `clamp_masks/` aplanke**šalia** produkto aplanko, niekada ne jo viduje (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`; eksportuojant iš `process` gaunama `tiff16/Reflectance_Calibrated_Images/x.tif` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif`) — kiekvienam spinduliavimo, atspindžio ir indekso produktui, kuriame bent vienas pikselis apribotas: tiek įrašai, tiek eksportai `process` (ir kiekvieno kadro TIFF failai `array-build-video --save-tiffs`). Vienos juostos `uint8`, produktui būdingame pikselių tinkle (įskaitant orientaciją, ROI, binningą ir masyvo suderinimą). Kiekviena kaukė nurodo savo produkto kamerą (`Make` / `Model` / serijos numerį), užfiksavimo laiką ir objektyvo žymes, taip pat išvardija juostas, kurias žymi jos bitai (`XMP-Camera:BandName`, pirmasis bitas 0); jos `ImageDescription` ir `Software` žymi ją kaip ribojimo kaukę, ir ji neturi kalibravimo.
- **Bitai**: 0-asis bitas = juosta produkto R kanale (`Chloros:BandRName`), 1-asis bitas = G (`BandGName`), 2-asis bitas = B (`BandBName`). Nustatytas bitas reiškia, kad to pikselio vertė toje juostoje buvo apribota iki 0 — šį pikselį ir bet kokį iš jo sudarytą indeksą laikykite mažo patikimumo.
- **`Chloros:UnmixClampMask`** yra pažymėtas ant kiekvieno produkto, kuriam buvo atliktas atskyrimas: kaukės kelias, nurodytas santykinai pagal paties produkto aplanką (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, visada atskirtas `/` – sujunkite jį su vaizdo aplanku, kad atidarytumėte kaukę), arba `none`, kai nė vienas pikselis nebuvo apribotas. Jei žymės nėra, tai reiškia, kad produktui niekada nebuvo taikytas „unmix“ (RGB arba monochromatinės kameros, „raw“ / „debayered“ lygiai).
- `lattice capture` / `array-capture` išspausdina kaukės kelią po išsaugotu failu (`clamp mask: …`), o užfiksavimo rezultatų sąraše ji nurodoma kaip kadro `clamp_mask`.
- `lattice index --live --save-multiband` įrašo po vieną kaukę kiekvienai šaltinio kamerai daugiabandžio steko atveju — žr. [`lattice index` — daugiabandžio steko kaukės](#lattice-index--multiband-stack-masks). Vaizdo įrašas neturi kaukės: `array-build-video` vietoj to pateikia kiekvieno kadro apribojimo koeficientus (žr. [`array-build-video`](#array-build-video--offline-reprocess-a-saved-burst)).

Kadangi nė viename produkto aplanke, jokiu lygmeniu, nėra kaukės, produkto aplankas (`radiance/`, `reflectance/`, `process` eksporto `*_Images/` aplankas) gali būti perduotas „Pix4D“, „Metashape“ ar kitai fotogrametrijos programinei įrangai tokia, kokia yra — įskaitant pakatalogius — o ji priima tik vaizdus. Perduokite jai produkto aplanką, o ne virš jo esantį aplanką (tame taip pat yra „`clamp_masks/`“ ir visi kiti eksporto tipai). „Chloros“ importavimo funkcija praleidžia kaukes. Kaukės skaitymas: `(mask & 1) != 0` reiškia „čia buvo apribotas R kanalo diapazonas“, `& 2` – G, `& 4` – B.

#### Į eksportą perkeliami EXIF duomenys

`process` nukopijuoja šaltinio nuotraukos **GPS bloką ir jo ExifIFD** į kiekvieną produktą, taigi
eksportuojant perkeliami `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` ir
`CameraSerialNumber` kartu su georeferencijavimu.

**`FocalLength` nėra neprivalomas fotogrametrijai.** „Pix4D“ apskaičiuoja žemės mėginio atstumą remdamasi
fokuso nuotoliu ir aukščiu; jei žymės nėra, programa grįžta prie visiškai neteisingo mastelio. Vieno
49 kadrų skrydžio virš apelsinų giraitės metu dėl trūkstamos žymės 411 m × 160 m plotas buvo rekonstruotas į
47,8 km × 13 km plotą – 455 MP ortofotografiją, kurioje daugiausia buvo „nodata“ duomenų, o tai vėliau buvo traktuojama kaip mozaikos sudėliojimo problema ir
„BigTIFF“ problema, kol kas nors patikrino GSD. Jei jūsų ortofotografija gaunama neįtikėtinu
masteliu, pirmiausia paleiskite „`exiftool -FocalLength`“ ant eksportuoto produkto.

Kopija sąmoningai **nėra** „`-all:all`“: IFD0'struktūrinės žymos sugadina „LATTICE“ išvestį, kai
yra kopijuojamos, o „`ExifImageWidth`“ / „`ExifImageHeight`“ yra atmesti, nes jie apibūdina
*šaltinio* užfiksavimą — eksportas, kurio dydis kada nors buvo pakeistas, priešingu atveju turėtų matmenis, prieštaraujančius pačiam rasteriui. XMP rašomas tiesiogiai, o ne kopijuojamas, nes „ExifTool“
atmeta tos pačios iškvietos XMP žymes, kai kopijuojamas XMP blokas (dėl to būtų prarastos MAPIR kalibravimo žymos).

### Kur išsaugomi išvesties failai

Produktai įrašomi **projekto aplanke, sugrupuoti pagal fotoaparatą, o po to – pagal failo formatą**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Fotoaparato aplankas „LATTICE“ atveju yra `LATT-<sensor>-<lens>-F<filter>` (atitinkantis užfiksuoto vaizdo EXIF
`Model`), o Survey3 – „`<model>_<filter>`“; dvi kameros, turinčios tą patį jutiklį ir filtrą, bet besiskiriančios
objektyvu, turi atskiras medžio struktūras, nes skiriasi vinjetavimas, matymo laukas ir iškraipymas. Formato
aprašo struktūra: `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` arba `tiff32`, o
`TIFF (32-bit, Percent)`.

> **Kiekvienas eksportuotas produktas išlaiko ŠALTINIO failo pavadinimą.** Spinduliavimo duomenų eksportas iš
> „`capture_…_raw.tif`“ vis dar vadinamas „`capture_…_raw.tif`“ — jis tiesiog yra
> „`tiff32/Radiance_Images/`“. **Produktą identifikuoja aplankas, o ne failo pavadinimas**, todėl ieškant
> pagal `*radiance*.tif` nieko nerandama; vietoj to reikia ieškoti pagal katalogą.

### Šviesos jutiklio įrašai — kalibruoti `.daq` + `.csv`

`process` taip pat apdoroja įvesties aplanke esančius `.daq` įrašus, ir tam **nereikia**
reikia jokių vaizdų: atskirai paleistas DAQ-U / DAQ-M / DAQ-E yra pilnas
įrašas, o aplankas, kuriame yra tik `.daq` failai, yra tinkamas įvesties šaltinis.

DAQ įrašai gali būti daromi **be** jo kalibravimo — būtent taip pagal numatytuosius nustatymus veikia viešai prieinami
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) įrašymo įrenginiai
(`record_daq.py`) daro pagal numatytuosius nustatymus: jie įrašo neapdorotus jutiklio rodmenis ir pažymi failą taip, kad
Chloros gautų to jutiklio gamyklinį kalibravimą **pagal serijos numerį** (pirmiausia iš vietinės talpyklos,
tada iš MAPIR debesies) ir jį pritaiko. „`process`“ rezultatą vėl įrašo:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` kiekvienam matavimui skiria vieną eilutę: UTC laiko žymą, integracijos laiką, bendrą galią,
fotopinį/skotopinį liuksą, PPFD (ir jo mėlynos/žalios/raudonos spalvų pasiskirstymą), didžiausią bangos ilgį, tada
visą spektrą pagal paties jutiklio bangų ilgių tinklelį. `.daq` importuoja duomenis iš naujo be
pakartotinės kalibracijos.

`<name>` yra paties įrašo failo pavadinimas. Du įrašai, esantys skirtinguose aplankuose, gali dalytis
vienu — kiekvieno užfiksavimo rodmenimis (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`), išsaugotais
tą pačią milisekundę dviejų kamerų — ir kiekvienas vis tiek gauna savo produktus: pirmasis išlaiko
`<name>`, o antrasis **po** pavadinimo prideda aplankus, kurie jį išskiria
(`<name>__<capture>_daq_calibrated.daq`), taigi įrašo data ir laikas vis dar nurodomi pirmiausia.

Sėkmingai užbaigus ciklą, ataskaitoje nurodoma `Light-sensor products written: N (calibrated .daq + .csv)`.
Skliaustuose aprašoma, kas iš tikrųjų buvo įrašyta, taigi rašoma
`(RAW COUNTS — this sensor has no calibration bundle)`, jei jutiklis be paketo, ir
`(N calibrated, M raw counts)`, jei aplankas, kuriame yra abu. Pati užkulisinė sistema
`[DAQ-EXPORT]` ir `[RUN-SUMMARY]` antraštes formuluoja tuo pačiu būdu — nė viena iš
šių trijų negali vadinti neapdoroto eksporto kalibruotu.

DAQ-U / DAQ-M / DAQ-E įrašas, kurio kalibravimo rinkinio negalima gauti — esate
neprisijungę arba to jutiklio kalibravimo duomenų nėra faile — yra **praleidžiamas su priežastimi** eilutėje
`[DAQ-EXPORT]`, niekada neišrašomas kaip „kalibruotas“ failas, kuriame yra neapdoroti skaičiai.
Prisijunkite prie interneto ir paleiskite iš naujo. Priežastis yra ta, kurią skaitytuvas faktiškai
nustatė tam failui (neskaitoma schema, nėra rinkinio, rašymo klaida), o vykdymo
santraukoje išvardijamos **skirtingos** priežastys — dvidešimt failų, praleistų dėl vienos priežasties, rodomi kaip viena
priežastis, o ne dvidešimt jos pasikartojimų. CLI pabaigoje nurodo kiekvieną praleistą įrašą
kartu su priežastimi:

```
⚠ Light-sensor recording not exported: <file>.daq: no calibration bundle available for …
```

Aplankas su įrašais, iš kurio **nė vienas** įrašas neeksportuojamas, laikomas nesėkmingu: žr.
[Vykdymas, kurio metu nerašomi jokie vaizdai, laikomas nesėkmingu](#a-run-that-writes-no-images-fails).

Įrašas, kurio pavadinimas baigiasi `_calibrated` arba `_raw` (`field_raw.daq`), **nėra
importuojamas**: tai yra pavadinimai, kuriuos „`process`“ suteikia savo pačios sukurtoms įrašų kopijoms, o įrašas, perskaitytas
kaip įrašas, būtų suskaičiuotas du kartus. CLI tai nurodo ir pavadina failą —
`Not imported as a light-sensor recording: field_raw.daq … Rename the file to import it.`
— vietoj to, kad praneštų apie tuščią aplanką. Prieš apdorodami tokį įrašą, pervardykite jį.

#### „DAQ-A“ įrašai eksportuojami kaip neapdoroti skaičiai

**„DAQ-A“** šeima yra senesnė už serijinių paketų sistemą ir neturi kalibravimo paketo,
kurį reikėtų atsisiųsti — vietoj to ji kalibruojama lauke pagal atspindžio taikinį, todėl
jos niekada nereikėjo. Atmetus šiuos įrašus, nebūtų buvę jokio būdo išgauti jų
duomenis, todėl jie eksportuojami su **kitu pavadinimu**:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Naudojamas kitoks failo pavadinimas, o ne žymė pačiame faile, nes informacija turi išlikti
siunčiant el. paštu kaip paprastą pavadinimą. `.csv` antraštėje nurodyta
`raw spectral sensor counts (NOT irradiance)` ir įspėjama, kad reikšmės yra palyginamos
**tame pačiame** faile — būtent tam jos ir naudojamos kalibruojant pagal atskaitos tašką — o
ne tarp skirtingų jutiklių. Nuo galios priklausančios fotometrinės stulpelės (bendroji galia, fotopinis ir
skotopinis liuksas, PPFD) užrašomos kaip **NULL**, o ne integruojamos iš skaičių, o vykdymo
santraukoje nurodyta „`RAW COUNTS`“, taigi „eksportuota“ žurnale negali būti traktuojamas kaip spinduliavimo intensyvumas.

Senosios **v1.01 / v1.02** versijos įrašai (juos rašo DAQ-A-SD) neturi kiekvieno matavimo epochos,
tik failo įrašymo laiką. Vaizdo ↔ žemyn nukreipto spinduliavimo atitikimo programa vis dar jų nepriima —
kadangi kadrą suderinti su įrašymo laiku būtų nematoma klaida — tačiau eksportavimo programa juos nuskaito, o
CSV faile išspausdinama „`clock=daq_created_on`“, taigi produktas nurodo, pagal kurį laikrodį jis veikia.

### Pastabos

- „`process`“ automatiškai nustato, ar jūsų aplankas yra Survey3, „LATTICE“ ar mišrus.
- Vykdymo eiga perduodama per „Server-Sent Events“; CLI rodo realaus laiko kiekvieno srauto vykdymo eigą („Detecting“, „Analyzing“, „Processing“, „Exporting“).
- Linux/„Jetson“ sistemose CLI patikrina keitimo atmintį ir gali įspėti prieš apdorojant didelius aplankus. Tekstūrą atpažįstantis „debayer“ taip pat automatiškai taiko GPU dažnio ribą mažos galios „Jetson“ įrenginiuose („Nano“, „Orin Nano“).
- Sėkmingai užbaigus operaciją, ataskaitoje nurodoma, kiek vaizdų produktų buvo įrašyta (`Image products written: N`, o jei atskyrimo metu buvo apribotas bet kuris pikselis – `(plus M clamp masks)`).
- **`process` saugiai dalijasi veikiančiu užkulisiu.** Jis automatiškai iš naujo paleidžia jau veikiantį užkulisių tik tada, kai tas užkulisių neveikia, jame įkeltas ankstesnis projektas ir jame nėra kamerų, masyvų ar DAQ įrenginių. Fonas, kuriame yra bet kuris iš šių elementų (GUI, transliuojantis masyvą, SDK scenarijus, DAQ įrašymas), naudojamas toks, koks yra, niekada nepaleidžiamas iš naujo, todėl „`process`“ gali veikti kartu su aktyviu masyvu. Jei fonas praneša apie vykstantį vykdymą, „`process` laukia iki 15 s, kol jis baigsis (ką tik užbaigtam vykdymui išvalyti reikia apie sekundę), tada atsisako su klaida „`Backend is mid-run (processing). Wait for it to finish, or pass --restart to force a fresh backend (this kills the in-flight run).`“. Globalus žymeklis „`--restart`“ vis tiek priverčia paleisti naują užkurtį ir nutraukia bet kokią kameros, masyvo ar DAQ sesiją jame. Užkurtis, kurį vykdo Linux „systemd“ paslauga, niekada nėra paleidžiamas iš naujo: `process` jį naudoja, o `--restart` vietoj to išspausdina `sudo systemctl restart <unit>` (žr. [Backendo paleidimas kaip paslauga](#running-the-backend-as-a-service-headless)). Taip pat ir Chloros programėlės nuosavas užpakalinis modulis: kai programėlė atidaryta, „`process`“ jį naudoja, įkeldamas savo projektą vietoj programėlės projekto (užpakalinis modulis, paleistas vietoj jos, užblokuotų atidarytą programėlę).
- **Vykstantis apdorojimo ciklas išlaiko savo projektą.** Kol vyksta apdorojimo vykdymas — paleistas programos, „`process`“, SDK skripto arba „`chloros-mcp`“ — užkulisiai atsisako kurti, atidaryti, importuoti į, pridėti ar pašalinti failus, perkelti, ištrinti ar keisti projekto apdorojimo nustatymus, grąžindamas HTTP 409 `PROCESSING_IN_PROGRESS` ir `A processing run is in progress on project '<name>', so ... now is refused`; `process`, kuris tai gauna, išeina iš `1`. Rodymo nustatymai (miniatiūrų dydis) vis tiek išsaugomi. Apdorojimo režimas ir projekto tikslinio atspindžio nustatymai atmetami tokiu pačiu būdu (vykdymo sesija išlaiko režimą, kuriuo buvo pradėta). Įrašymas, išsaugotas projekte vykdymo metu (iš programėlės, CLI, SDK arba `chloros-mcp`), išsaugomas kaip įprasta, tačiau įtraukiamas į projekto failų sąrašą tik pasibaigus vykdymui; tai patvirtina atsakymas (`project_add`), taip pat ir į projektą įtrauktas sustabdytas šviesos jutiklio įrašas. Anksčiau antrasis klientas galėjo perjungti atidarytą projektą vykdymo metu, o vykdomas procesas buvo eksportuojamas į kitą projektą.
- **„Ctrl+C“ sustabdo vykdymą užkulisiuose, o ne tik CLI.** CLI paprašo užkulisio sustabdyti apdorojimą, išveda `Waiting for the backend to stop (Ctrl+C again to stop waiting)...` ir laukia iki 120 s, kol pasirodys `Processing stopped.`, kad `chloros-cli process … && next` nepradėtų `next` serveryje, kuris vis dar užimtas vykdant vykdymą. Antrasis „Ctrl+C“ nutraukia laukimą. Tada `process` užbaigia `130`, kaip ir bet kuri pertraukta komanda.

#### Vykdymas, kuris neįrašo jokių vaizdų, baigiasi nesėkme

Jei paprašėte produktų, o vykdymas įrašė **nė vieno** — tik `project.json` ir
`calibration_data.json` — `process` tai traktuoja kaip nesėkmę: jis išspausdina
`Processing finished but wrote no image products.` ir **baigia veikimą su nelygiu nuliui rezultatu**, todėl skriptas gali
tai aptikti. Pranešime nurodytas projekto aplankas ir įprastos priežastys:

- įvesties aplankas nebuvo atpažintas kaip įrašas (patikrinkite išdėstymą ir `--input-level`), arba
- kiekvienas prašytas produktas buvo praleistas kaip netaikytinas toms kameroms (pvz., prašant
  spinduliavimo/atspindžio iš kamerų, turinčių tik RGB).

Paleiskite iš naujo su `--verbose` ir patikrinkite užkulisio žurnale eilutes `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`,
kurios paaiškina, kokios kameros buvo praleistos, o ši informacija kitaip nepatektų į CLI išvestį.

Sąmoningas vykdymas tik su metaduomenimis — išjungus visus produktus ir nenaudojant `--indices` — vis tiek laikomas
**sėkmingu**, nes tuščias vaizdo išvesties rezultatas šiuo atveju yra teisingas.

#### Vykdymas, kurio eksportuotų duomenų nebuvo galima patikrinti, laikomas nesėkmingu

Po vykdymo `process` patikrina eksportuotų failų žymes naudodamas „ExifTool“. Jei „ExifTool“ nepavyko paleisti, jis
išrašo „Eksporto patikrinimas NEPAVYKO: „ExifTool“ nepavyko paleisti, todėl N iš M eksportuotų failų žymės
nebuvo patikrintos (žr. [EXIFTOOL] eilutes užkulisinio žurnalo įrašuose)` and `Rezultatai įrašomi ir lieka
savo vietoje; paleiskite procesą iš naujo, kad juos patikrintumėte.`, does not print `Apdorojimas baigtas!“, ir **baigia veikimą su nelygiu nuliui rezultatu**.
Rezultatai išsaugomi.

#### Kokie kadrai įtraukiami į vykdymą

Įtraukiami kadrai, užfiksuoti prieš paleidžiant `process`. Vykdymo metu išsaugoti kadrai neįtraukiami; `process`
įspėja, kiek jų buvo praleista, o pakartotinai paleidus programą jie įtraukiami.

#### Vykdymas, kurio metu prarastas vaizdas, baigiamas nesėkmingai

Jei vaizdas nepavyko vykdymo metu, `process` išspausdina `N image(s) failed and were not exported` su
dažniausia priežastimi, neišspausdina `Processing complete!` ir **baigia veikimą su nelygiu nuliui rezultatu**. Kiti produktai yra
įrašomi ir lieka savo vietose, o vidiniame žurnale užfiksuojami visi gedimai.

Taip pat yra ir **tik šviesos jutiklio vykdymo atveju**: `.daq` įrašų aplanke pagal apibrėžimą nėra eksportuotinų vaizdų,
todėl vykdymas vertinamas pagal kalibruotus `.daq` / `.csv`, kuriuos jis užrašė vietoj to.
Jei nebuvo įrašyta **nieko** — visi įrašai buvo praleisti — išvedamas
`Processing finished but wrote no light-sensor products.`, kiekvienam įrašui suteikiamas pavadinimas su
praleidimo priežastimi, ir **baigiama su nelygiu nuliui rezultatu**.

---

## `chloros-cli login`

Autentifikuokite šį kompiuterį naudodami „Chloros+“ debesų paskyrą. Autentifikavimo duomenys saugiai išsaugomi `~/.chloros/user_session.json`.

```
chloros-cli login EMAIL PASSWORD
```

### Pavyzdžiai

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **„PowerShell“ `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (pašalinant dalį slaptažodžio arba dubliuojant jo dalis). Esant 401 klaidai, CLI automatiškai bando dar kartą su `$$`, pridėdama, o po to – su pašalintais pasikartojimais; jei bandymas pavyksta, sistema jus prisijungia ir parodo teisingą viengubų kabučių sintaksę, kurią reikia naudoti kitą kartą.

> **Naudojimas be grafinės sąsajos / su skriptais: jei sesija nėra išsaugota, pasirodo prisijungimo langas.** Pagrindinė komanda (`process`, `status`, `export-status`, `time-sync`, …) vykdymas be išsaugotos sesijos reikalauja įvesti `Email:` / `Password:` per standartinį įvesties srautą (stdin), prieš tęsiant veikimą. Jei standartinis įvesties srautas uždarytas (cron, „systemd“ vienetas), programa iš karto baigia veikimą su klaida `login cancelled`; jei standartinis įvesties srautas yra atviras terminalas arba kanalas, ji laukia įvesties. Prieš planuojant darbą be grafinės sąsajos, kiekviename kompiuteryje vieną kartą paleiskite `chloros-cli login EMAIL PASSWORD`.

---

## `chloros-cli logout`

Išvalo sesijos talpyklą ir priverčia vartotoją iš naujo prisijungti kitą kartą prisijungus.

Atsijungimas, kol vyksta apdorojimo ciklas, jo nesustabdo: ciklas baigiamas tuo pačiu apdorojimo režimu, kuriuo buvo pradėtas, o kitas ciklas vykdomas standartiniu apdorojimo režimu. Tai nurodo `logout` (`The processing run in progress finishes in <mode> mode; the next run uses standard`), taip pat ir programa, kai atsijungiate iš ten.

```bash
chloros-cli logout
```

---

## `chloros-cli status`

Rodo dabartinį licencijos lygį („Iron“/„Copper“/„Bronze“/„Silver“/„Gold“), autentišką vartotoją ir įrenginių susiejimų skaičių.

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

Aptinka „Thread-4“ eksporto eigą realiuoju laiku. Saugu iškviesti **`process`** vykdymo metu iš kitos komandų eilutės.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

Nustato CLI rodymo kalbą (palaikomos 38 kalbos, įskaitant CJK, RTL ir indų kalbas). Senesnėse konsolėse, kurios negali atvaizduoti skripto, sklandžiai perjungia į anglų kalbą.

```
chloros-cli language [LANG_CODE] [--list]
```

### Pavyzdžiai

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## Projekto aplanko komandos

Šios komandos tvarko numatytąją projekto aplanko vietą (bendrą su GUI).

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

`get-project-folder` išveda aplanką, į kurį patenka nauji projektai. Jei nustatytas aplankas nebeegzistuoja (buvo ištrintas, pervadintas arba yra ne prijungtame diske), CLI, kaip ir GUI, grįžta prie numatytosios aplankos: numatytasis aplankas išvedamas į stdout, įspėjimas su trūkstamo aplanko pavadinimu siunčiamas į stderr, o išėjimo kodas yra `0`.

---

## `chloros-cli update`

Tik Linux / „Jetson“. Patikrina, ar `version_url` yra naujesnis už `/etc/chloros/update.conf`, ir, jei yra naujesnė versija, atsisiunčia atitinkamą `.deb` ir iškart ją įdiegia su `sudo dpkg -i` (be patvirtinimo lango; `sudo` gali paprašyti įvesti slaptažodį). „Jetson“ sistemoje atnaujinimas siūlomas tik „JetPack 6“ sistemai. Windows sistemoje komanda neveikia ir praneša: „`up to date`: atnaujinkite naudodami naują diegimo programą“.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Linux / „Jetson“ sistemoje CLI taip pat **kiekvieną kartą paleidžiant atlieka automatinį atnaujinimų patikrinimą** (neblokuojantis, niekada neuždelsta komandos vykdymo): ji nuskaito „`/etc/chloros/update.conf`“, rezultatą 1 valandai išsaugo talpykloje kaip „`~/.chloros/update_cache.json`“ ir, jei yra naujesnė versija, išveda „`Update available: vX.Y.Z / Run: chloros-cli update`“. Bet kokios klaidos atveju ir Windows sistemoje ši funkcija tyliai praleidžiama; `CHLOROS_DISABLE_UPDATE_CHECK=1` ją išjungia. Dėl 1 valandos trukmės talpyklos per tą valandą išleista versija vis dar gali būti rodoma kaip naujausia.

---

## `chloros-cli selftest`

Atlieka 7 žingsnių greitąjį testą: versija, prievadų prieinamumas, užpakalinės dalies paleidimas, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), triukšmo šalinimo modelio buvimas, CUDA ir triukšmo šalinimo parengtis. Naudoja veikiantį užkurtį arba paleidžia jį tik testavimo tikslais ir po to jį sustabdo. Išveda `N/7 checks passed` ir nesėkmingus patikrinimus, o baigia veikimą `0` tik tada, kai visi septyni etapai praeinami sėkmingai; CUDA ir triukšmo šalinimo patikrinimas nepavyksta kompiuteryje be CUDA GPU (žr. [Įdiegimo patikrinimas](#verify-the-install)).

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

PTP „grandmaster“ būsena ir valdymas. Chloros pagrindinis kompiuteris veikia kaip PTP „grandmaster“; „LATTICE“ kameros ir „DAQ-E“ įrenginiai veikia kaip jo pavaldiniai, siekiant užtikrinti laiko žymes tarp įrenginių (maždaug 1 ms tarp įrenginių). PTP suderina **laiko žymes**: masyvo kameros fotografuoja vienu metu, nes jas suaktyvina M8 aparatinis trigeris (mažiau nei 100 µs tarp modulių), o ne dėl PTP. `chloros-cli time-sync` be papildomos komandos paleidžia `status`.

| Papildoma komanda | Aprašymas |
| --- | --- |
| `status` | Rodo pagrindinio mazgo būseną, BMCA prioritetus, laikrodžio identifikatorių ir tai, ar jo paketai pasiekia tinklą: `health` yra `OK` (`STOPPED`, kai „grandmaster“ neveikia), arba `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` su `health_detail`, tada į „stderr“ išspausdinama eilutė „`WARNING:`“ (į „stdout“ ir toliau siunčiami JSON duomenys). `sync_sent` / `announce_sent` skaičiuoja tik išsiųstus pranešimus; `*_send_errors` ir `last_send_error` skaičiuoja tuos, kurie nebuvo išsiųsti. Kai kameros ryšys nutrūksta ir vėl atsiranda, „grandmaster“ pats atkuria savo lizdus (`rebinds`, `last_rebind`). |
| `peers` | Per „Delay_Req“ aptiktų pavaldinių sąrašas (kameros + DAQ-E jutikliai). |
| `cameras` | Kiekvienos kameros PTP būklė (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | „Grandmaster“ proceso paleidimas iš naujo. |
| `set-priority --priority1 N --priority2 N` | BMCA prioritetų perrašymas. |

„Grandmaster“ siunčia pranešimus per kamerų tinklo sąsajas (bet kuriuo adapteriu, kurio adresas yra `169.254.x.x`). Jei nė vienas iš jų neveikia, jis grįžta prie numatytojo maršruto adapterio tik tada, kai šis yra prijungtas laidiniu ryšiu, bet niekada nenaudoja „Wi-Fi“ ar virtualaus adapterio (Tailscale, WSL), nes jokia kamera ar DAQ-E nėra už jo. Jei nėra ką skelbti, jis veikia be lizdų (`health` `NO_SOCKETS`), o kai prisijungia kita kamera ar DAQ, jis vėl sukuriamas tame tinklo adapteryje, kuris tuo metu veikia; `time-sync restart` tai atlieka iš karto.

„Grandmaster“ veikia savo atskirame procese, šalia užkulisio (`own_process` / `process_pid`, esančiame `time-sync status`), Windows sistemoje jam suteikiamas aukštas prioritetas (`process_priority`), todėl užimtas „backend“ (apdorojimo ciklas, eksportavimas, kelios komandos vienu metu) negali uždelsti laiko žymėjimo; anksčiau vienas toks uždelsimas galėjo vienu metu perkelti visų kamerų laikrodžius. Šis procesas baigiasi su „backend“; jei jo nepavyksta paleisti, „grandmaster“ veikia „backend“ viduje kaip ir anksčiau (`own_process` false, ir tai nurodyta „backend“ žurnale).

### Pavyzdžiai

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

„LATTICE“ kamerų valdymas. Kiekviena pakomanda nukreipiama per Chloros užkurtį; užkurtis valdo kamerų grupę, todėl vėlesni CLI-skambučiai pakartotinai naudoja tą patį atvirą identifikatorių.

### Bendrosios parinktys (bendros daugumai pakomandų)

| Žymė | Aprašymas |
| --- | --- |
| `-d, --device N` | Kameros indeksas (numatyta reikšmė: 0). |
| `-s, --serial SN` | Konkretus serijos numeris; pakeičia `--device`. |
| `--serials SN1,SN2,…` | Kableliais atskirti serijiniai numeriai, skirti kelių kamerų veikimui. [Laikinieji kelių kamerų komandos](#multi-camera-transient-sessions) atidaro tik šias kameras, o pirmoji iš jų suaktyvina trigerį. |
| `--all` | Veikia su kiekviena aptikta kamera. |
| `--exposure US` | Ekspozicijos trukmė mikrosekundėmis. |
| `--gain DB` | Stiprinimas dB. |
| `--pixel-format FMT` | pvz., `BayerRG8`, `BayerRG12`; `Mono8`, `Mono12` „M3M“ modelyje. Pavadinimas nustato jo bitų gylį kameros šeimoje: M3M modelyje „Bayer“ pavadinimas nustato „Mono“ formato gylį (`BayerRG8` nustato `Mono8`), o M3C modelyje „Mono“ pavadinimas nustato „Bayer“ pavadinimą (`Mono12` nustato `BayerRG12`). |
| `--width N` / `--height N` | Vaizdo matmenys. |
| `--preset {default,high_quality,high_speed,triggered}` | Taikyti nustatymų šabloną: jo pikselių formatą, ekspoziciją, stiprinimą ir kadrų dažnį (`high_speed`: `BayerRG8`, 5 ms, 6 dB, 60 kadrų per sekundę; `high_quality`: `BayerRG12`, 20 ms, 0 dB; `lattice presets` juos išvardija). M3M formate naudojamas to paties gylio „Mono“ formatas: `high_speed` fiksuoja `Mono8`, `high_quality` ir `triggered` `Mono12`. Kartu su juo pateiktas žymeklis (`--exposure`, `--gain`, `--pixel-format`) turi pirmenybę prieš išankstinį nustatymą, o nustatymas, kurio išankstinis nustatymas nenurodo, išlaiko kameros numatytąjį. Visi laisviveikimas, išskyrus `triggered`, kuris įjungia kamerą, kai 2-oje linijoje atsiranda aparatinės įrangos kraštas — jei ši linija nėra aktyvuota, kamera lauks amžinai, o ne fiksuos vaizdą. |
| `-o, --output DIR` | Išvesties katalogas (numatyta: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | GVSP paketo dydis. `auto` vykdo ICMP+GVSP tikrinimus; `jumbo` = 9000; `standard` = 1500. |

### „LATTICE“ kameros pirmojo prisijungimo darbo eiga

```bash
# 0. A backend must be running: the Chloros desktop app (Windows),
#    `chloros-backend`, or the Linux service (see "Start the Backend")

# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Full sensor (no binning, no ROI); the frame rate falls to
#        what the host and wire deliver, and the faster options are
#        printed
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### Papildomų komandų žinynas

#### Atradimas ir informacija

| Papildoma komanda | Paskirtis |
| --- | --- |
| `lattice info` | Rodo prijungtų kamerų sąrašą (gamintojas, modelis, serijos numeris, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Analizuoja pagrindinę sistemą, siekiant nustatyti optimalų kameros konfigūravimą. `--no-discover` praleidžia kamerų aptikimą (greitesnis, atliekama tik tinklo plokštės analizė). |
| `lattice network [--fix] [--estimate] [--cameras N] [--pixel-format FMT]` | Patikrinti/pataisyti tinklo plokštės nustatymus (Linux sistemoje taip pat branduolio lizdų priėmimo buferius — žr. [Linux kompiuteriai](#linux-hosts--socket-receive-buffers)); įvertinti pralaidumą ir kadrų skaičių per sekundę (FPS). |
| `lattice network-analysis --master SN [--slaves SN1,SN2,…] [--width N] [--height N] [--pixel-format FMT] [--binning {1,2,4}] [--force-tier TIER] [--packed {auto,on,off}] [--backend-url URL] [--json]` | „Stable-schema“ užpakalinės dalies tinklogalimybė + masyvo rekomendacija (grąžina `status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `--width` / `--height` numatyta reikšmė – visas jutiklis, `2048` × `1536`. Jis baigiasi `0`, skirtas `ok`, `auto_capped_fps` ir `auto_shrunk`, o `1` – į `needs_force_slip` ir `error`, su `--json` arba be jo. Jis pasiekia užkulisį adresu `--backend-url` (numatyta reikšmė – `http://127.0.0.1:5000`), o ne `CHLOROS_BACKEND_URL`. `auto_capped_fps` išlaiko prašomą skiriamąją gebą, bet apriboja tikslinius kadrų per sekundę skaičių — perskaitykite `recommended.recommended_target_fps` ir perduokite jį kaip prisijungimo tikslą; laikykite tai sėkme, o ne klaida. `auto_shrunk` pasirodo tik tada, kai prašomo kadro negalima pateikti jokiu kadrų dažniu; „connect“ niekada pats nesumažina kadrų. |
| `lattice analyze-array [--models M1,M2,…] [--binning {1,2,4}] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--packed {auto,on,off}] [--json]` | „What-if“ analizė neatidarant kamerų (`--width` / `--height` numatytasis `2048` × `1536`). Tiek šis, tiek `network-analysis` projektuoja tai, ką „connect“ transliuos: be `--pixel-format`, taikomi „connect“ nustatyti formatai kiekvienai kamerai (Mono12 / BayerRG12), o su `--packed auto` (numatyta reikšmė) – sprendimas, kurį „connect“ priimtų šiame kompiuteryje dėl [supakuoto 12 bitų](#packed-12-bit-transport---packed) sprendimas, kurį „connect“ priimtų šiame kompiuteryje — taigi nurodytas kadrų per sekundę skaičius (fps) yra greitis, kuriuo veikia masyvas. Abu rodo suspaudimo sprendimą. **`--n-active` yra bendras kamerų skaičius tinkle, o ne tik šio masyvo**— padidinkite jį, kai atskiros kameros transliuoja vienu metu arba kai tinklo pralaidumo biudžetas skaičiuojamas remiantis poreikiu, kuriame jų skaičius yra per mažas (numatyta reikšmė: `len(--models)`). Visada rodo suvestines eilutes `Wire budget:` (reikalaujamas MB/s palyginti su susidūrimų saugumo riba) ir `Max cameras:`, taip pat pažymi `** OVER-SUBSCRIBED**`, kai masyvas viršija tinklo pralaidumą — žr. [Masyvo fps ir srauto modelis](#array-fps--burst-model). |
| `lattice gpu` | Rodyti GPU būseną. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Patikrinti arba atnaujinti kameros programinę įrangą. Vietinis pasirinkimas `.fwa` yra fiksuotas: failas, esantis `firmware/<MODEL_PREFIX>/` ir atitinkantis surinkimo versiją `MIN_FIRMWARE_VERSION`, yra įrašomas, jei yra (aukščiausia versija naudojama tik kaip atsarginis variantas), todėl naujesnis gamintojo atvaizdas, laikomas diske, lieka neaktyvus, kol tas įtvirtinimas nėra pakeistas — sąmoningai naujesnės versijos pasiekia įrenginius per pasirašytą AWS manifestą, kuris yra teikiamas pirmenybė, kai yra naujesnis. |
| `lattice presets [--apply NAME]` | Rodyti arba taikyti kameros išankstinius nustatymus. `--apply` nustato, kaip vadinami išankstiniai nustatymai (jų pikselių formatas, ekspozicija, stiprinimas, kadrų dažnis, suaktyvinimas), ir išspausdina tuos nustatymus taip, kaip juos nuskaito kamera: M3M atveju – nustatymo gylio „Mono“ formatą; kadrų dažnį, kuriuo ji veikia, kuris gali būti mažesnis nei reikalauja nustatymas (`high_speed` reikalauja 60 kadrų per sekundę; pilno kadro kamera, prijungta prie 1 Gbps ryšio, veikia maždaug 35 kadrų per sekundę dažniu). Visi kiti nustatymai, kuriuos įrašė „open“, grąžinami tokie, kokie buvo rasti. „`--apply default`“ atkuria kameros gamyklinius numatytuosius nustatymus: gamyklinius nustatymus su laisvai veikiančia automatine ekspozicija „Line2“, išjungtu ryšio pralaidumo apribojimu ir defektų korekcija, išlaikant kalibruotą RGB kameros baltos spalvos balansą (visais kitais atvejais – vienetinis). Į kameros atmintį nieko neišsaugoma, todėl kitą kartą įjungus ji paleidžiama iš savo atminties kaip ir anksčiau. Kaip ir įrašymo ar prisijungimo komandos `--preset`, `default` nėra iš anksto nustatytų parametrų. |
| `lattice status` | Kameros būklė realiuoju laiku. |

#### Įrašymas

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice capture [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--no-daq] [--daq-tolerance-ms MS]` | Vienas kadras. **Pagal numatytuosius nustatymus išsaugo visus eksporto tipus**(`--processing all`); žr. [Įrašymo eksporto lygiai](#capture-export-levels-the-all-default). `--processing` priima `all`, `raw`, `debayered`, `radiance`, `reflectance` arba `display` (`preview` priimamas vietoj `display`); bet koks kitas pavadinimas atmetamas prieš paliečiant kamerą (išėjimas `1`). `--levels` išsaugo aiškų pogrupį (pakeičia `--processing`). Chloroso neturima kamera atidaroma įrašymui, o jos ekspozicija nustatoma iš scenos iš pradžių (daugiausia kelias sekundes), kaip tai daroma naudojant [laikinąsias komandas](#multi-camera-transient-sessions); kai fiksavimas baigiasi, nesvarbu kaip jis baigiasi, ta kamera grąžinama į tą būseną, kokioje buvo rasta (formatas, ekspozicija, spalvų apdorojimo grandinė, visi nustatymai, kuriuos pakeitė fiksavimas — sąrašas skyriuje [Daugiakameris (trumpalaikės sesijos)](#multi-camera-transient-sessions)). DAQ, skaitantis naudotą įrašą, išsaugomas kaip `.daq` „sidecar“ failas, į kurį įtrauktas tik neapdorotas įrašas; `--no-daq` / `--daq-tolerance-ms` jį koreguoja ([DAQ, skaitantis įrašą, išsaugo](#the-daq-reading-a-capture-saves)). `--force-daq` vis dar priimamas. `--jpeg-quality` = JPEG kokybė 1–100 (numatyta reikšmė 95).**Į atidarytą projektą išsaugoti failai įtraukiami į jį.** Kaip ir programėlėje, įrašai, kurių failai patenka į Chloros atidaryto projekto aplanką, pridedami prie to projekto failų sąrašo; kitur išsaugoti failai lieka nepaliesti. Komanda išveda vieną eilutę, kurioje tai nurodoma (`Project: ...`), arba, jei apdorojimo ciklas dar vyksta, kad jie yra sulaikyti ir bus įtraukti, kai ciklas baigsis. Taip pat veikia `multi-capture`, `array-capture` (taip pat ir „`--smart`“) bei „`project capture`“. |
| „`lattice continuous [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--queue-depth N] [--no-daq] [--daq-tolerance-ms MS]`“ | Perkelia į diską, kol paspaudžiama „Ctrl+C“. „`tiff`“ apdorojimo ciklokadrus sudaro `raw` failas – masyvas, į kurį įrašomi tos kameros duomenys: jutiklio mozaika, kiekviena plokštuma su savo reikšmėmis, nesuspausta, su kalibravimo paketu (2048 x 1536 12 bitų kadras yra 6,0 MiB plius paketas, nepriklausomai nuo scenos), taigi `process` prasideda nuo jų. `png` ir `jpg` talpina 8 bitus, taigi šie įrašai yra demozajuoti kadrai, skirti peržiūrai, nuo kurių `process` neprasideda. Esant susietam su DAQ (`daq pool-bind`) arba sujungus į grupę, kiekvienas kadras gauna savo `.daq` ir `.match.json` serijos `daq/` aplanke, nes „`lattice capture`“ išsaugo vieną; „`--no-daq`“ išjungia šią funkciją, o sustabdžius programą parodoma, kiek jų yra. Kiekvienas kadras įrašomas vieną kartą su visais metaduomenimis; paspaudus „Ctrl+C“ jau užfiksuoti kadrai įrašomi prieš atleidžiant kamerą. Programa pati sustabdo veiklą, kai užpildomas diskas, išspausdina „`Failed:`“ / `Disk full:` ir užsidaro kaip `1`; kiekvienas kadras, kurio nepavyko įrašyti, taip pat užsidaro kaip `1`. Kamera, palikta parengta aparatinio suveikimo režimui (`TriggerMode` `On`), šio ciklo metu nustatoma į laisvo veikimo režimą. Pasibaigus ciklui, kamera grąžinama į pradinę būseną — jos suaktyvinimo nustatymai ir visi ciklo metu pakeisti parametrai (ekspozicija, jautrumas, formatas, …) — išskyrus atvejus, kai pasireiškia klaida `--keep-settings`, kuri įrašo sesijos nustatymus į pačios kameros atmintį (laisvasis režimas), arba `--clear-settings`, kuri atkuria gamyklinius tos atminties nustatymus. Ciklas, kurio metu neužfiksuojamas nė vienas kadras, laikomas nesėkmingu: jei 10 sekundžių neužfiksuojamas nė vienas kadras, jis pats sustabdomas, išspausdinamas pranešimas `No frames:` su priežastimi ir užbaigiamas `1`. Kadrai, kurių rašytuvai nespėjo apdoroti, skaičiuojami kaip prarasti, o sustabdymo metu išspausdinamas pranešimas „`Dropped:`“ su priežastimi (kameros kadrų dažnis, rašytuvų dažnis ir eilės ilgis); esant mažesniam kadrų dažniui arba naudojant 8 bitų pikselių formatą, jie visi išsaugomi. Komanda kas 2 s patikrina ryšį su užkulisiu; jei ji užsidaro nenutraukusi įrašymo (`kill -9`, trūksta atminties), užkulisis jį sustabdo praėjus 30 s po paskutinio patikrinimo ir grąžina kamerą į (`stop_reason` `client_gone`, ir eilutė užpakalinės sistemos žurnale). `multi-stream` ir `align-stream` veikia taip pat. Paleidimas, kurio metu kamera atsidaro ilgiau, nei laukia komanda (60 s; 180 s `multi-stream` / `align-stream` atveju) — kamera vis dar įkeliama — yra atšaukiamas serverio: kamera grąžinama į pradinę būseną ir uždaroma, o įrašymas nutraukiamas (504 HTTP `CLIENT_DEADLINE_PASSED`). |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Naršyklėje veikiantis tiesioginis MJPEG peržiūros režimas: prisijungia prie kameros, išspausdina srauto URLą ir atidaro jį jūsų naršyklėje. `--ae-damping` nustato automatinės ekspozicijos slopinimą (0,4–100). |

##### Tiesioginiai srautai (MJPEG; be RTSP)

Chloros teikia tiesioginį vaizdo įrašą kaip **MJPEG per HTTP**(`multipart/x-mixed-replace`, riba `frame`) iš serverio.**RTSP nepalaikoma.** Atidarykite URL adresą naršyklėje arba perskaitykite jį naudodami bet kurį MJPEG klientą (OpenCV `cv2.VideoCapture(url)`, ffmpeg, VLC). Kamera pirmiausia turi būti prijungta serveryje (`lattice cam-connect`, `lattice array-connect`, `lattice viewer` arba per GUI).

| URLas (`http://127.0.0.1:5000` + …) | Srautas |
| --- | --- |
| `/api/camera/stream/<serial>` | Ekrano peržiūra, iki maždaug 30 kadrų per sekundę. Masyvo elementui – `?max_h=` (numatyta reikšmė – `720`) ir `?jpeg_q=` (numatyta reikšmė – `80`) nustato aukštį ir JPEG kokybę. Tai yra URL, kurį atidaro `lattice viewer`. |
| `/api/camera/<serial>/stream-annotated` | Tas pats peržiūros vaizdas su nupieštais GUI sluoksniais: `zebra=1`, `crosshair=1`, `grid=3x3` (arba `9x9`), `histogram=1`, `peaking=1`, `spot=1`; be užklausos naudojami projekto išsaugoti tos kameros perdangos nustatymai. |
| `/api/camera/<serial>/stream-reflectance` | 8 bitų atspindžio koeficientas kiekvienam juostos dažniui: ρ = pikselis ÷`X-Chloros-Pixel-Scale` antraštės (255, jei `refl_max` nėra nustatytas). `fps` (numatyta reikšmė `10`), `jpeg_q` (numatyta reikšmė – `95`), `max_h` (numatyta reikšmė – `0` = natūralus), `band=<name>`, `align=1`. JPEG, taigi su nuostoliais. Atmesta su HTTP 409, kai kamera neturi atspindžio (RGB kamera, nėra kalibravimo paketo, nėra DAQ rodmenų). |

- **Kadrų suderinimas tarp kamerų.**Kiekviena**masyvo elemento** siunčiama dalis visuose trijuose srautuose turi `X-Chloros-Group-Id` (masyvo sinchronizuotos grupės skaitiklis), `X-Chloros-Frame-Id` ir `X-Chloros-Timestamp-Ns` antraštes. Dalys iš skirtingų kamerų su tuo pačiu `X-Chloros-Group-Id` kodu yra iš to paties sužadinimo šaltinio. Atskiroji kamera neturi grupės ir nesiunčia grupės identifikatoriaus. Naršyklės ignoruoja kiekvienos dalies antraštes; jas skaito pats klientas, kuris analizuoja daugiašalį srautą.
- **Sustabdyta, kol atidarytas masyvo vaizdas.** Masyvo nario `/api/camera/stream/<serial>` siunčia statinį „paused“ kadrą, kol atidarytas sujungtas masyvo tiesioginis srautas (pavyzdžiui, GUI masyvo vaizdas), ir atnaujina veiklą, kai jis uždaromas.

#### Jutiklio nustatymas

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice configure [{--get N1 N2… \| --set N=V N=V… \| --dump}] [--json]` | Skaityti/rašyti bet kurį „GenICam“ mazgą. `--set ExposureTime=…` (`Gain=…`) be `ExposureAuto` (`GainAuto`) jungiklių, kurie pirmiausia išjungia automatinį režimą, kaip tai daro `lattice exposure --set`; jei kamera atsisako priimti reikšmę, į stderr išspausdinama `Failed to set <node>: …` ir programa uždaroma `1`. `--get` ir numatytoji sugrupuota peržiūra išspausdina „`note:`“ po „`ExposureTime`“ / „`Gain`“, kol veikia automatinis režimas — žr. [Ekspozicijos ir stiprinimo rodmenys automatiniame režime](#reading-exposure-and-gain-under-auto-mode). |
| `lattice exposure [{--auto \| --auto-once \| --off \| --set US}] [--brightness 0-255] [--brightness-auto] [--damping 0.4-100] [--upper-limit US]` | Ekspozicija ir AE. `--set` ekspozicija arba `--brightness` tikslas laikomas jūsų („Chloros“ automatinis ekspozicijos valdymas jo nekeičia); stiprinimas lieka toks, koks buvo, taigi automatiniame režime jis nuolat koreguojamas link tikslo, kol nenustatysite `lattice gain`. `--auto` grąžina ekspoziciją, o `--brightness-auto` grąžina tikslą „Chloros“ automatiniam tikslui. Jei žymės nenustatytos, sistema nuskaito ekspozicijos mazgus, naudodamas tą patį „`note:`“, kaip ir „`configure --get`“. Jei kamera atsisako „`--set`“ vertės, ji išspausdina „`Failed to set ExposureTime: ExposureTime=<value> refused (<camera error>); the camera accepts <min>..<max> now (ExposureAuto put back to <mode>)`“ į „stderr“ — automatinis režimas, kurio rašymas buvo išjungtas, atkuriama, o ne paliekama išjungta — neišspausdina `Exposure: … us (auto OFF)` eilutės ir užbaigia veiklą `1`. |
| `lattice gain [{--auto \| --off \| --set DB}]` | Stiprinimas ir automatinis stiprinimas. Jei `--set` reikšmė, kamera atsisako išvesti `Failed to set Gain: …` į stderr, neišspausdina `Gain: … dB (auto OFF)` eilutės ir užsidaro `1`. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N]` | Jutiklio ROI ir pikselių sujungimas. Pikselių sujungimas visada yra **vidurkis** — žr. toliau. |
| `lattice format [--set FMT] [--list]` | Pikselių formatas. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Aparatinis/programinis trigeris. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (nėra žymių = vienkartinis baltos spalvos balansavimas) | Baltos spalvos balansavimo operacijos. Tik RGB/Bayer kameros; mono M3M atveju – neveikianti operacija (praleidžiama). Vienkartinė operacija išlaiko nustatytą baltos spalvos balansą (`BalanceWhiteAuto` ir stiprinimus); visi kiti nustatymai, kuriuos ji rašo į atvirą srautą, grąžinami tokie, kokie buvo rasti, o kai vienkartinė operacija negali būti vykdoma – grąžinami visi nustatymai. |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct 2000-10000] [--get]` | RGB ekrano spalvų apdorojimo grandinė. `natural` (numatyta) yra pigus tiesioginis apdorojimas; `enhanced` prideda spalvų apvalių pašalinimą + gyvumą + CLAHE vietinį kontrastą, kad būtų pasiektas pilnas „hub-parity“ vaizdas, tačiau apdorojimo kaina vienam kadrui padidėja maždaug 2 kartus, todėl sumažėja **tiesioginis** kadrų dažnis — išsaugoti kadrai bet kuriuo atveju visada gauna pilną apdorojimą. Tik RGB/Bayer kameros; praleidžiama mono M3M. |
| `lattice color [--saturation 0-200] [--contrast 0-200] [--reset] [--get]` | Tiesioginio peržiūros sodrumas/kontrastas (kameros su RGB filtru); `100` yra neutralus, `--reset` nustato abu parametrus į 100. Tai neturi įtakos išsaugotiems kadrams. Praleidžiama naudojant mono M3M. |
| `lattice highlights [{--set -100..0 \| --reset \| --get}]` (nėra žymių = skaityti) | Rodyti **šviesias sritis** (kameros su RGB filtrais): sumažinti ryškiausius, balčiausius pikselius — pvz., ryškiai baltą paviršių, kuris vaizduojamas kaip plokščia balta dėmė. `0` = išjungta, `-100` = stipriausias. Tiesioginis peržiūros vaizdas ir ekrano lygio nuotraukos atitinka šį nustatymą; spinduliavimo/atspindžio rodikliai niekada to nedaro, be to, šis nustatymas negali atkurti apkarpytų jutiklio duomenų (tam reikia sumažinti ekspoziciją). Praleidžiama monochromatinėje M3M. |
| `lattice filter [--list]` | Išvardyti Chloros žinomus filtro modelius (`RGN-IMX265`, `OCN`, `NGB`, …). Kameros filtras nuskaitomas iš jos modelio eilutės ir negali būti nustatytas: `--set NAME` tik suranda modelį, tada išeina iš `1` su `setting the filter model is not supported`. |
| `lattice power [--sleep]` | Patikrina maitinimo/šiluminius mazgus; įjungia/išjungia mažos galios budėjimo režimą. `--sleep` palieka kamerą miego režime (jos ryšio pralaidumo riba įjungta (`DeviceLinkThroughputLimitMode` `On`) nustatyta į mažiausią vertę, energijos taupymo režimas įjungtas, jei kamera jį turi) o visi kiti nustatymai grąžinami į pradinę būseną; nesėkmingas perėjimas į miego režimą atstatomas visiškai. Pažadinimas grąžina ryšį į tą būseną, kokią jis buvo prieš miego režimą (ribą, režimą ir paketų vėlavimą `GevSCPD`, kurį kamera išlaiko lygų 0, kol riba įjungta; įrašas saugomas šiame kompiuteryje, todėl pabudimas po užpakalinės dalies paleidimo iš naujo taip pat jį randa), arba, jei kamera, kurios šis kompiuteris nepervedė į miego režimą, atrodo esanti miego režime (jos riba nustatyta minimaliame lygyje), riba išjungiama, o `GevSCPD` paliekamas lygus 0 esant 80 (gamybiniai nustatymai); riba, nustatyta virš minimalios vertės, paliekama tokia, kokia yra. Zondas ir pažadinimas atkuria tai, ką jų atidarymas įrašė. |

> **Binningas visada yra „Vidurkis“.** `lattice resolution --binning N` nustato „Vidurkio“ binningą ir
> nesiūlo režimo perjungimo. „Sum“ binningas kiekvieną DN padaugina iš N², o tai kainuoja maždaug du stopus
> šviesių sričių rezervo kiekvienam binningo žingsniui; Chloros tą koeficientą vėl išskaičiuoja prieš
> radiometrinę grandinę, tačiau pikseliai, kuriuos „Sum“ nustūmė į jutiklio viršutinę ribą, yra apkarpyti ir negali būti
> atkurti. Tai nesuteikia nieko, ko nesuteiktų „Average“ – indeksas yra nepriklausomas nuo masto, o spinduliavimo
> skaliarai yra kalibruojami pagal „Average“ mastelį. Jei kamera jau yra nustatyta į „Sum“ (palikta
> taip kitu įrankiu arba atkūrus gamyklinius nustatymus), Chloros tai aptinka iš mazgo atskaitymo ir
> kompensuoja; paleidus „`lattice resolution --binning N`“ ji vėl nustatoma į „Average“. 1.2.0
> `--binning-mode` žymė vis dar priimama, todėl senesni skriptai veikia, bet tik išspausdina įspėjimą:
> pikselių sujungimas lieka „Vidurkis“.

> **Pakeitus grupavimą, pikselių formatą ir trigerį kameroje, niekas nepasikeičia.** Kameroje
> užkulisinė programa lieka atidaryta ir transliuoja duomenis (`cam-connect`, GUI), `lattice resolution --binning`
> neįsigalioja (parodoma, kad „binning“ vis dar galioja, `Binning: 1x1`), o
> `lattice format --set` ir `lattice trigger --mode` kamera atmeta
> (`SC_ERR_ERROR -1001`). Pirmiausia jį atleiskite (`lattice cam-disconnect -s <SN>`) arba nustatykite
> juos prisijungimo metu (`cam-connect --pixel-format …`, `array-connect --binning …`).

##### Ekspozicijos ir stiprinimo rodmenų skaitymas automatiniame režime

Kai veikia kameros `ExposureAuto` (`GainAuto`), jos `ExposureTime` (`Gain`) mazgas išlaiko **paskutinę įrašytą vertę**, o ne tai, ką kamera daro dabar. Kai kamera yra atidaryta (`cam-connect`, `array-connect`, GUI), `lattice configure --get`, numatytasis `lattice configure` vaizdas ir `lattice exposure` (be žymių) todėl rodo mazgo vertę, o po ja – pastabą su naujausiu kameros kadru:

```

ExposureTime = 2000.448
  note: ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448); the camera's newest frame was at 4900 us.
```

Kadrą nuskaitykite iš pastabos; mazgo reikšmė yra tik paskutinis įrašas. Per HTTP tas pats atsakymas „`POST /api/camera/<serial>/read-nodes`“ palieka „`nodes`“ nepakitęs ir prideda „`frame_values`“ (`{"ExposureTime": <us>, "Gain": <dB>}`, naujausio kadro) ir `notes` (aukščiau pateiktas sakinys). Abu jie rodomi tik tuomet, kai automatinis režimas nėra `Off` ir kadras yra žinomas; tik skaitymui atidaryta kamera neturi kadro ir negauna nė vieno iš jų. `lattice gain` (be žymių) spausdina tik mazgą — pastabai naudokite `lattice configure --get Gain`.

##### Ekspozicija masyvo elemente

Kol masyvas transliuoja duomenis, kiekvienas elementas veikia su įjungtu **`AcquisitionFrameRateEnable`**, kadrų dažniu, šiek tiek viršijančiu masyvo suaktyvinimo dažnį: jo periodas yra suaktyvinimo periodas, atėmus 15 % (ne mažiau kaip 3 ms), arba pačios kameros maksimalus dažnis, kai suaktyvinimo dažnis jau yra artimas jam. Todėl narys su įjungtu „`configure --get`“ šiuo nustatymu nuskaito „`AcquisitionFrameRateEnable`“ kaip „`True`“ ir „`AcquisitionFrameRate`“; įjungimas išsijungia, kai masyvas sustoja.**Nerašykite šių dviejų mazgų ant masyvo elemento** — masyvas juos sinchronizuoja su savo trigeriu ir perrašo.

Šis nustatymas taip pat yra elemento ekspozicijos riba: apie 85 ms esant 10 kadrų per sekundę trigeriui (100 ms periodas), esant **bet kokiam ROI ar binningui**. Iki versijos 1.3.0 mažas ROI vietoj to apribodavo elemento ekspoziciją iki jutiklio laisvojo veikimo periodo (4,9 ms esant 768 × 304), nepriklausomai nuo to, kaip lėtai masyvas suveikdavo.**Rankiniu būdu nustatytas `ExposureTime`, ilgesnis už ribą, sulėtina suaktyvinimą, kad jis tilptų**, kai jis įrašomas naudojant `lattice exposure --set` arba `configure --set` (arba per GUI). Matrica pirmiausia atidaro to elemento viršutinę ribą atitinkamai vertei ir leidžia lėtesniam dažniui veikti keletą suveikimo periodų, todėl įrašymas gali užtrukti keletą sekundžių ilgiau; tada suveikimas vyksta ne greičiau, nei leidžia ekspozicija. Automatinio dažnio matricoje anksčiau veikęs dažnis grįžta, kai kiekvienas elementas, kurio rankinė ekspozicija jį sulėtino, vėl pereina į automatinę ekspoziciją. Vertė, kurios pati kamera negali užfiksuoti, vis tiek atmetama, o klaidos pranešime nurodomas jos priimtinų verčių diapazonas; nario automatinė ekspozicija atstatoma, jei ji buvo išjungta tik tam rašymo veiksmui. Recepto `apply` ekspozicija nesulėtina trigerio: jis išlaikomas viršutinėje riboje. Chloros niekada nesutrumpina rankinės ekspozicijos, kad ji atitiktų greitesnį trigerį; fiksuotas dažnis (`array-connect --target-fps F`), kuris yra greitesnis nei leidžia ekspozicija, palieka tam nariui pakankamai ilgą viršutinę ribą, ir tas narys priima trigerius ne greičiau, nei leidžia jo ekspozicija.

#### Kalibravimas ir jutikliai

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Kalibruoti pagal atspindžio taikinį. Jam ieškoma iki `--attempts` kadrų (numatyta reikšmė – `30`); nepilnas kadras (GVSP paketo praradimas) kainuoja vieną bandymą, o ne visą ciklą. Bandymas baigiasi nesėkme, jei pasiekiamas `no target detected in N attempts`, arba `no complete frame in N attempts: <last error>`, jei nė vienas rėmelis nepasiekė nepaliestas. |
| `lattice dls {--connect \| --spectrum \| --irradiance} --mac MAC [--filter NAME] [--camera SERIAL] [--json]` | DAQ-M spektras ir juostų stiprumai, nuskaityti per „Bluetooth“ (BLE) naudojant užkulisinę sistemą. `--mac` yra paties jutiklio BLE MAC adresas ir reikalingas atliekant bet kurį iš trijų veiksmų: numatytosios reikšmės nėra, nes kiekvienas įrenginys turi savo (`daq pool-discover --only ble` pateikia jų sąrašą); be jo komanda išeina iš `1`. Jei veiksmas nenurodytas, išvedama pagalba. `--camera SERIAL` integruoja išvestus juostų dydžius naudodamas tos kameros pačios išmatuotą spektrinį atsaką, o ne filtro modelio flotės kreivę — RGB kameroms, kai kameros kalibravimo paketas yra įkeltas (prijungtas) arba išsaugotas šio kompiuterio talpykloje. Antraštėje nurodoma, kurios kameros kreivė buvo panaudota: `Band magnitudes (camera <SERIAL>):` arba `Band magnitudes (model <filter>):`, jei kamera neturi savo (tai nėra RGB kamera, arba paketas neįkeltas ar neišsaugotas) ir vietoj to buvo panaudota standartinė kreivė. Per HTTP „`POST /api/dls/cli-read`“ priima „`camera_serial`“ ir grąžina „`band_magnitudes_source`“. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Taiko vinjetės korekciją esamiems vaizdams, kiekvienam pagal jo objektyvo plokščio lauko (iš EXIF „Model“). `--lens-model` taikoma vaizdams, kurių „Model“ nenurodo jokios kameros; vaizdas, kurio „Model“ prieštarauja šiai sąlygai, praleidžiamas. |

#### Daugiakameris režimas (trumpalaikės sesijos)

Laikina komanda atidaro kameras vienam vykdymo ciklui ir uždaro jas, kai jis baigiasi: `multi-capture` be prijungto nuolatinio masyvo, `multi-stream` ir `multi-test` čia, o žemiau – `align-calibrate`, `align-apply`, `align-stream` ir `index --live`. Visoms joms galioja šios taisyklės:

- **`--serials SN1,SN2,…` pasirenka kameras.** Atidaromos tik tos kameros, o pirmoji iš jų suaktyvina trigerį (jei nurodyta, tai taikoma ir `align-calibrate`, `--reference`). Jei serijinis numeris nėra tinkle, vykdymas baigiasi nesėkme dar prieš atidarant bet kurią kamerą. Jei nėra `--serials`, atidaromos visos tinkle esančios kameros. Kamera, kurią Chloros jau valdo (GUI, `cam-connect`, `array-connect`), atsisako vykdyti užduotį, nepriklausomai nuo to, ar `--serials` ją nurodo, ar ne: ji dalijasi tinklu, kuriam skirtas vykdymas. (`index --live` be `--serials` vietoj to atidaro savo profilyje įjungtas kameras: žr. [Indeksas / Augmenijos matematika](#index--vegetation-maths).)
- **Kameros dalijasi tinklu taip, kaip jį dalijasi `array-connect`.** Kiekviena kamera pritaikoma pagal savo dalį to, ką perduoda pagrindinis ryšys (tarpusavio paketų vėlavimas, o ne kameros pralaidumo riba, kurios 31,25 MB/s minimumą keturios kameros 1 GbE tinkle jau viršija), todėl keturios kameros veikia 1 Gbps ryšio linijoje. Kai kameros negali visos vienu metu siųsti duomenų lėčiausiu pasiekiamu greičiu, jos įjungiamos viena po kitos, kiekvienai skiriant atskirą ryšio liniją, ir komanda išspausdina „`Serial emit: the wire cannot carry these cameras at once, so each is triggered <ms> ms after the one before; their frames are not exposed together`“. Kai net viena kamera vienu metu netelpa, vykdymas atmetamas dar prieš bet kuriai kamerai pradedant transliuoti: `array over-subscribes the wire: <n> cameras demand <x> MB/s but the collision-safe ceiling is <y> MB/s. …`, HTTP 409, išėjimas `1`.
- **Visos kameros turi perduoti duomenis, kitaip operacija žlugs.** Kamera, kuri neperduoda nė vieno kadro, reiškia klaidą: komanda išveda „`Error: … no frame from <n> of <total> cameras: SN:<serial> (<why>), …`“, kur „`<why>`“ yra tos kameros duomenų gavimo klaida, po to nurodoma jos priežastis, ir užbaigiama „`1`“. Kadras, gautas su GVSP paketų praradimu (`IncompleteBufferError: GVSP packet loss …`), reiškia, kad tinklas nepalaiko visų kamerų bendro pralaidumo (`Every missing frame arrived with GVSP packet loss: …`): patikrinkite pagrindinio kompiuterio ir komutatoriaus aukštyninio ryšio linijos greitį (`lattice network`), naudokite „jumbo“ rėmus, arba įjunkite mažiau kamerų su komanda `--serials`. Kamera, kuri nieko neperdavė, paprastai negauna pagrindinės kameros signalo: patikrinkite jos M8 sinchronizavimo kabelį, tada paleiskite komandą `lattice multi-detect`. Ką kiekviena komanda atlieka su rėmelių grupe, kurioje trūksta kameros, nurodyta atitinkamoje eilutėje.
- **Kiekviena kamera grąžinama į tą pačią būseną, kokioje buvo rasta.** Pasibaigus vykdymui, kiekviena kamera grąžinama į tą pačią būseną, kokioje buvo, kai vykdymas ją atidarė: `TriggerMode`, `TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, ir `LineMode` / `LineSource` ant `Line2` ir `Line3` (o trigerio įvesties trikdžių pašalinimo masyvas perduoda pavaldžiojo įrenginio liniją: `LineFilterSelector` / `LineFilterWidth`); jo `PtpEnable` (ir su juo nustatytas `PtpSlaveOnly` / `PtpDomainNumber`); `ExposureAuto` / `GainAuto` (ir `ExposureTime` / `Gain`, kur buvo nustatyta rankinė reikšmė); `TargetBrightness`, `ExposureAutoLimitAuto` / `ExposureAutoUpperLimit` / `ExposureAutoLowerLimit`, `GainAutoUpperLimit`, `ExposureAutoDamping`; `BalanceWhiteAuto` (ir kiekvienas `BalanceRatio`, kur baltos spalvos balansas buvo nustatytas rankiniu būdu); `BlackLevelAuto` / `BlackLevel`; `PixelFormat`, pikselių sujungimas, `Width` / `Height` / `OffsetX` / `OffsetY`; `GammaEnable` (ir `Gamma`, kur buvo įjungta gama), `LUTEnable`, `ColorTransformationEnable`; `ReverseX` / `ReverseY`; `UserSetSelector`; paketo dydis (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`); perdavimo dažnį, `DeviceLinkThroughputLimitMode` (ir `DeviceLinkThroughputLimit`, kai režimas buvo `On`), tarppaketų tarpas `GevSCPD` ir paleidimo laiko poslinkis `GevSCFTD`, kuriuos kiekvienai kamerai suteikia masyvas; kiekvieno srauto įjungiamus duomenų blokus (`ChunkModeActive`, kiekvienas `ChunkEnable` pagal savo `ChunkSelector`); `AcquisitionFrameRateEnable` (ir `AcquisitionFrameRate`, kur įjungimas buvo įjungtas — kai jis išjungtas, dažnis yra tik skaitymo režimu ir nuskaito tiek, kiek leidžia sinchronizacija, taigi tai nėra nustatymas). Užrašoma tik tai, kas skiriasi: kamera pirmiausia sustojo, taip pat nustatomi stimuliavimo parametrai ir kadrų dažnis prieš ekspoziciją, kuriai reikalingas atitinkamas kadrų periodas (kamera apriboja `ExposureTime` vienu kadrų periodu). Naudojant „`--keep-settings`“, trigerio ir sinchronizavimo linijos laidai atjungiami, kamera veikia laisvuoju režimu, o likusi informacija išsaugoma pačios kameros atmintyje pagal nustatytą veikimo režimą; „`--clear-settings`“ ten įrašo gamyklinius nustatymus.
- **Ekspozicija nustatoma pagal vaizdą prieš išsaugant pirmąjį kadrą.** Serija paima sinchronizuotas kadrų grupes ir jas atmeta, tuo pačiu nustatydama kiekvienos kameros ekspoziciją ir stiprinimą pagal tuos kadrus. Siekiama, kad kameros taikinio vidutinis ryškumas neviršytų 1 % kadro apkarpymo; scena su ryškiais šviesiais taškais nepasiekia šio tikslo. Tai paprastai užtrunka 2–6 kadrų grupes ir ne daugiau kaip 5 s.
  - Kiekviena kamera nukreipiama į tikslą, nuo kurio ją paleidžia prijungtas masyvas, pagal savo filtrą: „TargetBrightness“ **40**daugiaspektrinėje kameroje (RGN, OCN, NGB) ir**60** RGB kameroje. Tai sudaro pusę ekspozicijos tikslo, kurį nustato 80 (tris ketvirtadalius – RGB atveju), todėl mažiau tikėtina, kad bus apkarpytas ryškus atspindžio skydelis. `--brightness N` (0–255) vietoj to nustato kiekvienos kameros tikslą (`multi-test` neturi `--brightness`: jo automatinė ekspozicija išjungta). Backend žurnale nurodoma kiekvienos kameros ekspozicijos vertė: `[transient-ae] AE target per camera (filter default): SN:<serial> <target>, …`.
  - Pirmiausia nustatoma ekspozicija, neviršijant pačios kameros automatinės ekspozicijos ribos. Stiprinimas taikomas tik likusiai daliai.
  - Nustatyta ekspozicija ir stiprinimas išlaikomi per visą veikimo laiką, todėl kiekvienas „`multi-stream`“ kadras fotografuojamas su ta pačia ekspozicija. Įrašymas, kuris turi prisitaikyti prie besikeičiančio apšvietimo, turi būti atliekamas prijungtame masyve (`array-connect`), kurio automatinė ekspozicija nuolat koreguojama.
  - `--exposure` ir `--gain` išlaikomi tiksliai tokie, kokie nurodyti. Kai pateikiami abu, nė vienas kadras nėra atmestas.
  - Pasibaigus ciklui, kiekvienos kameros automatinė ekspozicija vėl įjungiama.
  - Užkulisio žurnale pateikiamas rezultatas: `[array-ae] exposure solved in <s> s (<n> warm-up groups): SN:<serial> <ms> ms <dB> dB mean <m>/<target>, …` arba `exposure NOT solved after …` su priežastimi, o `(not settled)` – kiekvienai kamerai, kuriai tai nebuvo taikoma. Tokiu atveju kadrai vis tiek yra užfiksuojami.
  - `CHLOROS_TRANSIENT_AE_SETTLE_S` keičia 5 s ribą; `0` ją išjungia (žr. [Aplinkos kintamieji](#environment-variables)).

**Kol Chloros valdo bet kurią kamerą** (grafinę vartotojo sąsają, „`cam-connect`“ arba prijungtą masyvą), „`multi-stream`“, „`multi-test`“, `multi-detect`, `align-apply`, `align-stream` ir `index --live` atsisako veikti, nes jie atidaro visas tinklo kameras, o antrasis atidarymas užimtos kameros atidarymas gali sukelti užkrovos sistemos gedimą. (`index --live` atsisako veikti, kai kamera, kurią ji bandytų atidaryti, yra užimta: tai gali būti `--serials` arba jo profilio kameros, taip pat bet kuri kamera, priskirta profiliui, kuriame nėra nurodyta jokia kamera.) Fono sistema atsako HTTP 409 kodu „`CAMERA_HELD`“, nurodydama kiekvieną užimtą kamerą ir atlaisvinimo komandas (`chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all`), o komanda baigiasi `1`. Prijungus masyvą, `array-capture --continuous` jį įrašo vietoj `multi-stream`, o `array-capture` (pagal numatytuosius nustatymus išlygintas) išsaugo išlygintą rinkinį vietoje `align-apply`; `multi-capture` ir `align-calibrate` naudoja prijungtą masyvą, kuriame laikomos jų kameros, vietoj to, kad jas atidarytų.

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice multi-info [--serials SN1,SN2,…]` | Išvardyti visas kameras, turinčias sinchronizavimo vaidmenis; naudojant `--serials` – tik tas, kurios pirmos, kaip `MASTER`. |
| `lattice multi-capture [--serials SN1,SN2,…] [--format FMT] [--jpeg-quality N] [--processing LEVEL] [--brightness N] [--no-daq] [--daq-tolerance-ms MS]` | Po vieną sinchronizuotą kadrą iš kiekvienos kameros. Kai prijungtas nuolatinis masyvas, išsaugo **visus eksporto tipus pagal numatytuosius nustatymus**ir DAQ, kuris jį nuskaito ([DAQ, nuskaitantis užfiksuotą vaizdą, išsaugo](#the-daq-reading-a-capture-saves)); laikinasis atsarginis variantas be masyvo yra**tik be debayeringo** (likusiems pirmiausia paleiskite `array-connect`). `--brightness` taikomas tik atsarginiam režimui: prijungtas masyvas išlaiko savo automatinę ekspoziciją. Atsarginis režimas neišsaugo `.daq` (`--no-daq` / `--daq-tolerance-ms` čia neturi jokio poveikio), o `multi-capture` nepriima `--force-daq`. Atsarginėje sistemoje kamera, kuri nepateikia jokio kadro, nesėkmingai užbaigia įrašymą (`Error: multi-capture failed: no frame from …`, išeina `1`) ir joks tos kadrų grupės failas nėra išsaugomas. |
| `lattice multi-stream [--serials SN1,SN2,…] [--fps F] [--count N] [--format FMT] [--jpeg-quality N] [--brightness N]` | Srauto sinchronizuoti kadrai (laikini), `--fps` numatytasis `3`, kol paspaudžiama „Ctrl+C“ arba `--count` grupės. Kadrų grupė, kuriai kamera nieko nepateikė, nėra įrašoma, o trys iš eilės nutraukia srautą. Jei buvo praleista bet kuri grupė, vykdymas baigiamas išspausdinant „`Incomplete: <n> frame group(s) not written, a camera delivered nothing (SN:<serial> x<count>, …)`“, taip pat „`Error: the stream stopped: …`“, nurodant kamerą, kuri nutraukė srautą, ir uždaroma „`1`X. Sustoja, kai diskas užsipildo, išspausdina „`Failed:`“ / „`Disk full:`“ ir užsidaro su kodu „`1`“; bet koks failas, kurio nepavyko įrašyti, taip pat uždaro programą `1`. |
| `lattice multi-test [--serials SN1,SN2,…] [--count N] [--ptp-settle SECONDS]` | GPIO sinchronizavimo laiko testas (trumpalaikis), `--count` numatyti `10` suveikimai. Išspausdina, kaip dalijamasi tinklu (`Wire: each camera paced to <x> MB/s (<n> on a <y> MB/s ceiling, <link> Mbps link)`, o eilutė `Serial emit:` rodomi, kai kameros suveikia viena po kitos, o laiko žymos tuomet atspindi šį laiko poslinkį), po vieną `Sync group: master SN:<serial> -> SN:<serial>, …` eilutę kiekvienai sinchronizavimo grupei (`(no slaves: not synced)` – kamerai be sinchronizavimo kabelio, kuri suveikia savarankiškai), tada po vieną eilutę kiekvienam suveikimui: `[  0] SN:<serial>=<timestamp> \| …`, o `SN:<serial>=(no frame)` – kamerai, kuri to suveikimo metu nieko neperdavė. Tada po vieną eilutę „`Offsets from master SN:<serial> (<PTP / device (PTP off)> clock, <n> trigger(s)):`“ ir „`SN:<serial>: mean <±x> us, jitter <y> us`“ kiekvienai kamerai, kiekvienai kamerai, palyginti su pagrindine kamera per vieną taktą: suveikimas, kurio žymos sumaišo kamerų įrenginių laikrodžius, o PTP (PTP išjungtas arba vis dar sinchronizuojamas) yra praleidžiamas, o `Note:` juos skaičiuoja. Bet koks praleistas kadras reiškia, kad testas neišlaikytas (`Error: multi-test failed: <k> of <n> frame groups incomplete (SN:<serial> missed <k>); …`, išeiti iš `1`); trys iš eilės neišsamūs trigeriai nutraukia testą anksčiau laiko. Bandymas taip pat laikomas nesėkmingu (išeiti iš `1`), jei mažiau nei du trigeriai sinchronizuoja visas kameras pagal vieną laikrodį (`… the clocks cannot be compared`) arba jei laikrodžiai niekada nesutampa: PTP laikrodžiai skiriasi nuo pagrindinio daugiau nei 1 ms (pridėjus bet kokį serijinio lygmens poslinkį) (`… PTP is not locked`), arba įrenginių laikrodžių nuokrypis tarp trigerių padidėja daugiau nei šis dydis. Jei PTP „grandmaster“ neveikia, programa jį paleidžia, įjungia kiekvienos kameros PTP (kaip tai daro `array-connect`, nepriklausomai nuo tinklo konfigūracijos) ir prieš pirmąjį trigerį laukia, kol kiekvienos kameros PTP laikrodis sinchronizuosis su „grandmaster“ ir stabilizuosis: `PtpStatus` „Slave“ sinchronizuotas su vienu pagrindiniu laikrodžiu, nuokrypis neviršija 100 us ir išlieka stabilus 3 s, o kiekviena kamera taip sinchronizuota be pertraukos 120 s — maždaug 2 minutes nuo įjungimo. Laikrodis, kuris ką tik sinchronizavosi, apie 2 minutes po to, kai nuskaito „Slave“, nukrypsta iki ~200 us, o pačios kameros `PtpOffsetFromMaster` to nerodo; per 120 s kameros pasiekia ~10 us nuokrypį nuo stabilaus laikrodžio. Kamera vis dar yra sinchronizuota nuo ankstesnio bandymo, kurio metu PTP buvo įjungtas (kaip tai matė šis backend), skaičiuoja nuo to sinchronizavimo momento, todėl vienas bandymas po kito nevyksta iš naujo; kamera, kurią bandymas įjungia, arba kamera, rasta su įjungtu PTP, kurios sinchronizavimo šis backend nematė (įjungta kitur arba prieš backend perkrovimą), laukia savo 120 s, o abiejų ką tik sinchronizuotų kamerų masyve jos vis dar laukia savo laiko. Kamera, kuri išeina iš „Slave“ režimo, viršija 100 us arba laukimo metu 3 kartus iš eilės (1,5 s) seka kitą „grandmaster“ signalą, vėl pradeda skaičiuoti; vienas ar du tokie rodmenys užregistruojami (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`), o skaičiavimas tęsiasi; „grandmaster“ identifikatorius, kurio neįmanoma nuskaityti, laikomas nežinomu, o ne pasikeitimu. Jei visų kamerų skaičiavimas prasideda iš naujo vienu metu (per sekundę), tai reiškia, kad keičiasi „grandmaster“ laikas, o ne kamerų: užkulisinės sistemos žurnale nurodoma `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …`, `ptp_lock` įtraukia jį į `grandmaster_steps`, o pasibaigus laukimo laikui klaida prasideda nuo `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …`. Šiam atvejui riba nėra pratęsiama. `--ptp-settle SECONDS` nustato vykdymo stabilizavimo laiką (0–600; `0` – be stabilizavimo laukimo). Laukimo trukmė ribojama stabilizavimo laiku plius 90 s (pagal numatytuosius nustatymus – 210 s, ne mažiau kaip 90 s), o komanda laukia užpakalinės grandies 240 s plius stabilizavimo laikas (pagal numatytuosius nustatymus – 360 s). Ji išveda pranešimą „`PTP: locked after <s> s (each camera locked <settle> s, already locked: SN:<serial>, …; from the grandmaster: SN:<serial> <±x> us, …)`“, o galinio įrenginio žurnale kas 5 s įrašoma eilutė su kiekvienos kameros būsena ir nuokrypiu, kiek laiko ji jau yra užfiksuota ir kiek laiko liko: `[array-sync] PTP at <s> s: SN:<serial> Slave <±x> us for <s> s (<s> s left), … -- <s> s left, the wait gives up at <s> s`. Kiekviena kamera įjungiama su išjungtu PTP; tuomet laikrodžiai sinchronizuojasi per maždaug 10 s. Po to kiekviena kamera grąžinama į būseną, kurioje ją rado testas — jos PTP, automatinė ekspozicija ir stiprinimas, kuriuos testas vykdymo metu laiko išjungtais, o visa kita (išskyrus atvejus, kai rodomas `--keep-settings`) — taigi kamera, kurios PTP liko įjungtas (`lattice configure -s <serial> --set PtpEnable=True` arba bandymas su `--keep-settings`), lieka sinchronizuota, ir kai šis užkulisinis modulis pamato, kad ji sinchronizuota nustatytą stabilizavimo laiką, vėlesnis bandymas jos nelaukia. Jei laikrodžiai nesusiderina laukimo metu, bandymas žlunga dar prieš suveikimą (`PTP: NOT locked …`, išėjimas `1`) ir nurodo, ką daryti: kamera, kuri niekada nebuvo užfiksuota (`PtpStatus Listening`), reiškia, kad pagrindinis serveris jos nepasiekia (`time-sync status`); ta, kuri nuolat iškrisdavo, pavadinta pagal tai, kiek laiko ji buvo užfiksuota (`… had not been locked for the 120 s settle time … (SN:<serial> locked <s> s, <s> s to go)`); laikrodžiai vis dar suderinami: patikrinkite `time-sync status`, o kad jos būtų užfiksuotos prieš testą, įjunkite PTP ir palikite jį įjungtą (`lattice configure -s <serial> --set PtpEnable=True` kiekvienai kamerai arba atlikite testą su `--keep-settings`; testas atkuria tai, ką nustatė, todėl jis lieka įjungtas), tada paleiskite jį. |
| `lattice multi-detect [--line LINE] [--json]` | Suraskite kameras M8 sinchronizavimo linijoje ir numatytąjį pagrindinį įrenginį: RGB kamerą (modelio pavadinimas baigiasi `-FRGB`), jei ji prijungta kabeliu, kitaip – pirmąją pagal serijos numerį prijungtą kamerą. M8 kabeliai susilieja vienoje jungtyje, todėl **bet kuri prijungta kamera gali būti pagrindinė** — užrašytas „`MASTER`“ yra numatytasis nustatymas, o ne faktinis sujungimas. Kiekviena kamera paeiliui siunčia signalą linija, o kitos tikrinamos, ar gauna jos impulsą; kamera, jau rasta prijungtų kamerų grupėje, nėra tikrinama dar kartą, todėl visiškai sujungtos kameros patikrinimas trunka vieną ciklą. Jei nėra `--line`, tikrinama `Line2`, tada „`Line3`“. Išspausdina „`MASTER: SN:…`“, po vieną „`SLAVE: SN:…`“ už kiekvieną kitą prijungtą kamerą ir „`(probed on <line>)`“; „`--json` prideda visą rezultatą (`master`, `slaves`, `no_cable` — kameros, kurių impulsas nepasiekė — `line`, ir kiekvienai kamerai atskirai `results`). Baigus, kiekvienai kamerai grąžinami ankstesni trigerio ir sinchronizavimo linijos nustatymai (tie patys mazgai kaip nurodyta aukščiau). |

#### Suderinimas

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice align-calibrate [--serials SN1,SN2,…] [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N] [--brightness N]` — papildomi detektoriaus/suderinimo reguliatoriai `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, RANSAC reguliatoriai `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, kelių kadrų derinimas `[--averaging mean\|median\|inlier_weighted]`, geometriniai apribojimai `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, erdviniai apribojimai `[--roi X0,Y0,X1,Y1] [--mask PATH]` irpagal pavaldinį atliekami perrašymai `[--per-cam-override SN:KEY=VALUE]` (pakartojami) | Apskaičiuokite suderinimo profilį iš tiesioginių kamerų. Numatytieji nustatymai: `--method orb`, `--model rigid`, `--frames 3`, `--save alignment_profile.json` (tame aplanke, iš kurio paleidžiate programą), `--max-features 2000`, `--ratio-threshold 0.75`, `--matcher bf`, `--knn-k 2`, `--ransac-threshold-px 3.0`, `--ransac-iters 2000`, `--ransac-confidence 0.99`, `--averaging mean`. (`project align calibrate` turi savo numatytuosius parametrus; žr. [jo lentelę](#project-align-calibrate-options).) `--prefilter` numatytasis nustatymas yra `gradient` (kraštų žemėlapis; atitinka GUI/masyvo suderinimo įrankį — kraštai išlieka visose spektrinėse juostose). `--matcher flann` pasiteisina, kai yra daugiau nei ~5000 požymių; `--averaging median` yra atsparus vienam blogam įrašui, `inlier_weighted` sveria pagal atitikimų skaičių; `--lock-scale` projektuoja į artimiausią pasukimą (be mastelio), `--lock-axis` nuliuoja vieną transliacijos komponentą; `--mask` taikomas kiekvienai kamerai (kameros nustatymams naudokite `--per-cam-override`, pvz., `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` atsisako išsaugoti kalibravimą, kurio reprojekcijos RMS viršija ribą. `--method manual` paima savo taškus iš `--points PATH`: JSON-objektą `{"<serial>": [[x, y], ...], ...}`, kuriame pateikiamos kiekvienos kameros tų pačių objektų pikselių koordinatės, įskaitant etaloninę kamerą (mažiausiai po 2 kiekvienai kamerai); kiekvienas `--frames` sprendžiamas remiantis šiais taškais. Kai kameros yra sujungtos į masyvą, kalibravimas naudoja to masyvo tiesioginius kadrus, o profilis taip pat tampa masyvo suderinimu jo užfiksuotoms nuotraukoms. Kitais atvejais atidaromas [laikinasis masyvas](#multi-camera-transient-sessions): jei kamera nepateikia kadro nė viename iš `--frames`, kalibravimas baigiasi nesėkme (išėjimas iš `1`, nurodant jos pavadinimą), ir profilis nėra išsaugomas. |
| `lattice align-apply --profile PATH [--serials SN1,SN2,…] [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N] [--brightness N]` | Užfiksuokite vieną suderintą daugiabandį kadrą. `--bit-depth` pagal numatytuosius nustatymus suderina su kamera; `--no-crop` išlaiko visą kadrą (užpildo juodai); `--interpolation` (numatyta reikšmė `linear`) ir `--border-mode`/`--border-value` (numatyta reikšmė `constant`/0) valdo procesoriaus deformaciją — GPU kelias bet kuriuo atveju yra bilinearinis. Atidaro [laikinąjį masyvą](#multi-camera-transient-sessions): kamera, kuri nepateikia jokio kadro, nesėkmingai užbaigia įrašymą (išeina iš `1`, pavadindama jį), ir niekas nėra įrašoma. |
| `lattice align-stream --profile PATH [--serials SN1,SN2,…] [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N] [--brightness N]` | Srauto suderinti daugiabandžiai kadrai (tie patys „warp“ reguliatoriai kaip ir `align-apply`), `--fps` numatytasis `3`. Atidaro [laikinąjį masyvą](#multi-camera-transient-sessions): kadrų grupė, kuriai kamera nepateikė nieko, nėra išsaugoma, o trys iš eilės nutraukia srautą. Jei buvo praleista bet kuri grupė, vykdymas baigiamas išvedant `Incomplete: <n> frame group(s) not saved, a camera delivered nothing (SN:<serial> x<count>, …)` ir `Error: …`, ir uždaroma su pranešimu „`1`“. Srautas, kuris baigiasi dėl bet kokios kitos klaidos, taip pat išspausdina „`Error: …`“ ir uždaromas su pranešimu „`1`“. |
| `lattice align-info --profile PATH [--json]` | Rodo profilio duomenis. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Keičia sluoksnių tvarką. |

#### Indeksas / Augmenijos matematika

```bash
# Offline: compute NDVI from an aligned multi-band TIFF (an RGN camera's bands)
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: open the cameras, capture one aligned set with a profile made by
# `lattice align-calibrate`, and compute the index from it
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --save-multiband -o output/
```

**`--channel` priskiria iš anksto nustatytų parametrų simbolius juostoms** (NDVI: `red`, `nir`), po vieną `--channel SYMBOL=BAND` kiekvienam.

- **`--live`: tik simboliui, į kurį stekas negali atsakyti.** Kaip ir `--input` atveju, iš anksto nustatytas simbolis, kurį praleidžiate, priskiriamas tam steko juostos (profilio įjungtų kamerų juostų), kurios pavadinimas prasideda tuo simboliu. Tai patikrinama prieš atidarant bet kurią kamerą: simbolis, į kurį neatsako nė vienas dažnių juostos arba kelios dažnių juostos, tuomet atmetamas, juos įvardijant, ir išeinama iš `1`. Kiekvienam simboliui pritaikytame `--formula` reikia `--channel`.
- **`--live` produktai nurodo, kam jie priklauso.** `index.tif` ir `index_LUT.png` pateikia kameros, esančios steke, duomenis (gamintojas, modelis, serijos numeris), jos fotografavimo laiką (etaloninės kameros ekspozicijos trukmę, UTC) ir objektyvą, o abu nurodo indeksą kaip savo vieną juostą (`XMP-Camera:BandName` `NDVI`; spalvotas PNG failas nurodo indeksą, o ne jo spalvų kanalus), kaip tai daro `--input` produktas. Indeksas, sudarytas iš kelių kamerų juostų, vietoj to nurodo kiekvieną iš jų, o jo pavadinimas yra `Model` — žr. [Iš kelių kamerų sukurti failai](#files-made-from-several-cameras).
- **`--input`: skirta tik simboliui, kurio failas negali nurodyti.** Jei simbolį praleidžiate, imamas tas failo juostos pavadinimas, kurio pradžia sutampa su tuo simboliu: `red` pasirenka `Red_660` arba `Red_660_RGN`, o `nir` pasirenka `NIR_850`. Pavadinimai paimami iš paties failo: iš paties steko juostų sąrašo arba, Chloros spinduliavimo / atspindžio įrašo arba `process` produkto, jo `Chloros:Band{R,G,B}Name` / `XMP-Camera:BandName` žymų. Taigi `lattice index --input <reflectance capture> --preset NDVI` nereikia `--channel`.
- **Dvi juostos, atitinkančios vieną simbolį, nėra spėjamos** (9 kamerų stekas turi dvi NIR juostas): pavadinkite tą vieną. Simbolis, kuriam niekas neatitinka, atmetamas su „`channel_map missing entries`“, išeikite iš „`1`“.**Juostų pavadinimai steke** nurodo kameros filtrą: M3C kameros juostos yra `<band>_<filter>` — `NIR_850_RGN`, `Green_550_RGN`, `Red_660_RGN` RGN kameroje; `NIR_808_OCN`, `Cyan_490_OCN`, `Orange_615_OCN` OCN kameroje; `Blue_475_RGB`, `Green_550_RGB`, `Red_625_RGB` RGB kameroje; `Blue_475_NGB`, `Green_550_NGB`, `NIR_850_NGB` NGB sistemoje — o „M3M“ kamerosvienintelis juostos numeris yra `Mono_<filter>` (`Mono_660`, `Mono_850`). `lattice align-info --profile PATH` nurodo profilio juostas.

Pilnas žymių rinkinys: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI/NDRE/EVI/SAVI/GNDVI/…), `--formula EXPR`, `--channel SYM=BAND` (pakartojamas), `--capture-level raw|debayered|radiance|reflectance|unknown` (perrašo šaltinio TIFF faile įrašytą fiksavimo lygį; numatytasis nustatymas: skaitoma iš TIFF metaduomenų, o jei jų nėra – naudojamas Chloros failo `Chloros:ProcessingLevel`, taigi atspindžio fiksavimas praneša `reflectance`), `--output PATH` / `-o` (neprisijungus: kelias; jei nurodytas vaizdo plėtinys, failai pavadinami pagal jo šaknį — „`-o ndvi_FRGN.png`“ įrašo „`ndvi_FRGN.tif`“ ir „`ndvi_FRGN_LUT.png`“ — o be jo naudojamas priešdėlis, „`-o ndvi`“ -> `ndvi.tif`, `ndvi_LUT.png`; numatytasis variantas šalia įvesties, kaip „`<input name>_<preset>`“; su „`--live`“ ir be „`-o`“ – aplankas „`output/`“ tame aplanke, iš kurio paleidžiate komandą), `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. Su `--live` taip pat taikomi išlyginimo deformacijos reguliatoriai: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`, `--brightness N` (kamerų automatinės ekspozicijos tikslas, žr. trumpalaikių sesijų taisykles), `--serials SN1,SN2,…`. `--live` atidaro [trumpalaikių sesijų masyvą](#daugiakamerinių-tranzitinių-sesijų): kamera, kuri nepateikia jokio kadro, nesėkmingai užbaigia vykdymą (išeina iš `1`, jį pavadindama), ir nėra įrašomas joks stekas ar indeksas. Ji atidaro `--serials` (pirmoji suaktyvina trigerį), kitaip – profilio kameras, kurių įrašas `layer_order` yra įjungtas, pirmiausia jos etaloną – net jei etalono juostos yra išjungtos, nes kitos yra suderintos su su ja ir ji suaktyvina trigerį. Kamera, kurios juostos yra išjungtos, niekada nėra kalibruojama, todėl profilyje išjungta RGB-kamera nebepraleidžia atspindžio indekso (`no-absolute-radiance-scale`). Profilis, kuriame nenurodytos jokios kameros, atidaro visas tinklo kameras, kaip ir anksčiau.

Kiekviena forma veikia užkulisiuose, todėl ji turi būti paleista: paleiskite jį iš Chloros GUI arba naudodami komandą, kurią CLI išspausdina, kai negali prie jo prisijungti. Neprisijungus, foninė programa nuskaito „`--input`“ ir pati įrašo TIFF su plaukiojančiojo kablelio skaičiais bei spalvotą PNG, todėl santykinis „`--input`“ arba „`-o` atitinka aplanką, iš kurio paleidžiate komandą (žr. [Konvencijos](#conventions)). Abu neprisijungus sukurti produktai turi įvesties kameros įrašą, kaip ir `process` indekso eksportas: gamintojas / modelis / serijos numeris, GPS, objektyvo žymos, fotografavimo laikas (pats įvesties laikas; jei EXIF duomenyse jo nėra, tai užfiksuotas ekspozicijos momentas arba failo vardo žyma, UTC laiku) ir MAPIR kalibravimo identifikatorių — niekada nenaudojamas „raw-DN“ kalibravimas, kuris netaikomas indeksui — taip pat „`Chloros:ProcessingLevel=index`“ ir indekso išraišką; TIFF su plaukiojančiu kableliu nurodo vieną juostą, išankstinį nustatymą. `--list-presets` ir `--list-gradients` išspausdina užkulisio išankstinius nustatymus ir gradientų lenteles. Formulė, kurią atmetė užpakalinė dalis, arba `--channel` juosta, kurios TIFF-e nėra, išeina iš `1` su užpakalinės dalies pateikta priežastimi.

> **`--channel` simboliai yra jautrūs didžiosioms ir mažosioms raidėms.** Simbolių pusė turi tiksliai atitikti nustatymų kanalų pavadinimus (nustatymuose naudojamos mažosios raidės, pvz., NDVI = `red`,`nir` — patikrinkite `--list-presets`), o juostos pusė turi sutapti su juostos pavadinimu suderintame rinkinyje (arba būti juostos indeksu, skaičiuojamu nuo 0, neprisijungus prie interneto). `--channel red=Red_660_RGN --channel nir=NIR_850_RGN` veikia; `--channel RED=660` nesuveikia ir rodo `channel_map missing entries` klaida.

#### `lattice index` — daugiabandžių stekų kaukės

`--capture-level radiance` arba `reflectance` kiekvienos kameros 3×3 atskyrimas gali apriboti juostą iki 0 (žr. [Išskyrimo apribojimo kaukė](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on)). Daugiabandis rinkinys apima kelių kamerų juostas, ir kiekviena kamera apriboja savo juostą, todėl `--live --save-multiband` šalia rinkinio įrašo **po vieną kaukę kiekvienai šaltinio kamerai**:

```
output/
├── index_multiband.tif
└── clamp_masks/
    ├── index_multiband_SN<serial A>-<filter A>_clampmask.tif
    └── index_multiband_SN<serial B>-<filter B>_clampmask.tif
```

- Kiekviena kaukė yra vienos juostos `uint8` **rinkinio** pikselių tinklelyje (taikant tą patį suderinimo iškraipymą ir apkarpymą), su tos kameros bitais: bitas 0 = jos R kanalo juosta, bitas 1 = G, bitas 2 = B. Steko juosta, paimta iš kameros kanalo *c*, apribojama ten, kur nustatytas tos kameros bitas *c* (R = 0, G = 1, B = 2).
- Kiekvienos juostos įterptas kilmės šaltinis nurodo tos kameros kaukę (`unmix_clamp_mask`, atsižvelgiant į steko aplanką, arba `none`) ir bitą, kurį reikia nuskaityti (`unmix_clamp_mask_bit`).
- Kamera, kurios „unmix“ operacija nieko neapribojo, negauna jokio failo; monochromatinė arba RGB kamera neatlieka „unmix“ operacijos ir taip pat negauna jokio failo. CLI išspausdina kiekvieną kaukę po steku (`clamp mask SN…: …`); atsakyme jos išvardijamos kaip `clamp_masks`.
- To paties ciklo indeksinis TIFF / PNG failas apskaičiuojamas iš tos pačios steko, tame pačiame tinklelyje, todėl kaukės apibūdina ir jį. `--capture-level raw` nevykdo atskyrimo ir nerašo jokios kaukės.
- Stekas ir indeksas yra tiesiogiai `--output`, todėl kaukės patenka į `--output` priklausantį `clamp_masks/` — niekada virš jūsų pavadinto aplanko. Toje aplankėje yra sumaišyti skirtingi produkto tipai (stekas, indeksas, spalvotas atvaizdas): atrinkite jo failus, neperduokite viso aplanko fotogrametrijos programinei įrangai.

#### Nuolatiniai ryšiai („Smart-Prep“, GUI ekvivalentas)

Šios komandos išlaiko kameras atviras užkulisiniame pulke per visus CLI iškvietimus.

| Pakomanda | Paskirtis |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Pridėti vieną kamerą į rezervą (viena kamera, be masyvo). [Bendrosios parinktys](#common-options-shared-by-most-subcommands) `--exposure`, `--gain`, `--preset`, `--pixel-format`, `--width` / `--height` ir `--packet-size` (`standard` = 1500, `jumbo` = 9000 arba baitai; `auto` zondai) taikomi prisijungimo metu (`--exposure` arba `--gain` išjungia automatinį režimą). Ji grįžta, kai tik prasideda kameros srautas (laukiama apie 15 s, kol tai įvyks, tada 10 s, kol bus užpildytas pirmasis buferis) ir išveda `stream: streaming` arba `stream: no_frames -- connected, no frames yet: …`, jei srautas nieko neperdavė — jei kamera naudoja „jumbo“ paketus, kartu pateikiant patarimą vėl prisijungti su `--packet-size standard`. Kol viena kamera prisijungia (pirmasis prisijungimas atsisiunčia jos kalibravimą, trunka apie 70 s), kita komanda, kuriai reikalinga kamera, laukia jos ne ilgiau kaip 5 s, tada baigiasi nesėkme su HTTP 503 kodu „`CAMERA_POOL_BUSY`“, nurodant prisijungiančią kamerą; po akimirkos paleiskite ją dar kartą. „`lattice status`“ ir kamerų sąrašas niekada nelaukia: kamera, esanti prisijungimo procese, rodoma kaip prisijungianti, nurodant jos etapą. |
| `lattice cam-disconnect [--serial SN] [--all] [--keep-settings \| --clear-settings]` | Atleiskite. Kamera grąžinama į tą būseną, kokioje buvo rasta prisijungimo metu (sąlyginio iššaukimo jungtys, PTP, nustatymai); `--keep-settings` vietoj to išsaugo jos nustatymus savo atmintyje, o `--clear-settings` atkuria gamyklinius tos atminties nustatymus (žr. [Kameros atmintis](#camera-memory----keep-settings----clear-settings)). Kamera, kuri yra masyvo narys, atjungia visą masyvą. |
| `lattice cam-list` | Išvardyti kameros, esančias rezervo sąraše. |
| **`lattice array-connect`**|**Prijungti nuolatinį sinchronizuotą masyvą (REKOMENDUOJAMAS pradžios taškas).** Vykdo visą GUI „smart-prep“ eigą. Apie minutę 9 kameroms; iš karto po kamerų įjungimo tai gali užtrukti keletą minučių. Laukiant, išspausdinamas kiekvienas žingsnis, kurį pasiekia užkulisinė sistema, kaip `    <s> s: <step>` (pavyzdžiui, `    41 s: open SN:214000535 (4 of 9)`), o serverio žurnale kiekvienam žingsniui skiriama po vieną laiko eilutę (`[array-connect <id>] <step>: <s> s …`) ir paskutinė eilutė su lėčiausiais žingsniais. Jis laukia iki 600 s. Jei iki to laiko ryšys neužmegztas, serveris jį nutraukia: kiekviena jo atidaryta kamera grąžinama į tą būseną, kurioje buvo rasta, ir uždaroma, komanda išspausdina „`Error:`“ su pasiektu žingsniu ir užsidaro „`1`“, o masyvas nėra prijungtas (HTTP 504 „`CLIENT_DEADLINE_PASSED`“). „Ctrl+C“ atšaukia prisijungimą tuo pačiu būdu (`Interrupted: …`); jei masyvas jau buvo prisijungęs, eilutėje tai nurodoma ir pateikiamas `array-disconnect`, kuris jį atjungia. Komanda, nutraukta laukimo metu (`kill -9`), palieka prisijungimą neatšauktą, jei užpakalinė sistema negauna iš jos žinių 30 sekundžių. Masyvas, kuris veikia, bet dar neturi sinchronizuotos rėmelių grupės, vis tiek rodomas kaip prisijungęs, o jo srautas nurodomas kaip `no_frames`. |
| `lattice array-disconnect [--array-id ID] [--all] [--keep-settings \| --clear-settings]` | Atleisti masyvą. Kiekviena kamera paliekama veikti laisvai (`TriggerMode` `Off`), o visa kita išlieka tokia, kokia buvo nustatyta prisijungimo metu — jos trigerio ir sinchronizavimo linijos nustatymai (pagrindinio įrenginio`TriggerSource` grąžinami į buvusią padėtį, o sinchronizavimo linija – į įvesties režimą), PTP ir sesijos pakeisti kameros nustatymai – todėl vienos kameros komanda nariui veikia iš karto. `--keep-settings` išsaugo kiekvienos kamerosnustatymus, kokie jie buvo sesijos pabaigoje, į pačios kameros atmintį; `--clear-settings` atkuria gamyklinius tos atminties nustatymus. Žr. [Kameros atmintis](#camera-memory----keep-settings----clear-settings). |
| `lattice array-list` | Rodyti prijungtus masyvus. |
| `lattice array-status [--array-id ID]` | Tiesioginio vaizdo kadrų dažnis (fps), PTP, paskutinė klaida. `ptp_enabled` yra lygus `True` tik tada, kai kiekvienas mazgas nurodo, kad PTP įjungtas; `ptp_off:` nurodo mazgą, kuris to nedaro. `state:` eilutė rodo `restarting (applying settings)` per nustatymų perkrovimą, `recovering (…)`, kai vėl įjungiamas narys, kuris neteko maitinimo (žr. pastabą žemiau), `stalled (…)` – kai veikia duomenų surinkimo ciklas, bet nepaskelbia jokių kadrų grupių, ir `stopped (…)` – kai duomenų surinkimo ciklas nutraukiamas (iš naujo prijunkite masyvą). |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--orient] [--index\|--no-index] [--no-daq] [--daq-tolerance-ms MS] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Vienas sinchronizuotas įrašymas iš tiesioginio masyvo — Vienkartinis / Nuolatinis / Intervalinis / Greičiausias. **Numatytasis nustatymas – `all`**(vienas failas pagal atitinkamą eksporto tipą kiekvienai kamerai),**suderinta** taip, kaip ir programos įrašymas (`--no-aligned` vietiniams failams). Praleistos kameros (pvz., RGB, neįtrauktos į spinduliavimo / atspindžio duomenis) nurodomos kaip `Skipped: SN:<serial> (<reason>)`; DAQ, iš kurio buvo nuskaityti duomenys, išsaugomas kartu su įrašu, įtraukiamas tik neapdorotas įrašas, ir nurodomas kaip `DAQ: <path>` ([DAQ, iš kurio nuskaityti duomenys, išsaugo](#the-daq-reading-a-capture-saves)). Žr. [Įrašymo režimai, įrašymo įrenginiai ir neprisijungus atliekamas pakartotinis apdorojimas](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only] [--expr EXPR]` | Įrašyti tiesioginį sujungto indekso vaizdą į vaizdo įrašą/GIF (stebėjimo kokybės; kai GUI langas neuždarytas, užkulisinė programa atkuria masyvo NDVI, arba `--expr`). |
| `lattice array-burst [--duration S] [--max-frames N] [--orient] [--build] [--products …]` | Serijinis neapdorotų „Bayer“ duomenų fiksavimas dideliu kadrų dažniu (analizės kokybės; perdirbti neprisijungus). Kaip ir `array-record` bei `array-capture --continuous`, jis kiekvieno apklausos ciklo metu prisijungia prie užkulisio: nutraukiamas be sustabdymo (`kill -9`), serveris jį sustabdo praėjus 30 s po paskutinio prisijungimo (`stop_reason`, `client_gone`; serijos manifestas užbaigiamas ir pradedamas sujungto indekso kūrimas, kaip ir sustabdymo atveju), o masyvas lieka prijungtas. |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Iš naujo apdoroti išsaugotą neapdorotą seriją į kalibruotą vaizdo įrašą (-us). |

> **Kamera, kuri netenka maitinimo arba praranda ryšį, pati atsijungia.** Kai narys nustoja atsakyti (komutatoriaus maitinimo ciklas perkrauna kiekvieną jo maitinamą kamerą), masyvas jo laukia, vėl atidaro ryšį, kai jis vėl atsako, ir grąžina jai tai, ką nustatė `array-connect`: PTP, trigerio ir sinchronizavimo liniją, tinklo tempą, pikselių formatą ir kadro dydį bei automatinę ekspoziciją. Tada ji vėl transliuoja be naujų `array-connect` nustatymų. Tuo tarpu `array-status` rodo `state: recovering (...)`, o `last_error` nurodo, kurios kameros laukia; po to išspausdina `re-opened:`. Perkrauta kamera grįžta su išjungtu PTP, todėl kol ji vėl nebus atidaryta, „`ptp_enabled`“ yra „`False`“, o „`ptp_off:`“ ją pavadina.

> **Laikoma kamera niekada nėra atidaroma antrą kartą.** Antrasis atidarymas sustabdo laikėjo srautą ir perrašo jo nustatymus, o tai gali sukelti užkulisinės sistemos gedimą. Taigi, jei kamera yra laikoma baseine (GUI arba „`cam-connect`“), masyvo elemente arba jei kamerą laiko foninė užduotis (veikiantis „`lattice continuous`“, `multi-stream` arba `align-stream`, savo ar kito kliento), `lattice continuous`, `lattice capture`, `cam-connect`, `power --sleep`, `calibrate`, vienokadrų nuotrauka, o `white-balance` ir `presets --apply` atmetami su HTTP 409 kodu, nurodant savininką ir išleidimo komandą (`chloros-cli lattice cam-disconnect -s <SN>`, `chloros-cli lattice array-disconnect --array-id <ID>` arba, jei tai užduotis, jos identifikatorių ir kas ją pradėjo) bei išeina `1`. Taip pat elgiasi kamera, kurią šiuo metu naudoja kita komanda (`multi-test` arba `multi-capture`, atidarydama savo kameras, dar vienas vienkartinis veiksmas), kiekvienai čia esančiai komandai: klaida reiškia, kad reikia palaukti, kol ta komanda baigsis. `lattice power` be `--sleep` nuskaito per paties turėtojo identifikatorių, jei toks yra (rezervas, masyvas, užduoties atidaryta kamera), ir yra atmesta, jei kamerą naudoja kita komanda. Kiekvienas `CAMERA_HELD` atsakymas perduoda kodą tiek `code`, tiek `error_code`. Komandos, atidarančios visas kameras, pateiktos skyriuje [Daugiakameris (trumpalaikės sesijos)](#multi-camera-transient-sessions).

##### `array-connect` parinktys

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automatiškai aptikti visas „LATTICE“ kameras (reikia ≥2) | Pirmoji serijinė kamera yra pagrindinė (MASTER). Jei nenurodyta, aptikimas filtruojamas pagal „LATTICE“ (M3C / M3M) modelius ir prijungiamos visos jos. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinchronizavimo linija. |
| `--target-fps F` | autom. | Pagrindinio įrenginio trigerio aktyvacijos dažnis. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | autom. | Perrašyti lygio pasirinkimo nustatymus. |
| `--wire-ceiling-mbps MB_PER_S` | nustatoma automatiškai | **Pagrindinio kompiuterio nuolatinis laidinio ryšio pralaidumas, išreikštas MB/s — skaičius, nuo kurio priklauso visos matricos paskirstymas.** Sumažinkite jį, kai masyvas praneša apie GVSP sugadintus rėmus: automatinė vertė apskaičiuojama pagal tinklo plokštės nurodytą ryšio greitį, kuris pervertina USB adapterių, siaurų PCIe juostų ir užimtų bendrų tinklų pajėgumą. Ši vertė išsaugoma projekto masyvo fiksavimo bloke, todėl ją atkuria pakartotinis atidarymas, prisijungimas per CLI arba SDK. Žr. [Masyvo būklė](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | nėra (visas jutiklis) | Aparatinis binningas (visada „Vidurkis“). |
| `--width N` / `--height N` / `--pixel-format FMT` | visas jutiklis; formatas pagal kamerą | Aiškiai nustatytas kadro dydis arba pikselių formatas kiekvienai kamerai, vietoj viso jutiklio ir formato pasirinkimo pagal kamerą (Mono12 / BayerRG12). |
| `--no-recommend` | išjungta | Praleisti tinklo analizės etapą (tuomet kiekviena kamera išlaiko esamą dydį, o ne nustatoma pagal visą jutiklį). |
| `--no-ptp` | išjungta | Išjungti PTP (tuomet kamerų tarpusavio laiko žymos **nėra** palyginamos). |
| `--packed {auto,on,off}` | `auto` | Transliuoti 12 bitų kadrus suspaustus ir išpakuoti šiame kompiuteryje — daugiau kadrų per sekundę esant visam jutikliui, identiški duomenys. `auto` suspaudžia tik tada, kai tai naudinga ir šis kompiuteris gali tai sau leisti; `off` niekada nesupakuoja. Žr. [Supakuotas 12 bitų perdavimas](#packed-12-bit-transport---packed). |
| `--packet-size {auto,jumbo,standard,N}` | `auto` | GVSP paketo dydis kiekvienai masyvo kamerai. `auto` leidžia kiekvienai kamerai tikrinti tinklą; `jumbo` = 9000, `standard` = 1500 arba nurodytas dydis baitais jį fiksuoja. Iki versijos 1.3.0 `array-connect` priimdavo šį žymeklį, bet jį ignoruodavo. |

> **Pagal numatytuosius nustatymus naudojamas visas jutiklis.** Jei nenurodyta `--width` / `--height` / `--binning` /
> `--pixel-format`, `array-connect` nustato kiekvieną kamerą į visą jutiklį — be
> binningo, be ROI apkarpymo — ir leidžia kadrų dažniui sumažėti iki to, ką gali užtikrinti šis kompiuteris ir
> tinklas, o ne mažinti kadrų dydį, kad išlaikytų dažnį.
> Tinklo analizės eilutėje rodomas prognozuojamas dažnis, o kitoje eilutėje išvardytos
> greitesnės parinktys (pikselių sujungimas arba mažesnis ROI) su jų prognozuojamais dažniais; praleiskite
> `--binning` arba `--width`/`--height`, kad pasirinkite vieną iš jų. „Connect“ niekada savaime netaiko pikselių sujungimo ar apkarpymo
> .

##### Suspaustas 12 bitų perdavimas (`--packed`)

Veikiant visam jutikliui, matricos kadrų dažnis nustatomas pagal tai, kaip greitai kiekviena kamera gali siųsti
savo kadrus. 12 bitų formatai (`BayerRG12`, `Mono12`) paprastai perduodami kaip 16 bitų
žodžiai; **suspakuotas** perdavimas siunčia tuos pačius 12 bitų 1,5 baitų vienam pikseliui ir
išpakuoja juos šiame kompiuteryje, todėl kiekvieno kadro siuntimas užtrunka 25 % mažiau laiko —
maždaug **33 % daugiau kadrų per sekundę esant pilnam jutiklio veikimui** (išmatuota: 10,7 → 14,0 kadrų per sekundę
9 kamerų masyve). **Išsaugoti duomenys yra identiški**: tos pačios reikšmės, tie patys
16 bitų failai, tie patys metaduomenys.

- `auto` (numatyta) suspaudžia tik tada, kai tai naudinga **ir** kai šis kompiuteris gali
  atlikti išpakavimą. Prisijungus, serveris įvertina savo išpakuojimo greitį, procesorių skaičių,
  dabartinę procesoriaus apkrovą ir tinklo pralaidumą, todėl stacionarusis kompiuteris, turintis laisvų branduolių, suspaudžia
  naudodamas visą jutiklio pajėgumą, o užimtas arba mažos galios kompiuteris transliuoja neišpakuotus kadrus. Maži ROI ir sujungti kadrai sutaupo
  mažai.
- `off` niekada nesuspaudžia — naudokite jį, norėdami išjungti suspaudimą diagnozuojant arba kompiuteryje
  kompiuteryje, kuris turi visą savo procesoriaus galią skirti kitam darbui.
- `on` visada pakuoja 12 bitų formatus.

„connect“ išvestyje rodomas sprendimas ir jo priežastis („supakuota 12 bitų: įjungta (automatinis:
prognozuojama 11,0 -> 14,4 kadrų per sekundę, …)`); `/api/camera/array/status` praneša
`packed_transport` ir `packed_decision`. Norėdami nustatyti visam kompiuteriui galiojantį numatytąjį nustatymą
vietoj to, kad kiekvieną kartą perduotumėte žymę, nustatykite `CHLOROS_ARRAY_PACKED12=off` (arba
`on`) užkuro aplinkoje — tačiau aiškiai nurodytas `--packed on|off` vis tiek turi pirmenybę.
8 bitų ir 16 bitų formatai niekada nėra suspaudžiami.

### Vienas projekto įrašas, visi režimai

Projekto aplanke saugomi kiekvieno įrenginio nustatymai: `cameras.json` — kiekvienos kameros (`cameras[].settings`, skirta vienai kamerai ir kiekvienam masyvo nariui) bei kiekvieno masyvo bendrieji nustatymai (`arrays[].capture`: formatas, ROI, binningas, sužadinimo dažnis, laidų biudžetas) — ir `sensors.json`, kiekvieno šviesos jutiklio (integracijos laikas, kadrų vidurkis, riba). Programa, CLI, SDK ir MCP serveris visi skaito ir rašo tuos pačius failus tuo pačiu būdu:

- **Į projektą prijungtas įrenginys yra į jį įrašomas ir į jį įrašomi visi jo pakeitimai.** „Prie projekto“ reiškia, kad projektas atidarytas užkulisiuose (programėlėje) arba — iš SDK — per `ChlorosProject`, kuris nurodo savo aplanką. Kamera, matrica ar šviesos jutiklis, kurio projektas dar neturi, pridedamas prie jo failų, kai prisijungia (`lattice cam-connect`, `array-connect`, `daq pool-connect`, programėlė, SDK, `chloros-mcp`) kartu su jo nustatymais. Bet kokio režimo pakeitimas (`lattice exposure`, `gain`, `configure --set`, `resolution`, masyvonustatymai ir suaktyvinimo dažnis, šviesos jutiklio riba, programėlės valdymo elementai) po akimirkos išsaugomi to projekto failuose. Kameroje nustatyta ekspozicija, stiprinimas arba AE tikslas išsaugomi kaip užfiksuoti, todėl vėl atidarius projektą jie išlieka tokie patys.
- **Atidarius projektą, jo įrenginiai nustatomi pagal projektą** bet kuriame režime: kamera, matrica ar šviesos jutiklis, prijungti be savų nustatymų, gauna projekte išsaugotus nustatymus (`lattice cam-connect` / `array-connect`, kai projektas atidarytas; `chloros-cli project connect`, `open_project().connect_all()` – atidarius programėlę iš naujo). Matrica, prijungta su nauju identifikatoriumi, atnaujina projekto išsaugotą matricą su tomis pačiomis kameromis; matrica su kitomis kameromis pridedama šalia jos.
- **Programos vidinis išsaugojimas niekada nepakeičia įrenginio tiesioginių nustatymų** senesne jų kopija ir išsaugo kiekvieno nario perrašymus, kuriuos išsaugojo CLI arba SDK iškvietėjas.
- **Jūsų nustatytas pikselių formatas yra tas, kurį projektas išsaugo.** Formatas, nustatytas masyvo nustatymuose, naudojant `--pixel-format` arba `connect_array(pixel_format=)`, įskaitant 8 bitų formatą, yra išsaugomas ir atkuriamas tiksliai. Jei nieko nenustatyta, kiekviena kamera prisijungia naudodama savo 12 bitų numatytąjį nustatymą (`BayerRG12`, `Mono12`, jei tai monochromatinis jutiklis). Formato pavadinimas reiškia jo bitų gylį; šeima priklauso jutikliui, todėl spalvota kamera niekada nenustatoma į „Mono“ formatą, o monochromatinė kamera – į „Bayer“ formatą, ir eilutė `[PixelFormat <serial>] ... written` tai nurodo, kai formatas buvo susietas. 8bitų įrašo eksportai yra 8 bitų, įskaitant atspindžio duomenis: perskaluoti ir pažymėti jo `PixelScale`.
- Įrenginys, prijungtas be jokio projekto, nieko neįrašo.
- **Įrašas priklauso projektui, sukurtam po jo; atidarius projektą, įrašas lieka su juo.**Kai sukuriama projektas — SDK `create_project()`, `chloros-mcp` arba programėlės**Naujas projektas**— arba dubliuojamas (programėlės funkcija**„Duplikuoti“**, kuri atidaro kopiją), o įrašas vis dar rašomas į anksčiau atidarytą projektą, įrašymas tęsiasi, o įrašyta informacija patenka į naują projektą, toje pačioje jo vietoje (žemiau). Atidarius esamą projektą — `chloros-cli process`, `project devices --reconnect`, `ChlorosProject.process()`, programos funkcija**Atidaryti projektą** — įrašas toliau tęsiasi tame projekte, į kurį jis įrašomas: jis ten baigiasi, o jo failai prisijungia prie to projekto failų sąrašo (programa sustabdo kiekvieną įrašymą prieš atidarydama projektą).
  - `array-record` ir `array-burst`: vaizdo įrašas arba serijos aplankas su visais kadrais perkeliama ten visa, kai įrašymas sustabdomas (jo `--duration`, Ctrl+C). `Burst dir`, kurį ji išspausdina, yra naujas, o `--build` ir `array-build-video` veikia iš jo. Jei diskas atsisako perkelti, įrašas lieka ten, kur buvo įrašytas;žurnale nurodoma priežastis, taip pat ir sustabdymo HTTP-atsakyme (`relocation_error`). Sustabdyto įrašymo serijos neapdoroti kadrai ir `.daq` įtraukiami į to projekto failų sąrašą, kuriame jie atsiduria; vaizdo įrašas (`array-record`, iš serijos sukurtas vaizdo įrašas) nėra įtraukiamas į projekto failų sąrašą.
  - `array-capture --continuous`: kiekvienas įrašas, padarytas po pakeitimo, išsaugomas naujame projekte; anksčiau išsaugoti įrašai lieka ten, kur yra.
  - `daq pool-record` ir `project sensor log` (taip pat pačios programėlės šviesos jutiklio įrašas): `.daq`, kai įrašymas baigiasi, perkeliamas į naujo projekto`light_sensor` aplanką, kai įrašymas baigiasi, ir prisijungia prie to projekto failų sąrašo.

  Įrašas, esantis aplanke už atidaryto projekto ribų (`-o` kitur) lieka ten, kur yra, o „`lattice continuous`“ ir „`multi-stream`“ išlaiko jiems priskirtą aplanką.
- **Programoje įrašai, padaryti po naujo projekto sukūrimo, patenka į jį.** Sukūrus ar nukopijavus projektą, visi vykdomi įrašymo procesai ir įrašai tęsiasi, o kiekvienas po to atliktas įrašas („Įrašyti viską“, „Pradėti įrašyti“) išsaugomas naujame projekte ir įtraukiamas į jo failų sąrašą. Atidarius esamą projektą programoje, pirmiausia sustabdomi visi šie procesai.
- **Projektas neperkeliamas, kol vyksta įrašymas.**Atidaryto projekto perkėlimas į kitą darbo katalogą (programėlės**Nustatymai → Darbo katalogas**; CLI komanda projekto neperkelia, o tai daro HTTP klientas, siunčiantis duomenis į „`/api/move-project`“) atmetamas su HTTP 409 „`PROJECT_RECORDING`“ klaida, kol į jį rašo masyvo įrašymas ar serija, masyvo nepertraukiamas fiksavimas, vykdoma `lattice continuous` / `multi-stream` užduotis arba šviesos jutiklio įrašymas į jį rašo: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.` Programa atsisako taip pat, prieš paprašydama nurodyti aplanką, kol veikia jos pačios funkcijos „Užfiksuoti viską“, „Pradėti fiksuoti“ arba šviesos jutiklio įrašymas.

Tai nesusiję su tuo, kokios kameros nustatymų lieka po ryšio nutraukimo (žr. [Kameros atmintis](#camera-memory----keep-settings----clear-settings)): projektas pats nustato savo kameras, kai yra atidaromas.

### Kameros atmintis — `--keep-settings` / `--clear-settings`

Kai prijungiamas masyvas, jo kameros perima masyvo nustatymus, kuriuos vėliau galite keisti. Kai Chloros atjungia kameras — `array-disconnect`, tai yra laikinos komandos pabaiga (`multi-capture` be masyvo, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`), `lattice continuous` sustabdymas, kameros, kurios ji neturi, `lattice capture` pabaiga — ji kiekvieną grąžina į tą būseną, kokioje ji buvo rasta prieš prisijungimą: jos trigerio ir sinchronizavimo linijos laidus, PTP ir kameros nustatymus (pilnas sąrašas pateiktas skyriuje [Daugiakameris režimas (laikinos sesijos)](#multi-camera-transient-sessions)).

Norint naudoti kameras ne projekto ribose, dvi bet kurios iš šių komandų žymės įrašo į kameros **pačios atmintį** — tai, su kuo ji įsijungia, bet kurioje programinėje įrangoje:

- **`--keep-settings`** išsaugo kiekvienos kameros nustatymus, kokie jie buvo sesijos pabaigoje, į jos atmintį (UserSet2, nustatytas kaip paleidimo rinkinys). Kameros ir toliau veikia laisvai (`TriggerMode` `Off`), sinchronizavimo linijos laidai paliekami tokie, kokie yra. Kamera, neturinti „UserSet2“, išsaugo nustatymus į „UserSet1“; šis kompiuteris tai įsimena, todėl „Chloros“ masyvo savikontrolės funkcija — kuris iš naujo nustato „UserSet1“, nukrypusią nuo „Chloros“ bazinės linijos — jos nekeičia.
- **`--clear-settings`** kiekvienos kameros atmintį atkuria į gamyklinius nustatymus: „Chloros“ bazinę liniją, kurią jo masyvo savireguliacija įtvirtina — gamykliniai numatymai su „`TriggerMode`“, „`Off`“, „`TriggerSource`“, „`Line2`“, automatinė ekspozicija, išlaikytas kalibruotas RGB kameros baltos spalvos balansas — „UserSet1“, sudarantis paleidimo rinkinį.
- Šių dviejų negalima derinti. Kiekvienai kamerai skiriama eilutė: `SN:…: settings saved to the camera's memory (UserSet2); it powers up with them.` arba `SN:…: camera memory reset to the factory settings.`; atsakyme `camera_memory` kiekvienai kamerai nurodoma ta pati reikšmė.

Programa nesiūlo šių žymių.

**Kiekvienas išėjimo būdas grąžina kameras į pradinę būseną.**Tas pats grąžinimas į pradinę būseną vykdomas kiekvienu išėjimo keliu, ne tik vykdant aukščiau nurodytas komandas: `cam-disconnect` (ir programos kameros**X**); programėlės**X** ant masyvo arba bet kurio masyvo elemento eilutėje (masyvas negali veikti be elemento, todėl atleidžiamas visas masyvas); uždarant programėlę (ji suteikia užkulisiniam procesui iki 20 s, kad jis sugrąžintų kiekvieną kamerą, masyvą ir veikiančią komandą `lattice continuous`, prieš ją sustabdant; tuo metu vis dar vykdoma komanda — trumpalaikė komanda, atidarydama savo kameras arba laukdama jų PTP laikrodžių, vienkartinė komanda `lattice capture`, `power`, `white-balance` arba `presets --apply` — sustabdo veiklą artimiausiame saugiame taške, pirmiausia sugrąžina savo kameras, per ne daugiau kaip 8 s, ir atsako HTTP 503 `SHUTTING_DOWN`; vis dar veikiantis „array connect“ — `lattice array-connect`, programėlės „connect“ — sustoja prieš kitą kamerą ir uždaro atidarytas kameras, ne ilgiau kaip 10 s, ir atsako HTTP 503 `SHUTTING_DOWN`; o ta, kuri pasiekia sistemą išjungimo metu, nieko neatidaro: HTTP 503 `SHUTTING_DOWN`); ir CLI komanda, kuri nutraukiama arba kurios terminalas uždaromas (SIGTERM / SIGHUP sustabdo jos veikimą — ir sugrąžina kameras į pradinę būseną — prieš išeidama (`143` / `129`, kaip ir „Ctrl+C“). `lattice continuous`, `multi-stream` arba `align-stream`, nutrauktas iš karto (SIGKILL), kuris negali nieko sustabdyti, jo užduotis sustabdoma, o kameros grąžinamos į pradinę padėtį užkulisio sistemos 30 s po paskutinio patikrinimo. Backend, kuris užstrigo arba buvo priverstinai uždarytas, negali nieko atkurti; išjungus ir vėl įjungus maitinimą, kiekviena kamera grįžta į savo atmintį, o kitas Chloros prisijungimas perkonfigūruoja kamerą, kurią randa įjungtą.**Projektas pats nustato savo kameras.** Kai masyvas prisijungia prie atidaryto projekto, kiekvienas narys gauna nustatymus, kuriuos projektas jam išsaugojo — ekspoziciją, stiprinimą arba AE tikslą, priskirtą jam (laikomą kaip jūsų, todėl automatinė ekspozicija jo nekeičia) ir jo baltos spalvos balanso režimą („Chloros“ korekcija; pačios kameros baltos spalvos balansas išlieka išjungtas) — nepriklausomai nuo to, ką saugo kameros atmintis.

### „Smart-AE“ / „Smart-Capture“

„LATTICE“ masyvai, vos tik prisijungę, fone vykdo nuolatinę automatinę ekspoziciją (AE), tačiau naujai nukreiptam vaizdui suderinti reikia šiek tiek laiko. `array-capture --smart` yra **patogus sprendimas**: jis laukia, kol AE stabilizuosis visose masyvo kamerose, ir tik tada suaktyvina fotografavimą. Naudokite jį, kai sesijos metu keičiate sceną.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Pagal numatytuosius nustatymus stabilizavimo politika yra konservatyvi: 5 s laiko limitas, 1,5 s stabilumo langas, ±5 % ekspozicijos nuokrypio tolerancija. Jei norite, kad automatizavimas veiktų kitaip, nustatykite parametrus per SDK (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`).

#### Kaip veikia automatinė ekspozicija

- **Pirmiausia ekspozicija, paskui stiprinimas.** Kai stiprinimas nustatytas į „Auto“, jis išlieka 0 dB, kol ekspozicija pasiekia ilgiausią leidžiamą vertę.
- **Rankiniu būdu nustatant ekspoziciją, stiprinimas lieka „Auto“ režime**, kol nepakeisite stiprinimo arba nenustatysite jo į rankinį režimą. Grįžus į „Auto“ režimą, stiprinimo riba lieka tokia pati kaip anksčiau.
- **Maži šviesūs plotai nepatamsina vaizdo.** Atspindys, sudarantis iki 1 % kadro, ignoruojamas, o žinomų kalibravimo taikinių plokštėse – iki 0,5 % plokštės ploto.
- **DAQ ekspozicijos riba taikoma tik tada, kai matomas kalibravimo taikinys.** Ji apsaugo baltą plokštę nuo perkrovimo; jei plokštės nematyti, kamera eksponuoja pagal sceną.
- **Esant pastoviam apšvietimui ekspozicija išlieka stabili, o pasikeitus apšvietimui vaizdas stabilizuojasi per kelias sekundes.** Esant pastoviam apšvietimui ekspozicija ir jos riba išlieka nepakitę, o po didelio apšvietimo pokyčio vaizdas stabilizuojasi per kelias sekundes. Apkarpymas nebeapriboja ekspozicijos visam laikui, o pasikeitus domėjimosi sričiai (ROI) ekspozicija vėl nustatoma.
- **Ryškumo tikslas išlieka pasiekiamas.** Jis neauga, kol kamera yra pasiekusi ekspozicijos ribą ir jos jautrumas negali didėti, todėl sugrįžęs šviesos šaltinis nesukelia kadro peršvietimo.
- **Kamera, kurios automatinė ekspozicija nustoja reaguoti, išlaiko nustatymus, atitinkančius esamą apšvietimą.** Ryškioje saulėje kamera gali sumažinti ekspoziciją iki mažiausios vertės ir išlikti šioje padėtyje, rodydama tamsų kadrą, nors visi jos nustatymai rodytų normalius rodmenis. Chloros stebi kadrą, išlaiko kamerą tame apšvietimo lygyje, kurį ji išlaikė, ir atleidžia ją, kai apšvietimas pasikeičia. Tamsus arba uždengtas šviesos jutiklis to netrikdo.

### Įrašymo eksporto lygiai (numatytasis nustatymas `all`)

Nuo šios versijos, `lattice capture`, `lattice multi-capture` ir `lattice array-capture` **numatyta reikšmė yra `--processing all`** — po vieną išsaugotą failą kiekvienam eksporto tipui, taikomą kiekvienai kamerai, atitinkantį GUI funkciją „Capture All“ (Užfiksuoti viską). Lygiai yra tokie:

| Lygis | Išvestis | Taikoma |
| --- | --- | --- |
| `raw` | Vienkanalis „Bayer“ (mono kameros: viena juosta) tiesiai iš jutiklio. | Visos kameros. |
| `debayered` | 3 kanalų spalvotas vaizdas, išsaugotas **RGB**kanalų tvarka (TIFF su RGB fotometrine interpretacija; PNG/JPEG taip pat atidaromi kaip RGB): kalibravimo paketo tamsos poslinkis (DSNU) atimamas iš neapdorotų duomenų, tada atliekamas OpenCV kraštųatpažįstančio demozėjimo – be linijiškumo LUT, plokščio lauko ar atskyrimo (mono kameros: 1 kanalas, pilkosios skalės). Python SDK grąžinami atmintyje demozikuoti kadrai (`grab()`, `frame_stream()`) yra**BGR**, „OpenCV“ tvarka; tik išsaugotas failas yra RGB formatu. | Visos kameros. |
| `radiance` | float32 W/m²/sr/nm per visą radiometrinę grandinę. | Tik multispektrinės (M3C/M3M) — **praleidžiama RGB filtro kamerų**. |
| `reflectance` | uint16 ρ (`32768` = 1,0), suderinama su „Pix4D“. | Tik daugiaspektrinė, ir **tik tada, kai priskirtas DAQ + kamera kalibruota**; kitais atvejais praleidžiama. |
| `display` | Pilna GUI peržiūros grandinė (CCM + WB + gama pagal kameros profilį). Išsaugoma `preview/` aplanke. `lattice capture` taip pat priima pavadinimą `preview`; `array-capture` / `multi-capture` priima tik `display`. | Visos kameros. |

Nurodykite vieną lygį, kad būtų išsaugotas tik tas vienas (`--processing debayered`). Kai prašote `all`, lygiai, kurie netaikomi konkrečiai kamerai, yra praleidžiami (ir apie tai pranešama), o ne rodomos klaidos — neprijungta arba nekalibruota kamera vis tiek gauna `raw` / `debayered` / `preview`.

#### DAQ, skaitantis įrašą, išsaugo duomenis

Kiekvieną kartą, kai DAQ susiejamas su kamera ar matrica arba įtraukiamas į grupę, žemyn nukreiptas rodmuo, naudojamas įrašui, įrašomas į **`.daq`** failą, esantį „captures“ aplanko „`daq/`“ pakatalogyje, nurodant jo `.match.json` (kiek rodmuo skyrėsi nuo ekspozicijos) ir `.cameras.json` (kam priklauso rodmuo), ir pateikiamas `DAQ:` eilutėje. Tai galioja kiekvienam lygiui, įskaitant neapdorotus arba iš „bayes“ sistemos išlaisvintus įrašus, todėl bet kuris įrašas vėliau gali būti iš naujo apdorotas į spinduliavimą, atspindį arba indeksą. Tai tas pats failas su tuo pačiu pavadinimu ir papildomais failais, nepriklausomai nuo to, ar įrašas buvo gautas per GUI, CLI ar SDK. Jei DAQ rodmenų visai nėra, nėra ir `.daq`, o `multi-capture` be prijungto masyvo (laikinasis atsarginis variantas) nieko neišsaugo.

| Žymė | Poveikis |
| --- | --- |
| `--no-daq` | Neišsaugoti rodmenų užfiksavime, kurio lygiai jų nenaudoja (`raw`, `debayered`). `radiance`, `reflectance` arba `display` lygis vis tiek jį įrašo, nes pakartotiniam importavimui reikalingas rodmuo, kuriuo buvo pagamintas produktas. |
| `--daq-tolerance-ms MS` | Kiek rodmuo gali nukrypti nuo ekspozicijos ir vis tiek būti laikomas atitinkančiu leistiną nuokrypį (numatyta reikšmė – 500). Keičiasi tik vertinimas (`.match.json`, `in_tolerance` ir `tolerance_ms`, atspindžio koeficiento atveju – `DlsInTolerance`), o ne pats naudojamas rodmuo. Žr. [DAQ rodmuo, smarkiai nukrypęs nuo ekspozicijos](#a-daq-reading-far-from-the-exposure). |
| `--force-daq` | Senesnis būdas prašyti `.daq` duomenų, kai fiksuojama tik neapdorota medžiaga. Dabar tai yra numatytasis nustatymas, o `lattice capture` ir `array-capture` vis dar priima šį žymeklį (`multi-capture` – ne). `--no-daq` turi pirmenybę prieš jį. |

Koks DAQ: tas, kuris susietas su kamera ar matrica (GUI šviesos jutiklio pasirinkimas, projekte išsaugotas susiejimas arba `chloros-cli daq pool-bind`), arba naujausias, kurį turi užkulisinė sistema. Neapdorotų duomenų serija (`array-burst`) išlaiko savo `.daq` kiekvienam atskirai nuskaitymui serijos aplanke, kaip ir anksčiau.

**PNG ir JPEG formatai talpina 8 bitus vienam kanalui, todėl jie naudoja tik „`display`“ / „`preview`“ ir indeksų sandaugas.** „`--format png`“ arba „`jpg`“, kurių lygis viršija jų talpą, atmetami dar prieš užfiksuojant duomenis, nurodant lygį ir TIFF kaip formatą, kuris jį talpina:

- `radiance` (float32) ir `reflectance` (uint16, `32768` = 1,0) — visada, taigi `--processing all` (numatytoji reikšmė) su `--format png`/`jpg` taip pat atmetami: naudokite `--format tiff` arba `--levels display`.
- `raw` ir `debayered` iš kameros, transliuojančios daugiau nei 8 bitų (12 bitų M3C / Mono12 M3M kadrai) — CLI’as nemato kameros bitų gylio, todėl užkulisinė sistema juos atmeta (HTTP 400). Vienaskadrą turintis `lattice capture --processing debayered` užfiksuoja 8 bitų kadrą PNG / JPEG formatais ir nėra atmestas, taip pat nėra atmestas ir `multi-capture` be nuolatinio masyvo (8 bitų be „debayering“).

Kiekvienas įrašymo maršrutas atsako taip pat (`error_code` `FORMAT_CANNOT_HOLD_LEVEL`): `lattice capture`, `multi-capture`, `array-capture`, `project capture` / `burst` ir už jų esančius HTTP-maršrutus.

### Kaip atrodo įrašų aplankas

Kiekvienas eksporto tipas patenka į **savo pakatalogį**, esantį po „`-o`“, todėl daugiapakopiuose įrašuose tipai niekada nesimaišo. Paveikslėlyje pavaizduotas**numatytasis vienos kameros įrašymas `all` pagal `lattice capture`** — keli lygiai vienai kamerai, todėl kiekvienas failas turi priesagą `_<level>`:

```
output/
├── raw/           capture_<ts>_SN<serial>-<filter>_raw.tiff
├── debayered/     capture_<ts>_SN<serial>-<filter>_debayered.tiff
├── radiance/      capture_<ts>_SN<serial>-<filter>_radiance.tiff
├── reflectance/   capture_<ts>_SN<serial>-<filter>_reflectance.tiff
├── preview/       capture_<ts>_SN<serial>-<filter>_display.tiff
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
├── clamp_masks/   radiance/…_radiance_clampmask.tif, reflectance/…, index/… — the unmix clamp
│                  masks, one subfolder per product folder, only when a band clamped
└── daq/           daq_data_<sensor id>_<ts>.daq: the downwelling reading matched to the capture
                   (+ .daq.cameras.json: whose)
```

Kaip skiriasi failų pavadinimai:

- **Kameros laukas — serijos numeris, tada filtras:** kiekvieno įrašymo failo pavadinime nurodoma kamera, kuria jis buvo padarytas, kaip `SN<serial>-<filter>`, kur `<filter>` yra kameros modelio filtro dalis: `SN261902142-F450` atitinka `M3M-L87-F450`, „`SN213800234-FRGN`“ – „`M3C-L87-FRGN`“, „`SN214701292-FRGB`“ – RGB kamerai. Filtras sujungia serijos numerį su „`-`“, o ne su „`_`“, todėl pavadinime yra tie patys „`_`“ atskirti laukai kaip ir anksčiau (prefiksas, data, laikas, milisekundės, kamera, lygis). Kamera, kurios modelio negalima nuskaityti (ištrintas „DeviceUserID“), pavadinama tik „`SN<serial>`“. Ankstesnių versijų įrašai pavadinime neturi filtro; Chloros skaito abu.
- **Prefiksas:** `lattice capture` pavadina failus `capture_…`; `array-capture`, `multi-capture` su prijungtu masyvu, o `project capture` / `burst` / `stream`, o masyvo pavadinimas juos pavadina `sync_<ts>_SN<serial>-<filter>…`.
- **Lygio priesaga naudojama tik esant keliems lygiams.**Kamera, kuri viename įraše išsaugo**kelis**lygius (`all` arba `--levels a,b`), prideda `_<level>`. Kamera, kuri išsaugo**vieną** lygį, išlaiko paprastą pavadinimą be priesagos — „`--processing reflectance`“ įrašo „`reflectance/sync_<ts>_SN<serial>-<filter>.tiff`“ — todėl skriptai, parašyti vieno lygio įrašams, toliau veikia. Lygis vis dar nurodomas pagal aplanką.
- **Fiksavimo kaukės atitinka produkto pavadinimą:** `<product name>_clampmask.tif`. Vieno lygio atspindžio įrašas įrašo `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif`; `all` įrašas įrašo `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif`.
- **Plėtinys** eina po „`--format`“: numatytasis „`tiff`“ suteikia „`.tiff`“, o „`--format tif`“ suteikia „`.tif` (ribojimo kaukės visada yra `.tif`).
- **Viena išimtis:** `lattice capture --processing debayered` (arba `--levels debayered`) paima greitą vienąkadrų kelią ir įrašo `capture_<ts>_SN<serial>-<filter>.tiff` tiesiai į `-o` — be pakatalogio.
- **Srautai ir serijos** perneša tą patį lauką: `lattice continuous` įrašo `frame_<seq>_SN<serial>-<filter>.tiff`, `lattice multi-stream`, `stream_<seq>_SN<serial>-<filter>_<frame id>.tiff` ir neapdorotą seriją `<burst>_f<frame>_SN<serial>-<filter>_raw.tiff`.

`<ts>` yra įrašymo laiko žyma, o `<serial>` – kameros serijos numeris, taigi viena sinchronizuota grupė turi bendrą
laiko žymą visose kamerose. **Atkreipkite dėmesį į vieną asimetriją:** `display` lygis saugomas aplanke
pavadintame `preview/`, o daugialypio lygio failų pavadinimuose — aplankas ir priesaga skiriasi
tik tam lygiui. Nežinomi lygiai perkeliami į aplanką su savo pavadinimu, o jei pakatalogo
sukurti nepavyksta, failas įrašomas į išvesties šaknį, o ne prarandamas.

**„captures“ aplanko pakartotinis apdorojimas:**nukreipkite „`chloros-cli process`“ į**„captures“ šakninį katalogą**
(`output/`). „`process`“ paprastai importuoja tik jūsų nurodytą aplanką, tačiau jei tame aplanke nėra
nuotraukų, bet turi pakatalogius, jis automatiškai pereina į juos — taigi visų lygių pakatalogiai ir
pakatalogis „`daq/`“ yra įtraukiami vienu kartu. Kiekvienas „capture“ lygis importuojamas kaip viena nuotrauka, o
kiti lygiai pateikiami kaip režimai, o ne kaip po vieną nuotrauką kiekvienam lygiui.

Taip pat veikia tiesioginis **lygio pakatalogio** pavadinimo nurodymas (pvz., `output/raw/`). Tai padarius, `daq/`
lieka neįtrauktas, todėl, kai iš `raw/` gaunate radiometrinįiš `raw/` gaunate radiometrinį
produktą — kitaip laiko žymos atitikimas neturės su kuo susieti.

**Apdorojimas visada prasideda nuo `raw`.** Kiekvienoje užfiksuotoje serijoje neapdorotas kadras yra apdorojimo grandinės šaltinis;
`debayered`, `radiance`, `reflectance` ir `preview` yra peržiūros režimai, tačiau niekada nėra grąžinami
atgal į apdorojimo grandinę. Pakartotinai apdorojant išvestinį produktą, vėl būtų taikomi vinjetės, CCM ir
spinduliavimo skaičiavimai, kurie jau yra įtraukti į jo pikselius, todėl Chloros to atsisako, o ne
atlieka dvigubą apdorojimą. Dvi pasekmės, kurias verta žinoti:

- `index/` ir `composite/` atvaizdai **niekada** apdorojami. Tai yra išvesties duomenys, o ne užfiksuoti kadrai —
  NDVI LUT atvaizdas neturi jokios prasmingos spinduliavimo interpretacijos.
- „Captures“ aplankas, eksportuotas **be** `raw` (pvz., `array-capture --processing reflectance`), neturi
  neturi teisėto apdorojimo grandinės šaltinio. Šie įrašai importuojami ir rodomi įprastai, tačiau `process` juos praleidžia
  ir apie tai praneša:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Jei jums tikrai reikia perduoti išvestinį produktą — „hub“ sesiją, įrašytą su
  įjungtu „`demosaic`“ arba iš senosios versijos aplanko — „`--input-level {raw,debayered,processed}`“ priverčia naudoti įėjimo
  tašką ir panaikina praleidimą. Šis žymeklis yra sąmoningai numatytas išeities variantas; „`auto`“ (numatytoji reikšmė)
  niekada neapdoroja įrašo, kuriame nėra neapdorotų duomenų.

**Kalibravimo žymės: tik naudojant `raw`.** Įrašo failas „`raw`“ turi kameros kalibravimo paketą: „`CalibrationJSON`“ naudingąją apkrovą ir su „MicaSense“ suderinamas žymes (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), apibūdinančias jutiklio „raw“ reikšmes. Kiekvienas kitas lygio failas (`debayered`, `display`, `radiance`, `reflectance`, `index/` atvaizdavimas) bei kiekvienas `lattice continuous` / `multi-stream` kadras turi kameros identifikatorių (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) bei patį kadro įrašą, `XMP-mapir:FrameJSON` (ekspozicija, stiprinimas, temperatūra, pikselių formatas, užfiksavimo laikas, DAQ atitikimas), nes kiekvienas „`process`“ produktas turi savo identifikatorių. „`process`“ suranda tokio failo kalibravimą pagal jo serijinį numerį, o „`CalBundleSha256`“ – „Chloros“ kalibravimo talpykloje, atsisiųsdamas paketą vieną kartą, jei jo kompiuteryje jo nėra.

#### Iš kelių kamerų sukurti failai

Failas, sukurtas iš daugiau nei vienos kameros juostų — daugiajuostis sluoksnis (`align-apply`, `align-stream`, `index --live --save-multiband`), iš kelių kamerų sudarytas tiesioginis indeksas (`index.tif`, `index_LUT.png`), iš masyvo užfiksuotas vaizdas `composite/` — nurodo kiekvieną kamerą, kurios duomenys buvo panaudoti:

- **Sąrašai pagal kameras, išdėstyti pagal juostų tvarką** (sudėtinis vaizdas: foninis sluoksnis, tada priešplanis; indeksinis sluoksnis, kameros, iš kurių paimtos juostos): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (kalibravimo rinkinys, per kurį praėjo kiekvienos kameros juostos, arba — `align-apply` / `align-stream` rinkinys, kurio juostos nebuvo kalibruotos — kameros rinkinys, kurį Chloros išsaugojo talpykloje, tas, kurio pavadinimas nurodytas jos įrašų failuose), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — lygiagrečios sąrašai, kurių kiekvieno *n* elementas apibūdina tą pačią kamerą; `-`, kai kamera neturi jokios vertės (nėra kalibravimo paketo, nežinomas objektyvas). Kiekvienas fotoaparatas yra **vienas** elementas: jei jo pakete yra kelios reikšmės vienam laukui — pavyzdžiui, paketo halogeninių lempų sertifikatai — jos sujungiamos naudojant `;` (`F-1479-1;F-1480-1;F-1481-1`), niekada nenaudojant kablelio, todėl sąraše pateikiamas po vieną elementą kiekvienai kamerai, nepriklausomai nuo to, kaip jis išspausdintas.
- **EXIF `SerialNumber`** saugo tuos pačius serijinius numerius, sujungtus kableliais (`214000535,214000551`).
- Jame **nėra** atskirų kamerų identifikatorių (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): šie identifikatoriai nurodo vieną kamerą ir jos paketą, o `process` iš jų nustatytų failo kalibravimą. Indeksas, apskaičiuotas iš kelių kamerų juostų (`index.tif`, `index_LUT.png`) EXIF duomenyse nurodytas `Model` `LATTICE` (įrangos), o ne vienos kameros `LATT-…` modelio.**Vienos** kameros juostų failas — kiekvienos juostos TIFF failas (`--per-band`), `align-apply --per-camera` kadras, vienos kameros rinkinys arba tiesioginis indeksas — turi tos kameros identifikacinius žymes, kaip ir kiekvienas kitas užfiksuotas failas. `align-apply` steko, kiekvienos juostos ir kiekvienos kameros failai, taip pat kiekvienas `align-stream` stekas ir jo kiekvienos juostos failai turi savo grupės užfiksavimo laiką (etaloninės kameros ekspoziciją, UTC), o vienos kameros `--per-camera` kadras yra viena plokštuma, kaip ir `--bit-depth` stekas.**Kameros orientacija (apversti / pasukti).** Kameros orientacijos nustatymas (GUI parinktis „Mirror H /
„Mirror V“ / pasukimas) lemia, kaip įrašomas užfiksuotas vaizdas:

- **`raw` ir `debayered` įrašomi tiksliai taip, kaip juos nuskaitė jutiklis**, nepriklausomai nuo nustatymų.
  Pasuktas ar apverstas „Bayer“ mozaikos filtras turėtų kitokį spalvų filtro modelį, o kalibravimo
  žemėlapiai („dark offset“, „flat field“) yra paties jutiklio tinklelyje.
- **`preview`/`display`, `radiance`, `reflectance` ir `index/` atvaizdai yra orientuoti** — pasukti
  vieną kartą, atlikus visus kalibravimo skaičiavimus, todėl jie atidaromi taip, kaip buvo kadruojama kamera. Jų
  `clamp_masks/…_clampmask.tif` failai yra tame pačiame tinklelyje.
- **Kiekviename užfiksuotame faile nustatymas įrašomas** XMP formatu: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, pagal laikrodžio rodyklę, po
  apvertimų) ir `ChlorosOrientationApplied` — `false` neapdorotuose ir be „bayeringo“ (natūralūs pikseliai),
  `true` orientuotuose vaizduose. `ChlorosProcessing` išvardija operacijas, taikytas to failo
  pikseliams (`none`, kai vaizdas natūralus). Tai apima kiekvieną kadrą, kurį perduoda `lattice continuous` (
  pačios kameros nustatymas, netaikytas) ir kiekvieną `lattice multi-stream` kadrą (tapatybė: jo
  sudėtiniai kadrai yra natūralūs, kaip ir masyvo užfiksavimo atveju). `lattice align-stream` / `align-apply`
  rinkiniai įrašo pagrindinės kameros nustatymus, netaikytus: jų juostos yra iškreiptos į pagrindinio kadro
  plokštumą, bet nepasuktos.
- **`process` taiko užfiksuotą orientaciją**, kai apdoroja neapdorotą duomenų rinkinį: pirmiausia tamsos poslinkį, plokščio
  lauko korekciją, demozikavimą ir atskyrimą jutiklio koordinatėse, po to – masyvo suderinimo iškraipymą, o
  orientacija — taigi peržiūros, spinduliavimo, atspindžio ir indekso produktai gaunami lygiai tokie patys kaip
  gyvojo vaizdo, ir įrašomi `ChlorosOrientationApplied=true`. Nekalibruoti atsarginiai
  produktai (`Sensor_Response_Images`, `Vignette_Corrected_Images`) orientuojami taip pat, po
  plokščio lauko. Produktas be „Bayes“ filtravimo išlieka nepakeistas.
- **Chloros vaizdų peržiūros programa rodo neapdorotą arba be „Bayes“ filtravimo failą, pasuktą pagal jo įrašą** (tik ekrane —
  failas nėra keičiamas), todėl jis atrodo kaip jo produktai; miniatiūra, pilno dydžio vaizdas,
  kursoriaus rodmenys ir „Index/LUT“ smėlio dėžė – visur naudojamas tas pats pasukimas. Failas be įrašo ir
  produktas (jau orientuotas) rodomi taip, kaip buvo išsaugoti. Eksportuojant į „sandbox“ įrašoma orientacija, kurią
  turi jo pikseliai (`ChlorosOrientationApplied=true`; sluoksnio be įrašo identifikatorius).
- **Užfiksuoti vaizdai be įrašo apdorojami kaip originalūs** (nepasikeitęs elgesys). Iki versijos 1.3.0
  vienos kameros užfiksuotas vaizdas su nustatytu apvertimu ar pasukimu pats apversdavo savo neapdorotus ir debayeringo procesą praėjusius failus;
  Chloros negali atskirti tokio jau-pasuktą neapdorotą failą nuo natūralaus ir nespėja to atspėti, todėl
  apdoroja jį taip, kaip jis buvo išsaugotas. Kalibravimo žemėlapiai yra pačio jutiklio tinklelyje, todėl tokiam
  įrašui, kai svarbi jo kalibracija, naudokite įrašą, padarytą su 1.3.0 versija.
- **Masyvai pagal numatytuosius nustatymus neapverčia nė vieno nario failo** (suderinto vaizdo iškraipymas ir `Alignment*` XMP yra
  pačių kamerų, neapverstuose kadruose) ir įrašo tapatybę. `array-capture --orient` apverčia kiekvieno
  nario produktus po suderinimo deformacijos: pagal **pagrindinio** orientaciją, kai jie buvo
  deformuoti į jo kadrą (kad nariai liktų suderinti), kitais atvejais – pagal jų pačių kamerų orientaciją. Kiekviename
  failo įrašoma kadro, kuriame yra jo paties pikseliai, orientacija — neapdorotas vaizdas, atitinkantis jo pačios kameros orientaciją —
  o neapdorotas vaizdas, kuriame įrašyta suderinimo transformacija, taip pat įrašo `ChlorosAlignedFlipHorizontal` /
  `ChlorosAlignedFlipVertical` / `ChlorosAlignedRotation`: pagrindinio kadro, jei produktas iškraipytas
  į pagrindinio kadro formą. `process` pasuka kiekvieną produktą pagal tą, kuris priklauso jo pikseliams:
  suderintąjį, kai iškraipo „raw“ failą (`--array-alignment`, numatytasis — tiesioginio suderinimo
  užfiksavimo produktai), o kai ne – pagal pačios kameros (`--no-array-alignment` — tiesioginio
  nesuderinimo užfiksavimo produktai). Ta pati parinktis galioja ir `array-burst`, `project capture` / `burst` /
  `stream`, receptams ir SDK.

### Praleisti įrašai mišrių filtrų masyvuose

Kai viename masyve sumaišote RGB ir multispektrines kameras, `array-capture --processing radiance` (arba `reflectance`) išsaugo multispektrinius kadrus ir **praleidžia** RGB-kameros kadrus — spinduliavimas pagal „Bayer“ metodą nėra reikšmingas plačiajuosčio dažnio jutikliui. CLI aiškiai išspausdina kiekvieną išsaugotą failą (kartu su jo eksporto lygiu), kiekvieną įrašytą „`.daq`“ ir kiekvieną praleidimą, todėl failų skaičius nėra netikėtas:

```
  Saved: …/output/reflectance/sync_…_SN213800234-FRGN.tiff [reflectance] (SN:213800234, fid:1)
  Saved: …/output/reflectance/sync_…_SN214000533-FOCN.tiff [reflectance] (SN:214000533, fid:1)
  Saved: …/output/reflectance/sync_…_SN214701288-FNGB.tiff [reflectance] (SN:214701288, fid:1)
  DAQ:   …/output/daq/daq_data_daq-e-54b5e0_….daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

Praleidimo priežasties žymos atitinka šį šabloną: `<level>-not-applicable-to-rgb-cam`. Kamera, kurios kalibravimo paketas nėra įkeltas, praleidžia `radiance` / `reflectance` su `<level>-needs-calibration (connect the camera so its cal pack loads)`, o jei nėra tinkamų DAQ atspindžio rodmenų, praleidžia su `reflectance-skipped-no-dls`. Atspindys taip pat gali būti praleistas su `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, taip pat su „`dls-uncalibrated-band-<nm>`“, kai juosta daugiausia yra už DAQ šviesos jutiklio radiometriškai kalibruoto diapazono ribų (~374–974 nm) — iš siunčiamų SKU tik „F988“, kurio palaikomas darbo srautas yra atspindžio skydelio.

Naudokite „`--processing debayered`“ (arba „`display`“), kad būtų įtrauktos visos kameros, nepriklausomai nuo filtro tipo, arba numatytąjį „`all`“, kad vienu kartu gautumėte visus taikytinus lygius kiekvienai kamerai.

### DAQ rodmuo, nutolęs nuo ekspozicijos

Kiekvienas `.daq`, kurį išsaugo įrašymas, yra DAQ rodmuo, atitinkantis tą ekspoziciją, o šalia esantis `.match.json` fiksuoja, kiek toli vienas nuo kito jie buvo (`gap_ms`) ir ar tai neviršija leistinos nuokrypos ribos (`in_tolerance`, 500 ms, jei fiksavimo nustatymuose nenurodyta kitaip – `--daq-tolerance-ms`). Rodmuo, kuris neatitinka šio nuokrypio, vis tiek naudojamas, todėl nė vienas kadras neprarandamas, ir jis pažymimas: atspindžio failo XMP duomenyse nurodomi `DlsInTolerance=False` ir `DlsMatchGapS`. `lattice capture`, `multi-capture` ir `array-capture` taip pat išspausdina šią informaciją po atitinkamu `.daq`, o DAQ'srauto gedimą, kai tai paaiškinama:

**Kaip matavimo reikšmė susiejama su ekspozicija.** Fiksavimas laukia pirmojo DAQ matavimo po ekspozicijos, kad galėtų sujungti abiejų pusių matavimus (`method` `interpolated`). DAQ-E siunčia rodmenį kas 0,4 s dienos šviesoje ir kas 1–2 s patalpoje, todėl laukimas pridedamas prie komandos, o ne prie ekspozicijos, ir sudaro maždaug pusantro tokio intervalo. DAQ, kuris nustojo siųsti duomenis, nelaukiamas. DAQ-E rodmuo yra šviesos vidurkis per intervalą nuo ankstesnio rodmens, todėl jis įrašomas to intervalo viduryje: `gap_ms` matuojamas nuo ekspozicijos iki to vidurio, o `bracket_stamps_ns` nurodo pačių rodmenų laiko žymes. `array-capture --continuous` laukia nesulėtindamas fiksavimo: jo neapdoroti failai įrašomi iš karto, o jų `.daq` – kai tik gaunamas rodmuo. Naudojant „`--interval`“, kiekvienas ciklas laukia kaip atskiras fiksavimas.

```
  DAQ:   …/output/daq/daq_data_daq-e-54b774_….daq
         DAQ reading 114.9 s from the exposure (tolerance 500 ms): reflectance uses it, labelled DlsInTolerance=False
         DAQ stream fault: no frame accepted from daq-e-54b774.local in 25 s, after 1932 good frame(s). …
```

Jei iš tų duomenų nebuvo įrašytas joks atspindžio failas, pirmoji eilutė baigiasi „`saved anyway; its .match.json says in_tolerance: false`“.

Užfiksavimas vis tiek baigiasi `0`: kiekvienas failas buvo įrašytas ir nurodo, ką jame yra. DAQ, kurio srautas sustojo, toliau teikia paskutinį rodmenį, todėl prieš pasikliaujant to užfiksavimo atspindžio duomenimis, patikrinkite jį naudodami `chloros-cli daq pool-list`. Backend vėl prijungia sujungtą DAQ-E, kuris nustojo teikti duomenis (žr. [DAQ-E, kuris nustoja teikti duomenis](#a-daq-e-that-stops-delivering)). Rodmuo, neatitinkantis leistinos nuokrypos, išspausdina tik savo eilutę `DAQ:`, nebent tai būtų išsaugotas rodmuo (žr. toliau).

**Matavimo vertė, išsaugota po to, kai DAQ nustojo veikti.** Kai nė vienas DAQ negali pateikti matavimo vertės ekspozicijai (priskirtas DAQ yra nepasiekiamas arba nė vienas sujungtasis DAQ neturi tokios vertės, pvz., po `daq pool-disconnect`), fiksavimas pakartotinai naudoja paskutinį Chloros turėtą rodmenį, o jo „`.match.json`“ apibūdina tą rodmenį šiai ekspozicijai: „`method`“, „`retained`“, „`gap_ms` iš šios ekspozicijos į mėginius, iš kurių buvo gautas rodmuo, `in_tolerance` iš to tarpo ir `retained_from_epoch_ns` – ekspoziciją, su kuria rodmuo buvo suderintas pirmiausia. Todėl užfiksavimas, atliktas praėjus ilgam laikui po to, kai DAQ nustojo veikti, išspausdina aukščiau nurodytas eilutes, o po dar viena, kuri išspausdinama net tada, kai rodmuo vis dar yra leistinų nuokrypių ribose:

```
         This is the DAQ's last reading: the DAQ is no longer reporting, so each capture reuses it until a new one arrives
```

Joje „`.daq`“ vis dar nurodo DAQ, kuris užfiksavo rodmenį, kartu su kalibravimu ir dangteliu, kuriais buvo atliktas matavimas, todėl pakartotinai importuojant žinoma, kuris DAQ, kalibravimas ir dangtelis jį sukūrė.

### Failai, kurių nepavyksta įrašyti

Kamera, kurios failo neįmanoma įrašyti (dėl užpildyto disko ar teisių klaidos), netrukdo išsaugoti kitų kamerų failų. Kiekviena fiksavimo komanda (`lattice capture`, `multi-capture`, `array-capture`; `project capture`, `burst`, `stream`; ir recepto `capture`, `burst` bei `stream`) išveda kiekvieną failą, kurio nepavyko išsaugoti, į „stderr“, tada **užsidaro `1`**, nors likusieji buvo išsaugoti:

```
  Failed: SN:214701292 [reflectance] OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

Aptariami visi failai, kuriuos turi užfiksuotas vaizdas, įskaitant vienintelįkadrą, iš kurio pašalintas „debayeringas“ (`lattice capture --levels debayered`). `.daq` įrašas, kurio kilmės „sidecar“ failo (`.match.json`) nepavyko įrašyti, yra nurodytas kaip `[match]` (pats `.daq` buvo išsaugotas).

Kai diskas užpildytas, pasikartojantys įrašai (`array-capture --continuous` / `--interval`, `project burst` / `stream`, receptas `burst` / `stream`) **sustoja tame etape**, o ne rašo nieko į tą patį diską likusiam vykdymo laikui. Apie bet kokią kitą klaidą pranešama kiekviename etape, kuriame ji įvyksta, o vykdymas tęsiasi iki galo, tada uždaromas `1`. `array-capture --continuous` (nuolatinis užpakalinės dalies duomenų surinkimas) nepateikia jokių failų sąrašo, įskaitant ir nesėkmingus: juos suskaičiuoja savo pažangos ir apibendrinimo eilutėse (`N failed`, `N file(s) failed to write.`) ir užbaigia veikimą `1` — žr. [Nuolatinis režimas](#continuous-mode---continuous).

Įrašymo programos — `array-burst` ir `array-record` — praneša apie savo pačių nesėkmes. `array-burst` pats sustabdo savo veiklą, kai diskas užsipildo arba prieš pat tai. Abu įrašymo įrenginiai toliau veikia nepaisydami kitų įrašymo nesėkmių. Kai kuris nors iš jų praneša apie klaidą, ji išspausdinama po skaitiklių, o komanda **išeina iš `1`**:

```
  Recorder error: disk-full: OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

`Disk low:` vietoj `Disk full:` reiškia, kad įrašymo serija buvo sustabdyta dar neužpildžius diską. Prieš sustabdymą įrašyti kadrai išsaugomi. `array-burst --build` praleidžia kompiliaciją, kai baigiasi vieta, nes vaizdo įrašai būtų įrašomi į tą patį diską. Pirmiausia atlaisvinkite vietos, tada paleiskite `array-build-video --burst-dir …`. Po bet kurios kitos klaidos kompiliacija vis tiek tęsiasi.

`array-record`, kuris neįrašė nė vieno kadro, taip pat laikomas nesėkmingu įrašymu (`Recorder error: no-frames: the combined view published no frame to record …`, išeikite iš `1`) — žr. [`array-record`](#array-record--combined-index-videogif-monitoring-grade).

---

## Įrašymo režimai, įrašymo įrenginiai ir neprisijungus atliekamas perdirbimas

Visi jie veikia **nuolatinėje matricoje** (pirmiausia paleiskite `array-connect`). Jie atspindi GUI įrašymo skydelį.

### `array-capture` režimai

`array-capture` yra viena komanda su keturiais užrakto režimais ir eksporto perjungimo parinktimis:

| Režimas | Žymė | Veikimas |
| --- | --- | --- |
| **Vienkartinis** *(numatyta)* | (nėra) | Viena sinchronizuota fiksavimo grupė, po to užbaigiama. |
| **Nuolatinis** | `--continuous` | Fiksavimai vienas po kito, kol bus užfiksuoti `Ctrl+C`, `--count N`, `--duration S` arba kol bus užpildytas visas diskas. Vykdo pačios užkulisinės programos nepertraukiamą fiksavimą (GUI „Nepertraukiamo režimo“ funkcija) — žr. [Nepertraukiamą režimą](#continuous-mode---continuous) žemiau. |
| **Intervalas** | `--interval S` | Vienas ciklas kas `S` sekundžių (skaičiuojant nuo kiekvieno ciklo pradžios), tos pačios ribos. |
| **Greičiausias** | `--fastest` | Tik neapdoroti duomenys+ priskirtas DAQ rodmuo + sudėtinis indeksas; praleidžia spinduliavimo/atspindžio/ekrano skaičiavimus, kad kadras būtų pateiktas greitai. Reiškia `--processing raw --force-daq`. Vėliau iš naujo apdorokite išsaugotą `.daq` į kalibruotus produktus. |

Eksporto perjungikliai (derinami su bet kuriuo režimu; visi naudoja tą patį GUI/SDK galinį tašką):

| Žymė | Poveikis |
| --- | --- |
| `--array-id ID` | Masyvas, iš kurio reikia įrašyti duomenis (neįrašykite, jei prijungtas tik vienas). |
| `-o, --output DIR` | Išvesties aplankas (numatyta reikšmė – `output`, tame pačiame aplanke, iš kurio paleidžiama komanda). |
| `--format {tiff,tif,png,jpg,jpeg}` | Failo formatas (numatyta reikšmė – `tiff`). PNG / JPEG formatai naudoja tik `display` ir indeksinį atvaizdavimą; žr. [Užfiksavimo eksporto lygiai](#capture-export-levels-the-all-default). |
| `--processing LEVEL` | Vienas eksporto lygis (`raw`, `debayered`, `radiance`, `reflectance`, `display`) arba `all` (numatyta reikšmė). |
| `--levels L1,L2,…` | Konkretus eksporto tipų pogrupis (pvz., `raw,radiance,reflectance`); **pakeičia `--processing`**. |
| `--aligned` / `--no-aligned` | **Numatytasis: suderintas, kaip ir Chloros programėlės to paties masyvo fiksavimas**(jos nustatymas „**Suderintas**“ kiekvienam masyvui, įjungtas, jei jo neišjungiate). Kiekvieno nario ne „raw“ eksportas (`debayered`, `display`, `radiance`, `reflectance`, indekso atvaizdavimas) yra transformuojamas į pagrindinės kameros kadrą ir apkarpomas iki narių bendros persidengimo srities, todėl failai yra suderinti pikselių lygiu ir gaunami su tokiu pačiu dydžiu, apkarpymu ir „`Alignment*`“ žymėmis, kaip ir programos – mažesni nei jutiklis (arba ROI) dėl narių poslinkių; esant ROI, transformacija perorientuojama į langą. `XMP-mapir:AlignmentJSON` nurodo `"applied": true` ir pateikia `crop_roi`. Neapdoroti duomenys išlieka neiškreipti natūralaus dydžio, tačiau transformacija yra įrašyta metaduomenyse (`"applied": false`). `--no-aligned` išsaugo kiekvieną failą natūralaus dydžio, neiškreiptą. `--aligned` yra numatytasis variantas. Nepriklausomai nuo to, ar iškraipymas taikomas, ar ne, kiekvienas nario failas įrašo savo kameros `Alignment*` transformaciją, kai masyvas turi suderinimo profilį — išskyrus kamerą, kurios profilis nesuderinamas (išskirta arba priverstinai nustatyta kaip tapatybė dėl nesėkmingo sprendimo), ir etaloninę kamerą, kai nesuderinama jokia kita kamera: šios neturi „`Alignment*`“ žymių. Jei masyve nėra profilio, įrašas išsaugomas nesuderintas, o CLI išspausdina priežastį eilutėje „`Alignment:` eilutėje, kaip ir visais kitais atvejais, kai deformacijos negalima pritaikyti (elementai skirtinga raiška, masyvas nesinchronizuotas kabeliu, kadras, kuriam negalima pritaikyti profilio). Masyvo automatinis suderinimas sukuria profilį, kai ekspozicija stabilizuojasi; `align-calibrate` sukuria jį pagal užklausą. |
| `--orient` | Paversti kiekvieno elemento **produktus**— `display`, `radiance`, `reflectance`, `index/` atvaizdą ir `composite/` — pagal kameros orientaciją po suderinimo deformacijos: produktas, deformuotas į pagrindinio kadro orientaciją (numatyta) pagal**pagrindinio**orientacijos, todėl elementai išlieka suderinti; nesuderintas vaizdas (`--no-aligned` arba vaizdas, kuriam nebuvo galima pritaikyti deformacijos) – pagal savo kameros orientaciją.**Numatytasis nustatymas: išjungta – kiekvienas elemento failas išlieka natūralus**, kaip visada būna su masyvo vaizdais. `raw` ir `debayered` bet kuriuo atveju išlieka originalūs. Kiekviename faile įrašoma produktų orientacija (žr. [Kameros orientacija](#kaip-atrodo-įrašų-aplankas)); `Alignment*` XMP išlieka natūralaus kadro transformacija, pritaikyta prieš orientaciją, taigi `process` (deformacija, tada orientacija) atkuria tiesioginį vaizdą. |
| `--index` / `--no-index` | Išsaugoti / praleisti kiekvienai kamerai nustatytą augmenijos indekso perdangą, jei ji yra sukonfigūruota. Numatytasis nustatymas: atvaizduoti. |
| `--no-daq` | Praleisti `.daq` „sidecar“ failą, kai nė viename pasirinktame lygyje nenaudojamas DAQ (tik neapdoroti duomenys). Pagal numatytąjį nustatymą rodmenys išsaugomi, todėl kadrus galima pakartotinai apdoroti į atspindžio koeficientą / indeksą neprisijungus. `--force-daq` vis dar priimamas (numatyta reikšmė). |
| `--daq-tolerance-ms MS` | DAQ atitikimo tolerancija šiems įrašams (numatyta 500 ms). |
| `--smart` | Prieš suaktyvinant laukite, kol AE stabilizuosis visose kamerose (žr. [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | TIFF pikselių suspaudimas. `none` (numatyta nuo 1.3.0 versijos; 1.2.0 versijoje buvo nurodyta `deflate`) = nesuspausti — jutiklio pikseliai tiksliai tokie, kokie nuskaityti, ~6,3 MB vienam pilnos raiškos kadrui, ir greičiausias nuolatinis įrašymo greitis; `deflate` = be nuostolių zlib L1 + horizontalusis prognozuotojas, ~4,1 MB vienam kadrui, lėtesnis. Abu variantai yra be nuostolių ir importuojant atkuriami identiškai. |

> **Vienkartinio įrašymo TIFFas + pastovaus perdavimo greičio modelis.**Kiekvienas failas įrašomas**vieną kartą**: pikseliai, XMP paketas, IFD0 „Gamintojas/Modelis/Fotoaparato serijos numeris“ ir EXIF `ExposureTime` / `SerialNumber` žymės įrašomi per vieną ciklą, o vėliau niekas jų neperrašo. `--continuous`X fiksuoja paeiliui sinchronizuotas grupes — kiekvienas įrašymas perima grupę po ankstesnės, įskaitant tą, kuri buvo paskelbta dar tuo metu, kai tas įrašymas buvo išsaugomas — taigi jis veikia pagal masyvo greitį. DEFLATE užima Python GIL, todėl suspaustų duomenų įrašymas**nėra**lygiagretus tarp atskirų kamerų įrašymo sriegių; nuolatinis įrašymas pilna raiška jutiklio dažniu reikalauja nesuspaustų įrašymų (numatyta reikšmė)**ir** NVMe klasės diskas (~600 MB/s nuolatinis greitis 9 kamerų masyvui esant ~10 kadrų per sekundę). Tas pats parametras pateikiamas kaip `compression` ir `POST /api/camera/array/capture`.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (aligned is the default; drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --no-index -o out/

# Each member at its native size, unwarped
chloros-cli lattice array-capture --processing reflectance --no-aligned -o native/

# Continuous raw capture for 60 s, as fast as the array publishes
chloros-cli lattice array-capture --continuous --duration 60 --fastest -o flightline/
```

#### Nuolatinis režimas (`--continuous`)

`array-capture --continuous` (be `--interval` arba `--smart`) perduoda vykdymą **pačiam užpakalinio lygmens nuolatiniam įrašymui** — sesijai, kurioje veikia GUI nuolatinio režimo funkcija. Pagrindinė sistema pati išsaugo kiekvieną sinchronizuotą grupę, vykdydama du fiksavimus vienu metu ir negaudama jokių užklausų iš CLI tarp grupių, todėl ji spėja su greitu masyvu, kuriame užklausa vienam fiksavimui atsilieka (išmatuota 73–77 fiksavimai per sekundę 9 kamerų masyve su 512 pikselių ROI). Kiekvienas aukščiau nurodytas eksporto perjungimas taikomas kiekvienam įrašui.

- **Ribos:** `--count N` sustos po N įrašų (vienas įrašas = viena sinchronizuota grupė: kiekvieno nario failai); `--duration S` – po S sekundžių. Jei nenustatyta nė viena iš šių sąlygų, programa veikia tol, kol pasiekiamas `Ctrl+C`, užpildomas diskas, įvyksta klaida arba masyvas išjungiamas. `Ctrl+C` sustabdo užkulisinės sistemos sesiją, tada išspausdina santrauką.
- **Pažanga** kas 2 s, pridedant `, N failed` ir `, N groups skipped`, kai vertė nėra nulinė (praleista grupė yra ta, kurią masyvas paskelbė, bet kurios įrašymas nebuvo atliktas):

  ```
     12.0s  880 captures (73.3/s), 7920 files
  ```

- **Nėra sąrašo pagal atskirus failus.** Vykdymas baigiasi viena apibendrinančia eilute — užfiksuoti duomenys, greitis, failai ir išvesties aplankas — po kurios, jei reikšmė nėra nulinė, pridedami `N file(s) failed to write.` / `N group(s) skipped.`:

  ```
  Done — 880 captures (73.3/s), 7920 files in /home/user/flightline.
  ```

- **Sustoja**, kai užpildomas diskas (`Continuous capture stopped: the drive is full.`), arba įvykus klaidai ar masyvui išnykus (`Continuous capture stopped: <reason>.`) (abu atvejai rodomi stderr).
- **Išeitimo būsena:** `0`, kai išsaugoti įrašai ir visi failai buvo įrašyti, įskaitant `Ctrl+C`; `1`, jei nepavyko įrašyti bet kurio failo, dėl užpildyto disko, klaidos, masyvo išjungimo arba jei įrašas visai nebuvo atliktas.
- **„Microsoft Defender“:** kai „Defender“ naudoja vieną ar daugiau procesoriaus branduolių, nuskaitydamas failus, kuriuos įrašo programa, vykdymo metu vieną kartą į stderr išspausdinamas pranešimas: `Note: Microsoft Defender is using N.N CPU cores scanning the capture files, CPU the capture needs. Re-run the Chloros installer with "Exclude the Chloros backend from Microsoft Defender scanning" checked.` Žr. diegimo programos [Sistemos nustatymus](#windows-exe).
- Vienu metu vykdomas tik vienas nepertraukiamas įrašymas kiekvienam masyvui; antrasis atmetamas su klaida `a continuous capture is already running for this array`. Per HTTP sesija yra „`POST /api/camera/array/continuous/start`“ (kūnas „`/api/camera/array/capture`“ plius „`max_captures`“ / „`duration_s`“), `GET /api/camera/array/continuous/status?array_id=ID` ir `POST /api/camera/array/continuous/stop`.

**Interval**(`--interval S`, su `--continuous` arba be jo) ir**`--continuous --smart`** išlaiko ciklą pagal užklausą: viena fiksavimo užklausa per kiekvieną praėjimą — kiekvienu numatytu momentu „Interval“ režimu, po to, kai AE stabilizuojasi „`--smart`“ — kiekvieno praėjimo failai išvardijami kaip „Vieno“ režime, pabaigoje yra eilutė „`Done — N capture pass(es).`“, o „`--count N`“ skaičiuoja praėjimus.

### `array-record` — sujungto indekso vaizdo įrašas/GIF (stebėjimo kokybės)

Įrašo masyvo **tiesioginį sujungto indekso vaizdą** į „`.avi`“ (ir pasirinktinai į „`.gif`“). Kai atidarytas GUI sujungtas vaizdas, jis įrašo tą vaizdą taip, kaip jis rodomas. Jei niekas neatidaryta, užkulisinė sistema pati atkuria vaizdą: indeksą, kurį perduodate su „`--expr`“, arba pagal numatytuosius nustatymus masyvo NDVI, kai tik masyvas suderinamas. Numatytasis NDVI naudoja raudonąjį ir NIR juostų, artimiausias 660 nm ir 850 nm, kaip ir tiesioginis vaizdas. Įrašymo metu atidarytas GUI vaizdas įrašomas tol, kol jis lieka atidarytas. Jis kas 2 s tikrina pažangą ir sustoja, kai gauna `--duration`, `Ctrl+C` arba kai įrašymo įrenginys pats baigia veikti.

Įrašymas, kurio metu nerašomas joks kadras, baigiasi nesėkme: įrašymo programa praneša apie klaidą `no-frames: …` (matrica gali vis dar būti suderinama arba nustatoma jos ekspozicija) ir komanda **baigia veikimą `1`**.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF (no GUI needed)
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# Record a chosen index instead of the default NDVI
chloros-cli lattice array-record --duration 30 \
  --expr "(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)" -o monitoring/
```

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--array-id ID` | tik matrica | Tikslinė matrica (neįrašykite, jei prijungta tik viena). |
| `-o, --output DIR` | `output` | Išvesties katalogas. Santykinis kelias nurodomas nuo aplanko, iš kurio vykdote komandą. |
| `--fps F` | `10` | Įrašymo kadrų dažnis. |
| `--duration S` | iki Ctrl+C | Automatinis sustabdymas po `S` sekundžių. |
| `--gif` | išjungta | Taip pat įrašyti animuotą GIF failą. |
| `--gif-only` | išjungta | Įrašyti tik GIF (be `.avi`). |
| `--expr EXPR` | masyvo NDVI | Indekso išraiška, kurią reikia įrašyti, kai nėra atidarytas joks GUI langas, masyvo juostų pavadinimuose (pvz., `'(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)'`; žr. [juostų pavadinimai](#index--vegetation-maths)). Trumpas spalvos pavadinimas (`NIR`, `Red`) veikia tik tada, kai jį turi viena masyvo juosta. Atidarytas GUI langas užrašomas taip, kaip rodomas. |

### `array-burst` — neapdoroti „Bayer“ duomenys, didelio kadrų dažnio serija (analizės kokybės)

Tiesiogiai nuskaito sinchronizuotos grupės buferį iš kadrų fiksavimo ciklo — **nereikia kalibravimo grandinės, „exiftool“ ar tiesioginio peržiūros režimo** — todėl veikia visu fotoaparato kadrų fiksavimo greičiu. Įrašo neapdorotus kadrus + kiekvieno kadro manifestą + po vieną `.daq` už kiekvieną atskirą DLS rodmenį pagal `<output>/bursts/<base>/`. Perapdorokite neprisijungus (kita komanda) arba perduokite „`--build`“, kad tai būtų atlikta iškart sustojus.

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--array-id ID` | tik masyvas | Tikslinis masyvas. |
| `-o, --output DIR` | `output` | Išvesties katalogas (duomenų srautas patenka į `<DIR>/bursts/<base>/`). |
| `--duration S` | iki Ctrl+C | Automatinis sustabdymas po `S` sekundžių. |
| `--max-frames N` | neribotas | Automatinissustojimas po `N` neapdorotų kadrų. |
| `--orient` | išjungta | Iš šio serijos kadrų sukurti produktai pasukami pagal kameros orientaciją — kiekvienos kameros produktai pagal savo kameros, o sujungto indekso – pagal pagrindinės kameros orientaciją, kaip daroma `array-capture --orient`. Neapdoroti kadrai išlieka nepakeisti; serijos `manifest.json` įrašo kiekvieno elemento orientaciją ir parinktį. |
| `--build` | išjungta | Sustojus, nedelsiant iš naujo apdorokite seriją (taip pat kaip `array-build-video`). |
| `--products …` | `combined:index` | Naudojant `--build`: kurį (-ius) vaizdo įrašą (-us) sukurti (žr. toliau). |
| `--fps F` | `10` | Naudojant `--build`: išvestinio vaizdo kadrų dažnis (fps). |
| `--save-tiffs` | išjungta | Naudojant `--build`: taip pat išsaugoti kalibruotus TIFF failus pagal kiekvieną kadrą (kiekviename iš jų nurodant [unmix clamp mask](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on), kai pikselis apribotas; kompiliacijos rezultatuose jie nurodomi kaip `clamp_masks`). |
| `--gif` | išjungta | Naudojant `--build`: taip pat įrašyti animuotus GIF failus. |

### `array-build-video` — neprisijungus pakartotinai apdoroti išsaugotą seriją

Kiekvieną neapdorotą kadrą laiko atžvilgiu suderina su artimiausiu išsaugotu `.daq` rodmeniu ir perduoda jį per **tą pačią spinduliavimo / atspindžio / indekso grandinę kaip ir importo procesą**, sukuriant vieną ar daugiau vaizdo įrašų.

`--products` yra kableliais atskirtas `kind:level` elementų sąrašas, kur `kind` ∈ `per_cam` | `combined` ir `level` ∈ `radiance` | `reflectance` | `index`. Jei nurodytas tik `level` (be `kind:`), numatytasis nustatymas yra `per_cam`. Numatytasis nustatymas yra `combined:index`.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--burst-dir DIR` | (privaloma) | Kelias į serijos aplanką (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` sąrašas, kaip nurodyta aukščiau. |
| `--fps F` | `10` | Išvestinio vaizdo kadrų dažnis (fps). |
| `--save-tiffs` | išjungta | Taip pat išsaugoti kalibruotus TIFF failus pagal kiekvieną kadrą kartu su vaizdo įrašais. |
| `--gif` | išjungta | Taip pat įrašyti animuotus GIF failus. |

**Vaizdo įraše nėra ribojimo kaukės** (vienas vaizdų srautas, 8 bitai). Vietoj to, kompiliacijos rezultatas turi `clamp_fractions`: kiekvienam vaizdo įrašui, kiekvieno kadro pikselių dalis, kurią atskyrimo procesas apribojo juostoje, kurią sudaro jo R / G / B kanalai — `Chloros:Band{R,G,B}UnmixClampFraction`, kuriuo būtų pažymėtas to kadro spinduliavimo/atspindžio sandaugos rezultatas (kiekvienos dalyvaujančios kameros bendram indeksui). Vykdymo metu blogiausias kiekvieno produkto kadras išspausdinamas eilutėje „`Clamp:`“. „`--save-tiffs`“ prideda TIFF failų kaukes pagal pikselius, `clamp_masks/<level>/` šalia TIFF failų `<level>/` aplanko.**Jei kompiliacija nutraukiama, vaizdo įrašai nelieka sugadinti.** Kiekvienas vaizdo įrašas įrašomas kaip „`<name>.partial.avi`“ ir galutinį pavadinimą gauna tik tada, kai yra baigtas, todėl senesnis to paties pavadinimo vaizdo įrašas išlieka, kol jo pakaitalas bus užbaigtas. Vykdykite kompiliaciją dar kartą, kad pakeistumėte likusį „`.partial.avi`“.

Vykdymo metu išspausdinami visi įrašyti vaizdo įrašai, po to – bet kokie „`Skipped:`“ produktai (prašyta kamera/lygis – arba sujungtas indeksas – dėl kurių nebuvo sukurtas vaizdo įrašas, nurodant priežastį – pvz., nėra kalibravimo rinkinio, nėra DLS atspindžio rodmenų arba nėra raudonosios ir NIR juostų poros kombinuotam indeksui) bei visus „`Note:`“ elementus. RGB kamera, kuriai buvo užklaustas spinduliavimas arba atspindys, vietoj to gauna savo vaizdo įrašą, kurio baltos spalvos balansas nustatomas, jei yra kameros kalibravimo rinkinys; kamera, atkurianti vaizdą be jo, nurodoma `Note:`. Ji **grąžina nelygų nuliui rezultatą**, kai prašytas produktas buvo praleistas arba nebuvo sukurtas pagal užklausą (konfigūruota indekso išraiška nėra įvertinama neprisijungus); sukurti vaizdo įrašai vis tiek yra įrašomi ir išvardijami.

> **Pasirinkite tinkamą įrašymo įrenginį.** `array-record` yra *stebėjimo lygio* — jis įrašo atkurtą tiesioginį kompozitinį vaizdą: GUI sujungtą vaizdą arba, jei GUI neatidaryta, pačios užkulisinės sistemos NDVI (arba `--expr`) vaizdą. `array-burst` → `array-build-video` yra *analizės lygio* — jis saugo neapdorotus jutiklio duomenis pilnu greičiu ir vėliau atkuria kalibruotus spinduliavimo/atspindžio/indekso vaizdo įrašus, nereikalaujant tiesioginio vaizdo.

### Mono (M3M) vienos juostos kameros

**M3M**serija yra „Bayer“**M3C**modelių mono versija: viena siaurajuostė interferencijos filtras kiekvienoje kameroje (`M3M-<lens>-F<wavelength>`, pvz., `M3M-L87-F685`), todėl jutiklis pateikia**vieną pilkosios skalės juostą** be „Bayer“ mozaikos. Nereikia atlikti demozėjimo, pašalinti kanalų tarpusavio persikreipimo ir nustatyti baltos spalvos balanso — visas spalvų apdorojimo procesas, skirtas RGB vaizdui, čia tiesiog netaikomas.

Ką tai reiškia CLI sąsajoje:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**aptinka monochromatinę kamerą ir**praleidžia ją, parodydami vienos eilutės pranešimą**, vietoj to, kad siūlytų beprasmiškus nustatymus. Tos pačios sesijos metu jos vis tiek veikia įprastai su RGB/„Bayer M3C“ kamera.
- **„Bayer“ pikselių formatas reiškia jo gylį.** M3M siūlo tik mono formatus, todėl „Bayer“ formatas iš `--pixel-format` arba `--preset` nustato to paties gylio mono formatą: `BayerRG8` ir `--preset high_speed` duoda `Mono8`; `BayerRG12`, `high_quality` ir `triggered` duoda `Mono12`; suspakuotas pavadinimas lieka suspakuotas (`BayerRG12p` duoda `Mono12p`). Failai ir pavadinimas `lattice format` nurodo „Mono“ formatą. Masyvas, kuriame sumaišytos M3C ir M3M kameros, vieną formatą priima : „Bayer“ kiekvienoje M3C, o „Mono“ gylis – kiekvienoje M3M. Pavadinimas atitinka bitų gylį: tas pats galioja ir atvirkščiai, todėl M3C niekada nėra priskiriama „Mono“ formatui (`--pixel-format Mono12` yra `BayerRG12` M3C kameroje, nepriklausomai nuo to, kuri kamera yra masyvo pagrindinė), o „`lattice format --set`“ atvaizduoja taip pat ir išspausdina nustatytą formatą.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** vis dar veikia — spinduliavimo ir atspindžio žemėlapiai yra *kiekvienos juostos* radiometriniai žemėlapiai ir yra puikiai apibrėžti vienai juostai. Monochromatiniai kadrai turi **tapatybės** jutiklio atsako matricą (be 3×3 atskyrimo), todėl plokštuma praeina per kalibravimo skaičiavimus nepakitusi.
- **Viena monochromatinė kamera negali apskaičiuoti augmenijos indekso.**NDVI, NDRE ir pan. reikalauja mažiausiai dviejų juostų (pvz., Red + NIR). Norint gauti indeksą iš monochromatinės įrangos, reikia nukreipti**keletą** M3M kamerų į skirtingus bangos ilgius, suderinti jas į vieną daugiajuostį rinkinį ir apskaičiuoti *to* indeksą:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Mono_660 --channel nir=Mono_850 \
  --save-multiband -o output/
```

`index --live` pats atidaro kameras (tai yra [laikina sesija](#multi-camera-transient-sessions)), todėl iš pradžių nieko neprijunkite: `--serials`, kitaip profilyje įjungtos kameros (žr. [Indeksas / Augmenijos skaičiavimai](#index--vegetation-maths)). Kol Chloros valdo bet kurią kamerą (GUI, `cam-connect`, `array-connect`) , prašymas atmetamas su HTTP 409 kodu `CAMERA_HELD` ir programa uždaroma `1`; jas atleiskite naudodami `chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all`.

`--channel` simboliai turi **tiksliai** atitikti nustatymų kanalo pavadinimus (skiriamos didžiosios ir mažosios raidės; „NDVI“ pavadinimai rašomi mažosiomis raidėmis `red`, `nir` — žr. `--list-presets`), o juostos pusės pavadinimas nurodo juostą suderintame steke (neprisijungus taip pat priimami nuo 0 prasidedantys juostų indeksai, pvz., `--channel red=0 --channel nir=1`).

Visą steką atskiriantis elementas yra `M3M` žymė modelio eilutėje (ji niekada neatsiranda `M3C` eilutėje), pateikiama GUI/SDK kaip `is_mono`.

---

## Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas (LATTICE kamerų masyvai)

LATTICE kameros transliuoja GVSP per pagrindinio kompiuterio Ethernet adapterį, todėl daugiakamerių masyvų atveju adapterio **vairuotojas**ir**priėmimo žiedo dydis** yra tokie pat svarbūs kaip ir ryšio sparta. Neteisingi nustatymai pasireiškia kaip „`FRAMES WILL DROP`“ / `Reduce ROI to enable` vartais masyvo nustatymų skydelyje (ir `lattice network-analysis` / „SDK“ `analyze_array_network()`), net jei pačios kameros veikia tinkamai.

### USB 10GbE adapteriai — Realtek RTL8157 („Realtek USB 10GbE Family Controller“)

Toliau pateikti nustatymai yra Windows tvarkyklės savybės. Linux sistemoje USB adapteriui taip pat nustatomas apribojimas (400 MB/s x86_64 sistemoje, 200 MB/s „NVIDIA Jetson“) (žr. [Masyvo kadrų dažnis ir serijinio perdavimo modelis](#array-fps--burst-model)), o kiekvienam adapteriui svarbūs branduolio lizdo priėmimo buferiai — žr. [Linux kompiuteriai](#linux-hosts--socket-receive-buffers).

| Elementas | Reikalinga vertė | Kodėl tai svarbu |
| --- | --- | --- |
| **Vairuotojo versija**|**≥ v10.67 (2026 m. sausio mėn.)**, INF `rtump64x64sta.inf` | Senasis**2016 m.**vairuotojas (v10.65, `rtump64x64.inf`) netinkamai tvarko išjungimą ir klaidų patikrinimus su**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**išjungiant, perkraunant ar perjungiant į miego režimą. Perėjimas užstringa (laiko limitas ~5 min.), vartotojas priverstinai išjungia maitinimą, o pasikartojantys netvarkingi išjungimai**sugadina WMI saugyklą**(PowerShell ir įrankiai pradeda strigti su klaida `Invalid class`) ir**užblokuoja USB steką** kito paleidimo metu (tinklo plokštė neįsijungia; USB įrenginiai nustoja būti atpažįstami). Atnaujinkite iš realtek.com (arba raktelio gamintojo) prieš tikėdamiesi, kad kompiuteris paleis iš naujo be problemų. |
| **Priėmimo buferiai**— raktinis žodis `ReceiveBufferLen` |**256**(maksimalus tvarkyklės dydis) | Tinklo plokštės RX žiedas. Tvarkyklės numatytasis dydis**32**palieka tik ~0,26 MB naudingo žiedo — tai pernelyg mažai daugia-kamerų duomenų srautams — todėl masyvo skydelis praneša apie klaidą `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` ir blokuoja prisijungimus. Esant**256**žiedas yra didelis (**~13,5 MB, išmatuota laboratorijos 10GbE serveryje**), suteikiantis RX srautui realią atsargą daugiakameriniams GVSP serijiniams duomenų perdavimams. (Ar tam tikra konfigūracija iš tiesų *prisijungia*, nusprendžiama pagal du patikrinimus — **ištuštinimo atsižvelgiantį**priėmimo patikrinimas ir**bendro perviršinio užsakymo** patikrinimas — o ne paprastas serijos ir žiedo palyginimas; žr. [Matricos fps ir serijų modelis](#array-fps--burst-model).) |
| **Priimamieji URB**— raktinis žodis `PendingReceives` |**64** (maks.) | Siunčiami USB užklausų blokai; didinkite kartu su priėmimo buferiais, kad būtų galima sugerti srautus. |
| **Jumbo rėmelis** — raktinis žodis `*JumboPacket` | **9014** | Reikalingas 9000 baitų GVSP paketams (6 kartus mažiau paketų per rėmelį nei 1500). |

> ⚠️ **Atnaujinus tinklo plokštės tvarkyklę, šios išplėstinės savybės GRĄŽINAMOS į numatytuosius nustatymus.**Atnaujinus arba pakeitus tinklo plokštės tvarkyklę,**vėl pritaikykite** `ReceiveBufferLen=256` ir `PendingReceives=64`, kitaip masyvo skydelis vėl užsiblokuos, net jei „aparatūroje nieko nepasikeitė“. Tai yra pagrindinė priežastis, dėl kurios anksčiau veikusi įranga staiga atsisako prisijungti.

Taikykite iš **administratoriaus teisių** turinčios „PowerShell“ aplinkos (pakeiskite savo adapterio pavadinimą, pvz., `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` apima USB 10GbE adapterius.** Dabar sistema aptinka adapterio tipą ir nustato teisingą „receive-ring“ raktinį žodį: `*ReceiveBuffers`→2048 PCIe tinklo plokštėms (Intel I219, ir pan.), arba „`ReceiveBufferLen`→256“ + „`PendingReceives`→64“ „Realtek“ **USB** 10GbE valdikliui (kuris neatskleidžia „`*ReceiveBuffers`“). Nustatyti ribiniai dydžiai priderinami prie kiekvieno tvarkyklės nurodytos maksimalios vertės (`NumericParameterMaxValue`), todėl niekada nebus įrašyta vertė, kuri viršija leistiną diapazoną. Vykdykite šią komandą iš **administratoriaus teisių** terminalo; kaip ir bet koks registru pagrįstas optimizavimas, pakeitimai įsigalioja tik po adapterio perkrovimo arba kompiuterio perkrovimo. Anksčiau minėtos rankinės `Set-NetAdapterAdvancedProperty` komandos lieka puikia alternatyva — jos taikomos iš karto (iš naujo susiejant adapterį) be perkrovimo.

### Linux kompiuteriai — lizdų priėmimo buferiai

Linux sistemoje, įskaitant „NVIDIA Jetson“, branduolio lizdų priėmimo buferiai yra tokie pat svarbūs kaip ir tinklo plokštės (NIC) priėmimo žiedas. Esant branduolio numatytiesiems nustatymams (`net.core.rmem_max` / `net.core.rmem_default` = 212 992 baitai), kameros siunčiamų GVSP paketų srautas perpildo srauto lizdą dar prieš Chloros spėjant jį perskaityti. Paketai yra atmesti branduolyje, nors pats tinklo adapteris nepraneša apie jokius praradimus, o masyvas praranda kadrų grupes ir sumažina kadrų dažnį. Atlikus matavimus su 4 kamerų masyvu, prijungtu per USB 10 GbE adapterį, padidinus abu parametrus iki 32 MB, masyvo kadrų dažnis po prisijungimo padidėjo nuo 4,96 iki 7,52 kadrų per sekundę, o nepertraukiamo neapdorotų duomenų įrašymo dažnis – nuo 4,7 iki 7,6 įrašų per sekundę, be jokių paketų praradimų branduolyje.

- **.deb paketas juos nustato.** Įdiegus paketą, įrašoma reikšmė `/etc/sysctl.d/60-chloros-gige.conf` (abi reikšmės nustatomos į `33554432`) ir ji iš karto pritaikoma. Jei vertė jau yra nustatyta didesnė, ji paliekama nepakeista.
- **`lattice network` juos tikrina.** Jei kuri nors iš jų yra mažesnė, *Rastų problemų* sąraše nurodoma `socket receive buffers too small for GigE Vision (…)`, o užkulisio žurnale pateikiamas tas pats įspėjimas, kai pirmą kartą prijungiama kamera ar masyvas.
- **Norint jas padidinti rankiniu būdu** (iki kito perkrovimo; paketo failas jas taiko kiekvieno paleidimo metu):

  ```bash
  sudo sysctl -w net.core.rmem_max=33554432 net.core.rmem_default=33554432
  ```

  `lattice network --fix` taip pat padidina ir išlaiko šias reikšmes, tačiau tik tada, kai užkulisinė programa veikia kaip „root“. Užkulisinė programa, veikianti kaip jūsų vartotojas (paleista su `chloros-backend` arba [`chloros-backend@<user>`](#running-the-backend-as-a-service-headless)) negali keisti branduolio nustatymų ir vietoj to išspausdina eilutę `sudo sysctl`.

**Automatinis adapterių optimizavimas taikomas tik prijungtiems adapteriams.** Pirmą kartą prijungus kamerą, kaip „root“ veikiantis foninis procesas optimizuoja laidinius adapterius, kurių priėmimo žiedas yra mažas. Dabar jis optimizuoja tik adapterius, turinčius ryšį, todėl atjungtas integruotas prievadas išlaiko savo MTU, o priėmimo buferiai lieka nepakitę. `lattice network` nebepateikia atjungto adapterioMTU kaip problemą. `lattice network`, `--fix`, `lattice probe` ir adapterių optimizavimas atsižvelgia tik į laidinius Ethernet prievadus, o ne „Jetson“ CAN magistralę (`can0`) ar USB įrenginio režimo jungtis (`usb0`, `usb1`), kurias jis siūlo pagrindiniam kompiuteriui; USB Ethernet adapteris, skirtas kameroms, vis dar įskaičiuojamas.

### Tinklo pagrindai (visos „LATTICE“ jungtys)

- **Adresavimas:** vietinis ryšio adresas `169.254.0.0/16` (GigE Vision LLA). Pagrindinis kompiuteris naudoja statinį adresą `169.254.x.x/16`; kameros ir „DAQ-E“ pačios prisiskiria adresus tame pačiame diapazone. DHCP ar šliuzo nereikia.
- **Paketo dydis:**pageidautina „jumbo“ (9000), bet leiskite automatiniam tikrinimui jį nustatyti — jis apeina kameros 1500 baitų ICMP ribą naudodamas GVSP tikrinimą, todėl nustato „jumbo“ dydį ten, kur laidinis ryšys iš tiesų jį perduoda.**Kiekviena kamera matuojama vieną kartą per kiekvieną backend sesiją ir dar kartą, kai pasikeičia jos ryšys (kitas adapteris, kitas ryšio greitis arba nutrūkęs ryšys, pvz., pakeitus komutatorių); prisijungus iš naujo, naudojamas ankstesnis rezultatas. Po pokyčių už komutatoriaus, kuris liko veikiantis, paleiskite backend iš naujo, kad matavimas būtų atliktas dar kartą.**Pirmasis prisijungimas kartais gali užtrukti keletą minučių atliekant šį patikrinimą, ir žurnale tai nurodoma, kai taip atsitinka. Naudokite „pin“ su `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` tik tada, kai žinote geriau nei zondas, ir teikite pirmenybę vienkartiniam nustatymui, o ne nuolatiniam: „pin“ apeina zondą, todėl jei maršrutas iš tikrųjų negali perduoti 9000,**kiekvienas** duomenų surinkimas baigsis laiko limito pasiekimu su `SC_ERR_TIMEOUT -1011` (žr. [Aplinkos kintamieji](#environment-variables)).
- **RX žiedas keičiasi proporcingai su `ReceiveBufferLen`:**esant numatytam `32`, naudingas žiedas yra ~0,26 MB (per mažas bet kokiam daugia-kameros duomenų srautui); esant maksimaliam `256` jis yra didelis (~13,5 MB, išmatuota laboratorijos 10GbE serveryje), suteikiant realią atsargą. Ar konfigūracija prisijungs, nusprendžiama pagal išteklių naudojimą atsižvelgiančią priėmimo patikrą**ir** toliau pateiktą suvestinę perviršinio užsakymo patikrą — o ne pagal grynąjį serijos ir žiedo dydžių palyginimą.

### Masyvo kadrų per sekundę (fps) ir serijos modelis

Kaip skaityti masyvo nustatymų skydelį (ir `lattice analyze-array` / SDK `analyze_array_network`):

- **Burst duomenys sumuojami pagal kiekvieną kamerą, atsižvelgiant į tikrąjį kiekvienos kameros pikselių formatą.**Mono**M3M**kameros transliuoja**Mono12 (2 B/px)**, arba, naudojant `--pixel-format`, savo gylio Mono formatą (`BayerRG8` yra `Mono8`, 1 B/px);**M3C**„Bayer“ kameros transliuoja 8 arba 12 bitų duomenis (M3C programinė įranga tyliai perduoda „BayerRG12“, net jei prašoma „BayerRG8“). Taigi 4 kamerų pilnos raiškos kadras yra**~12,6 MB, jei visos kameros yra 8 bitų, bet ~25 MB, jei trys kameros yra 12 bitų „mono“**. Projekcija nustato kiekvienos kameros formatą pagal jos modelį (identifikavimo talpyklą), todėl duomenų srautas atitinka tai, ką iš tikrųjų perduoda laidinis ryšys — o ne vienodą „BayerRG8“ prielaidą.
- **USB Ethernet adapterio greitis ribojamas iki 200 MB/s, nepriklausomai nuo jo techninių duomenų.** Efektyvumo lentelė, kuri paverčia ryšio greitį nuolatiniu skaičiumi, yra pagrįsta PCIe duomenimis; USB tinklo plokštė nurodo savo *Ethernet* ryšio greitį, tačiau ją riboja USB magistralė ir jos tvarkyklė. USB 10GbE raktas, naudotas siekiant ~1063 MB/s „nuolatinį“ greitį — skaičių, kuris niekada nebuvo išmatuotas — o dėl to susidariusio greičio svyravimo buvo sugadinta 6–18 % rėmelių, nors vis tiek buvo rodomas tinkamas tikslinis kadrų per sekundę skaičius. USB jungiamieji tinklo adapteriai dabar turi absoliučią ribą: **200 MB/s** Windows ir „NVIDIA Jetson“ sistemose, **400 MB/s** Linux x86_64 sistemoje (Linux sistemoje adapteriui, prijungtam prie USB 2.0 prievado, skiriama **30 MB/s**, o techninių charakteristikų aprašyme nurodoma jį prijungti prie USB 3 prievado) (ribą nulemia pagrindinio kompiuterio USB magistralė ir tvarkyklė, todėl ji nepriklauso nuo techninių charakteristikų; USB 1 GbE adapteris pasiekia ~80 MB/s ir jam tai neturi įtakos). Linux sistemoje adapteris atpažįstamas iš „sysfs“ (atsarginiu atveju naudojamas `ethtool -i` arba `bus-info`). Kiekvienas rodiklis buvo išmatuotas: Windows sistemoje tas pats 4 kamerų rinkinys sugadino 6–10 % kadrų, kai greitis viršijo 200 MB/s; Linux x86_64 sistemoje tas pats adapteris veikė be sutrikimų esant bet kuriam greičiui iki 480 MB/s (pasiektas 273 MB/s greitis – tai pačių kamerų riba). „NVIDIA Jetson“ sistemoje tas pats adapteris veikė be klaidų tik esant 200 MB/s greičiui, o viršijus šį greitį kadrų praradimai pasitaikė su pertraukomis. `wire_ceiling_source` pajėgumų įraše tai nurodyta žodžiais, o `nic_is_usb` – žymėmis. Abiem atvejais šiuos nustatymus galima perrašyti naudojant `--wire-ceiling-mbps`.
- **Praleidimas priklauso nuo išteklių apkrovos, o ne nuo viso duomenų srauto palyginimo su žiedu.** Vienu metu perduodamas duomenų srautas turi tilpti tik į *trumpalaikį užlaikymą* = `max(0, Σ per-cam arrival − host drain) × emit_window`, o ne į visą srautą. Greito kompiuterio / lėtos kameros struktūroje (**PCIe**10G pagrindinis kompiuteris + 4× 1 GbE kameros: atvykimas ≈ 320 MB/s, ištuštinimas ≈ 1063 MB/s) pagrindinis kompiuteris ištuština duomenis greičiau, nei kameros juos užpildo, užlaikymas ≈ 0, todėl pilnos raiškos sim-emit**leidžia**, nors 25 MB srautas viršija 13,5 MB žiedo talpą. Prijunkite tas pačias keturias kameras prie**USB** 10GbE adapterio Windows sistemoje, ir išsiuntimo greitis bus 200 MB/s, o ne 1063 — duomenų priėmimas viršija išsiuntimo greitį, o praradimai pasireiškia kaip sugadinti kadrai, o ne kaip mažesnis kadrų dažnis. 1 GbE kompiuteryje kamerų 31,25 MB/s DLThr riba lemia, kad duomenų srautas viršija perdavimo greitį → sistema teisingai **blokuoja**(šios klasės blokavimui sumažinkite ROI arba naudokite binningą ≥ 2). Prieiga yra viena iš**dviejų** jungiamųjų vartų — kita yra toliau pateikta suvestinė perviršinio užsakymo patikra.
- **Prognozuojamas kadrų per sekundę skaičius (fps) atitinka lėčiausios kameros ciklą.**Vienalaikio perdavimo režimu kiekviena kamera siunčia savo kadrą tuo pačiu metu, todėl masyvas veikia pagal ilgiausią iš lėčiausio nario jutiklio nuskaitymo ir perdavimo trukmės (kadro baitai ÷ jo perdavimo greitis: kameros**prieigos ryšys**, 1 GbE ≈ 80 MB/s, arba jos dalis iš kompiuterio laidinio ryšio ribos, jei ši yra mažesnė). Kiekviena ekspozicija sutampa su ankstesnio kadro nuskaitymu, todėl ekspozicija neįeina į ciklą: automatinė ekspozicija užpildo laikotarpį, kurį nustato perdavimo greitis, tada padidina jautrumą, o tamsus vaizdas nebesumažina perdavimo greičio (versijoje 1.2.0 taip buvo). Tik ilgesnė nei šis laikotarpis rankinė ekspozicija sulėtina suveikimą (žr. [Ekspozicija masyvo naryje](#exposure-on-an-array-member)). Kamerų pridėjimas nelėtina masyvo, kol jų laidų dalys nesumažėja. Serijiniame išsiuntime (`slip-emit-and-capture`) kamerų išsiuntimo laikai sudedami, o kiekvieno elemento ekspozicija įskaičiuojama į laiko poslinkį. Išmatuota esant pilnam jutiklio naudojimui, 12 bitų, 9 kamerų masyve su PCIe 10 GbE pagrindiniu kompiuteriu: 10,7 kadrų per sekundę arba 14,0 kadrų per sekundę naudojant [suspakuotą perdavimą](#packed-12-bit-transport---packed).
- **Po nepertraukiamo fiksavimo projekcija rodo, kokį dažnį ji išlaikė.** 30 s ar ilgesnis fiksavimas automatiniu dažniu įrašo tą dažnį, kurį sistema pasiekė su tuo masyvo dydžiu šiame kompiuteryje; dažnio reguliatoriaus trumpi bandymai su didesniu dažniu neįtraukiami. Nuo to momento `array-connect` eilutėje ir masyvo nustatymų skydelyje nurodoma ta sparta, kai ji yra mažesnė už modelio nustatytąją, kartu nurodant, kas ją apribojo: `clock-bound` (masyvas veikė nustatytu greičiu, griebimo kilpa laukė savo trigerio signalo), `loop-bound` (griebimo kilpai nebeliko laiko), `capture-bound` (fiksavimas atsiliko ir praleido grupes) arba `measured` (nenurodyta, kuri iš šių priežasčių). Modelio rodiklis pateiktas `wire_fps_max`. Masyvas vis tiek pasiekia tą dažnį, kurį perduoda laidinis ryšys.
- **Masyvas veikia bet kuriame tinkle — viską lemia kadrų dažnis.**Kiekviena kamera gauna lygų**susidūrimams atsparaus ryšio ribos**(`sustained × sim_emit_factor`) dalį, sinchronizuotą su GevSCPD, ir visos jos vis dar fotografuoja pagal tą patį trigerį. Lėtuose tinkluose dalis yra maža, o kadrų dažnis mažėja kartu su ja: 8 pilnos raiškos 12 bitų kameros 1 GbE tinkle be „jumbo“ rėmelių gauna po 52 / 8 = 6,5 MB/s, tai yra apie**1 kadrą per sekundę**(apie 1,3 kadrų per sekundę su suspaudimu), o 10G kompiuteris jas valdo 11–14 kadrų per sekundę greičiu. Jei kamerų negalima sinchronizuoti taip lėtai — tai sužinosite, perskaitę kiekvienos kameros GevSCPD diapazoną — masyvas perjungia į**serijinį**perdavimo režimą (`slip-emit-and-capture`): kameros paeiliui perduoda duomenis laidu pilnu ryšio greičiu, suaktyvinamos viena po kitos, taigi kiekvienas rėmelis vis tiek pasiekia tikslą, tačiau nariai**nėra eksponuojami vienu metu**. `analyze-array` ir „connect“ išvestis nurodo režimą; nustatykite `--force-tier sim-capture-sim-emit`, kad atsisakytumėte atsarginio režimo. Kadro dydis neturi įtakos šiam pasidalijimui, todėl**kadrų sujungimas ir mažesnis ROI padidina kadrų dažnį, bet nekeičia to, ar masyvas tilps**. Tik tas laidą, kuris negali perduoti masyvo net ir nuosekliai, yra**perkrautas**— pvz., kai kamerų yra žymiai daugiau, nei vienas laidas gali aptarnauti, arba kai juo transliuoja daug kitų kamerų — ir tokiu atveju simptomas yra GVSP paketų praradimas, o ne mažesnis dažnis: `analyze-array` nuliuoja pasiekiamų kadrų per sekundę (fps) skaičius ir išspausdina `** OVER-SUBSCRIBED**`, ryšys užmezgamas su garsiu įspėjimu, o HTTP ryšys su `pin_resolution: true` (griežtas režimas) **atsisako**. Sprendimai: mažiau kamerų, „jumbo“ rėmeliai nuo vieno galo iki kito arba greitesnis tinklo adapteris. `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` sumažina atsisakymo lygį iki garsaus įspėjimo bandymams — žr. [Aplinkos kintamieji](#environment-variables).

### Masyvo būklė — kuri posistemė praranda rėmelius

Prijungto masyvo `GET /api/camera/array/<array_id>/capability` rodo realaus laiko
`health` bloką, kuris perskaičiuojamas **10 sekundžių** slenkamuoju langu. Jis suskirsto kadrų praradimą
į dvi priežastis, kurioms reikia priešingų sprendimų, o ne pateikia vieną „nepilną“
rodiklį, kuris nenurodo nė vienos iš jų:

| Laukelis | Ką tai reiškia | Kurioje posistemyje |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (pagal serijinį numerį) | Rėmelis **atvyko, bet buvo struktūriškai sugadintas**— GVSP paketo praradimas. |**Tinklas**: laidų pralaidumas, tempas, NIC RX žiedas, MTU |
| `never_arrived_rate_pct` (pagal serijos numerį) | Rėmelis **visai nepasiekė**— kamera nesuveikė arba nieko neišsiuntė. |**Sukėlėjas / sinchronizacija**: M8 kabelis, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Blogiausias kiekvienos kameros rodiklis. | — |
| `per_cam_rate_pct` | Bendras neišsamus rodiklis vienai kamerai (abi priežastys kartu). | — |
| `stable_for_seconds` | Kiek laiko kiekviena kamera išliko žemiau 0,01 %. | — |

Viršijus 5 %, užkulisinė sistema į žurnalą įrašo eilutę „`[array-health <id>] WARN`“, nurodydama padalijimą —
pirmojo pažeidimo metu, pasikeitus rimtumo lygiui, kartą per minutę, kol padėtis tęsiasi, ir dar kartą, kai
ji išsilygina. Sugedusi pusė į backend žurnalo failą įrašo eilutę „`[gvsp-corrupt <SN>]`“
pirmojo atitikimo kiekvienai kamerai ir priežasties atveju, o vėliau – apibendrinimą kas 60 s; programos žurnalo skydelyje vietoj to rodoma eilutė
eilutė „`[array-health <id>]`“. Kiekvienas įvertinimas vis tiek patenka į galinio serverio žurnalo failą;
skaitikliai juda su kiekvienu buferiu, nepriklausomai nuo to, kas išspausdinama.

Tame pačiame įraše nurodytas skaičius, nuo kurio priklauso visas paskirstymas:

| Laukas | Ką reiškia |
| --- | --- |
| `wire_ceiling_mbps` | Galiojantis nuolatinis pralaidumo limitas, MB/s. |
| `wire_ceiling_source` | Iš kur gautas šis skaičius, žodžiais — pvz., `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` (Linux x86_64 sistemoje – `USB-capped 400 MB/s (…; sysfs: bus=usb (usb2/2-1/2-1:1.0))`) arba `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, kai jį nustato `--wire-ceiling-mbps` (arba GUI laukelis **Wire Budget**). |
| `nic_is_usb` | `true`, jei naudojamas USB Ethernet adapteris — žr. aukščiau nurodytą USB ribą. |

**Aiškinimas:** nelygi nuliui `gvsp_corrupt_rate_pct` reikšmė, kai `never_arrived_rate_pct` yra lygus 0
reiškia, kad trigeris ir kabelio sinchronizacija veikia puikiai, o 100 % nuostolių tenka tinklo
keliui — sumažinkite `--wire-ceiling-mbps` ir prisijunkite iš naujo. Priešinga situacija rodo, kad problema yra
sinchronizacijos kabelyje arba trigerio linijoje.

> **`--target-fps` nėra veiksnys, lemiantis sugadintus rėmus.** GevSCPD tempas nustatomas
> vieną kartą prisijungus, todėl sumažinus suaktyvinimo dažnį keičiasi darbo ciklas, o ne
> vienalaikio siuntimo serijos dažnis. Išmatuotas 5 kartų paklausos sumažinimas nepagerino situacijos;
> sumažinus laidinio ryšio ribą nuo 240 iki 200 MB/s, to paties įrenginio sugadintų kadrų procentas sumažėjo nuo 10,4 %
> iki 0,00 %.

> **Veikiantis masyvas negali pats persuplanuoti savo veikimo naudojant „LATTICE“ (M3C / M3M) programinę įrangą.** Ji
> negali šio gedimo pašalinti pati; atjunkite ir vėl prijunkite, kad prisijungimo laiko pasirinkimo funkcija galėtų
> iš naujo suplanuoti veikimą pagal naują ribą.

### Simptomas → sprendimas

| Simptomas (Masivo nustatymai / prisijungimas / `analyze_array_network`) | Priežastis | Sprendimas |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` nustatytas į 32 (paprastai po tvarkyklės atnaujinimo) | Nustatykite `ReceiveBufferLen`→256, `PendingReceives`→64; vėl atidarykite skydelį (perkraukite užkulisinę programą, jei ji išsaugojo seną žiedo dydį) |
| Įstrigimas perkraunant ar išjungiant sistemą; vėliau `Invalid class` WMI klaidos, NIC neįsijungia, trūksta USB įrenginių | Senoji 2016 m. „Realtek USB 10GbE“ tvarkyklė → BSOD `0x9F` → priverstinis išjungimas | Atnaujinkite adapterio tvarkyklę iki ≥ v10.67 (2026), tada vėl pritaikykite aukščiau nurodytus priėmimo žiedo nustatymus |
| Masyvas prisijungia esant maksimaliam jutiklio pajėgumui, bet kadrų dažnis yra žemas | Automatinis režimas išlaiko maksimalų jutiklio pajėgumą ir leidžia dažniui nukristi iki to, ką perduoda kabelis | Pasirinkite vieną iš greitesnių variantų, nurodytų prisijungimo išvesties sąraše (`--binning 2` / `4` arba mažesnį `--width`/`--height`), įjunkite „jumbo“ kadrus arba atnaujinkite ryšį |
| „Array“ rodo tinkamą tikslų kadrų per sekundę skaičių, tačiau perduoda tik jo dalį; `health.gvsp_corrupt_rate_pct` nėra lygus nuliui, `never_arrived_rate_pct` 0 | Kompiuterio numanomas pralaidumo biudžetas viršija tai, ką jis iš tikrųjų gali išlaikyti (būdinga USB Ethernet adapteriui, siaurai PCIe juostai arba bendrai struktūrai) | Prijunkite iš naujo nustatydami mažesnę `--wire-ceiling-mbps` reikšmę ir dar kartą patikrinkite būklės bloką. **Ne** `--target-fps` — „GevSCPD“ srauto reguliavimas nustatytas prisijungimo metu |
| Linux: masyvas praranda rėmelių grupes ir sumažina rėmelių perdavimo dažnį, nors tinklo plokštė nepraneša apie praradimus; `lattice network` nurodo `socket receive buffers too small` | Branduolio lizdo priėmimo buferiai yra numatyti pagal numatytuosius nustatymus, todėl kameros paketai prarandami branduolyje | Padidinkite `net.core.rmem_max` / `rmem_default` reikšmę iki 32 MB — žr. [Linux kompiuteriai](#linux-hosts--socket-receive-buffers). .deb failas tai atlieka diegimo metu |
| Kamerų nėra paskelbtose grupėse; `health.never_arrived_rate_pct` nėra lygus nuliui, `gvsp_corrupt_rate_pct` lygus 0 | Suveikimo / sinchronizavimo kelias — kameros nesuveikia, tai nėra tinklo problema | Patikrinkite M8 sinchronizavimo kabelį ir `--line`; įsitikinkite, kad kiekvienas narys yra įjungtas (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` viršyta `analyze-array` reikšmėje arba atsisakymas prisijungti griežtajame režime (`pin_resolution`; `array over-subscribes the wire`) | Laidas negali perduoti duomenų masyvo bet kokiu greičiu, kurį pasiekia kameros, vienu metu ar paeiliui — kamerų yra žymiai daugiau, nei laidas gali aptarnauti, arba jame transliuoja kitos kameros | Mažiau kamerų, „jumbo“ rėmeliai nuo vieno galo iki kito arba greitesnis tinklo adapteris. **ROI/binning NEPADĖS** (dalijimasis nepriklauso nuo rėmelio dydžio). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` perrašo bandymų stende (priima paketų praradimą) |
| Masyvas prisijungia per `slip-emit-and-capture` (serijinis) lėtu tinkle | Kamerų veikimo dažnio negalima sumažinti tiek, kad jos galėtų vienu metu dalytis laidu, todėl jos veikia paeiliui; kadrai nėra eksponuojami tuo pačiu metu | „Jumbo“ rėmeliai arba greitesnis tinklo adapteris atkuria vienalaikį vaizdo fiksavimą; `--force-tier sim-capture-sim-emit` to reikalauja (ir vietoj to praranda paketus) |

---

## `chloros-cli daq`

Spektrinio jutiklio komandos. `pool-*` komandos yra „ploni“ HTTP-klientai, kurie valdo jutiklį per užkulisio nuolatinį rezervą, todėl jiems reikalingas veikiantis užkulisys (žr. [Užkulisio paleidimas](#start-the-backend)). Už transportą atsakingas užpakalinis modulis, todėl CLI ir SDK skriptai dalijasi vienu aktyviu identifikatoriumi, o ne konkuruoja dėl nuosekliojo prievado. `pool-discover` yra vienintelis būdas sužinoti DAQ-M BLE MAC adresą.

> **`chloros-cli daq --help`** (arba `-h`, arba `daq help`) išvardija `pool-*` pakomandas (Linux sistemoje – kartu su komandomis, skirtomis tiesiogiai valdyti aparatūrą, kurias įtraukia kompiliacija), o „`chloros-cli daq <subcommand> --help`“ (arba „`daq help <subcommand>`“) pateikia kiekvienos iš jų parinktis. Jos apima prisijungimą, srauto perdavimą, kalibruotų „`.daq`“ failų įrašymą, „cap“ profilių keitimą ir DAQ pririšimą prie kameros, o „DAQ-E“ atveju – jo tinklo nustatymus, PTP, perkrovimą ir programinės įrangos atnaujinimus. DAQ taip pat galima valdyti iš Python naudojant „`chloros_sdk.connect_daq_sensor()`“, kuris naudoja tą patį bendrą kelią.
>
> **Naudokite „`pool-*`“ komandas visose platformose.** Jos veikia vienodai Windows, Linux ir „Jetson“ sistemose. Windows sistemoje `daq discover` ir `daq list` yra `pool-discover` aliasai, o bet kuri kita `daq` komanda, kuri nėra `pool-*`, baigia „`1`“ su „`DAQ direct-hardware commands require the full daq package`“. Linux ir „Jetson“ sistemose `daq discover`, `daq list` ir kitos komandos, išskyrus `pool-*` bei `daq`, yra DAQ įrankio tiesioginėskomandos, nepriklausančios nuo aparatinės įrangos: jos atidaro jutiklį pačioje CLI aplinkoje, už backend'o rezervo ribų (t. y. ne tame jutiklyje, kurį valdo backend'as, GUI ar skriptas), ir ten priimamos komandos „`daq discover`“, „`--only`“ bei „`--timeout`“, bet ne „`--json`. Šiame žinyne aprašomos tik `pool-*` komandos.

### DAQ jutiklio pirmojo prisijungimo darbo eiga

```bash
# 1. Scan every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U serial port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq pool-discover
chloros-cli daq pool-discover --only ble           # BLE only
chloros-cli daq pool-discover --json               # machine-readable

# 2. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect                                   # smart-detect
chloros-cli daq pool-connect --port /dev/ttyUSB0               # DAQ-U on a serial port (Windows: COM3)
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 3. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 4. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

#    Bind it to a camera, so that camera's captures use and save its readings
chloros-cli daq pool-bind --camera 213800234 CB-7C-A8-2E-5F

# 5. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 6. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### `pool-*` nuoroda

| Pakomanda | Paskirtis |
| --- | --- |
| `daq pool-connect` (smart-detect) | Atidaryti jutiklį užkulisiniame pulke. |
| `daq pool-connect --port PORT` (`-p`) | „DAQ-U“ per konkretų nuoseklųjį prievadą. |
| `daq pool-connect --ble` | DAQ-M per BLE, automatinis MAC adresų nuskaitymas. |
| `daq pool-connect --mac MAC` (`-m`) | DAQ-M per BLE su žinomu MAC adresu (reikalauja `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E per Ethernet su žinomu kompiuterio vardu arba IP adresu (apima `--eth`). |
| `daq pool-connect --eth` | DAQ-E per Ethernet, kompiuteris aptiktas automatiškai (mDNS + ARP atsarginis variantas; veikia iš tuščios ARP talpyklos Windows ir Linux sistemose). |
| `daq pool-connect --integration-time MS (-t) --frame-avg N (-f) --no-ae` | Integracijos lango / AE būsenos nustatymas. |
| `daq pool-connect --no-stream` | Prisijungti, bet dar nepradėti srauto perdavimo (tęsti su `pool-stream --start`). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | Viršutinės ribos korekcijos profilis. Numatytasis nustatymas serveryje yra `sunshine_cosine`; `none` yra „plikas“ jutiklis. [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor) jį ignoruoja (fiksuotas difuzorius). **Jutikliai tiekiami be dangtelio, todėl pavadinkite pritvirtintą dangtelį:** numatytasis įrašomas kaip numanomas (`cap_id_source=auto_default`), o apdorojimo metu apie tai bus įspėjama, nes be dangtelio DAQ-U, esant saulės spindulių korekcijai, rodo ~30 kartų didesnius rodmenis. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Nuskaitykite kiekvieną transportą, ieškodami jutiklių, prie kurių galėtumėte prisijungti, bet neprisijungdami. **Štai kaip rasti DAQ-M BLE MAC adresą.** `--timeout` nustatoma kiekvienam transportui (numatyta 5 s, 1–20). Jau atidaryti sensoriai sąraše nerodomi — prijungtas „DAQ-M“ nustoja siųsti skelbimus — todėl jiems naudokite „`pool-list`“. `usb` rodo tik USB serijinius įrenginius: kompiuterio nuosavi serijiniai prievadai (`COM1`, „Linux “ `/dev/ttyS*`) negali būti jutikliais ir yra praleidžiami. |
| `daq pool-list` | Rodo visus jutiklius užkulisiniame pulke. |
| `daq pool-bind (--camera SERIAL \| --array ARRAY_ID) {SENSOR \| --clear}` | Susieja kamerą arba masyvą su DAQ, kaip tai daroma GUI šviesos jutiklio pasirinkimo funkcijoje: jos užfiksuoti duomenys naudoja, atitinka ir išsaugo to DAQ rodmenis. `SENSOR` yra jutiklio identifikatorius iš `pool-list` arba adresas, kuriuo pasiekiamas DAQ. `--clear` (nėra `SENSOR`) nutraukia susiejimą, ir duomenų fiksavimas grįžta prie naujausio DAQ. Susiejimas trunka tol, kol iš naujo paleidžiamas užkulisinis modulis arba kol ta kamera, masyvas ar DAQ atjungiamas: atjungimas (šiuo atveju iš SDK arba programoje) nutraukia visus atjungiamos įrangos susiejimus, nepriklausomai nuo to, kas juos nustatė, o masyvo elementų susiejimus – kartu su pačiu masyvu. Kamera turi būti prijungta, atidarytame projekte arba anksčiau atidaryta šiame kompiuteryje, o matrica – prijungta arba atidarytame projekte; bet koks kitas serijos numeris ar identifikatorius atmetamas pagal pavadinimą ir niekas nesiejamas. |
| `daq pool-disconnect {--sensor-id ID \| --all}` | Atleidimas — nepriklausomai nuo to, kas atidarė jutiklį, todėl taip pat atlaisvinamas jutiklis, kurį avariją patyręs scenarijus paliko rezervo bazėje (pirmiausia užbaigiamas bet koks įrašymas į jį). Jutiklis, kurio nėra rezervo bazėje, neturi ko atleisti: jis išspausdina „`ID: already released (not in the pool)`“ ir užsidaro „`0`“; atleidimas, kurį užkulisinė programa atmetė arba nepatvirtino, užsidaro „`1`“. SDK: `DAQSensorSession.force_disconnect()`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N naujausi spektro kadrai (numatyta reikšmė – 1). |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Tęsti / pristabdyti srautą. |
| `daq pool-record --sensor-id ID [--duration SEC] [-o DIR] [--device-name NAME] [--confirm-after S] [--heartbeat S]` / `--stop` | Pradėti / sustabdyti .daq įrašymą. `-o` numatytasis nustatymas yra `~/Documents/DAQ Live View` galinio serverio kompiuteryje (santykinis kelias yra toje pačioje aplankoje, iš kurios paleidžiate komandą); `--duration` (`-d`, numatyta reikšmė `0` = iki `--stop`). `--confirm-after S` įspėja, jei praėjus S sekundžių nuo paleidimo nėra gauta jokių rodmenų (numatyta reikšmė – 20; 0 išjungia šią funkciją); naudojant `--duration`, `--heartbeat S` kas S sekundžių išspausdina duomenų skaičių ir įspėja, kai jis nustoja didėti (numatyta reikšmė – 30; nustatymas 0 išjungia šią funkciją). Įrašas DAQ-E, kurio duomenų srautas nutrūksta, tęsiasi tame pačiame faile, kai tik užkulisinė sistema vėl prisijungia, o `--stop` išvardija pertraukas ([DAQ-E, kuris nustoja perduoti duomenis](#a-daq-e-that-stops-delivering)). „Ctrl+C“ anksti nutraukia `--duration` įrašymą ir apie tai praneša; be `--duration` įrašymas po Ctrl+C (išėjimas iš `130`) tęsiasi iki `--stop`. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Vykdymo metu pakeisti viršutinės ribos korekcijos profilį. Būtinas `--cap-id`; `--cap-id none` pašalina viršutinės ribos korekciją (neapdorotas jutiklis). DAQ-E-S išlaiko `as_recorded` nustatymą, kad ir koks jis būtų. |
| `daq pool-enable-ptp --sensor-id ID` | Vėl įjunkite PTP DAQ-E įrenginiui, kurio PTP yra išjungtas. Nustatymas išsaugomas įrenginyje, todėl išjungtas įrenginys lieka išjungtas — jo laiko žymos nėra sinchronizuotos pagal PTP — kol ši komanda nebus įvykdyta. `pool-list` pažymi tokį įrenginį (`!! PTP is switched OFF`) ir išspausdina šią komandą jam. Chloros niekada pats iš naujo neįjungia įrenginio, nes išjungimas gali būti tyčinis. |
| `daq pool-info --sensor-id ID [--json]` | DAQ-E įrenginio nuosavi nustatymai: jo programinė įranga (ir tai, ar ši Chloros versija turi naujesnę), jo adresas ir kaip jis buvo gautas (DHCP / link-local / statinis), išsaugoti tinklo nustatymai, kol jutiklis juos paleis (įskaitant vieną, išsaugotą su `--no-reboot`), srautas įjungus maitinimą ir PTP. Jei jutiklio srautas nepasiekia šio kompiuterio, programa nuskaito jutiklį per naują ryšį ir apie tai praneša. |
| `daq pool-set-network --sensor-id ID {--auto \| --static IP [--mask M] [--gateway G]} [--no-reboot] [--force] [--timeout S]` | Nustatykite „DAQ-E“ naudoti DHCP (`--auto`) arba statinį adresą (`--mask` numatytasis `255.255.255.0`). Jutiklis iš naujo paleidžiamas, kad šie nustatymai įsigaliotų, ir vėl prisijungia su ankstesniais nustatymais, laukdamas iki `--timeout` sekundžių (numatyta 45); `--no-reboot` tik išsaugo šiuos nustatymus. Žr. [DAQ-E įrenginio nustatymai](#daq-e-device-settings). |
| `daq pool-set-auto-stream --sensor-id ID {--on \| --off}` | Ar DAQ-E pradeda transliaciją įjungus maitinimą, kai nėra prijungta jokia programinė įranga (išsaugoma jutiklyje). Chloros bet kuriuo atveju pats pradeda transliaciją. |
| `daq pool-reboot --sensor-id ID [--timeout S]` | Perkraukite DAQ-E ir vėl prijunkite jį su ankstesniais nustatymais (laukiama iki `--timeout`, numatyta 45 s). |
| `daq pool-update-firmware --sensor-id ID [--timeout S]` | Įrašykite šio Chloros leidime esančią DAQ-E programinę įrangą į jutiklį, kuriame veikia senesnė versija, tada vėl jį prijunkite (trunka apie minutę; neišjunkite maitinimo; `--timeout` numatyta 45 s, kol jis vėl pradės veikti). Jau naujausią versiją turintis jutiklis paliekamas kaip yra. |

### DAQ-E, kuris nustoja perduoti duomenis

DAQ-E, esantis duomenų bazėje ir 20 sekundžių neperduodantis spektro, yra iš naujo prijungiamas serverio. Tai atliekama išjungiant ir vėl įjungiant jungiklį, maitinančią jutiklį: jutiklis paleidžiamas iš naujo ir neperduoda duomenų, kol negauna nurodymo (išskyrus atvejus, kai nustatytas `pool-set-auto-stream --on`).

- **Pirmiausia patikrinama, ar tai tas pats jutiklis.** Jutiklio ankstesniu adresu, nieko nekeičiant, užklausiamas jutiklio pavadinimas, MAC adresas ir serijos numeris. Jis taip pat siunčia užklausą naudodamas jutiklio vardą „`daq-e-<id>.local`“, tačiau pasitiki šiuo atsakymu tik tada, kai jis gaunamas iš paties jutiklio tinklo adapterio (MAC). Joks kitas atsakęs įrenginys nėra iš naujo prijungiamas ar įrašomas.
- **Jis vėl prisijungia naudodamas sesijos nustatymus:** integracijos laiką, kadrų vidurkavimą, automatinę ekspoziciją ir ekspozicijos profilio ribą tokius, kokie jie buvo, o ne jutiklio įjungimo numatytuosius nustatymus. Jutiklis, kurio PTP buvo įjungtas, o grįžus buvo išjungtas, vėl įjungiamas. Tada jis iš naujo paleidžia srautą ir kompiuterio imtuvą, skirtą jam priimti.
- **Jis bando iš naujo vis ilgesniais intervalais:** iš karto, tada po 5, 10, 20 ir 40 sekundžių, o vėliau – kas 60 sekundžių, kol jutiklis nepasiekiamas. Kiekvieną kartą tik klausiama jutiklio, kas jis yra. Jutiklis, kuris atsako, bet po trijų prisijungimo bandymų vis dar nieko nesiunčia, paliekamas ramybėje ir apie jį pranešama (`auto-reconnect GAVE UP`). `pool-stream --start` bando dar kartą.
- **`pool-list` rodo esamą padėtį.** Kol jutiklis tyli, jo eilutė `STREAM FAULT` nurodo, ką daro prisijungimo atnaujinimo procesas. Eilutė išnyksta su pirmuoju spektru, o jutiklis tada rodo `reconnected automatically N time(s)`.
- **Įrašymas tęsiasi toje pačioje `.daq` vietoje.** Failas pažymi kiekvieną pertrauką dviem eilutėmis: `event_type` 5 paskutiniame spektre prieš pertrauką ir 6 pirmajame po jos, kiekvienoje nurodant tik laiko žymą. `pool-record --duration` išspausdina „`!! STREAM LOST …`“, kol jutiklis yra išjungtas, ir „`stream resumed after …`“, kai jis vėl įjungiamas. „`pool-record --stop`“ išvardija kiekvieną pertrauką ir pateikia įspėjimą, kaip ir tuščio failo atveju (įrašas, kuriame nėra jokių rodmenų, baigiasi `1`).
- **Įrašas išlaiko vieną kalibravimą.** Jei jutiklis grįžta su kitokiu kalibravimu, kai įrašas jau vyksta, likusi įrašo dalis išlaiko tą kalibravimą, su kuriuo buvo pradėta, ir apie tai pranešama sustabdymo įspėjime. `pool-disconnect` + `pool-connect` perima naują kalibravimą.
- **Jūsų komandos turi pirmenybę.** Jutiklis, kuriam taikote komandą `pool-disconnect` arba sustabdote komandą `pool-stream --stop`, niekada nebus iš naujo prijungtas, net jei prijungimas jau vyksta.
- **`pool-info` su neveikiančiu jutikliu** nuskaito jutiklį nauju ryšiu ir prideda `!! stream NOT reaching this computer`. Apie jutiklį, kuris neatsako, pranešama iš karto. `pool-list` neberodo PTP būsenos jutikliui, kuris nustojo atsakyti.
- DAQ-U ir DAQ-M nėra aptariami. DAQ-M pats iš naujo prisijungia per „Bluetooth“, o nuo USB atjungtam DAQ-U vėl reikia „`pool-connect`“.

### DAQ-E-S — fiksuotas difuzorius ir pakreipimo jutiklis

**DAQ-E-S** yra DAQ-E su dviem skirtumais, kurie svarbūs programinei įrangai. Viskas kita — aptikimas, prisijungimas, PTP, įrašymas, toliau nurodyti įrenginio nustatymai — veikia lygiai taip pat kaip ir DAQ-E atveju.**Jo difuzorius yra nuolatinis, todėl nereikia koreguoti dangtelio.** „Sunshine“ tipo difuzorius yra įmontuotas ir buvo ant įrenginio, kai buvo atliekamas jo gamyklinis kalibravimas, todėl tas kalibravimas jau rodo tikrąjį žemyn nukreiptą spinduliavimą. Chloros nustato jo dangtelį į `as_recorded` (be profilio), nesvarbu, ko būtų prašoma: apibrėžtis, pavadinta jo vardu (`sunshine_cosine`, `fov_*`, `none`), yra ignoruojama su pranešimu, o jo įrašuose saugoma `cap_id_source=model`, todėl įspėjimas apie numanomą ribą jam niekada nesuveikia. Taip pat nenaudokite ribos jo duomenims rankiniu būdu: viršuje esantis „`sunshine_cosine`“ rodytų maždaug 11 kartų didesnę vertę.

**Jame įmontuotas 3 ašių akselerometras.** Saulės spindulių difuzoriaus kosinusinė charakteristika galioja tik tada, kai jis yra horizontalioje padėtyje, todėl pakreiptas jutiklis rodo neteisingą spinduliavimo intensyvumą — o atspindžio koeficientas perima šią paklaidą — be jokių kitų požymių. Todėl kiekvienas DAQ-E-S rodmuo nurodo jo padėtį:

| Laukas | Reikšmė |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Kampai laipsniais. `None`, kai programinė įranga pažymi kampą kaip neapibrėžtą (laisvasis kritimas, neveikianti ašis). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Pagreitis miligais. `mag_mg`, kuris smarkiai skiriasi nuo ~1000, reiškia, kad įrenginys judėjo ir kampai nėra reikšmingi tam mėginiui. |
| `cal_applied` | Nustatyta: ašys ir kampai yra difuzoriaus koordinačių sistemoje (taikomas prietaiso montavimo kalibravimas). Išvalyta: neapdorota plokštės koordinačių sistema, kurioje gali būti rodomas iki ~2,3° nuokrypis, kuris nėra tikras. |
| `sample_age_ms` | Akselerometro duomenų amžius, kai buvo išsiųstas spektras. |
| `temp_raw` | Nekalibruota lustų temperatūros **tendencija** — tik skirtumai, niekada ne termometro rodmenys. |

`daq pool-latest --json` grąžina juos po kiekvieno kadro `imu` raktu (`null` DAQ-U, DAQ-M arba paprastame DAQ-E). Iš DAQ-E-S įrašytas `.daq` tas pačias reikšmes saugo savo `imu_*` stulpeliuose (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) šalia kiekvieno spektro, todėl skaitytojas gali atskirti debesį nuo apverstos trikojės. Visuose kituose modeliuose šios stulpelės yra tuščios.

Padėties duomenų srautas pateikiamas atskirai: `daq pool-list` išspausdina `!! ATTITUDE STREAM FAULT (spectra unaffected)`, kai prietaisas neveikia, ir nurodo, iš kiek gedimų jis vėliau atsigavo. Gedimas šioje vietoje niekada nesukelia spragos spektro įraše. `daq pool-discover` nurodo įrenginį kaip `DAQ-E-S` (kaip `DAQ-E`, jei jis neatsako į tapatybės patikrinimą per maždaug 2 s).

### DAQ-E įrenginio nustatymai

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330

# A fixed address on the network this computer is on (192.168.1.x)
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1

# Back to the factory setting: DHCP, falling back to a 169.254.x.x
# link-local address when no DHCP server answers
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto

chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

- **`--static` be `--gateway` nesaugo jokio šliuzo** (bet koks anksčiau nustatytas šliuzas yra pašalinamas), o tai tinka kamerų tinklui be maršrutizatoriaus.
- **Šio kompiuterio tinkle turi būti nustatytas statinis adresas.** Chloros atmeta adresą, kurio potinklis neapima šio kompiuterio adreso jutiklio tinkle: po perkrovimo niekas negalės pasiekti jutiklio, kad jį pakeistų atgal. Naudokite „`--force`“ tik tada, kai šiam kompiuteriui bus priskirtas naujas adresas arba jis bus nukreiptas į naują potinklį.
- **Perkraunimas atmetamas, kol jutiklis įrašo.** Pirmiausia sustabdykite įrašymą (`pool-record --stop`). Įrašymas, pradėtas prieš pat perkraunimą, jo metu uždaromas, o `pool-set-network`, `pool-reboot` ir `pool-update-firmware` baigiasi eilute `Warning:`, kurioje nurodytas įrašymo failo pavadinimas (net ir tada, kai jutiklis vėliau negrįžta).
- **Po perkrovimo Chloros vėl prijungia tik patį jutiklį.** Jis laukia ten, kur buvo jutiklis – o po „`--auto`“ taip pat pagal jo „`daq-e-<id>.local`“ pavadinimą – atsakymo su jutiklio pavadinimu, kompiuterio vardą ir modelį, iš tinklo adapterio (MAC), kurį jis turėjo, ir vėl prisijungia prie adreso, iš kurio buvo gautas atsakymas. Atsakymas su šiuo pavadinimu priimamas tik su tuo pačiu MAC adresu, taigi iš kompiuterio, kuris pasiekia jutiklį per maršrutizatorių, arba iš WSL2 jo numatytame NAT tinkle, jutiklis, kurį DHCP perkėlė į naują adresą, nėra iš naujo prijungiamas: komanda tai nurodo ir išveda komandą `pool-connect --eth-host <its address> …`, kuri jį sugrąžina su ankstesniais ekspozicijos ir ekspozicijos ribos nustatymais.
- **`pool-update-firmware` atmeta jutiklį, kurio programinės įrangos versijos negali nuskaityti.** Jutiklis, kurio atsakymas po blykstės užtrunka ilgiau nei numatytas laukimo laikas, nelaikomas sugedusiu: kai tik jis atsako, jis vėl prijungiamas su naujos programinės įrangos nustatymais, o komanda įspėja, kad jis reagavo lėtai.
- **Programinės įrangos atnaujinimui reikalingas įeinantis TCP ryšys.** Jutiklis vėl prisijungia prie šio kompiuterio, kad surinktų vaizdą. Chloros klausosi adapterio, kuris nukreipia į jutiklį (kiekviename adapteryje tik tada, kai negali nustatyti, kuris iš jų), ir aptarnauja tik paties jutiklio adresą; bet koks kitas ryšys uždaromas neaptarnavus. Windows sistemoje diegimo programos užkardos taisyklė „`Chloros Backend (TCP-In)`“ leidžia jam prisijungti (žr. [Windows (.exe)](#windows-exe)); ugniasienė, kuri jį blokuoja, sukelia atnaujinimo nesėkmę su klaida `… device never connected back on TCP <port> …`.
- **Naudodami WSL2, naudokite veidrodinį tinklą** (`networkingMode=mirrored` ir `%UserProfile%\.wslconfig`). Numatytame NAT režime šio kompiuterio adresas jutiklio tinkle yra pats WSL adresas, todėl kiekvienas statinis adresas atmetamas (išskyrus `--force`), tiesioginė transliacija nepasiekia WSL, o programinės įrangos atnaujinimas negali būti užbaigtas, nes jutiklis vėl prisijungia prie kompiuterio, kad gautų atvaizdą.
- **Jutiklio ID ir kompiuterio vardas nesikeičia.** Nepriklausomai nuo jo adreso, „DAQ-E“ vis tiek yra „`daq-e-<id>`“ ir „`daq-e-<id>.local`“, todėl „`--eth-host daq-e-<id>.local`“ jį vis tiek suranda.
- **GUI turi tuos pačius nustatymus**: skirtukas „Šviesos jutikliai“, jutiklio nustatymai (ratukas), „Tinklas“. GUI išlaiko savo ryšį su jutikliu, todėl pakeitus jutiklį, su kuriuo GUI turi atidarytą ryšį, per GUI: perkraunant iš CLI, GUI sesija su juo nutraukiama.
- Atitinkami SDK modeliai yra `DAQSensorSession.device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` ir `update_firmware()`.

---

## `chloros-cli project`

Atidarykite, prisijunkite prie ir valdykite išsaugotą Chloros projektą (aplanką su `cameras.json` + `sensors.json` + `project.json`). Viskas nukreipiama per užkulisinę sistemą, todėl GUI ir CLI rodo identišką aparatinės įrangos būseną.

### Papildomų komandų žinynas

| Papildoma komanda | Paskirtis |
| --- | --- |
| `project open PATH [--relink FOLDER]` | Atspausdina projekto įrenginių sąrašą (kameros, masyvai, jutikliai), nuskaito jį iš failų ir patikrina šaltinio failus (žr. [Perkeltas projektas](#a-moved-project---relink)). Ji neatidaro projekto užkulisiuose: ten atidarytas projektas, jo įrenginiai ir įrašai lieka tokie, kokie yra. Išeina iš `1`, jei nerandama nė vieno projekto šaltinio failo. |
| `project devices PATH [--reconnect]` | Rodo sąrašą arba pakartotinai vykdo aptikimą. |
| `project connect PATH [--cameras-only] [--sensors-only] [--packed {auto,on,off}]` | Prijungia kiekvieną išsaugotą kamerą / masyvą / jutiklį. Kiekvienas masyvas prijungiamas taip, kaip tai daro `lattice array-connect`: iki 600 s, jo veiksmai išvedami į „stderr“ (`[array <id>]    <s> s: <step>`; „stdout“ yra JSON ataskaita), o per tą laiką neprijungtas masyvas atšaukiamas — jo kameros grąžinamos į pradinę būseną — ir apie tai pranešama per `errors`, jos niekada nepaliekamos prijungtos. `--packed` nustato kiekvieno masyvo suspaustą 12 bitų perdavimą (numatyta reikšmė `auto`; projektas nesaugo nustatymų kiekvienam masyvui atskirai; žr. [Suspaustas 12 bitų perdavimas](#packed-12-bit-transport---packed)). Kiekvienas šviesos jutiklis atidaromas užkulisio DAQ pulke jo išsaugotame adrese su išsaugotu integravimo laiku, kadrų vidurkavimu, automatine ekspozicija ir riba: jutiklis, kuriam nepasirinkta riba, gauna „`sunshine_cosine`“, tas, kuris išsaugotas be ribos (`none`), lieka be ribos, o „DAQ-E-S“ niekada netaiko ribos. GUI DAQ skydelis nėra rezervo dalis: pirmiausia uždarykite jutiklį ten. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | Vienkartinis kadrų įrašymas iš pavadintos kameros arba matricos (`--prefix` numatytasis, `capture`). `--orient` (matricos): matrica išsaugo neapdorotus duomenis, kurie išlieka natūralioje formoje — tai reiškia, kad produktai, kuriuos `process` iš jų sukuria, turi būti orientuoti kaip `array-capture --orient`. Numatytasis nustatymas: natūralioje formoje. `project burst` / `stream` ir recepto `capture` / `burst` / `stream` (`orient: true`) turi tą pačią parinktį. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | N kadrų serija iš nurodytos kameros arba masyvo (`-n/--count` numatyta reikšmė 5; `-i/--interval` sekundės tarp kadrų, numatyta reikšmė 0). Matricos serijos pašalina pasikartojančias sinchronizuotas grupes (pasenimo stebėjimo mechanizmas), todėl dalinai cikluojanti matrica negali grąžinti N vieno kadro kopijų; spausdina kiekvienos iteracijos rezultatus. Jei fiksavimas nepavyksta, serija sustabdoma ir išėjama iš `1`, nurodant, kiek kadrų buvo išsaugota iki to momento. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--orient]` | Fiksuoja į diską cikliškai: `--fps F` fiksuoja antrą (pagal numatytąjį nustatymą – 3), po vieną kadrą per įrašymą iš kameros, po vieną sinchronizuotą kadrų grupę iš masyvo (su tuo pačiu senumo stebėjimo mechanizmu kaip ir `project burst`), kol `-n N` įrašo (numatyta reikšmė `0` = kol paspaudžiama „Ctrl+C“, tuomet parodoma, kiek kadrų buvo išsaugota, ir uždaroma programa `0`, arba `1`, jei nebuvo išsaugota nė vieno kadro arba įvyko failo įrašymo klaida; jei prieš `-n N` užfiksuojant paspaudžiama „Ctrl+C“, programa uždaroma `130`). Jei įrašymas nepavyksta, srautas sustabdomas ir uždaroma `1`, nurodant, kiek kadrų buvo išsaugota iki to momento. `--poll-interval` yra pasenęs ir ignoruojamas: jis vis dar priimamas, todėl senesni skriptai veikia, tačiau stderr išvedamas įspėjimas. |
| `project sensor read PATH NAME [--json]` | Naujausias spektro kadras. |
| `project sensor log PATH NAME [--seconds SEC] [-o DIR] [--device-name NAME]` | Įrašyti .daq failą `--seconds` (numatyta reikšmė – 10). Jei įraše nėra jokių matavimų, apie tai pranešama į stderr ir programa uždaroma `1`. |
| `project run PATH RECIPE.yaml` | Vykdyti YAML/JSON duomenų surinkimo receptą. `--dry-run` atlieka patikrinimą nevykdydamas. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Apskaičiuoti masyvo suderinimą — žr. [žemiau pateiktą žymių lentelę](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Išspausdinti dabartinį suderinimo profilį. |
| `project align clear PATH NAME` | Ištrinti išsaugotą profilį. |
| `project align tweak PATH NAME --serial SN [--dx N] [--dy N] [--rotation-deg N] [--scale N]` | Pakeisti vieno pavaldinio transformaciją (numatyta: `0`, `0`, `0`, `1.0`: be pokyčių). |
| `project align export PATH NAME --to FILE` | Išsaugoti profilį JSON formatu. |
| `project align import PATH NAME --from FILE [--no-validate]` | Įkelti išsaugotą profilį. |

#### `project align calibrate` Parinktys

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Suderinimo metodas. **Šios rašybos skiriasi nuo `lattice align-calibrate`**, kuriam naudojamos trumposios formos `orb` / `akaze` / `phase`; šios dvi komandos nėra tarpusavyje keičiamos naudojant šį žymeklį. |
| `--model {translation, rigid, affine, homography}` | `affine` | Transformuoti modelį, kad jis tilptų. |
| `--frames N` | `1` | Sinchronizuoti kadrų momentinius vaizdus su vidurkį. |
| `--reference SN` | pagrindinė | Etaloninės kameros serijos numeris; visi kiti nariai yra deformuojami pagal ją. |
| `--max-features N` | `5000` | ORB požymių skaičiaus riba. |
| `--ratio-threshold F` | `0.75` | Lowe’s santykio testas. |
| `--ransac-threshold-px F` | `3.0` | RANSAC vidinių taškų slenkstis. |
| `--min-matches N` | `15` | **Kokybės riba** — atmesti sprendimą, jei atitinkančių taškų skaičius mažesnis už šį skaičių. |
| `--max-reproj-err-px F` | `4.0` | **Kokybės riba** — atmesti sprendimą, jei viršijama ši RMS reprojekcijos paklaida. |
| `--checkerboard RxC` | — | Plokštės geometrija, skirta `--method checkerboard`, pvz., `9x6`. |
| `--name PROFILE` | tuščia | Profilio pavadinimas, įterptas į išsaugotą JSON failą. **Ne masyvo pavadinimas** — tai yra pozicinis `NAME`. |

Dėl šių dviejų kokybės patikrinimų kalibravimas gali sėkmingai išspręsti problemą, bet vis tiek
atsisakyti išsaugoti: profilis, kuris neatitinka bent vieno iš jų, tyliai neteisingai registruotų kiekvieną
vėlesnį įrašą, todėl jis atmetamas, o ne išsaugomas.

### Perkeliamas projektas (`--relink`)

Projekto `project.json` kiekvienam šaltinio failui — kiekvienam vaizdui ir šviesos jutiklio
įrašui — suteikia pavadinimą pagal jo pilną kelią. Perkėlus projekto aplanką arba visą projektų aplanką
į kitą diską, tie keliai vis dar nurodo senąją vietą.

Atidarius projektą, jis pats ištaiso tai, ką gali: failas, kuris buvo projekto
aplankelyje, ieškomas aplankelyje, kuriame projektas yra dabar, o kiekvienas ten rastas kelias
yra nukreipiamas iš naujo ir išsaugomas (`project.json` pirmiausia yra atsarginė kopija kaip
`project.json.before-relink-<date>-<time>`). Tai vyksta kiekvieną kartą atidarant – programą,
`chloros-cli project open`, „`chloros-cli process`“ į esamą projektą, į SDK ir „`chloros-mcp`“. Kelias peradresuojamas tik į ten esantį failą.

Failai, kurie buvo už projekto aplanko ribų – „Hub“ sesijos metu, importuojant per „Add Folder“ –
tokiu būdu negali būti rasti. Nurodykite aplanko, kuriame jie dabar yra, pavadinimą:

```bash
chloros-cli project open "N:/Chloros Projects/Field_A" --relink "E:/backup/Hub Projects"
```

`--relink` paima aplanką, į kurį buvo perkelti failai, arba bet kurį aukštesnio lygio aplanką: kiekvieno trūkstamo
failo aplankas ieškomas po juo pagal jo galinius aplanko pavadinimus
(`Hub Projects/<session>/raw/<serial>`, tada `<session>/raw/<serial>`, ...), o kiekvieno
aplanko failai paimami iš tos vietos, kurioje jų randama daugiausia. `project open` išspausdina
tai, kas buvo peradresuota, kiek šaltinio failų buvo rasta ir aplankus, kuriuose buvo likusieji
failai. Kai kurie vis dar trūksta: įspėjimas, uždarykite `0` – projektas atsidaro su likusiais. Nė vieno
nerasta: klaida, nurodanti tuos aplankus ir `--relink`, uždarykite `1`; taip pat ir užkulisinė programa
atsisako atidaryti tokį projektą dėl `process` arba `project devices --reconnect` (HTTP 409 `PROJECT_SOURCES_MISSING`).

### Pavyzdžiai

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Its hub captures moved to another drive: point it at them
chloros-cli project open "/home/user/Chloros Projects/Field_A" --relink "/media/backup/Hub Projects"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### Recepto DSL

`project run RECIPE.yaml` priima YAML arba JSON failą, kuriame aprašoma veiksmų seka:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        auto_exposure: "Off"
        gain_db: 6.0
        auto_gain: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

Palaikomi veiksmai: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. `apply` naudoja raktus `cameras.json`, `settings` — `exposure_us`, `auto_exposure`, `gain_db`, `auto_gain`, `target_brightness`, … (senesni pavadinimai `exposure_time`, `exposure_auto`, `gain`, `gain_auto` priimami kaip sinonimai). Tokiu būdu nustatytas ekspozicijos laikas, stiprinimas arba tikslinis ryškumas laikomi jūsų pasirinktais: „Chloros“ automatinis ekspozicijos valdymas (įskaitant DAQ matuojamą ekspoziciją) jų nekeičia. Rankinė ekspozicija palieka stiprinimą tokį, koks buvo: automatiniame režime kamera toliau reguliuoja stiprinimą (visame jo diapazone) siekdama tikslinio ryškumo, kol nenustatysite `gain_db` arba `auto_gain: "Off"`. Vėlesnis „`auto_exposure: "Continuous"`“ vėl perduoda ekspoziciją automatiniam režimui, o „`target_brightness_auto: true`“ – išlaikytą tikslą grąžina „Chloros“ automatiniam tikslui. Veiksmas „`burst`“ reikalauja nustatyti „`name`“ (privaloma), „`count`“ (numatyta reikšmė – 5), `interval` (sekundės, numatyta reikšmė 0), `output`, `format`, ir `settings` (tokie patys kameros nustatymai kaip ir `apply`); masyvo serijos naudoja tą patį naujai sinchronizuotos grupės stebėjimo mechanizmą kaip ir `project burst`.

A `capture`, `burst` arba `stream` žingsnio `output` numatytasis nustatymas yra `output`. Santykinis `output` — įtrauktas kaip numatytasis — nustatomas pagal aplanką, iš kurio paleidžiate `chloros-cli project run`, o ne pagal recepto failo ar projekto aplanką (versijoje 1.2.0 jis buvo perkeltas į užkulisio diegimo aplanką).

Paleiskite jį:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

**Kiekvienas paleidimas pirmiausia patikrinamas pagal projektą.**Kiekvienas veiksmas turi turėti kaip raktą būtent vieną iš aukščiau nurodytų veiksmažodžių — `- capture: {name: FrontLeft}`, o ne `- action: capture` ar `- type: capture` — jei raktas yra `name`, projekte turi būti `cameras.json` / `sensors.json`, o `apply` / `capture` / `burst` / `stream` bei šviesos jutiklį, skirtą `sensor`; o „`stream`“ reikalauja, kad „`count`“ > 0, o „`wait`“ – trukmės. Receptas, kuriame yra bet kuri iš šių klaidų,**neatlieka** jokio veiksmo (`[recipe] error: …`, `[recipe] not run: …`, išėjimas `1`), net ir su `stop_on_error: false`. Raktų, kurių žingsnis nenaudoja, pavyzdys yra `[recipe] warning:`, kaip ir kiekvienas receptas valdomas masyvas ar šviesos jutiklis, kuris šiuo metu nėra prijungtas. Receptas niekada neprijungia įrenginių: pirmiausia paleiskite `chloros-cli project connect PATH`. `--dry-run` išspausdina receptą, tada tas pačias klaidas ir įspėjimus, o `[recipe] valid: …` / `NOT valid: …`, nieko nevykdo ir užsidaro `1`, kai recepte randama klaida.

---

## Aplinkos kintamieji

| Kintamasis | Poveikis |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Perrašo užkulisinio serverio URLą (numatyta reikšmė – `http://127.0.0.1:5000`) — **taikoma tik komandų šeimoms `lattice`, `project` ir `daq pool-*`** (`lattice network-analysis` vietoj to naudoja `--backend-url`). Pagrindinės komandos (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) priskiria `http://127.0.0.1:<port>` ir ignoruoja šį kintamąjį (IPv4 literatas leidžia išvengti Windows `localhost`→`::1` ~2 s užklausos trukdymo), todėl jie visada nukreipiami į vietinį kompiuterį. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | Nustatoma užkulisinės aplinkos parametrų rinkinyje. `1` sumažina atmetimo dėl per didelio užsakymų skaičiaus lygį — masyvo jungtis su `pin_resolution` arba [laikina daugiakamerė komanda](#multi-camera-transient-sessions) (`error_code` `ARRAY_OVERSUBSCRIBED`) — į garsų įspėjimą, leidžiantį GVSP paketų praradimą. Tik bandymams — žr. [Masyvo kadrų per sekundę ir serijos modelis](#array-fps--burst-model). |
| `CHLOROS_ARRAY_PACKED12` | Nustatoma užkulisinės aplinkos parametruose: `on` arba `off` nustato numatytąjį [supakuotą 12 bitų perdavimą](#packed-12-bit-transport---packed) kiekvienam masyvui (numatyta reikšmė – `auto`). Tiksliai nurodytas `--packed` vis tiek turi pirmenybę. |
| `CHLOROS_DISABLE_UPDATE_CHECK` | `1` (arba `true` / `yes`) išjungia Linux CLI [automatinį atnaujinimų patikrinimą](#chloros-cli-update) paleidimo metu. |
| `CHLOROS_CLI_MODE` | Nustato pati CLI; nurodo užkulisiniam moduliui įjungti lygiagretų apdorojimą. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` praleidžia „GVSP“ atsarginį patikrinimą (tik ICMP rezultatai). **Tai išjungia „jumbo“, o ne tik sumažina žurnalo įrašų skaičių** — kamera atsako į DF pingus tik iki 1500 kiekviename kelyje, todėl šis patikrinimas yra vienintelis dalykas, galintis aptikti „jumbo“. Sutaupo ~1 s vienai kamerai vienam prisijungimui; kainuoja ~1,45× laidinio ryšio ribą, jei tinklas *galėtų* perduoti „jumbo“ paketus. SDK įspėja, kai tai nustatote. |
| `CHLOROS_GVSP_SESSION_REUSE` | Nustatoma užkulisinės sistemos aplinkoje: `0` matuoja paketo dydį kiekvieno prisijungimo metu, o ne vieną kartą kiekvienai kamerai per užkulisinės sistemos sesiją. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | Fiksuoja GVSP paketo dydį kaip N baitų; visiškai praleidžia tikrinimą. Geriau rinktis nustatymą pagal komandą (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`), o ne nuolatinį nustatymą: fiksuotas dydis nebesitaiko prie priešais esančio tinklo, o nustatant 9000 maršrute, kuris negali perduoti „jumbo“ paketų, **kiekvienas** duomenų surinkimas baigsis laiko limito pasiekimu naudojant `SC_ERR_TIMEOUT -1011`. |
| `CHLOROS_TRANSIENT_AE_SETTLE_S` | Nustatoma užkulisinės aplinkos parametruose: didžiausias sekundžių skaičius, kurį trunka [laikina daugiakamerė komanda](#multi-camera-transient-sessions) arba kameros, kurios niekas nelaiko, `lattice capture`, gali skirti kiekvienos kameros ekspozicijai nustatyti iš ankstesnės scenos, kol ji išsaugo kadrą (numatyta reikšmė `5`). `0` išjungia šią funkciją: tada kiekvienos kameros automatinė ekspozicija veikia nuo pirmojo kadro, pradedant nuo jos ekspozicijos ribos. |
| `CHLOROS_LOG_DIR` | Nustatoma užkulisinės programos aplinkoje: aplankas, į kurį išsaugomi išsamūs žurnalai. Numatytasis nustatymas: `%LOCALAPPDATA%\Chloros\logs` Windows sistemoje, `~/.cache/chloros/logs` Linux sistemoje (`$XDG_CACHE_HOME/chloros/logs`, kai tai nustatyta). .deb paketo šakninė paslauga nustato `/var/log/chloros`, kurį gali skaityti `adm` grupė — žr. [Backend'o paleidimas kaip paslauga](#running-the-backend-as-a-service-headless). |
| `TMPDIR` (Linux) | Laikinųjų failų saugojimo vieta. CLI automatiškai naudoja `/mnt/ssd/tmp`, jei jis nurodytas. |

---

## Išėjimo kodai

| Kodas | Reikšmė |
| --- | --- |
| `0` | Sėkmė. |
| `1` | Bendro pobūdžio nesėkmė (dauguma pakomandų klaidų). Įrašymo komanda taip pat baigia veikimą su kodu `1`, jei nepavyko įrašyti kurio nors failo, kurį buvo paprašyta įrašyti, net jei kiti failai buvo išsaugoti, ir su kodu `array-burst` / `array-record` išjungia `1`, kai įrašymo programa praneša apie klaidą — žr. [Failai, kurių nepavyko įrašyti](#files-that-fail-to-write). `process` taip pat užsidaro `1`, kai nepavyksta patikrinti vykdymo eksportų arba kai prarandamas vaizdas, nors kiti produktai yra įrašyti. |
| `2` | Argumentų klaida (nežinomas arba trūkstantis žymeklis; taip pat `time-sync set-priority` be jokio prioriteto). |
| `130` | Nutraukta klavišų deriniu „Ctrl+C“: bet kuri komanda, kurią ji nutraukia — `lattice`, `project`, `daq` ir `process` (`process` po to, kai užkulisinė programa sustabdė vykdymą, žr. [`process` pastabas](#pastabos)). Komanda, kurios įprastas sustabdymas yra „Ctrl+C“ — `lattice array-capture --continuous` / `--interval`, `lattice viewer`, `lattice continuous`, `multi-stream`, `align-stream`, `array-record`, `array-burst` ir `project stream` su `-n 0` — užbaigia veikimą pagal tai, ką atliko: `0`, kai viskas buvo įrašyta, `1`, kai kas nors nepavyko. |
| `143` / `129` | Nutrauktas SIGTERM / SIGHUP (128 + signalas) po to, kai buvo atliktas komandos valymas: vykdomas užduotis sustabdytas ir kameros grąžintos į pradinę padėtį, apdorojimo ciklas sustabdytas užkulisiuose. `lattice continuous` arba kitas įrašas, kurio įprastas sustabdymas yra „Ctrl+C“, taip pat užbaigia `143`, o ne `0`. |

`lattice network-analysis` uždaro `1`, jei sprendimai yra `needs_force_slip` ir `error`, o „`selftest`“ uždaro „`1`“, nebent būtų sėkmingai atlikti visi septyni patikrinimai.

---

## Trikčių šalinimo patarimai

- **„Reikia prisijungti“** → Šiame kompiuteryje vieną kartą paleiskite `chloros-cli login EMAIL PASSWORD`.
- **„Projekto aplankas šiam kompiuteriui yra per gilus“** → Šiame kompiuteryje išjungti Windows ilgi keliai (Windows numatytasis nustatymas), todėl joks kelias negali būti ilgesnis nei 260 simbolių, o produktai turi būti ne daugiau kaip apie 130 simbolių žemiau projekto aplanko. Nieko nebuvo apdorota. Pranešime nurodytas ilgiausias tinkamas projekto aplankas: naudokite trumpesnį (`-o` arba `chloros-cli set-project-folder`) arba įjunkite Windows ilgus kelius ir iš naujo paleiskite Chloros. Windows diegimo programa siūlo šią parinktį kaip „Įjungti ilgus Windows kelius“, kuri pagal numatytuosius nustatymus yra pažymėta; kitaip naudokite grupės politiką: *Kompiuterio konfigūracija > Administraciniai šablonai > Sistema > Failų sistema > Įjungti ilgus Win32 kelius*.
- **„backend unreachable“** → Paleiskite Chloros darbalaukio programą (Windows) arba patys paleiskite „backend“: „`"C:\Program Files\Chloros\backend\chloros-backend.exe"`“ Windows sistemoje (jūsų diegimo aplankoje esantis „`backend`“ aplankas; jo nėra `PATH`), `chloros-backend` Linux sistemoje. Arba patikrinkite `CHLOROS_BACKEND_URL`, jei naudojatės nuotoliniu būdu.
- **`lattice` komandos baigiasi klaida „LATTICE kameros tvarkyklės nerastos“** → Užkulisinė programa negalėjo įkelti kartu su ja įdiegtos „LATTICE“ kameros vykdymo aplinkos. Kiekviena `lattice` komanda vykdoma užkulisinėje programoje; pati CLI-sąsaja neturi jokių kameros tvarkyklių. Iš naujo įdiekite Chloros: Windows diegimo programa ir .deb failas įdiegia vykdymo aplinką šalia užkulisinės programos.
- **„Array connect“ / „Array Settings“ rodo pranešimą „FRAMES WILL DROP“ arba „Reduce ROI to enable“** → Pagrindinio kompiuterio tinklo plokštės (NIC) priėmimo žiedas yra per mažas (paprastai po NIC tvarkyklės atnaujinimo jis nustatomas į 32). Žr. [Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](#host-nic-setup--tuning-lattice-arrays) — nustatykite „`ReceiveBufferLen=256`“, „`PendingReceives=64`“.
- **Kompiuteris užstringa paleidžiant iš naujo / išjungiant, tada WMI rodo `Invalid class` / tinklo plokštė neįjungiama / trūksta USB įrenginių** → Pasenusi USB 10GbE adapterio tvarkyklė sukelia `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atnaujinkite adapterio tvarkyklę — žr. [Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](#host-nic-setup--tuning-lattice-arrays).
- **Linux: įrašai priklauso vartotojui „root“, todėl jų negalima ištrinti, o užkulisio žurnalas nėra jūsų `~/.cache`** → Užkulisio programa veikia kaip „root“ per `chloros-backend.service`. Perjunkite į `chloros-backend@<user>.service`, kuriame jis veikia kaip jūs — žr. [Backend'o paleidimas kaip paslauga](#running-the-backend-as-a-service-headless). Iki tol root paslaugos žurnalas yra `/var/log/chloros` (jį gali skaityti `adm` grupė).
- **Linux: užpakalinio modulio vienetas yra `failed` su pranešimu „Jau veikia kitas egzempliorius“ (baigimo kodas 98)** → Kitam užpakaliniam moduliui jau priklauso tas prievadas: vienu metu veikia tik vienas. Jei įjungti du užpakalinio modulio vienetai, palikite vieną: `sudo systemctl disable --now <unit>` skirtas tam, kurio nenaudojate — žr. [Užpakalinio modulio paleidimas kaip paslauga](#running-the-backend-as-a-service-headless). Jei įjungtas tik šis modulis, jį užima už „systemd“ ribų paleistas foninis procesas (per „`chloros-backend`“, „`chloros-cli`“ arba SDK skriptą): sustabdykite tą foninį procesą, tada „`sudo systemctl start <unit>`“.
- **„Jetson“ keitimo įspėjimas** → Pridėkite failu pagrįstą keitimą; CLI išspausdina tikslias komandas `fallocate` / `swapon`.
- **„backend not reachable“ iš `lattice`, `project` arba `daq` komandos** → neveikia joks backend: šios komandos jo niekada nepaleidžia. Paleiskite komandą `chloros-cli backend status` ir žr. [Backend paleidimas](#start-the-backend).
- **„`daq net ...` nėra prieinamas „Chloros“** (Linux ir „Jetson“) → Kai kurie DAQ-E įrenginio nustatymai yra rezervuoti MAPIR paslaugai. Tinklo nustatymai, perkrovimai ir programinės įrangos atnaujinimai atliekami naudojant komandas `pool-set-network`, `pool-reboot` ir `pool-update-firmware`; dėl kitų klausimų kreipkitės į MAPIR pagalbos tarnybą.
- **„DAQ tiesioginėms aparatinės įrangos komandoms reikalingas pilnas „daq“ paketas“** (Windows) → Naudokite `pool-*`, kuris valdo tą patį jutiklį per užkurtį, arba `chloros_sdk.connect_daq_sensor()` iš Python.

---

## Taip pat žr.

- [Python SDK žinynas](sdk-reference.md) — programinis kiekvienos CLI komandos atitikmuo.
- [DAQ šviesos jutikliai](https://mapir.gitbook.io/chloros/daq-light-sensors) Chloros vadove — jutikliai, jų ribos ir kalibruotas diapazonas, įrašymas, DAQ-E tinklas.
- [„LATTICE“ kameros](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) Chloros vadove — prijungimas, kamerų nustatymai, fiksavimo režimai, masyvai.
- Dokumentacija internete: `https://mapir.gitbook.io/chloros/cli`
