# Chloros Python SDK žinynas

**Versija:**

1.3.0**Sukurta:**2026-07-29 19:19 ·**Atnaujinta:** 2026-11-06**Paketas:** `chloros-sdk` (PyPI)**Skirta:** Optimizuota LLM naudojimui; suprantama žmogui.**Apimtis:** Visos viešos klasės, funkcijos ir pagalbinės funkcijos, kurias teikia `import chloros_sdk`, su pavyzdžiais, kuriuos galima nukopijuoti ir įklijuoti, apimantys vaizdų apdorojimą, vienos kameros valdymą, sinchronizuotus masyvus, DAQ jutiklių ir projektų automatizavimą, taip pat `chloros-mcp` serverį, skirtą AI asistentams.

Jei norite susipažinti tik su svarbiausia informacija, pereikite prie:
- [Įdiegimas ir greitasis pradžios vadovas](#installation)
- [„Smart-Connect“ LATTICE kamerų masyvams](#smart-connect-for-lattice-cameras)
- [DAQ jutiklių sesijos](#daq-sensor-sessions)
- [Kamerų pagalbinės programos](#camera-utilities)
- [Laikinieji masyvai, suderinimo profiliai ir indeksai](#transient-arrays-alignment-profiles--indices)
- [Projekto aplankas ir receptai](#project-folder--recipes)
- [Projekto automatizavimas](#project-automation--chlorosproject)
- [„Smart-AE“ / „Smart-Capture“](#smart-ae--smart-capture)
- [Tiesioginiai kadrai jūsų pačių apdorojimo grandinėje](#live-frames-in-your-own-pipeline)
- [MCP serveris (AI asistentai)](#mcp-server-ai-assistants)

> **Pakeitimai versijoje 1.3.0** (ką pastebi esamas 1.2.0 scenarijus).
>
> **Pakeitimai, kuriuos gali tekti apdoroti 1.2.0 scenarijui:**
>
> - **Dabar išmetama išleidimo klaida, kurios užkulisiai nepatvirtina.** `CameraSession.disconnect()`, `ArraySession.disconnect()` / `force_disconnect()` – sesijos atidarytame masyve, `DAQSensorSession.disconnect()` / `force_disconnect()` – ant jos atidaryto jutiklio, o `ChlorosProject.disconnect_all()` sukelia `ChlorosConnectError`, kai užkulisiai atsisako išlaisvinti arba laiku nepatvirtina, nes įrenginys vis dar gali būti užimtas. 1.2.0 versijoje jie grįžo nepastebimai. `with` bloko išėjimas vis dar niekada nesukelia išimties.
> - **Pasiskolintas įrenginys niekada nėra pašalinamas.** „`with attach_array(...)`“ ir aiškus „`disconnect()`“ ant jo palieka masyvą veikiantį (1.2.0 versijoje išeinant iš bloko jis buvo pašalinamas, net jei jį valdė GUI); jei norite jį užbaigti, iškvieskite `force_disconnect()`. Taip pat `connect_daq_sensor()`, kuris aptiko, kad jutiklis jau yra rezervuare (įskaitant „zero-flag“ išmanųjį aptikimą), palieka jį atvirą `disconnect()` ir išeinant iš jo `with` bloko.
> - **PNG ir JPEG priima tik `display` ir indeksinius produktus.** `capture()` su `format="png"` / `"jpg"` (`ext=".png"` / `".jpg"`) ir `radiance` arba `reflectance` lygį, arba `processing="all"`, prieš bet kokį užklausimą sukelia `ValueError`; 12 bitų `raw` / `debayered` atmetamas užkulisio. Žr. [PNG / JPEG](#png--jpeg-take-the-display-and-index-products-only).
> - **`connect_daq_sensor(sensor_id=...)` atidaro tą jutiklį arba sukelia** `ChlorosConnectError`, išvardydamas, ką rado; jis niekada neperjungia į kitą jutiklį. Žr. [Pritvirtintas transportas](#pinned-transport).
> - **`frame_stream()` sukelia išimtį, o ne nutyla:** `FrameFetchError` atmetus prašymą, kitame žingsnyje pakartotų (atspindžio rodmenį, visiškai neįtraukiant DAQ), po 60 s be rėmelio ir po 60 s be naujo trigerio. Žr. [Vienas trigeris vienam skaitymui](#one-trigger-per-read--group_id).
> - **Skaitymo laiko limito pasibaigimas arba nutrūkęs ryšys sukelia `ChlorosConnectError`** iš kiekvieno „smart-connect“ iškvietimo ir `analyze_array_network()`, kur 1.2.0 versijoje buvo sukeliamas pagrindinis laiko limito pasibaigimas arba ryšio klaida.
> - **`connect_array()` pagal numatytuosius nustatymus laukia 600 s (`timeout`, 180 s versijoje 1.2.0), o ryšys, kurio užmezgimo nepavyko, yra atšaukiamas.** Šis iškvietimas siunčia savo „`timeout`“ į užkulisinę sistemą, kuri grąžina kiekvieną kamerą į tą būseną, kurioje ji buvo rasta, ir nieko neprijungia, jei prisijungimas neįvyksta laiku; iškvietimas sukelia „`ChlorosConnectError`“ (HTTP 504 `CLIENT_DEADLINE_PASSED`). 1.2.0 versijoje prisijungimas iškart po kamerų įjungimo galėjo užtrukti ilgiau nei skambutis ir sujungti masyvą po to, kai jis sukėlė išimtį. `connect_all()` kiekvieno išsaugoto masyvo laukia tokiu pačiu būdu (120 s versijoje 1.2.0). Žr. [`connect_array()` parašas](#connect_array-signature).
> - **`connect_array()` išlaiko visą jutiklį.** Jei nenaudojami `width` / `height` / `binning`, kiekviena kamera nustatoma į visą jutiklį, o kadrų dažnis sumažėja iki to, ką užtikrina pagrindinis kompiuteris ir tinklas; 1.2.0 sumažino arba sujungė kadrus, kad išlaikytų kadrų dažnį. Žr. [Sinchronizuotas masyvas](#synchronized-array--arraysession-smart-prep).
> - **„Bayer“ pikselių formatas M3M (mono) kameroje reiškia jos gylį.** M3M siūlo tik „Mono“ formatus, o nustatymų pavadinimai nurodo „Bayer“ formatus: versijoje 1.2.0 kamera atmetė šį formatą ir išlaikė savo, o `settings.pixel_format` pavadino „Bayer“ formatu. `LatticeCamera(preset=...)`, `apply_preset()`, `set_format()`, `CameraSettings(pixel_format=...)`, `connect_camera(preset=...)` ir masyvobendras formatas dabar nustato to paties gylio „Mono“ formatą „M3M“: „`"BayerRG8"`“ ir „`high_speed`“ sudaro „`Mono8`“; „`"BayerRG12"`, `high_quality` ir `triggered` sudaro `Mono12`; `cam.settings.pixel_format` jį pavadina. Tas pats ir atvirkščiai: M3C niekada nėra įrašomas į „Mono“ formatą, taigi `"Mono12"` – iš bet kurio iš šių, `connect_array(pixel_format=...)`, `set_settings(pixel_format=...)` ar masyvo išsaugoto formato – M3C formate duoda `BayerRG12`, nepriklausomai nuo to, kuri kamera yra masyvo pagrindinė. Jūsų perduodamas „`CameraSettings`“ išlaiko savo pavadinimą, todėl tas pats objektas vis dar prašo kiekvienos kameros pateikti tą gylį savo šeimoje. „`connect_camera(preset=...)`“ išlaiko išankstinio nustatymo formatą (versijoje 1.2.0 kamera nustatoma į 12 bitų po išankstinio nustatymo pritaikymo), o kamera, atidaryta pagal išankstinį nustatymą, neberašys to, ką užfiksavo, į „`PRESETS`“. Bet koks kitas formatas, kurio kamera atsisako (kurio nėra jos `PixelFormat` sąraše, pvz., `RGB8` „M3M“ modelyje arba `BayerRG16` programinėje įrangoje, kurioje jo nėra), taip pat nėra pavadinamas kaip atskiras: po `LatticeCamera` prisijungimo, `apply_settings()` arba `set_format()`, `cam.settings.pixel_format` ir `packed_transport` nurodo formatą, kuriuo kamera transliuoja vaizdą, o viena išspausdinta eilutė nurodo formatą, kurio ji atsisakė.
> - **`analyze_array_network()` nurodo, kokie jungties nustatymai taikomi:** `pixel_format=None` (buvo `"BayerRG8"`) reiškia, kad jungtis nustato formatus (Mono12 / BayerRG12), o `packed="auto"` nurodo suspaustą perdavimo būdą, todėl jo nurodomas kadrų skaičius per sekundę (fps) gali skirtis nuo 1.2.0'. `pixel_format`, kurį perduodate, nurodo M3M tokio gylio. Žr. [Tinklo analizės pagalbinę programą](#network-analysis-helper).
> - **`CameraSession.capture()` grąžina `CaptureResult`** (vis dar rėmų žodynų sąrašą) su `.skipped`, `.failed`, `.daq_files` ir `.daq_matches`. `ArraySession.capture()` rezultatas taip pat apima `.alignment` (ar buvo pritaikytas išlyginimo iškraipymas ir kodėl ne) bei `.oriented`.
> - **Masyvų įrašai iš esmės yra suderinami, kaip ir programos.** `ArraySession.capture()`, `continuous()`, `capture_repeated()` ir `ArrayHandle.capture()` / `capture_smart()` iškraipo kiekvieno nario `debayered`, `display`, `radiance`, `reflectance` ir indeksinius failus į pagrindinės kameros kadrą ir apkarpo juos iki narių persidengimo (mažesnį už jutiklį, atsižvelgiant į kamerų nuokrypius, `XMP-mapir:AlignmentJSON` `"applied": true`), kad jie būtų tokie patys failai kaip ir Chloros programėlės užfiksuota ta pati scena; `raw` išlieka originalaus dydžio. Anksčiau, fotografuojant be „`aligned=True`“, kiekvienas failas buvo išsaugomas natūralaus dydžio, neištaisytas. „`aligned=False`“ išlaiko šią savybę. „`chloros-mcp`“`capture_array` ir `start_capture_run` naudoja tą patį numatytąjį nustatymą. Žr. [`ArraySession` metodai](#arraysession-methods).
> - **`connect_all()` nurodo užkurtį, kuris atmeta SDK.** Veikiantis užkurtis, iš kurio atsijungta arba kurio planas yra žemesnis nei „Copper“, pranešamas per `errors` su savo priežastimi (HTTP 401 / 403), o ne kaip „neveikia joks Chloros užkulisinis modulis“, ir kiekvienas „`connect_all()`“ iškvietimas vėl patikrina užkulisinį modulį, todėl jo iškvietimas po prisijungimo veikia tam pačiam projekto objektui.
> - **Santykinis `output_dir` veikia su vietiniu užkulisiu.** Santykiniai keliai prieš siunčiant paverčiami absoliučiais, atsižvelgiant į jūsų skripto darbo katalogą. Versijoje 1.2.0 užkulisys juos iššifruodavo savo įdiegimo aplanke, dėl ko kildavo klaida „Prieiga uždrausta“.
> - **`numpy` dabar yra privalomas** SDK reikalavimas (funkcijos „live-frame“ grąžina „numpy“ masyvus).
> - **Eksporto lygio pavadinimas, kuris nėra „one“, yra atmestas.** `processing=` ir `levels=` priima `raw`, `debayered`, `radiance`, `reflectance` arba `display` (arba aliasą, pvz., `preview`; `processing=` taip pat `"all"`). Bet koks kitas pavadinimas atmetamas prieš paliečiant kamerą (HTTP 400 `UNKNOWN_LEVEL`: `capture()` sukelia `ChlorosConnectError`, `grab()` / `frame_stream()` `FrameFetchError`); 1.2.0 versijoje buvo išsaugotas arba grąžintas `raw`.
> - **„Texture Aware“ funkcija neleidžiama naudoti su „LATTICE“ įrašais.** Projekte, kuriame yra „LATTICE“ užfiksuotų vaizdų, „`configure(debayer="Texture Aware (Slow, Highest Quality)")`“ sukelia „`ChlorosConfigurationError`“, taip pat ir „`process()`“ po „`configure()`“, kuris prieš importavimą paprašė prieš importą. Anksčiau projekte išsaugota „Texture Aware“ reikšmė nustatoma kaip „Standard“, o generuojama klaida `UserWarning`. „LATTICE“ užfiksuoti vaizdai visada apdorojami standartiniu demozėjimo būdu, todėl 1.2.0 versija priėmė šį nustatymą ir jį ignoravo; Chloros programa taip pat nesiūlo šio nustatymo šiems vaizdams. Žr. [Palaikomos reikšmės](#supported-values).
> - **Kameros grįžta į tą būseną, kurioje buvo rastos — išskyrus atvejus, kai naudojamas `keep_settings=True`.** `ArraySession.disconnect()` / `force_disconnect()`, kiekvienos laikinosios funkcijos pabaigoje (`capture_cameras_transient()`, `start_multi_camera_stream()`, `test_array_sync()`, `calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index()`) bei sustabdžius `start_camera_continuous()`, kiekviena kamera grąžinama į pradinę būseną: su išlikusiais trigerio ir sinchronizavimo linijos sujungimais, PTP bei visais sesijos metu pakeistais kameros nustatymais (ekspozicija ir stiprinimas, AE tikslą, ribas ir slopinimą, baltos spalvos balansą, juodos spalvos lygį, pikselių formatą, pikselių sujungimą ir ROI, apverstymus, kadrų dažnį). `keep_settings=True`, priešingai, išsaugo kiekvienos kameros nustatymus pačios kameros atmintyje, kad ji įsijungtų su jais; `clear_settings=True` atkuria gamyklinius tos atminties nustatymus. Bet kuriuo atveju kameros paliekamos veikti savarankiškai. Projektas, prisijungęs prie kamerų, nustato jas pagal savo išsaugotus nustatymus. Žr. [Kameros atmintis](#camera-memory--keep_settings--clear_settings).
> - **Kiekvienas režimas projektą išsaugo tuo pačiu būdu.** Bet koks projekto rėmuose prijungtos kameros, matricos ar šviesos jutiklio pakeitimas — atliktas per SDK, programėlę, CLI ar „MCP“ serverį — išsaugomas to projekto „`cameras.json`“ / `sensors.json`, o atidarius projektą bet kuriame režime, įrenginiai nustatomi pagal jį; `ChlorosProject` pavadina savo aplanką užkulisiniame serveryje. Anksčiau projektą išsaugodavo tik programėlė. Žr. [Vienas projekto įrašas, kiekvienas režimas](#one-project-record-every-mode).
> - **Iš kelių kamerų sukurti failai nurodo kiekvieną iš jų.** Daugiabandis vaizdų rinkinys, iš kelių kamerų juostų sudarytas tiesioginis indeksas ir masyvo fiksavimo `composite/` vaizdas pateikia kiekvienos šaltinio kameros serijos numerį, modelį ir kalibravimo paketą sąrašų pavidalu (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …), o EXIF `SerialNumber` sąraše serijiniai numeriai išvardyti, atskirti kableliais. Anksčiau juose nebuvo nurodoma jokia kamera arba buvo nurodytas tik pirmosios kameros serijinis numeris. Žr. [Iš kelių kamerų sukurti failai](#files-made-from-several-cameras).
> - **Tik `raw` failas turi kameros kalibravimo žymes.** „MicaSense“ suderinamame rinkinyje (`BlackLevel`, `RadiometricCalibration`, atskirikanalas `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*` rinkiniai) bei `CalibrationJSON` duomenų blokas apibūdina jutiklio neapdorotus duomenis, todėl jie yra įtraukti tik į `raw` įrašymo faile. Visi kiti failai — įrašymo`debayered`, `display`, `radiance`, `reflectance` ir indeksų failai, nepertraukiamo įrašymo ir kelių kamerųsrauto kadrai bei kiekvienas `process()` produktas — turi kameros identifikatorių (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...), o užfiksuotame kadre – paties kadro įrašą (`XMP-mapir:FrameJSON`: ekspozicija, stiprinimas, temperatūra, pikselių formatas, užfiksavimo laikas). 1.2.0 versijoje jie taip pat turėjo „raw“ rinkinį, kurį jį taikančios programos („Pix4D“, „Metashape“) pritaikydavo pikseliams, kurie nėra „raw“ reikšmės. Apdorojimo programa nustato tokio failo kalibravimą pagal jo serijos numerį ir `CalBundleSha256`.
>
> **Kiti pakeitimai:**
>
> - **Masyvo automatinis kadrų dažnis nustatomas pagal tai, ką pateikia masyvas.** Dažnis, kuris praranda daugiau nei 1 % savo suaktyvinimų, sumažinamas – tai vertinama remiantis keliais ilgais stebėjimais, o ne vienu, o dažnis, nuo kurio jis buvo sumažintas, kartkartėmis bandomas iš naujo. Jūsų pačių nustatytas dažnis lieka nepakitęs.
> - **Automatinė ekspozicija tapo stabilesnė.** Ji išlaiko pastovų apšvietimą, prisitaiko per kelias sekundes po pokyčių, ignoruoja nedidelius peršvietimus ir atkuria kameros veikimą, kai ji nustoja reaguoti esant ryškiai saulei. Žr. [Kaip veikia automatinė ekspozicija](#how-auto-exposure-behaves).
> - **`import_images(recursive=True)` skaičiuoja tik jūsų nuotraukas ir įrašus.** Aplankas, kuriame yra projekto `camera_cal/`, nebeapima tų kalibravimo failų kaip nuotraukų ir įrašų, o projektas, kuris juos jau buvo įtraukęs, juos pašalina.
> - **`configure(radiometric_output=...)` taikoma.** Dabar ji nustato projekto `Export radiance` / `Export reflectance` produktus; anksčiau kiekvieno projekto savi produktų nustatymai turėjo pirmenybę, todėl „`radiance`“, „`sensor-response`“ ir „`all`“ visi eksportavo spinduliavimą ir atspindį.
> - **`test_array_sync()` paleidžiamas iš kamerų įjungimo būsenos.** Jis įjungia kamerų PTP veikimui, palaukia, kol jų laikrodžiai susinchronizuos ir stabilizuosis prieš pirmąjį suaktyvinimą (`ptp_lock`) — kiekviena kamera susinchronizuojama 120 s, maždaug 2 minutes nuo įjungimo (`ptp_settle_s=` jį nustato), nes ką tik sinchronizuotas laikrodis apie 2 minutes nukrypsta iki ~200 us — ir grąžina kiekvienos kameros PTP į tą būseną, kokią rado. Kameros, kurios vis dar yra sinchronizuotos iš ankstesnio bandymo, nebelaukiama dar kartą. Anksčiau PTP būdavo išjungiamas vieno komutatoriaus tinkle, kuriame neatsakydavo joks DAQ-E, o kiekvienos kameros laisvai veikiantis laikrodis būdavo traktuojamas kaip PTP laikrodis, kuris nesinchronizuojasi („PTP nesusinchronizuotas“, kiekvieno paleidimo metu).
> - **`.daq` įrašas nurodo, ar jo dangtelis buvo pasirinktas, ar numatytas.** DAQ-U, DAQ-M ir DAQ-E tiekiami be dangtelio, o Chloros negali matyti, ar jis uždėtas, todėl jutiklis, prijungtas be pavadinto dangtelio, pagal numatytąją reikšmę gauna `sunshine_cosine` – tai *prielaida*. Kiekviename Chloros įraše (schema 1.30) dabar šalia `cap_id` saugo `cap_id_source`: `operator`, kai pavadinote dangtelį (įskaitant `none`), `auto_default`, jei tai buvo numatyta reikšmė. Apdorojant įrašą, kurio kondensatoriaus pavadinimas buvo numatytas, o projekto DAQ kondensatoriaus nustatymas paliktas „Auto“, išspausdinamas `[daq-dls] WARNING`, nurodantis failo pavadinimą: paprastas „DAQ-U“ su saulės spindulių korekcija rodo maždaug 30 kartų didesnę vertę. **Pavadinkite įdėtą kondensatorių**, kai prijungiate (`connect_daq_sensor(cap_id=...)` arba `DAQSensorSession.set_cap()`, `chloros-cli daq pool-connect --cap-id` / `daq pool-set-cap`, arba GUI kondensatoriaus išskleidžiamajame meniu), arba nustatykite projekto DAQ kondensatorių, ir įspėjimas išnyks.
> - **Windows sistemoje nebebus „pip“ konsolės langų.** Kompiuteryje, kuriame įdiegta Python, užkulisinė programa ir kiekvienas jos vaizdų apdorojimo procesas paleidimo metu vieną kartą paleisdavo „`pip`“, ir kiekvienas atidarydavo konsolės langą. Dabar to nebedaro.
> - **Tiesioginiai „`.daq`“ įrašai išlieka net ir nutraukus elektros tiekimą.**Įrašas rašomas su žurnalu ir visišku disko sinchronizavimu po kiekvieno įrašymo, vykstančio už jutiklio, todėl lėtas diskas vėluoja tik failo įrašymą, bet niekada – matavimų duomenų perdavimą. Įrašas, sustabdytas įrašymo viduryje (dėl elektros tiekimo nutraukimo ar priverstinio uždarymo), gali palikti šalia savęs „`<name>.daq-journal`“:**nukopijuokite juos abu kartu.** Chloros atkuria žurnalą pirmą kartą perskaitant failą, todėl įrašas atidaromas su visais duomenimis, įrašytais iki sustabdymo (prarandama apie paskutines 3 sekundes); versijoje 1.2.0 elektros tiekimo nutraukimas netinkamu momentu galėjo padaryti visą failą neskaitomą („duomenų bazės disko atvaizdas yra netinkamos formos“).
> - **`DAQSensorSession.record_start()` užimtame diske nebeužstrigdo jutiklio**: matavimų duomenys išdėstomi eilėje ir įrašomi atskirame sraute, o tas, kurio nepavyko įrašyti, įtraukiamas į „`status()`“ ir „`record_errors`“ (kartu su „`record_last_error`“).
> - **Tiesioginis `LatticeCamera`:**kadras be ekspozicijos ir stiprinimo dabar praneša apie kameros**tikrąją** ekspoziciją ir stiprinimą (atsarginis variantas skaitydavo iš talpyklos vertę, kuri galėjo likti nepakitusi per visą automatinės ekspozicijos ciklą), o `apply_settings(trigger_source='LineN')` / `set_trigger_source('LineN')` taiko tą patį 5 µs įvesties trikdžių pašalinimą, kurį gauna „Chloros“ kabeliniai pavaldiniai (nefiltruotas, pavaldinys iššaukia kadrus be išsiųsto trigerio).
> - **Programiškai suaktyvinamos kameros nebepraleidžia kas antrojo suaktyvinimo signalo.** Programinio suaktyvinimo režimu veikianti kamera dabar į eilę įtraukia suaktyvinimo signalą, kuris gaunamas jos duomenų nuskaitymo metu (arba Chloros laukia, kol ji bus pasirengusi), kaip tai jau darydavo kabeliniu būdu sinchronizuotos kameros.
> - **`discover_daq_sensors()` pavadina DAQ-E-S `DAQ-E-S`.** Jo `model` buvo `DAQ-E` kiekvienam „Ethernet“ jutikliui; tas, kuris per maždaug 2 s neatsako į tapatybės patikrinimą, vis dar yra sąraše kaip `DAQ-E`.
> - **Nauja: „`chloros-mcp`“ – MCP serveris, skirtas AI asistentams.** „Claude Desktop“, „Claude Code“, „Cursor“ ir kiti MCP klientai gali apdoroti vaizdus (sukurti projektą, importuoti užfiksuotus vaizdus, pasirinkti nustatymus, apdoroti, pateikti ataskaitą apie eksportuotus rezultatus) ir valdyti „LATTICE“ kameras, matuoklių masyvus bei DAQ šviesos jutiklius: prisijungti, peržiūrėti, nustatyti ekspoziciją ir spalvas, užfiksuoti, įrašyti. Šios priemonės atlieka tas pačias funkcijas kaip ir šis SDK. Jos įdiegiamos kartu su SDK, o Linux .deb paketas jas įdiegia į katalogą „`/usr/bin`“. Žr. [MCP serveris (AI asistentai)](#mcp-server-ai-assistants).
> - **Nauja: kameros pagalbinės programos** – komandos „`chloros-cli lattice`“, kurias SDKas iškvietia: spalvų profilis, sodrumas / kontrastas, šviesiausios sritys, baltos spalvos balansas (vienkartinis, automatinis, fiksuoti santykiai), išankstiniai nustatymai, filtrų modeliai, programinės įrangos būsena ir atnaujinimas, maitinimas (patikrinimas, miego režimas, pažadinimas), tikslinis kalibravimas, tinklo adapteriai (patikrinimas, įvertinimas, taisymas), vaizdo kampo korekcija failuose, DLS rodmenys ir pagrindinio kompiuterio patikrinimas. Žr. [Kameros įrankiai](#camera-utilities).
> - **Nauja: likusi CLI dalis kaip SDK iškvietimai**, taigi CLI, SDK ir `chloros-mcp` siūlo tas pačias funkcijas (CLI saugumo sumetimais išlaiko tik prisijungimą, DAQ-E konfigūravimą ir tiesioginius DAQ serverius): kameros atidaromos vienu komandomis (`start_camera_continuous()`, `start_multi_camera_stream()`, `capture_cameras_transient()`, `test_array_sync()`, `detect_array_master()`, `analyze_array_models()`), suderinimo profilio failai (`calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `alignment_profile_info()`, `reorder_alignment_profile()`), indeksai (`index_catalog()`, `compute_index()`, `capture_live_index()`), PTP „grandmaster“ (`time_sync_status()` ir kiti), projekto aplanką (`project_folder()`, `set_project_folder()`, `reset_project_folder()`) bei receptus (`run_project_recipe()`, „CLI“ nuosavas vykdytojas). Šviesos jutikliai: `connect_daq_sensor(cap_id=...)`, `DAQSensorSession.set_cap()`, `daq_bindings()` / `set_daq_bindings()`, `daq_band_presets()` ir `light_sensor_reflectance()` (dviejų jutiklių aplinkos + objekto atspindžio koeficientai ir indeksai); kamerai: `CameraSession.status()` ir `capture_repeated()`; matricos atveju: `ArraySession.capability()` ir `set_member_settings()` (elemento ekspozicija, stiprinimas, ...). Žr. [Laikinieji masyvai, suderinimo profiliai ir indeksai](#transient-arrays-alignment-profiles--indices), [Projekto aplankas ir receptai](#project-folder--recipes) ir [DAQ jutiklių sesijos](#daq-sensor-sessions).
> - **Patobulinti „LATTICE“ indeksų produktai** (NDVI ir kiti indeksai) iš `process()`, visuose apdorojimo režimuose.
> - **„LATTICE“ OCN kameros: patobulinti spinduliavimo, atspindžio ir indeksų produktai esant saulės šviesai.** Po juostų atskyrimo taikoma parkų korekcija — realiuoju laiku, įrašymuose ir `process()`; RGN kameros lieka nepakitusios. Žr. [Radiometriniai rezultatai](#radiometric-output-lattice-multispectral-pipeline).
> - **Kiekvienas įrašas išsaugo naudotą DAQ rodmenį, kaip tai daro GUI.** `CameraSession.capture()` ir `ArraySession.capture()` / `continuous()` įrašo `.daq` ir jo `.match.json`, kai tik DAQ yra susietas arba įtrauktas į grupę, įskaitant ir tik neapdorotus įrašus; 1.2 versijoje0 versijoje neapdorotas arba debayered įrašas buvo išsaugotas tik su `force_daq=True`. `daq=False` jį išjungia, o `daq_tolerance_ms=` nustato atitikimo toleranciją; abu rezultatai nurodo, kas buvo išsaugota `.daq_files` ir `.daq_matches`. Dabar įrašymas laukia kito DAQ rodmens, kad galėtų sujungti rodmenis prieš ir po ekspozicijos. Žr. [DAQ rodmuo, kurį išsaugo įrašymas](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
> - **`ArraySession` turi tiesioginius kadrus ir serverio pusėje vykdomą nepertraukiamą įrašymą.** `grab()` ir `frame_stream()` grąžina „numpy“ kadrus iš vieno trigerio (tas pats skaitymas kaip ir projekto API funkcijose `ArrayHandle.grab()` / `frame_stream()`), o `continuous()` išsaugo kiekvieną sinchronizuotą grupę užkulisiuose ir grąžina [`ContinuousHandle`](#continuoushandle). Žr. [Tiesioginiai kadrai jūsų pačių apdorojimo grandinėje](#live-frames-in-your-own-pipeline).
> - **Masyvas `grab()` yra priskirtas vienam trigeriui** (`ArrayHandle.grab()` ir naujasis `ArraySession.grab()`). 1.2.0 versijoje 12 iš 20 keturiųkameros `ArrayHandle.grab()` skaitymai apėmė du trigerius. Dabar kiekvienas kadras turi `group_id`, o masyvas `frame_stream()` kiekvieną trigerį pateikia vieną kartą. Žr. [Vienas trigeris vienam skaitymui](#one-trigger-per-read--group_id).
> - **Kiekvienos juostos ir suderinti kadrai:** `grab()` / `frame_stream()` perima `band=`, `align=`, `bit_depth=` ir `refl_max=` iš kameros ir matricos identifikatorių. Žr. [Vienos juostos eksportavimas](#exporting-a-single-band--band).
> - **Nauji `ArraySession` metodai ir argumentai:** `set_target_fps()`, `force_disconnect()`, `capture(after_seq=..., orient=...)` (kartu su `CaptureResult.group_seq` ir `.failed`), `continuous()`, `connect_array(packed=...)`; `capture_repeated()` sujungia iš eilės einančias grupes ir sustoja, kai diskas užpildomas.
> - **`ArraySession.record()` įrašo, kai GUI nėra atidaryta.** Uždarius GUI sujungtą vaizdą, užkulisinė sistema pati atkuria vaizdą: masyvoNDVI arba indeksą, kurį perduodate kaip `expr=`. `RecorderHandle.stop()` sukelia `ChlorosConnectError` dėl įrašo, kuris nepavyko, t. y. nebuvo įrašytas nė vienas kadras, ir išlaiko sustabdymo rezultatą `.result` (palikus `with` bloką, išimtis nesukeliama: patikrinkite `rec.result`). Žr. [`RecorderHandle`](#recorderhandle).
> - **Nauji `DAQSensorSession` metodai:** `enable_ptp()`, `force_disconnect()` ir DAQ-E įrenginio nustatymams – `device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` ir `update_firmware()`. `record_stop()` grąžina `warning` ir `gaps`. Perkėlimas, užbaigęs įrašymą, nurodo jo pavadinimą savo atsakyme arba, jei perkėlimas nepavyksta, `ChlorosConnectError` (`recording_closed`; žr. [DAQ-E įrenginio nustatymai](#daq-e-device-settings)). Žr. [`DAQSensorSession` metodai](#daqsensorsession-methods).
> - **Bendrai naudojamas DAQ-E, kuris nustoja perduoti duomenis, yra iš naujo prijungiamas užkulisio modulio**, išlaikant jo ankstesnius nustatymus, o jame vykstantis įrašymas tęsiamas tame pačiame `.daq`, o pertrauka pažymima faile; `record_stop()` grąžina pertraukas kaip `gaps`. Versijoje 1.2.0 jutiklis tylėjo, kol nebuvo atjungtas ir vėl prijungtas, o tai nutraukė įrašymą. Žr. CLI žinyno skyrių [„DAQ-E, kuris nustoja perduoti duomenis“](cli-reference.md#a-daq-e-that-stops-delivering).
> - **`open_project()` veikia naudojant įdiegtą SDK.** „Wheel“ (įdiegimo programair PyPI) dabar turi reikiamus projekto įkėlėjus; anksčiau `open_project()` sukeldavo `ImportError`, nurodydamas `project_cameras` / `project_sensors` bet kuriame per „pip“ įdiegtame SDK. `connect_all()` jungia išsaugotas kameras, masyvus ir šviesos jutiklius per veikiantį užkulisinį (šviesos jutikliai – per jo DAQ rezervą), o išsaugotas įrenginys, kurio jis negali rasti, yra nurodytas po `errors` kartu su priežastimi, o ne tiesiog praleidžiamas be jokio paaiškinimo. Išsaugotas masyvas, kuris jau veikia užkulisinėje sistemoje (GUI, arba `chloros-cli project connect`) yra prijungtas ir paliekamas veikti, o anksčiau tai buvo pranešama kaip klaida („Kamera  <sn>jau </sn>yra <sn>masyve<id>“). Žr. [`ChlorosProject` metodai](#chlorosproject-methods).
> - **`ChlorosLocal()`, `process_folder()` ir `process_lattice_capture()` suranda Chloros Windows sistemoje.** Jos ieško vietos, kurioje diegimo programa įdėjo užkurtį (pagal numatytuosius nustatymus – „`<install folder>\backend\`“, „`C:\Program Files\Chloros\backend\`“, pasirinktoje aplankoje pagal diegimo programos įrašą) ir prisijungia prie jau veikiančio „backend“, jo visai neieškodami. 1.2.0 versijoje jie rodydavo pranešimą „Chloros backend nerastas“ įdiegtoje mašinoje, nebent būtų perduotas `backend_exe=`.
> - **Apie backendą, kuris užsidaro paleidimo metu, pranešama iš karto.** Kai `ChlorosLocal()` arba „smart-connect“ iškvietimas paleidžia backendą, kuris užsidaro prieš atsakydamas, skambutis per kelias sekundes sukelia klaidą „`ChlorosBackendError`“ iš „`ChlorosLocal`“ ir „`ChlorosConnectError`“ iš „smart--connect skambučių, nurodant užbaigimo kodą, komandą, kurią jis vykdė, ir paskutines eilutes, kurias išvedė foninis procesas. 1.2.0 versija laukė 60 s paleidimo laiko ribos pabaigos ir nenurodė priežasties. Žr. [Foninio proceso automatinis paleidimas](#backend-auto-start).
> - **Backendas toliau veikia tarp skriptų.** Išėjus iš „`with ChlorosLocal(...)`“ bloko, `process_folder()` / `process_lattice_capture()` grįžimas ir `chloros-mcp` uždarymas nebeužblokuoja SDK paleisto užkulisinio modulio; kitas scenarijus jį vėl naudoja. Jis sustabdomas `logout()` (nebent Chloros programa yra atidaryta arba vyksta vykdymas), `shutdown_backend()` arba savaime po 30 minučių, jei niekas jo nenaudoja (`CHLOROS_BACKEND_IDLE_MIN` užkulisio aplinkoje tai keičia; `0`: niekada).
> - **SDK-o paleistas užkulisinis procesas yra prašomas užsidaryti, o ne priverstinai nutraukiamas.** `shutdown_backend()` leidžia užpakalinės sistemos procesui atlaisvinti savo kameras ir šviesos jutiklius bei užbaigti bet kokį įrašymą; jis nutraukiamas tik tuo atveju, jei po 10 sekundžių vis dar veikia. 1.2.0 versija jį nutraukdavo iš karto (Windows sistemoje – priverstinis uždarymas), dėl ko prie to užpakalinio modulio prijungtas DAQ šviesos jutiklis galėjo nereaguoti. Užpakalinis modulis, kurį SDK aptiko veikiantį, lieka paliktas ramybėje.
> - **Linux sistemoje automatinis paleidimas niekada nevykdo `backend_server.py` iš dabartinio aplanko.** Jei nebuvo įdiegtas joks backend, skriptas, paleistas aplanke, kuriame buvo toks backend, jį paleisdavo; dabar paleidžiamas tik įdiegtas backend (arba, `ChlorosLocal` atveju, tas, kurį nurodo `backend_exe=`).
> - **`configure(indices=...)`**į projektą įtraukia kiekvieną kameros juostų nustatymą, įtraukia naują projektą ir įspėja (`UserWarning`), jei negali įtraukti pavadinimo.**`import_images()`** praneša apie `.daq` įrašus kaip `scan_count` ir nebeįspėja apie „LATTICE“ įrašų aplanką.
> - **`read_image_audit_tags()` veikia įdiegus per „pip“.** Jis pats nuskaito vaizdo metaduomenis; versijoje 1.2.0 jis grąžindavo `{}` be darbalaukio įdiegimo vidinių komponentų, esančių Python kelyje.
> - **„`capture()`“ iškart po „`connect_array()`“** laukia iki 20 s, kol gaus pirmąją masyvo kadrų grupę, o ne rodo klaidą „HTTP 503: Masyvas vis dar šyla“.
> - **`status()` praneša apie sustojimą.** Naujas laukas „`stalled`“ yra „True“, kol veikia duomenų surinkimo ciklas, tačiau nieko nepaskelbia, o „`actual_fps`“ tada yra lygus 0. Versijoje 1.2.0 „`actual_fps`“ išlaikė paskutinę vertę , todėl sustojusio masyvo reikšmė buvo „`actual_fps > 0`“, o fiksavimas nepavyko dėl 503 klaidos „Nėra naujos sinchronizuotos kadrų grupės“.
> - **Mažiau neišsamių kadrų rinkinių ir 503 klaidų fiksavimo metu:** kiekvienos pavaldžiosios kameros trigerio įvestis dabar yra apsaugota nuo trikdžių.
> - **Suspaustas 12 bitų perdavimas** (`connect_array(packed="auto")`) padidina viso jutiklio masyvo kadrų dažnį maždaug trečdaliu. Žr. [Suspaustas 12 bitų perdavimas](#packed-12-bit-transport-packed).
> - **Matricos ekspozicija pagal ROI:** mažas ROI nebeapriboja elemento ekspozicijos jutiklio laisvojo veikimo laikotarpiu (4,9 ms esant 768 × 304). Žr. [Ekspozicija matricos elementuose](#exposure-on-array-members).
> - **Automatinė ekspozicija nebežemina matricos automatinio kadrų dažnio.** Kabeliu sinchronizuotas masyvas veikia tokiu dažniu, kokį leidžia tinklas ir kamerų duomenų nuskaitymas; automatinė ekspozicija užpildo tą laikotarpį, kurį suteikia tas dažnis, tada padidina stiprinimą. Ilgesnė nei šis laikotarpis rankinė ekspozicija sulėtina suaktyvinimą, kad jis tilptų, o ne būtų atmestas. Žr. [Ekspozicija masyvo elementuose](#exposure-on-array-members).
> - **`read_nodes()`**prideda `frame_values` ir `notes`, kol veikia automatinė ekspozicija (žr. [`read_nodes()` esant automatinei ekspozicijai](#read_nodes-under-auto-exposure--frame_values-and-notes)). Kai kamera atmeta ekspoziciją ar stiprinimą,**`set_settings()`** nurodo priimtiną diapazoną ir vėl įjungia automatinį režimą, kurį buvo išjungęs dėl tos vertės; `set_settings(target_brightness_auto=True)` yra naujas.
> - **`orient=`** dėl `ArraySession.capture()` / `capture_fastest()` / `continuous()` / `burst()` ir `ArrayHandle.capture()` / `capture_smart()` pasuka produktus pagal kameros orientaciją. Žr. [`ArraySession` metodai](#arraysession-methods).
> - **`DLS()` reikalauja pačio jutiklio `mac_address`.** Nėra daugiau numatytojo MAC adreso (jis buvo vieno įrenginio adresas); `DLS()` be jo sukelia `DLSError`. Žr. [DAQ-M juostų dydžius](#daq-m-band-magnitudes--dlsband_magnitudes).
> - **Windows diegimo programa** siūlo parinktis „Įjungti ilgus Windows kelius“ ir „Išskirti Chloros užkurtį iš „Microsoft Defender“ nuskaitymo“; abi parinktys pažymėtos pagal numatytuosius nustatymus. Žr. [1 žingsnį](#step-1--install-the-chloros-platform-package).
> - **Linux .deb** įdiegia Python SDK sistemai `python3` (anksčiau jis tik įrašydavo „wheel“ failą į diską), padidina branduolio lizdo priėmimo buferius, kurių reikia kamerų srautams, ir prideda užkulisinę paslaugą, veikiančią jūsų vartotojo vardu. „Jetson“ paketas yra `chloros_1.3.0_arm64_jp6.deb`, skirtas „JetPack 6“, su „JetPack“ CUDA „PyTorch“. Žr. [1 žingsnį](#step-1--install-the-chloros-platform-package) ir [2 žingsnį](#linux-deb).

---

## Architektūra per 60 sekundžių

SDK yra plonas Python sluoksnis, uždėtas ant Chloros užkulisio (to paties „Flask“ serverio, kurį naudoja darbalaukio GUI ir CLI). Automatizavimui importuokite `chloros_sdk` ir iškvieskite aukšto lygio metodus; iš esmės kiekvienas iškvietimas tampa HTTP užklausimu į vietinį backendą 5000 prievade — `http://127.0.0.1:5000/api/...` (sąmoningai ne „`localhost`“, kuris Windows sistemoje pirmiausia nukreipia į „`::1`“ ir kainuoja ~2 s už kiekvieną užklausą, kai jungiamasi tik prie IPv4 backend). Backend valdo aparatinės įrangos parką — kameras, DAQ jutiklius, suderinimo profilius, kadrų buferius — todėl SDK skriptai gali veikti kartu su GUI, nesivaržydami dėl serijinių prievadų ar tinklo plokštės pralaidumo.

Naudosite tris sąsajas:

1. **`ChlorosLocal` + nemokamos funkcijos** (`process_folder`, `process_lattice_capture`) — Vaizdų apdorojimo grandinė. Vienu Python iškvietimu apdorokite visą aplanką, atlikdami kalibravimą, „debayer“ ir indeksų eksportą.
2. **„Smart-connect“ tvarkyklės** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Atidarykite nuolatinę užkulisinę sesiją realaus laiko aparatūrai. Tas pats „smart-prep“ procesas kaip ir GUI: tinklo patikrinimas, automatinis lygmens pasirinkimas, PTP, AE sėjos nustatymas, GPIO trigerio konfigūracija.
3. **`ChlorosProject` / `open_project`** — Įkelia išsaugotą projektą (aplanką su `cameras.json` + `sensors.json` + `project.json`), iš karto prijungia jo kameras, matricas ir šviesos jutiklius bei valdo įrašymus naudodamas pavadintus identifikatorius.

Paviršiai 1 ir 2 **automatiškai paleidžia vietinį užkulisinį procesą**, jei toks dar neklauso (tas pats komplekte esantis binarinis failas, kurį paleidžia GUI / CLI) — taigi paprastas skriptas veikia iš naujos komandų eilutės, jums nereikia iš anksto paleisti užkulisinio proceso: `ChlorosLocal` (ir `process_folder()` / `process_lattice_capture()`), `connect_camera()`, `connect_array()`, `connect_daq_sensor()`, `discover_lattice_cameras()` ir `discover_daq_sensors()`. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` ir `analyze_array_network()` niekada nepaleidžiami: jiems reikalingas veikiantis užkulisinis procesas. Perduokite `auto_start_backend=False`, jei norite atsisakyti (pvz., kai nurodoma nuotolinė užkulisinė programa, kuri niekada nepaleidžiama). Žr. [Užkulisinės programos automatinis paleidimas](#backend-auto-start). „Surface 3“ elgiasi kitaip: `open_project()` nepriima `auto_start_backend` parametro, o `connect_all()` niekada nepaleidžia užkulisio — jis vieną kartą patikrina „`http://127.0.0.1:5000`“ ir, jei niekas neatsako, grįžta prie tiesioginio (be užkulisio) „`lattice_sdk`“ įrenginio valdymo. Tam reikalinga „Arena“ SDK vykdymo aplinka ir „OpenCV“ (`cv2`), kurį galima importuoti į Python, tačiau SDK „wheel“ jo neįdiegia; be jų kiekviena išsaugota kamera ir masyvas pranešami kaip klaida, nurodant priežastį. Projekto šviesos jutikliai neturi kelio be backend'o: jei backend'as neveikia, kiekvienas išsaugotas jutiklis pranešamas kaip klaida, nurodant priežastį (žr. [`SensorHandle`](#sensorhandle)). Tik `proj.process()` ir `stream(..., overlays=True)` lėtąja inicijavimo tvarka sukuria `ChlorosLocal()` (kuris paleidžiamas automatiškai).

Visi trys yra autentiški-gated: vieną kartą paleiskite `chloros-cli login` kompiuteryje arba prisijunkite per darbalaukio GUI. Ką sukelia iškvietimas be galiojančios sesijos, priklauso nuo sąsajos (žr. [Išimtys](#exceptions)): `ChlorosAuthenticationError` iš `ChlorosLocal` (ir `process_folder()` / `process_lattice_capture()`), `ChlorosConnectError` iš „smart-connect iškvietų ir jų sesijų, `status: "error"` su `error_code` iš `analyze_array_network()`, taip pat įrašas po `errors` `connect_all()` ataskaitoje.

Reikalavimai:
- Python 3.7+ (kaip nurodyta pakete; sukurta ir išbandyta su 3.10 versija)
- Vietoje įdiegta „Chloros Desktop“ programa (užkulisinis binarinis failas pateikiamas diegimo programoje)
- Aktyvi „Chloros+“ paskyra **bet kuriame mokamame „Chloros+“ lygyje („Copper“ ar aukštesniame)**; nemokamame**„Iron“**lygyje nėra prieigos prie SDK/CLI. Tai privaloma**serveriopusėje**: kiekvienas užklausimas su SDK/CLI žymėmis turi turėti tiek aktyvią sesiją, tiek mokamą planą. Planas, žemesnis nei „Copper“, gauna `403` su `error_code: PLAN_UPGRADE_REQUIRED` (`ChlorosLicenseError` iš `ChlorosLocal`, o `ChlorosConnectError` – iš „smart-connect“ iškvietų). Atsijungęs skambintojas vietoj to gauna `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError` iš `ChlorosLocal`, `ChlorosConnectError` iš „Smart-Connect“ skambučių) — šie du kodai skiriasi, nes pakartotinai paleidus `chloros-cli login` išsprendžia `401` problemą, tačiau negali išspręsti `403` problemos, kuriai reikia plano atnaujinimo.
- Naudojimas neprisijungus palaikomas plano atidėjimo laikotarpiu: lygis nuskaitomas iš serverio patvirtinimo talpyklos (5 min.) arba pasirašytos, prie įrenginio priskirtos licencijos talpyklos (30 dienų mėnesiniams planams, iki prenumeratos galiojimo pabaigos metiniams planams). Pasibaigus šiam atidėjimo laikotarpiui, planas tampa nemokamu, o prieiga prie SDK ir CLI sustabdoma, kol kompiuteris bent kartą prisijungs prie serverio. `chloros-cli status` (`GET /api/license-status`) lieka pasiekiama nemokamoje pakopoje, todėl priežastis yra matoma.
- Windows 10/11 (64 bitų), **„Ubuntu“ 22.04 LTS ar naujesnė versija**arba „Jetson“ (JetPack 6). „Ubuntu“ 20.04**nėra** palaikoma: `.deb` priklausomybės kyla iš to, su kuo susietas užpakalinis modulis, įskaitant `libc6 (>= 2.35)`, o „focal“ pateikia „glibc 2.31“.

---

## Įdiegimas

Python SDK yra plonas Python sluoksnis, veikiantis virš Chloros užpakalinės dalies. Jei nevaldo užpakalinės dalies kitoje mašinoje (žr. [Nuotolinio užpakalinės dalies režimas](#remote-backend-mode-pip-only-host-via-tunnel)), jums reikia **vietoje įdiegto Chloros darbalaukio paketo** (Windows diegimo programa arba Linux `.deb`), į kurį įeina DAQ šviesos jutikliai. Pakete yra užkulisio binarinis failas, „Arena SDK“ vykdymo aplinka, skirta „LATTICE“ kameroms, ir kalibravimo rinkiniai.

Naujausi atsisiuntimai: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### 1 žingsnis — Įdiekite Chloros platformos paketą

#### Windows (.exe)

1. Atsisiųskite `Chloros Installer 1.3.0.exe` iš atsisiuntimo puslapio.
2. Paleiskite diegimo programą ir sekite vedlio nurodymus. Numatytasis diegimo kelias yra `C:\Program Files\Chloros\`.
3. Bent kartą paleiskite Chloros ir prisijunkite naudodami savo „Chloros+“ paskyrą.

Diegimo programa leidžia užkulisiniam modulio (`chloros-backend.exe`, nieko kito) per Windows užkardą kiekviename tinklo profilyje: UDP įėjimą, skirtą kamerų srautams, PTP, mDNS ir DAQ-E srautui, bei TCP įėjimą tik iš vietinio potinklio, skirtą DAQ-E ryšiui su kompiuteriu programinės įrangos atnaujinimo metu ([`update_firmware()`](#daq-e-device-settings)). Pašalinimo programa pašalina abi taisykles. Fono programos HTTP API bet kuriuo atveju lieka `127.0.0.1`.

Vedlyno grupėje **Sistemos nustatymai** yra dvi parinktys, kurios abi pagal numatytuosius nustatymus yra pažymėtos:

| Parinktis | Ką daro | Atsisakymas tylaus diegimo |
| --- | --- | --- |
| **Įjungti ilgus Windows kelius** | Leidžia projektų aplankams būti giliau nei „Windows“ 260 simbolių kelio riba. Kai ilgieji keliai išjungti (Windows numatytasis nustatymas), projektų aplankas, esantis per giliai jo produktams, atmetamas dar prieš pradedant apdoroti duomenis — žr. [Trikčių šalinimas](#troubleshooting-pointers). | `/MERGETASKS="!longpaths"` |
| **Išskirti Chloros užkurtį iš „Microsoft Defender“ nuskaitymo** | Sustabdo „Defender“ realausskenavimą kiekvieno failo, kurį įrašo kamera. Esant dideliam įrašymo dažniui, skenavimas atima apie 30 % našumo: išmatuota esant ROI 512 9 kamerų masyve, nepertraukiamas RAW įrašymas veikė 52,7 kadrų/s greičiu su skenavimu ir 77,7 kadrų/s greičiu, kai užkulisiai buvo išjungti. | `/MERGETASKS="!defenderexclusion"` |

Nuolatinis įrašymas praneša apie „Defender“ apkrovą kaip `defender_cores` (žr. [`ContinuousHandle`](#continuoushandle)).

#### Linux amd64 (.deb)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

Įdiekite naudodami „`apt install ./…`“, o ne „`dpkg -i`“: „apt“ taip pat įdiegia Python SDK reikalavimus, kuriuos paketas rekomenduoja, o ne reikalauja (žr. [2 žingsnį](#linux-deb)).

#### Linux arm64 — „Jetson“ (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

Skirta „Jetson Orin“ / „Orin NX“ / „Orin Nano“ su „JetPack 6“ (L4T R36). Jo „PyTorch“ yra „JetPack 6“ CUDA versija, todėl GPU apdorojimas vyksta „Orin“ GPU; jis naudoja „CUDA“ ir „cuDNN“ bibliotekas, kurias įdiegia „JetPack 6“ (`sudo apt install nvidia-jetpack` atvaizde, kuriame jų trūksta).

#### Linux: priėmimo buferiai ir užkulisinė paslauga

Abu .deb paketai padidina branduolio lizdo priėmimo buferius, kurių reikia „LATTICE“ kamerų srautams, iki 32 MB (`/etc/sysctl.d/60-chloros-gige.conf`). Esant branduolio numatytiesiems nustatymams, kameros paketai yra atmesti branduolyje, o masyvas praranda kadrus ir kadrų dažnį; `chloros-cli lattice network` praneša apie buferius, kai jų lygis yra žemas. Žr. CLI žinyno skyrių [Linux kompiuteriai — lizdo priėmimo buferiai](cli-reference.md#linux-hosts--socket-receive-buffers).

Kompiuteryje be grafinės sąsajos paleiskite užkurtį kaip paslaugą **savo vartotojo vardu**, kad skriptas rastų veikiantį užkurtį (`connect_all()` niekada nepaleidžia užkurtio):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
```

Tuomet užkulisinė programa veikia kaip jūs: jos įrašomi duomenys priklauso jums, ir ji naudoja jūsų `chloros-cli login`. „Root“ `chloros-backend.service` vis dar yra įdiegtas kompiuteriuose, kuriuose ši funkcija įjungta, tačiau kiekvienas failas, kurį įrašo „root“ užkulisinė programa, priklauso „root“. Žr. CLI žinyno skyrių [Fono proceso paleidimas kaip paslauga](cli-reference.md#running-the-backend-as-a-service-headless).

Paslaugos paleistas fono procesas yra bendras, jo niekada nestabdo klientas: SDK skriptai ir `chloros-cli` jį naudoja taip, kaip jį randa (`ChlorosLocal.shutdown_backend()` sustabdo tik tą backendą, kurį pats paleido `ChlorosLocal`), o `GET /api/status` pavadina šį modulį `service`. Paleiskite jį iš naujo naudodami „`sudo systemctl restart chloros-backend@$USER.service`“. Atnaujinant paketą, diegimo metu sustabdomos užkulisinės paslaugos, todėl veikiantis scenarijus praranda savo kameras ir jutiklius, o vėliau vėl paleidžia kiekvieną įjungtąjį; pašalinus paketą, jos sustabdomos, o įjungtosios išjungiamos. Vienu metu veikia tik vienas backendas, todėl įjunkite vieną įrenginį: antrasis atsisako paleisti ir lieka `failed` būsenoje (baigimo kodas 98, nepaleidžiamas iš naujo), o jo `systemctl status` nurodo priežastį. Norėdami perjungti vienetus, išjunkite tą, kurio nenaudojate.</id></sn>

### 2 žingsnis — Įdiekite Python SDK

**Chloros diegimo programa pateikia atitinkamą SDK „wheel“ paketą.** Kiekviena Windows diegimo programa ir Linux .deb paketas į diską įrašo failą „`chloros_sdk-X.Y.Z-py3-none-any.whl`“, kuris tiksliai atitinka GUI / CLI / užkulisinės dalies versiją. Jums nereikia ieškoti atnaujinimų „PyPI“, kad viskas būtų sinchronizuota.

#### Windows

Diegimo programa automatiškai paleidžia „`pip install`“ su pridedamu „wheel“ failu, naudodama jūsų sistemos Python (pirmenybė teikiama „`py.exe`“ paleidimo programai, o jei ji neveikia, naudojama „`python -m pip`“). Jokių veiksmų atlikti nereikia — sėkmingai įdiegus, „`import chloros_sdk`“ veiks jūsų Python aplinkoje. Jei kompiuteryje nėra Python, diegimo programa tyliai praleidžia šį žingsnį, o GUI ir CLI toliau veikia.

#### Linuxas (.deb)

Abu .deb paketai (x86_64 ir „Jetson“) įdiegia SDK sistemai „`python3`“, todėl „`import chloros_sdk`“ veikia po įdiegimo, neturint „pip“ ir prisijungimo prie tinklo. Paketai (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` ir projekto įkėlėjai) įtraukiami į `/usr/lib/python3/dist-packages`, o SDK komanda „`chloros-mcp`“ ([MCP serveris](#mcp-server-ai-assistants)) – į „`/usr/bin`“; visi jie priklauso paketui: atnaujinimas juos pakeičia, o pašalinus paketą – jie taip pat pašalinami.

SDK reikalavimai, `python3-numpy` ir `python3-requests`, yra **rekomenduojami, o ne privalomi** (grafinė vartotojo sąsaja, CLI ir užkulisiai yra savarankiški): `sudo apt install ./chloros_<version>_amd64.deb` (`…_arm64_jp6.deb` „Jetson“ įrenginyje) juos įdiegia, o `dpkg -i` – ne. Įdiegimo išvestyje nurodoma SDK versija, kurią importuoja „`python3`“, bet kokie trūkstami reikalavimai, taip pat apie „`chloros_sdk`“, esantį kitur „`python3`“ kelyje, kuris užgožia paketo kopiją (pavyzdžiui, anksčiau įdiegtą naudojant „`pip install --user`“).

„virtualenv“ aplinkoje, kuri nemato sistemos paketų, į ją įdiekite komplekte esantį „wheel“ failą:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

„Air-gapped“ „Jetson“ diegimų atveju tai vyksta visiškai neprisijungus prie interneto — SDK ir „wheel“ failas jau yra diske.

#### Viešasis PyPI

Kompiuteriams, kuriuose veikia tik „pip“ (neįdiegtas Chloros darbalaukio paketas; tokiu atveju SDK valdo užkulisį kitame kompiuteryje, žr. [Nuotolinio užkulisio režimas](#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

PyPI siūlo naujausią stabilią versiją. Užtikrinkite, kad SDK versija atitiktų Chloros užkulisio, kurį jis valdo, versiją (žr. [Versijų numeravimas ir suderinamumas](#versioning--compatibility)). DAQ-M pagalbinei programai `DLS` taip pat reikalinga `bleak`: `pip install "chloros-sdk[camera]"` (arba `pip install bleak`).

#### Patikrinkite

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Reikalinga „Chloros+“ prenumerata.** Visiems SDK iškvietimams reikalingas aktyvus „Chloros+“ prisijungimas. Vieną kartą kiekviename kompiuteryje paleiskite `chloros-cli login user@example.com 'YourPassword'`; prisijungimo duomenys išsaugomi `~/.chloros/`.

### Ar man reikalingas „Desktop Package“?

Vien „pip“ paketo daugumai darbo eigų **nepakanka**. Štai ko reikia kiekvienam SDK sąsajos tipui:

| SDK sąsajos tipas | Reikia „Desktop Package“? | Kodėl |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Taip**(norint paleisti vieną)**/ Ne** (jei užkulisinė programa jau pasiekiama) | Naudoti veikiančią užkulisinę programą (darbalaukio programos arba kito paleisto skripto); kitaip automatiškai paleisti įdiegtąją: `/usr/lib/chloros/chloros-backend` (Linux) arba `<install folder>\backend\chloros-backend.exe` (Windows; pagal numatytuosius nustatymus – `C:\Program Files\Chloros\backend\`, pasirinktinis diegimo aplankas nuskaitomas iš diegimo programos įrašo). Perduokite `backend_exe=`, kad galėtumėte jį pavadinti patys. |
| `chloros-mcp` ([MCP serveris](#mcp-server-ai-assistants)) | **Taip**(paleisti vieną)**/ Ne** (backendas jau pasiekiamas) | Sukurtas remiantis `ChlorosLocal`, taigi toks pat kaip ir eilutėje aukščiau. |
| `read_image_audit_tags` | **Ne** | Skaito apdoroto vaizdo metaduomenis (tik Pythono standartinėje bibliotekoje). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Taip**(vietinis)**/ Ne**(nuotolinis) | Grynieji HTTP-klientai per užkurtį. Vietinis užkurtis → reikalingas darbalaukio paketas. Nuotolinis užkurtis → `backend_url=`**per tunelį** (žr. „Nuotolinio užkurčio režimas“ — pridedami užkurtiai jungiasi tik prie kilpos). |
| `ChlorosProject` / `open_project` | **Taip** | Per backend'ą valdo išsaugotus projektus. |
| Tiesioginės LATTICE klasės (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Taip** | Reikalinga „Arena SDK“ vykdymo aplinka, kuri yra įtraukta į darbalaukio paketą, ir „OpenCV“ (`cv2`) jūsų Python aplinkoje, kurios nei darbalaukio paketas, nei SDK neįdiegia (`pip install opencv-python`). Kitais atvejais `CAMERA_AVAILABLE` importuojant tampa `False`. `DLS` taip pat reikalauja `bleak`. |

### Nuotolinio užpakalinio modulio režimas (tik „pip“ kompiuteris, per tunelį)

> **Įdiegtas užpakalinis modulis nėra pasiekiamas per LAN.** Jis klausosi tik
> kilpos prievado (`127.0.0.1` / `::1`), todėl `backend_url="http://<lan-ip>:5000"`
> **negali pasiekti įdiegtos Chloros**. Norėdami valdyti backendą kitame
> kompiuteryje, patys persiųskite jo kilpos prievadą ir nukreipkite SDK į
> tunelį:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"     # the tunnel endpoint
serials = ["213800234", "214000533"]   # master first

# auto_start_backend=False: the tunnel URL looks local, so without it a tunnel
# that is down would start this machine's own backend, if one is installed.
arr = chloros_sdk.connect_array(serials, backend_url=BACKEND,
                                auto_start_backend=False)
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                     backend_url=BACKEND,
                                     auto_start_backend=False)

# The backend opens every path on ITS machine: pass absolute paths valid there.
# (A relative path would be made absolute against this machine's working folder.)
arr.capture("/home/chloros/captures/run1", processing="raw")
```

„Headless“ / CI / robotikos kompiuteriai gali turėti vieną kompiuterį su pilna darbalaukio instaliacija kaip „Chloros“ serverį, o visur kitur – `pip install chloros-sdk`, tačiau perdavimas tarp jų vyksta per aukščiau nurodytą vartotojo sukonfigūruotą tunelį, o ne tiesioginiu LAN URL adresu.

> **`ChlorosLocal` kompiuteryje, kuriame veikia tik „pip“.** `ChlorosLocal(backend_url=BACKEND)` prisijungia prie užpakalinės dalies, kurią gali pasiekti be darbalaukio įdiegimo: jis ieško vietinio užpakalinės dalies binarinio failo tik tada, kai turi jį paleisti (niekas neatsako ir `auto_start_backend=True`). Iki versijos 1.2.0 jis pirmiausia ieškodavo binarinio failo ir išmesdavo klaidą `ChlorosBackendError` („Chloros“ užpakalinis serveris nerastas…“) kompiuteryje, kuriame veikia tik „pip“, net jei užpakalinis serveris buvo pasiekiamas. Aplankai, kuriuos nurodote (`process_folder`, `import_images`), yra tikrinami šiame kompiuteryje ir skaitomi užpakalinės sistemos, todėl naudojant nuotolinę užpakalinę sistemą jų pavadinimai abiejuose kompiuteriuose turi sutapti, pavyzdžiui, bendrinamame diske.

### Tik DAQ darbo eiga (tik „pip“ kompiuteris)

Vien „`pip install chloros-sdk`“ negali valdyti DAQ jutiklio: DAQ jutiklis visada valdomas per Chloros backendą su „`connect_daq_sensor()`“:

- **Jutiklis šiame kompiuteryje:** čia įdiekite Chloros darbalaukio paketą. „`connect_daq_sensor()`“ paleidžia savo backendą, jei jis dar neveikia.
- **Jutiklis kitame kompiuteryje:** tam kompiuteriui reikalingas darbalaukio paketas; prisijunkite prie jo užkulisio per tunelį [Nuotolinio užkulisio režimu](#remote-backend-mode-pip-only-host-via-tunnel). Užkulisys atidaro jutiklį savo kompiuteryje, todėl DAQ-U turi būti prijungtas prie to kompiuterio, DAQ-M turi būti jo „Bluetooth“ veikimo atstumu, o DAQ-E turi būti tinkle, kurį jis gali pasiekti.

```python
import chloros_sdk

# http://127.0.0.1:5000 is the local desktop install's backend, or the tunnel.
with chloros_sdk.connect_daq_sensor() as daq:   # smart-detect USB / BLE / ETH
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Bet kuriuo atveju užpakalinė sistema taiko „Chloros+“ planą: prisijunkite prie kompiuterio, kuriame veikia užpakalinė sistema (`chloros-cli login` arba darbalaukio programa), naudodami bet kurį mokamą planą („Copper“ ar aukštesnį). Atsijungus nuo užkulisio arba naudojant planą, žemesnį nei „Copper“, abiem atvejais generuojamas `ChlorosConnectError`, kuriame nurodoma užkulisio priežastis (`AUTH_REQUIRED` / `PLAN_UPGRADE_REQUIRED`).

---

## Greitasis pradžios vadovas

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## Aukščiausio lygio API rodyklė

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by CameraSession.capture / ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job
chloros_sdk.ContinuousHandle                      # handle for an array continuous() capture
chloros_sdk.GrabResult                            # dict returned by grab() / yielded by frame_stream()
chloros_sdk.FrameFetchError                       # raised by grab()/frame_stream() on a refusal

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()
chloros_sdk.daq_bindings() / set_daq_bindings(cameras=, arrays=)   # which DAQ feeds which camera
chloros_sdk.daq_band_presets(filter_code=None)    # band sets for Ambient + Object reflectance
chloros_sdk.light_sensor_reflectance(ambient_id, object_id, ...)   # two DAQs -> reflectance + indices
chloros_sdk.time_sync_status() / time_sync_peers() / time_sync_cameras()   # PTP grandmaster
chloros_sdk.restart_time_sync() / set_time_sync_priority(priority1=, priority2=)

# === Camera utilities (the CLI's lattice commands; by serial) ===
chloros_sdk.camera_color_profile(serial) / set_camera_color_profile(serial, ...)
chloros_sdk.camera_color_adjust(serial) / set_camera_color_adjust(serial, ...)
chloros_sdk.camera_highlights(serial) / set_camera_highlights(serial, value)
chloros_sdk.camera_white_balance_once(serial) / set_camera_white_balance(serial, ...)
chloros_sdk.camera_presets() / apply_camera_preset(serial, preset)
chloros_sdk.camera_filter_models() / camera_filter_model_info(name)
chloros_sdk.camera_firmware_status() / update_camera_firmware(serial)
chloros_sdk.camera_power_probe(serial) / sleep_camera(serial) / wake_camera(serial)
chloros_sdk.calibrate_camera_with_target(serial, ...)
chloros_sdk.network_adapters(...) / fix_network_settings()
chloros_sdk.apply_vignette_correction(input_dir, output_dir, ...)
chloros_sdk.read_dls(mac, ...) / probe_system(...) / system_info()

# === Transient cameras, alignment profiles, indices (cameras opened for one command) ===
chloros_sdk.start_camera_continuous(serial, ..., daq=None, daq_tolerance_ms=None)  # → CliJob; a DAQ bound to the camera records one .daq per frame (daq=False: none)
chloros_sdk.start_multi_camera_stream(serials, ...)   # → CliJob
chloros_sdk.capture_cameras_transient(serials, ...)
chloros_sdk.test_array_sync(serials, count, ptp_settle_s=None) / detect_array_master(line) / analyze_array_models(models)
chloros_sdk.calibrate_alignment_profile(serials, save_path, ...)
chloros_sdk.capture_aligned_multiband(profile_path, ...) / start_aligned_stream(profile_path, ...)  # → dict / CliJob
chloros_sdk.alignment_profile_info(path) / reorder_alignment_profile(path, ...)
chloros_sdk.index_catalog() / compute_index(input_path, ...) / capture_live_index(profile_path, ...)
chloros_sdk.CliJob                                # stats() / stop() / wait()

# === Project lifecycle ===
chloros_sdk.open_project(path, relink=None)       # → ChlorosProject
chloros_sdk.project_folder() / set_project_folder(path) / reset_project_folder()
chloros_sdk.run_project_recipe(recipe, project_path)  # `chloros-cli project run`
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics               # DLS: DAQ-M over Bluetooth; needs bleak

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosProjectFilesMissingError      # a ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True when chloros_sdk.project imported; open_project()
                                 # still raises ImportError without the project loaders
```

---

## Vaizdų apdorojimas — `ChlorosLocal`

Pagrindinė procesų grandinės klasė. Sukuriant klasę paleidžia užkulisinį procesą (jei nė vienas neatsako ir `auto_start_backend=True`), sukuria / konfigūruoja projektus, stebi vykdymo eigą, grąžina vykdymo pabaigos ataskaitas.

### Konstruktorius

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

### Metodai

| Metodas | Aprašymas |
| --- | --- |
| `create_project(project_name, camera=None)` | Sukuria naują projektą (pasirinkus galima naudoti kameros šabloną, pvz., `"Survey3N_RGN"`). |
| `open_project(project_path, relink=None)` | Atidaro išsaugotą projektą (jo aplanką arba jo pavadinimą projektų aplanke) ir nustato jį kaip atidarytą projektą. Perkelto projekto keliai nukreipiami į aplanką, kuriame jis dabar yra; `relink` nurodo aplanką, kuriame dabar yra kiti trūkstami failai. Grąžina užkulisio atsakymą su `source_files`; sukelia išimtį `ChlorosProjectFilesMissingError`, jei nieko nerandama. Žr. **Perkeltas projektas** žemiau. |
| `import_images(folder_path, recursive=False)` | Importuoja RAW/TIF/JPG/DNG vaizdus **ir `.daq` šviesos jutiklio įrašus**. Grąžina `count` (vaizdus) ir `scan_count` (įrašus). Įspėja tik tuo atveju, jei aplanke nėra nei vienų, nei kitų. |
| `export_light_sensor(daq=True, csv=True)` | Įrašo kalibruotus `.daq` + `.csv` duomenis už kiekvieną projekto šviesos jutiklio įrašą, į `<project>/Light Sensor/`. Žr. [Šviesos jutiklio įrašai](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | Nustatyti apdorojimo slankiklius. `indices` yra iš anksto nustatyti pavadinimai (`NDVI`, `GNDVI`, `OSAVI`, …), kiekvienas iš jų pridėtas prie projekto pagal jo kameros juostas; pavadinimas, kuris nėra iš anksto nustatytas arba kurio juostos negali sukurti (`NDRE` reikalauja RE ir NIR, kurių nė viena kamera nepateikia), gauna pavadinimą „`UserWarning`“ ir neturi indeksinio vaizdo. „`daq_log_path`“ pavadinimas reiškia šviesos jutiklio įrašą (failas „`.daq`“ arba „`.csv`“, arba jų aplankas), su kuriuo lyginamas kiekvienas kadras tiek „LATTICE“, tiek Survey3 sistemose, vietoj įrašų, importuotų kartu su vaizdais; jei nurodytas kelias trūksta arba jame nėra įrašo, naudojami importuoti įrašai. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Vykdyti apdorojimo grandinę. Grąžina `{"status": "complete", "async": False}`, taip pat raktą „`summary`“, jei jį pateikia užkulisinė sistema — žr. [Vykdymo pabaigos santrauką ir patarimus](#post-run-summary--hints) — `export_check_failure`, kai nepavyko patikrinti vykdymo eksportų, ir `image_failure`, kai prarastas vaizdas. `wait=False` grąžina `{"status": "started", "async": True}` iš karto. |
| `get_config()` / `get_status()` / `status()` | Patikrinkite užpakalinės sistemos būseną. |
| `logout()` | Atsijungti: išvalo talpyklos duomenis ir atlaisvina šio kompiuterio įrenginio lizdą (rezultate – `device_deregistration`). Vykstantis apdorojimo ciklas nėra sustabdomas: jis baigiamas tuo pačiu režimu, kuriuo buvo pradėtas (rezultate – `run_in_progress: true` ir `run_mode`), o kitas ciklas vykdomas standartiniu apdorojimo būdu. |
| `shutdown_backend()` | Sustabdo užkulisinį procesą, jei jį paleido šis egzempliorius; rasta veikianti užkulisinė programa paliekama veikti. `with` bloko palikimas arba egzemplioriaus išvalymas iš atminties jo nesustabdo: fonas toliau veikia kitam scenarijui ir sustabdo save po 30 minučių, jei niekas jo nenaudoja. Fonas yra paprašomas išsijungti, todėl jis pirmiausia atlaisvina savo kameras ir šviesos jutiklius bei užbaigia bet kokį įrašymą; jis nutraukiamas tik tuo atveju, jei po 10 sekundžių vis dar veikia. |
| `discover_cameras()` | Aptikti „LATTICE“ kameras **per šio egzemplioriaus užkurtį** (`/api/camera/discover`). Grąžina žodynų sąrašą (`serial`, `model`, `ip`, …) — tokio paties formato, kokį mato GUI ir CLI. Tuščias sąrašas, jei nerasta jokių kamerų arba neįmanoma prisijungti prie užkulisio. |
| `camera_capture(output_dir, format="tiff", **settings)` | Užfiksuoja vieną kadrą**per užkurtį**(automatiškai paleidžiamą šiuo identifikatoriumi), kad jis būtų paruoštas taip pat kaip GUI ar CLI (numatyta 12 bitų, pakartotinis rezervo naudojimas, įterpti kalibravimo metaduomenys). Nustatykite tikslą naudodami `serial=` arba `device_index=`; perduokite `exposure`/`gain`/`pixel_format`/`preset` kaip `**settings`. Grąžina senosios versijos metaduomenų žodyną (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Generuoja perklotais vaizdais sudarytus peržiūros kadrus iš sujungtų kamerų — plonas MJPEG klientas per užkulisio `/api/camera/<serial>/stream-annotated` maršrutą (zebra / tinklelis / kryžminis taškas / histograma / pikavimas / taškas, nubrėžtas serverio pusėje). `decode=True` generuoja BGR masyvus ir reikalauja OpenCV (`cv2`), kurio SDK-as neįdiegia; `False` generuoja neapdorotus JPEG baitus. Taip pat pasiekiamas kiekvienam projektui kaip `ChlorosProject.stream(overlays=True)`. |

**Vykstantis apdorojimas išlaiko savo projektą.** Kol vyksta apdorojimo procesas — šio skripto, programėlės, `chloros-cli process` arba `chloros-mcp` — užkulisiai atsisako kurti, atidaryti, importuoti į projektą arba keisti jo apdorojimo nustatymus (HTTP 409 `PROCESSING_IN_PROGRESS`): `create_project()` ir `import_images()` sukelia `ChlorosProcessingError`, o `configure()` sukelia `ChlorosConfigurationError`, o `process()`, kurio režimas skiriasi nuo vykdomojo, sukelia `ChlorosProcessingError` (vykdymas išlaiko tą režimą, kuriuo buvo pradėtas), kiekvienas su užkulisio pranešimu. Į projektą išsaugotas įrašas prisijungia prie jo, kai vykdymas baigiasi (tai nurodo jo atsakymo `project_add`). Palaukite, kol vykdymas baigsis (`get_status()`), tada tęskite.**Perkeltas projektas.** Projekto `project.json` kiekvienam šaltinio failui (kiekvienam vaizdui ir šviesos jutiklio įrašui) suteikia pavadinimą pagal jo pilną kelią, todėl perkėlus projekto aplanką – arba visą „projects“ aplanką į kitą diską – jie ir toliau nurodo senąją vietą. Kiekvienas atidarymas pats ištaiso, ką gali: failas, kuris buvo projekto aplanke, ieškomas tame aplanke, kuriame projektas yra dabar, o kiekvienas ten rastas kelias peradresuojamas ir išsaugomas (`project.json` pirmiausia yra atsarginai kopijuojamas kaip `project.json.before-relink-<date>-<time>`). Kelias visada nukreipiamas tik į ten esantį failą. Failai, kurie buvo už projekto aplanko ribų – „Hub“ sesija, importas per „Add Folder“ – randami tik su `relink=<the folder they are in now>` (arba bet kuriuo aukštesniu aplanku). `open_project()` grąžina `source_files`: `total`, `found`, `missing`, `missing_roots` (aplankai, kuriuose buvo trūkstami failai), `missing_outside_project` ir `relink` (kas buvo peradresuota, iš kur į kur). Projektas, kuriame trūksta kai kurių failų, atidaromas su likusiais; projektas, kuriame nerasta nė vieno failo, neatidaromas, o `open_project()` sukelia klaidą `ChlorosProjectFilesMissingError` (`.missing_roots`, `.audit`; HTTP 409 `PROJECT_SOURCES_MISSING`). CLI klaidos `project open --relink` ir `chloros-mcp``open_project` (`relink_folder`) atlieka tą patį.

Naudokite kaip konteksto tvarkyklę, kad būtų užtikrintas išvalymas:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

### Šviesos jutiklio įrašai — kalibruoti `.daq` + `.csv`

DAQ-U / DAQ-M / DAQ-E duomenis galima įrašyti **be** jų kalibravimo rinkinio. Būtent
tai viešai prieinami [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
įrašymo įrenginiai (`record_daq.py`) daro pagal numatytuosius nustatymus: jie įrašo neapdorotus jutiklių rodmenis ir pažymi
failą taip, kad Chloros gautų to jutiklio gamyklinį kalibravimą **pagal serijos numerį** — pirmiausia iš vietinės talpyklos, tada iš MAPIR debesies — ir pritaiko jį importavimo metu.

Chloros rezultatą išrašo atgal kaip du produktus vienam įrašui, pavadinimu
`<project>/Light Sensor/`:

| Produktas | Kas tai yra |
| --- | --- |
| `<name>_calibrated.daq` | Pakartotinai apdorojamas archyvas — ta pati schema kaip ir tiesioginiame įraše, dabar nurodant paketą, kuris jį sukūrė. Pakartotinai importuojant jis **nėra** kalibruojamas antrą kartą. |
| `<name>_calibrated.csv` | Spektrinis spinduliavimo intensyvumas W/m²/nm vienetais pagal paties jutiklio bangų ilgių tinklelį, po vieną eilutę kiekvienam matavimui, taip pat fotometrinės kolonos (bendroji galia, fotopinis/skotopinis liuksas, PPFD ir jo pasiskirstymas pagal mėlyną, žalią bei raudoną spalvas, didžiausias bangos ilgis). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Tik jutikliai be paketų (DAQ-A).** Neapdoroti spektriniai jutiklio skaičiai — *ne* spinduliavimo intensyvumas. Žr. toliau. |

`<name>` yra paties įrašo failo pavadinimas. Du įrašai skirtinguose aplankuose gali turėti bendrą
vieną — kiekvieno kadro rodmenis (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`), išsaugotus
tą pačią milisekundę dviejų kamerų — ir kiekvienas vis tiek gauna savo produktus: pirmasis išlaiko
`<name>`, o antrasis **po** pavadinimo prideda aplankus, kurie jį išskiria
(`<name>__<capture>_daq_calibrated.daq`), taigi įrašo data ir laikas vis dar nurodomi pirmiausia.

`process()` atlieka šį eksportą kaip vieną iš savo etapų. Jam **nereikia** vaizdų:
atskirai skraidantis šviesos jutiklis yra puikus darbo srautas, o toks projektas pagal savo struktūrą neturi
jokių vaizdų.

**DAQ-A įrašai eksportuojami kaip neapdoroti skaičiai.** DAQ-A šeima yra senesnė už serijinių
rinkinių sistemą ir neturi jokio rinkinio, kurį reikėtų atsisiųsti — vietoj to ji kalibruojama lauke pagal
atspindžio taikinį, todėl jos niekada nereikėjo. Šie įrašai eksportuojami
su `_raw` šaknimi, o ne su `_calibrated`: naudojamas kitoks failo pavadinimas, o ne žymė
failo viduje, nes pavadinimas turi išlikti nepakitęs, kai failas siunčiamas el. paštu kaip paprastas pavadinimas.
`.csv` antraštėje nurodyta „`raw spectral sensor counts (NOT irradiance)`“ ir įspėjama, kad
reikšmės yra palyginamos **tame pačiame** faile — būtent tai, kam naudojamas etaloninis kalibravimas
— o ne tarp skirtingų jutiklių. Nuo galios priklausančios fotometrinės stulpelės (bendroji galia,
fotopinis/skotopinis liuksas, PPFD) grąžinamos kaip **NULL**, o ne integruojamos iš skaičiavimų.

DAQ-U / DAQ-M / DAQ-E, kurių paketo tiesiog nepavyko atsisiųsti, vis tiek yra **praleidžiami**,
o ne įrašomi neapdoroti: tokiu atveju paketas egzistuoja ir „prisijunkite iš naujo ir apdorokite dar kartą“ yra tikras patarimas.

Senosios **v1.01 / v1.02** įrašai (juos rašo DAQ-A-SD) neturi kiekvieno skaitymo epochos,
tik failo įrašymo laiką. Vaizdo ↔ žemyn nukreipto srauto atitikimo programa vis dar juos atmeta — atitikimas
kadrą su įrašymo laiku būtų nematoma klaida — tačiau eksportuotojas juos nuskaito, o
failas CSV išspausdina `clock=daq_created_on`, todėl produktas nurodo, pagal kokį laikrodį jis veikia.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

Įrašas, kurio kalibravimo rinkinio negalima atsisiųsti (neprisijungus prie interneto arba jei jutiklio
kalibravimo duomenų faile nėra), yra nurodytas kaip „`skipped`“ **su priežastimi**. Jis niekada
nėra išrašomas kaip „kalibruotas“ failas, kuriame saugomi neapdoroti skaičiai — prisijunkite prie interneto ir
vykdykite dar kartą, tada eksportavimas bus užbaigtas.

### Pažangos atgaliniai skambučiai

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Apibendrinimas ir patarimai po vykdymo

Užbaigus operaciją, `process()` atsisiunčia `GET /api/processing-summary` ir prideda jo turinį kaip `result["summary"]`. Atsisiuntimas vykdomas pagal geriausius pastangų principą ir niekada neblokuoja sėkmingo grįžimo — jei santrauka nėra prieinama, `process()` grįžta prie paprastos `{"status": "complete", "async": False}` formos. Kiekvienas įrašas `summary["hints"]` — pilni sakiniai su siūlomais pataisymais, pvz., kodėl vykdymo rezultatas buvo nulinis — taip pat iš naujo pateikiamas kaip Python `UserWarning`, todėl vykdymai su nuliniais rezultatais yra savaime diagnozuojami, net jei niekada nepatikrinate žodyno:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` yra kompiuteriui suprantama pusė:

| Raktas | Ką skaičiuoja |
| --- | --- |
| `models` | Vykdymo metu nustatytos kamerų grupės. |
| `images_in_groups` | Šių grupių šaltiniai. |
| `targets_found` | Aptikti atspindžio taikiniai. |
| `images_calibrated` | Vaizdai, kuriems vykdymo metu buvo sukurtas atspindžio produktas. |
| `images_failed` | Vaizdai, kurių apdorojimas nepavyko ir kurie nebuvo eksportuoti. |
| `exported_files` | **Vaizdo produktų failai , kuriuos sukūrė vykdymo ciklas**: kiekvienas produktas (debayeringas, peržiūra, spinduliavimas, atspindžio koeficientas, indeksas, tikslinių objektų atvaizdai) bet kokiu formatu. |
| `reflectance_files` | Tarp jų esantys atspindžio koeficiento produktai (`Reflectance_Calibrated_Images`), nepriklausomai nuo to, ar juos kalibravo tikslinis objektas, ar šviesos jutiklio (DAQ) spinduliavimo intensyvumas. Jei vykdymo metu nebuvo tikslinio objekto, bet vis tiek buvo užrašyti atspindžio duomenys, tai nurodoma užuominoje; tik tuo atveju, kai nebuvo užrašyta nieko, pranešama, kad trūksta atspindžio išvesties duomenų. |
| `clamp_masks_written` | Šalia jų užrašytos [nesumaišymo apribojimo kaukės](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) užrašytos šalia jų — tai papildomi duomenys, o ne galutiniai rezultatai, todėl jų nėra `exported_files` (CLI taip pat juos skaičiuoja atskirai). |
| `captures_left_out` | Neapdoroti kadrai projekto `captures` aplanke, kurių šis vykdymas neužfiksavo. Vykdymo pradžioje laukiama, kol bus gauti anksčiau užfiksuoti duomenys, todėl šis skaičius yra 0, nebent vykdymo metu buvo išsaugotas užfiksuotas vaizdas arba pasibaigė laukimo laikas. `summary["captures_left_out_note"]` nurodo, ką su jais daryti. |
| `daq_recordings_exported` / `daq_recordings_skipped` | Šviesos jutiklio įrašai, sąmoningai skaičiuojami atskirai — jie yra iš kito etapo ir egzistuoja vykdymuose, kuriuose nėra jokių vaizdų, todėl juos įtraukus, atrodytų, kad vykdymas, skirtas tik duomenų surinkimui (DAQ), eksportavo vaizdus. |

Kartu su jais: `summary["output_dirs"]` (kiekvienas katalogas, į kurį buvo įrašyta),
`summary["light_sensor_export"]`, `summary["stopped"]` (yra „true“, kai vartotojas nutraukė
vykdymą, todėl daliniai skaičiai nebūtų traktuojami kaip užbaigtas bandymas, kurio metu buvo gauta per mažai duomenų), ir
`summary["groups"]` (paskirstymas pagal grupes).

`exported_files` yra įrašomas konvejerio **rašymo metu**, o ne vėliau nuskaitomas iš
projekto vaizdo objektų. Lygiagretaus apdorojimo ir GPU strategijos kuria savo vaizdo
objektus (GPU kelių atveju – darbininkų subprocesuose), todėl senasis nuskaitymas pranešdavo
`0 file(s) written` kiekvienam tokiam vykdymui ir tada išsiuntė „zero-exports“ užuominą — vykdymuose,
kuriuose viskas veikė. Jei kuriate skriptą pagal šį skaičių, dabar sėkmingas lygiagretusis vykdymas
praneša apie nelygų nuliui skaičių.

Praleidimai dėl šviesos jutiklio praneša priežastį, kurią skaitytuvas faktiškai nustatė kiekvienam failui —
neskaitoma schema, trūkstantis rinkinys, rašymo klaida — **duplikatai pašalinti**, taigi dvidešimt failų,
praleistų dėl vienos priežasties, rodomi kaip viena priežastis, o ne kaip dvidešimt jos pasikartojimų.

> **`process()` nesukelia išimties, kai vykdymo metu nesukuriama jokių vaizdų.** Tai viena iš dviejų vietų, kuriose SDK ir
> CLI sąmoningai skiriasi: `chloros-cli process` traktuoja „buvo užklausti produktai, bet nė vienas nebuvo
> įrašytas“ kaip nesėkmę ir baigia veikimą su nelygiu nuliui rezultatu, tuo tarpu SDK grįžta įprastai ir praneša apie
> būklę per `summary` / patarimus. Jei jūsų procesų grandinė turėtų sustoti esant tuščiam vykdymui, patikrinkite tai
> patys — peržiūrėkite `summary` (arba suskaičiuokite failus projekto aplanke), o nepasikliaukite
> išimties nebuvimu. Įprastos priežastys yra įvesties aplankas, kuris nebuvo atpažintas kaip
> įrašymo šaltinis, ir produktai, praleisti kaip netaikytini esamoms kameroms (pvz., spinduliavimas iš tik RGB
> kamerų).
>
> `process()` taip pat nesukelia išimties, kai vykdymo eksportų nepavyko patikrinti: jis rodo įspėjimą ir nustato
> `result["export_check_failure"]`. Produktai yra įrašomi. Norėdami sustabdyti procesą dėl to, patikrinkite tą raktą. Vykdymas, kurio
> vaizdas buvo prarastas, veikia taip pat: rodo įspėjimą ir nustato `result["image_failure"]`, o `chloros-cli process`
> baigia veikimą su nelygiu nuliui rezultatu.

### Patogios funkcijos

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "GNDVI", "OSAVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# process_folder() / process_lattice_capture() also take input_level=,
# array_alignment=, array_alignment_crop=, array_alignment_interpolation=,
# progress_callback=, poll_interval=, timeout=, processing_stuck_timeout=,
# backend_exe= and api_url= (NOT backend_url=). indices defaults to ["NDVI"].

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

Kiekvienas procesas veikia savo `with ChlorosLocal(...)` viduje; jo paleistas foninis procesas toliau veikia, kai jis grįžta, kito iškvietimo metu, o po 30 minučių, jei niekas jos nenaudoja, pati sustabdo savo veiklą. Nei viena iš jų nepriima `auto_start_backend`, `radiometric_output` ar `daq_log_path`; naudokite `ChlorosLocal` ir `configure()`. `process_lattice_capture()` nepriima `camera`, `debayer` ar `ppk`: jis praleidžia skydelio aptikimą ir naudoja standartinį „debayer“.

### Palaikomos reikšmės

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"TIFF (8-bit)"            # all-8-bit images are written as this; never raised
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer for Survey3 imagery, Chloros+ only.
                                         # Survey3 only: on a project holding LATTICE captures
                                         # (which always use the standard demosaic) configure()
                                         # or process() raises ChlorosConfigurationError; a
                                         # Texture Aware value the project saved earlier is set
                                         # to Standard with a UserWarning, as the app does. Needs a
                                         # CUDA GPU the backend's PyTorch can use (an NVIDIA
                                         # GPU, or a Jetson Orin's). Without one, process()
                                         # uses the standard debayer, and the backend prints
                                         # one "[DEBAYER] ... needs a CUDA GPU" line saying why.
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as Chloros's saved debayered (3-channel) product
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### Radiometrinė išvestis (LATTICE multispektrinis apdorojimo procesas)

`process` apdorojimo grandinės LATTICE multispektrinio (M3C/M3M) eksporto lygis — `reflectance` (numatyta reikšmė), `radiance`, `sensor-response` arba `all` (kiekvienam vaizdui taikomas režimas) — nustato projekto **„Eksportuoti spinduliavimą“**ir**„Eksportuoti atspindį“** produktus: `radiance` eksportuoja tik spinduliavimą, `reflectance` – tik atspindį, `sensor-response` – nei vieno (tik debayeringo ir peržiūros produktai), o `all` – abu. Produktų be „bayeringo“ ir peržiūros negalima keisti šiame lygyje. `configure()` turi tam skirtą raktinį žodį:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

Išplėstinis išeities variantas — produktų rašymas per `custom_settings` — vis dar veikia, tačiau atminkite, kad jis pakeičia visą nustatymų bloką (žr. toliau pateiktą įspėjimą). Įrašykite raktus „`"Export <product>"`“: projekto savus nustatymus „`"Export radiance"`“ / `"Export reflectance"` nustatymai turi pirmenybę prieš senesnį raktą „`"Radiometric output"`“, todėl vien tik tas raktas nieko nekeičia.

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Export radiance": True, "Export reflectance": False},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (numatytoji reikšmė) padalina kameros spinduliavimą iš **laiko žymos atitikmeniu nustatytu DAQ žemyn nukreiptu spinduliavimu**, kuris automatiškai nustatomas iš įrašyto `.daq` (DAQ-U/M/E)**arba DAQ-M savito tipo `.csv`**, rasto kartu su vaizdais; bet koks vietoje trūkstamas kameros ar DAQ kalibravimo rinkinys pirmą kartą naudojant yra**automatiškai atsisiunčiamas iš AWS**. CLI tai pateikia pagal-tipo produkto perjungikliais `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.**Kieno tai rodmenys?**`.daq` įraše išsaugoti rodmenys yra tos (-ių) kameros (-ų), kurią (-ias) jis apibūdina, rodmenys — nurodyti šalia esančiame `<file>.daq.cameras.json` „sidecar“ (vienetiniai, sinchronizuoti ir masyvo įrašai; neapdorotų serijos nuotraukų sąraše kiekvieno elemento rodmenys nurodomi atskirai) — ir yra suderinamas**tik su tų kamerų vaizdais**. Todėl atskira kamera, neturinti savo rodmenų, nebegali naudotis kitos kameros DAQ; priskirkite jai DAQ arba įrašykite savo. DAQ nuosavas įrašas (`chloros-cli daq pool-record` arba įrašas, pradėtas per GUI), arba bet kuris `.daq` be to „sidecar“ (įskaitant senesnius įrašus), vis dar tinka visoms kameroms.**OCN kameros saulės šviesoje.** Kiekvienas OCN kalibravimo rinkinys sudaromas esant siaurajuosčiam apšvietimui (po vieną šviesos diodą kiekvienam juostos diapazonui), o saulės šviesoje juostų diapazonai susimaišo. Chloros 1.3.0 versija po juostų atskyrimo taiko visam kamerų parkui saulės pagrindu nustatytą korekciją, kuri pagerina OCN spinduliavimo, atspindžio ir indekso rodiklius saulės šviesoje. Ji veikia visur, kur vyksta juostų atskyrimas — „`process()`“, užfiksuotuose vaizduose, „`grab()`“ / „`frame_stream()`“ ir tiesioginiame vaizde — tiems OCN paketams, kurie tai palaiko; RGN kameros lieka nepakitusios. Ankstesne versija užfiksuoti OCN vaizdai taip pat gauna šią korekciją, kai jie apdorojami kompiuteryje, kuriame išsaugota kalibracija, su kuria jie buvo užfiksuoti (Chloros išsaugo kameros kalibraciją, kai kamera yra prijungta); priešingu atveju jie apdorojami kaip ir anksčiau. Todėl OCN spinduliavimo, atspindžio ir indekso reikšmės skiriasi nuo 1.2.0 versijos.

> `custom_settings` **pakeičia** visą apskaičiuotų nustatymų bloką (pagal projektą jis apeina kitus „`configure()`“ raktinius žodžius ir patikrinimą). Naudodami jį, įtraukite visus jums svarbius „`Project Settings`“ raktinius žodžius, kaip parodyta pavyzdyje aukščiau.

#### Indekso pikselių skaitymas (NDVI ir kiti augmenijos indeksai)

**Sveikųjų skaičių indeksinis rasteris yra kiekvieno atskiro vaizdo kontrasto išplėtimas, o ne −1…1 žemėlapis.** `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)` ir `JPG (8-bit)` indeksų produktai — įskaitant 8 bitų TIFF, į kurį įrašomas 8 bitų vaizdas — saugo

```

DN = (value − IndexMin) / (IndexMax − IndexMin) × full_scale
```

kur `IndexMin` / `IndexMax` yra **to kadro mažiausia ir didžiausia baigtinės reikšmės indeksų vertės**. DN 0 nėra −1, o pilna skalė nėra +1.

Chloros pažymi abu galinius taškus, kad atvaizdavimas išliktų apverčiamas:

| Žyma | Reikšmė |
| --- | --- |
| `Chloros:IndexScaling` | `linear_minmax` — žemiau pateikta konvencija |
| `Chloros:IndexMin` | indekso reikšmė, saugoma kaip DN 0 |
| `Chloros:IndexMax` | indekso vertė, saugoma kaip pilna skalė |

Perskaitykite juos ir apverskite, normalizuodami saugomą DN į 0…1 pagal saugomo duomenų tipo maksimumą, kad viena išraiška apimtų visus sveikųjų skaičių formatus:

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

Skaitymui nereikia `-config pix4d.config` — „exiftool“ šias XMP savybes nuskaito be pagalbos. Konfigūracija reikalinga tik norint jas *įrašyti*.

> **`IndexMin == IndexMax`** reiškia, kad kiekvienas to kadro pikselis turėjo tą pačią indekso vertę. Rasteris užpildytas vidutiniu masteliu ir nėra kuo dalyti — imkite galinį tašką.

> **Jei nėra `Chloros:Index*` žymių, tai reiškia, kad nėra nurodyto atvaizdavimo.** LUT eksportas yra spalvotas atvaizdas, kurio pikseliai yra gradientinės spalvos, susietos su jūsų LUT slenksčio langu, o kadras be pikselių su baigtiniu indeksu neturi nurodyti jokio diapazono. Abiem atvejais nereikia daryti prielaidų apie atitikmenį — taikoma ta pati taisyklė kaip ir trūkstamos `PixelScale` žymės atveju.**Kadangi ištempimas taikomas kiekvienam atskirai vaizdui, du vieno skrydžio kadrai nėra palyginami pagal DN**— kiekvienas turi savo galinius taškus, todėl tas pats NDVI atitinka skirtingą DN skirtinguose vaizduose. Atliekant kiekybinį darbą su visais skrydžio duomenimis (zoninė statistika, fiksuotos ribos, laiko eilutės), eksportuokite**`TIFF (32-bit, Percent)`**: indeksiniai rasteriai ten įrašomi kaip**pačios indeksų reikšmės** tipo „float32“, be perskalavimo ir be žymių. Nepaisant formato pavadinimo, indeksiniai produktai nėra ×100 — tas mastelis taikomas tik sveikųjų skaičių tipo atspindžio reikšmėms.

#### „Unmix“ apribojimo kaukė — kuriuose pikseliuose juosta buvo apribota

3×3 spektrinis atskyrimas gali paversti juostą neigiamą — ten, kur šviesoje beveik nėra tos juostos, arba ten, kur scena nepatenka į kameros atskyrimo matricos apibrėžtą sritį. Tai nėra tikroji vertė, todėl eksportuojant ji nustatoma kaip 0 — o tiesioginiame vaizde rodomas tas pats 0. Juosta gali būti apribota keliuose pikseliuose arba beveik visuose; kaukė užfiksuoja bet kurį iš šių atvejų. `Chloros:Band{R,G,B}UnmixClampFraction` nurodo, kokioje kadro dalyje tai įvyko; apribojimo kaukė nurodo, **kuriuose pikseliuose**:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— `clamp_masks/` aplanke**šalia** produkto aplanko, niekada ne pačiame jame (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`) — kiekvienam spinduliavimo, atspindžio ir indekso produktui, kuriame buvo apribotas bent vienas pikselis. Vienos juostos `uint8`, ant paties produkto pikselių tinklelio (įskaitant orientaciją, ROI, binningas ir masyvo išlyginimo deformacija įtraukti). Kiekviena kaukė nurodo savo produkto kamerą (`Make` / `Model` / serijos numeris), užfiksavimo laiką ir objektyvo žymes, taip pat išvardija juostas, kurias žymi jos bitai (`XMP-Camera:BandName`, pirmasis bitas 0); jos `ImageDescription` ir `Software` žymi ją kaip fiksuotą kaukę, ir ji neturi kalibravimo.
- **Bitai**: 0-asis bitas = juosta produkto R kanale (`Chloros:BandRName`), 1-asis bitas = G (`BandGName`), 2-asis bitas = B (`BandBName`). Nustatytas bitas reiškia, kad to pikselio vertė toje juostoje buvo apribota iki 0 — šį pikselį ir bet kokį iš jo sudarytą indeksą laikykite mažo patikimumo.
- **`Chloros:UnmixClampMask`** yra pažymėtas ant kiekvieno produkto, kuriam buvo atliktas atskyrimas: kaukės kelias, nurodytas santykinai pagal paties produkto aplanką (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, visada atskirtas `/`-skiriamas — sujunkite jį su vaizdo aplanku, kad atidarytumėte kaukę), arba „`none`“, kai nė vienas pikselis nebuvo apribotas. Žymės nebuvimas reiškia, kad produktui niekada nebuvo taikytas „unmix“ procesas (RGB arba monochrominės kameros, neapdoroti/debayeringo lygiai).
- Įrašymo rezultatai pateikia jį kaip kadro **`clamp_mask`** (kaukės kelias arba `None`, kai nieko neapribota) — `CameraSession.capture()` rezultatuose, `ArraySession.capture()`'`CaptureResult` kadruose ir HTTP užfiksavimo atsakymuose. Neprisijungusios versijos sąraše kaukės nurodytos kaip `clamp_masks`.
- Tiesioginio indekso daugiabandis stekas (`/api/camera/cli-array-index` su `save_multiband`) gauna po vieną kaukę kiekvienai šaltinio kamerai, išvesties aplanke esančiame `clamp_masks/` — žr. CLI nuorodą`lattice index`. Vaizdo įrašas neturi kaukės: vietoj to neprisijungusios versijos rezultatuose pateikiama kiekvieno kadro apribojimo dalis.

Kadangi nė viename produkto aplanke, nepriklausomai nuo jo lygmens, nėra kaukės, produkto aplanką galima perduoti fotogrametrijos programinei įrangai tokį, koks jis yra — įskaitant pakatalogius — ir ji apdoroja tik vaizdus. Perduokite jai produkto aplanką, o ne virš jo esančio aplanko. „Chloros“ importavimo funkcija praleidžia kaukes.

```python
import os, numpy as np, tifffile

mask = tifffile.imread(
    "clamp_masks/radiance/capture_<ts>_SN<serial>-<filter>_radiance_clampmask.tif")
# or, from the image: os.path.join(os.path.dirname(image_path), tag), with
# tag = the image's Chloros:UnmixClampMask
r_clamped = (mask & 1) != 0      # the band Chloros:BandRName names
g_clamped = (mask & 2) != 0
b_clamped = (mask & 4) != 0
```

---

## „Smart-Connect“ „LATTICE“ kameroms

Nuolatinės užkulisinės sesijos, skirtos veikiančiai aparatūrai. Naudojami tie patys galiniai taškai, kuriuos naudoja GUI, todėl elgsena yra identiška SDK, CLI ir „GUI“ aplinkose.

### Viena kamera — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

Čia nustatyti ekspozicijos, stiprinimo ir tikslo parametrai išsaugomi kaip jūsų: „Chloros“ automatinis ekspozicijos valdymas (įskaitant DAQ matuojamą ekspoziciją) jų nekeičia. `cam.set_settings(exposure_auto="Continuous")` (arba `gain_auto=`) grąžina ekspoziciją (stiprinimą), o „`cam.set_settings(target_brightness_auto=True)`“ grąžina tikslą „Chloros“ automatiniam tikslui.

#### „`connect_camera()`“ parašas

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

- **Jis grįžta, kai srautas jau paleistas.** Užkulisinė sistema laukia, ribodama laukimo trukmę (apie 15 s, kol prasideda srautas, tada 10 s, kol užpildomas pirmasis buferis – ne pirmasis peržiūros kadras, kuris pasirodo maždaug po 1,5 s), todėl iškart po jo pasirodo „`status()`“, o po to – „`streaming`“. Sesijos „`connect_stream`“ yra atsakymo „`stream`“ blokas: `state` `streaming`, `no_frames` (srautas prasidėjo, bet nieko neperdavė — tai nurodo `note`, o kamerai, naudojančiai „jumbo“ paketus, nurodoma vėl prisijungti su 1500 baitų dydžio paketu), `starting` arba `start_failed`, kur `waited_s`. `None` skirta kamerai, kuri jau buvo prijungta. Peržiūros (`get_camera_preview`, `/api/camera/jpeg/<serial>`), paprašyta iškart po laukimo, ribojama (4 s), kol gausite pirmąjį kameros peržiūros kadrą, vietoj atsakymo „Kadrų dar nėra“; kamera, kurios srautas nieko neperdavė (`no_frames` arba įjungta aparatinio trigerio režimu), vis tiek atsako iš karto.
- **Paketo dydį galite nustatyti patys.** `settings={"gev_packet_size": 1500, "gev_packet_size_auto": False}` atidaro kamerą su 1500 baitų paketais, nepatikrinus (`chloros-cli lattice cam-connect --packet-size standard` siunčia tą patį).
- **Vienu metu gali būti užimtas tik vienas ryšys su kamerų grupe**: atidarymas, paketo dydžio tikrinimas, pirmasis kalibravimo duomenų atsisiuntimas (apie 70 s, jei kamera šiame kompiuteryje naudojama pirmą kartą), srauto paleidimas. Užklausa, kuriai tuo metu reikalinga kamera (`read_nodes`, `set_settings`, įrašymas, antrasis prisijungimas) laukia jos ne ilgiau kaip 5 s, tada grąžina klaidą su HTTP 503 `CAMERA_POOL_BUSY`, nurodydamas prisijungiančią kamerą (`ChlorosConnectError`); paklauskite vėl po akimirkos. Sąrašai niekada nelaukia: `list_cameras()` (`GET /api/camera/pool`), `list_arrays()` ir `CameraSession.status()` atsako iš karto. Prijungimo procese esanti kamera sąraše nurodyta po grupės atsakymo `connecting` (`serial`, `phase` — `opening the camera`, `rebalancing the pool bandwidth`, `loading its calibration`, `starting its stream`, `waiting for its stream` — ir `elapsed_s`), o jo `status()` nurodo `connecting: True` su jo `phase`; kitos kameros nuskaitomos realiuoju laiku, kai kanalų grupė yra laisva, kitais atvejais atsakoma pagal paskutinį nuskaitymą (`live: False`).

#### `CameraSession` metodai

| Metodas | Aprašymas |
| --- | --- |
| `status(timeout=15.0)` | Kameros tiesioginė būsena, kaip ją nuskaito `chloros-cli lattice status`: `streaming`, `stream_state` (`starting`, `no_frames`, `streaming`, `start_failed` arba `stopped`), `device` (modelis, serijos numeris, programinė įranga, temperatūra, `is_mono`) ir jo dabartinius nustatymus. |
| `read_nodes(names, enum_names=(), timeout=30.0)` | Skaito „GenICam“ mazgus; grąžina `{nodes, errors, enums, device, was_in_pool, in_array_id}`, taip pat `frame_values` ir `notes`, kol veikia automatinė ekspozicija (žr. toliau). |
| `set_settings(**kwargs)` | Įrašo mazgus pagal patogų pavadinimą (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `target_brightness_auto`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). `exposure_time` (`gain`) yra rankinis ekspozicijos (stiprinimo) nustatymas: įrašytas be `exposure_auto` (`gain_auto`), kad pirmiausia būtų išjungtas automatinis režimas, todėl veikiantis automatinis režimas neatsisakytų šios vertės. Rezultatas `applied` išlaiko tai, ką užfiksavo kamera; kameros atmestas įrašymas yra `errors` ir nėra išsaugotas. Klaida nurodo diapazoną, kurį kamera dabar priima (`ExposureTime` maksimumas priklauso nuo kameros kadrų dažnio), o automatinis režimas, kuris buvo išjungtas tik dėl atmestos reikšmės, vėl įjungiamas; `restored` pateikia jo sąrašą (pvz., `{"ExposureAuto": "Continuous"}`). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, daq=None, daq_tolerance_ms=None, settings=None, timeout=None)` | Užfiksuoti **vieną** kadrą. Grąžina `CaptureResult`: kadrų metaduomenų žodynų sąrašą (po vieną kiekvienam išsaugotam lygiui), su `.skipped`, `.failed`, `.daq_files` ir `.daq_matches`. (Serijinis / kelių kadrų fiksavimas buvo pašalintas — jei reikia serijos, iškvieskite `capture()` cikle.) |
| `capture_repeated(output_dir="output", *, count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Iš eilės (`interval_s=0`) arba intervalais atliekamas fotografavimas iš šios kameros, kai atliekamas `count` fiksavimas ir (arba) `duration_s` (bent vienas, kad procesas baigtųsi), kaip ir `ArraySession.capture_repeated()` atveju su masyvu. Grąžina `CaptureResult` sąrašą; sustabdo po įrašo, pranešančio apie užpildytą diską. `on_capture(i, result)` vykdomas po kiekvieno; kiti raktiniai žodžiai nukreipiami į `capture()`. |
| `disconnect(keep_settings=False, clear_settings=False)` | Išlaisvinimas iš rezervo, kamera grąžinama į tą būseną, kokioje buvo rasta prisijungus (sąlyginio paleidimo laidai, PTP, nustatymai); `keep_settings=True` vietoj to išsaugo nustatymus savo atmintyje, o `clear_settings=True` išvalo tą atmintį (žr. [Kameros atmintį](#camera-memory--keep_settings--clear_settings)); grąžina `camera_memory`, kai prašoma bet kurio iš jų. Kamera, kuri yra masyvo narys, išlaisvina visą masyvą. Nėra jokio veiksmo, jei prisijungėme prie jau atidarytos sesijos. Grąžina, kai užkulisiai patvirtina atlaisvinimą arba kai nėra ko atlaisvinti (kamera jau pašalinta arba neveikia jokie užkulisiai); jei atleidimas pasiekė laiko limitą arba jį atmetė užkulisinė sistema, sukeliamas kodas `ChlorosConnectError`, nes kamera vis dar gali būti užimta ir kitas prisijungimas su ja susidurtų. Išeinant iš `with` bloko kodas niekada nesukelia išimties. |

Atributai: `serial`, `device` (kameros aptikimo įrašas) ir `backend_url`.

`capture()` eksporto valdymas (tas pats modelis kaip masyvas + GUI):

- `processing` / `levels` — `processing="all"` išsaugo visus taikytinus eksporto tipus; `levels=["raw","radiance"]` išsaugo tik tuos (perrašo `processing`). Norint naudoti užkulisio numatytuosius nustatymus, praleiskite abu.
- `daq` — DAQ, iš kurio buvo nuskaityti naudojami duomenys, pagal numatytuosius nustatymus išsaugomas kaip `.daq` papildomas failas, įskaitant tik neapdorotą duomenų rinkinį, kad vėliau kadrą būtų galima perdirbti į atspindžio koeficientą ar lūžio rodiklį. `daq=False` praleidžia jį, jei nė vienas lygis jo nenaudoja. `force_daq=True` yra senesnė numatytosios reikšmės rašyba. Jei nėra DAQ, nėra „`.daq`“.
- „`daq_tolerance_ms`“ — kiek rodmuo gali nukrypti nuo ekspozicijos ir vis tiek atitikti leistiną nuokrypį (pagal numatytuosius nustatymus – 500). Keičiasi tik vertinimas.

#### `read_nodes()` naudojant automatinę ekspoziciją — `frame_values` ir `notes`

Kol veikia `ExposureAuto` (`GainAuto`), mazgas `ExposureTime` (`Gain`) mazgas nuskaito **paskutinę įrašytą reikšmę**, o ne tai, ką tuo metu daro kamera. `nodes` vis dar išlaiko tą mazgo reikšmę. Kai užklausiate `ExposureTime` arba `Gain` iš prijungtos kameros, kurios automatinis režimas nėra `Off`, atsakyme papildomai nurodoma:

- `frame_values` — naujausio kameros kadro reikšmė, nurodyta po mazgo pavadinimo (`ExposureTime` mikrosekundėmis, `Gain` decibelais);
- `notes` — po vieną sakinį kiekvienam tokiam mazgui, kuriame nurodomos abi reikšmės.

```python
r = cam.read_nodes(["ExposureTime", "Gain"])
print(r["nodes"]["ExposureTime"])                  # last value written, e.g. 2000.448
print(r.get("frame_values", {}).get("ExposureTime"))  # what the newest frame used, e.g. 19600.0
for name, note in r.get("notes", {}).items():
    print(note)
# ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448);
# the camera's newest frame was at 19600 us.
```

Abu raktiniai žodžiai nėra, kai automatinis režimas yra `Off` (tuomet mazgas yra kamerostikroji vertė) ir kai kamera nėra prijungta prie užpakalinės dalies. Masyvo atveju `ArraySession.status()["member_ae"]` vienu metu pateikia kiekvieno elemento kadrų vertes.

#### DAQ matuojama ekspozicijos riba (HTTP)

Prijungus DAQ šviesos jutiklį, Chloros apriboja kameros ekspoziciją × stiprinimą pagal išmatuotą dienos šviesą, kad balta etaloninė plokštė nebūtų perkrauta. Riba taikoma tik tol, kol kalibravimo taikinys yra tos kameros matymo lauke (matomas jos kadre per paskutines 10 s); jei taikinio matymo lauke nėra, vietoj to veikia įprasta kameros automatinė ekspozicija su savo pačios apsauga nuo peršvietimo, todėl likusi scenos dalis nėra išlaikoma tamsesnė nei reikia. SDK-apvalkalo nėra; šią funkciją galima peržiūrėti arba įjungti / išjungti kiekvienai kamerai atskirai naudojant `GET` / `POST /api/camera/<serial>/daq-exposure-cap` (POST kūno lauke nurodžius `{"enabled": false}`, ši funkcija bus išjungta tai kamerai). Atsakyme pateikiami `enabled`, `cap_us` (maksimali riba realaus vaizdo režimu, `None`, kai funkcija neaktyvi, įskaitant atvejus, kai kalibravimo taikinys nėra matomas), `k_exp_g` (konstanta, pagal kurią apskaičiuojama viršutinė riba) ir:

| Laukas | Reikšmė |
| --- | --- |
| `k_exp_g_source` | Kieno konstanta naudojama: `camera` — šios kameros, apskaičiuota pagal kalibravimo paketo laboratorinį tašką (kameros su RGB); `fleet` — parko konstanta, naudojama tais atvejais, kai rinkinyje nėra laboratorinio taško; `override` — lauke nustatytas perrašymas, nustatytas serveryje. |
| `k_exp_g_fleet` | Parko konstanta, skirta palyginimui. |

### Sinchronizuotas masyvas — `ArraySession` (Smart-Prep)

`connect_array` yra **rekomenduojamas pradinis taškas**, skirtas daugiakamerinėms sistemoms. Jis viduje vykdo visą „Smart-Prep“ procesą su grafinė vartotojo sąsaja:

1. **Tinklo analizė**(`/api/camera/array/recommend`) — nustato**visą jutiklį** (be pikselių sujungimo, be ROI apkarpymo) ir leidžia kadrų dažniui sumažėti iki to, ką gali užtikrinti šis kompiuteris ir tinklas, atsižvelgiant į pikselių formatus ir pakavimo būdus, kuriuos naudos „Connect“. Kadrai niekada nėra suspaudžiami, siekiant išlaikyti aukštą dažnį: nurodykite mažesnį `width` / `height` arba `binning=2` / `4`, kad mainytumėte skiriamąją gebą į kadrų dažnį (galiniame taške`alternatives` sąraše išvardytos šios parinktys su numatomais kadrų dažniais (fps)).
2. **Automatinis lygio pasirinkimas** — `sim-capture-sim-emit` (visos kameros eksponuoja pagal tą patį trigerį, signalai siunčiami pakaitomis, kad būtų dalijamasi laidu; kadrų dažnis mažėja, kai dalijimosi pralaidumas mažėja) bet kuriame tinkle, kuriame kameros gali būti sinchronizuotos dalytis laidu; `slip-emit-and-capture` (kameros naudoja laidą paeiliui, o ne eksponuoja vienu metu) – tik tada, kai to padaryti neįmanoma.
3. **PTP įjungta**pagal numatytuosius nustatymus — skirtingų kamerų laiko žymos sutampa su vienu bendru laikrodžiu iki**~1 ms**tikslumu, o užkulisio [PTP grandmasteris](#ptp-grandmaster--time_sync_status). Vienalaikis eksponavimas užtikrinamas naudojant „M8“ aparatinį trigerį (**< 100 µs** tarp modulių), o ne PTP: PTP suderina *laiko žymes*, o ne ekspozicijas.
4. **Automatinis pikselių formato pasirinkimas kiekvienai kamerai** — monochromatinis → `Mono12`, spalvotas → `BayerRG12`.
5. **AE nustatymų išsaugojimas** — užfiksuoja kiekvienos kameros dabartinę AE būseną, kad prisijungus ekspozicija nebūtų iš naujo nustatoma proceso metu.
6. **GPIO trigerio konfigūracija** — `connect_array` įjungia kiekvieną kamerą (`TriggerMode=On`, `TriggerSource=Line2`), kad pagrindinio įrenginioimpulsas valdytų pavaldžias kameras per M8 kabelį. Tai žingsnis, skirtas tik masyvui: atskirai atidaryta kamera su `LatticeCamera` vietoj to veikia laisvuoju režimu. Atleidus masyvą (`disconnect()` / `force_disconnect()`, `chloros-cli lattice array-disconnect`) grąžina visas kameras į laisvo veikimo režimą (`TriggerMode=Off`), o visos kitos nustatytos reikšmės išlieka tokios, kokias nustatė „connect“: trigerio ir sinchronizavimo linijos nustatymai (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`; pagrindinės kameros sinchronizavimo linija grąžinama į įvestį), PTP ir kameros nustatymus, kuriuos pakeitė sesija — išskyrus atvejus, kai `keep_settings=True` / `clear_settings=True` (žr. [Kameros atmintis](#camera-memory--keep_settings--clear_settings)).

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

Atributai: `array_id`, `master_serial`, `slave_serials`, `ptp_enabled`, `sync_mode`, `packed_transport` ir `packed_reason` (paskutiniai keturi yra `None` ant rankenos nuo `attach_array`), `connect_stream` ir `backend_url`. `connect_stream` yra prisijungimo atsakymo `stream` blokas, vieno prisijungimo forma plius `members` (`{serial: {state, why?}}`): `streaming`, kai tik atvyksta pirmoji sinchronizuota masyvo grupė (užpakalinė dalis jos laukia, ribotą laiką, apie 10 s), arba „`no_frames`“, kai kiekvieno nario paskutinė nesėkminga duomenų paieška tampa „`why`“, o didelių paketų atveju 1500 baitų atsarginis variantas `note`. `None` su `attach_array` identifikatoriumi.

#### `connect_array()` parašas

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # pin the master trigger rate (auto if None); see set_target_fps
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    packed="auto",                        # packed 12-bit transport: "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=600.0,                        # s; also the backend's deadline (see below)
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`timeout` nurodo, kiek laiko iškvieta laukia prisijungimo, ir šis laikas siunčiamas į
galinį serverį kaip galutinis terminas. Jei iki to laiko ryšys neužmezgamas, viskas atšaukiama: kiekviena kamera, kurią jis
atidarė, grąžinama į tą būseną, kurioje buvo rasta, ir uždaroma, joks masyvas nėra prijungtas, o
skambutis sukelia `ChlorosConnectError` (HTTP 504 `CLIENT_DEADLINE_PASSED`, nurodant
etapą, kurį pasiekė prisijungimas). Taigi iššauktas skambutis niekada nepalieka už jo
už jo. 9 kamerų masyvas prisijungia per maždaug minutę; iškart po to, kai
kameros įsijungia, tai gali užtrukti keletą minučių. Masyvas, kuris veikia, bet dar neturi
sinchronizuotos kadrų grupės, grąžinamas su kodu `connect_stream["state"]`
`no_frames`. Užkulisio žurnale yra po vieną laiko eilutę kiekvienam prisijungimo etapui
(`[array-connect <id>] <step>: <s> s …`) ir paskutinė eilutė, nurodanti lėčiausią.

`force_tier` reikšmės:
- `"sim-capture-sim-emit"` — tikrasis sinchroniškumas (visos kameros suveikia tuo pačiu laikrodžio impulsu).
- `"sim-capture-ftd-stagger"` — lankstus laiko srities išskirstymas (kameros siunčia signalus šiek tiek skirtingu laiku, todėl paketai perduodami serijiniu būdu per laidą).
- `"slip-emit-and-capture"` — nuoseklusis fiksavimas kiekvienai kamerai (be laiko sinchronizacijos). Pasirenkama automatiškai tik tada, kai kamerų veikimo greičio negalima pakankamai sulėtinti, kad jos galėtų vienu metu dalytis laidą.

`wire_ceiling_mbps` perrašo **pagrindinio kompiuterio nuolatinį laidų pralaidumo limitą** MB/s — vienintelį
skaičių, nuo kurio priklauso visos matricos išteklių paskirstymas. Palikite jį `None`, kad būtų naudojama automatiškai nustatyta
vertė. Sumažinkite jį, kai masyvas praneša apie GVSP sugadintus rėmus: automatinė vertė nustatoma
pagal tinklo plokštės nurodytą ryšio greitį, kuris pervertina USB adapterių, siaurų PCIe juostų ir
užimtų bendrų struktūrų pajėgumą — o šis pervertinimas pasireiškia sugadintais rėmeliais, o ne
akivaizdžiai lėtu ryšiu. Ši reikšmė išsaugoma projekto masyvo fiksavimo bloke, todėl
atidarius iš naujo arba vėliau nustatant „`connect_array`“ ji atkuriama kaip ir bet kuris kitas masyvo parametras.
Žr. [Masyvo būklė](#array-health--which-subsystem-is-losing-frames).

#### Suspaustas 12 bitų perdavimas (`packed=`)

Esant pilnam jutiklių skaičiui, masyvo kadrų dažnis nustatomas pagal tai, kaip greitai kiekviena kamera gali siųsti
savo kadrus. 12 bitų formatai (`BayerRG12`, `Mono12`) paprastai perduodami kaip 16 bitų
žodžiai; suspaustas perdavimas siunčia tuos pačius 12 bitų 1,5 baito dydžio vienam pikseliui ir
juos išpakuoja pagrindiniame kompiuteryje, todėl kiekvieno kadro siuntimas užtrunka 25 % mažiau laiko — apie
**33 % daugiau kadrų per sekundę esant pilnam jutiklio veikimui** (išmatuota: 10,7 → 14,0 kadrų per sekundę
9 kamerų masyve). **Išsaugoti duomenys yra identiški**: tos pačios reikšmės, tie patys 16 bitų
failai, tie patys metaduomenys.

- `packed="auto"` (numatyta) suspaudžia tik tada, kai tai naudinga **ir** pagrindinis kompiuteris gali
  susiimti su išpakuojimu: prisijungus, užkulisinė sistema išmatuoja savo išpakuojimo greitį,
  procesorių skaičių, esamą apkrovą ir tinklo pralaidumą. Stacionarus kompiuteris su laisvais branduoliais
  suspaudžia duomenis esant pilnam jutiklių skaičiui; užimtas arba mažos galios kompiuteris (vienplokštiniai kompiuteriai,
  įterptiniai valdikliai) transliuoja nesuspaustus duomenis. Maži ROI ir sugrupuoti kadrai duoda mažai naudos.
- `packed="off"` (arba `False`) niekada nesuspaudžia — tam, kad būtų galima tai atmesti diagnozuojant,
  arba kompiuteryje, kurio procesoriaus resursai turi būti skirti kitoms užduotims.
- `packed="on"` (arba `True`) visada pakuoja 12 bitų formatus.
- Bet kuri kita reikšmė sukelia `ValueError`. 8 bitų ir 16 bitų formatai niekada
  nėra pakuojami.

`analyze_array_network(..., packed="auto")` projektuoja tą patį sprendimą,
todėl jo fps yra greitis, kuriuo masyvas veiks (atsakyme pateikiami
`packed_transport` ir `packed_reason`); jo `pixel_format` numatytasis nustatymas yra tas, kurį
taiko „connect“ (Mono12 / BayerRG12), o ne 8 bitų.

Sprendimas ir jo priežastis nurodyti sesijoje:

```python
arr = chloros_sdk.connect_array(serials)          # packed="auto"
print(arr.packed_transport, arr.packed_reason)
# True  auto: projected 11.0 -> 14.4 fps, unpack 0.16 of 1.50 budgeted cores

arr = chloros_sdk.connect_array(serials, packed="off")
```

`ChlorosProject.connect_all(packed=...)` taiko tą patį perrašymą kiekvienam
projekto masyvui (numatyta reikšmė `None`, o tai reiškia, kad `"auto"`: projektas nesaugo
supakuotų nustatymų kiekvienam masyvui).
Norint nustatyti visam kompiuteriui galiojančią numatytąją reikšmę, nustatykite `CHLOROS_ARRAY_PACKED12=off` (arba `on`)
užkulisinės aplinkos nustatymuose; tačiau aiškiai nurodytas `packed="on"` / `"off"` vis tiek turi pirmenybę.
`/api/camera/array/status` praneša apie `packed_transport` ir `packed_decision`.

#### Numatomi kadrų dažniai

Išmatuoti masyvo kadrų dažniai (sinchronizuoti su aparatine įranga, 12 bitų; visas jutiklis = 2048 × 1536). Naudokite juos, kad patikrintumėte, ar `status()["actual_fps"]`; `analyze_array_network()` projektai atitinka jūsų kompiuterio ir tinklo dažnį.

| Konfigūracija | Masyvo kadrų dažnis |
| --- | --- |
| Visas jutiklis, PCIe 10 GbE pagrindinio kompiuterio tinklo plokštė, suspaustas perdavimas (`packed="auto"` stacionariame kompiuteryje) | ~14 kadrų per sekundę |
| Visas jutiklis, kompiuterio PCIe 10 GbE tinklo plokštė, nesuspaustas perdavimas (`packed="off"`) | ~10,5 kadrų per sekundę |
| Visas jutiklis, 8 kameros su USB 10 GbE adapteriu | ~3,8 kadrų per sekundę |
| Visas jutiklis, 1 GbE pagrindinis ryšys | 1,2–1,7 kadrų per sekundę |
| 2 × 2 pikselių sujungimas (`binning=2`), 10 GbE pagrindinis tinklo adapteris | ~30–33 kadrų per sekundę |

- Naudojant PCIe 10 GbE jungtį, dirbant su visu jutikliu ribojančiu veiksniu yra **kameros**, o ne tinklas: 4 ir 8 kameros veikia tuo pačiu greičiu.
- **`target_fps` yra suaktyvinimo dažnis, o matrica niekada neveikia greičiau.** `target_fps=1` valdo masyvą 1 kadrų per sekundę greičiu; `None` (numatytoji reikšmė) valdo jį didžiausiu greičiu, kurį palaiko pagrindinis kompiuteris ir tinklas. Jei greitis viršija šią ribą, jis sumažinamas iki jos (žr. [`set_target_fps`](#interval-capture-for-drone-surveys)).
- **`link_speed_mbps` iš `4294` į `status()` nėra ryšio greitis.** Tai reiškia, kad „Windows“ negalėjo nuskaityti adapterio greičio (vairuotojas pranešė didžiausią 32 bitų vertę), o tai būdinga USB Ethernet adapteriams. USB adapterio naudingas greitis „Windows“ sistemoje bet kuriuo atveju yra ribojamas iki 200 MB/s (žr. [Array Health](#array-health--which-subsystem-is-losing-frames)); norėdami jį optimizuoti, žr. CLI žinyno skyrių „USB 10GbE adapteriai“ dalyje [Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **USB ribojimas taikomas ir „Linux“ sistemoje: 400 MB/s „x86_64“ platformoje, 200 MB/s „NVIDIA Jetson“ platformoje.** Adapteris atpažįstamas iš sysfs (atsarginiu atveju naudojamas `ethtool -i`), todėl USB 5 GbE arba 10 GbE adapterio pralaidumas apskaičiuojamas pagal šį apribojimą, o ne pagal jo techninius duomenis (500 / ~1060 MB/s). x86_64 rodiklis buvo išmatuotas „Linux“ sistemoje: tas pats adapteris, kuris „Windows“ sistemoje sugadina kadrus esant greičiui virš 200 MB/s, sklandžiai valdė 4 kamerų masyvą esant bet kuriam ribiniam greičiui iki 480 MB/s. „NVIDIA Jetson“ platformoje tas pats adapteris veikė be sutrikimų tik esant 200 MB/s greičiui, o viršijus šį greitį retkarčiais prarandavo kadrus. Jei jūsų pagrindinis kompiuteris apdoroja daugiau duomenų be `gvsp_corrupt` kadrų, padidinkite jį naudodami „`wire_ceiling_mbps`“. „Linux“ sistemoje USB 2.0 prievade esančiam adapteriui skiriama 30 MB/s; prijunkite jį prie USB 3 prievado.

#### Perteklinis paskirstymas (minimalus greitis vienai kamerai)

Masyvas veikia bet kuriame tinkle; viską lemia kadrų dažnis. „Sim-emit“ tempas kiekvienai kamerai paskirsto vienodą dalį susidūrimų išvengiančio ryšio pralaidumo, ir visos jos vis tiek eksponuoja tuo pačiu trigeriu, taigi lėtu ryšiu dalis — ir kadrų dažnis — yra maži: 8 pilnos-rezoliucijos 12 bitų kameros 1 GbE tinkle be „jumbo“ rėmelių gauna po 6,5 MB/s, tai yra apie **1 kadrą per sekundę**(apie 1,3 kadro per sekundę su suspaudimu). Dalies apatinė riba yra nustatyta pagal lėčiausią greitį, kuriuo kameros gali būti reguliuojamos (`per_cam_floor_bps`: prisijungus nuskaitoma iš kiekvienos kameros GevSCPD diapazono, ir niekada ne mažiau kaip 1 MB/s). Jei ryšio linija reikalauja mažesnio greičio, prisijungimas pereina prie**serijinio**perdavimo (`slip-emit-and-capture`): kameros paeiliui perduoda duomenis pilnu ryšio greičiu, taigi kiekvienas rėmelis pasiekia tikslą, bet nariai**nėra atskleidžiami vienu metu**— tai nurodo atsakymo lygis ir blokatoriai (`SERIAL: ...`) tai patvirtina; perduokite `force_tier="sim-capture-sim-emit"`, kad atsisakytumėte grįžimo prie atsarginio režimo. Kadro dydis neturi įtakos dalijimuisi, todėl**kadrų sujungimas ir ROI padidina kadrų dažnį, bet nekeičia to, ar masyvas tilps**. Tik tas ryšys, kuris negali perduoti masyvo jokiu dažniu – nei vienu metu, nei paeiliui,**yra perkrautas** — gedimo priežastis yra GVSP paketų praradimas, o ne mažesnis kadrų dažnis. Sprendimai: mažiau kamerų, „jumbo“ kadrai nuo vieno galo iki kito arba greitesnis tinklo adapteris.

- Atsakymai „`analyze_array_network()`“ ir „`/api/camera/array/connect`“ perduoda „`oversubscribed`“, „`aggregate_demand_bps`“, „`collision_safe_ceiling_bps`, `max_cams_collision_safe` ir `per_cam_floor_bps`. Kai `oversubscribed` yra „true“, projekcija **nustato fps laukelius į nulį** (`achievable_fps_max` / `fps_bright` / `fps_dark`), o ne pateikia klaidinančio duomenų perdavimo greičio, kuris yra lėtas, bet veikia.
- `POST /api/camera/array/connect` priima `pin_resolution` kūno parametrą (**tik HTTP — ne SDK kwarg**; `connect_array` jo neatskleidžia). Tai griežtas režimas: per daug užregistruotas prisijungimas su nustatytu `pin_resolution` yra**kategoriškai atmestas**, pateikiant klaidą, kurioje nurodomos visos išeitys. Jei šio parametro nėra, prisijungimas vyksta su įspėjimu, kad masyvas praras paketus. Bet kuriuo atveju prisijungimas išlaiko prašomą rėmo dydį — jis niekada pats nesugrupuoja ar neapkarpo.
- Tik bandymams: nustatykite „`CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1`“ užkulisinės aplinkos parametrų rinkinyje, kad atmetimas būtų sumažintas iki garsaus įspėjimo — jūs vis tiek prisijungsite ir sutiksite su paketų praradimu.

#### Masyvo būklė — kuri posistemė praranda rėmus

`ArraySession.capability()` (`GET /api/camera/array/<array_id>/capability`) perduoda realaus laiko `health` bloką
prijungtame masyve, kuris perskaičiuojamas kas **10 sekundžių** laiko tarpu. Jis suskirsto rėmelių praradimą
į dvi priežastis, kurioms reikia priešingų sprendimų, vietoj vieno „nepilno“ rodiklio, kuris
nenurodo nė vienos iš jų:

| Laukelis | Ką tai reiškia | Kurioje posistemyje |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (pagal serijos numerį) | Kadras **atėjo, bet buvo struktūriškai sugadintas**— GVSP paketų praradimas. |**Tinklas**: laidų pralaidumas, tempas, NIC RX žiedas, MTU |
| `never_arrived_rate_pct` (pagal serijos numerį) | Rėmelis **visai nepasiekė**— kamera nesuveikė arba iš jos nieko neišėjo. |**Sukėlėjas / sinchronizacija**: M8 kabelis, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Blogiausias kameros perdavimo greitis kiekvienai. | — |
| `per_cam_rate_pct` | Bendras neišsamumo rodiklis vienam fotoaparatui (abi priežastys kartu). | — |
| `stable_for_seconds` | Kiek laiko kiekvienas fotoaparatas buvo žemiau 0,01 %. | — |

Kartu su `health`, tame pačiame įraše nurodytas skaičius, nuo kurio priklauso visas paskirstymas:

| Laukas | Ką reiškia |
| --- | --- |
| `wire_ceiling_mbps` | Galiojantis nuolatinis kompiuterio laidinio ryšio pralaidumo limitas, MB/s. |
| `wire_ceiling_source` | Iš kur gautas šis skaičius, žodžiais — pvz., `USB-capped 200 MB/s (was theoretical 1062; …)` arba `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, kai jį nustatė `wire_ceiling_mbps=`. |
| `nic_is_usb` | `true`, jei naudojamas USB Ethernet adapteris. |

```python
import chloros_sdk

serials = ["213800234", "214000533"]           # master first
arr = chloros_sdk.attach_array(serials)
h = arr.capability()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    # force_disconnect(), not disconnect(): this handle came from attach_array,
    # and disconnect() never tears down an array the session only attached to.
    # It returns only once the backend confirms the release; a teardown that
    # timed out or was refused raises ChlorosConnectError instead, so the
    # reconnect below never races a half-released array.
    arr.force_disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Aiškinimas:** nelygi nuliui `gvsp_corrupt_rate_pct` reikšmė, kai `never_arrived_rate_pct` yra 0, reiškia,
kad trigeris ir kabelio sinchronizacija veikia puikiai, o 100 % nuostolių tenka tinklo maršrutui — sumažinkite
`wire_ceiling_mbps` ir prisijunkite iš naujo. Atvirkštinis modelis rodo, kad problema yra sinchronizavimo kabelyje arba
trigerio linijoje.

> **`target_fps` nėra veiksnys, lemiantis sugadintus rėmus.** GevSCPD tempas nustatomas vieną kartą
> prisijungus, todėl sumažinus trigerio dažnį keičiasi darbo ciklas, o ne
> vienalaikio siuntimo serijos dažnis. Išmatuotas 5 kartų paklausos sumažinimas nedavė jokio pagerėjimo, o
> sumažinus laidinio ryšio ribą nuo 240 iki 200 MB/s, to paties įrenginio sugadintų kadrų procentas sumažėjo nuo 10,4 % iki
> 0,00 %.

> **Veikiantis masyvas negali pats persiplanuoti LATTICE (M3C / M3M) programinėje įrangoje.** Ji negali
> pati išspręsti šios problemos; atjunkite ir vėl prijunkite, kad prisijungimo laiko pasirinkimo programa iš naujo suplanuotų veikimą pagal
> naują ribą.

**USB Ethernet adapterio greitis yra ribojamas iki 200 MB/s** zondo, nepriklausomai nuo jo
pavadinimo: efektyvumo lentelė, kuri paverčia ryšio greitį pastoviu skaičiumi, yra
nustatyta pagal PCIe standartą, o USB tinklo plokštė nurodo savo Ethernet ryšio greitį, tačiau yra ribojama
USB magistralės ir jos tvarkyklės. Ribojimas yra absoliutus, o ne dalinis — USB 1 GbE adapteris
pasiekia ~80 MB/s ir jam tai neturi įtakos. Tai taikoma Windows ir Linux (įskaitant „Jetson“);
žr. [Numatomi kadrų dažniai](#expected-frame-rates), kur tai buvo išmatuota.

#### `ArraySession` metodai

| Metodas | Aprašymas |
| --- | --- |
| `status(timeout=10.0)` | Veikiančio masyvo būklė realiuoju laiku: `target_fps` / `target_fps_auto` (sukėlimo dažnis ir ar dažnio valdiklis jį valdo), `target_fps_clamped_reason` (kodėl fiksuotas dažnis išlaikomas žemesnis, kitaip – `None`), `actual_fps` (išmatuota; 0, kai paėmimo ciklas pasidavė arba užstrigo), `recommended_fps`, `streaming` („False“, kai paėmimo ciklas nutraukiamas — patikrinkite šį rodiklį, o ne tik `last_error`; lieka „True“ po nustatymų perkrovimo, ką žymi `restarting`), `stalled` (vertė „True“, kol „grab“ ciklas veikia, bet nepaskelbė jokių kadrų grupių maždaug dešimties kadrų laikotarpiu, 2–30 s; `last_error` tai nurodo tik po 60 s), `last_frame_age_s`, `last_error`, `serials`, `ptp_enabled` (`True` rodo tik tada, kai masyvas buvo prijungtas prie PTP **ir** kiekvienas narys paskutinį kartą perskaitė `PtpEnable` įjungtą; tai nėra tas pats, kas užrakintas, kurį matuoja `ptp_epoch`), `ptp_configured` (ko paprašė prisijungęs įrenginys), `ptp_off_members` / `ptp_unread_members` (nariai, kurie paskutinį kartą perskaitė, kad `PtpEnable` išjungtas / neatsakė), `member_recovery` (`None` arba narys, praradęs maitinimą ar ryšį: `state`, `waiting`, nors jis neatsako, `reopening`, tada `recovered`, kartu su `members` ir `why` — masyvas jį vėl įtraukia, kai jis vėl atsako, ir grąžina jo prisijungimo metu nustatytą PTP, trigerio ir sinchronizavimo linijos sujungimus, tempą ir kameros nustatymus, tada vėl transliuoja be naujų `connect_array`) ir `ptp_epoch` (išmatuota), `sync_mode` / `sync_tier`, `packed_transport` / `packed_decision`, `per_cam_emit_mbps`, `link_speed_mbps` (`4294` reiškia, kad Windows negalėjo nuskaityti adapterio greičio — žr. [Numatomi kadrų dažniai](#expected-frame-rates)), `trigger_pipeline` / `pipeline_tune`, `radiometric_chain` (ar galima naudoti spinduliavimo grandinę pagal juostą ir kuris elementas ją nutraukė) bei `band_clamp` — tuo tarpu tiesioginis vaizdas atkuria matricos duomenis: apšviestų pikselių dalį, kurią atskyrimo funkcija apribojo iki 0, kiekvienam elementui (`members`: `{serial: [band0, band1, band2]}` pagal kameros R, G, B juostų tvarką) ir kiekvienoje juostoje, kaip rodo kompozicija (`composite`: `{band name: share}`), o `warn` rodo dalį, kurią GUI pažymi kaip „Mažo patikimumo juostos duomenys“ (matuojami maždaug kartą per sekundę; `None`, kai neveikia tiesioginis vaizdas), o `member_ae` — kiekvienam nariui ekspozicija (`exposure_us`) ir stiprinimas (`gain_db`) jo paskutinio paskelbto kadro, paimti iš paties kadro (`read_nodes` iš `ExposureTime` / `Gain` grąžina paskutinę reikšmę, įrašytą automatinės ekspozicijos veikimo metu, o ne tai, ką ji daro). |
| `set_member_settings(serial, **kwargs)` | Įrašyti vieno nario kameros nustatymus, kaip juos perima `CameraSession.set_settings()` (ekspoziciją, stiprinimą, tikslinį ryškumą ir t. t.); narys lieka masyve. Jei serija nėra narys, sukelia išimtį `ChlorosConnectError`. Žr. [Ekspozicija masyvo nariams](#exposure-on-array-members). |
| `capability(timeout=10.0)` | Tai, ką nustatė „smart-prep“ (fiksavimo lygis, kadro dydis, `pixel_format`), veikiantis `health` blokas ir pagrindinio kompiuterio laidų pralaidumo biudžetas. Žr. [Masyvo būklė](#array-health--which-subsystem-is-losing-frames). |
| `set_target_fps(fps, timeout=10.0)` | Keisti suaktyvinimo dažnį, kai masyvas veikia, be pakartotinio prijungimo. Skaičius jį fiksuoja (dažnio valdiklis jo nekeičia); `None` grąžina jį į automatinį režimą. Grąžina `{requested, target_fps, target_fps_auto, clamped}` — `clamped` yra „True“, kai dažnis buvo sumažintas iki to, kurį gali perduoti pagrindinis kompiuteris ir tinklas. Žr. [Intervalinis vaizdų fiksavimas dronų tyrimams](#interval-capture-for-drone-surveys). |
| `grab(processing="debayered", with_metadata=False, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, bit_depth=None, band=None, align=False, refl_max=None)` | Po vieną kadrą iš kiekvienos kameros, **visus iš vieno suaktyvinimo**, kaip „numpy“ masyvus (failai nerašomi). Grąžina `GrabResult` (`dict`): `{serial: array}` arba `{serial: (array, metadata)}` su `with_metadata=True`. `.group_id` yra trigeris, kurį dalijasi kadrai; `.errors` priskiria kiekvieną neįtrauktą kamerą prie priežasties (kamera, kurios negalima aptarnauti tuo trigeriu, yra praleidžiama, niekada neužpildoma iš kitos). `processing` yra `raw` / `debayered` / `radiance` / `reflectance` / `display`. Tas pats skaitymas kaip ir projekto API [`ArrayHandle.grab()`](#one-trigger-per-read--group_id) — viena bendra įgyvendinimo versija — todėl čia veikia kiekvienas ten esantis raktinis žodis (`band`, `align`, `bit_depth`, `refl_max`) čia veikia. Reikia `numpy`. |
| `frame_stream(*, processing="debayered", fps=5.0, duration=None, count=None, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, with_metadata=False, bit_depth=None, band=None, align=False, refl_max=None)` | Iteratorius, pateikiantis vieną `grab()` rezultatą per ciklą (tik raktinių žodžių argumentai), reguliuojamas pagal `fps`, ribojamas `duration` (sekundėmis) ir (arba) `count` (`None` = neribotas). **Kiekvienas pateiktas rinkinys yra naujas trigeris:** „tick“, kurio naujausias trigeris yra jau pateiktas rinkinys, laukia kito trigerio, o ne kartoja jį, todėl srautas veikia lėčiau nei `fps` arba masyvo kadrų dažnis (pasirenkamas lėtesnis). Sukelia `FrameFetchError`, o ne nutyla: iš karto, kai kiekviena kamera dėl kokios nors priežasties atsisako, dėl kurios kitas žingsnis turėtų pasikartoti, po 60 s žingsnių be kadro ir po 60 s be naujo trigerio („nėra naujo trigerio 60 s“). Įgyvendinimas toks pat kaip `ArrayHandle.frame_stream()`. Žr. [Tiesioginiai kadrai jūsų pačių apdorojimo grandinėje](#live-frames-in-your-own-pipeline). |
| `continuous(output_dir="output", *, format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, max_captures=None, duration_s=None, daq=None, daq_tolerance_ms=None, timeout=30.0)` | Pradėti pačios užkulisinės programos nepertraukiamą įrašymą (GUI „Continuous“ režimas) → [`ContinuousHandle`](#continuoushandle). Išsaugo **kiekvieną** sinchronizuotą grupę, kurią skelbia masyvas, taip, kaip ją išsaugotų `capture()` (tos pačios eksporto kontrolės), kol bus sustabdyta arba kol `max_captures` / `duration_s` (`None` = kol nebus sustabdytas). Sukelia `ChlorosConnectError`, jei šiam masyvui jau veikia kitas procesas. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, smart=False, after_seq=None, daq=None, daq_tolerance_ms=None, timeout=300.0)` | Viena sinchronizuota fiksavimo grupė. Grąžina `CaptureResult` (kadrų žodynų sąrašą + `.skipped` + `.failed` + `.group_seq` + `.daq_files` + `.daq_matches` + `.alignment` + `.oriented`). Perduokite ankstesnį rezultatą`group_seq` kaip `after_seq`, kad gautumėte kitą sinchronizuotą grupę po jo, o ne lauktumėte naujos. Eksporto valdymo elementai žemiau. |
| `capture(..., smart=True)` | **Išmanusis fiksavimas** — laukia, kol AE stabilizuosis visose kamerose, tada suaktyvina fiksavimą. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, orient=None, timeout=120.0)` | Greičiausias fiksavimas: tik neapdoroti duomenys + priskirtas DAQ rodmuo (+ nemokamas sujungtas indeksas). Atitinka GUI mygtuką „Greičiausias fiksavimas“. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Vienkartinis / Nuolatinis / Intervalinis vienoje ribotoje kilpoje. Grąžina `list[CaptureResult]`.**Reikalingas `count` ir (arba) `duration_s`**, kad procesas būtų nutrauktas (SDK-e nėra „Ctrl+C“). Taip pat baigiasi anksčiau, po ciklo, kai ciklas praneša apie pilną diską (įrašas `.failed` su `disk_full`); tas ciklas yra paskutinis rezultatas. |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, expr=None, timeout=30.0)` | Pradėti įrašyti tiesioginį sujungto indekso vaizdą į vaizdo įrašą/GIF → `RecorderHandle`. Vaizdo įrašas yra „Motion-JPEG“ tipo `.avi` formatu (ffmpeg nereikalingas). Jei atidarytas GUI vaizdas, užkulisiai jį atkuria: `expr` (indekso išraiška masyvo juostų pavadinimuose, pvz., `"(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)"`) arba, pagal numatytuosius nustatymus, masyvo NDVI. Vienas sudėtinis įrašymo įrenginys vienam masyvui. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, orient=None, timeout=30.0)` | Pradėkite didelio kadrų dažnio „raw-Bayer“ serijos fotografavimą → `RecorderHandle`. Perdirbkite neprisijungus naudodami `build_video()`. `orient=True`: `build_video()` sukurti produktai pasukami pagal kameros orientaciją (kiekvienos kameros produktai – pagal savo kameros orientaciją, sujungtas indeksas – pagal pagrindinės kameros orientaciją); neapdoroti kadrai išlieka nepakeisti, o serijos manifestas įrašo kiekvieno elemento orientaciją. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Neprisijungus perdirbkite išsaugotą neapdorotų kadrų seriją į kalibruotą (-us) vaizdo įrašą (-us). Blokuoja, kol užduotis bus atlikta (`wait=True`), ir grąžina kompiliacijos rezultatą: `{success, outputs, errors, skipped, substitutions, notes, degraded, combined}` — žr. [kompiliacijos rezultatą](#the-build_video-result) žemiau. |
| `build_video_status(job_id, timeout=15.0)` | Patikrinti neprisijungusį kūrimo užduotį: `{running, result, error, burst_dir}`. |
| `disconnect(keep_settings=False, clear_settings=False)` | Atleisti visą masyvą — **jei šią sesiją atidarė** (`connect_array`): tuomet tai tampa `force_disconnect()`, o iššaukia `ChlorosConnectError`, kai išlaisvinimas nepatvirtinamas (`with` bloko išėjimas niekada nesukelia išimties). Naudojant `attach_array` rankenėlę, masyvas paliekamas veikti, o į stderr išvedama viena eilutė, tai nurodanti: masyvas gali priklausyti GUI arba kitam skriptui. Kiekviena kamera paliekama laisvai veikianti ir tokia, kokia buvo nustatyta prisijungimo metu; `keep_settings=True` išsaugo jos nustatymus pačios kameros atmintyje, o `clear_settings=True` atkuria gamyklinius nustatymus (žr. [Kameros atmintis](#camera-memory--keep_settings--clear_settings)). |
| `force_disconnect(timeout=60.0, keep_settings=False, clear_settings=False)` | Atleidžia visą masyvą, nepriklausomai nuo to, kas jį atidarė. Naudokite šią komandą su prijungtu rankenėliu, jei norite užbaigti masyvo veikimą (pvz., norėdami vėl prisijungti su kitokiais nustatymais). Grįžta, kai užkulisiai patvirtina atleidimą arba kai nėra ko atleisti (masyvas jau ištrintas arba neveikia jokie užkulisiai); jei atleidimas pasiekė laiko limitą arba užkulisiai atsisakė, iššaukiamas `ChlorosConnectError`. Su bet kuriuo žymekliu grąžinama tai, ką gavo kiekvienos kameros atmintis (`camera_memory`), kitais atvejais – `None`. |

`capture()` eksporto valdymas (tas pats galinis taškas, kurį naudoja GUI ir CLI):

- `processing` / `levels` — `processing="all"` (arba `levels=["raw","radiance",…]`) išsaugo kiekvieną taikytiną eksporto tipą pagal kamerą; viena `processing` reikšmė išsaugo tik tą lygį.
- `aligned` — **pagal numatytuosius nustatymus įjungta (`None`), nes Chloros programėlės to paties masyvo įrašymas yra**(jos**suderinto** fiksavimo nustatymas kiekvienam masyvui). Kiekvieno nario ne žaliavinis eksportas yra suderinamas su pagrindinės kameros kadru pagal masyvo [suderinto profilio](#array-alignment) ir apkarpomas iki narių bendro persidengimo, todėl failai yra suderinti pikselių lygiu ir gaunami tokio pat dydžio, apkarpymo bei su „`Alignment*`“ žymėmis, kokias naudoja programa (ROI srityje transformacija perskaičiuojama pagal langą); `XMP-mapir:AlignmentJSON` nurodo `"applied": true` su savo `crop_roi`. „Raw“ failai lieka neiškreipti, išlaikydami pradinį dydį, tačiau transformacija yra įrašyta metaduomenyse. `aligned=False` išsaugo kiekvieną failą pradiniame dydyje, neiškreiptą; `aligned=True` yra numatytasis variantas. `continuous()`, `capture(smart=True)`, `capture_repeated()` ir projekto lygio `ArrayHandle.capture()` / `capture_smart()` naudoja tą patį numatytąjį nustatymą. Nepriklausomai nuo to, ar taikomas iškraipymas, kiekvienas nario failas įrašo savo kameros `Alignment*` transformaciją, kai masyvas turi suderinimo profilį — išskyrus kamerą, kurios profilis nesuderinamas (ji buvo praleista arba dėl nesėkmingo sprendimo priverstinai nustatyta kaip tapatybė), taip pat etaloninė kamera, kai nėra suderinta jokia kita kamera: šios kameros neturi `Alignment*` žymių. Jei masyve nėra profilio, įrašas išsaugomas nesuderintas, o rezultate tai nurodoma: „`result.alignment`“ yra „`{"requested": True, "applied": False, "warnings": [...]}`“ – po vieną įspėjimą dėl kiekvienos priežasties, dėl kurios deformacija negalėjo būti pritaikyta (nėra profilio, elementai skirtingos skiriamosios gebos, masyvas nesinchronizuotas, kad profilio negalima pritaikyti prie kadro). `applied` tampa `True` tik tada, kai kiekvienas eksportas, kuriam buvo reikalingas iškraipymo taikymas, jį gavo. Matricos automatinis suderinimas sukuria profilį, kai ekspozicija stabilizuojasi; `ArrayHandle.calibrate_alignment()` / CLI komandos `align-calibrate` sukuria jį pagal užklausą. `result.oriented` nurodo, ar `orient=True` apdorojo produktus.
- `render_index=False` — praleisti kiekvienos kameros augmenijos indekso perdangą; pagal numatytuosius nustatymus ji atvaizduojama ten, kur sukonfigūruota.
- `daq` / `daq_tolerance_ms` — DAQ rodmenys pagal numatytuosius nustatymus išsaugomi kaip `.daq` papildomas failas, net jei nė vienam pasirinktam lygiui to nereikia; `daq=False` tuomet jį praleidžia, o `daq_tolerance_ms` nustato atitikimo toleranciją (pagal numatytuosius nustatymus – 500). `force_daq=True` yra senesnė numatytųjų nustatymų rašybos forma. Žr. [„Užfiksuotas DAQ rodmuo išsaugo“](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
- `orient=True` — paversti kiekvieno nario **produktus**(`display`, `radiance`, `reflectance`, kiekvienos kameros indeksą ir sudėtinį vaizdą) pagal kameros orientaciją po suderinimo deformacijos: pagal**pagrindinės**kameros orientaciją, kai jie buvo iškraipyti į jos kadrą (numatytasis nustatymas `aligned` — nariai lieka suderinti), kitais atvejais — pagal jų pačių kamerų orientaciją.**Numatytasis nustatymas: kiekvienas elemento failas išlaiko savo orientaciją**, kaip visada būna masyvo įrašų atveju; `raw` ir `debayered` bet kuriuo atveju išlaiko savo orientaciją. Kiekvienas failas fiksuoja kadro, kuriame yra jo pikseliai, orientaciją (`XMP-Camera:ChlorosRotation` / `ChlorosFlip*` / `ChlorosOrientationApplied` — neapdorotas failas, jo pačios kameros), o `Alignment*` XMP išlieka natūralaus kadro transformacija, pritaikyta prieš orientaciją; „raw“ failas, kuriame yra ta transformacija, taip pat įrašo `ChlorosAligned*` (pagrindinio kadro), t. y. produkto orientaciją, iškreiptą pagal pagrindinio kadro orientaciją. Apdorojimo metu kiekvienas produktas pasukamas pagal orientaciją, priklausančią jo pikseliams: pagal suderintą orientaciją, kai iškreipiamas „raw“ failas (įjungta „Array alignment“ funkcija), o pagal pačios kameros orientaciją, kai neatlieka suderinimo. `capture_repeated()` ir `capture_fastest()` jį perduoda toliau; projekto lygio `ArrayHandle.capture()` / `capture_smart()` taip pat jį perima.

**TIFF suspaudimas (tik HTTP parametras):** `ArraySession.capture()` nesiunčia `compression` rakto, todėl taikomas užkulisio numatytasis nustatymas — `POST /api/camera/array/capture` nuskaito `compression` kūno parametrą, `"none"` pagal numatytąjį nustatymą: nesuspaustas, jutiklio pikseliai tiksliai tokie, kokie nuskaityti (~6,3 MB vienam pilnos raiškos kadrui). `"deflate"` (be nuostolių „zlib“ L1 + horizontalusis prognozuotojas, ~4,1 MB/kadras) rašo mažesnius failus lėčiau — abu variantai yra be nuostolių ir importuojant nuskaitomi identiškai. SDK nepateikia jokių „kwarg“ parametrų šiam režimui; išeitis yra `chloros-cli lattice array-capture --compression deflate` arba neapdorotas HTTP. DEFLATE užima Python GIL, todėl suspaustų duomenų rašymas nėra lygiagretinamas tarp atskirų kamerų rašymo sriegių — norint užtikrinti nepertraukiamą pilnos raiškos įrašymą jutiklio dažniu, reikia naudoti nesuspaustą numatytąjį režimą. Išsamiau: [CLI žinynas → array-capture](cli-reference.md).**Iš eilės einančios grupės (HTTP):** galinis taškas grąžina `group_seq`; ciklas, kuris kitame užklausime jį nusiunčia atgal kaip `after_seq`, gauna kitą sinchronizuotą grupę — įskaitant tą, kuri buvo paskelbta, kol buvo išsaugomas ankstesnis įrašas — vietoj to, kad lauktų grupės, paskelbtos po užklausos, o `skipped_groups` praneša apie visas grupes, kurias ciklas praleido. `capture_repeated`, CLI komandos „`--continuous`“ ir GUI „Nuolatinis režimas“ visos tai atlieka; intervalo perėjimai išlieka aktualūs kiekvieną numatytą momentą.**Eksporto perrašymai pagal narį (tik HTTP):**tas pats galinis taškas taip pat priima `exclude_serials` (sąrašas — pašalinti narius iš išsaugoto rinkinio; masyvas vis dar veikia kaip viena sinchronizuota grupė, o pašalinti nariai grąžinami `excluded`), `serial_levels` (`{serial: [level tokens]}` peržymėjimai kameros lygiu) ir `serial_index` (`{serial: bool}` peržymėjimai kameros indekso perdangos lygiu). Tai yra GUI atitikmenų kūno parametrai ir**dar nėra SDK kwargs**; nariai, kurių nėra žemėlapiuose, grįžta prie visam masyvui taikomų `levels` / `render_index`.

##### Praleistų kamerų tikrinimas — `CaptureResult.skipped`

`ArraySession.capture()` grąžina `CaptureResult`, kuris yra `list` paklasė: peržiūrėkite jį, indeksuokite, `len()` — visi esami modeliai ir toliau veikia. Naujas kodas gali patikrinti `.skipped` atributą, kad pamatytų, kurios kameros buvo išskirtos ir kodėl. Dažniausias atvejis – tai RGB kameros mišriojo filtro matricoje, kai prašoma `processing="radiance"` arba `"reflectance"` — spinduliavimas pagal „Bayer“ modelį plačiajuosčio dažnio jutikliui neturi prasmės, todėl apdorojimo sistema praleidžia tas kameras, o ne generuoja beprasmiškus duomenis.

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

Priežasties žymos atitinka šį šabloną: `<level>-not-applicable-to-rgb-cam` (po vieną įrašą kiekvienam praleistam lygiui, kiekvienas su `level`). Kamera, kurios kalibravimo paketas nėra įkeltas, praleidžia `radiance` / `reflectance` su `<level>-needs-calibration (connect the camera so its cal pack loads)`, o jei nėra tinkamų DAQ atspindžio rodmenų, praleidžia su `reflectance-skipped-no-dls`. Kiti su atspindžiu susiję praleidimai yra `reflectance-skipped-no-fresh-dls` (nėra naujų žemyn nukreiptų spindulių rodmenų), `reflectance-skipped-bound-daq-unavailable (…)` (nebuvo galima pasiekti priskirto DAQ), ir `dls-uncalibrated-band-<nm>` — juosta daugiausia yra už DAQ šviesos jutiklio radiometriškai kalibruoto diapazono ribų (~374–974 nm), todėl atmetamas absoliutus, DAQ pagrįstas atspindžio padalijimas, o kadras grįžta prie jutiklio atsako. Tarp parduodamų SKU tik F988 sukelia šią klaidą; šiai kamerai palaikomas darbo srautas yra atspindžio plokštės darbo srautas.

##### Failai, kurių nepavyko įrašyti — `CaptureResult.failed`

Įrašymas, kurio metu pavyko įrašyti tik dalį failų, vis tiek grąžina: sąraše yra visi failai, kurie **buvo** įrašyti, o `.failed` išvardija likusius, po vieną žodyną kiekvienam failui su `serial` (`None` – sujungtam kompozitui ir masyvo `.daq` „sidecar“), `level` (`raw`, `radiance`, …, `index`, `composite`, `daq`, arba `match` — `.daq` kilmės įrašas, kai `.daq` buvo įrašytas, bet jo „sidecar“ nebuvo) ir `error`. Jei diskas yra pilnas, įraše taip pat nurodytas `disk_full: True`; kiekvienas vėlesnis įrašymas į tą diską žlugtų taip pat, todėl `capture_repeated()` ten ir baigiasi. Dėl dalinio įrašymo išimtis nekelia, o failas „`.failed`“ nebuvo paliktas diske pusiau įrašytas. „`.failed`“ yra tuščias, kai įrašymas vyksta be klaidų.

```python
result = arr.capture("output/", processing="all")
for f in result.failed:
    print(f"SN:{f['serial']} {f['level']} not written: {f['error']}")
if any(f.get("disk_full") for f in result.failed):
    raise SystemExit("drive full -- free space and capture again")
```

##### DAQ, skaitantis įrašą, išsaugo — „`.daq_files`“ ir „`.daq_matches`“

Kiekvieną kartą, kai DAQ yra susietas su kamera ar matrica arba įtrauktas į grupę, kiekvienas įrašas išsaugo jo naudotą žemyn nukreiptą rodmenį: `.daq` įrašų aplanko `daq/` pakataloge, kartu su `.match.json` ir `.cameras.json` (kurio rodmenys tai yra). Tai galioja ir neapdorotam, ir debayered įrašui, ir failas yra tas pats, nepriklausomai nuo to, ar įrašas buvo atliktas per GUI, CLI ar SDK. `daq=False` praleidžia jį įrašui, kurio lygiai nenaudoja DAQ (tačiau `radiance`, `reflectance` arba `display` lygis jį vis tiek įrašo); `daq_tolerance_ms` perkelia leistinos nuokrypos ribą tam įrašo, o ne naudotą rodmenį. Kuris DAQ: tas, kuris susietas su kamera ar matrica (GUI šviesos jutiklio pasirinkimas, projekto susiejimas arba `chloros-cli daq pool-bind`), arba naujausias, kurį turi užkulisinė sistema. CLI nurodo valdiklius `--no-daq` ir `--daq-tolerance-ms` ([CLI nuoroda](cli-reference.md#the-daq-reading-a-capture-saves)).

```python
r = cam.capture("out", levels=["raw"])      # a CameraSession; ArraySession results match
for m in r.daq_matches:                      # one per .daq in r.daq_files
    print(m["daq_file"], m["gap_ms"], m["in_tolerance"])
```

Užfiksavimas išsaugo naudotą šviesos jutiklio rodmenį kaip `.daq`, o šalia jo – `<file>.daq.match.json`, nurodantį, kaip rodmuo buvo susietas su ekspozicija: `method`, `gap_ms` (nuo ekspozicijos iki artimiausio DAQ rodmens) ir `in_tolerance` (per 500 ms arba užfiksavimo `daq_tolerance_ms`). Atspindžio failo XMP duomenyse pateikta ta pati informacija kaip ir `Chloros:DlsMatchMethod`, `Chloros:DlsMatchGapS` bei `Chloros:DlsInTolerance`.

Užfiksavimas laukia pirmojo DAQ rodmens po ekspozicijos, kad galėtų sujungti rodmenis iš abiejų pusių (`method` `interpolated`). DAQ-E siunčia rodmenį kas 0,4 s dienos šviesoje ir kas 1–2 s patalpoje, todėl `capture()` grąžinamas praėjus maždaug pusantro tokio intervalo; pati ekspozicija nėra atidėliojama. Nesiunčiantis DAQ-E įrenginys nelaukiamas. DAQ-E rodmuo yra šviesos vidurkis per laikotarpį nuo ankstesnio rodmens, todėl jis įrašomas to laikotarpio viduryje: `gap_ms` matuojamas pagal tą vidurį, o `.match.json`'`bracket_stamps_ns` pateikia pačių rodmenų laiko žymes. `ArraySession.continuous()` laukia, nesulėtindamas duomenų surinkimo. `capture_repeated()` atgaliniai-atgaliniai praėjimai nelaukia, todėl išlaiko savo dažnį ir yra suderinami su artimiausiu rodmeniu prieš ekspoziciją.

Rodmuo niekada nepasensta, todėl nė vienas kadras neprarandamas dėl pavėluoto rodmens — vietoj to jis pažymimas:

- **DAQ srautas sustojo** — naudojamas naujausias rodmuo, o `gap_ms` nurodo, kiek laiko jis senas (`in_tolerance: false` – praėjo daugiau nei 500 ms).
- **Nėra DAQ, iš kurio galima nuskaityti**— atjungtas (`disconnect()`, `chloros-cli daq pool-disconnect`) arba neįmanoma prisijungti prie susieto DAQ — pakartotinai naudojamas paskutinis kameros rodmuo, susietas su**šiuo** ekspozicijai: `method`, `retained`, `gap_ms` iš šios ekspozicijos, `in_tolerance` perskaičiuojamas, o `retained_from_epoch_ns` nurodo ekspoziciją, su kuria jis buvo pirmą kartą suderintas. Kamera, kurios rodmenys niekada nebuvo užregistruoti, vis tiek negauna atspindžio koeficiento, o rezultatas paaiškina, kodėl (`reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, arba kad užfiksavimas buvo atmestas).

Failai, kuriuos užfiksuotas vaizdas išsaugo Chloros atidaryto projekto aplanke, prisijungia prie to projekto failų sąrašo, kaip ir užfiksuotas vaizdas programoje (kitur išsaugoti failai lieka nepaliesti): `CameraSession.capture()`, `ArraySession.capture()` (taip pat ir `smart=True`) ir `capture_cameras_transient()`. Rezultatas nurodytas `.project_add` (`capture_cameras_transient()`: žodyno `project_add`) -- `held`, `files` ir užkulisio `message`; `held` yra `True`, kol vyksta apdorojimo ciklas, o failai sujungiami jam pasibaigus. `None`, kai į projektą nebuvo įtrauktas joks failas.

`CameraSession.capture()` ir `ArraySession.capture()` grąžina juos kaip rezultato `.daq_files` ir `.daq_matches`, nes HTTP užfiksuoti atsakymai (`/api/camera/<serial>/cli-capture`, `/api/camera/capture`, `/api/camera/capture-all-synced`, `/api/camera/array/capture`) yra „`daq_files`“ ir „`daq_matches`“, po vieną kiekvienam „`.daq`“: „`daq_file`“, `cameras`, `method`, `gap_ms`, `in_tolerance`, `tolerance_ms` ir `stream_fault` — pačios DAQ srauto klaida, tikrinama tik tada, kai rodmenys neatitinka leistinų nuokrypių. Prieš pasikliaujant užfiksuoto atspindžio duomenimis, patikrinkite `in_tolerance` jo `.match.json` arba XMP, arba DAQ [`status()`](#daqsensorsession-methods).

`processing` lygiai:

| Lygis | Išėjimas |
| --- | --- |
| `"raw"` | Vienkanalis „Bayer“ (mono kameros: viena juosta) tiesiai iš jutiklio. |
| `"debayered"` *(numatytasis SDK nustatymas)* | 3 kanalų spalvotas: kalibravimo paketo tamsos nuokrypis (DSNU) atimtas iš neapdorotų duomenų, tada taikomas „OpenCV“ kraštų atpažinimo demozėjimas — be linijiškumo LUT, plokščio lauko ar atskyrimo (mono kameros: 1 kanalas pilkosios skalės). Išsaugotas TIFF failas yra **RGB**kanalų tvarka (fotometrinis RGB, todėl bet kuri vaizdų peržiūros programa rodo tikrąsias spalvas); atmintyje esantys masyvai, kuriuos grąžina `grab()` / `frame_stream()`, yra OpenCV**BGR** tvarka — norėdami palyginti su išsaugotu failu, apverskite kanalus (`frame[..., ::-1]`). `chloros-cli process` įrašo tą patį rezultatą kaip ir `Debayered_Images`. |
| `"radiance"` | float32 W/m²/sr/nm, apskaičiuota per visą radiometrinę grandinę. Tik daugiaspektrinėms kameroms — RGB kameros praleidžiamos. |
| `"reflectance"` | uint16 0..32768 (suderinama su „Pix4D“); reikalauja tiesioginio DAQ susiejimo absoliučiam etalonui. Tik daugiaspektrinė. |
| `"display"` | Visas grandinės suderinimas su GUI peržiūra (CCM + WB + gama pagal kameros profilį). |
| `"all"` | **Vienas failas kiekvienam taikytinam lygiui** kiekvienai kamerai (atitinka GUI „Capture All“ / CLI-numatytąjį nustatymą). Grąžintas `CaptureResult` tada turi po vieną kadrų žodyną kiekvienam `(cam, level)`, su lygiu kiekviename žodyne; netaikomi lygiai rodomi `.skipped`. DAQ, skaitantis naudotą įrašą, išsaugomas kaip `.daq` papildomas failas. |

> **Pastaba — numatytasis nustatymas skiriasi nuo CLI.** „`ArraySession.capture()`“ numatytasis nustatymas yra „`processing="debayered"`“; komandos „`chloros-cli lattice array-capture`“ numatytasis nustatymas yra „`processing="all"`“. Norėdami atkartoti SDK ir GUI daugiapakopį išsaugojimą, aiškiai perduokite „`processing="all"`“ iš SDK.

##### Masyvo įrašymo failų pavadinimai

Kiekvienas lygis išsaugomas savo atskirame „`output_dir`“ pakatalogyje (`raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/`, skirtas `display`, `index/`, `composite/`). Kiekvieno failo pavadinime nurodoma kameros serijos numeris `SN<serial>-<filter>`: serijos numeris, po to – kameros modelio filtro dalis — `SN214000533-FOCN` atitinka `M3C-L87-FOCN`, `SN261902142-F450` – modelio `M3M-L87-F450` atveju – sujungta su `-`, todėl pavadinime yra tie patys „`_`“ atskirti laukai kaip ir anksčiau. Kamera, kurios modelio negalima nuskaityti, yra tik „`SN<serial>`“, o ankstesnių versijų įrašų pavadinimuose nėra filtro. Failo pavadinimas priklauso nuo to, kiek lygių buvo paprašyta nario:

| Nario prašymas | Failo pavadinimas | Pavyzdys (`reflectance`, numatytasis `format="tiff"`) |
| --- | --- | --- |
| **Vienas lygis** (`processing="reflectance"` arba vieno elemento `levels`) | `sync_<timestamp>_SN<serial>-<filter>.<format>` — be lygio priesagos, kaip ankstesnėse versijose, todėl esami skriptai ir toliau veikia | `reflectance/sync_<ts>_SN214000533-FOCN.tiff` |
| **Keli lygiai** (`processing="all"` arba `levels` su daugiau nei vienu elementu) | `sync_<timestamp>_SN<serial>-<filter>_<level>.<format>` | `reflectance/sync_<ts>_SN214000533-FOCN_reflectance.tiff` |

[Fiksavimo kaukė](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) eina po produkto pavadinimo, `clamp_masks/<product folder>/<product name>_clampmask.tif`: vienolygio atspindžio fiksavimas įrašo „`clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif`“, o „`processing="all"`“ įrašo „`clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif`“. Priesagos taisyklė nustatoma kiekvienam elementui atskirai pagal prašytus lygius, todėl praleistas lygis (pvz., atspindžio įrašymas su RGB kamera) jos nekeičia. Geriau skaitykite kelius iš grąžintų kadrų, o ne kurkite juos patys. Vienos kameros įrašymas (`CameraSession.capture()`) visada prideda lygį: `capture_<ts>_SN<serial>-<filter>_<level>.tiff`.

##### PNG / JPEG: imami tik ekrane rodomi ir indeksuoti produktai

PNG arba JPEG formatuose vienam kanalui tenka 8 bitai. Prašymas įrašyti vaizdą su lygiu, kurio šis formatas nepalaiko, atmetamas dar prieš pradedant įrašyti, o pranešime nurodomas lygis ir TIFF kaip formatas, kuris jį palaiko:

- **`radiance`**(float32) ir**`reflectance`**(uint16, `32768` = 1,0) — taip pat ir `processing="all"`, kuris juos apima. SDK tai tikrina pagal argumentus: `capture()` sukelia**`ValueError`** be užklausos (`CameraSession`, `ArraySession`, `CameraHandle`, `ArrayHandle`, `capture_smart`).
- **`raw` / `debayered` iš kameros, transliuojančios daugiau nei 8 bitus** (12 bitų M3C, Mono12 M3M) — užkulisinė sistema juos atmeta, žinodama kiekvienos kameros bitų gylį: HTTP 400 su „`error_code`“ ir „`FORMAT_CANNOT_HOLD_LEVEL`“, iškeltas kaip „`ChlorosConnectError`“ (`CameraSession` / `ArraySession`) arba `RuntimeError` (`CameraHandle` / `ArrayHandle`). Vieno lygio `debayered` vienos kameros įrašas užfiksuoja 8 bitų kadrą PNG / JPEG formatu ir nėra atmestas.

Radiometrinius duomenis išsaugokite kaip „`format="tiff"`“ (`ext=".tiff"`); PNG / JPEG naudokite „`display`“ ir indeksiniams atvaizdams.

#### Kaip veikia automatinė ekspozicija

- **Pirmiausia ekspozicija, paskui stiprinimas.** Kai stiprinimas nustatytas į „Auto“, jis išlieka 0 dB, kol ekspozicija pasiekia didžiausią leidžiamą vertę.
- **Rankinė ekspozicija palieka stiprinimą automatiniame režime**, kol nepakeisite stiprinimo arba nenustatysite jo į rankinį režimą. Grįžus į automatinį režimą, stiprinimo riba lieka tokia pati kaip anksčiau.
- **Maži šviesūs plotai nepatamsina vaizdo.** Atspindys, sudarantis iki 1 % kadro, ignoruojamas, o žinomų kalibravimo taikinių plokštėse – iki 0,5 % plokštės ploto.
- **DAQ ekspozicijos riba taikoma tik tol, kol matomas kalibravimo taikinys.** Tai apsaugo baltą plokštelę nuo apkarpymo; kai plokštelė nematoma, kamera eksponuoja pagal sceną.
- **Pastovi šviesa išlieka stabili, o pasikeitusi šviesa stabilizuojasi per kelias sekundes.** Esant pastoviai šviesai, ekspozicija ir jos riba išlieka nepakitusios, o po didelio šviesos pokyčio vaizdas stabilizuojasi per kelias sekundes. Išblukimas nebeapriboja ekspozicijos visam laikui, o pasikeitus tiriamajai sričiai (ROI) ekspozicija vėl nustatoma.
- **Ryškumo tikslas išlieka pasiekiamas.** Jis neauga, kol kamera yra pasiekusi ekspozicijos ribą ir jos stiprinimas negali didėti, todėl sugrįžęs šviesos šaltinis nesukelia kadro peršvietimo.
- **Kamera, kurios automatinė ekspozicija nustoja reaguoti, išlaikoma toje apšvietimo lygyje, kuriame ji yra.** Ryškioje saulėje kamera gali nukristi iki trumpiausios ekspozicijos ir ten likti su tamsiu kadru, nors visi jos nustatymai rodo normalius rodmenis. Chloros stebi kadrą, išlaiko tą kamerą tame apšvietimo lygyje, kurį ji išlaikė, ir paleidžia ją, kai apšvietimas pasikeičia. Tamsus arba uždengtas šviesos jutiklis to netrikdo.

#### Ekspozicija masyvo nariuose

„LATTICE“ kamera apriboja „`ExposureTime`“ iki vieno kadro dažnio periodo. Kol masyvas transliuoja, Chloros paleidžia kiekvieną elementą su įjungtu „`AcquisitionFrameRateEnable`“, dažniu, šiek tiek viršijančiu suaktyvinimo dažnį, todėl elemento ekspozicijos riba yra šiek tiek mažesnė už suaktyvinimo periodą **bet kuriame ROI**:

- Elemento kadro trukmė yra lygi suaktyvinimo trukmei, atėmus 15 % trukmės atsargą (mažiausiai 3 ms, daugiausiai pusę trukmės), o jo ekspozicijos riba yra lygi tai kadro trukmei, atėmus apie 0,1 ms. Pavyzdys: 10 fps suaktyvinimo dažnis (100 ms) suteikia nario dažnį apie 11,8 fps ir ekspozicijos ribą apie 85 ms.
- **Rankinis `ExposureTime`, ilgesnis už tą ribą, sulėtina suaktyvinimą, kad jis tilptų.** Chloros pirmiausia atidaro to nario ribą šiai vertei ir leidžia lėtesniam dažniui veikti kelis suaktyvinimo periodus (įrašymas gali užtrukti kelias sekundes ilgiau), tada jį įrašo, ir trigeris suveikia ne greičiau, nei leidžia ekspozicija. Automatinio dažnio masyve anksčiau buvęs dažnis grįžta, kai kiekvienas narys, kurio rankinė ekspozicija jį sulėtino, vėl pereina į automatinę ekspoziciją. Vertė, kurios pati kamera negali užfiksuoti, vis tiek atmetama, o dabar priimamas diapazonas nurodomas klaidos pranešime, o nario automatinė ekspozicija grąžinama, jei ji buvo išjungta tik tam rašymo veiksmui. Iki versijos 1.3.0 nedidelis ROI apribodavo kiekvieną narį iki paties jutiklio laisvojo veikimo periodo, nepriklausomai nuo sužadinimo dažnio: 4,9 ms esant 768 × 304, todėl nė vienas elementas negalėjo būti pašviesintas.
- Chloros niekada nesutrumpina rankinės ekspozicijos, kad ji atitiktų greitesnį sužadinimo dažnį. Automatinis dažnis neviršija to, ką leidžia rankinės ekspozicijos; fiksuotas dažnis, greitesnis nei leidžia ekspozicija, nustato tam nariui pakankamai ilgą ribą, ir jis priima sužadinimus ne greičiau, nei leidžia jo ekspozicija. Sumažinkite ekspoziciją arba fiksuotą dažnį, kai kiekvienas narys turi reaguoti į kiekvieną sužadinimą.
- **Atgalinis nuskaitymas:** nuskaitant elemento mazgus rodoma „`AcquisitionFrameRateEnable` True“ ir „`AcquisitionFrameRate`“ elemento dažniu; įjungimas išsijungia, kai masyvas sustoja.
- **Masyvas valdo šiuos du mazgus. Nerašykite `AcquisitionFrameRateEnable` arba `AcquisitionFrameRate` į masyvo narį** — vietoj to nustatykite masyvokūno greitį naudodami `target_fps` / `set_target_fps()`.

Nario ekspoziciją įrašykite naudodami `arr.set_member_settings(serial, exposure_time=20000)` (raktiniai žodžiai: `CameraSession.set_settings()`; `chloros-cli lattice exposure --serial` veikia taip pat). Narys lieka masyve. Neatidarykite masyvo nario naudojant `connect_camera()`: „connect“ atmeta kamerą, kuri jau yra masyve, o „`CameraSession`“, kurį jis grąžintų, pašalintų kamerą iš jos masyvo, kai ji atsijungia. „`ArraySession.status()["member_ae"]`“ praneša apie kiekvieno elementoekspoziciją ir stiprinimą iš naujausio kadro.

### Vienas projekto įrašas, kiekvienam režimui

Projekto aplanke saugomi kiekvieno įrenginio nustatymai: `cameras.json` — kiekvienos kameros (`cameras[].settings`, skirta vienai kamerai ir kiekvienam masyvo nariui) bei kiekvieno masyvo bendrieji nustatymai (`arrays[].capture`: formatas, ROI, binningas, suaktyvinimo dažnis, laidų biudžetas) — bei `sensors.json`, kiekvieno šviesos jutiklio (integracijos laikas, kadro vidurkis, riba). Programa, CLI, SDK ir MCP serveris visi skaito ir rašo tuos pačius failus tuo pačiu būdu:

- **Prie projekto prijungtas įrenginys yra išsaugomas tame projekte ir į jį įrašomi visi pakeitimai.** „Prie projekto“ reiškia, kad projektas atidarytas užkulisiuose (programėlėje) arba — per `ChlorosProject` (`open_project()`), kuris užkulisiuose nurodo savo aplanką (`project_path`). Kamera, matrica ar šviesos jutiklis, kurių projektas dar neturi, prijungus juos įtraukiami į jo failus (`connect_camera()`, `connect_array()`, šviesos jutiklio prijungimas, CLI, programėlė, `chloros-mcp`) su atitinkamais nustatymais. Perėjimas iš bet kurio režimo (`set_settings()`, `ArraySession.set_member_settings()`, `set_target_fps()`, masyvo nustatymai, `set_cap()`, CLI, programėlės valdikliai) po akimirkos išsaugomi to projekto failuose. Kameroje nustatytas ekspozicijos, stiprinimo ar AE tikslas išsaugomas kaip užfiksuotas, todėl vėl atidarius projektą jis išlieka toks pat.
- **Atidarius projektą, jo įrenginiai nustatomi pagal projektą** bet kuriame režime: kamera, matrica ar šviesos jutiklis, prijungti be savų nustatymų, gaunaišsaugotus nustatymus (`lattice cam-connect` / `array-connect`, kai projektas atidarytas; `chloros-cli project connect`, `open_project().connect_all()` – atidarius programą iš naujo). Matrica, prijungta su nauju identifikatoriumi, atnaujina projekto išsaugotą matricą su tomis pačiomis kameromis; matrica su kitomis kameromis pridedama šalia jos.
- **Programos išsaugojimas niekada nepakeičia įrenginio tiesioginių nustatymų** senesne jų kopija ir išsaugo kiekvieno elemento perrašymus, kuriuos išsaugojo CLI arba SDK iškvietėjas.
- **Jūsų nustatytas pikselių formatas yra tas, kurį projektas išsaugo.** Formatas, nustatytas masyvo nustatymuose, naudojant `--pixel-format` arba `connect_array(pixel_format=)`, įskaitant 8 bitų, yra išsaugomas ir tiksliai atkuriama jungtis. Jei nieko nenustatyta, kiekviena kamera prisijungia naudodama savo 12 bitų numatytąjį nustatymą (`BayerRG12`, `Mono12`, jei tai monochrominis jutiklis). Formato pavadinimas reiškia jo bitų gylį; šeima priklauso jutikliui, todėl spalvota kamera niekada nebus nustatyta į „Mono“ formatą, o monochromatinė kamera – į „Bayer“ formatą, o eilutė „`[PixelFormat <serial>] ... written`“ tai nurodo, kai formatas buvo susietas. 8 bitų įrašo eksportai yra 8 bitų, įskaitant atspindžio duomenis: perskaluoti ir pažymėti savo „`PixelScale`“.
- Prietaisas, prijungtas nepriklausomai nuo jokio projekto, nieko.
- **Įrašas priskiriamas projektui, sukurtam po jo; atidarius projektą, įrašas lieka su savo projektu.**Kai sukuriama — `create_project()`, `chloros-mcp` arba programėlės funkcija**Naujas projektas**— arba dubliuojamas (programos funkcija**„Duplikuoti“**, kuri atidaro kopiją), o įrašas vis dar rašo į anksčiau atidarytą projektą, įrašas tęsiasi, o tai, kas įrašoma, patenka į naują projektą, toje pačioje vietoje jame (žr. žemiau). Atidarius esamą projektą — `ChlorosProject.process()` (kuris atidaro projektą užkulisiuose), `chloros-cli process`, programėlės funkcija**Atidaryti projektą** — įrašas toliau tęsiasi tame projekte, į kurį jis įrašomas: jis ten baigiasi, o jo failai prisijungia prie to projekto failų sąrašo (programėlė sustabdo kiekvieną įrašymą prieš atidarydama projektą).
  - `ArraySession.record()` ir `ArraySession.burst()`: vaizdo įrašas arba serijos aplankas su visais kadrais perkeliama ten visa, kai įrašymas sustabdomas. Sustabdymo rezultate nurodomas naujas kelias (`out_dir`, `video_path`), o serijos surinkimas vykdomas iš jo. Jei diskas atsisako perkelti, įrašas lieka ten, kur buvo įrašytas, ir rezultatas tai nurodo (`relocation_error`). Sustabdyto serijos įrašo neapdoroti kadrai ir `.daq` įtraukiami į to projekto, kuriame jis atsiduria, failų sąrašą; vaizdo įrašas nėra pridedamas prie projekto failų sąrašui.
  - `ArraySession.continuous()`: kiekvienas įrašas, padarytas po pakeitimo, išsaugomas naujame projekte; anksčiau išsaugoti įrašai lieka ten, kur yra.
  - `DAQSensorSession.record_start()` (ir pačios programėlės šviesos jutiklio įrašas): `.daq` perkeliama į naujo projekto `light_sensor` aplanką, kai įrašymas baigiasi; `record_stop()` grąžina naują kelią, o failas įtraukiamas į to projekto failų sąrašą.

  Įrašas, padarytas į aplanką už atidaryto projekto ribų, lieka ten, kur yra, o „`start_camera_continuous()`“ / daugiasrautinės užduotys išlaiko joms paskirtą aplanką.
- **Programoje įrašai, padaryti po naujo projekto sukūrimo, patenka į jį.** Sukuriant ar kopijuojant projektą, visi vykdomi įrašymo procesai ir įrašai tęsiami, o kiekvienas po to atliktas įrašymas („Įrašyti viską“, „Pradėti įrašyti“) išsaugomas naujame projekte ir įtraukiamas į jo failų sąrašą. Atidarius esamą projektą programoje, visos operacijos pirmiausia sustabdomos.
- **Projektas neperkeliamas, kol vyksta įrašymas.**Perkėlus atidarytą projektą į kitą darbo katalogą (programos**Nustatymai → Darbo katalogas**; joks SDK-skambutis neperkelia projekto, HTTP klientas, siunčiantis duomenis į `/api/move-project`, tai daro) atmetamas su HTTP 409 `PROJECT_RECORDING`, kol į jį rašoma atliekant masyvo įrašymą ar seriją, masyvo nepertraukiamą fiksavimą, kameros nepertraukiamą / daugiasrautinį užduotį arba šviesos jutiklio įrašymą: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.`

Tai nesusiję su tuo, kokie kameros nustatymai išlieka atjungus (žr. [Kameros atmintis](#camera-memory--keep_settings--clear_settings)): projektas pats nustato savo kameras, kai yra atidaromas.

### Kameros atmintis — `keep_settings` / `clear_settings`

Prijungus masyvą, jo kameros perima masyvo nustatymus, kuriuos vėliau galite keisti. Kai Chloros atjungia kameras — `ArraySession.disconnect()` / `force_disconnect()`, laikinosios funkcijos pabaigoje, sustabdžius `start_camera_continuous()` — kiekviena kamera grąžinama į tą būseną, kurioje ji buvo prieš prijungimą: su trigerio ir sinchronizavimo linijos laidyba, PTP ir kameros nustatymais — `ExposureAuto` / `GainAuto` (ir rankinis `ExposureTime` / `Gain`), `TargetBrightness` bei AE ribas ir slopinimą, baltos spalvos balansą, juodos spalvos lygį, `PixelFormat`, pikselių sujungimą ir ROI, spalvų apdorojimo grandinę (`GammaEnable` / `Gamma`, `LUTEnable`, `ColorTransformationEnable`), `ReverseX` / `ReverseY`, `UserSetSelector`, PTP `PtpSlaveOnly` / `PtpDomainNumber`, paketo dydis (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`), stimuliavimo dažnis (`DeviceLinkThroughputLimitMode` / `DeviceLinkThroughputLimit`, `GevSCPD`, `GevSCFTD`), duomenų fragmentai (`ChunkModeActive`, kiekvienas `ChunkEnable` po savo `ChunkSelector`), įvesties trigerio išlyginimas, o masyvas pateikia pavaldinio sinchronizacijos liniją (`LineFilterSelector` / `LineFilterWidth`) ir duomenų surinkimo kadrų dažnį (`AcquisitionFrameRate` tik tada, kai jo įjungimas buvo įjungtas: kai jis išjungtas, dažnis rodo didžiausią vertę, kurią leidžia sinchronizavimas, o ne nustatymą). Rašoma tik tai, kas skiriasi: kada kamera sustojo pirmą kartą, sinchronizavimas ir kadrų dažnis prieš ekspoziciją, kuriai reikalingas jų kadro laikotarpis.

Norint naudoti kameras ne projekto ribose, du argumentai įrašo į kameros **pačios atmintį** — tai, su kuo ji įsijungia, bet kurioje programinėje įrangoje (`disconnect()` / `force_disconnect()`, kiekvienoje laikinojoje funkcijoje, `start_camera_continuous()` ir `ChlorosProject.disconnect_all()`; MCP: tie patys argumentai kaip `disconnect_device` ir laikinosiose priemonėse; CLI: `--keep-settings` / `--clear-settings`):

- **`keep_settings=True`** išsaugo kiekvienos kameros nustatymus, kokie jie buvo sesijos pabaigoje, į jos atmintį (UserSet2, nustatytas kaip paleidimo rinkinys). Kameros vis dar paliekamos veikti laisvai (`TriggerMode` `Off`), sinchronizavimo linijos laidai paliekami taip, kaip rasti. Kamera, neturinti „UserSet2“, išsaugo nustatymus į „UserSet1“; šis kompiuteris tai įsimena, todėl Chloros masyvo savireguliacija — kuri atkuria „UserSet1“, nukrypusią nuo Chloros bazinės linijos — jos neliečia.
- **`clear_settings=True`** atkuria kiekvienos kameros atmintį į gamyklinius nustatymus: Chloros bazinę liniją, kurią įtvirtina masyvo savikontrolė — gamykliniai numatymai su „`TriggerMode`“, „`Off`“, „`TriggerSource`“, „`Line2`“, automatine ekspozicija, RGB kameros kalibruotu baltos spalvos balansu išsaugotas — „UserSet1“, sudarytas paleidimo rinkinys.
- Ne abu (`ValueError`; užkulisinė sistema atsako 400). Atsakyme pateikiamas `camera_memory`, kiekvienai kamerai – `{"saved_to": "UserSet2" | "UserSet1" | None, "errors": [...]}` arba `{"cleared": bool, "errors": [...]}` — `force_disconnect()` jį grąžina; užduoties atveju jis pateikiamas su `stop()`.

Programa šių duomenų nepateikia.

**Kiekvienas išėjimo būdas grąžina kameras į pradinę padėtį.**Grąžinimas į pradinę padėtį vykdomas kiekvienu išėjimo keliu: `CameraSession.disconnect()` (programos kamera**X**), programos**X** masyve arba bet kuriame masyvo elemente (visas masyvas atlaisvinamas), uždarant programą (užkulisinė dalis turi iki 20 s, kad sugrąžintų visas kameras, masyvus ir veikiančius nepertraukiamo įrašymo procesus; vis dar veikianti laikina funkcija arba vienkartinis iškvietimas sustoja artimiausiame saugaus sustabdymo taške, pirmiausia sugrąžina savo kameras, iki 8 s, ir atsako HTTP 503 `SHUTTING_DOWN`; vis dar veikiantis `connect_array()` sustoja prieš savo kitą kamerą ir grąžina kameras, kurias buvo atidaręs, iki 10 s, ir sukelia `ChlorosConnectError` (HTTP 503 `SHUTTING_DOWN`); o ta, kuri pasiekia sistemą išjungimo metu, nieko neatidaro: HTTP 503 `SHUTTING_DOWN`), taip pat `chloros-cli` komandą, kuri nutraukiama arba kurios terminalas uždaromas (jos užduotis sustabdoma, o kameros pirmiausia grąžinamos į pradinę būseną). Užkulisinė programa, kuri sugenda arba yra priverstinai nutraukta, negali nieko grąžinti; išjungus ir vėl įjungus maitinimą, kiekviena kamera grįžta į savo atmintį.**Projektas pats nustato savo kameras.** Kai masyvas prisijungia per projektą — atidarytą programos projektą arba „`ChlorosProject`“, kuris nurodo savo pavadinimą užpakalinės sistemos modulio (`project_path`) atžvilgiu — kiekvienas masyvo narys gauna nustatymus, kuriuos projektas jam išsaugojo: ekspoziciją, stiprinimą arba AE tikslą, priskirtą šiai kamerai (laikomą kaip jūsų, todėl automatinė ekspozicija jos nekeičia) ir jos baltos spalvos balanso režimą (Chloros korekcija; pačios kameros baltos spalvos balanso funkcija išlieka išjungta), nepriklausomai nuo to, kas saugoma kameros atmintyje.

### Fotografavimo režimai ir įrašymo įrenginiai

Masyvo paviršius atspindi GUI fotografavimo skydelį: vienkartinio / serijinio / intervalinio / greičiausio užrakto režimai, taip pat du įrašymo įrenginiai (tiesioginis sudėtinis vaizdo įrašas ir neapdorotų kadrų serija → perdirbimas neprisijungus).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Continuous — every synced group for 60 s, saved by the backend itself
    with arr.continuous("run1/", processing="raw", duration_s=60) as run:
        run.wait()
    print(run.result["captures"], run.result["captures_per_s"],
          run.result["stop_reason"])

    # Combined-index video/GIF recorder: the GUI's combined view, or with no
    # GUI open the array's NDVI (pass expr="..." for another index)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    if rec.result and rec.result.get("error"):   # leaving the block does not raise
        print("recording failed:", rec.result["error"])
    else:
        print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**yra SDK serijinio / intervalinio fotografavimo ciklas. Kadangi nėra `Ctrl+C`, kuris leistų jį nutraukti iš skripto, jūs**privalote** perduoti „`count`“ ir (arba) „`duration_s`“ (ciklas sustos, kai bus pasiektas bet kuris iš jų). „`interval_s`“ matuojamas nuo kiekvieno ciklo pradžios (kaip ir GUI). Nuolatiniai praėjimai (`interval_s=0`) sujungia `group_seq` → `after_seq`, taigi tai yra iš eilės einančios sinchronizuotos grupės pagal masyvo. Likusieji „kwargs“ parametrai perduodami tiesiai į `capture()`.
- **`continuous`**yra pačios užkulisinės sistemos nepertraukiamas fiksavimas — GUI „Nepertraukiamo režimo“ atitikmuo.**Naudokite jį, o ne `capture_repeated()`, jei norite, kad kiekvienas suveikimas būtų įrašytas į diską dideliu kadrų dažniu:** `capture_repeated()` iš jūsų skripto siunčia po vieną HTTP užklausą kiekvienai grupei, o `continuous()` palaiko du fiksavimo procesus backend viduje be duomenų perdavimo tarp grupių. Išmatuota esant ROI 512 9 kamerų masyve: 73–77 įrašai per sekundę, palyginti su masyvu, skelbiančiu apie 78. „`capture_repeated()`“ palikite intervaliniam įrašymui (`interval_s`) ir peržiūrint kadrus po vieną. Windows sistemoje, siekiant užtikrinti aukštą kadrų dažnį, išimkite užkulisinę sistemą iš „Microsoft Defender“ (žr. [1 žingsnį](#step-1--install-the-chloros-platform-package)).
- **`record`** yra *stebėjimo lygio*: jis įrašo atvaizduotą tiesioginį sujungtų indeksų kompozitą. Kai atidarytas GUI sujungtas vaizdas, jis įrašo tą vaizdą taip, kaip jis rodomas. Jei niekas neatidaryta, užkulisinė programa pati atkuria vaizdą: `expr`, jei perduodate vieną, kitaip – masyvo NDVI (raudonasis ir NIR juostos, artimiausios 660 nm ir 850 nm, kaip juos pasirenka tiesioginis vaizdas), kai masyvas yra suderintas. Juostas `expr` pavadinkite taip, kaip jas pavadina masyvas: `<band>_<filter>` – M3C kamerai (`NIR_850_RGN`, `Red_660_RGN`, `Orange_615_OCN`), `Mono_<filter>` – M3M kamerai (`Mono_850`); trumpas spalvos pavadinimas (`NIR`, `Red`) veikia tik tada, kai jį turi viena masyvo juosta. Įrašymo metu atidarytas GUI langas įrašomas tol, kol jis lieka atidarytas. Įrašymas, kurio metu nerašomas joks kadras, baigiasi klaida `error`, `"no-frames: …"` (masyvas vis dar gali derinti arba stabilizuoti ekspoziciją), ir sukelia išimtį `stop()` (žr. [`RecorderHandle`](#recorderhandle)). Vienas sudėtinis įrašymo įrenginys vienam masyvui (sukelia išimtį, jei jau veikia kitas).
- **`burst` → `build_video`** yra *analizės kokybės*: `burst` įrašo neapdorotus kadrus + kiekvieno kadro manifestą + po vieną `.daq` už kiekvieną atskirą DLS nuskaitymą pagal `<output>/bursts/<base>/`, dirbdamas pilnu įrašymo ciklo greičiu (be grandinės, be „exiftool“, be tiesioginio peržiūros režimo). `build_video` suderina kiekvieno kadro laiką su artimiausiu `.daq` ir pakartotinai paleidžia importo proceso spinduliavimo/atspindžio/indekso grandinę. `products` yra `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` sąrašas (numatyta reikšmė: sujungtas indeksas). `burst().stop()` taip pat automatiškai paleidžia „best-effort“ sujungtoindekso versiją, kuri sustabdymo rezultate grąžinama kaip `build_job`.

##### `build_video` rezultatas

| Raktas | Reikšmė |
| --- | --- |
| `success` | `True` tik tada, kai kiekvienas užsakytas produktas buvo sukurtas pagal užsakymą: bent vienas išvesties elementas, nieko nėra `skipped`, ir nėra `degraded`. |
| `outputs` | Įrašytų vaizdo įrašų (ir GIF failų) keliai. |
| `errors` | Problemos, pateiktos tekstu. |
| `skipped` | Po vieną `{"serial", "level", "reason"}` už kiekvieną užsakytą produktą, kurio atveju **nebuvo** sukurtas vaizdo įrašas — kameros (nėra kalibravimo rinkinio tai kamerai, nėra DLS atspindžio rodmenų, nesėkminga apdorojimo grandinė) arba kombinuotų indeksų, kuriuose yra `"kind": "combined"` ir `serial` `None` (tarp kamerų nėra raudonos ir NIR juostų poros, nėra grupės su spinduliavimu abiem juostomis). Bet koks įrašas sukuria `success` ir `False`; kiti vaizdo įrašai vis tiek įrašomi ir išvardijami. |
| `substitutions` | Kameros atvaizduojamos kitokiu nei prašytu lygiu, ir kodėl — RGB kamera, kuriai prašoma spinduliavimo/atspindžio, gauna savo rodymo vaizdo įrašą, kurio baltos spalvos balansas nustatytas, jei yra kameros kalibravimo rinkinys. Tai nėra klaida. |
| `notes` | Informacinis tekstas, ne klaidos — pvz., kamera be individualaus indekso išraiškos neturi indekso vaizdo įrašo, arba RGB-kameros ekrano vaizdo įrašas neturi baltos spalvos balanso (nėra kalibravimo rinkinio arba jame nėra gamyklinio baltos spalvos balanso), nurodant kameros pavadinimą. Tai nėra klaida. |
| `degraded` | `True`, kai produktas buvo užrašytas, bet nėra tas, kurio buvo prašoma: neprisijungus sujungtas indeksas yra įmontuotas raudonos ir NIR normalizuotas skirtumas, todėl sukonfigūruota indekso išraiška nėra įvertinama. Sukuria `success` `False`. |
| `combined` | Kombinuoto indekso santrauka: naudotos juostos, LUT diapazonas, sukurti kadrai. |
| `clamp_masks` | Kiekvieno kadro [nesumaišymo fiksavimo kaukių](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on)kalibruotų TIFF failų [unmix clamp masks](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) keliai (`save_tiffs=True`), esančios `clamp_masks/<level>/` šalia TIFF failų `<level>/` aplanko, po vieną kiekvienam TIFF, kuriame buvo apribotas pikselis. Vaizdo įraše jų nėra. |
| `clamp_fractions` | Ką vaizdo įrašas pateikia vietoj kaukės: po vieną `{"kind", "serial", "level", "frames"}` kiekvienam vaizdo įrašui (`kind` `"combined"` sujungto indekso kameroms), `frames` – `[frame, R, G, B]` sąrašas — to kadro pikselių dalis, kurią atskyrimo procesas apribojo juostoje, kurią užima jo R / G / B kanalas (`Chloros:Band{R,G,B}UnmixClampFraction`). |

#### `RecorderHandle`

Grąžina `ArraySession.record()` ir `ArraySession.burst()`. Naudokite jį kaip konteksto tvarkyklę, kad procesas automatiškai sustotų išeinant iš aplinkos, arba valdykite jį rankiniu būdu.

| Elementas | Aprašymas |
| --- | --- |
| `job_id` | Užkulisinės užduoties identifikatorius (str). |
| `kind` | `"composite"` (iš `record`) arba `"raw"` (iš `burst`). |
| `start_stats` | Žodynas, grąžintas `start` iškvietimo metu. |
| `result` | `None` vykdymo metu; galutinis sustabdymo rezultato žodynas, kai procesas sustabdytas. |
| `stats(timeout=10.0)` | Darbo statistika realiuoju laiku (įrašyti kadrai, pasiektas kadrų per sekundę skaičius, praėjęs). |
| `stop(timeout=60.0)` | Sustabdo įrašymo įrenginį; grąžina ir išsaugo galutinį rezultatą (`.result` išlieka `None`, jei pats sustabdymo prašymas nepavyko). Jei įrašymas `record()` nepavyko — t. y. nebuvo įrašytas nė vienas kadras (`error` `"no-frames: …"`) — rezultatas įrašomas į talpyklą kaip `.result`, tada sukeliamas `ChlorosConnectError`. Idempotentinis (antrasis iškvietimas grąžina talpykloje esantį rezultatą, nesukeldamas išimties). Išeinant iš `with` bloko įrašymo programa sustabdoma nesukeliant išimties, todėl po to patikrinkite `rec.result.get("error")`. |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

#### `ContinuousHandle`

Grąžina `ArraySession.continuous()`. Naudokite jį kaip konteksto tvarkyklę — išėjimas iš `with` bloko sustabdo įrašymą, jei jis vis dar vyksta — arba valdykite jį naudodami `status()` / `wait()` / `stop()`. Vienu metu vyksta vienas nepertraukiamas fiksavimas kiekvienam masyvui.

| Elementas | Aprašymas |
| --- | --- |
| `start_status` | Pradžios iškvietimo grąžintas būsenos žodynas. |
| `result` | `None` vykdymo metu; galutinis būsenos žodynas, kai fiksavimas baigtas (nustatytas `stop()`, arba `status()` / `wait()`, kuris užfiksavo jos pabaigą). |
| `status(timeout=10.0)` | Dabartinis būsenos žodynas (laukeliai žemiau). |
| `wait(timeout=None, poll_s=0.5)` | Blokuoti, kol fiksavimas baigsis savaime — pasiekus `max_captures` arba `duration_s`, ištuštėjus masyvui, užpildžius diską, įvykus klaidai — ir grąžinti galutinę būseną. Naudojant `timeout`, grąžinkite naujausią būseną praėjus nurodytam sekundžių skaičiui, net jei procesas vis dar vyksta: patikrinkite `running`. |
| `stop(timeout=30.0)` | Sustabdykite įrašymą ir grąžinkite jo galutinę būseną. Idempotentinis (antrasis iškvietimas grąžina išsaugotą rezultatą). |

Būsenos laukai:

| Laukas | Reikšmė |
| --- | --- |
| `running` | „True“, kol vyksta duomenų surinkimas. |
| `captures` | Sinchronizuotos grupės išsaugotos. |
| `files` | Failai įrašyti. |
| `failed_files` | Failai, kurių nebuvo galima įrašyti (kaip nurodyta [`CaptureResult.failed`](#files-that-could-not-be-written--captureresultfailed)). |
| `failed_captures` | Visiškai nepavykę fiksavimo bandymai, įskaitant tuos, kuriuos fiksavimo sistema bandė pakartoti (HTTP 503 klaida, kai masyvas laiku neturėjo naujos grupės). |
| `skipped_groups` | Masyvo paskelbtos grupės, kurios nebuvo išsaugotos (fiksavimas atsiliko). |
| `captures_per_s` | Vidutinis išsaugojimo greitis nuo pradžios. |
| `elapsed_s` / `started_at` | Iki šiol praėjusios sekundės; pradžios laikas (Unix epochos sekundės). |
| `last_group_seq` | Naujausia sinchronizuota ir išsaugota grupė. |
| `last_error` | Paskutinė klaida arba `None`. |
| `stop_reason` | Pasibaigus: `count` (pasiekta `max_captures`), `duration` (pasiektas `duration_s`), `stopped` (iškviestas `stop()`), `array_gone` (masyvas buvo atjungtas), `error` (20 nesėkmių iš eilės arba užklausa, kurią atmetė užkulisinė sistema), `disk_full` (failas, kuriam diske trūko vietos). `None` veikimo metu. |
| `max_captures` / `duration_s` | Ribos, su kuriomis buvo paleista (`0` = nėra). |
| `defender_cores` | „Microsoft Defender“ per pastarąsias 10 sekundžių naudoti procesoriaus branduoliai; `None`, kol bus išmatuotas vienas, ir visada `None` kompiuteryje be „Defender“. |
| `defender_warned` | „True“, kai fiksavimo duomenys užregistruoja, kad „Defender“ jį lėtina (naudodamas 1 ar daugiau branduolių). Išskirkite užkulisinę sistemą iš „Defender“ nuskaitymo — žr. [1 žingsnį](#step-1--install-the-chloros-platform-package). |

```python
run = arr.continuous("captures/run1", processing="raw", max_captures=5000)
try:
    while True:
        st = run.wait(timeout=5)              # returns every 5 s while running
        print(f"{st['captures']} captures, {st['captures_per_s']:.1f}/s, "
              f"{st['skipped_groups']} skipped")
        if not st["running"]:
            break
finally:
    final = run.stop()                        # no-op once it has ended
if final["failed_files"] or final["stop_reason"] in ("disk_full", "error"):
    raise SystemExit(f"continuous capture: {final['stop_reason']}: "
                     f"{final['last_error']}")
```

### Tiesioginiai kadrai jūsų pačių duomenų apdorojimo grandinėje

Norėdami sinchronizuotus kadrus perduoti į savo kodą — mašininio mokymosi sistemą, pasirinktinę peržiūros programą, savo įrašymo įrenginį — naudokite `ArraySession.frame_stream()`. Kiekvienas žingsnis sukuria `GrabResult`: `{serial: numpy array}` kiekvienai kamerai, **visos iš vieno trigerio**, o to trigerio identifikatorius nurodytas `.group_id`. Tai tas pats įgyvendinimas kaip ir projekto API `ArrayHandle.frame_stream()`, todėl viskas, kas pateikta skyriuose [Vienas trigeris vienam skaitymui](#one-trigger-per-read--group_id), [`band`](#exporting-a-single-band--band), [`align`](#srautų suderinimas--align) ir [`bit_depth`](#bitų gylis--tikslumo mainai už pralaidumą) taikoma abiem atvejais.

```python
import chloros_sdk
from chloros_sdk import FrameFetchError

serials = ["213800234", "214000533", "214701288", "214701292"]   # master first

with chloros_sdk.connect_array(serials) as arr:
    try:
        for frames in arr.frame_stream(processing="raw", fps=10, duration=300,
                                       with_metadata=True):
            if not frames:              # no camera answered this tick
                continue
            if frames.errors:           # cameras missing from this trigger
                print("left out:", {s: str(e) for s, e in frames.errors.items()})
            for serial, (img, hdr) in frames.items():
                # img: numpy array (raw: one Bayer or mono plane)
                # hdr: group_id, frame_id, timestamp_ns, exposure_us, gain_db, ...
                my_pipeline(frames.group_id, serial, img, hdr["timestamp_ns"])
    except FrameFetchError as exc:      # the stream cannot continue
        print("stream ended:", exc.status, exc)
```

- **Kiekvienas pateiktas rinkinys yra naujas trigeris.** `frames.group_id` sraute niekada nepasikartoja; kai prašoma daugiau `fps`, nei veikia masyvas, srautas laukia kito trigerio, todėl veikia pagal lėtesnį iš dviejų.
- **Trūkstamos kameros yra įvardijamos, o ne užpildomos.** Kamera, kurios nebuvo galima aptarnauti tuo trigerio metu, nėra žodyne ir yra išvardyta `frames.errors`.
- **Jis baigiasi triukšmingai.** `FrameFetchError` atmetimo atveju kartojasi kitą ciklą, praėjus 60 s be kadro arba praėjus 60 s be naujo trigerio.**Ką tai suteikia.**`frame_stream()` perkelia ištisus „numpy“ kadrus per vietinį HTTP ryšį ir pasiekia beveik**170 MB/s**greitį. To pakanka neapdorotų kadrų, sugrupuotų kadrų arba ROI masyvo apdorojimui realiuoju laiku, tačiau nepakanka, kad būtų apdorojamas kiekvienas trigeris pilna skiriamąja geba — norint išsaugoti**kiekvieną** trigerį, reikia išsaugoti į diską naudojant [`continuous()`](#continuoushandle). Išmatuota naudojant keturių kamerų masyvą (FOCN, FRGN, FRGB, F450) su suspaustu perdavimu; kiekvienas žingsnis buvo pilnas vieno sužadinimo rinkinys:

| | Visas jutiklis (matrica 14,2–14,4 kadrų per sekundę) | 2 × 2 sujungimas (matrica ~33 kadrų per sekundę) |
| --- | --- | --- |
| `frame_stream(processing="raw")` | 6,7 rinkinių/s | 22,2 rinkinių/s |
| `frame_stream(processing="debayered")` | 2,3 rinkiniai/s | 9,3 rinkiniai/s |
| `frame_stream(processing="display")` | 0,8 rinkiniai/s | 3,0 rinkiniai/s |
| MJPEG kiekvienai kamerai, 4 srautai 720p raiška | po 13,5 kadrų per sekundę; kiekvienas kadras susietas su suveikimu | po ~25 kadrų per sekundę; ~45 % suveikimų pasikartoja visuose 4 srautuose |

Naudokite „`processing="raw"`“, jei norite didesnio pralaidumo ir demozėjimo savo pusėje; „`debayered`“ lygis taip pat pašalina kalibravimo paketo tamsos nuokrypį, o „`raw`“ demozajavimas to nedaro.

#### MJPEG srautai kiekvienai kamerai (be RTSP)

**RTSP nepalaikoma.** Transliacijos sąsaja yra MJPEG per HTTP (`multipart/x-mixed-replace`, po vieną JPEG failą kiekvienai daliai, kiekvienoje dalyje naudojamas `Content-Length`), po vieną URL adresą kiekvienai kamerai:

| URL | Ką pateikia |
| --- | --- |
| `GET /api/camera/stream/<serial>` | 8 bitų peržiūra, iki ~30 kadrų per sekundę. Masyvo nariui dydį ir kokybę nustato `max_h` (numatyta reikšmė 720, 64–2160) ir `jpeg_q` (numatyta reikšmė 80) nustato dydį ir kokybę. |
| `GET /api/camera/<serial>/stream-annotated` | Tas pats peržiūros vaizdas su ant jo nupieštais GUI sluoksniais (zebra, tinklelis, kryžminis taškas, histograma, kontrasto piko rodiklis, taškinis ekspozicijos matuoklis). `ChlorosLocal.camera_stream()` jį nuskaito už jus. |
| `GET /api/camera/<serial>/stream-reflectance` | 8 bitų atspindžio koeficientas kiekvienam juostos dažniui — žr. [Tiesioginis atspindžio koeficiento srautas](#live-reflectance-stream-mjpeg). |

- **Trigerių antraštės.** Masyvo nario atveju kiekviena visų trijų srautų dalis perduoda `X-Chloros-Group-Id`, `X-Chloros-Frame-Id` ir `X-Chloros-Timestamp-Ns`. Dalyvės iš skirtingų kamerų su tuo pačiu `X-Chloros-Group-Id` yra tas pats trigeris: surinkite dalis ir sugrupuokite pagal tą antraštę, kad vėl surinktumėte rinkinį. Atskira kamera neturi grupės: jos peržiūros dalys neturi nė vienos iš šių antraščių (jos atspindžio dalys turi tuščią `X-Chloros-Group-Id`).
- **Peržiūros srautas praleidžia kadrus esant apkrovai.** Jis pateikia naujausią kadrą ir praleidžia visus, kurių nespėjo laiku išsiųsti, todėl esant dideliems masyvo dažniams ne kiekvienas trigeris pasiekia kiekvienos kameros srautą (žr. lentelę aukščiau). Kiekvienam trigeriui naudokite `frame_stream()` arba `continuous()`.
- **`/api/camera/stream/<serial>` sustabdomas, kol atidarytas bendras masyvo tiesioginis srautas** (`/api/camera/array/stream/<array_id>` — jį atidaro GUI masyvo peržiūra). Tada kiekvieno masyvo nario srautas siunčia vieną vietos laikiklio kadrą „Paused (array stream active)“ be `X-Chloros-*` antraščių ir nieko daugiau, kol sujungtas srautas neuždaromas; tada jis pats atsinaujina. `stream-annotated` ir `stream-reflectance` nepertraukia veikimo. Uždarykite GUI masyvo peržiūrą, kai scenarijus skaito kiekvienos kameros peržiūros srautus.
- **Skaičiams, o ne nuotraukoms, naudokite `frame_stream()`.**JPEG yra prarandamasis ir 8 bitų; `frame_stream()` grąžina tuos pačius rezultatus be kodeko kelyje.

### Intervalinis vaizdų fiksavimas dronų tyrimams

Tyrimo skrydžiui reikia vienos sinchronizuotos vaizdų grupės kas kelis kelionės metrus, o ne kiekvieno kadro, kurį gali sukurti masyvas. Tai kontroliuoja du parametrai:**`target_fps`**nustato, kaip dažnai kameros fiksuoja kadrą, o**`capture_repeated(interval_s=…)`** – kaip dažnai kadras yra išsaugomas.**Tarp išsaugojimų kameros toliau transliuoja vaizdą.** Kiekvienu intervalu išsaugoma naujausia sinchronizuota grupė; tarp jų esantys kadrai nėra įrašomi. Automatinė ekspozicija koreguojama kiekviename kadre, todėl ekspozicija jau yra nusistovėjusi, kai įvyksta išsaugojimas. Nėra režimo, kuriame kameros sustotų tarp kadrų ir būtų pažadinamos kiekvienam kadrui: tuomet kiekvienas kadras turėtų laukti, kol prasidės srautas ir nusistovės ekspozicija.**Nustatykite kadrų dažnį pagal intervalo poreikius.**Palikus automatinį režimą, sistema veikia didžiausiu dažniu, kurį palaiko serveris ir tinklas. Nustatant mažesnį dažnį, sumažėja kamerų kaistimas, tinklo apkrova ir serverio procesoriaus apkrova, nekeičiant to, kas yra išsaugoma. Pasirinkite dažnį, kuris užtikrina**2–4 kadrus per intervalą**:

- Kiekvienas įrašymas laukia kito kadro po to, kai jis užsakomas, todėl įrašyta grupė pasiekia tikslą praėjus iki vieno kadro laikotarpiui po numatyto momento. Didesnis dažnis sumažina tą skirtumą.
- Automatinė ekspozicija koreguojama kartą per kadrą, todėl labai mažas dažnis sulėtina, kaip greitai ekspozicija reaguoja į praplaukiančius debesis.

**Intervalą apskaičiuokite pagal skrydžio planą:**

```
spacing    = along-track footprint × (1 − forward overlap)
interval_s = spacing / ground speed
```

Pavyzdžiui, 60 m plotas su 80 % persidengimu į priekį reiškia 12 m tarpą; esant 10 m/s greičiui, tai yra `interval_s=1.2`, o fiksuotas „`target_fps=3`“ užtikrina nuo 3 iki 4 kadrų per intervalą. Fotografavimas vyksta pagal laiką: SDK neturi atstumu ar GPS valdomų režimų, todėl skenavimo linijose išlaikykite pastovų greitį žemės atžvilgiu.

**Išsaugokite neapdorotus duomenis orlaivyje ir kalibruokite vėliau.** `interval_s` matuojamas nuo kiekvieno išsaugojimo pradžios, todėl jei išsaugojimas trunka ilgiau nei intervalas, kitas išsaugojimas prasideda vėliau ir atstumas nukrypsta. Išsaugant neapdorotus duomenis kartu su šviesos jutiklio rodmenimis (`processing="raw"`; rodmenys išsaugomi pagal numatytuosius nustatymus), kiekvienas išsaugojimas trunka trumpai, o kiekvieną kadrą vėliau galima konvertuoti į atspindžio koeficientą.

```python
import chloros_sdk

INTERVAL_S = 1.2      # from the flight plan
FLIGHT_S = 20 * 60    # bounds the loop; it also stops at count= if given

with chloros_sdk.connect_array(serials, target_fps=3.0) as arr:
    groups = arr.capture_repeated(
        "survey/", interval_s=INTERVAL_S, duration_s=FLIGHT_S,
        processing="raw", render_index=False,
        on_capture=lambda i, r: print(f"group {i}: {len(r)} frames"))
```

- **`connect_array(target_fps=…)`**nustato dažnį nuo pat pradžių;**`arr.set_target_fps(fps)`**jį keičia skrydžio metu be pakartotinio prisijungimo, o**`arr.set_target_fps(None)`** grąžina jį į automatinį režimą. Greitis, viršijantis tai, ką gali perduoti pagrindinis kompiuteris ir tinklas, sumažinamas iki tos ribos — patikrinkite `clamped` rezultate — o mažiausias greitis yra 0,1 fps.
- **Nenaudokite `burst()` kadrų retinimui.** Jis įrašo kiekvieną masyvo sukurtą kadrą; jis laikosi fiksuoto `target_fps`, todėl tai tinkamas įrankis tik tada, kai norima gauti kiekvieną kadrą.
- **`target_fps` neištaiso sugadintų kadrų.** Sumažinus šią reikšmę, sumažėja generuojamų kadrų skaičius, o ne kiekvieno kadro siuntimo greitis; jei kadrai sugadinti dėl GVSP, vietoj to sumažinkite `wire_ceiling_mbps` (žr. [Masyvo būklė](#array-health--which-subsystem-is-losing-frames)).

```python
r = arr.set_target_fps(5.0)
if r["clamped"]:
    print(f"asked for {r['requested']} fps, running at {r['target_fps']}")
```

### Prisijungimas prie jau prijungto masyvo — `attach_array`

Jei masyvas jau veikia (jį atidarė GUI arba ankstesnė SDK sesija iškvietė `connect_array`), vietoj pakartotinio prisijungimo naudokite `attach_array`, kad gautumėte jo identifikatorių. `connect_array` tokioje situacijoje visada rodo klaidą „Kamera  <sn>jau yra masyve<id>“, nes POST užklausos siuntimas naudojant `/array/connect`, kai narys jau yra pulke, nėra idempotentinis; `attach_array` nuskaito `/api/camera/array/list` ir atitinka pagal „array_id“ arba serijinius numerius.

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

Šablonas: SDK skriptai, veikiantys kartu su darbalaukio grafine vartotojo sąsaja, turėtų pirmiausia pabandyti naudoti „`attach_array`“, o jei baseine dar nėra masyvo, pereiti prie „`connect_array`“.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Prijungtas masyvas yra pasiskolintas, o ne nuosavas.** `attach_array` rankena niekada nenutraukia masyvo veikimo: nei `with` bloko išėjimas, nei aiškus `disconnect()` išlaisvinimas jo neišlaisvina (aiškus iškvietimas išspausdina vieną eilutę į „stderr“, nurodydamas, kad masyvas tebėra aktyvus). Ta pati taisyklė galioja kaip ir „`CameraSession`“ / „`DAQSensorSession`“ atveju. Taigi bendras naudojimas su GUI yra saugus tiek su „`with`“, tiek be jo:
>
> ```python
> with chloros_sdk.attach_array(serials) as arr:
>     arr.capture("output/", processing="reflectance")
> # … the array stays up for the GUI
> ```
>
> Kai norite jį užbaigti — tarkim, norėdami vėl prisijungti su kitu `wire_ceiling_mbps` — iškvieskite `arr.force_disconnect()`. Bandymas vėl prisijungti po paprasto `disconnect()` su prijungtu rankenėliu baigsis klaida „Kamera <sn>jau </sn>yra <sn>masyve<id>“, nes niekas nebuvo atleista.

### Tinklo analizės pagalbinė priemonė

Naudinga prieš atidarant masyvą — leidžia numatyti, ar jūsų siūlomi nustatymai tiks:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    # width / height are sensor pixels BEFORE binning (what connect_array
    # takes with binning=); out_width / out_height is what is streamed.
    print(f"Connect with width={r['width']} height={r['height']} "
          f"binning={r['binning']} ({r['out_width']}x{r['out_height']} streamed)")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

```python
analyze_array_network(
    master_serial,
    slave_serials=None,
    width=2048, height=1536,              # sensor pixels before binning
    pixel_format=None,                    # None = what connect applies (Mono12 / BayerRG12)
    binning=1,                            # 1 | 2 | 4
    force_tier=None,
    packed="auto",                        # "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",
    timeout=30.0,
) -> dict
```

Jis niekada nepaleidžia užkulisio: jei užkulisio atsakymo nėra, sukeliamas `ChlorosConnectError`, o atmestas užklausimas grįžta kaip rezultatas. Netinkamas `packed` sukelia `ValueError`.

`status` yra vienas iš šių: `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (kitu atveju – `error` – įskaitant atmestą užklausą: nesate prisijungę, `error_code: AUTH_REQUIRED`, arba planas, mažesnis už SDK ribą, `PLAN_UPGRADE_REQUIRED`). `auto_capped_fps` reiškia, kad prašoma skiriamoji geba tinka RX žiedui tik esant ribotam suaktyvinimo dažniui — išlaikykite skiriamąją gebą ir pereikite nuo `target_fps=result["recommended"]["recommended_target_fps"]` prie `connect_array` (žr. [6 pavyzdį](#6-capability-probe-before-connecting-a-4-cam-array)). `auto_shrunk` pasirodo tik tada, kai prašomo kadro negalima perduoti jokiu kadrų dažniu; „connect“ niekada pats nesumažina kadrų, todėl `recommended` taikykite patys.

**Kaip skaityti projekciją** (tas pats modelis kaip GUI skydelyje „Array Settings“):

- **„Burst“ (`frame_bytes_total`) sumuojamas pagal kiekvieną kamerą, naudojant kiekvienos kameros tikrąjį pikselių formatą.**Mono**M3M**kameros transliuoja „Mono12“ (2 bitai/pikselis) arba, naudojant „`pixel_format`“, savo gylio „Mono“ formatą (`"BayerRG8"` yra `Mono8`, 1 B/px), taigi be jo 4 kamerų pilnos raiškos kadras yra**~25 MB** su trimis „mono“ kameromis, o ne ~12,6 MB, kaip numato prielaida, kad visos kameros yra 8 bitų. Užkulisinė sistema nustato kiekvienos kameros formatą pagal jos modelį.
- **Pralaidumas (`burst_fits_nic_ring`) atsižvelgia į duomenų nutekėjimą**, o ne „visas burstas prieš žiedą“: „sim-emit“ tinka, kai pagrindinis kompiuteris ištuština RX žiedą greičiau, nei kameros jį užpildo. 10G pagrindinis kompiuteris + 1 GbE kameros**priima** pilnos raiškos kadrą net tada, kai burstas viršija žiedo talpą; o 1 GbE pagrindinis kompiuteris blokuoja (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` yra konservatyvus serijinio duomenų išgavimo ribinis dydis** — `max(readout+emit, N×emit)`, kai kiekvienos kameros duomenų siuntimas apribotas iki 1 GbE kameros ryšio, nepriklausomai nuo ekspozicijos. Pvz., ~2,8 kadrų per sekundę 4 kamerų pilnos raiškos 12 bitų masyvui (atitinka vykdymo metu išmatuotą ~2,7–3,0). Pilnas modelis: [CLI nuoroda → Matricos kadrų per sekundę ir serijinio įrašymo modelis](cli-reference.md#array-fps--burst-model).
- **Po nepertraukiamo įrašymo projekcija rodo, kokį kadrų skaičių ji išlaikė.** 30 s ar ilgesnis įrašymas automatiniu greičiu fiksuoja greitį, kuris nusistovėjo esant tam masyvo dydžiui šiame kompiuteryje; greičio reguliatoriaus trumpi bandymai su didesniu greičiu neįtraukiami. Nuo to momento `array-connect`'eilutėje, `connect_array()` analizėje ir masyvo nustatymų skydelyje nurodoma ta dažnio vertė, kai ji yra mažesnė už modelio nustatytąją, kartu nurodant, kas ją apribojo `cycle_limiter`: `"clock"` (masyvas veikė nustatytu dažniu, o duomenų surinkimo ciklas laukė savo paleidimo signalo), `"loop"` (duomenų surinkimo ciklui nebeliko laiko), `"capture"` (fiksavimas atsiliko ir praleido grupes) arba `"measured"` (nenurodyta, kuris iš šių atvejų). Modelio rodiklis pateiktas `wire_fps_max`. Masyvas vis tiek pasiekia tą vertę, kurią perduoda laidinis ryšys.
- **Per didelis užsakymas (`oversubscribed: true`) reiškia, kad laidinis ryšys negali perduoti masyvo jokiu greičiu, kurį pasiekia kameros** — fps laukai (`achievable_fps_max` / `fps_bright` / `fps_dark`) rodo 0, o vaizdų sujungimas to neišsprendžia (jis sumažina baitų skaičių viename kadre, o ne sinchronizuotų baitų skaičių per sekundę). Vien tik lėtas kabelis to nesukelia: vietoj to sumažėja kadrų dažnis, o jei kamerų veikimo dažnio negalima pakankamai sulėtinti, lygis tampa `slip-emit-and-capture` (serijinis). Sprendimai – mažiau kamerų, „jumbo“ rėmeliai arba greitesnis tinklo adapteris; `max_cams_collision_safe` nurodo jūsų ryšio linijos ir kamerų ribą. Atsakyme taip pat pateikiami kodai `aggregate_demand_bps`, `collision_safe_ceiling_bps` ir `per_cam_floor_bps`. Žr. [Per didelis prenumeratorių skaičius](#over-subscription-the-per-cam-floor).

### Atradimas ir sąrašo sudarymas

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

Kiekviena iš jų priima tik raktinius žodžius `backend_url=` ir `timeout=30.0` ir grąžina žodynų sąrašą. `discover_lattice_cameras()` (`serial`, `ip`, `mac`, `model`, `lattice_model`, `vendor`, `connected`, `array_id`, …) taip pat priima `auto_start_backend=True`; `list_*` iškvietimai niekada nepaleidžia užkulisio.

---

## „Smart-AE“ / „Smart-Capture“

„LATTICE“ masyvai, vos tik prisijungę, fone nuolat vykdo automatinį ekspozicijos nustatymą (AE), tačiau naujai nukreiptam vaizdui sukonverguoti reikia šiek tiek laiko. **„Smart-capture“** yra patogus sprendimas: jis tikrina kiekvienos kameros ekspoziciją, laukia, kol masyvas stabilizuojasi visame lange, ir tada paleidžia fotografavimą. Tai atitinka GUI: darbalaukio programėlės „smart“ fotografavimo mygtukas iššaukia tą patį užkulisinio proceso galinį tašką.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

Naudojant `ChlorosProject` (kitas skyrius), atsiranda daugiau nustatymų:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

„Smart-AE“ politika pagal numatytuosius nustatymus yra konservatyvi. Sugriežtinkite `exposure_tolerance_pct`, jei atliekate reiklius radiometrinius darbus; sušvelninkite, jei dirbate su greitai besikeičiančiomis scenomis, kur jums tiesiog reikia „pakankamai artimo“ rezultato.

---

## DAQ jutiklių sesijos

Nuolatinis spektrinių jutiklių (DAQ-U per USB, DAQ-M per BLE, DAQ-E per Ethernet) užkulisinis rezervas. Atitinka kameros funkcionalumą: „Smart-Detect“, rezervo pakartotinis naudojimas, idempotentinis prijungimas.

### Išmanusis aptikimas (be konfigūracijos)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

Pirmenybė: Ethernet → BLE → USB. Perduokite bet kurią aiškią užuominą, kad užfiksuotumėte perdavimo būdą.

### Užfiksuotas perdavimo būdas

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# A specific sensor by id (as list_daq_sensors() shows it; ':' or '-' alike)
daq = chloros_sdk.connect_daq_sensor(sensor_id="CB-7C-A7-5A-73")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

`sensor_id=` nurodo vieną jutiklį, ir atidaromas tik tas jutiklis. Jis palyginamas su jau atidarytais jutikliais, tada DAQ-E kompiuterio vardas arba IP adresas (DAQ-E, kurį tinklo nuskaitymas mato tik pagal IP adresą, yra užklausiamas, tik skaitymo režimu, dėl jo vardo, MAC ir serijinio numerio, todėl bet kuris iš jų taip pat jį aptinka), DAQ-M BLE MAC adresas ir identifikatorius, kurį DAQ-U arba DAQ-M praneša, kai yra atidarytas (bet kuris kitas atidarytas patikrinimui yra vėl uždaromas). Jei jis nerandamas, `connect_daq_sensor` sukelia `ChlorosConnectError`, kuriame išvardijama, kas buvo rasta; taip pat nurodant adresą (`port=` / `mac=` / `eth_host=`), kuriame yra kitas jutiklis, taip pat sukelia išimtį. Ji niekada neperjungia į bet kurį matomą jutiklį — būtent tam ir skirtas iškvietimas su nulio žymekliu.</id></sn></id></sn>

### `connect_daq_sensor()` parašas

```python
connect_daq_sensor(
    *,                                    # every argument is keyword-only
    sensor_id=None,                       # one named sensor (see above)
    transport=None,                       # "usb" | "ble" | "eth"; None = smart-detect
    port=None,                            # DAQ-U serial port ("COM3", "/dev/ttyUSB0")
    mac=None,                             # DAQ-M BLE MAC (implies transport="ble")
    eth_host=None,                        # DAQ-E hostname or IP (implies transport="eth")
    integration_time=None,                # ms; None = the sensor's own
    frame_avg=None,
    enable_ae=None,
    cap_id=None,                          # cap correction: "sunshine_cosine", "fov_30", ..., "none" (bare)
    start_streaming=True,
    backend_url="http://127.0.0.1:5000",
    timeout=60.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> DAQSensorSession
```

Jau baseine esantis jutiklis grąžinamas toks, koks yra (pasiskolintas): `integration_time`, `frame_avg` ir `enable_ae` jam netaikomi. Pirmiausia jį atjunkite, kad galėtumėte atidaryti su savo nustatymais. Išimtis yra `cap_id`: pasiskolintame jutiklyje jis nustatomas naudojant `set_cap()`. Jei tai nepadaryta, galioja užkulisinės sistemos numatytasis nustatymas (žr. pastabą po metodų lentele).

### `DAQSensorSession` metodai

| Metodas | Aprašymas |
| --- | --- |
| `status(timeout=10.0)` | Duomenų bazės įrašo santrauka (transliacijos/įrašymo būsena, bangų ilgių diapazonas, kalibravimo sha, integravimo laikas, frame_avg, AE būsena). DAQ-E, kurio srautas nutilo: `reconnect` (kai veikia užpakalinės dalies automatinis pakartotinis prisijungimas: `state`, `attempts`, `next_attempt_in_s`, `last_error`; `None`, kol vyksta srautas), `stream_lost_s`, `reconnects` ir `last_reconnect`, o `record_gaps` reiškia vykstantį įrašymą; `rx_stream_fault` nurodo pakartotinio prisijungimo būseną, kol gaunamas pirmasis spektras. |
| `latest(*, n=1, timeout=10.0)` | Grąžina iki N naujausių spektro kadrų (kiekvienas `ts_ns`, `spectrum`, `is_saturated`, `integration_time`, `x`, `y`, `z`). |
| `stream_start()` / `stream_stop()` | Tęsti / pristabdyti srautą (rankenėlė lieka atidaryta). |
| `record_start(*, output_dir=None, device_name=None, timeout=15.0)` | Pradėti .daq failo įrašymą. Grąžina failo kelią. Neveikia su DAQ-U/M be AWS kalibravimo paketo (išskyrus DAQ-E). |
| `record_stop()` | Sustabdo įrašymą. Grąžina `{path, rows, warning, gaps}`; `warning` yra lygus `None`, jei įrašas yra be klaidų, kitu atveju – užkulisio diagnozė (pavyzdžiui, tuščias failas arba srauto trūkumas), kuri taip pat išvedama į „stderr“. `gaps` išvardija kiekvieną atkarpą, kurioje srautas nieko neperdavė: `start_utc`, `end_utc` (`None`: vis dar prarasta sustabdymo metu), `duration_s`, `reconnected`, `marked_in_file`. Nuolatinio įrašymo atveju šis laukas yra tuščias. |
| `set_cap(cap_id, timeout=15.0)` | Nustatykite viršutinės ribos korekcijos profilį, kuris bus taikomas nuo kito rodmens: `"sunshine_cosine"`, `"fov_15"` ... `"fov_90"` arba `"none"` (arba `None`), jei jutiklis yra be apsauginio dangtelio. Grąžina jutiklioapibendrinimą, kurį atkuria `cap_id`. DAQ-E-S išlaiko `as_recorded` nepriklausomai nuo užklausos (jo difuzorius yra įmontuotas). Sukelia `ChlorosConnectError`, jei profilis neįkeliama. CLI komanda: `chloros-cli daq pool-set-cap`. |
| `enable_ptp(timeout=15.0)` | Tik „DAQ-E“: vėl įjungia PTP įrenginiui, kurio PTP yra išjungtas (`status()["rx_ptp_enabled"]` yra `False`; nustatymas išsaugomas įrenginyje, todėl jis lieka išjungtas, kol vėl neįjungiamas). Grąžina atnaujintą būseną. Sukelia `ChlorosConnectError`, jei tai DAQ-U / DAQ-M, arba kai įrenginys nepatvirtina. Chloros niekada pats iš naujo neįjungia įrenginio, nes išjungimas gali būti tyčinis. CLI komanda: `chloros-cli daq pool-enable-ptp`. |
| `device_info(timeout=15.0)` | Tik DAQ-E: paties įrenginio nustatymai. `firmware` (`running`, `bundled`, `update_available`), `ip` ir `ip_mode` (`dhcp` / `linklocal` / `static`: kas dabar veikia), `network` (išsaugotas nustatymas: `mode`, taip pat `ip` / `mask` / `gateway`, kai įrenginys neveikia), `network_change_pending` (galioja tol, kol įrenginys veikia pagal išsaugotą nustatymą, į kurį įtrauktas `set_network(..., reboot=False)`), `auto_stream`, `ptp`. `firmware["update_available"]` yra lygus `None`, kai įrenginioversijos nepavyko nuskaityti. Kol įrenginio srautas nepasiekia šio kompiuterio, jis nuskaitomas naujo ryšio metu, o žodynas prideda `stream_silent` (`True`), `stream_fault` ir `reconnect`; įrenginys, kuris neatsako, sukelia klaidą. CLI: `pool-info`. |
| `set_network(mode="auto", *, ip=None, mask=None, gateway=None, reboot=True, force=False, comeback_timeout=45.0)` | DAQ-E tik: DHCP (`"auto"`) arba statinis adresas. Įrenginys perkraunamas, kad pakeitimai įsigaliotų, ir vėl prisijungiamas; ši sesija toliau veikia, o `address` seka įrenginį. `reboot=False` tik išsaugo nustatymus. Žr. [„DAQ-E“ įrenginio nustatymai](#daq-e-device-settings). CLI komanda: `pool-set-network`. |
| `set_auto_stream(enabled, timeout=15.0)` | Tik DAQ-E: transliacija įjungus maitinimą, kai nėra prijungta jokia programinė įranga (išsaugota įrenginyje). Grąžina patvirtintą nustatymą. CLI: `pool-set-auto-stream`. |
| `reboot(*, comeback_timeout=45.0)` | Tik DAQ-E: iš naujo paleisti įrenginį ir vėl prijungti jį su ankstesniais nustatymais. CLI: `pool-reboot`. |
| `update_firmware(*, comeback_timeout=45.0)` | Tik DAQ-E: įrašykite šio Chloros leidimo programinę įrangą į įrenginį, kuriame veikia senesnė versija, tada vėl prijunkite jį (trunka apie minutę; neišjunkite maitinimo). Grąžina `updated`, `fw_before`, `fw_after`, `bundled` ir `warning`, jei įrenginys grįžo per lėtai arba grįžo su kita versija; `updated` yra `False`, jei įrenginys jau turi naujausią versiją. Iššaukia „`ChlorosConnectError`“, jei neįmanoma nuskaityti įrenginio versijos. CLI: „`pool-update-firmware`“. |
| „`disconnect()`“ | Išlaisvinimas iš rezervo. Nėra-op, jei rankenos yra prijungtos, bet nepriklauso. Jei tai yra jutiklis, kurį atidarė ši sesija, tai yra `force_disconnect()`, o sukelia `ChlorosConnectError`, kai išlaisvinimas nėra patvirtintas (`with` bloko išėjimas niekada nesukelia išimties). |
| `force_disconnect(timeout=30.0)` | Atleiskite jutiklį, nepriklausomai nuo to, kas jį atidarė — pavyzdžiui, jutiklis, kurį sugriuvęs skriptas paliko rezervoje, ir kurį naujas skriptas pasiekia tik kaip prijungtą identifikatorių, kurio „`disconnect()`“ nieko neveikia. Užbaigus kito kliento jutiklį, nutraukiamas bet koks jo atliekamas įrašymas. Grįžta, kai užkulisinė sistema patvirtina atleidimą arba kai nėra ko atleisti (jutiklis nėra rezervo sąraše arba neveikia joks užpakalinis modulis); išlaisvinimas, kurio laikas pasibaigė arba kurį užpakalinis modulis atmetė, sukelia `ChlorosConnectError`. CLI: `chloros-cli daq pool-disconnect --sensor-id ID`. |

Atributai: `sensor_id`, `model`, `transport`, `address` (eina po DAQ-E, kurį `set_network()` peradresavo), `info` ir `backend_url`.

> **Nurodykite dangtelio pavadinimą.** „DAQ-U“, „DAQ-M“ ir „DAQ-E“ tiekiami be dangtelio, o Chloros negali nustatyti, ar jis yra uždėtas: jutiklis, prijungtas be „`cap_id`“, gauna „`sunshine_cosine`“ – tai prielaida, kurią, kaip rodo įrašai, jie padarė (`cap_id_source=auto_default`). Neapsaugotas DAQ-U, pritaikius šią korekciją, rodo maždaug 30 kartų didesnę vertę, todėl neapsaugotam jutikliui perduokite `cap_id="none"` (arba `set_cap("none")`), o kitais atvejais – įmontuoto dangtelio profilį.

### DAQ-E-S — fiksuotas difuzorius ir pakreipimo jutiklis

**DAQ-E-S** yra DAQ-E su dviem skirtumais, kurie svarbūs programinei įrangai. Viskas kita — aptikimas, prisijungimas, PTP, įrašymas, toliau nurodyti įrenginio nustatymai — veikia lygiai taip pat kaip ir DAQ-E atveju.**Jo difuzorius yra nuolatinis, todėl dangtelio korekcijos nereikia.** „Sunshine“ tipo difuzorius yra įmontuotas ir buvo įrenginyje, kai buvo atliktas jo gamyklinis kalibravimas, todėl kalibravimas jau rodo tikrąjį žemyn nukreiptą spinduliavimą. Chloros nustato savo ribą kaip „`as_recorded`“ (be profilio), nesvarbu, ko būtų prašoma: riba, pavadinta jo vardu (`sunshine_cosine`, `fov_*`, `none`) ignoruojamas ir rodomas pranešimas, o įrašai saugomi su `cap_id_source=model`, todėl įspėjimas apie numanomą ribą jam niekada nesuveikia. Taip pat nenaudokite ribos jo duomenims nustatyti rankiniu būdu: viršuje esantis `sunshine_cosine` rodytų maždaug 11 kartų didesnę vertę.

**Jame įmontuotas 3 ašių akselerometras.** Saulės spindulių difuzoriaus kosinusinė charakteristika galioja tik tada, kai jis yra horizontalioje padėtyje, todėl pakreiptas jutiklis rodo neteisingą spinduliavimo intensyvumą — o atspindžio koeficientas perima šią paklaidą — be jokių kitų požymių. Todėl kiekvienas DAQ-E-S rodmuo atspindi jo padėtį:

| Laukas | Reikšmė |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Kampai laipsniais. `None`, kai programinė įranga pažymi kampą kaip neapibrėžtą (laisvas kritimas, neveikianti ašis). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Pagreitis miligais. `mag_mg`, jei reikšmė toli nuo ~1000, reiškia, kad įrenginys judėjo ir kampai nėra reikšmingi šiam mėginiui. |
| `cal_applied` | Nustatyta: ašys ir kampai yra difuzoriaus koordinačių sistemoje (taikomas prietaiso montavimo kalibravimas). Išvalyta: neapdorota plokštės koordinačių sistema, kurioje gali būti rodomas iki ~2,3° nuokrypis, kuris nėra tikras. |
| `sample_age_ms` | Akselerometro mėginio amžius, kai buvo išsiųstas spektras. |
| `temp_raw` | Nekalibruota lustų temperatūros **tendencija** — tik skirtumai, niekada ne termometro rodmenys. |

`DAQSensorSession.latest()` grąžina juos po kiekvieno rėmo `imu` raktu (`None` „DAQ-U“, „DAQ-M“ arba paprastame „DAQ-E“). Iš DAQ-E-S įrašytas `.daq` saugo tas pačias reikšmes savo `imu_*` stulpeliuose (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) šalia kiekvieno spektro, todėl skaitytojas gali atskirti debesį nuo nugriauto trikojo. Visuose kituose modeliuose šios stulpelės yra tuščios.

Padėties srautas pateikia duomenis apie savo laukus `status()` (`rx_imu_stream_fault`, `rx_imu_stream_faults`, `rx_last_imu_stream_fault`), atskirai nuo spektrinių duomenų, kad akselerometro gedimas niekada nebūtų traktuojamas kaip įrašo spraga. `discover_daq_sensors()` ir sesijos `model` nurodo `DAQ-E-S`.

### DAQ-E įrenginio nustatymai

```python
with chloros_sdk.connect_daq_sensor(eth_host="daq-e-def330.local") as daq:
    info = daq.device_info()
    print(info["ip"], info["ip_mode"], info["firmware"])

    # A fixed address on the network this computer is on
    daq.set_network("static", ip="192.168.1.50", gateway="192.168.1.1")
    print(daq.address)              # 192.168.1.50: the session follows it

    if info["firmware"]["update_available"]:
        daq.update_firmware()
```

- **Šio kompiuterio tinkle turi būti nustatytas statinis adresas.** `set_network` sukelia `ChlorosConnectError`, jei subtinkle nėra šio kompiuterioadresas jutiklio tinkle: po perkrovimo niekas negalės prisijungti prie jutiklio, kad jį sugrąžintų į pradinę būseną. `force=True` nustatymą naudokite tik tada, kai šiam kompiuteriui bus priskirtas naujas adresas arba jis bus nukreiptas į naują potinklį.
- **`set_network`, `reboot` ir `update_firmware` perkrauna jutiklį** ir sukelia `ChlorosConnectError`, kol jis įrašo duomenis, ir jei jis neatsistato per `comeback_timeout` sekundžių. Jutiklio ID ir `daq-e-<id>.local` kompiuterio vardas niekada nesikeičia.
- **Įrašymas, pradėtas prieš pat perkrovimą, yra užbaigiamas ir pavadinamas.** Kai jutiklis vėl pradeda veikti, grąžinamame žodyne yra `recording_closed` (failo kelias) ir `warning`, kuriame tai nurodoma. Jei jis negrįžta arba programinės įrangos perdavimas nepavyksta, vietoj to tai nurodo `ChlorosConnectError`: jo pranešimas baigiasi įspėjimu, o nustatomi `e.recording_closed` / `e.warning` (abu `None`, jei nebuvo uždarytas joks įrašymas). Kai jutiklis vėl prisijungs, pradėkite įrašymą iš naujo.
- **Prijungiamas tik pats jutiklis.** Po paleidimo iš naujo įrenginys turi atsakyti, kur jis buvo -- o po `set_network("auto")` taip pat ir savo `.local` vardu -- nurodydamas savo vardą, kompiuterio vardą ir modelį, pagal turėtą tinklo adapterio (MAC) adresą; `address` tampa IP adresu, kuris atsakė. Atsakymas pagal pavadinimą skaičiuojamas tik su tuo MAC adresu, taigi iš kompiuterio, kuris pasiekia jutiklį per maršrutizatorių, arba iš WSL2 numatytojo NAT tinklo, jutiklis, kurį perkėlė DHCP, nėra vėl prijungiamas (`ChlorosConnectError`; prijunkite jį iš naujo pagal naują adresą).
- **Norint atnaujinti programinę įrangą, jutiklis turi vėl prisijungti.** `update_firmware()` siunčia užklausą įrenginiui, o įrenginys atidaro TCP ryšį atgal į šį kompiuterį, kad gautų atvaizdą, per OS pasirinktą prievadą. Backend klausosi adapterio, kuris pasiekia įrenginį (visų adapterių, tik tada, kai negali nustatyti, kuris iš jų), ir aptarnauja tik tą įrenginį: bet koks kitas ryšys atmetamas (`[DAQ-FW] … refused a firmware connection from <ip>` serverio išvestyje), kol jis toliau laukia. Užkarda, kuri blokuoja ryšį, sukelia atnaujinimo nesėkmę su pranešimu `device never connected back on TCP <port>`. Windows diegimo programa prideda atitinkamą taisyklę (žr. [1 žingsnį](#windows-exe)); kitoje ugniasienėje leiskite įeinančius TCP ryšius į užkurtį iš jutiklio potinklio.
- **Naudodami WSL2, naudokite veidrodinį tinklą** (`networkingMode=mirrored` ir `.wslconfig`): numatytame NAT režime kiekvienas statinis adresas atmetamas (WSL adresas nėra jutiklio tinkle), srautas nepasiekia WSL, todėl programinės įrangos atnaujinimas negali būti užbaigtas.
- Grafinė vartotojo sąsaja (GUI) išlaiko savo ryšį su jutikliu: paleidimas iš naujo naudojant skriptą nutraukia GUI sesiją su juo. GUI turi tuos pačius nustatymus (skirtukas „Šviesos jutikliai“, jutiklio nustatymai, „Tinklas“).

### Atradimas — adreso, su kuriuo galima prisijungti, paieška

`discover_daq_sensors()` nuskaito USB / BLE / ETH ieškodamas jutiklių, kuriuos *galėtumėte* atidaryti. Tai yra DAQ atitikmuo `discover_lattice_cameras()`, ir vienintelis būdas gauti **DAQ-M BLE MAC adresą** — „DAQ-E“ turi įrenginio vardą, o „DAQ-U“ – COM prievadą, tačiau MAC adresas nėra nei išspausdintas ant įrenginio, nei nurodytas operacinėje sistemoje.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               DAQ-U  {'vid': '0403', 'pid': '6001', 'manufacturer': 'FTDI'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| Laukas | Aprašymas |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | COM prievadas / BLE MAC / kompiuterio vardas — perduoti į `connect_daq_sensor` kaip `port=` / `mac=` / `eth_host=`. |
| `display` | Žmogui suprantama etiketė. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` \| `DAQ-E-S`, arba `None`. „Ethernet“ jutiklis yra `DAQ-E-S` arba `DAQ-E`, kaip jis pats praneša, kai jo paprašoma, tik skaitymo režimu, skenavimo metu (`DAQ-E`, jei neatsako laiku). USB serijinis prievadas nuskaitomas kaip „`DAQ-U`“, jei jo tilto mikroschema yra viena iš tų, kurias naudoja „DAQ-U“ (FTDI, CH340, CP210x, Prolific) — tai galima suprasti iš prievado aprašymo, o ne zondas — o „`None`“ rodo bet kurį kitą USB įrenginį, kuris vis tiek yra sąraše, todėl jį galite perduoti kaip „`port=`“. Serijinis prievadas be USB gamintojo identifikatoriaus ir be tokio tilto lusto (pagrindinės plokštės UART, pvz., Linux `/dev/ttyS*`, „Bluetooth“ serijinis ryšys, „Intel AMT“) negali būti DAQ-U ir nėra įtrauktas į sąrašą. |
| `extra` | Informacija apie kiekvieną perdavimo būdą (BLE skelbiamas pavadinimas; USB `vid`, `pid`, `manufacturer`, `serial_number`; DAQ-E ip/fw/…). Tuščios reikšmės praleidžiamos. |

| Parametras | Numatytasis | Aprašymas |
| --- | --- | --- |
| `transports` | visi trys | Seką (arba CSV eilutę), ribojančią nuskaitymą. Verta nurodyti, kai žinote, ko norite — BLE yra lėčiausia grandis. |
| `scan_timeout` | 5 | Skenavimo langas kiekvienam perdavimo būdui sekundėmis; užkulisiai apriboja iki 1–20. |
| `timeout` | 60,0 | HTTP riba visam iškvietimui (kaip ir kitur SDK). |
| `auto_start_backend` | `True` | Sukuria vietinį užkurtį, jei joks neveikia. Niekada nesukuria nuotolinio `backend_url`. |

> **Jau atidaryti jutikliai rezervuare nerodomi.** Prijungtas BLE įrenginys nustoja siųsti skelbimus, o atidaryto COM prievado negalima patikrinti, todėl atradimo sąraše pateikiami tik tie įrenginiai, su kuriais *galima prisijungti*. Tikėtina, kad iš karto po prijungimo rezultatas bus tuščias — naudokite `list_daq_sensors()` tam, ką jau turite. Transportai, kurių nuskaitymas negali būti vykdomas (neįdiegta „bleak“ / „zeroconf“), yra praleidžiami, o ne pranešami, todėl kompiuteris be „Bluetooth“ vis tiek gauna atsakymus apie USB ir ETH.

### Sąrašas

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Kuris DAQ maitina kurią kamerą — `daq_bindings()`

Užfiksuoto vaizdo atspindžio koeficientas ir `.daq`, kurį jis išsaugo, naudoja su ta kamera ar matrica susietą DAQ, kitaip – naujausią DAQ, kurį turi užkulisinė sistema. Tai nustato GUI šviesos jutiklio pasirinkimas ir `chloros-cli daq pool-bind`; taip pat:

```python
chloros_sdk.set_daq_bindings(cameras={"214701281": "COM7"},          # a sensor id or an address
                             arrays={"array-1779862544497": "daq-e-def330"})
chloros_sdk.set_daq_bindings(cameras={"214701281": None})            # None clears it
for serial, b in chloros_sdk.daq_bindings()["cameras"].items():
    print(serial, b["binding"], b["source"], b["resolved"]["in_pool"])
```

`daq_bindings()` grąžina `{"cameras": {serial: b}, "arrays": {array_id: b}}`: kiekvienas `b` saugo `binding` (nustatyta reikšmė), `source` (`project`, iš projekto `sensors.json` arba `runtime`), `known` / `known_as` (arba ar kameros ar masyvo raktų pavadinimai yra žinomi Chloros, ir kaip: `connected`, `project` — atidaryto projekto `cameras.json` — arba, jei tai kamera, `seen`: anksčiau atidarytas šiame kompiuteryje; `known: False` – raktui, kuris nieko neapibūdina) ir `resolved` (jis dabar priskirtas šiam baseino jutikliui: `in_pool`, `sensor_id`, `address`, `model`, `streaming`). Vykdymo metu nustatytas susiejimas turi pirmenybę prieš projektosesijos metu; projekto failas nėra keičiamas. Atjungus kamerą, masyvą ar DAQ, nutraukiamos visos su ta įranga susietos sąsajos, nepriklausomai nuo to, kas jas nustatė (masyvo elementai susieti su pačiu masyvu).

`set_daq_bindings()` susieja tik tą kamerą, kuri yra prijungta, yra atidarytame projekte arba anksčiau buvo atidaryta šiame kompiuteryje, ir tą masyvą, kuris yra prijungtas arba yra atidarytame projekte (arba raktą, kuris jau yra susietas). Bet kuris kitas raktas sukelia `ChlorosConnectError`, pavadindamas jį (`400`, `code`, `UNKNOWN_BINDING_TARGET`, su raktų `unknown`), ir **niekas** tame iškvietime nėra susietas. `None` išvalo bet kurį raktą.

### Aplinkos + objekto atspindžio koeficientai — `light_sensor_reflectance()`

Du sujungti duomenų surinkimo įrenginiai (DAQ), vienas nukreiptas į dangų (aplinkos), o kitas — į žemę (objekto), pateikia objekto atspindžio ir augmenijos indeksus pagal naujausius matavimus: GUI porą „Aplinka + Objektas“ ir `chloros-cli daq reflectance`, antrą kartą neatidarant jutiklių. Abu įrenginiai turi būti prijungti ir perduoti duomenis.

```python
r = chloros_sdk.light_sensor_reflectance("daq-e-def330", "daq-e-54b774")
print(r["reflectance_type"])                       # "Apparent Reflectance" when both are calibrated
for name, band in r["bands"].items():
    print(name, band["rho"] if band["valid"] else band["reason"])
for idx in r["indices"]:
    print(idx["name"], idx["value"])
```

```python
light_sensor_reflectance(
    ambient_sensor_id, object_sensor_id, *,
    bands=None,          # [{"name", "center_nm", "fwhm_nm", "role"}]; role blue / green / red / nir
    preset=None,         # a preset id from daq_band_presets(); default: the suggested one, else "GENERIC"
    expressions=None,    # custom indices: [{"name": "MyNDVI", "expr": "(NIR_850 - Red_660) / (NIR_850 + Red_660)"}]
    builtins=True,       # include the built-in indices
    backend_url="http://127.0.0.1:5000", timeout=30.0,
) -> dict
```

- **Juostos.** Kiekvienos juostos atspindžio koeficientas yra objekto juostosintegruotas spinduliavimo intensyvumas aplinkos fone. `daq_band_presets(filter_code=None)` pateikia juostų rinkinius: po vieną kiekvienam „LATTICE M3C“ filtrui (`RGN`, `OCN`, `NGB`, kameros savasis juostų modelis, taigi DAQ NDVI ir kameros NDVI yra tas pats dydis) bei `GENERIC` plačiajuostis. Jo „`suggested`“ yra iš anksto nustatytas atitikmuo „`filter_code`“, kitaip – pirmojo prijungto fotoaparato filtras (`suggested_from` nurodo, kuris).
- **Grąžina** `bands` (kiekvienam dažnių juostai: `rho`, `valid`, `reason`, `e_ambient`, `e_object`), `indices` (įmontuotosįdėklai), `custom` (kiekvienas su `value` arba `valid: False` ir `error`), `invalid_bands`, `reflectance_type`, `preset` ir abiejų rodmenų `timestamps` (`ts_ns`). Juosta, kurios aplinkos jutiklis negalėjo išmatuoti (už jo matavimo diapazono ribų, esant triukšmo lygiui), yra pripažįstama negaliojančia, nurodant priežastį, o kiekvienas indeksas, kuriam ji reikalinga, yra praleidžiamas ir niekada neskaičiuojamas pagal nulį.
- **Akivaizdusis arba santykinis.** Nuo masto priklausomi indeksai (SAVI, OSAVI, EVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI) reikalauja absoliutaus santykio, todėl jie pateikiami tik tada, kai abu jutikliai yra kalibruoti pagal galią (kiekvienas turi `calibration_bundle_sha`): `"Apparent Reflectance"`. Priešingu atveju rezultatas yra `"Relative Reflectance"`, kur pateikiami tik nuo skalės nepriklausomi indeksai.
- **Dvi skirtingos bangų ilgių tinklelės** lyginamos pagal jų sutapimą: aplinkos jutiklio taškai, esantys objekto diapazone, ir objekto spektras, interpoliuotas į juos.
- **Du jutikliai, niekada ne vienas.** Vieno jutiklio pavadinimas kaip abiejų (tas pats identifikatorius, bet kokia rašyba, kurią priima duomenų bazė) sukelia klaidą `ChlorosConnectError`: jutiklio rodmuo pačiam sau yra 1,0 kiekviename diapazone, o tai reiškia, kad nieko nematuoja. Backend taip pat tai atmeta (`POST /api/daq/indices` atsako į `400`, `code` – į `SAME_SENSOR`, kai SDK siunčiami `ambient_sensor_id` ir `object_sensor_id` yra vienas sujungtas jutiklis, pvz., jo ID ir jo prievadas).

### Bendras naudojimas su CLI ir kitais skriptais

Backend'o DAQ jutiklių grupę bendrai naudoja visi kompiuteryje esantys CLI ir SDK klientai. Jei jutiklis jau yra grupėje — atidarytas `chloros-cli daq pool-connect` arba kitu skriptu — iškvietus `connect_daq_sensor(port="COM3")` iš Python grąžinamas pasiskolintas identifikatorius: jo „`disconnect()`“ yra neveikiantis, todėl jūsų skriptas neišplėš jutiklio iš po kliento, kuris jį atidarė, išeinant iš apimties. Tas pats galioja ir „`sensor_id=`“ (įvedamam kaip „`:`“ arba „`-`“ – tinka bet kuris variantas), kai DAQ-E, pavadintam pagal jo IP adresą, kai jis buvo atidarytas pagal kompiuterio vardą (arba atvirkščiai), taip pat „zero-flag“ išmaniojo aptikimo atveju, kai užkulisinė sistema atsako su jau turimu jutikliu — `with` blokas visada atleidžia tik tą jutiklį, kurį pats atidarė.

Chloros GUI **nėra** šio rezervo dalis: jo DAQ skydelis palaiko savo ryšį su jutikliu, todėl „`connect_daq_sensor`“ niekada nepasiskolina jutiklio, kurį atidarė GUI. Nenaudokite to paties jutiklio iš GUI ir iš skripto tuo pačiu metu. DAQ-U arba DAQ-M, kurį GUI paprastai valdo, tiesiog nepavyksta atidaryti, tačiau DAQ-E priima antrąją sesiją: skripto prisijungimas pakeičia įrenginio duomenų surinkimo nustatymus GUI aplinkoje, o jo „`disconnect()`“ sustabdo įrenginio srautą, dėl ko nutraukiamas GUI tiesioginis vaizdas ir bet koks jo atliekamas įrašymas. Pirmiausia uždarykite jutiklį GUI aplinkoje.

### PTP „Grandmaster“ — `time_sync_status()`

Fone veikia PTP „Grandmaster“, iš kurio „LATTICE“ kameros ir DAQ-E įrenginiai gauna savo laiko žymes. Jis paleidžiamas pagal poreikį: `connect_camera()`, `connect_array()`, `connect_daq_sensor()` ir `connect_all()` jį paleidžia, jei jis neveikia — įskaitant prisijungimą, kuris vėliau nepavyksta, pavyzdžiui, kai „`connect_all()`“ bando prisijungti prie DAQ-E įrenginio, kurio nėra — taip pat tai daro „`test_array_sync()`“ ir kiekvienas iškvietimas, kuris atidaro kameras vienai komandai (`capture_cameras_transient()`, `start_multi_camera_stream()`, suderinimo iškvietimai, `capture_live_index()`).

- **Kur tai skelbiama.** Per laidinius tinklo adapterius, prijungtus prie kamerų (įskaitant `169.254.x.x` vietinį adresą), kitais atvejais – per numatytojo maršruto adapterį, jei jis yra laidinis. Niekada per „Wi-Fi“ ar virtualų adapterį („Tailscale“, WSL): už jų nėra nei kameros, nei „DAQ-E“. Jei tokio adapterio nėra, programa veikia skelbdama nieką (`health` `NO_SOCKETS`) ir susieja su adapteriu, kurį įjungia kitas prisijungęs fotoaparatas ar DAQ.
- **Po ryšio nutrūkimo.** Kai kameros adapteris praranda ryšį ir jį atgauna (ištraukus kabelį, pakeitus komutatorių, iš naujo identifikavus USB adapterį), „Grandmaster“ pats atkuria savo lizdus be perkrovimo, ir iš naujo patikrina kiekvieną prijungtą „DAQ-E“, iš naujo įjungdamas tą, kurio ryšys buvo nutrūkęs. Jis taip pat atkuria ryšį, kai visos kameros ir „DAQ-E“, kurias jis aptarnavo, 15 sekundžių lieka neaktyvios.
- **Ar pasiekiamas kabelis?** Perskaitykite `time_sync_status()` (`GET /api/time-sync/status`). `state` rodo tik tai, kad veikia pagrindinio įrenginio procesas; `health` rodo, ar jo paketai išsiunčiami: `OK` (gedimų nepastebėta), `STOPPED`, `NO_SOCKETS`, `SENDS_FAILING`, `DEGRADED` (kai kurie adapteriai neveikia) arba `PEERS_SILENT` (kameros ir DAQ-E, kuriuos jis aptarnavo, nustojo atsakyti), o `health_detail` pateikiamas sakinys. `announce_sent` / `sync_sent` / `delay_resps_sent` skaičiuoja tik išsiųstus pranešimus; `announce_send_errors`, `sync_send_errors`, `follow_up_send_errors`, `delay_resp_send_errors`, `last_send_error`, `failing_interfaces` ir `last_delay_req_age_s` apibūdina tuos, kurie nebuvo išsiųsti, o `rebinds` / `last_rebind` – pakartotinius siuntimus. `sync_sends_slow` skaičiuoja sinchronizacijas, kurių siuntimas truko ilgiau nei 200 us, o `sync_send_max_us` yra ilgiausias; kiekvieno „Follow_Up“ kilmės laikas skaičiuoja ne daugiau kaip 200 us jo siuntimo. „Grandmaster“ veikia atskirame procese (`own_process`, `process_pid`), Windows sistemoje jam suteikiamas aukštas prioritetas (`process_priority`), todėl užimtas „backend“ negali atidėti laiko žymėjimo ir vienu metu perkelti visų kamerų laikrodžių; jei to proceso nepavyksta paleisti, jis veikia „backend“ viduje kaip ir anksčiau (`own_process` false).

```python
st = chloros_sdk.time_sync_status()
if st.get("health") not in (None, "OK", "STOPPED"):
    print("PTP:", st["health"], "-", st.get("health_detail"))
    chloros_sdk.restart_time_sync()
```

| Funkcija | Ką daro |
| --- | --- |
| `time_sync_status()` | „Grandmaster“ būsena, būklė, laikrodžio identifikatorius, prioritetai ir pranešimų skaitikliai (žr. aukščiau). `chloros-cli time-sync status`. |
| `time_sync_peers()` | PTP pavaldiniai, kuriuos jis girdi (kameros, DAQ-E): `peers`, kiekvienas `port_identity`, `last_seen_ip`, `last_delay_req_age_ms`, `delay_req_count`. `time-sync peers`. |
| `time_sync_cameras()` | Kiekvienos prijungtos matricos kameros `PtpStatus`, `PtpOffsetFromMaster` ir `PtpMeanPathDelay` (kamera, kurios programinėje įrangoje nėra PTP mazgų, turi tik serijinį numerį). `time-sync cameras`. |
| `restart_time_sync()` | Sustabdykite ir iš naujo paleiskite pagrindinį mazgą; po to iš naujo patikrinamas kiekvienos sujungtos DAQ-E kameros PTP (`daq_ptp_rechecked`). Sukelia `ChlorosConnectError`, kai negrįžta. `time-sync restart`. |
| `set_time_sync_priority(priority1=None, priority2=None)` | Jo PTP prioritetai (0–255, mažesnis laimi „best-master“ rinkimus), tinkle, kuriame yra kitas PTP pagrindinis mazgas. Reikia, kad jis veiktų. `time-sync set-priority`. |

Kiekvienas iš jų paima `backend_url=` ir `timeout=` ir paleidžia vietinį užkulisinį procesą, kuris dar neveikia.

---

## Kameros pagalbinės programos

Ką dar atlieka „`chloros-cli lattice`“, be prisijungimo ir vaizdo fiksavimo, kaip paprastos funkcijos „`chloros_sdk`“ (modulis „`chloros_sdk.camera_utils`“). Kiekviena iš jų iškvietia užkurtį, kurį naudoja CLI komanda, laikydamasi CLI taisyklių, todėl abi veikia panašiai. Jos priima kameros **serijinį numerį**, o ne sesiją, ir tuos pačius raktinius žodžius „`backend_url=`“ / „`timeout=`“, kuriuos naudoja „smart-connect“ iškvietimai; jei užpakalinė sistema neveikia, ji paleidžiama, kaip tai daro „`connect_camera()`“. Atsisakymo atveju iškeliama „`ChlorosConnectError`“ su užpakalinės sistemos nurodyta priežastimi.

```python
import chloros_sdk as c

c.set_camera_color_profile("214701281", profile="natural")
c.set_camera_highlights("214701281", -40)
print(c.camera_firmware_status()["cameras"])
st = c.network_adapters(estimate=True, cameras=4, pixel_format="BayerRG12")
```

| Funkcija | Ką daro |
| --- | --- |
| `camera_color_profile(serial)` / `set_camera_color_profile(serial, profile=, custom_cct_k=)` | Ekrano spalvų profilis: `raw`, `linear`, `natural`, `enhanced` arba `custom_temp`, ir jo spalvų temperatūra (ribojama nuo 2000 iki 10000 K; rodoma, kai profilis yra `custom_temp`). |
| `camera_color_adjust(serial)` / `set_camera_color_adjust(serial, saturation=, contrast=, reset=False)` | Ekrano sodrumas ir kontrastas, po 0–200 % (100 = nepakitęs); `reset=True` abu parametrus grąžina į 100. |
| `camera_highlights(serial)` / `set_camera_highlights(serial, value)` | Sumažina ryškiausių, baltiausių ekrano pikselių atvaizdavimą: -100..0 (apkarpyta link nulio, tada apribota; 0 = išjungta). |
| `camera_white_balance_once(serial, settle_s=None)` | Vienkartinis baltos spalvos balansavimas: kamera trumpai transliuoja vaizdą, suderina parametrus ir grąžina `red_ratio` / `blue_ratio` (`ratios_read` „False“, jei negali jų pranešti). Ji išlaiko nustatytą baltos spalvos balansą; visi kiti jos atvirojo failo įrašyti parametrai grąžinami tokie, kokie buvo rasti. |
| `set_camera_white_balance(serial, mode=None, red=None, blue=None)` | `mode="auto"` (nuolatinis) arba `"off"`; arba fiksuoti `red` / `blue` balanso santykiai, kurie pirmiausia išjungia automatinį režimą. Santykis, kurio kamera nepripažįsta, yra nurodytas po `errors`. |
| `camera_presets()` / `apply_camera_preset(serial, preset)` | Kameros išankstiniai nustatymai, ir pritaikius vieną iš jų: kamera išlaiko nustatymus, kuriuos nurodo išankstinio nustatymo pavadinimai (atsakyme nurodytas `settings`, nes kamera juos nuskaito vėliau, pvz., kadrų dažnį, kuriuo ji veikia; `None` – laukui, kurio išankstinis nustatymas nepavadina), o visi kiti nustatymai, kuriuos įrašė „open“, grįžta į pradinę būseną. `"default"` atkuria kameros gamyklinius numatytuosius nustatymus (gamyklinius nustatymus su laisvai veikiančia automatine ekspozicija, išjungtu ryšio pralaidumo apribojimu ir defektų korekcija, išlaikant kalibruotą RGB kameros baltos spalvos balansą), kurie nėra išsaugomi kameros atmintyje: atsakyme yra `factory_defaults: True` ir visi `settings` laukai. `camera_presets()` komandoje `default` yra `{factory_defaults: True, description}`. |
| `camera_filter_models()` / `camera_filter_model_info(name)` | Filtrų modeliai ir vieno modelio detalės (juostos, spektrinio atsako sąlyginis skaičius). Kameros filtro modelis nuskaitomas iš jos modelio eilutės; jis nenustatomas rankiniu būdu. |
| `camera_firmware_status(serial=None)` | Kiekvienos kamerosprograminė įranga: `current_version`, `available_version`, `update_offered`, `needs_minimum_update` ir `minimum_firmware`. |
| `update_camera_firmware(serial, force=False)` | Įrašykite šios Chloros versijos programinę įrangą į vieną kamerą: atstatykite gamyklinius nustatymus, įrašykite, perkraukite, Chloros baziniai nustatymai perrašomi; kameros kalibravimas išlieka. Trunka keletą minučių; kamera turi būti įjungta. `force=True` iš naujo įrašo programinę įrangą į esamą kamerą. |
| `camera_power_probe(serial)` | Kameros maitinimo ir šiluminių mazgų duomenys bei jos temperatūra. |
| `sleep_camera(serial)` / `wake_camera(serial)` | Kameros mažos galios budėjimo režimas (ji lieka miego būsenoje, kol bus pažadinta: jos ryšio pralaidumo riba įjungta (`DeviceLinkThroughputLimitMode` `On`) nustatyta iki minimumo, įjungtas energijos taupymo režimas, jei kamera jį turi), su temperatūra prieš / po; pažadinus ryšys grąžinamas į tą būseną, kokią jis buvo miego režime (ribą, režimą ir paketų vėlavimą `GevSCPD`, kurį kamera išlaiko lygų 0, kol riba įjungta; įrašas saugomas šiame kompiuteryje, todėl jį randa ir vėliau pažadinusi serverio pusė), arba, jei kamera, kurios šis kompiuteris nepervedė į miego režimą, atrodo esanti miego būsenoje (jos riba nustatyta žemiausiame lygyje), išjungus ribą ir palikus `GevSCPD` vertę 0 esant 80 (gamykliniai nustatymai); ribinis dydis, nustatytas virš minimalaus lygio, paliekamas toks, koks yra. Išsaugoma tik maitinimo būsena; visi kiti parametrai, į kuriuos buvo įrašyta atviroji reikšmė, grąžinami į rastą būseną. |
| `calibrate_camera_with_target(serial, filter=None, attempts=30, settings=None)` | Fiksuokite kadrus, kol į kadrą pateks MAPIR kalibravimo taikinys, ir pritaikykite kanalų koeficientus pagal jo skydelius: `target_id`, `panel_dns`, `coefficients`. |
| `network_adapters(estimate=False, cameras=1, pixel_format="BayerRG8")` | Šio kompiuterio „GigE Vision“ Ethernet adapteriai (MTU, ryšio greitis, „jumbo“ kadrai) ir bet kokie `issues`; `estimate=True` nurodo kadrų dažnį ir pralaidumą, kuriuos gautų `cameras` kameros. |
| `fix_network_settings()` | Taiko „GigE Vision“ adapterio nustatymus šio kompiuteriokameros adapteriui. Tai pakeičia adapterio nustatymus ir veikia tik tada, kai Chloros veikia kaip administratorius; priešingu atveju programa užsidaro, pateikdama rastų duomenų žurnalą. |
| `apply_vignette_correction(input_dir, output_dir, lens_model=None)` | Atlikite vaizdų, esančių aplanke, vinjetės korekciją, kiekvienam vaizdui pritaikydami atitinkamo objektyvo plokščio lauko žemėlapį: `processed`, `lens_models` (objektyvas, pagal kurį buvo koreguotas kiekvienas failas), `skipped` (su priežastimis). Be `lens_model` kiekvieno failo objektyvas nustatomas pagal jo EXIF „Model“ lauką; `lens_model` taikomas failams, kurių „Model“ laukelyje nenurodyta jokia kamera, o failas, kurio „Model“ duomenys prieštarauja šiam nustatymui, yra praleidžiamas. Tik failai. |
| `read_dls(mac, filter=None, camera_serial=None)` | Vienas DLS (Bluetooth žemyn nukreipto šviesos jutiklio) rodmuo: spektras, spinduliavimo intensyvumas kiekvienoje juostoje ir juostų stiprumai, nustatomi pagal pačios kameros atsaką, kai nurodytas `camera_serial`. `discover_daq_sensors(transports=["ble"])` nustato MAC adresą. |
| `probe_system(pixel_format="BayerRG12", discover=True)` / `system_info()` | Kompiuterio aparatinė įranga, skirta kameroms, ir rekomenduojama konfigūracija (`report_text`), taip pat sistemos CPU / GPU / „PyTorch“ vykdymo aplinka. |

- **Kai kuriems reikia, kad kamera būtų skirta tik jiems.** `camera_white_balance_once()`, `apply_camera_preset()`, `sleep_camera()` / `wake_camera()`, `calibrate_camera_with_target()` ir `update_camera_firmware()` atidaro kamerą. Kol Chloros ją laiko (prijungta programėlėje, su `connect_camera()` kaip masyvo narys arba šiuo metu naudojama kitos komandos, pavyzdžiui, masyvo testas arba fotografavimas, atidarant jo kameras), serveris atsisako su HTTP 409 `CAMERA_HELD` (tiek `code`, tiek `error_code`), nurodydamas, kaip jį atlaisvinti. Spalvų nustatymai, ryškiausios vietos, `camera_power_probe()` ir rodmenys veikia prijungtoje kameroje.
- **Spalvos, ryškiausios vietos ir baltos spalvos balansas skirti RGB kameroms.** Vienos juostos monochromatinėje (M3M) kameroje šie nustatymai nieko nekeičia ir grąžina `{"skipped": True, "reason": ...}`, kaip ir CLI. Tai yra ekrano nustatymai: tiesioginis vaizdas ir ekrano lygio kadrai, bet jokiu būdu ne spinduliavimas ar atspindys.

---

## Laikinieji masyvai, suderinimo profiliai ir indeksai

Ką atlieka „`chloros-cli lattice`“ be nuolatinio ryšio, kaip ir funkcijos „`chloros_sdk`“ (modulis „`chloros_sdk.array_utils`“): `continuous`, `multi-capture`, `multi-stream`, `multi-test`, `multi-detect`, `analyze-array`, `align-calibrate`, `align-apply`, `align-stream`, `align-info`, `align-reorder` ir `index`. Kiekviena iš jų siunčia tą patį užklausą, kurią siunčia CLI komanda, naudodama CLI numatytuosius parametrus.

```python
import chloros_sdk as c

prof = c.calibrate_alignment_profile(["214000535", "214000551", "214000556"],
                                     "D:/rig/alignment_profile.json", method="orb", model="rigid")
print({sn: t["reprojection_error"] for sn, t in prof["transforms"].items()})

c.capture_aligned_multiband("D:/rig/alignment_profile.json", "D:/rig/out")
idx = c.capture_live_index("D:/rig/alignment_profile.json", preset="NDVI",
                           output_dir="D:/rig/ndvi", capture_level="reflectance")

with c.start_multi_camera_stream(["214000535", "214000551"], "D:/rig/stream", fps=5, count=50) as job:
    print(job.wait()["final_stats"])          # waits for the 50 groups, then closes the cameras
```

- **Jos pačios atidaro kameras.** Kiekviena komanda atidaro savo kameras, atlieka savo užduotį ir jas uždaro, todėl jai reikalingos kameros, kurių Chloros neturi (prijungtos programėlėje, su „`connect_camera()`“ arba kaip masyvo elementas); užkulisiai atsisako su HTTP 409 „`CAMERA_HELD`“ ir nurodo, kaip atlaisvinti vieną. Laikinas atidarymas trunka šiek tiek laiko (apie 40 s kelioms kameroms). Pakartotiniams įrašams `connect_array()` vieną kartą yra greičiausias būdas, ir jis tinka visų tipų eksportavimui, kur laikinis įrašymas išsaugo debayered vaizdą. `calibrate_alignment_profile()` yra išimtis: prijungtame tų kamerų masyve jis apdoroja duomenis iš paties masyvo kadrų (`used_live_array`).
- **`serials`** yra kameros, kurias reikia atidaryti, pirmoji iš jų suaktyvina trigerį; `None` atidaro visas kameros tinkle.
- **`settings`** yra kamerų nustatymų perrašymų žodynas, kurį sudaro laukai, nustatomi per CLI žymes: `exposure_time` (µs), `gain` (dB), `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `frame_rate`, `gev_packet_size` ir `preset` (pagal pavadinimą nustatytas fotoaparato nustatymas). Nurodytas ekspozicijos arba stiprinimo parametras, kaip ir žymės, išjungia automatinį valdymą.
- **`gpu`**: `True` priverčia naudoti GPU „warp“, o `False` – CPU (`--gpu` / `--no-gpu`); `None` leidžia pasirinkimą palikti užkulisiniam moduliui.
- **`keep_settings`**/**`clear_settings`** (kiekviena funkcija, atidarianti kameras, numatyta reikšmė – `False`): pabaigoje kiekviena kamera grąžinama į tą būseną, kokioje buvo rasta; `keep_settings=True` vietoj to išsaugo vykdymo nustatymus pačios kameros atmintyje (laisvas veikimas), `clear_settings=True` atkuria gamyklinius tos atminties nustatymus — žr. [Kameros atmintis](#camera-memory--keep_settings--clear_settings). Atsakymas (užduoties atveju – `stop()`) perduoda `camera_memory`.
- **Keliai yra užkulisio**: santykiniai keliai paverčiami absoliučiais, atsižvelgiant į jūsų darbo katalogą, kai užkulisinė programa veikia šiame kompiuteryje. Failo „alignment-profile“ funkcijos (`alignment_profile_info()`, `reorder_alignment_profile()`) skaito failą šiame kompiuteryje, kaip tai daro CLI.

| Funkcija | Ką ji daro |
| --- | --- |
| `start_camera_continuous(serial, output_dir="output", *, format="tiff", jpeg_quality=95, queue_depth=64, settings=None)` | Nepertraukiamai įrašo vienos kameros vaizdą, kol nėra sustabdoma. Grąžina `CliJob`. `tiff` vykdymo kadrai yra `raw` failas – masyvas, į kurį įrašomi tos kameros kadrai (viena mozaikos plokštuma pagal jutiklioreikšmėmis, nesuspaustas); `png` ir `jpg` talpina 8 bitus, taigi šie ciklai yra demozikuoti kadrai, skirti peržiūrai. Prisijungęs arba sujungtas DAQ įrašo po vieną `.daq` už kiekvieną kadrą į ciklo`daq/` aplanką (`daq_files` jos statistikoje). Kiekvienas kadras įrašomas vieną kartą su visais metaduomenimis (modelis, įrašymo laikas, objektyvas, juostos, kalibravimas neapdorotame kadre); `stop()` grąžinamas tik tada, kai įrašyti visi jau užfiksuoti kadrai (iki 25 s), ir tik tada kamera išjungiama. Kamera, palikta parengta aparatinio trigerio veikimui (`TriggerMode` `On`) nustatoma į laisvo veikimo režimą visam ciklo laikui (pradinio atsakymo `trigger` nurodo, kas buvo rasta). Sustabdžius kamerą, ji grąžinama į tą būseną, kurioje buvo rasta — su jos suveikimo nustatymais ir visais vykdymo metu pakeistais parametrais — išskyrus atvejus, kai naudojamas `keep_settings=True`, kuris išsaugo vykdymo nustatymus pačios kameros atmintyje (laisvasis režimas), arba `clear_settings=True`, kuris iš naujo nustato tą atmintį. Ciklas, kurio metu neįrašomas nė vienas kadras, baigiasi po 10 s su `stop_reason` ir `no_frames`, o priežastis nurodyta `last_error`, o jo `stop()` grąžina tą priežastį kaip `error`. Kadrų praradimai dėl to, kad įrašymo moduliai nespėjo apdoroti duomenų, yra skaičiuojami, o `drop_reason` nurodo priežastį. Jei Chloros, kurią laiko kamera, naudokite `CameraSession.capture_repeated()`. `lattice continuous`. |
| `start_multi_camera_stream(serials=None, output_dir="output", *, fps=3.0, count=None, format="tiff", jpeg_quality=95, settings=None)` | Srauto sinchronizuotos kadrų grupės `fps`, `count` grupėms arba kol bus sustabdyta. Grąžina `CliJob` (jo `start_reply` turi `n_cams` ir laidą `pacing`); jo galutinėse statistikos duomenyse skaičiuojamos paimtos, įrašytos ir atmestos grupės, failai, kurių rašytuvas negalėjo įrašyti, ir grupės, dėl kurių kamera nieko nepateikė. `lattice multi-stream`. |
| `capture_cameras_transient(serials=None, output_dir="output", *, format="tiff", jpeg_quality=95, settings=None)` | Vienas sinchronizuotas įrašymas, vienas debayeringo apdorotas vaizdas iš kiekvienos kameros: `results` (`filepath`, `serial`, `frame_id`) ir `pacing`. Pasirodo pranešimas apie trūkstamą kamerą, nurodant, kiek failų buvo išsaugota pirmiausia. `lattice multi-capture` be prijungto masyvo. |
| `test_array_sync(serials=None, count=10, ptp_settle_s=None, timeout=None)` | `count` sinchronizuoti kadrai su fiksuota ekspozicija: `sync_groups`, `master`, po vieną `group_timestamps` eilutę kiekvienam suveikimui (kamera, kuri praleido vieną kadrą, nėra savo eilutėje) ir `sync`: kiekvienos kitos kameros nuokrypis nuo pagrindinės, pagal kiekvieną suveikimą (`offsets_us`: pagal kamerą `mean_us`, `min_us`, `max_us`, `jitter_us`, `groups`), per VIENĄ taktą (`clock_domain`: `ptp` arba `device` — PTP išjungtas, kiekviena kamera naudoja savo veikimo laikrodį, todėl jos nuokrypis nustatomas paleidimo metu, o sinchronizaciją matuoja tik svyravimas). Trigeris, kurio žymos yra įrenginio laikrodžio ir PTP mišinys (PTP išjungtas arba vis dar sinchronizuojamas), yra praleidžiamas ir įskaitomas (`groups_mixed_domain`), kaip ir tas, kur PTP laikrodis vis dar yra daugiau nei `agree_within_us` nuo pagrindinio (`groups_clocks_disagree`); tai patvirtina `notes`. `clock_offset_mean_us` / `clock_offset_jitter_us` yra didžiausios vidutinės vertės ir svyravimo reikšmės vienai kamerai. `success` yra klaidingas, o `error` – tada, kai praleistas trigeris, kai mažiau nei du trigeriai suderina visas kameras pagal vieną laikrodį arba kai laikrodžiai niekada nesutampa (`sync.problems`); eilutės grąžinamos bet kuriuo atveju. Jis paleidžia PTP „grandmaster“, jei šis neveikia (`time_sync` nurodo, kad `grandmaster_running` ir `grandmaster_started_by_this_test`), įjungia kiekvienos kameros PTP (kaip tai daro `connect_array()`) ir prieš pirmąjį trigerį palaukia, kol kiekvienos kameros PTP laikrodis sinchronizuosis su pagrindiniu laikrodžiu ir stabilizuosis: `PtpStatus` „Slave“ sinchronizuotas su vienu pagrindiniu laikrodžiu, nuokrypis ne didesnis kaip 100 us ir stabilus 3 s, o kiekviena kamera taip sinchronizuota, be pertraukos, 120 s — maždaug 2 minutes nuo įjungimo. Laikrodis, kuris ką tik sinchronizavosi, maždaug 2 minutes po to, kai nuskaito „Slave“, nukrypsta iki ~200 us, o pačios kameros `PtpOffsetFromMaster` to nerodo; per 120 s kameros pasiekia stabilų laikrodį su ~10 us nuokrypiu. Kamera, kuri vis dar yra užfiksuota nuo ankstesnio bandymo, kurio metu PTP buvo įjungtas (kaip tai matė šis backend), skaičiuoja nuo to užfiksavimo momento, todėl vienas bandymas, atliekamas iškart po kito, nebereikia laukti; kamera, kurią bandymas įjungia, arba kamera, rasta su įjungtu PTP, kurios sinchronizacijos šis backend nematė (įjungta kitur arba prieš backend perkrovimą), laukia savo 120 s, o abiejų ką tik sinchronizuotų kamerų masyve jos vis dar laukia savo laiko. Kamera, kuri išeina iš „Slave“ režimo, viršija 100 us ribą arba seka kitą „grandmaster“ 3 matavimus iš eilės (1,5 s) laukimo metu, vėl pradeda skaičiuoti; vienas ar du tokie rodmenys užregistruojami (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`), o skaičiavimas tęsiasi, o „grandmaster“ identifikatorius, kurio negalima nuskaityti, laikomas nežinomu, o ne pasikeitimu. Jei visų kamerų skaičiavimas prasideda iš naujo vienu metu (per sekundę), tai reiškia, kad keičiasi „grandmaster“ laikas, o ne kamerų: užpakalinės sistemosžurnale nurodyta `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …`, `ptp_lock` įtraukia jį į `grandmaster_steps`, o pasibaigus laukimo laikui klaida prasideda nuo `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …`. Šiam atvejui riba nėra pratęsiama. `ptp_settle_s` (0–600; `0`, jei laukimo be stabilizavimo nėra) nustato vykdymo stabilizavimo laiką; kita reikšmė atmetama (HTTP 400). Laukimo trukmė ribojama iki apdorojimo laiko plius 90 s (pagal numatytuosius nustatymus – 210 s, ne mažiau kaip 90 s), o iškvietimas laukia užpakalinės sistemos `timeout` s: pagal numatytuosius nustatymus – 240 plius apdorojimo laikas (360 s), kaip ir `chloros-mcp` bei `test_array_sync`. `ptp_lock` turi `converged`, `waited_s`, `timeout_s`, `settle_s`, `locked_s` (kiek laiko kiekviena kamera buvo užfiksuota ir stabili), `found_settled` (kameros, kurios jau buvo užfiksuotos nustatytą laiką, kai buvo aptiktos) ir, kiekvienai kamerai atskirai, `status`, `offsets_ns`, `slave_s` (kiek laiko ji buvo sinchronizuota; „None“: nesinchronizuota) ir `remaining_s` („None“, kai nė vienos kameros PTP neįsijungia). Backend žurnale kas 5 s yra eilutė su kiekvienos kameros būsena, kiek laiko ji jau yra sinchronizuota ir kiek laiko liko. Kiekviena kamera įjungiama su išjungtu PTP; po to laikrodžiai sinchronizuojasi per maždaug 10 s. Vėliau kiekviena kamera grąžinama į tą būseną, kokią nustatė testas – jos PTP, automatinė ekspozicija ir stiprinimas, kuriuos testas vykdymo metu laiko išjungtais (`ExposureAuto` / `GainAuto` ir rankinis `ExposureTime` / `Gain`), o likusios, išskyrus atvejus, kai `keep_settings=True` — taigi kamera, kurios PTP liko įjungtas (`PtpEnable=True` arba bandymas su `keep_settings=True`) lieka užfiksuota, ir kai šis užpakalinis modulis mato, kad ji užfiksuota nustatytą stabilizavimo laiką, vėlesnis testas jos nelaukia. Jei laikrodžiai nesustabilizuojasi, `success` yra klaidingas prieš bet kokį suaktyvinimą, o `error` nurodo, ką daryti: kamera, kuri niekada nebuvo užfiksuota (`not_slave`), reiškia, kad pagrindinis serveris jos nepasiekia (`time_sync_status()`); ta, kuri nuolat iškrisdavo, pavadinta pagal tai, kiek laiko ji buvo užfiksuota (`remaining_s`); laikrodžiai vis dar suderinami (`far`): patikrinkite `time_sync_status()`, o norėdami, kad jie būtų užfiksuoti prieš bandymą, įjunkite PTP ir palikite jį įjungtą (`PtpEnable=True` kiekvienoje kameroje arba `keep_settings=True`; testas atkuria rastą būseną, todėl jis lieka įjungtas), tada paleiskite jį. `lattice multi-test`. |
| `detect_array_master(line=None)` | Kameros sinchronizavimo linijoje ir numatytoji pagrindinė kamera: `master_serial`, `slave_serials`, kurias patikrino `line`. `lattice multi-detect`. |
| `analyze_array_models(camera_models=None, *, width=2048, height=1536, n_active=None, force_tier=None, binning=1, packed="auto", pixel_format=None)` | Koks šių kamerų modelių (produktų pavadinimų, `"LATT-M3C-L87-FOCN"`) atsirastų šio kompiuterio tinkle: `tier`, `achievable_fps_max`, laidų biudžetas (`max_cams_collision_safe`, `oversubscribed`), `cam_caps` ir `fix_suggestions`. Pagal numatytuosius nustatymus aptiktų kamerų modeliai; nė viena kamera nėra atidaryta. Kameras, kurias turite, su nustatymais, kuriuos naudotų „connect“, rasite [`analyze_array_network()`](#network-analysis-helper). `lattice analyze-array`. |
| `calibrate_alignment_profile(serials=None, save_path="alignment_profile.json", *, frames=3, method="orb", model="rigid", reference_serial=None, checkerboard=None, control_points=None, preview=False, vignette=False, roi=None, mask_path=None, rms_threshold_px=None, per_cam_overrides=None, settings=None, ...)` | Nustatykite suderinimo profilį pagal tiesioginius kadrus ir išsaugokite jį. `method`: `orb`, `akaze`, `phase`, `checkerboard` (kartu su `checkerboard="9x6"` – vidiniai kampai) arba `manual` (kartu su `control_points={serial: [[x, y], ...]}` – taip pat ir atskaitos kameros). `model`: `translation`, `rigid`, `affine` arba `homography`. Išsamesnės parinktys yra CLI žymės: `max_features`, `ratio_threshold`, `matcher` (`bf` / `flann`), `knn_k`, `prefilter` (`none` / `gradient` / `clahe` / `blur` / `hist_match`), `ransac_threshold_px`, `ransac_max_iters`, `ransac_confidence`, `averaging`, `lock_rotation`, `lock_scale`, `lock_axis`; `per_cam_overrides` yra `{serial: {option: value}}`. `rms_threshold_px` atsisako išsaugoti blogesnį sprendimą. Grąžina `save_path`, `output_width` / `output_height`, `crop_roi`, `reference_serial`, `transforms` (kiekvienai kamerai `reprojection_error`, `num_matches`) ir `preview_path`. `lattice align-calibrate`. |
| `capture_aligned_multiband(profile_path, output_dir="output", *, serials=None, format="tiff", bit_depth=None, gpu=None, no_crop=False, bands=None, order=None, vignette=False, per_band=False, per_camera=False, interpolation="linear", border_mode="constant", border_value=0, settings=None)` | Vienas suderintas daugiabandis vaizdas per profilį: `files` (daugiabandis TIFF su savo `bands` ir dydžiu, plius po vieną kiekvienai juostai su `per_band` ir po vieną kiekvienai kamerai su `per_camera`) ir `vignette_corrected`. `bands` atrenka, o `order` surūšiuoja juostas pagal pavadinimus (sąrašas arba kableliais atskirtas tekstas). `lattice align-apply`. |
| `start_aligned_stream(profile_path, output_dir="output", *, serials=None, fps=3.0, count=None, ...)` | Srauto suderinti daugiajuostiniai vaizdai (tos pačios deformavimo parinktys kaip ir `capture_aligned_multiband()`), skirti `count` arba kol bus sustabdyta. Grąžina `CliJob`; jo galutiniai statistiniai duomenys: `frames_written` ir bet kokie `frame_groups_incomplete`. `lattice align-stream`. |
| `alignment_profile_info(path)` | Profilio failas, apibendrintas kaip `align-info`, išspausdina: `name`, `created`, `method`, `reference_serial`, `sensor_resolution`, `output_width` / `output_height`, `crop_roi`, `camera_serials`, `transforms` (kiekvienai kamerai: `model`, `reprojection_error`, `num_matches`, `reference`), `camera_models`, `layer_order` (kiekvienai kamerai: `band_names`, `wavelengths_nm`, `enabled`); `profile` yra visas failas. |
| `reorder_alignment_profile(path, order=None, enable=None, disable=None)` | Keisti profilio failą vietoje: `order` yra juostų pavadinimai (kiekvienas perkelia savo kameros sluoksnį aukštyn, ta pačia tvarka), `enable` / `disable` yra serijos numeriai. Grąžina naują `layer_order`. `lattice align-reorder`. |
| `index_catalog()` | Indekso nustatymai (`presets`: kiekvienas `name`, `channels`, `formula`) bei spalvų nustatymai `gradients`. `lattice index --list-presets` / `--list-gradients`. |
| `compute_index(input_path, *, preset=None, formula=None, channel_map=None, output=None, capture_level="unknown", output_format="all", colorize=False, gradient="RdYlGn", vmin=None, vmax=None, percentile=None, bg_mode="clip")` | Indeksas, susietas su suderintu daugiabandžiu TIFF failu: `preset` (`NDVI`, `NDRE`, ...) arba `formula`, susietas su simboliais `channel_map`, atitinka juostas (`{"NIR": "NIR_850", "Red": 0}`, pagal pavadinimą arba indeksą). Iš anksto nustatytassimbolis `channel_map` praleidžia ir pasirenka tą failo juostą, kurios pavadinimas prasideda šiuo simboliu (`red` -> `Red_660`, `nir` → `NIR_850_RGN`), pavadintą pagal patį failą (jo juostų sąrašą arba Chloros užfiksavimo / produkto pavadinimą `Chloros:Band{R,G,B}Name`), todėl NDVI Chloros atspindžio vaizde nereikia „`channel_map`“; du juostų deriniai, atitinkantys vieną simbolį, yra atmesti, o ne spėjami. `capture_level` nurodo, ką apima juostos: `reflectance` arba `radiance` suteikia vertėms kiekybinį pobūdį; nekalibruotas sluoksnis (`align-apply`) pateikia kokybines reikšmes; `"unknown"` nuskaito duomenis iš failo, įskaitant Chloros failo `Chloros:ProcessingLevel`. Grąžina `stats` (formą, 1–99 procentilio intervalą, LUT intervalą, naudotą `capture_level`) ir `files`, įrašytą šalia įvesties (`<input stem>_<preset>.tif`, `..._LUT.png`) arba `output`, kelias: su vaizdo plėtiniu failai pavadinami pagal jo kamieną (`output="D:/out/ndvi_FRGN.png"` įrašo `ndvi_FRGN.tif` ir `ndvi_FRGN_LUT.png`), o jei jo nėra – tai yra priešdėlis (`D:/out/ndvi` → `ndvi.tif`, `ndvi_LUT.png`); perskaitykite kelius iš `files`. Juose yra įvesties kameros įrašai (gamintojas / modelis / serijos numeris, GPS, objektyvas, fotografavimo laikas UTC, kalibravimo identifikatorius, bet niekada ne „raw-DN“ kalibravimas), `Chloros:ProcessingLevel=index`, o indeksas yra jų vienintelis kanalas (`XMP-Camera:BandName`, TIFF ir „LUT PNG“). Tik failai; be kameros. `lattice index --input`. |
| `capture_live_index(profile_path, *, preset=None, formula=None, channel_map=None, serials=None, output_dir="output", output_prefix="index", capture_level="reflectance", save_multiband=False, ...)` | Užfiksuoti, kalibruoti (`reflectance` arba `radiance`; `raw` šį žingsnį praleidžia), suderinti ir apskaičiuoti indeksą vienu žingsniu; `save_multiband=True` išsaugo kalibruotą rinkinį, kurį vėliau gali paimti `compute_index()`. Grąžina `stats` (kartu su `serials`, kurio juostas jis naudojo) ir `files`. Ji atidaro `serials` (pirmoji suaktyvina trigerį), kitaip – profilio kameras, kurių įrašas `layer_order` yra įjungtas, pirmiausia etaloninę (atidaroma net ir tada, kai jos juostos išjungtos); kamera, kurios juostos išjungtos, niekada nekalibruojama. „`channel_map`“ reikalingas tik simboliui, į kurį stekas negali atsakyti, kaip `compute_index()` pavyzdyje: išankstinio nustatymo simbolis pasirenka vieną įjungtą juostą, kurios pavadinimas prasideda tuo simboliu, patikrintą prieš atidarant bet kurią kamerą (jei neatsako nė viena arba atsako kelios, jos atmetamos, nurodant jų pavadinimus); kiekvienam simboliui reikalingas individualus `formula`. `index.tif` ir LUT nurodo steko kamerą, fotografavimo laiką (etaloninės kameros ekspoziciją, UTC) ir objektyvą, o abu nurodo indeksą kaip savo vienintelę juostą (`XMP-Camera:BandName`, pvz. `NDVI`: spalvotas PNG failas nurodo indeksą, o ne jo spalvų kanalus). `lattice index --live`. |

### `CliJob`

Rankena, kurią grąžina trys srautai. Užduotis vykdoma užkulisiuose, kur ji valdo savo kameras, kol nėra sustabdyta.

| Elementas | Aprašymas |
| --- | --- |
| `stats(timeout=10.0)` | `running`, `state`, `stats` (išsaugoti / įrašyti, atmesti, `fps`, `elapsed`, įrašymo klaidos, `stop_reason`; nuolatinė užduotis taip pat `frames_grabbed` ir `frames_queued` – kiekvienas užfiksuotas kadras yra įrašomas, įtraukiamas į eilę, atmestas arba įrašymo klaida – ir `drop_reason`, `writers` ir `write_ms`, vidutinis laikas, per kurį buvo įrašytas vienas kadras) ir `output_dir`. `state` yra `running`, `ended` (jis sustojo pats ir dar nebuvo sustabdytas), `stopping` (sustabdytas procesas išrašo savo eilę: aktualios statistikos, ir `stopped_by`) arba `finished` (jo galutinė statistika, sustabdymo `error` ir `stopped_by`). Sustabdyto užduoties įrašas saugomas 10 minučių (daugiausia paskutines 32), kiekvienam klientui: kitas skriptas arba `chloros-mcp` nuskaito tą pačią būseną ir galutines statistikos duomenis. Užduotis `chloros-cli` pradėta (`lattice continuous`, `multi-stream`, `align-stream`) yra sustabdoma užkulisio modulio, kai CLI 30 sekundžių neatsilieka — užbaigta, jos kompiuteris miego režime — su `stop_reason`, `client_gone` ir `stopped_by` užkulisio modulyje; statistikos skaitymas iš čia nepadeda išlaikyti jo veikimo. Iš SDK paleista užduotis veikia tol, kol nėra sustabdyta. Kiekvienas paleidimas siunčia savo „`timeout`“ kaip užkulisio galutinį terminą („`client_deadline_s`“): kamera, kuri atidaroma po to (kai viena vis dar įkeliama), yra grąžinama ir uždaroma, o užduotis nepradedama, ir šis iškvietimas sukelia „`ChlorosConnectError`“ (`CLIENT_DEADLINE_PASSED`). Kamera, kurią užima užduotis, atmetama kiekvienam kitam atidarymui (`connect_camera()`, kito paleidimo, trumpalaikio įrašymo) su HTTP 409 kodu `CAMERA_HELD`, nurodant užduotį ir ją pradėjusį asmenį. |
| `stop(timeout=30.0)` | Sustabdyti ją ir uždaryti jos kameras: `final_stats`, `output_dir` ir `error`, kai sustabdymas nevyko sklandžiai. Antrasis iškvietimas grąžina tą patį atsakymą, taip pat ir sustabdymas iš kito kliento: siunčiamas, kol pirmasis vis dar rašo savo eilę, laukia jo (iki 25 s; praėjus šiam laikui, iššaukiamas išimtis, o galutinės statistikos pateikiamos `stats()`). |
| `wait(timeout=None, poll_s=0.5)` | Laukti, kol jis baigsis savaime (pasiekus `count`, užpildžius diską, įvykus klaidai), tada `stop()` jį, kad surinktų galutinius skaičius. `timeout` sukelia `TimeoutError`, kai jis vis dar veikia; užduotis be `count` baigiasi tik tada, kai ji sustabdoma. |
| `job_id`, `kind`, `output_dir`, `start_reply`, `stopped` | Jo identifikatorius ir tipas (`continuous`, `multi-stream`, `align-stream`), kur jis rašo, pradinio maršruto atsakymą ir ar `stop()` buvo paleistas. |

Naudokite jį kaip konteksto tvarkyklę, kad jis būtų sustabdytas išeinant iš apimties.

---

## Projekto aplankas ir receptai

**Projekto aplankas** yra aplankas, kuriame kuriamos naujos projektai, bendrai naudojamas Chloros programėlės, CLI ir SDK (modulis `chloros_sdk.system_utils`; `chloros-cli project-folder`):

```python
print(chloros_sdk.project_folder())                # the configured folder, else the default
chloros_sdk.set_project_folder("D:/Flights")       # created if missing
chloros_sdk.reset_project_folder()                 # -> {"path": <default>, "was_set": True}
```

Jei sukonfigūruotas aplankas nebeegzistuoja, kaip ir programėlė, grįžtama prie numatytojo (`Documents/MAPIR/Chloros Projects`). Santykinis kelias, nurodytas `set_project_folder()`, imamas iš jūsų darbo katalogo, kai užkulisinė sistema veikia šiame kompiuteryje.

**Receptas** – tai YAML / JSON veiksmų sąrašas, kurį `chloros-cli project run PATH RECIPE` vykdo pagal išsaugotą projektą. `run_project_recipe()` jį vykdo naudodamas CLIsavo vykdytoju (įdiegtu su SDK), todėl vienas receptas veikia vienodai abiem atvejais:

```python
r = chloros_sdk.run_project_recipe("D:/campaign/morning.yaml", "D:/Projects/Field_A")
print(r["ok"], r["actions"])
print(r["log"])                                    # what the CLI would have printed

chloros_sdk.run_project_recipe({"actions": [{"apply": {"name": "FrontLeft", "settings": {"exposure_us": 5000}}},
                                            {"wait": "2s"},
                                            {"capture": {"name": "FrontLeft", "output": "pose_a/", "format": "tiff"}}]},
                               "D:/Projects/Field_A")
```

```python
run_project_recipe(recipe, project_path, *, dry_run=False, echo=False,
                   backend_url="http://127.0.0.1:5000") -> dict
```

- **`recipe`** yra `.yaml` / `.yml` / `.json` failas arba pati recepto versija: žodynas su `actions` (`apply`, `capture`, `burst`, `stream`, `sensor`, `wait`) bei pasirinktinai `overrides` ir `stop_on_error` (numatyta reikšmė „true“: vykdymas sustabdomas po pirmojo nesėkmingo veiksmo). Veiksmai apibrėžti CLI-nuorodoje [Recipe DSL](cli-reference.md#recipe-dsl); santykinis `output` nustatomas pagal jūsų skripto darbo katalogą.
- **Grąžina** `ok` (visi veiksmai sėkmingi), `actions` (kiek), `log` (vykdytojo išvestis; `echo=True` taip pat ją išspausdina vykdymo metu) ir `validation`. Jei recepto failo nėra arba jo nepavyksta išanalizuoti, iškeliama `ChlorosConnectError`.
- **Patikrinama prieš paleidimą.**`validation` yra `{"ok", "errors", "warnings", "steps"}`: kiekvienas veiksmas turi turėti būtent vieną žinomą veiksmažodį kaip raktą (`{"capture": {"name": ...}}`, o ne `{"action": "capture"}` ar `{"type": "capture"}`), nurodantis projekte esantį tinkamo tipo fotoaparatą, masyvą ar jutiklį. Receptas su klaida**ne** vykdo jokio veiksmo (`ok` false, klaidos `log`), nesvarbu, ką nurodo `stop_on_error`. Nenaudojami raktiniai žodžiai, ir kiekvienas receptas valdo masyvą ar šviesos jutiklį, kuris šiuo metu nėra prijungtas, yra `warnings`. Receptas neprijungia įrenginių: prijunkite projekto pirmąjį (`ChlorosProject.connect_all()`, `chloros-cli project connect`, arba MCP įrankį `connect_project_devices`). `dry_run=True` nieko nevykdo ir grąžina `{"recipe": ..., "validation": ...}`.
- Vienu metu procese vykdomas tik vienas receptas. YAML receptui reikalingas PyYAML.

---

## Projekto automatizavimas — `ChlorosProject`

Išsaugotas Chloros projektas yra aplankas, kuriame yra `cameras.json` + `sensors.json` + `project.json`. `open_project(path, relink=None)` įkelia manifestą (`relink`: aplankas, kuriame dabar yra perkelto projekto trūkstami šaltinio failai, naudojamas, kai jį įkelia užkulisinė sistema — `process()` — žr. **Perkeltas projektas** skyriuje „`ChlorosLocal`“), o „`connect_all`“ įjungia visas išsaugotas kameras, matricas ir šviesos jutiklius su jų išsaugotais nustatymais — tokia pati aparatinės įrangos būsena, kokią sukurtų grafinė vartotojo sąsaja. Šviesos jutikliai prisijungia per užkulisio [DAQ jutiklių rezervą](#daq-sensor-sessions), todėl jiems reikalingas veikiantis užkulisinis modulis (žr. [`SensorHandle`](#sensorhandle)).

### Minimalus pavyzdys

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Light sensors are addressable by name OR slot_id
reading = proj.sensors["Sky"].read()   # (spectrum, is_saturated, ...) or None

# One frame from every camera and array, one spectrum from every sensor
proj.capture_all("./out")

proj.disconnect_all()
```

Arba kaip konteksto tvarkyklė:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` metodai

| Metodas | Aprašymas |
| --- | --- |
| `connect_all(*, cameras=True, arrays=True, sensors=True, verbose=False, align=None, packed=None)` | Aptinka ir prijungia kiekvieną išsaugotą kamerą, matricos ir šviesos jutiklį. Grąžina prijungimo ataskaitą pagal klases (`{"cameras": {...}, "arrays": {...}, "sensors": {...}}`, kiekviena su `connected`, `missing` ir `errors`; `arrays` taip pat apima `alignment`). Jei yra užkulisinė programa, klausanti `127.0.0.1:5000`, išsaugotos kameros ir masyvai randami ir prijungiami per ją. Jei tokios nėra, šis procesas pats juos suranda ir valdo per `lattice_sdk`, kuriam reikalinga „Arena“ SDK-vykdymo aplinka ir „OpenCV“ (`cv2`) — SDK ratas nei vieno, nei kito neįdiegia; kai to padaryti nepavyksta, kiekviena išsaugota kamera ir matrica yra išvardyta po `errors` nurodant priežastį, o `missing` lieka tuščias. Veikiantis, bet SDK atmetantis užpakalinis modulis (atsijungta: HTTP 401; planas žemiau „Copper“: 403) nereiškia, kad „nėra užpakalinio modulio“: kiekviena išsaugota kamera, matrica ir šviesos jutiklis yra išvardyti po `errors` su užpakalinio modulio priežastimi, ir šiame procese nieko nėra atidaryta. Kiekvienas iškvietimas vėl patikrina užkulisį, todėl „`connect_all()`“ po prisijungimo arba paleidus Chloros prisijungia prie to paties projekto objekto. Išsaugotas masyvas, jau veikiantis „backend“ su tomis pačiomis kameromis (GUI arba `chloros-cli project connect`), prijungiamas toks, koks yra, netaikant iš naujo jo išsaugotų nustatymų, o „`disconnect_all()`“ palieka jį veikiantį. Šviesos jutikliai visada eina per užkurtį (žr. [`SensorHandle`](#sensorhandle)). Jis niekada nesukuria užkurtio. |
| `disconnect_all()` | Viską išmontuoti. Pirmiausia atleidžiami visi įrenginiai; tada, jei užkulisiai nepatvirtino kameros, masyvo ar šviesos jutiklio atleidimo (išmontavimo laiko limitas pasibaigė arba buvo atmestas), kiekvienam iš jų suteikiamas pavadinimas `ChlorosConnectError`. Įrenginys, kurio jau nebėra, arba jei neveikia joks užpakalinis procesas, yra tylus. Masyvas arba šviesos jutiklis, kurį buvo atidaręs kitas klientas, lieka veikti. Projekto `with` išėjimas niekada nesukelia išimties. |
| `capture_all(output_dir=".")` | Po vieną kadrą iš kiekvienos prijungtos kameros + masyvo, ir vienas spektro kadras (`SensorHandle.read()`) iš kiekvieno prijungto šviesos jutiklio, paimami paeiliui (pirmiausia kameros, tada masyvai, tada jutikliai), o ne vienu metu. |
| `stream(camera, overlays=False, fps=10.0)` | Generatorius, generuojantis BGR `numpy` kadrus iš nurodytos kameros (arba matricos). `overlays=False` skaito kadrus su identifikatoriumi`grab()` `debayered` lygyje: per užkurtį, kai projektas prijungtas per jį, kitais atvejais – tiesioginė `lattice_sdk` kadrų paėmimo kilpa (masyvai generuoja `{serial: frame}` žodynus). `overlays=True` nukreipia per `ChlorosLocal.camera_stream()` → užpakalinės dalies `/api/camera/<serial>/stream-annotated` MJPEG srautą, o kameros išsaugotas `ui.overlay` blokas perduodamas kaip užklausos parametrai. Reikalingas užpakalinės dalies režimas, OpenCV (`cv2`) ir **autonominė kamera**: tiesioginio režimo kamera sukelia `RuntimeError` (backend negali paimti kameros, kurią valdo šis procesas), o masyvas sukelia `NotImplementedError` (sudaro kompozicinius perdengimus pagal kamerą — transliuoja elementą pagal pavadinimą). Vienkartinis ekvivalentas: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Atlikti suderinimą su kiekvienu šiuo metu prijungtu masyvu. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Vykdyti kalibravimo / indeksavimo procesą projekto vaizduose (apima `ChlorosLocal.process`; šie keturi yra **vieninteliai** priimtini argumentai — `indices=` ir pan. sukelia išimtį `TypeError`; nustato indeksus per `ChlorosLocal.configure()`). Lėtai sukuria `ChlorosLocal()`, kuris automatiškai paleidžia užkurtį. |

Atributai:
- `proj.cameras` — `Dict[str, CameraHandle]`, indeksuojamas pagal pavadinimą IR serijos numerį.
- `proj.arrays` — `Dict[str, ArrayHandle]`, indeksuotas pagal pavadinimą IR array_id.
- `proj.sensors` — `Dict[str, SensorHandle]`, indeksuotas pagal pavadinimą IR slot_id.
- `proj.config` — `project.json["config"]` žodynas.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**Apdorojimo lygiai.** `capture()`, `grab()` ir `frame_stream()` visi naudoja tą patį `processing`
žymeklį, o grandinė yra kaupiamoji — kiekvienas lygis vykdo viską, kas yra virš jo:

| Lygis | Išvestis | Pastabos |
| --- | --- | --- |
| `raw` | 1 kanalas, „Bayer“, jutiklio natūralus | Be demozėjimo. Šiame lygyje perdangos nėra prieinamos. |
| `debayered` | 3 kanalų spalvotas (**numatyta** reikšmė `CameraHandle` ir visiems `grab()` / `frame_stream()`; `ArrayHandle.capture()`, `capture_smart()` ir `stream()` numatytasis nustatymas yra `raw`): BGR atmintyje (`grab()` / `frame_stream()`), išsaugota kaip RGB naudojant `capture()` | „Backend“ režimu, kaip įrašas: kalibravimo paketo tamsos poslinkis (DSNU) nuo neapdorotų duomenų, tada „OpenCV“ kraštų atpažinimo demozė – be linijiškumo LUT, plokščio lauko ar atskyrimo. Vienintelis lygis, kuris veikia be užpakalinio režimo, kur naudojama paprasta kameros bibliotekos bilinearinė demozė (be tamsos poslinkio). Išsaugotas TIFF failas yra fotometrinis RGB, todėl reikia apversti atmintyje esančio kadro kanalus (`frame[..., ::-1]`), kad būtų galima palyginti su failu. |
| `radiance` | float32, W/m²/sr/nm, **viena plokštuma kiekvienam juostos kanalui** | Pilna radiometrinė grandinė: demozėjimas + 3×3 atskyrimas (multispektrinis) + DSNU + plokščio lauko korekcija + NIST skalė, išskaičiuojant ekspoziciją × stiprinimą, kad reikšmės būtų absoliučios. |
| `reflectance` | uint16, 32768 = 1,0 (arba uint8, 255 = 1,0 — žr. `bit_depth`), **viena plokštuma kiekvienai juostai** | Šviesos srautas, padalintas iš žemyn nukreipto spinduliavimo intensyvumo (ρ = π·L/E). Reikalingas DLS/DAQ rodmuo — žr. pastabą žemiau. |
| `display` | 8 bitų, panašus į sRGB | GUI atitikmuo: CCM + baltos spalvos balansas + gama per kameros aktyvų spalvų profilį. |

Tik tiesioginiame (be užkulisio) režime prieinamas tik `debayered`: `grab()` kitame lygyje ignoruoja
`NotImplementedError`, `capture()` ignoruoja `processing`, o `frame_stream()` padidina
`RuntimeError` kiekviename lygyje. `reflectance` reikia tinkamo žemyn nukreipto rodmens — kadro pabaigos taškas automatiškai įtraukia
sujungtą DAQ į kameros DLS lizdą, tačiau jei DAQ nėra susietas, grandinė atsisako
atspindžio išėjimo ir sąžiningai pažymi pažeminimą grąžinamuose metaduomenyse, o ne tyliai
grąžinti prastesnį produktą.

> **Atspindžio DN skalė — jos nekoduokite kietai.** „LATTICE“ atspindžio koeficientas naudoja `32768` = ρ 1,0 ir pažymi
> XMP `Chloros:PixelScale=32768`; Survey3 atspindžio koeficientas naudoja `65535` = ρ 1,0 ir neturi
> `Chloros:*` žymių. Perskaitykite žymę ir padalinkite iš jos. Ji apibrėžta „uint16“ srityje, todėl išlieka
> `32768` kiekvienam formatui, kuriame keičiamas mastelis (16 bitų TIFF, 8 bitų TIFF, 8-bitų PNG/JPG, 32-bitų procentais)
> — pirmiausia normalizuokite saugomą duomenų tipą atgal į „uint16“ (×257 iš 8 bitų, ×65535 iš „float“). Vienintelė
> išimtis yra failas iš versijos, senesnės nei 1.3.0: tuomet 8 bitų TIFF buvo *apkarpytas*, o ne perskalintas, todėl
> jo negalima apibūdinti jokiu masteliu, be to, jame nėra `PixelScale` ar MicaSense tuplės. Trūkstamą žymą
> „LATTICE“ atspindžio faile traktuokite kaip „nėra galiojančio mastelio“, o ne kaip numatytąjį.

**Kalibravimo žymės: tik `raw`.** Įrašo `raw` failas turi kameros kalibravimo paketą: duomenų bloką „`CalibrationJSON`“ ir su „MicaSense“ suderinamas žymes (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), apibūdinančias jutiklio neapdorotus duomenis. Kiekvienas kito lygio failas (`debayered`, `display`, `radiance`, `reflectance`, `index/` atvaizdavimas) bei kiekvienas nepertraukiamo įrašymo / kelių kamerų srauto kadras turi kameros identifikatorių (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...), taip pat pačio kadro įrašą – `XMP-mapir:FrameJSON` (ekspozicija, stiprinimas, temperatūra, pikselių formatas, užfiksavimo laikas, DAQ atitikimas), nes kiekvienas „`process()`“ produktas turi savo identifikatorių. Apdorojimo metu tokio failo kalibravimas randamas pagal jo serijinį numerį ir „`CalBundleSha256`“ „Chloros“ kalibravimo talpykloje, o jei šio kompiuterio talpykloje jo nėra, paketas atsisiunčiamas vieną kartą.

#### Failai, sukurti iš kelių kamerų

Failas, sukurtas iš daugiau nei vienos kameros juostų — daugiajuostis sluoksnis (`capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index(save_multiband=True)`, `Registration.save_multiband()`), iš kelių kamerų sudarytas tiesioginis indeksas (`index.tif`, `index_LUT.png`), masyvo užfiksavimo vaizdas `composite/` — pavadinimuose nurodomos visos kameros, iš kurių jis sudarytas:

- **Kiekviename-kameros sąrašai, pagal juostų tvarką** (kompozicija: foninis sluoksnis, tada priešplanis; indeksinis sluoksnis, kameros, iš kurių gauti juostų duomenys): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (kalibravimo rinkinys, kurį praėjo kiekvienos kameros juostos, kitaip — `capture_aligned_multiband()` / `start_aligned_stream()` rinkinys, kurio juostos nepraėjo nė vieno — kameros rinkinys, kurį Chloros įrašė į talpyklą, tas, kurio pavadinimas sutampa su jos įrašų pavadinimu), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — lygiagrečios sąrašų eilutės, kurių kiekvienos *n* elementas apibūdina tą pačią kamerą; `-`, kai kamera neturi reikšmės (nėra kalibravimo paketo, nežinomas objektyvas). Kiekviena kamera yra **vienas** elementas: jei jos pakete tam pačiam laukui yra kelios reikšmės — paketo halogeninių lempų sertifikatai — jie sujungiami naudojant `;` (`F-1479-1;F-1480-1;F-1481-1`), niekada nenaudojant kablelio, todėl sąraše pateikiamas po vieną elementą kiekvienai kamerai, nepriklausomai nuo to, kaip jis atspausdinamas.
- **EXIF `SerialNumber`** saugo tuos pačius serijinius numerius, sujungtus kableliais (`214000535,214000551`).
- Jame **nėra** atskirų kamerų identifikatorių (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): tie identifikatoriai nurodo vieną kamerą ir jos paketą, o apdorojimo metu pagal juos nustatoma failo kalibracija. Iš kelių kamerų juostų apskaičiuotas indeksas (`index.tif`, `index_LUT.png`) turi EXIF `Model` `LATTICE` (įrangos komplektą), o ne vienos kameros modelį `LATT-…`.**Vienos** kameros juostų failas — kiekvienos juostos TIFF failas, `capture_aligned_multiband(per_camera=True)` kadras, vienos kameros stekas arba tiesioginis indeksas — turi tos kameros identifikacines žymes, kaip ir kiekvienas kitas įrašymo failas. `capture_aligned_multiband()` stekas, kiekvienos juostos ir kiekvienos kameros failai, taip pat kiekvienas `start_aligned_stream()` stekas ir jo kiekvienos juostos failai turi savo grupės įrašymo laiką (etaloninės kameros ekspozicijos laiką, UTC), o vienos kameros `per_camera` kadras yra viena plokštuma, kaip ir `bit_depth` rinkinys.**Kameros orientacija (apversti / pasukti).** Kadrų iš `grab()` / `frame_stream()` orientacija visada atitinka
kameros natūralią orientaciją kiekviename lygmenyje. Išsaugoti kadrai atitinka kameros orientacijos
nustatymus (GUI parinktys „Mirror H“ / „Mirror V“ / pasukimas):

- **`raw` ir `debayered` failai atitinka būtent tai, ką nuskaitė jutiklis**, nepriklausomai nuo nustatymų — pasuktas ar apverstas „Bayer“
  mozaikos modelis būtų kitoks spalvų filtrų modelis, o kalibravimo žemėlapiai yra
  pateikiami pačio jutiklio tinklelyje.
- **`display`, `radiance`, `reflectance` ir indeksiniai failai yra orientuoti** — pasukti vieną kartą, atlikus visus
  kalibravimo skaičiavimų. Jų ribojimo kaukės yra tame pačiame tinklelyje.
- **Kiekvienas užfiksuotas failas įrašo nustatymą** XMP formatu: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, pagal laikrodžio rodyklę, po
  apversimų) bei `ChlorosOrientationApplied` — `false` neapdorotuose ir be „bayeringo“ vaizduose, o `true` – orientuotuose
  produktuose. `ChlorosProcessing` išvardija operacijas, pritaikytas to failo pikseliams (`none`, kai
  failas yra natūralus). Tai apima kiekvieną nepertraukiamo įrašymo kadrą (pati kameros nustatymas,
  nepritaikytas) ir daugiakamerio srauto kadrą (identitetas: jo sudedamosios dalys yra natūralios, kaip ir
  masyvo įrašymo atveju).
- **Apdorojant projektą, įrašyta orientacija** taikoma produktams, kuriuos jis sukuria iš neapdorotų
  (pirmiausia kalibravimas pagal jutiklio koordinates, po to masyvo suderinimo iškraipymo koregavimas, galiausiai orientacija),
  kad jie atitiktų tiesioginio įrašymo produktus — įskaitant nekalibruotus jutiklio atsako / vinjetės koreguotus
  atsarginius produktus; jo debayeringo produktas išlieka natūralus.
- **Chloros vaizdų peržiūros programa rodo neapdorotą arba „debayered“ failą, pasuktą pagal jo įrašą** — tik peržiūrai,
  failas nėra keičiamas; failas be įrašo rodomas toks, koks buvo išsaugotas.
- **Įrašai be įrašo apdorojami kaip natūralūs.** Iki versijos 1.3.0 vienos kameros įrašas
  pats apdorodavo savo RAW ir „debayered“ failus; Chloros negali atskirti tokio RAW failo nuo natūralaus ir
  nesprendžia.
- **Matricos įrašai pagal numatytuosius nustatymus išlaiko kiekvieną failą natūralų** ir įrašo identifikatorių;
  `ArraySession.capture(orient=True)` apdoroja jų rezultatus (žr. masyvo įrašymo valdymo elementus).

#### Išvestis pagal juostas ir mišrios matricos

`radiance` ir `reflectance` lygiuose kamera grąžina **vieną plokštumą kiekvienai tos
kameros juostai** — tris M3C atveju (jos trys nesumaišytos juostos, BGR kanalų tvarka), vieną – M3M atveju (jos
vienintelis siaurajuostis filtras). Mono kameros pakartotiniai plokštumų vaizdai yra sujungiami, todėl **mišrus
M3C/M3M matrica suteikia skirtingas matricos formas kiekvienam serijiniam numeriui**. Perskaitykite raktą `bands` iš kadro
metaduomenų, kad sužinotumėte, kas yra kiekviena plokštuma; nedarykite išvadų remdamiesi kanalų skaičiumi:

```python
for serial, (arr, hdr) in arr_handle.grab(processing="reflectance",
                                          with_metadata=True).items():
    print(serial, arr.shape, hdr["bands"])   # ('…', (1536, 2048, 3), ['nir','green','red'])
                                             # ('…', (1536, 2048, 1), ['F685'])
    rho = arr.astype("float32") / hdr["pixel_scale"]
```

Prieš naudodami vertes, visada patikrinkite `hdr["processing_level"] == "reflectance"`. Sumažinimas
(nėra DAQ rodmenų, nėra kalibravimo paketo, nežinomas juostų išdėstymas) pranešamas `processing_level` ir
`radiometric_refused`, o ne padidinimas — gautas masyvas vis dar yra galiojantis rėmelis, tiesiog
tai nėra tas produktas, kurio prašėte. Išsaugotas įrašas, kurio lygis buvo sumažintas, savo XMP turi tą patį kodą
kaip ir `Chloros:RadiometricRefused` (pvz., `no-band-layout`, `no-dls-snapshot`).

#### Vienas trigeris vienam skaitymui — `group_id`

**Masyvo nariai aptarnaujami iš masyvo sinchronizuotos kadrų grupės, o ne iš kiekvienos kameros atskiro
įrašymo buferio.** Aparatūriškai sinchronizuoto masyvo kameros eksponuoja kartu, tačiau N skaitymų iš kiekvienos kameros
nėra *atliekami* kartu — skaitant kiekvienos kameros naujausią buferyje esantį kadrą, peržengiamos trigerio
ribas, o vienas praleistas kadras desinchronizuoja rinkinį niekam to nepastebint.

Tiekimas iš grupės yra būtinas, tačiau savaime nepakankamas: kiekvienas užklausimas vis tiek
fiksuoja *naujausią* grupę savo momentu, o įrašymo ciklas gali paskelbti naujus kadrus tarp jų.
Išmatuota realiu 4 kamerų masyvu, **12 iš 20 lygiagrečių skaitymų apėmė dvi grupes** — visada
būtent gretimas. Taigi skaitymas yra **pririštas**:

1. Kiekvienas kadras turi `group_id` – sistemos monotoninį skelbimo skaitiklį.
2. „`grab()`“ nuskaito pagrindinę kamerą (arba, jei pagrindinė kamera negali aptarnauti užklausos, kitą kamerą, kuri gali),
   tada priskiria likusias kameras prie grupės identifikatoriaus, kurį gavo („`?group_id=N`“ HTTP
   galiniame taške).
3. Užkulisinė sistema saugo naujausias grupes **mažiausiai pusę sekundės**, todėl giminingas elementas, kurį jau
   aplenkė vėlesni paskelbimai, vis dar aptarnauja tą trigerį — pakankamai ilgai, kad būtų galima apskaičiuoti kiekvienos kameros
   atspindžio eksporto duomenų perdavimui ir atgal, net ir 9 kamerų pilno jutiklio masyve. Priskyrimas, kurio užkulisiai jau
   nebeturi, atmetamas su kodais `409` ir `group_aged_out: true`, o ne tyliai atsakoma
   kitu trigeriu — `grab()` iš naujo nuskaito rinkinį.

Pakartotinai išmatuota naudojant prisegimą: **20 iš 20 skaitymų grąžino vieną trigerį.** „`grab()`“ tai atlieka už jus;
„`group_id=`“ reikalingas tik tada, kai tiesiogiai valdote HTTP galinį tašką.

- Kamera, kurios negalima aptarnauti priskirtoje grupėje — jos ten nėra arba ji atmeta užklausą
  (`band`, kurio ji neturi, arba lygio, kurio ji negali sukurti) — yra **praleidžiama, o ne užpildoma**
  iš kito trigerio, o kitos kameros vis tiek grąžinamos.
- Žodynas, kurį grąžina `grab()`, yra `GrabResult` (paprasta „`dict`“ paklasė;
  „`from chloros_sdk import GrabResult`“): `.errors` susieja kiekvieną
  praleistą serijinį numerį su išimtimi, kurią sukėlė jo skaitymas, o `.group_id` yra bendra grupė. Tuščias
  `.errors` reiškia, kad visos kameros sudarė šią grupę.
- Atskiroji kamera neturi grupės, praneša `group_id: None` ir **atmeta** `group_id=`
  žymę, o ne ignoruoja ją.

```python
frames = arr_handle.grab(processing="reflectance")
for serial, err in frames.errors.items():
    print(f"SN:{serial} omitted: {err}")       # e.g. status=400 ... band 'F685' not in [...]

frames = arr_handle.grab(processing="reflectance", with_metadata=True)
ids = {hdr["group_id"] for _a, hdr in frames.values()}
assert len(ids) == 1, f"straddled triggers: {ids}"     # belt and braces
```

Tas pats skaitiklis skelbiamas **kiekviename atskiroje kameros MJPEG sraute**, priklausančiame masyvo nariui —
peržiūros (`/api/camera/stream/<serial>`), anotuotos peržiūros (`/stream-annotated`) ir
atspindžio srautas (`/stream-reflectance`) — kaip `X-Chloros-Group-Id` kiekvienoje dalyje, be
`X-Chloros-Frame-Id` ir `X-Chloros-Timestamp-Ns`. Srautai yra ne momentiniai, o
nuolatiniai, todėl jie suderinami surenkant dalis ir grupuojant pagal tą antraštę — nereikia
nustatyti jokio išvadų. Atskira kamera neturi grupės. Žr.
[MJPEG srautai pagal kamerą](#per-camera-mjpeg-streams-no-rtsp).

**`frame_stream()` niekada nepatyli.** Žymė, kurios patikrinimas nepavyksta dėl priežasties, kuri gali išsispręsti savaime,
yra praleidžiama — kameros srautas jos atžvilgiu nieko negrąžina, o masyvo srautas grąžina kameras, kurios
atsakė (galbūt nė viena). Šios priežastys yra: dar nėra kadro; kameros duomenų paėmimo sriegis sustabdytas
sekundę ar dvi (`404 not streaming`, į kurį užpakalinė sistema atsako, kai kita kamera prisijungia
arba atsijungia, arba nustatymų pakeitimas iš naujo subalansuoja grupę); užpakalinės dalies perkėlimas arba `5xx`; suderinimas
vis dar vyksta (`409 … retry shortly`); ir atspindžio atmetimas, nes DAQ dar neturi rodmenų
(`no-dls-snapshot`, pasenęs arba tuščias rodmuo). Atsisakymas kitame žingsnyje tik pasikartotų —
nežinomas `band`, `bit_depth=8` dėl spinduliavimo, kalibravimo paketas be spinduliavimo skalės, atspindžio
atsisakymas, kai **nėra jokio DAQ** (nė vienas nepriskirtas kamerai ar jos matricai, nė vienas neprijungtas prie
galinio modulio: atmetimo `dls_source` yra `"none"`), bet koks kitas `4xx` —
sukelia `chloros_sdk.FrameFetchError` (`RuntimeError`, pernešantis `.status` ir `.body`) iš
kart. Taip pat ir **60 sekundžių** praleistų takelių be jokio kadro tarp jų, nesvarbu, koks yra `fps` — pakankamai
ilgas, kad praeitų suderinimo stabilizavimas ir užpakalinės dalies paleidimas iš naujo, ir pakankamai trumpas, kad kamera, kuri
iš tikrųjų nebeveikia, nutrauktų srautą. Ši atsisakymo klaida taip pat yra `FrameFetchError`, tiek kamerai, tiek
matricai, o paskutinio skaitymo išimtis nurodyta kaip `.errors` (o kameros atveju – kaip `__cause__`).
Masyvo atveju „atmesta“ reiškia, kad visos kameros atmetė; išimtis `.errors` nurodo kiekvienos kameros
priežastį.

**Masyvas `frame_stream()` kiekvieną trigerį pateikia vieną kartą** (taip pat ir `ArrayHandle` bei `ArraySession`).
Tikas, kurio naujausias rinkinys yra jau pateiktas trigeris, laukia kito trigerio, o ne
vėl pateikia tą patį rinkinį, todėl srautas veikia lėčiau nei `fps` arba paties masyvo kadrų, o iš eilės einantys rinkiniai niekada nesidalija vienu `group_id`. Masyvas, kuris nustoja skelbti, sukelia
`FrameFetchError` („jokio naujo trigerio 60 s“) vietoj to, kad kartotų savo paskutinį rinkinį. Be to,
srautas, kuriam buvo paprašyta daugiau, nei masyvas paskelbė — arba kuris prasidėjo iškart po prisijungimo — pateikdavo
pakartojimus: viename išmatuotame bandyme 20 rinkinių turėjo 11 skirtingų grupės identifikatorių.

```python
from chloros_sdk import FrameFetchError

try:
    for arr in cam.frame_stream(processing="radiance", bit_depth=8):
        my_analysis(arr)
except FrameFetchError as exc:
    print(exc.status, exc.body)        # the HTTP status and the backend's reason
```

#### Vieno dažnio juostos eksportavimas — `band`

`grab()` / `frame_stream()` imkite `band="<name>"`, kad eksportuotumėte tik vieną kameros juostą kaip
vienakanalį `(H, W, 1)` masyvą — tokios pačios formos, kokią sukuria monokromatinė kamera, todėl vienos juostos išvestis
apdorojama vienodai, nesvarbu, ar ji gauta iš M3M, ar atskyrus M3C. FRGN raudonoji juosta:

```python
red = cam.grab(processing="reflectance", band="red")        # (1536, 2048, 1)
```

Priimtini rašybos variantai yra pilnas išdėstymo pavadinimas (`Red_660`, `NIR_850`, `Green_550`) arba jo trumpas
pavadinimas (`red`, `nir`, `green`), neatsižvelgiant į didžiųjų ir mažųjų raidžių skirtumą. Nežinomas pavadinimas atmetamas, pateikiant galimus
pavadinimų sąrašą.

> **Pasirinkimas atliekamas pagal pavadinimą, o ne pagal kanalo numerį — tai padaryta sąmoningai.** „1 kanalas“ yra `Green_550` pagal
> masyvo BGR kanalų tvarką, bet *raudonas* pagal filtro kodo(`RGN`), ir šie du
> nesutampa. Pasirinkus neteisingą, gaunamas visiškai įtikinamas kadrą iš neteisingo dažnių juostos, kurio
> niekas tolesnėje grandinėje negali aptikti. Dėl šios priežasties nėra skaitmeninio indekso.

#### Srautų suderinimas — `align`

`align=True` transformuoja kadrą į masyvo bendrą atskaitos kadrą. Kiekviena kamera transformuojama
pagal **tą pačią** profilio skiriamąją gebą ir apkarpymo ROI, todėl atskirai atidaryti kiekvienos kameros srautai
būna **tarpusavyje suderinti** — jų nereikia prašyti kartu:

```python
frames = arr_handle.grab(processing="reflectance", align=True)   # {serial: array}
# or one MJPEG stream per camera, each co-registered on its own:
#   GET /api/camera/<serial>/stream-reflectance?align=1
```

Reikalavimai ir veikimas:

- Kamera turi būti **aparatūriškai sinchronizuotame** masyve (M8 sinchronizavimo kabelis), turinčiame suderinimo
  profilį — bendras suderinimas remiasi prielaida, kad nariai eksponuojami vienu metu, ko programinis ar
  laisvai veikiantis masyvas to neužtikrina. Priešingu atveju bus atmesta su kodu `409`.
- **Atmetama, niekada tyliai nesuderinama.** Įrašymo kelias gali išsaugoti nesuderintus duomenis su įspėjimu,
  nes failas išlaiko savo kilmės žymes ir gali būti perregistruotas vėliau; tiesioginė transliacija neturi
  tokios antros galimybės, todėl trūkstamas profilis yra klaida, o ne atsarginis variantas.
- **Prašymas suderinti pradeda suderinimo procesą.** Prašymas `align` masyve be profilio paleidžia
  kalibravimą ir grąžina `409` su tuo, ko laukia (pvz., *„laukiama, kol ekspozicija
  stabilizuosis ties 214000551 (1/3)“*) — pabandykite dar kartą, ir paprastai per keliasdešimt sekundžių pavyks.
  Tai svarbu dirbant be monitoriaus: automatinis suderinimas prisijungimo metu nutraukiamas po 30 s, o kiekvienas bandymas
  per tą laikotarpį praleidžiamas, kol „smart-AE“ vis dar stabilizuojasi, todėl masyvas, kurio ekspozicija
  konverguoja lėtai, anksčiau likdavo be profilio visai sesijai ir be galimybės jo gauti
  neatidarius darbalaukio sujungto vaizdo. `alignment_status` taip pat praneša
  `ae_align_holdouts`, nurodydamas, kurios kameros vis dar stabilizuojasi, ir jų pažangą.
- Kiekvienam vieno masyvo srautui suteikite **tą patį `max_h`**, kitaip jų apkarpymai atsidurs skirtinguose pikselių
  tinkleliuose.
- Suderinimas taikomas *prieš* bitų gylio kodavimą, todėl deformacija interpoliuoja visiškai tikslias
  vertes.

#### `bit_depth` — tikslumo mainai į pralaidumą

`grab()` ir `frame_stream()` priima `bit_depth=16` (numatyta reikšmė) arba `bit_depth=8`:

| `bit_depth` | Atspindžio kodavimas | Kaina |
| --- | --- | --- |
| `16` | uint16, 32768 = ρ 1,0, rezervas iki ρ 2,0 | Pilnas tikslumas. |
| `8` | uint8, apimantis `0..refl_max` (numatyta ρ 1,0) | Pusė baitų; ~0,004 ρ kvantizacija esant numatytai viršutinei ribai. Vertės, viršijančios viršutinę ribą, apkarpomos — žr. toliau. |

##### 8 bitų viršutinė riba — `refl_max`

`refl_max` nustato ρ, kurį apima 8 bitų diapazonas, todėl `pixel_scale = 255 / refl_max` ir ρ vis dar yra
`pixel / pixel_scale`. `grab()` ir `frame_stream()` jį traktuoja kaip raktinį žodį tiek kameros, tiek
masyvo identifikatoriuose (`?refl_max=` HTTP galiniame taške). Padidinkite jį, kad gautumėte daugiau erdvės veidrodiniams
šviesos atspindžiams, proporcingai didesniais žingsniais:

```python
frame = cam.grab(processing="reflectance", bit_depth=8, refl_max=1.5)   # 0..1.5 in 0..255
```

| `refl_max` | `pixel_scale` | žingsnis |
| --- | --- | --- |
| `1.0` (numatyta) | 255 | 0,0039 ρ |
| `1.5` | 170 | 0,0059 ρ |
| `2.0` | 127,5 | 0,0078 ρ |

> **Automatinė ekspozicija negali ištaisyti 8 bitų perkrovos, o bandymas tai padaryti tik pablogina duomenis.**
> ρ = π·L/E, išskaičiavus ekspoziciją ir stiprinimą, taigi ρ yra scenos, o ne
> fotoaparato nustatymų savybė — matuojant tiesioginiame matricoje, ekspozicijos sumažinimas perpus pakeitė vidutinę ρ vertę 1,3 %. O
> ekspoziciją, kad „pritaikytumėte“ diapazoną, pikseliai patenka į **jutiklio** prisotinimą, o tai
> sumažina L vertę ir iškreipia ρ į **mažąją** pusę be jokios nurodytos paklaidos (išmatuota: maks. ρ 1,203 → 0,985 esant
> 2× ekspozicijai, vidurkis −23 %). Pasirinkite diapazoną naudodami `refl_max`; užtikrinti, kad jutiklis nepasiektų
> prisotinimo, yra būtent tai, kam jau skirta automatinė ekspozicija (AE), ir tai apsaugo ρ *teisingumą*, o ne jo diapazoną.

**Skaitykite `reflectance_clipped_fraction`, o ne žymę.** `reflectance_clipped_at_1_0` suveikia dėl
vieno pikselio — realioje scenoje jis pranešė `True`, nors dalis buvo `0.000001`, vienas pikselis
iš milijono. Būtent šis santykis nurodo, ar reikia padidinti viršutinę ribą.

**`bit_depth=8` yra tik atspindžio koeficientas.** Prašymas gauti `radiance` yra atmestas, o ne
patenkintas: atspindžio koeficientas turi fiksuotą 0–1 intervalą, kuris išlieka 8 bitų kodavime, o spinduliavimo intensyvumas neturi
fiksuoto intervalo, todėl norint jį sutalpinti į 8 bitus, reikia kiekvienam kadrui taikyti procentilio ištempimą, kurio atvaizdavimas
keičiasi kiekviename kadre — tai vaizdas, o ne matavimas.

#### Tiesioginis atspindžio srautas (MJPEG)

`GET /api/camera/<serial>/stream-reflectance` teikia 8 bitų atspindžio koeficientą kiekvienam juostos kanalui kaip
`multipart/x-mixed-replace` — M3C kaip 3 kanalų JPEG, M3M kaip pilkosios skalės vaizdą. Užklausos parametrai:
`fps` (numatyta reikšmė 10), `jpeg_q` (numatyta reikšmė 95), `max_h` (0 = natūralus), `band` (viena pavadinta juosta, kaip
nurodyta aukščiau), `align` (`1`, skirtas bendram registravimui — tos pačios taisyklės kaip ir SDK `align`), `refl_max` (8 bitų
kodavimo riba, kaip nurodyta aukščiau). Kiekviena dalis turi
`X-Chloros-Group-Id`, `X-Chloros-Frame-Id`, `X-Chloros-Timestamp-Ns`, `X-Chloros-Bands`,
`X-Chloros-Pixel-Scale`, `X-Chloros-Aligned` ir `X-Chloros-Reflectance-Clipped`.
**Skirtingų kamerų dalys, turinčios tą patį `X-Chloros-Group-Id`, yra tas pats trigeris** —
būtent taip iš naujo sujungiami keli vieno masyvo srautai iš kiekvienos kameros, ir tai garantuojama
serverio pusėje, nes masyvo elementai pateikiami iš sinchronizuotos kadrų grupės (žr.
[Vienas trigeris vienam skaitymui](#one-trigger-per-read--group_id)). Jis grąžina `409`, kai atspindžio koeficientas
nėra prieinamas prisijungimo metu, ir nutraukia srautą, jei jis tampa neprieinamas srauto perdavimo metu, taigi sustojęs
srautas reiškia „nebėra atspindžio koeficiento“, o ne „vis dar gerai, tiesiog statinis“.

> **JPEG yra prarandamasis formatas — šie pikseliai yra matavimas.** Suspaudimo klaida tiesiogiai atsispindi
> atspindžio vertėse ir yra didžiausia kraštuose, kur yra medžiagų ribos. Dėl šios priežasties kokybės
> numatytasis nustatymas yra 95. Jei reikia, kad skaičiai išliktų nepakitę, naudokite
> `grab(processing="reflectance")` / `frame_stream(...)`, kurie grąžina tą patį rezultatą be
> kodeko kelyje (o `bit_depth=8` – jei apribojimas yra duomenų perdavimo kaina, o ne kodekas).

> **EXIF duomenys perkeliami į eksportą.** `process()` nukopijuoja šaltinio įrašo GPS bloką
> **ir jo ExifIFD** į kiekvieną produktą, todėl eksportuose yra `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` ir `CameraSerialNumber`, taip pat
> georeferencija. „`FocalLength`“ yra duomenys, pagal kuriuos „Pix4D“ apskaičiuoja žemės mėginių atstumą – be jų
> rekonstrukcija atliekama pagal visiškai neteisingą mastelį (vienu išmatuotu atveju 411 m plotas
> virto 47,8 km plotu). Kopija sąmoningai nėra `-all:all`: IFD0 struktūrinės žymos sutrikdo
> „LATTICE“ išvestį, o `ExifImageWidth`/`Height` yra pašalinti, nes jie apibūdina šaltinio
> fiksavimą, o ne eksportuotą rasterį.

Fiksavimo etapo papildomos žymos (taikomos radiometriniams lygiams — `radiance`, `reflectance`, `display`):

| Vėliavėlė | Numatytasis | Reikšmė |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + lygio lauko korekcija + 3x3 atskyrimas + NIST radiometrinė skalė. |
| `apply_white_balance` | `True` | WB LUT. Atsižvelgia į DLS, kai DAQ yra susietas su kamera. |
| `apply_index` | `False` | Augmenijos indekso vertinimas. |
| `index_expression` | `None` | Formulės perrašymas. Jei laukastuščias → indeksas įjungiamas automatiškai. |
| `annotated` | `False` | GUI dekoracijų (zebra/tinklelis/piko rodymas) perdengimas. Nėra prieinama `raw`. |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **Grąžinamoji reikšmė yra `chloros_sdk.CapturePaths`, kurių tipas yra `CapturePathMap`, o ne `Dict[str, str]`.**
> `chloros_sdk.CapturePathMap` yra `Dict[str, Union[str, List[str]]]`: vieno lygio
> „`processing`“ kiekvienam serijiniam numeriui suteikia vieną kelią, o „`processing="all"`“ suteikia
> **eilišką sąrašą** visų to fotoaparato išsaugotų produktų. „`ArrayHandle.capture()`“ nepriima
> nepriima `levels=` ir ignoruoja jam nežinomus raktinių žodžių argumentus (`levels`, `daq`,
> `render_index`, `force_daq`), todėl perduokite tik čia nurodytus (`aligned=False`
> išsaugo vietinius failus, kaip ir `ArraySession.capture()`); tiems valdikliams naudokite
> `ArraySession.capture()`. Tiesioginis sudėtinis kompozitas, jei jis buvo transliuojamas, patenka po papildomu
> `"combined"` raktu, o ne pagal serijinį numerį. Kodas, kuris remiasi `str`, sugenda
> sąrašo formoje, nors tipų tikrintuvas neprieštarauja — anotacijoje buvo nurodyta, kad `Dict[str, str]`
> kurį laiką po sąrašo formos išleidimo, todėl ir egzistuoja šis aliasas. Normalizuokite
> tada, kai norite plokščios formos:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

> **Ko negalima rašyti: `.failed`.** Žemėlapis, kurį grąžina `capture()`, yra
> `chloros_sdk.CapturePaths` — paprasta `dict` paklasė, todėl viskas, kas nurodyta aukščiau, veikia
> be pakeitimų — kuri taip pat apima `.failed`: failus, kurių užkulisinė sistema negalėjo įrašyti,
> tuos pačius įrašus kaip [`CaptureResult.failed`](#files-that-could-not-be-written--captureresultfailed)
> (`serial`, `level`, `error` ir `disk_full: True`, kai diskas užpildytas). Dalinis
> įrašymas vis tiek grąžina visus kelius, į kuriuos buvo įrašyta; kai diskas užpildytas, žemėlapis yra
> tuščias, o `.failed` paaiškina, kodėl. `.skipped` išvardija praleidimus pagal kamerą, o
> `.group_seq` – sinchronizuotą grupę, o `.alignment` / `.oriented` yra
> užkulisinių procesų, kaip ir `CaptureResult`. `capture_smart()` juos grąžina kaip
> `result["frames"].failed` ir `result["failed"]`.
>
> ```python
> paths = arr.capture("./out", processing="all")
> if any(f.get("disk_full") for f in paths.failed):
>     raise SystemExit("drive full -- free space and capture again")
> ```

### Masyvų suderinimas

`ArrayHandle` atskleidžia visą suderinimo paviršių. Profiliai pagal numatytuosius nustatymus galioja tik sesijos metu — norėdami juos išsaugoti, aiškiai iškvieskite `export_alignment()`.

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Manual control points: each camera's pixel coordinates of the same
# features, the reference camera's included (>= 2 per camera). Every one
# of num_frames is solved from these points.
arr.calibrate_alignment(method="manual", model="rigid", control_points={
    "214701173": [[412, 300], [1630, 310], [1020, 1180]],   # reference (master)
    "214701292": [[418, 297], [1637, 306], [1027, 1177]],
})

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### Suderinimas prisijungimo metu

`connect_all(align=...)` gali automatiškai suderinti kiekvieną masyvą prisijungimo metu:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

Jei nenurodyta, naudojamas `project.json["config"]["auto_align_on_connect"]`.

### `SensorHandle`

Išsaugotas šviesos jutiklis (DAQ-U, DAQ-M arba DAQ-E), atidarytas `connect_all()` užkulisio [DAQ jutiklių rezervą](#daq-sensor-sessions) — tą patį rezervą, kurį naudoja `connect_daq_sensor()` ir `chloros-cli daq pool-connect`.

```python
reading = proj.sensors["Sky"].read()
if reading is not None:
    spectrum, is_saturated, integration_time, x, y, z = reading
```

- `read()` laukia **kito** jutiklio spektro — tas, kuris jau buvo įrašytas į buferį iškvietimo metu, neįskaitomas — ir grąžina `(spectrum, is_saturated, integration_time, x, y, z)` arba `None`, jei per 5 s nieko negauna. Sustabdytas srautas atnaujinamas. `spectrum` yra išreikštas W/m²/nm, kai jutiklis yra kalibruotas.
- `.sensor` yra jutiklio [`DAQSensorSession`](#daq-sensor-sessions), visais kitais atvejais: `latest(n=...)`, `status()`, `record_start()` / `record_stop()`.
- `.slot_id`, `.model`, `.serial`, `.name` ir `.settings` yra išsaugoti įrašai iš `sensors.json`.

**Ką atkuria `connect_all`.** Kiekvienas jutiklis atidaromas jo išsaugotame adrese — COM prievade, „Bluetooth“ adresu arba „DAQ-E“ serveryje — ir, jei projekte buvo užregistruotas jutiklio identifikatorius, tai turi būti būtent tas jutiklis: kitas įrenginys tame adrese atmetamas kaip neprijungtas. Taikomi jo išsaugoti integracijos laikas, kadrų vidurkavimas ir automatinė ekspozicija, taip pat jo dangtelio korekcija:

| Įrašytas „cap“ | Prijungta su |
| --- | --- |
| „cap“ profilis (pvz., `fov_30`) | Tuo profiliu. |
| Nepasirinkta | `sunshine_cosine`, numatytasis prisijungimo variantas, užregistruotas kaip numatytas (`cap_id_source=auto_default`), todėl apdorojimo metu rodomas įspėjimas. Jutikliai tiekiami be dangtelio: pasirinkite uždėto dangtelio profilį. |
| `none` — GUI pasirinkta kaip **Nėra (jokio dangtelio jutiklis)** | Dangtelio korekcija netaikoma. |

DAQ-E-S nepriima jokio dangtelio profilio, nesvarbu, kas buvo išsaugota: jo difuzorius yra kalibravimo dalis (žr. [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor)). Ankstesniu Chloros įrašytame projekte buvo užregistruotas nuogas jutiklis kaip vienintelis pasirinkimas, todėl jis vėl prisijungia prie `sunshine_cosine`; vėl pasirinkite **Nėra (nuogas jutiklis)** GUI ir išsaugokite projektą.

Jutiklis, kurį kitas klientas jau turi atidarytą rezervuare (CLI, kitas scenarijus), prijungiamas toks, koks yra, be naujų nustatymų taikymo, o „`disconnect_all()`“ leidžia jam veikti; „`connect_all()`“ atidarytas jutiklis yra atlaisvinamas. Kaip ir „`connect_daq_sensor()`“ atveju, Chloros GUI **nėra** grupės dalis: uždarykite jutiklį GUI prieš prijungdami projektą iš skripto.**Reikalavimai ir ataskaita.** Šviesos jutikliams reikalingas veikiantis Chloros užkulisinis modulis, prisijungęs prie mokamo „Chloros+“ lygio („Copper“ ar aukštesnio), o `connect_all()` jo niekada nepaleidžia. Jei nė vienas neveikia, kiekvienas išsaugotas jutiklis yra išvardytas po `report["sensors"]["errors"]`, nurodant šią informaciją. Ten taip pat rodomas jutiklis, kurio backend'as negali atidaryti, nurodant backend'o priežastį; `report["sensors"]["missing"]` lieka tuščias šviesos jutikliams.

---

## Tiesioginė įranga (be backend'o)

Jei norite, kad LATTICE kameros (CI, robotai be galvutės, įterptinės) visiškai nepriklausytų nuo užkulisio, tiesiogiai importuokite `lattice_sdk` — jį pakartotinai eksportuoja `chloros_sdk`. Įspėjimas dėl `CAMERA_AVAILABLE`: `lattice_sdk` yra SDK ratuke, tačiau jam reikalinga „Arena SDK“ vykdymo aplinka ir „OpenCV“ (`cv2`), o ratukas nei vieno, nei kito neįdiegia. Šviesos jutikliai veikia per backendą — [`connect_daq_sensor()`](#daq-sensor-sessions) arba projekto `proj.sensors` — su viena išimtimi: DAQ-M juostos stiprumo pagalbine programa [`DLS`](#daq-m-band-magnitudes--dlsband_magnitudes), kuriam taip pat reikalingas „`bleak`“.

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### Išankstiniai nustatymai ir trigeris

Trys iš keturių išankstinių nustatymų veikia **laisvojo veikimo** režimu: kamera eksponuoja nuolat, o
`capture()` grąžina kitą kadrą. `triggered` yra išimtis — jis parengia
kamerą laukti aparatinio signalo 2-oje linijoje, todėl ji nieko nefiksuoja, kol toks signalas nepasiekia.

| Nustatymas | Trigeris | Naudokite, kai |
| --- | --- | --- |
| `default` | laisvas veikimas | bendrasis naudojimas |
| `high_speed` | laisvas veikimas | 8 bitai, 60 kadrų per sekundę riba, trumpa ekspozicija |
| `high_quality` | laisvas veikimas | 12 bitų, be kadrų per sekundę apribojimo — įprastas pasirinkimas fotografuojant |
| `triggered` | **parengta, 2-oji linija** | kamera prijungta prie M8 sinchronizavimo kabelio, o ją suaktyvina kitas įrenginys |

Jei pasirinksite `triggered` (arba patys nustatysite `trigger_mode="On"`), kai 2-oji linija
nėra aktyvuota, kiekvienas `capture()` pasieks laiko limitą — kaip ir turėtų, nes jūs paprašėte
kameros palaukti. SDK paaiškina, kodėl taip atsitinka; žr.
[SC_ERR_TIMEOUT įrašymo metu](#direct-hardware-backend-free).

> **Pastaba — „GVSP probe“ / `SC_ERR_TIMEOUT -1011` pranešimai prisijungiant nėra klaidos.**
> Prisijungiant SDK bando suderinti**jumbo rėmelius** (9000 baitų GVSP paketus), siekdamas didesnio pralaidumo. Tiesioginiame „taškas-taškas“ tinklo plokštės ryšyje (pvz., vietiniame `169.254.x.x` adrese) tinklas paprastai negali perduoti „jumbo“ rėmų, todėl šis bandymas baigiasi laiko limitu ir į žurnalą įrašomos tokios eilutės:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Tai yra **numatyta atsarginė priemonė**: SDK automatiškai grįžta prie standartinių 1500 baitų paketų, o kamera toliau jungiasi įprastai (toliau einančios eilutės „`[chunk-enable …]`“ yra įprastos prisijungimo sekos dalis). Įrašymas vis dar veikia.
>
> Šį testą galite praleisti, tačiau **jis nėra tik žurnalų slopintuvas — jis išjungia „jumbo“ rėmus.** Kamera atsako į „Don't-Fragment“ pingus tik iki 1500 baitų, nesvarbu, koks geras būtų jūsų tinklas, todėl vien ping testu niekada nepavyks aptikti „jumbo“ rėmų; tik šis testas tai gali padaryti. Išjunkite jį, ir kamera visam laikui naudos standartinius 1500 baitų paketus, bet kuriame tinkle:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Tai verta daryti tik tinkle, apie kurį *žinote*, kad jis nepalaiko „jumbo“ rėmelių, nes tai sutaupo maždaug vieną sekundę prisijungimo laiko kiekvienai kamerai. Kadangi tai yra realus kompromisas, o ne tik kosmetinis, dabar SDK tai nurodo, kai jį naudojate:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Nepakeiskite šio nustatymo, nebent turite priežastį.**Jei paliksite šią funkciją įjungtą, kiekviena kamera bus matuojama vieną kartą per kiekvieną backend sesiją ir dar kartą, kai pasikeičia jos ryšys (kitas adapteris, kitas ryšio greitis arba nutrūkęs ryšys, pvz., pakeitus komutatorių); prisijungiant iš naujo naudojamas tas pats rezultatas.**Po pokyčių už komutatoriaus, kuris liko veikiantis, paleiskite backend iš naujo, kad matavimas būtų atliktas dar kartą.** Pirmasis prisijungimas kartais gali užtrukti keletą minučių dėl šio patikrinimo, ir jei taip atsitinka, tai nurodoma žurnale.
>
> Jei *norite* „jumbo“ pralaidumo, įjunkite „jumbo“ nuo galo iki galo (NIC MTU 9000 + komutatorius, kuris juos praleidžia), arba nustatykite jį naudodami `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000`, kai žinote, kad ryšys jį perduoda — nors geriau naudoti `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` kiekvienam komandos vykdymui, nei nustatyti jį nuolat, nes fiksuotas dydis praleidžia tikrinimą ir neleidžia prisitaikyti prie priešais esančio tinklo. **Kiekvienas** įrenginys maršrute turi perduoti „jumbo“ paketus — įskaitant bet kokį PoE skirstytuvą ar įterptuvą, o tai dažniausiai yra priežastis, dėl kurios kitaip „jumbo“ paketus palaikanti konfigūracija negali jų perduoti.

> **`SC_ERR_TIMEOUT -1011`, pasireiškiantis `capture()` / `grab*()` metu, yra kita problema — tai yra tikra klaida.**
> Pirmiau pateikta pastaba susijusi tik su `-1011`, užregistruotu**prisijungimo laiko zondavimo**metu. Ta pati klaida, iškeliama po**duomenų surinkimo**, reiškia, kad kamera prisijungė sėkmingai, bet nesiunčia jokių vaizdų:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> Tai išduoda kamera, kurios *valdymo* kanalas veikia tinkamai — aptikimas veikia, nustatymai ir `[chunk-enable …]` įrašai sėkmingi — tačiau *kiekvienas* kadras viršija laiko limitą.
>
> **Paprastai tai atsitinka dėl to, kad kamera yra nustatytas veikti pagal aparatinį trigerį.** Esant klaidoms „`trigger_mode="On"`“ ir „`trigger_source="Line2"`“, kamera nieko nesiunčia, kol M8 sinchronizavimo kabelyje neatsiranda elektrinis frontas. Jei neturite kabelio, perduodančio signalą į tą liniją, kiekvienas kadrų paėmimas laukia amžinai. Kamera nėra sugedusi, o tinklas veikia gerai — ji veikia tiksliai taip, kaip jai nurodyta.
>
> `CameraSettings()` ir `default` / `high_speed` / `high_quality` nustatymai leidžia veikti laisvuoju režimu, o įrašymas, kurio laikas baigiasi, kai kamera yra parengta, pateikia paaiškinimą, o ne tik rodo kodą `-1011`. `PRESETS["triggered"]` pagal numatytą funkciją parengia „Line2“.
>
> Norint priversti bet kurią kamerą veikti laisvojo veikimo režimu:
>
> ```python
> import copy
> settings = copy.deepcopy(PRESETS["high_quality"])   # PRESETS entries are shared
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> Jei su „`trigger_mode="Off"`“ vis tiek pasibaigia laiko limitas, kamera tikrai neperduoda duomenų — atsiųskite mums žurnalą ir „`ip link show`“.

#### Spalvų profiliai (RGB tiesioginis peržiūros režimas) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` nustato ekrano spalvų profilį **tiesioginiam peržiūros režimui** RGB kamerose (daugiaspektrinės kameros šio nustatymo nepaiso):

| Profilis | Reikšmė |
| --- | --- |
| `raw` | Visiškai apeiti radiometrinę grandinę. |
| `linear` | DSNU + flat + WB, be CCM, be gama. |
| `natural` | Linijinis + išmatuotas CCM + sRGB gama, tik su paprastu apdorojimu (spalvų išlyginimas + šviesių sričių desaturacija) — realistiškas numatytasis nustatymas. |
| `enhanced` | `natural` ir pilnas „hub-parity“ apdorojimas (spalvų apvalių kraštų pašalinimas, gyvumas, CLAHE vietinis kontrastas). Turtingesnė vaizdo išvaizda, tačiau apdorojimo kaina vienam kadrui yra maždaug **dvigubai didesnė**, taigi mažesnis tiesioginio transliavimo kadrų dažnis. |
| `custom_temp` | `natural`, bet baltos spalvos balansas (WB) fiksuotas prie `custom_cct_k` Kelvino (DLS ignoruojamas; apribotas iki 2000–10000 K serverio pusėje). |

Profilis yra **tik tiesioginiam peržiūrėjimui skirtas** greičio/išvaizdos reguliatorius: išsaugoti kadrai visada gauna visą sodrų apdorojimą, nepriklausomai nuo pasirinkto profilio, todėl pasirinkus `natural`, siekiant sutaupyti kadrų generavimo laiko, į diską įrašomos medžiagos kokybė nesumažėja. Nežinomas profilis sukelia `ValueError`; kai pasiekiamas „chloros“ backend, pakeitimas taip pat yra siunčiamas į jį POST metodu, kad kitas peržiūros kadras tai atspindėtų (tiesioginiai SDK’o vartotojai be backend’o vis tiek gauna nustatymų pakeitimus).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Ryškiausios vietos (ekranas RGB) — `set_highlights`

`LatticeCamera.set_highlights(value)` išskiria ryškiausius, balčiausius **ekrano** pikselius RGB kamerose — tai pataisa, skirta išvengti, kad ryškiai balta paviršiaus dalis būtų atvaizduojama kaip plokščia balta dėmė. `value` yra `-100`..`0` (ribotas); `0` yra išjungtas, ir vaizdas tada atrodo lygiai taip pat, kaip ir be šio nustatymo. Stipresni nustatymai sumažina ekrano baltumą (iki maždaug 60 % esant `-100`) ir išplečia tonų kreivę, todėl paviršius, kurį peržiūros automatinis ryškumas išstūmė į gryną baltą, atgauna savo detales.

Tai yra ekrano valdymo parametras, tačiau, skirtingai nuo sodrumo ir kontrasto (kurie keičia tik tiesioginį peržiūros vaizdą), jis taip pat keičia tai, ką išsaugote: tiesioginis peržiūros vaizdas ir **ekrano lygio** įrašai prisitaiko prie jo, todėl tie failai taip pat gaunasi tamsesni (juose nustatytas `Chloros:DisplayHighlights`); spinduliavimo ir atspindžio eksportai to niekada nedaro, be to, ji negali atkurti apkarpytų jutiklio duomenų — tam reikia sumažinti ekspoziciją. Daugiaspektrinės ir monokromatinės kameros į tai neatsižvelgia. Vertė saugoma `settings.highlights` ir siunčiama (POST) į pasiekiamą „chloros“ užkurtį, tiksliai kaip ir „`set_color_profile`“; CLI atitikmuo yra „`lattice highlights --set N`“.

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_highlights(-60)                      # 0 = off, -100 = strongest
```

#### Mono (M3M) kameros ir `Calibration`

Mono **M3M** kamera (`M3M-<lens>-F<wavelength>`) yra vienos juostos: viena pilkosios skalės plokštuma, be „Bayer“ mozaikos, be 3×3 spektrinio persikryžminimo matricos. `Calibration` ją atpažįsta ir pateikia `is_mono` žymę. Atspindžio koeficientas vis dar taikomas kaip radiometrinis žemėlapis kiekvienam juostos diapazonui (atskyrimas yra tapatybės matrica), tačiau daugiajuostės matematinės operacijos su viena kamera duoda prasmingus rezultatus, o ne beprasmiškus:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

Norėdami sudaryti augmenijos indeksą naudojant monokromatinę įrangą, sujunkite kelias skirtingų bangų ilgių M3M kameras į suderintą daugiabandį sluoksnį (žr. [Matricos suderinimas](#array-alignment)) ir apskaičiuokite indeksą visam tam sluoksniui, o ne vienai kamerai.

#### DAQ-M juostų stiprumai — `DLS.band_magnitudes`

`DLS(mac_address, filter_model="RGN-IMX265")` prisijungia prie DAQ-M per „Bluetooth“ naudodamas savo BLE MAC adresą. Tai vienintelė šviesos jutiklių klasė, kuri bendrauja su jutikliu tiesiogiai, o ne per užkulisinę sistemą, todėl jai reikalinga tai, ko reikia tiesioginėms kamerų klasėms (`CAMERA_AVAILABLE`: „Arena“ SDK-vykdymo aplinka ir „OpenCV“), taip pat `bleak` „Bluetooth“ (`pip install "chloros-sdk[camera]"` arba `pip install bleak`; be jo `DLS` sukelia `DLSError`). Kitur prijungtas „DAQ-M“ (užkulisio rezervas, projektas, GUI) nustoja siųsti skelbimus, todėl pirmiausia jį ten atleiskite. **Reikalingas `mac_address`**: numatytosios reikšmės nėra, nes kiekvienas įrenginys turi savo, o `DLS()` be jos sukelia `DLSError` prieš bandant užmegzti bet kokį ryšį. Raskite MAC adresą naudodami `discover_daq_sensors(transports=["ble"])` (žr. [Atradimas](#discovery--finding-an-address-to-connect-with)) arba `chloros-cli daq pool-discover --only ble`.

`DLS.band_magnitudes(spectrum=None, *, spectral_response=None)` integruoja DAQ-M spektrą (paskutinį rodmenį, kai `spectrum` yra `None`; `DLSError`, jei jo dar nėra) per kameros trijų kanalų atsakus ir grąžina `{"red": …, "green": …, "nir": …}` — raktų pavadinimai atitinka kanalų tvarką; naudojant RGB kameros kreives, trečiasis (`nir`) yra mėlynasis kanalas.

- Jei `spectral_response` nenurodytas, naudojama filtro modelio tipinė kreivė (ta, su kuria buvo sukurtas `filter_model` ir `DLS`), kuri buvo išmatuota viename įrenginyje.
- `spectral_response` vietoj to naudoja pačios kameros išmatuotas kreives: žodyną `{"wavelengths": [...], "R": [...], "G": [...], "B": [...]}`, saugomą tos kameros kalibravimo pakete (`color_correction.spectral_response_smoothed` — RGB kameros; daugiaspektriniai paketai tokios kreivės neturi). Jei žodyno negalima naudoti, grįžtama prie serijos kreivės.

```python
from chloros_sdk import DLS

# camera_curves: {"wavelengths": [...], "R": [...], "G": [...], "B": [...]}
with DLS(mac_address="AA:BB:CC:DD:EE:FF") as dls:
    reading = dls.acquire()
    fleet = dls.band_magnitudes(reading)                         # fleet curve
    own = dls.band_magnitudes(reading,
                              spectral_response=camera_curves)   # the camera's own
```

CLI komanda `chloros-cli lattice dls --camera SERIAL` atlieka tą patį per užkulisinę sistemą, surasdama kameros kreivę įkeltame arba talpyklos kalibravimo pakete, ir nurodo, kurios kameros kreivė buvo panaudota. Per HTTP tai yra `POST /api/dls/cli-read` su `mac` (būtina: be jos atsakymas yra HTTP 400, „`code`“ „`MAC_REQUIRED`“) ir „`camera_serial`“ tekste; atsakymo „`band_magnitudes_source`“ vertė yra „`camera <serial>`“, kai buvo naudojama pačios kameros kreivė, arba „`model <filter>`“, kai naudojama visų kamerų bendra kreivė (kamera be kreivės: ne RGB arba neįkeltas paketas arba įrašyta į talpyklą).

---

## Išimtys

Naudokite bazinę klasę, kad tvarkytumėte „visus Chloros veikimo sutrikimus“:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

> `ChlorosAuthenticationError` ir `ChlorosConfigurationError` yra `chloros_sdk` (ir `chloros_sdk.exceptions`), bet nėra `__all__`, todėl `from chloros_sdk import *` jų neįtraukia: importuokite juos pagal pavadinimą. `ChlorosError` šeima yra išplėsta `ChlorosLocal` ir ant jos sukurtomis funkcijomis (`process_folder()`, `process_lattice_capture()`); „Smart-connect“ paviršius sukuria `ChlorosConnectError`.

Hierarchija:

```

ChlorosError
├── ChlorosBackendError           (backend binary not found / failed to start)
├── ChlorosConnectionError        (HTTP transport failure; no backend running
│                                  with auto_start_backend=False)
├── ChlorosLicenseError           (403 PLAN_UPGRADE_REQUIRED: plan below Copper)
├── ChlorosAuthenticationError    (401 AUTH_REQUIRED: not logged in)
├── ChlorosConfigurationError     (bad configure() inputs)
└── ChlorosProcessingError        (pipeline failed; create/import refused)
    └── ChlorosProjectFilesMissingError  (open_project(): none of the
                                  project's source files found, even after
                                  the relink; .missing_roots, .audit)

ChlorosConnectError                (the smart-connect surface: connect_*, attach_array,
                                    list_*, discover_*, every CameraSession /
                                    ArraySession / DAQSensorSession method,
                                    RecorderHandle, ContinuousHandle,
                                    disconnect_all(), and analyze_array_network()
                                    when no backend answers; 401 and 403 included.
                                    Derives from plain Exception, NOT from
                                    ChlorosError, so `except ChlorosError` will
                                    not catch it. .warning: a warning the
                                    backend sent beside its error (also at the
                                    end of the message); .recording_closed: the
                                    recording a failed DAQ-E restart closed;
                                    both None otherwise)

FrameFetchError (RuntimeError)     (grab() / frame_stream() refusals; .status,
                                    .body, .errors)
ValueError                         (capture() arguments: PNG/JPEG with a level they
                                    cannot hold; a bad packed=)
FileNotFoundError / ImportError    (open_project(): no such folder / no project
                                    loaders)
RuntimeError                       (CameraHandle / ArrayHandle: a backend refusal)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## Pavyzdžiai nuo pradžios iki pabaigos

### 1. Aplanko apdorojimas su pasirinktiniu pažangos juostele

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. „LATTICE“ masyvas realiuoju laiku → atspindžio koeficientas + DAQ etalonas

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. Projektu pagrįsta duomenų surinkimo kampanija

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. Daugiakamerinis kadrų srautas → „NumPy“ apdorojimo grandinė

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. „Headless“ tiesioginis įrangos (be užkulisio) įrašymo scenarijus

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. Galimybių patikrinimas prieš prijungiant 4 kamerų masyvą

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["width"], height=r["height"],     # before binning
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Įrašymo recepto ekvivalentas (grynas Python)

`run_project_recipe()` vykdo CLI receptą tokiu, koks jis yra (žr. [Projekto aplankas ir receptai](#project-folder--recipes)). Tuos pačius veiksmus, parašytus Python kalba, sudaro:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply: connect_all() applied each camera's saved settings. CameraHandle
    # has no settings method; a CameraSession on the same pooled camera does
    # (it is borrowed, so it never releases the camera)
    chloros_sdk.connect_camera(cam.serial).set_settings(
        exposure_time=5000, gain=6.0)

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## MCP serveris (AI asistentai)

`chloros-mcp` leidžia AI asistentui, kuris naudoja [Model Context Protocol](https://modelcontextprotocol.io) (MCP), pvz., „Claude Desktop“, „Claude Code“ ar „Cursor“, valdyti Chloros: apdoroti vaizdus (sukurti projektą, importuoti užfiksuotų vaizdų aplanką, pasirinkti nustatymus, apdoroti, pranešti, kas buvo eksportuota), taip pat valdyti „LATTICE“ kameras, matuoklių masyvus ir DAQ šviesos jutiklius (prisijungti, peržiūrėti, nustatyti ekspoziciją ir spalvas, fotografuoti, įrašyti, atnaujinti programinę įrangą). Jo įrankiai atlieka tas pačias funkcijas, kurias atlieka šis SDK. Jūs pateikiate užklausą paprasta kalba, o asistentas iškvietia serverio įrankius:

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

The assistant calls:
  create_project          {"project_name": "Flight_2026-10-17"}
  import_images           {"folder_path": "D:\\Flights\\2026-10-17"}
  configure_processing    {"indices": ["NDVI"]}
  start_processing        {}
  get_processing_status   {}      (every 30-60 s until its state is "finished")

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.

The assistant calls:
  list_devices            {}
  get_camera_preview      {"array_id": "array-1779862544497"}
  start_capture_run       {"array_id": "array-1779862544497", "mode": "interval",
                           "interval_s": 10, "duration_s": 300,
                           "processing": "reflectance"}
  get_job_status          {"job_id": "..."}   (until its state is "finished")
```

Serveris sukurtas remiantis šiuo SDK: apdorojimo įrankiai kreipiasi į [`ChlorosLocal`](#image-processing--chloroslocal), o aparatinės įrangos įrankiai – [„smart-connect“ iškvietimus](#smart-connect-for-lattice-cameras), [DAQ sesijas](#daq-sensor-sessions), `ChlorosProject` ir [kameros pagalbines programas](#camera-utilities), kurios visos bendrauja su tuo pačiu užkulisiu, kurį naudoja Chloros programa. Jos užklausose yra `X-Chloros-Client: sdk`, todėl serveris taiko „SDK“ prisijungimo ir plano patikrinimus, o kartu taikomos ir „SDK“ taisyklės (pavyzdžiui, „Texture Aware“ funkcija neleidžiama naudoti „LATTICE“ užfiksuotoms nuotraukoms).

### Įdiegimas

Serveris pateikiamas `chloros-sdk` pakete; nieko kito diegti nereikia. SDK įdiegia komandą `chloros-mcp`, kuri yra tokia pati kaip `python -m chloros_sdk.mcp_server`:

```bash
pip install chloros-sdk        # not needed where the Chloros installer set up the SDK (Step 2)
chloros-mcp --help
```

- **Windows:** Chloros diegimo programa per „pip“ įdiegia SDK į jūsų Python aplinką, o tai įdeda „`chloros-mcp.exe`“ į to „Python“ aplinkos „`Scripts`“ aplanką.
- **Linux (.deb):** paketas įdiegia SDK į sistemos katalogą „`python3`“ ir įdeda komandą „`chloros-mcp`“ į „`/usr/bin`“, taigi ji yra kiekvieno vartotojo „`PATH` aplinkoje. Virtualioje aplinkoje (virtualenv) `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` įtraukia komandą `chloros-mcp` į virtualios aplinkos katalogą `bin`.

Serveris naudoja tik standartinę biblioteką, veikiančią ant SDK (Python 3.7+). MCP klientas jį paleidžia ir bendrauja su juo per stdin/stdout; jūs patys jo nevykdote, išskyrus atvejus, kai naudojate `--help`.

### Kliento nustatymas

**„Claude Desktop“:**pridėkite serverį prie „`claude_desktop_config.json`“ (Windows sistemoje – „`%APPDATA%\Claude\claude_desktop_config.json`“; jį atidarysite pasirinkę**„Nustatymai“ > „Kūrėjas“ > „Redaguoti konfigūraciją“**), tada iš naujo paleiskite „Claude Desktop“:

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

**„Claude Code“:**

```bash
claude mcp add chloros -- chloros-mcp
```

**Kursorius:** tas pats įrašas `mcpServers` kaip ir „Claude Desktop“, `~/.cursor/mcp.json` (kiekviename projekte; `%USERPROFILE%\.cursor\mcp.json` Windows sistemoje) arba `.cursor/mcp.json` viename projekte.

Parinktys įvedamos į `args` arba po komandos „Claude Code“ (`claude mcp add chloros -- chloros-mcp --no-auto-start`). Norėdami naudoti konkretų „Python“ SDK vietoj `chloros-mcp` arba `PATH`, paleiskite modulį su tuo Python (jo pilnas kelias nurodomas kaip „`command`“, jei tai nėra pirmasis „`python3`“ „`PATH`“):

```json
{
  "mcpServers": {
    "chloros": {"command": "python3", "args": ["-m", "chloros_sdk.mcp_server"]}
  }
}
```

Klientas paleidžia serverį be jūsų apvalkalo, todėl jei jis praneša, kad serveris nepavyko paleisti, `chloros-mcp` greičiausiai nėra tame `PATH`, kurį mato klientas. Vietoj to nurodykite pilną kelią (`where chloros-mcp` Windows sistemoje, `which chloros-mcp` Linux sistemoje, kur .deb failas yra `/usr/bin/chloros-mcp`), arba paleiskite modulį su Python, kuriame yra SDK.

### Parinktys

| Parinktis | Numatytasis | Ką daro |
| --- | --- | --- |
| `--backend-url URL` | `http://127.0.0.1:5000` | Naudotinas Chloros užpakalinis modulis. |
| `--no-auto-start` | išjungta | Niekada nepaleisti užpakalinės dalies: naudoti jau veikiančią, pvz., Chloros programėlės, Linux užpakalinės dalies paslaugą arba tunelį į kito kompiuterio (žr. [Nuotolinio užpakalinio modulio režimas](#remote-backend-mode-pip-only-host-via-tunnel); tokiu atveju aplankų keliai turi nurodyti tą patį aplanką abiejuose kompiuteriuose). |
| `--backend-exe PATH` | ten, kur Chloros jį įdiegia | Užkulisinė programa, kurią reikia paleisti, kai nė viena neveikia, jei Chloros įdiegtas ten, kur SDK jos neieško. |
| `--log-level LEVEL` | `WARNING` | `DEBUG`, `INFO`, `WARNING` arba `ERROR`. Įrašai siunčiami į „stderr“, kurį dauguma klientų rodo savo MCP žurnale; „stdout“ perduoda tik protokolo pranešimus. |

### Įrankiai

Kiekvieno įrankio argumentai atitinka to SDK iškvietimo, kurį jis apgaubia, argumentus; klientas pateikia kiekvieną iš jų su visa schema. Keliai yra absoliutūs.

**Apdorojimas**

| Įrankis | SDK iškvietimas | Ką daro |
| --- | --- | --- |
| `create_project` | `ChlorosLocal.create_project` | Sukuria projektą ir nustato jį kaip atidarytą projektą, su kuriuo dirba apdorojimo įrankiai. Nurodžius paprastą pavadinimą, aplankas sukuriama numatytame projekto aplanke; nurodžius absoliutų kelią, jis sukuriama ten. |
| `open_project` | `ChlorosLocal.open_project` | Atidaro išsaugotą projektą ir nustato jį kaip atidarytą projektą. Perkelto projekto keliai nukreipiami į aplanką, kuriame jis dabar yra; `relink_folder` (absoliutus) nurodo aplanką, kuriame dabar yra kiti trūkstami failai. Grąžina `source_files` ir `image_count`. Nerasta: klaida, nurodanti, kur jie buvo, ir pavadinanti `relink_folder`; nieko neatidaryta. |
| `import_images` | `import_images` | Importuoja Survey3 poras `.RAW`+`.JPG`, „LATTICE“ `.tif` užfiksuotus duomenis ir DAQ `.daq` įrašus; grąžina `count` (vaizdus) ir `scan_count` (įrašus). |
| `configure_processing` | `configure` | Keičia perduotus nustatymus, o kitus – ne (reikšmės: [Palaikomos reikšmės](#supported-values)); `custom_settings` siunčia visą nurodytą konfigūraciją. |
| `get_project_settings` | `get_config` | Nuskaito atidaryto projekto išsaugotus apdorojimo, eksporto ir indeksavimo nustatymus. |
| `start_processing` | `process(wait=False)` | Pradeda apdoroti atidarytą projektą ir iš karto grįžta. |
| `get_processing_status` | `get_status` | Praneša apie `state` (`idle`, `starting`, `processing` arba `finished`) ir pažangą; baigus, įrašomi vykdymo `summary`, `export_check_failure`, kai eksportų nebuvo galima patikrinti, ir `image_failure`, kai buvo prarasti vaizdai: kiti produktai įrašomi; praneškite, kad vykdymas nepavyko. |
| `process_folder` | `process_folder` / `process_lattice_capture` | Sukuria projektą aplanke, importuoja jį, konfigūruoja ir paleidžia vykdymą vienu iškvietimu (`lattice: true`, skirtas „LATTICE“ vaizdų įrašymui). Kol vykdymas vyksta, jis atmetamas dar prieš ką nors sukuriant, o vidinė sistema tuo metu taip pat atmeta projekto pakeitimus iš bet kurio įrankio. |
| `export_light_sensor` | `export_light_sensor` | Įrašo kalibruotus `.daq` ir `.csv` failus, skirtus projekto šviesos jutiklio įrašams, neapdorodamas vaizdų, ir, jei gamyklinis kalibravimas nėra išsaugotas talpykloje, jį atsisiunčia iš „MAPIR Cloud“ (žr. [Šviesos jutiklio įrašai](#light-sensor-recordings--calibrated-daq--csv)). |
| `read_image_audit_tags` | `read_image_audit_tags` | Skaito apdoroto vaizdo Chloros audito metaduomenis (šaltinis, kalibravimas, pritaikyti etapai). |

**Kameros ir matricos**

| Įrankis | SDK iškvietimas | Ką daro |
| --- | --- | --- |
| `list_devices` | `list_cameras`, `list_arrays`, `list_daq_sensors` | Kas šiuo metu yra prijungta bet kuriame kliente, kiekviename su `connected_here`; taip pat serveryje vykdomos užduotys. |
| `discover_devices` | `discover_lattice_cameras`, `discover_daq_sensors` | Kameros ir šviesos jutikliai, kuriuos būtų galima prijungti. |
| `get_device_status` | `CameraSession.status`, `ArraySession.status` / `capability`, `DAQSensorSession.status` | Vienos kameros, masyvo (su jo pajėgumais ir būkle) arba šviesos jutiklio realaus laiko būsena. |
| `analyze_array_network` | `analyze_array_network` | Ką šis kompiuteris ir tinklas gali palaikyti siūlomam masyvui. |
| `connect_camera` / `connect_array` | `connect_camera` / `connect_array` | Prijunkite kamerą arba sinchronizuotą masyvą (pirmiausia pagrindinę). |
| `connect_project_devices` | `open_project(...).connect_all` | Prijunkite kiekvieną įrenginį, kurį prisimena išsaugotas projektas, su jo nustatymais. |
| `disconnect_device` | `disconnect` / `force_disconnect` | Atjunkite kamerą, masyvą arba šviesos jutiklį (žr. „Elgsena“). `keep_settings: true`, taikomas matricos atžvilgiu, išsaugo kiekvienos kameros nustatymus į jos pačios atmintį, o `clear_settings: true` išvalo tą atmintį (jos rezultatas perduodamas `camera_memory`). |
| `disconnect_project_devices`, `align_project_arrays`, `capture_project_devices` | `disconnect_all`, `align_arrays`, `capture_all` | Projekto įrenginiai, visi kartu. |
| `read_camera_settings` / `set_camera_settings` | `CameraSession.read_nodes` / `set_settings` | Kameros nustatymų skaitymas arba rašymas pagal patogų pavadinimą arba GenICmazgo pavadinimo. |
| `set_array_frame_rate` | `ArraySession.set_target_fps` | Nustatyti masyvo dažnį arba grąžinti jį į automatinį režimą. |
| `capture_camera` / `capture_array` | `CameraSession.capture` / `ArraySession.capture` | Vienas kadravimas (`fastest` ir `wait_for_exposure` yra `capture_fastest` ir nuotrauka su nustatytu ekspozicijos laiku). Jei nėra `output_dir`, nuotrauka išsaugoma atidaryto projekto`captures` aplanką, o failai įtraukiami į projektą (laikomi atidėtais, kol baigsis apdorojimo ciklas); tai patvirtina atsakymo `project_add` tekstas. |
| `start_capture_run` | `ArraySession.continuous`, `capture_repeated` | Nuolatinis arba intervalinis fiksavimas, kaip užduotis. |
| `start_array_recording` | `ArraySession.record` / `burst` | Vaizdo įrašas su sujungtu indeksu arba neapdorota serija, kaip užduotis. |
| `build_burst_video` | `ArraySession.build_video` | Serijos vaizdų perdirbimas į kalibruotą vaizdo įrašą, kaip užduotis. |
| `get_job_status` / `stop_job` | | Užduoties stebėjimas arba sustabdymas. |
| `get_camera_preview` | | Kiekvienos kameros tiesioginis peržiūros vaizdas, pateikiamas kaip atskiras vaizdas. |
| `grab_frames` | `ArraySession.grab`, `CameraHandle.grab` | Vienas kadras (arba vienas sinchronizuotas rinkinys) apdorojimo lygiu, nesaugant: statistika pagal plokštumą ir kiekvieno kadro PNG failas. |
| `calibrate_array_alignment`, `get_array_alignment`, `clear_array_alignment`, `nudge_array_alignment`, `export_array_alignment`, `import_array_alignment` | `ArrayHandle.calibrate_alignment`, `alignment_status`, `clear_alignment`, `tweak_alignment`, `export_alignment`, `import_alignment` | Masyvo išdėstymas. |

**Šviesos jutikliai**

| Įrankis | SDK-funkcija | Ką daro |
| --- | --- | --- |
| `connect_light_sensor` | `connect_daq_sensor` | Prijungti DAQ-U, DAQ-M arba DAQ-E (`cap_id` nustato jo ribos korekciją). |
| `get_light_sensor_reading` | `DAQSensorSession.latest` | Naujausi matavimo duomenys: laiko žyma, prisotinimas, spektro diapazonas ir pikas, taip pat spektras, išlygintas pagal bangos ilgio intervalus (`full_spectrum` kiekvienam taškui). |
| `set_light_sensor_streaming` | `stream_start` / `stream_stop` | Srauto paleidimas arba sustabdymas. |
| `start_light_sensor_recording` / `stop_light_sensor_recording` | `record_start` / `record_stop` | Įrašyti `.daq` failą kaip užduotį; `duration_s` jį sustabdo savaime. |
| `set_light_sensor_cap` | `DAQSensorSession.set_cap` | Viršutinės ribos korekcija (`none`, kai jutiklis be dangtelio). |
| `get_light_sensor_bindings` / `set_light_sensor_bindings` | `daq_bindings` / `set_daq_bindings` | Kuris jutiklis perduoda kiekvienos kameros ir matricos atspindžio koeficientą. |
| `get_light_sensor_band_presets` / `get_light_sensor_reflectance` | `daq_band_presets` / `light_sensor_reflectance` | Juostų rinkiniai ir dviejų srautinio perdavimo jutiklių aplinkos bei objekto atspindžio koeficientai ir indeksai. |
| `get_light_sensor_device_info`, `set_light_sensor_network`, `set_light_sensor_auto_stream`, `enable_light_sensor_ptp`, `reboot_light_sensor`, `update_light_sensor_firmware` | `device_info`, `set_network`, `set_auto_stream`, `enable_ptp`, `reboot`, `update_firmware` | „DAQ-E“ įrenginio nustatymai, išsaugoti pačiame įrenginyje. |

**Kameros pagalbinės programos** ([Kameros pagalbinės programos](#camera-utilities))

| Įrankis | SDK-funkcija | Ką daro |
| --- | --- | --- |
| `get_camera_color_settings` / `set_camera_color_settings` | `camera_color_profile`, `camera_color_adjust`, `camera_highlights` ir jų nustatymo funkcijos | RGB kameros ekrano spalvos: profilis ir spalvų temperatūra, sodrumas / kontrastas, šviesios sritys. |
| `run_camera_white_balance` / `set_camera_white_balance` | `camera_white_balance_once` / `set_camera_white_balance` | Vienkartinis baltos spalvos balansavimas arba automatinis / išjungtas / fiksuoti santykiai. |
| `list_camera_presets` / `apply_camera_preset` | `camera_presets` / `apply_camera_preset` | Kameros išankstiniai nustatymai. |
| `list_filter_models` | `camera_filter_models`, `camera_filter_model_info` | Filtrų modeliai arba vienas pagal pavadinimą. |
| `get_camera_firmware_status` / `update_camera_firmware` | `camera_firmware_status` / `update_camera_firmware` | Programinės įrangos versijos ir blykstė (numatyta užduotis). |
| `get_camera_power_info` / `set_camera_power` | `camera_power_probe`, `sleep_camera`, `wake_camera` | Maitinimo ir šiluminė būsena; miego arba budėjimo režimas. |
| `calibrate_camera_with_target` | `calibrate_camera_with_target` | Rodomo kalibravimo taikinio koeficientai. |
| `get_network_adapters` / `fix_network_settings` | `network_adapters` / `fix_network_settings` | Kameros tinklo adapteriai, įvertinimas ir pataisa (tik administratoriui). |
| `apply_vignette_correction` | `apply_vignette_correction` | Vaizdų aplanko vinjetės korekcija. |
| `read_dls` | `read_dls` | Vienas DLS nuskaitymas. |
| `probe_system` / `get_system_info` | `probe_system` / `system_info` | Pagrindinio kompiuterio aparatinė įranga ir veikimo aplinka. |

**Laikinieji fotoaparatai, suderinimo profiliai ir indeksai** ([Laikinieji masyvai, suderinimo profiliai ir indeksai](#transient-arrays-alignment-profiles--indices)). Kiekviena čia esanti priemonė, atidarianti fotoaparatus, naudoja `keep_settings` / `clear_settings` (numatyta reikšmė „false“): žr. [Kameros atmintį](#camera-memory--keep_settings--clear_settings).

| Įrankis | SDK-kvietimas | Ką daro |
| --- | --- | --- |
| `start_camera_continuous` / `start_multi_camera_stream` | `start_camera_continuous` / `start_multi_camera_stream` | Įrašyti vienos kameros vaizdą arba sinchronizuotai transliuoti kelių kamerų vaizdą kaip užduotį (`duration_s` jį sustabdo automatiškai). |
| `capture_cameras_transient` | `capture_cameras_transient` | Vienas sinchronizuotas įrašymas neprijungiant masyvo. |
| `test_array_sync` / `detect_array_master` | `test_array_sync` / `detect_array_master` | Trigerio sinchronizacijos patikrinimas ir informacija, kurios kameros yra sinchronizacijos linijoje. |
| `analyze_array_models` | `analyze_array_models` | Ką šių modelių masyvas gautų šiame tinkle. |
| `calibrate_alignment_profile` | `calibrate_alignment_profile` | Išspręskite ir išsaugokite suderinimo profilio failą (`options` saugo išsamesnes sprendimo parinktis). |
| `capture_aligned_multiband` / `start_aligned_stream` | `capture_aligned_multiband` / `start_aligned_stream` | Suderintas daugiabandis vaizdas arba jų srautas kaip užduotis. |
| `read_alignment_profile` / `reorder_alignment_profile` | `alignment_profile_info` / `reorder_alignment_profile` | Profilio failo turinys; juostų tvarka ir įjungtos kameros. |
| `list_index_presets`, `compute_index`, `capture_live_index` | `index_catalog`, `compute_index`, `capture_live_index` | Indekso nustatymai ir gradientai; daugiajuostinio TIFF failo indeksas; fiksavimas į indeksą vienu žingsniu. |

**Laiko sinchronizavimas, projekto aplankas ir receptai** ([PTP Grandmaster](#ptp-grandmaster--time_sync_status), [Projekto aplankas ir receptai](#project-folder--recipes))

| Įrankis | SDK-kvietimas | Ką daro |
| --- | --- | --- |
| `get_time_sync_status` | `time_sync_status`, `time_sync_peers`, `time_sync_cameras` | PTP pagrindinio mazgo būklė, jo lygiaverčiai mazgai ir kiekvienos matricos kameros PTP būsena. |
| `restart_time_sync` / `set_time_sync_priority` | `restart_time_sync` / `set_time_sync_priority` | Perkraukite jį; nustatykite jo prioritetus. |
| `get_project_folder` / `set_project_folder` / `reset_project_folder` | `project_folder` / `set_project_folder` / `reset_project_folder` | Aplankas, kuriame kuriami nauji projektai. |
| `run_project_recipe` | `run_project_recipe` | Vykdykite išsaugoto projekto receptą kaip užduotį su jos žurnalu. Pirmiausia atliekamas patikrinimas: receptas su klaida atmetamas ir nieko nevykdomas; apie neprijungtus masyvus / jutiklius pateikiamas įspėjimas (jie neprijungiami: pirmiausia vykdomas `connect_project_devices`). `dry_run` grąžina patikrinimo rezultatą ir nieko nevykdo. |

Nėra įrankio, kuriuo būtų galima atidaryti išsaugotą projektą apdorojimui: kiekviena apdorojimo užduotis prasideda nuo `create_project` (arba `process_folder`).

### Reikalavimai

- **Tame pačiame kompiuteryje turi būti įdiegta Chloros.** Apdorojimą atlieka Chloros užkulisinė programa. Vietoj to serveris gali naudoti užkulisinę programą, prieinamą per tunelį (`--backend-url` su `--no-auto-start`, žr. [Parinktys](#options)).
- **Prisijungimas tame kompiuteryje** per Chloros programėlę arba naudojant `chloros-cli login`. Serveris negali prisijungti už jus: iškviestas įrankis, kai esate atsijungę, pateikia atsakymą „Neprisijungta prie Chloros“ su backend priežastimi, o asistentas jus apie tai informuoja.
- **Bet kuris mokamas „Chloros+“ planas („Copper“ ar aukštesnis).** Serveris veikia per SDK, o nemokamame „Iron“ plane jo nėra: įrankis, paleistas nemokamame plane, atsako „Nėra prieinama šiame Chloros plane“, nurodydamas užkulisinės sistemos priežastį.
- Python 3.7 ar naujesnė versija su SDK ir MCP klientas, kuris paleidžia vietinius (stdio) serverius.

### Veikimas

- **Backend.** Paleidus serverį arba peržiūrint jo įrankių sąrašą, niekas nepradedama; pirmasis įrankio iškvietimas užmezga ryšį. Jei veikia Chloros programa (arba Linux backend paslauga), serveris naudoja jos backendą ir palieka jį veikiantį. Jei joks backend neveikia, serveris paleidžia įdiegtąjį be lango. Kai serveris užsidaro (jūs uždarote klientą arba jis iš naujo paleidžia serverį), jis sustabdo savo pradėtas duomenų surinkimo užduotis ir šviesos jutiklio įrašus bei atjungia prijungtus įrenginius; backend toliau veikia, todėl nebaigtas apdorojimo ciklas tęsiasi iki galo, o po 30 minučių, jei niekas jo nenaudoja, jis sustabdomas automatiškai. Naudojant `--no-auto-start`, serveris niekada nepaleidžia foninės tarnybos: jei įrankis iškviečiamas, kai foninė tarnyba neveikia, jums bus pasiūlyta atidaryti Chloros programėlę, o po to, kai tai padarysite, kitas iškvietimas užmegs ryšį.
- **Vienas atidarytas projektas.** Fone veikianti programa turi vieną atidarytą projektą, kuriuo dalijasi su Chloros programa, kai ši yra atidaryta, todėl, kol veikia asistentas, programoje nedirbkite su kitu projektu.
- **Keičiami tik perduoti nustatymai.** `configure_processing` palieka kiekvieną nenurodytą nustatymą jo išsaugotoje reikšmėje (SDK `configure()`, jei jie nėra perduoti, atkuria „debayer“, vinjetės korekciją, atspindžio kalibravimą, PPK ir eksporto formatą į numatytuosius; serveris juos užpildo iš projekto). Jei jis negali nuskaityti projekto išsaugotų nustatymų, nieko nekeičia ir apie tai praneša, nebent būtų nurodyti visi penki. `indices` pakeičia projekto indeksų sąrašą. Rezultate išvardijama tai, kas buvo išsiųsta kaip `settings_sent`, o pavadinimai po `warnings` nurodo bet kurį indeksą, kurio kameros juostos negali sukurti.
- **Apdorojimas vyksta pagal „pradėti-tada-patikrinti“ principą.** `start_processing` grįžta, vos tik prasideda apdorojimas, kuris trunka nuo kelių minučių iki kelių valandų, o asistentas po to siunčia `get_processing_status`. `state` yra `idle` (nėra apdorojimo), `starting` (užkulisiai dar nepranešė apie vykdymą, iki 20 s), `processing` arba `finished`, su vykdymo [santrauka](#post-run-summary--hints): kas buvo eksportuota, ir patarimai, jei eksportuota mažiau nei tikėtasi (nėra vaizdų, nerastas kalibravimo taikinys). Rezultatai įrašomi į projekto aplanką, kaip ir naudojant programėlę. `start_processing` atmetamas, kol vyksta vykdymas, o užpakalinė sistema, kuri nustoja atsakyti, pranešama kaip klaida, o ne kaip užbaigtas vykdymas.
- **Ką mato asistentas.** SDK įspėjimai atsispindi įrankio rezultatuose kaip `warnings`, o nesėkmės (atsijungimas, planas, netinkamas kelias) – kaip klaidų tekstas, kurį jis gali perskaityti ir perduoti jums. Sąrašas, kuriame yra daugiau nei 20 elementų, sutrumpinamas iki pirmųjų 20, o jo pilnas ilgis nurodomas kaip `<key>_total`. Keliai turi būti absoliutūs.
- **Įrenginiai yra bendrai naudojami.** Kameros, matricos ir šviesos jutikliai veikia serveryje, todėl asistentas gali valdyti vieną iš jų per prijungtą Chloros programėlę, o jo atliekami pakeitimai rodomi programėlėje. `disconnect_device` pateikia informaciją apie tai, prie ko prisijungė serveris; įrenginys, prie kurio prisijungė kitas klientas (programėlė, `chloros-cli`, skriptas, kitas `chloros-mcp`) atleidžiamas tik naudojant `force: true`, kuris yra SDK `force_disconnect`, o atmetimo pranešime nurodoma, kas jį prijungė, jei serveris tai žino; įrenginys, kurio užkulisiai visiškai neturi, pranešamas kaip neprisijungęs (`released: false`, `connected: false`, nėra ką atskleisti). Užrašymas arba transliacija, kurią vykdo užkulisinė sistema, turi savo `get_job_status` peržiūrą su `counts` — `frames_written`, `frames_dropped`, `frames_failed`, `drop_reason` (kadrų grupės daugia-kamerų transliacijai) — ir vienos eilutės `summary` („įrašyta 220 kadrų, prarasta 55, nesėkmių 0: ...“), kol jis veikia, o baigus – galutiniai skaičiai; kito kliento sąrašas (`other_clients_jobs`) pateikia tą patį (`list_cameras()`, `list_arrays()` ir `list_daq_sensors()` pateikia kiekvieno įrenginio`connected_by`: `client`, `label`, `name`, `at`). Kiekvienas „`chloros-mcp`“ save identifikuoja serveriui kaip „`chloros-mcp (pid N)`“ („`chloros_sdk.connect.CLIENT_LABEL`“, siunčiamas kaip „`X-Chloros-Client-Label`“; bet kuris SDK skriptas gali jį nustatyti). Norint sustabdyti kito kliento šviesos jutiklio įrašymą, taip pat reikia `force`.
- **Ilgas darbas yra užduotis.** `start_capture_run`, `start_array_recording`, `build_burst_video`, `start_light_sensor_recording`, `start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`, `run_project_recipe`, ir bet kokio užklausimo dėl fiksavimo, sprendimo ar indekso, pvz., `background`, atveju iš karto grąžinkite `job_id`; po to seka `get_job_status`, o `stop_job` užbaigia . Vykdymas tęsiasi, kol nėra sustabdytas, nebent jam būtų suteiktas `max_captures`, `duration_s` (arba, serijos atveju, `max_frames`); `min_free_gb` atsisako paleisti, jei diske yra per mažai laisvos vietos. Jokių apribojimų netaikoma, nebent juos nustatytumėte. Įrašymas ar srautas, kurį vykdo užkulisinė programa (`start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`) išlaiko užkulisio užduoties identifikatorių, todėl kitas `chloros-mcp` — paleistas vėliau — jį suranda pagal tą identifikatorių: `get_job_status` jį seka (ir išvardija tokias užduotis kaip `other_clients_jobs`, nuo `GET /api/camera/cli-jobs`, kiekvieną su `started_by`), o „`stop_job`“ jį sustabdo tik su „`force: true`“. Bet kuri kita užduotis (fiksavimo ciklas, masyvo įrašymas ar serija, fone vykstantis fiksavimas) vyksta tame „`chloros-mcp`“, kuris jį paleido, ir tik tas serveris apie tai žino: kitas, kuris jo paprašė, tai patvirtina. Sustabdyta užduotis skaito `stopping`, kol išrašo savo eilę, tada `finished` su galutiniais skaičiais, 10 minučių, o iš bet kurio „`chloros-mcp`“: tas, kuris jį paleido, mato kito priverstinį sustabdymą lygiai taip pat. `get_job_status` niekada nelaukia sustabdymo: jo metu (savo `stop_job`, kito klientoarba sustabdymo, kuris atlaisvina užduoties, kuri baigėsi savaime, kameras) jis iš karto atsako su `stopping` ir tiesioginiais skaičiais, o `stop_job`, priklausantis jau sustojusiai užduočiai, tai patvirtina. Baigusi užduotis nurodo priežastį `stop_reason`: savo priežastį (`no_frames`, `disk_full`, `member_missing`, arba, jei tai nepertraukiamas masyvo vykdymas, `count`, `duration`, `array_gone`), o `limit reached` – tik tada, kai jos skaičiai rodo jos `count` (arba serijos `max_frames`, įrašo`duration_s`) pasiekiamas, `error`, `stopped` (su „`stopped_by`“), kai klientas jį sustabdė, kitais atvejais – „`finished`“.
- **Kur patenka įrašai.** Į atidaryto projekto aplanką „`captures`“, kur juos išsaugo programa, nebent būtų nurodytas „`output_dir`“.
- **Vaizdai.** `get_camera_preview` ir `grab_frames` grąžina vaizdus, kuriuos asistentas gali peržiūrėti, be jų JSON duomenų. `grab_frames` vaizdai peržiūrai yra kontrasto atžvilgiu išlyginti; jų statistiniai duomenys atspindi tikrąsias vertes.
- **Kai serveris užsidaro**, jo paleistos užduotys sustabdomos, o jo pradėtas šviesos jutiklio įrašymas uždaromas. Jei serveris paleido užkurtį, prieš jį sustabdydamas jis taip pat atjungia prijungtus įrenginius; programėlės užkuryje jie lieka prijungti.
- **Protokolas.** Tik „stdio“ (be HTTP galinio taško), tik įrankiai (be išteklių ar užklausų), tekstiniai ir vaizdiniai rezultatai, MCP protokolo versijos nuo 2024-11-05 iki 2025-11-25.**Kas neįtraukta.** Prisijungimas ar atsijungimas (paskyra yra jūsų; serveris negali prisijungti) ir backend'o, kurio serveris nepaleido, sustabdymas. Viskam kitam, ką šis SDK atlieka su duomenų apdorojimu ir aparatine įranga, yra skirtas atitinkamas įrankis. (DAQ-E konfigūravimas – jo pavadinimas, autentiškumo žetonas, OTA slaptažodis, parašo reikalavimas, gamyklinių nustatymų atstatymas – o tiesioginiai DAQ serveriai yra tik „`chloros-cli`“ kompetencija, taip pat ir SDK: neteisingas pavadinimas ar žetonas užblokuoja kiekvieną Chloros įdiegimą į įrenginį.)

---

## Automatinis užpakalinės dalies paleidimas

„Smart-Connect“ įėjimo taškai — `connect_camera`, `connect_array`, `connect_daq_sensor`, `discover_lattice_cameras` ir `discover_daq_sensors` — yra „ploni“ HTTP-klientai, kurie daro prielaidą, kad fonas klausosi adresu `127.0.0.1:5000` (numatytoji „smart-connect“ sąsajos URL). Kai GUI arba CLI jau veikia, vienas iš jų jau veikia. Paleidus tik skriptą, jo gali nebūti — todėl šios funkcijos **automatiškai paleidžia pridedamą užkulisio programą** (be lango, taip pat kaip tai daro `ChlorosLocal`) prieš pirmąjį iškvietimą, tada laukia iki 60 s, kol ji paleis. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` ir `analyze_array_network()` jo niekada nepaleidžia.

Taisyklės:

- **Kuriama tik vietinė URL.** `backend_url`, nukreipiantis į `localhost` / `127.0.0.1` / `[::1]`, yra tinkamas; bet kuris kitas kompiuteris laikomas kieno nors kito kompiuteriu ir niekada nėra paleidžiamas.
- **Jei užkulisinis procesas užsidaro prieš atsakydamas, laukimas nutraukiamas.** Skambutis iškart grįžta su išėjimo kodu, komanda, kurią jis vykdė, ir paskutinėmis eilutėmis, kurias išvedė užkulisinis procesas, vietoj to, kad lauktų 60 s. Išėjimo kodas 98 yra išimtis: kitas foninis procesas jau užima prievadą arba užrakto failą (priklausantį Chloros programai, foninio proceso paslaugai arba kitam skriptui, kuris vis dar paleidžiamas), todėl iškvietimas toliau laukia, kol tas procesas atsakys. `ChlorosLocal` veikia taip pat.
- **Foninis procesas paliekamas veikti, kad būtų galima jį pakartotinai naudoti** (kaip ir CLI) — jūsų skriptui pasibaigus, numatytasis išjungimas nevyksta. Pakartotinai paleidus skriptą, pakartotinai naudojamas veikiantis užkulisinis procesas.
- **Atsisakykite naudojant `auto_start_backend=False`** bet kuriame iš šių iškvietimų (pvz., kai nurodote nuotolinį backendą arba patys valdote backendo gyvavimo ciklą).

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

Jei negalima rasti arba paleisti pridedamo binarinio failo, vėlesnis HTTP iškvietimas sukelia veiksmingą, **platformai pritaikytą** `ChlorosConnectError`, o ne paprastą „ryšys atmestas“ sekimą — Windows sistemoje ji nukreipia į darbalaukio programą arba komandą „`chloros-cli`“; Linux sistemoje (be grafinės sąsajos) ji nukreipia į komandą „`chloros-cli`“ arba „`.deb`“.

---

## Aplinka ir antraštės

SDK pažymi kiekvieną užkulisinį HTTP iškvietą su `X-Chloros-Client: sdk`. Užkulisinė sistema taiko SDK/CLI licencijavimo taisykles (reikalingas prisijungimas **ir** mokamas „Chloros+“ planas), o ne GUI nemokamo lygio kelią. Tai nustatoma automatiškai importavimo metu — jums nereikia nieko daryti.

Antraštė pridedama prie kiekvieno `requests` / `urllib`, kurio URL-e yra `localhost` arba `127.0.0.1`, bet kuriame prievade — įskaitant jūsų pačių vietines paslaugas — ir pakeičia jūsų pačių nustatytą vertę. Kreipimaisi į kitus kompiuterius lieka nepakitę.

Perrašykite vidinio serverio URL, perduodami „`backend_url=`“ („`ChlorosLocal`“ priima „`backend_url=`“ arba `api_url=`; `process_folder()` ir `process_lattice_capture()` priima tik `api_url=`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(Įdiegtas užpakalinis modulis klausosi tik kilpos jungtyje, todėl LAN `backend_url` negali jo pasiekti; žr. [Nuotolinio užpakalinio modulio režimas](#remote-backend-mode-pip-only-host-via-tunnel).

---

## Versijos ir suderinamumas

- SDK versija pateikiama kaip `chloros_sdk.__version__`.
- SDK išvestys priklauso nuo komplekte esančios backend versijos. Senesnio SDK ir naujesnio backend derinimas paprastai veikia (į priekį suderinami galiniai taškai), tačiau naujesnio SDK ir senesnio backend derinimas gali sukelti `404` klaidas naujuose galiniuose taškuose — atnaujinkite darbalaukio programą, kad ji atitiktų.
- „Smart-connect“ sąsaja (`connect_camera` / `connect_array` / `connect_daq_sensor`) ir tinklo analizės galinis taškas grąžina stabilias JSON schemas; nauji laukai yra papildomi.

---

## Trikčių šalinimo patarimai

- **`ChlorosAuthenticationError: Login required`** (iš `ChlorosLocal`; „Smart-Connect“ iškvietimai sukelia klaidą `ChlorosConnectError: Login required. …` dėl tos pačios priežasties) → Šiame kompiuteryje vieną kartą paleiskite „`chloros-cli login EMAIL PASSWORD`“ arba prisijunkite per Chloros darbalaukio programą.
- **`ChlorosProcessingError: Failed to start processing: … The project folder is too deep for this computer …`** (arba „`Failed to create project: …`“ iš „`create_project()`“) → šiame kompiuteryje išjungti Windows ilgi keliai (Windows numatytasis nustatymas), todėl joks kelias negali viršyti 260 simbolių, o produktai turi būti išdėstyti iki maždaug 130 simbolių žemiau projekto aplanko. Nieko nebuvo apdorota. Pranešime nurodytas ilgiausias tinkamas projekto aplankas: naudokite trumpesnį arba įjunkite Windows ilgus kelius ir iš naujo paleiskite Chloros. Windows diegimo programa siūlo šią parinktį kaip „Įjungti ilgus Windows kelius“, kuri pagal numatytuosius nustatymus yra pažymėta; kitaip naudokite grupės politiką: *Kompiuterio konfigūracija > Administraciniai šablonai > Sistema > Failų sistema > Įjungti ilgus Win32 kelius*.
- **`ChlorosConnectError: No Chloros backend is running …`** → „Smart-connect“ funkcija automatiškai paleidžia vietinį užkulisinį procesą, todėl ši klaida pasirodo tik tada, kai negalima rasti arba paleisti pridedamo binarinio failo (pvz., „pip“be darbalaukio paketo). Pranešimas priklauso nuo platformos: Windows atidarykite darbalaukio programą arba paleiskite bet kurią „`chloros-cli`“ komandą; Linux sistemoje paleiskite komandą „`chloros-cli`“ (grafinės sąsajos nėra) arba įdiekite „`.deb`“. Norėdami naudoti nuotolinį backendą, perduokite „`backend_url=`“ (ir `auto_start_backend=False`).
- **`CAMERA_AVAILABLE == False`** importuojant → nepavyko įkelti `lattice_sdk`: „Arena“ SDK-vykdymo aplinka arba „OpenCV“ (`cv2`), negali būti importuota šioje Python versijoje. Backend'u valdomas paviršius (`connect_*`, projektuojamas per backend'ą, `ChlorosLocal`) vis dar veikia.
- **Masyvas jungiasi esant visam jutiklio skiriamajam gebėjimui, bet mažam kadrų dažniui**→ Pagal projektą: automatinis režimas išlaiko visą jutiklio skiriamąjį gebėjimą ir leidžia dažniui sumažėti iki to, kurį perduoda laidinis ryšys. Perduokite `binning=2` / `4` arba mažesnį `width` / `height`, jei norite gauti daugiau kadrų per sekundę, arba įjunkite „jumbo“ kadrus / atnaujinkite ryšį. Binningas ir ROI**nepadeda** išspręsti bendro per didelio užsakymo (`oversubscribed: true`, fps laukai 0): per didelio kamerų skaičiaus, palyginti su laidinio ryšio pralaidumu, problemos negalima išspręsti naudojant „binning“ ar ROI — sumažinkite kamerų skaičių, įjunkite „jumbo“ rėmelius arba pereikite prie greitesnio tinklo plokštės (žr. [Per didelis užsakymų skaičius](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` praneša, kad tinklo plokštės (NIC) priėmimo žiedas yra labai mažas (~0,26 MB) / jungčių vartai rodo pranešimą „FRAMES WILL DROP“** → Pagrindinio kompiuterio tinklo plokštės priėmimo žiedas yra nustatytas pagal numatytuosius parametrus (dažnai po tinklo plokštės tvarkyklės atnaujinimo nustatomas į 32). Naudojant „Realtek“ USB 10GbE adapterį, nustatykite `ReceiveBufferLen=256` ir `PendingReceives=64` (su padidintais įgaliojimais), tada perkraukite užkulisinę sistemą, kad ji iš naujo perskaitytų žiedą. Visa procedūra: [CLI nuoroda → Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Pagrindinis kompiuteris užstringa perkraunant ar išjungiant, vėliau atsiranda WMI klaidos `Invalid class` / tinklo plokštė neįsijungia** → Pasenęs USB 10GbE tvarkyklė sukelia klaidą `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atnaujinkite adapterio tvarkyklę iki naujausios versijos (≥ 2026) ir iš naujo pritaikykite priėmimo žiedo nustatymus. Žr. [CLI nuoroda → Pagrindinio kompiuterio NIC nustatymas ir optimizavimas](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Masyvo fiksavimas nepavyksta dėl HTTP 503 klaidos „Nėra naujos sinchronizuotos rėmelių grupės“** → Masyvas per laukimo laikotarpį nepaskelbė jokios naujos rėmelių grupės. Patikrinkite, ar `arr.status()`: `stalled: True` (arba `streaming: False`) su `actual_fps`. Jei 0 reiškia, kad fiksavimo ciklas nustojo skelbti — atjunkite ir vėl prijunkite masyvą. Jei `stalled` yra „False“, o `actual_fps` yra didesnis už 0, bandykite dar kartą. Iš karto po „`connect_array()`“ duomenų surinkimas jau laukia pirmosios grupės iki 20 s.
- **„`status()`“ rodo „`link_speed_mbps: 4294`“** → Windows negalėjo nuskaityti adapterio greičio; tai būdinga USB Ethernet adapteriams ir nėra gedimas. Žr. [Numatomi kadrų dažniai](#expected-frame-rates).
- **`update_firmware()` nepavyksta su `device never connected back on TCP <port>`** → Užkarda užblokavo „DAQ-E“ ryšį su šiuo kompiuteriu. Windows diegimo programa prideda taisyklę (žr. [„DAQ-E“ įrenginio nustatymai](#daq-e-device-settings)); jei naudojate kitą užkardą, leiskite įeinančius TCP srautus į serverį iš jutiklio potinklio.
- **Atspindžio matavimas atmestas** → Norint gauti atspindžio koeficientą absoliučia skale, veikiantis DAQ turi būti susietas su kamera (arba matrica). Susiekite per grafinę vartotojo sąsają arba naudokite `processing="radiance"` (W/m²/sr/nm), kuriam nereikia susieto jutiklio.
- **`smart=True` duomenų surinkimas trunka ilgiau nei tikėtasi** → AE konvergencija priklauso nuo scenos dinamikos. Naudojant `ArrayHandle.capture_smart()`, padidinkite `exposure_tolerance_pct`, sutrumpinkite `stability_window_s` arba sumažinkite `settle_timeout_s`, kad suveikimas būtų greitesnis ir mažiau stabilus.

---

## Taip pat žr.

- [CLI žinynas](cli-reference.md) — kiekviena CLI pakomanda atitinka SDK iškvietą.
- [DAQ šviesos jutikliai](https://mapir.gitbook.io/chloros/daq-light-sensors) Chloros vadove — jutikliai, jų ribos ir kalibruotas diapazonas, įrašymas, DAQ-E tinklas.
- [„LATTICE“ kameros](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) Chloros vadove — prijungimas, kamerų nustatymai, fiksavimo režimai, masyvai.
- Dokumentai internete: `https://mapir.gitbook.io/chloros/api-python-sdk`
