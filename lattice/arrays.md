# Daugiakameriniai masyvai

LATTICE **masyvas**– tai dvi ar daugiau LATTICE kamerų, sujungtų į vieną sinchronizuotą vienetą. Viena kamera yra**pagrindinė**: ji siunčia aparatinės GPIO trigerio impulsą bendra sinchronizavimo linija (numatyta**Line2**), todėl kiekviena masyvo kamera užfiksuoja tą patį momentą. Chloros suteikia PTP laiko sinchronizavimą, tiesioginį peržiūros vaizdą (atskiri kiekvienos kameros vaizdo fragmentai arba vienas suderintas daugiabandis kompozicinis vaizdas) ir sinchronizuotą fiksavimą — kiekvienas fiksavimo ciklas sukuria vieną**kadrų grupę**, kurioje visos kameros turi tą patį laiko žymą ir kadro ID (fiksavimo išvestyje nurodytas kaip `fid:N`).

Mono (M3M) kameros augmenijos indeksus generuoja naudodamos masyvus – viena kamera prisideda viena juosta, o masyvas jas suderina į daugiajuostę seką. Žr. [Mono kameros ir augmenijos indeksai](mono-indices.md).

Yra trys lygiaverčiai būdai, kaip prijungti masyvą, ir visi jie vykdo tą patį „smart-prep“ procesą:

| Paviršius | Įėjimo taškas |
| --- | --- |
| Vartotojo sąsaja | Skirtukas „Kameros“ → **Prijungti masyvą** (mėlynas mygtukas) |
| CLI | `chloros-cli lattice array-connect --serials SN1,SN2,…` (pirmasis serijos numeris = pagrindinė kamera) |
| Python SDK | `connect_array(serials=[…])` → `ArraySession` (pirmasis serijos numeris = pagrindinis) |

„Smart-prep“ atlieka šias operacijas eilės tvarka: tinklo galimybių patikrinimą (ICMP DF ping + GVSP patikrinimas), sinchronizavimo lygmens pasirinkimą, automatinį rėmelio dydžio sumažinimą, kad tilptų į laidą, PTP įjungimą, automatinį pikselių formato parinkimą kiekvienai kamerai, automatinį ekspozicijos nustatymą pagal kiekvienos kameros išsaugotą būseną ir GPIO trigerio konfigūraciją „Line2“.

{% hint style="info" %}
Kad visa tai veiktų, kameros turi būti pasiekiamos per ryšį — žr. [Kamerų prijungimas](connecting.md), kur rasite informaciją apie aptikimą, adresavimą ir kalibravimo duomenų atsisiuntimą pirmojo prisijungimo metu. Daugiakamerinėse sistemose pagrindinio kompiuterio tinklo plokštės (NIC) priėmimo žiedo nustatymai yra tokie pat svarbūs kaip ir ryšio linijos greitis; išsami simptomų ir sprendimų lentelė pateikta [CLI Nuoroda § Pagrindinio kompiuterio tinklo plokštės nustatymas ir optimizavimas](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays).
{% endhint %}

## Dialogo langas „Array Connect“

Skirtuke „Kameros“ → **„Prijungti masyvą“**atidaromas trijų žingsnių vedlys:**Pasirinkimas → Rodymo režimas → Nustatymai**.

### 1 žingsnis — Pagrindinės ir pavaldžiosios

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered. Table showing Camera / Serial / IP / Master radio / Slave checkbox columns, with the green "GPIO master detected — selections pre-populated" probe banner visible above the table. -->

kameros pasirinkimas Atidarius dialogą, jis iškart nuskaito tinklą („Tinklo nuskaitymas...“), tada tikrina GPIO trigerių laidus („GPIO laidų tikrinimas...“). Norint sukurti masyvą, reikia mažiausiai **2 kamerų**.

Jei įmanoma, laidų tikrinimas iš anksto užpildo vaidmenų pasirinkimą ir rodo vieną iš trijų pranešimų:

| Pranešimas | Reikšmė |
| --- | --- |
| „Aptikta GPIO pagrindinė kamera — pasirinkimai užpildyti iš anksto“ (žalia) | Tikrinimas nustatė trigerio topologiją; pagrindinio radijo ir pavaldinių žymės langeliai jau pažymėti. |
| „Pagrindinis įrenginys neaptiktas – patikrinkite GPIO kabelį“ (oranžinis) | Nė viena kamera neužfiksavo trigerio impulso; patikrinkite sinchronizavimo laidus. Vis dar galite pasirinkti vaidmenis rankiniu būdu. |
| „Nėra sinchronizavimo kabelio: {serialai}“ (oranžinė) | Prie išvardytų kamerų nėra prijungtas sinchronizavimo kabelis. |

Kamerų lentelėje yra stulpeliai **„Kamera“ / „Serijinis numeris“ / „IP“ / „Pagrindinė (radijas)“ / „Pavaldžioji (žymės langelis)“**:

* Pasirinkite tiksliai **vieną pagrindinę kamerą**ir**vieną ar daugiau pavaldžių kamerų**. Dar kartą spustelėjus esamos pagrindinės kameros radijo mygtuką, pažymėjimas bus išvalytas.
* Kamera, pažymėta kaip **„Nėra sinchronizavimo kabelio“**, niekada negali būti pasirinkta kaip pavaldžioji kamera — pavaldžioji kamera be sužadinimo laidų amžinai lauktų sinchronizavimo linijoje ir transliuotų neveikiančią medžiagą. Vietoj to prijunkite tą kamerą kaip atskirą kamerą.
* Kameros, kurios jau yra prijungtos kaip atskiros, *nėra* išjungiamos: prijungimas prie masyvo atleidžia atskirą sesiją ir vėl atidaro kamerą masyvo viduje.

**Toliau: Ekrano režimas →**įjungiamas, kai pasirenkamas pagrindinis įrenginys ir bent viena pavaldžioji kamera.**Pakartotinis nuskaitymas** iš naujo paleidžia įrenginių paiešką ir laidų tikrinimą.

{% hint style="warning" %}
**Atšaukti** yra išjungta, kol vyksta nuskaitymas arba laidų tikrinimas — atšaukimas tikrinimo viduryje gali sugadinti kamerą SDK su „LATTICE“ kameros programine įranga. Palaukite, kol baigsis sukasi ratukas.
{% endhint %}

### 2 žingsnis — Rodymo režimas

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->

| Režimas | Ką gaunate |
| --- | --- |
| **Atskiros kameros** | Po vieną tiesioginio vaizdo plytelių kiekvienai kamerai, visos suaktyvinamos kartu, kad kadrai būtų sinchronizuoti. Kiekviena kamera išlaiko savo spalvą ir nustatymus. |
| **Sujungtos kameros** *(numatyta)* | Vienas langelis, kuriame rodomas suderintas daugiabandis NDVI/index kompozitas. Kameros naudoja bendrą masyvo spalvą. |

Rodymo režimas keičia tik tiesioginio peržiūros vaizdą — vaizdo fiksavimo veikimas abiejuose režimuose yra toks pat.

### 3 žingsnis — masyvo nustatymai ir numatomas rezultatas

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Pin resolution / Trigger Rate controls, right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->

Įėjus į šią sceną, Chloros paprašo serverio pateikti **rekomendaciją**ir automatiškai taiko ROI + binningo derinį, kuris tinka jūsų NIC imtuvo žiedui (jis teikia pirmenybę binningui, o ne ROI apkarpymui, nes binningas išsaugo visą matymo lauką). Kiekvienas jūsų atliktas pakeitimas iš naujo paleidžia analizę realiuoju laiku ir atnaujina dešinėje esantį**Numatomų rezultatų** skydelį.

Kairysis stulpelis — nustatymai:

| Valdymas | Pasirinkimai | Numatytasis | Pastabos |
| --- | --- | --- | --- |
| **ROI (regėjimo laukas)** | Visas (2048×1536) / Pusė (1024×768) / Ketvirtis (512×384) | Visas | Jutiklio apkarpymas: pusės/ketvirčio apkarpymas į mažesnę sritį išlaikant natūralų pikselių žingsnį. |
| **Pikselių sujungimas** | 1× / 2× (suma 2×2) / 4× (suma 4×4) | 1× | Aparatinis pikselių sujungimas: 2×2 = visas matymo laukas už ketvirtadalį laidų sąnaudų; 4×4 = visas matymo laukas už 1/16 kainos. Paslėpta, jei kameros nepalaiko pikselių sujungimo. |
| **Vaizdas, perduodamas laidais** (nuskaitymas) | — | — | Po pikselių sujungimo faktinis plotis×aukštis, iš tikrųjų siunčiamas laidais, suapvalintas iki 16 kartotinių (mažiausiai 64). |
| **Kontaktų skiriamoji geba**| žymės langelis | išjungta | Chloros paprastai automatiškai padidina pikselių sujungimą prisijungus, kai numatomas perdavimo greitis nukrenta žemiau**1,5 fps**. Fiksavimas išlaiko jūsų pasirinktą kadro dydį ir priima mažesnį dažnį — taip paverčia per didelės apkrovos konfigūraciją į griežtą prisijungimo atmetimą, o ne į automatinį dažnio mažinimą. |
| **Sukėlimo dažnis** | 0,5–60 kadrų per sekundę, žingsnis 0,1 | tuščia = autom. | Pagrindinio įrenginio sukelimo dažnis. Palikite tuščią, kad Chloros jį nustatytų pats. |
| **Pralaidumo biudžetas**| 20–2000 MB/s, žingsnis 10 | tuščia = autom. | Kiek pagrindinis kompiuteris iš tikrųjų gali priimti, išreikšta MB/s —**vienintelis skaičius, nuo kurio priklauso visas masyvo paskirstymas.** Automatiškai nustatoma pagal tinklo adapterį. Sumažinkite šią reikšmę, jei masyvas praneša apie sugadintus kadrus: nustatyta reikšmė pervertina USB adapterių ir bendrų komutatorių pajėgumą. Pakeitus šią reikšmę, prognozė bus iš naujo apskaičiuojama realiuoju laiku. |

Dešinioji skiltis — **Prognozuojamas rezultatas**:

* **Sinchronizacijos lygis** — „Vienalaikis fiksavimas“ (žalia), „Vienalaikis fiksavimas (FTD – pakopinis siuntimas)“ (žalia), „Pakopinis fiksavimas (100 ms nuokrypis)“ (gintarinė) arba „Konfigūracija per didelė“ (raudona).
* **Kadrų per sekundę prognozė** — rodoma kaip intervalas („silpnas → ryškus“), nes sinchronizuoto masyvo dažnis priklauso nuo lėčiausios kameros ekspozicijos.
* **NIC linija** — ryšio greitis ir nuolatinis pralaidumas („NIC {mbps} Mbps · nuolatinis {N} MB/s“).
* **Sim-emit burst patikrinimas** — ar pagrindinio kompiuterio NIC gali priimti vieną vienalaikį duomenų srautą iš visų kamerų („Sim-emit burst: X MB · NIC žiedo talpa: Y MB ✓/✗“).
* **Laido pralaidumo patikrinimas** — pastovios būsenos bendras poreikis palyginti su susidūrimų saugiu laido pralaidumo limitu („Laido pralaidumas: {poreikis} MB/s, kurio reikalauja {n} kamerų · limitas {limitas} MB/s ✓/✗ viršytas“).
* **„Maksimalus kamerų skaičius šiame tinkle: {n} — nustatomas pagal minimalų pralaidumą vienai kamerai, todėl grupavimas jo nepadidina.“** — rodomas, kai artėjate prie (arba viršijate) kamerų skaičiaus ribą.
* **„Su šiais nustatymais bus prarandami kadrai.“**— raudonas įspėjimas su serverio pateikta priežastimi, taip pat trukdžių sąrašu ir mėlynais**sprendimų pasiūlymais** („Kad šis masyvas tilptų tinkle“ / „Kad būtų įjungtas vienalaikis įrašymas“).**„Taikyti ir prisijungti“** yra užblokuota, kol nėra prognozės, o jos etiketė nurodo, kodėl funkcija atmetama:

| Mygtuko etiketė | Reikšmė | Kas iš tikrųjų padeda |
| --- | --- | --- |
| „Analizuojama...“ | Analizė vis dar vyksta. | Palaukite. |
| **„Per daug kamerų šiam tinklui“**| Kamerų masyvas per daug apkrauna laidą (bendras patikrinimas nepavyko). | Mažiau kamerų, „jumbo“ rėmeliai nuo vieno galo iki kito arba greitesnė tinklo plokštė.**Mažesnis ROI nepadės** — žr. toliau. |
| **„Sumažinkite ROI, kad būtų galima įjungti“** | Su šiais nustatymais rėmeliai būtų prarandami (neišlaikytas serijos/žiedo patikrinimas). | Sumažinkite ROI, padidinkite binningą arba sutvarkykite tinklo plokštės priėmimo žiedą. |

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->

Prisijungiant gali pasirodyti žalias **kalibravimo atsisiuntimo langas** su pažangos juosta, rodančia kiekvieną serijos numerį: pirmą kartą prijungus kamerą prie kompiuterio, „Chloros“ per GigE iš kameros atsisiunčia apie 3,8 MB gamyklinį kalibravimo paketą (maždaug 70 sekundžių vienai kamerai). Į talpyklą įrašytos kameros šio lango nerodo. Žr. [Kamerų prijungimas](connecting.md).

## Pralaidumas: kiek kamerų telpa

Tai, kiek kamerų gali aptarnauti masyvas, priklauso nuo laidinio ryšio, o ne nuo Chloros, todėl planavimo duomenys pateikiami aparatinės įrangos vadove: **[Masyvo pralaidumo planavimas](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Kaip „Chloros“ juos naudoja: prisijungimo dialogo lange paleidžiamas tinklo patikrinimas, prognozuojamas pasiekiamas kadrų dažnis ir pasirenkamas tinkamas lygis. Jei masyvas per daug apkrauna laidą, jis atsisako prisijungti, o ne tyliai praleidžia paketus — žr. aukščiau aprašytą prognozuojamų rezultatų skydelį.

## Kai dingsta rėmeliai

Kamera gali nebūti paskelbtoje grupėje dėl dviejų visiškai skirtingų priežasčių,
ir joms reikia priešingų sprendimų. Chloros skaičiuoja jas atskirai, o ne pateikia vieną
„nepilną“ skaičių, kuris nenurodo nė vienos iš jų:

| Kas įvyko | Ką tai reiškia | Kur ieškoti |
| --- | --- | --- |
| **Sugadintas**— kadras pasiekė tikslą, bet buvo struktūriškai sugadintas | GVSP paketų praradimas tinklo kelyje |**Laido pralaidumo ribos**, tinklo plokštės priėmimo žiedas, „jumbo“ kadrai, komutatorius |
| **Niekada nepasiekė**— rėmelis iš viso neatėjo | Kamera nesuveikė arba iš jos nieko neišėjo |**M8 sinchronizavimo kabelis**, sinchronizavimo linija, ar visi įrenginiai įjungti |

Suskirstymas pervertinamas kas 10 sekundžių, kol masyvas transliuoja. Viršijus 5 %, tai
užregistruojama nurodant abu skaičius, o apie kiekvieną sugadintą buferį pranešama pirmą kartą, kai tai
įvyksta kiekvienai kamerai, tada duomenys apibendrinami kartą per minutę, kad ilga sesija išliktų įskaitoma.

**Sugadinti kadrai, kurių skaičius lygus nuliui (niekada negautų kadrų), reiškia, kad suveikimas ir kabelio sinchronizacija veikia puikiai**ir kiekvienas prarastas kadras yra tinklo kelyje. Sprendimas – sumažinti**Wire Budget** ir
iš naujo prisijungti.

{% hint style="warning" %}
**Sukeltuvo dažnio sumažinimas nepadeda išspręsti sugadintų kadrų problemos.** Kameros paketų
perdavimo tempas nustatomas vieną kartą, prisijungimo metu. Sukeltuvo dažnio sumažinimas keičia, kaip dažnai įvyksta duomenų srautas,
o ne tai, kaip greitai pats srautas patenka į laidą. Išmatuotoje 4 kamerų sistemoje
5 kartų sumažinus suaktyvinimo dažnį niekas nepasikeitė, o sumažinus „Wire Budget“ nuo 240 iki
200 MB/s, toje pačioje sistemoje sugadintų kadrų procentas sumažėjo nuo 10,4 % iki nulio.
{% endhint %}

Veikiantis masyvas negali pats persiplanuoti – atjunkite ir vėl prijunkite, kad prisijungimo laiko
pasirinkėjas galėtų veikti pagal naują pralaidumo limitą.

### USB tinklo adapterių pralaidumas ribojamas iki 200 MB/s

USB Ethernet adapteris nurodo savo *Ethernet* ryšio greitį, tačiau tai, ką jis iš tikrųjų
gali išlaikyti, riboja USB magistralė ir jos tvarkyklė. USB 10GbE raktui anksčiau buvo priskiriamas
maždaug 1000 MB/s pralaidumas — skaičius, kurio niekas niekada nebuvo išmatavęs — ir keturių kamerų veikimas
pagal tą fantominį rezervą sugadino 6–18 % kadrų, nors masyvas
vis dar rodė tinkamą tikslų kadrų dažnį. USB jungiamų adapterių greitis dabar ribojamas iki
**200 MB/s**. Ši riba yra absoliuti, o ne procentinė, nes ribojantis veiksnys yra
magistralė: USB 1 GbE adapteris pasiekia apie 80 MB/s ir jam tai neturi įtakos.

Jei jūsų kompiuteris iš tiesų yra greitesnis nei nustatyta riba, padidinkite **Wire Budget**, kad tai atspindėtų.

## PTP laiko sinchronizavimas

Kadrų *sinchronizavimas* vyksta per aparatinės įrangos trigerį; **PTP** (IEEE 1588 PTPv2) užtikrina vienodus *laiko žymes* visuose įrenginiuose. Jis įjungiamas pagal numatytuosius nustatymus, kai masyvas prijungiamas:

* **Chloros pagrindinio kompiuterio užkulisiai valdo PTP „grandmaster“**. „LATTICE“ kameros ir „DAQ-E“ šviesos jutikliai veikia kaip jo pavaldiniai 0 domeno ribose, todėl vaizdų laiko žymos ir DAQ spektrai sutampa su vienu laikrodžiu (~1 ms).
* `--no-ptp` (CLI) išjungia šią funkciją bandymams ant stalo — tuomet skirtingų kamerų laiko žymos **nėra** suderinamos.
* Sinchronizacijos būklę patikrinkite naudodami „CLI“:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Pačiame skirtuke „Kameros“ nėra PTP indikatoriaus; čia pateikiami kiekvienos kameros sinchronizavimo duomenys: tik skaitymo režimu veikiantys laukeliai **Vaidmuo**(Pagrindinė/Pavaldžioji),**Sinchronizavimo linija**ir masyvo**Galimybės** lygis. DAQ-E PTP būsena rodoma skirtuke „Šviesos jutikliai“ esančiuose jutiklio duomenyse.

## Masyvo vaizdas

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows, and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->

realiuoju laiku Pagrindinėje transliacijos srityje siūlomi du išdėstymo variantai (perjungti galima viršutinėje juostoje): **tinklelinis vaizdas**(kiekvienas langelis yra ląstelė; perkelkite, kad pakeistumėte tvarką, kai tinklelio spynelė yra atrakinta) ir**sąrašo vaizdas**(masyvai užima visą plotį viršuje, o po jais – viena aktyvi kamera).**Transliacijos mastelio** slankiklis keičia plytelių dydį; kai langelio plotis yra mažesnis nei 200 pikselių, pavadinimo ir kadrų per sekundę (fps) antraštės paslepiamos automatiškai.**Atskirasis režimas** rodo po vieną plytelių kiekvienai kamerai. Kiekvienoje plytelių rodomi:

* kameros pavadinimą (viršuje kairėje),
* **kadrų per sekundę rodmenį** (viršuje dešinėje) — tai yra kameros *tikrasis kadrų ėmimo dažnis*, kurį praneša serveris, o ne peržiūros atnaujinimo dažnis (tiesioginė peržiūra apribota iki 30 kadrų per sekundę, nepriklausomai nuo kadrų ėmimo dažnio),
* būsenos taškas — žalias (transliacija) / gintarinis (įkėlimas) / raudonas (klaida),
* **seno kadro sukimosi indikatorius**, kai 2 sekundes negaunamas naujas kadras — tai normalu maždaug 5 sekundes po bet kokio prisijungimo ar atjungimo, kol serveris iš naujo paskirsto duomenų srauto išteklius tarp kamerų.**Kombinuotas režimas**rodo vieną sudėtinį langelį: serveris atlieka debayeringą, mastelio keitimą, suderinimą, triukšmo pašalinimą, konvertavimą į spinduliavimą pagal juostas (plius DLS atspindžio koeficientą, kai prijungtas šviesos jutiklis), įvertina masyvo indekso išraišką, taiko LUT ir transliuoja rezultatą kaip MJPEG. Kol nebus atvaizduotas pirmasis suderintas kadras, plytelių langelis rodo savo būseną: „Rengiamas masyvas…“, „Kalibruojamas suderinimas…“, „Laukiama pirmojo kadro…“ arba — jei išeikvotas automatinio suderinimo pakartojimų limitas (~30 s) — „Reikalingas suderinimas“ su mygtuku**Kalibruoti suderinimą**.

Naudingi faktai apie kombinuotąjį režimą:

* Sudėtinis vaizdas susiejamas su **pagrindinės**kameros kadru. AE-ROI taikymas ir taškinis matavimas sudėtiniame vaizde yra tikslūs pagrindinei kamerai ir apytiksliai – pagalbinėms; naudokite**Split View** (matricos nustatymai → „Rodyti sudėtines kameras“), kad gautumėte pikselio tikslumo kiekvienos kameros plyteles, neatidarydami papildomų kameros jungčių.
* **Sluoksnių rodymas**(masyvo nustatymai; pagal numatytuosius išjungta) leidžia pasirinkti priešakinį ir foninį sluoksnį – bet kurią sudėtinę kamerą arba**Indeksą**. Kai priešakinis sluoksnis = Indeksas, pikseliai už LUT minimalios/maksimalios ribos rodo foninį sluoksnį.
* **Renderio skiriamoji geba** (numatyta reikšmė – 720p) nustato tiesioginės transliacijos aukštį *ir* išsaugoto kompozicijos eksporto dydį. Kiekvienos kameros vaizdai visada eksportuojami pilna skiriamąja geba.
* Suderinimas apskaičiuojamas kiekvienai sesijai ir niekada neišsaugomas — RMS nuokrypius ir mygtuką „Recalibrate“ (Pakalibruoti iš naujo) rasite masyvo nustatymų skydelio skyriuje, skirtame suderinimui.

## Įrašymas: stebėjimas ir analizė

Matricos fiksavimo paviršiai aiškiai skirstomi į **stebėjimo lygio**(įrašoma tai, ką matote) ir**analizės lygio** (įrašomi neapdoroti duomenys, kalibruojama vėliau):

| Darbo eiga | Lygis | Kas išsaugoma | Vartotojo sąsaja | CLI |
| --- | --- | --- | --- | --- |
| **Fiksuoti**(nuotraukos) | Analizės | Viena sinchronizuota kadrų grupė per vieną praėjimą; failai pagal kiekvieną kamerą kiekviename pasirinktame eksporto lygyje (neapdoroti/be debayeringo/spinduliavimo/atspindžio/peržiūros/indekso) + `.daq` „sidecar“ | Mygtukas „**Užfiksuoti viską**“ + Užfiksavimo nustatymai | `lattice array-capture` |
| **Įrašyti indeksinį vaizdo įrašą** | Stebėjimas | Rodomas tiesioginis sujungtų indeksų kompozitas — 8 bitų, peržiūros skiriamoji geba, įterptas LUT; būtina, kad būtų atidarytas tiesioginis srautas | ● Įrašyti indeksinį vaizdo įrašą (sujungti masyvai) | `lattice array-record` |
| **Neapdorotų kadrų serija → vaizdo įrašo sukūrimas**| Analizė | Neapdoroti jutiklio kadrai pilnu įrašymo greičiu + manifestas + `.daq`, po to – neprisijungus atliekama rekonstrukcija į kalibruotą spinduliavimo / atspindžio / indeksinį vaizdo įrašą, laiko atžvilgiu suderintą su DAQ rodmenimis | ⦿ Įrašyti neapdorotų kadrų seriją →**Sukurti vaizdo įrašą** | `lattice array-burst` → `lattice array-build-video` |

Praktinė taisyklė: jei pikseliai bus naudojami *matavimams*, naudokite fiksavimą arba seriją (analizės kokybės); jei tiesiog reikia *peržiūrėti arba pademonstruoti*, ką užfiksavo matrica, įrašykite indeksuotą vaizdo įrašą (stebėjimo kokybės).

### Įrašymo nustatymai (GUI)

<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->

Ratelis šalia **„Capture All“** atidaro langą „Capture Settings“ (reikia atidaryto projekto — įrašai į jį išsaugomi):

* **Įrašymo režimas**:**Vienkartinis**(vienas praėjimas) /**Nuolatinis**(paeiliui; ribojamas užfiksavimų skaičiumi, numatytasis – 1, arba trukme, numatytasis – 10 s) /**Intervalas** (laiko tarpas: N įrašai kas X intervalą, iš viso Y; numatyta reikšmė – 1 kas 5 s per 1 minutę).
* **Eksporto tipai pagal kamerą**: „Raw“, „Debayered“, „Radiance“, „Reflectance“, „Preview“, „Index“ – numatyta, kad visi tinkami tipai yra įjungti. „Radiance“/„Reflectance“ yra paslėpti kameroms RGB-filter;**„Reflectance“ rodomas tik tada, kai kamera turi DAQ šviesos jutiklį** (savo arba paveldėtą iš matricos); „Index“ reikalauja sukonfigūruotos indekso išraiškos.
* **Suderinta**(pagal masyvą, numatytasis nustatymas**įjungta**): iškraipo eksportuojamus elementus pagal masyvo suderinimo profilį, kad eksportai būtų suderinti pikselių lygiu. „Raw“ visada lieka nesuderintas, bet transformacija nurodoma metaduomenyse.
* **Greičiausias įrašymas** (perjungimas): tik „raw“ duomenys + priskirtas DAQ rodmuo + nemokamas sujungto indekso kompozitas, praleidžiant kalibravimo skaičiavimus įrašymo metu, siekiant maksimalaus greičio — spinduliavimą/atspindį/indeksą vėliau atkurti iš išsaugoto `.daq`.
* Pasirinkimai išlieka projekte. Paslėptos arba sustabdytos kameros praleidžiamos.

Atitikmuo CLI (tas pats užkulisio galinis taškas, ta pati semantika):

```bash
# One synced group, every applicable export level per camera (the default)
chloros-cli lattice array-capture -o output/

# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# 30-second monitoring clip of the combined index view, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# 5-second analysis-grade raw burst, then build the combined index video
chloros-cli lattice array-burst --duration 5 --build --products combined:index --fps 10 -o capture/
```

TIFF vaizdų įrašymo suspaudimas yra `deflate` (be nuostolių, numatytasis) arba `none` — pilnos žymių lentelės, įrašų aplanko struktūra ir pakartotinio apdorojimo taisyklės pateiktos [CLI nuorodoje](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).

## DAQ šviesos jutiklio suporavimas

Atspindžio ir apšvietimo koreguotiems peržiūros vaizdams reikalingi žemyn nukreiptos šviesos duomenys iš DAQ jutiklio (prijungto skirtuke **Šviesos jutikliai**):

* Šoninėje juostoje esančioje **masyvo eilutėje**rodomas mygtukas**„DAQ · įjungta/išjungta“ mygtuką** — *įjungta*, kai nustatytas masyvo lygio šviesos jutiklis **arba** bet kuri masyvo kamera turi savo jutiklį; jo paaiškinime nurodyta, kuris jutiklis tiekia duomenis kuriai kamerai.
* Priskirkite visam masyvui masyvo nustatymuose → **Aplinkos šviesos jutiklis**→**Šviesos jutiklis** išskleidžiamajame meniu. Šis pasirinkimas išlieka visą projekto laiką, taikomas visoms masyvo kameroms, tačiau atskirų kamerų nustatymai vis tiek gali jį pakeisti savo jutiklio nustatymais.
* Po juo esanti būsenos eilutė rodo dabartinę būseną: **Išjungta**→ „Laukiama pirmojo spektro…“ →**„Veikia — visų masyvo kamerų apšvietimas pakoreguotas“** → arba, jei per paskutines 3 sekundes nebuvo gautas naujas spektras, pasirodo pranešimas apie pasenusius duomenis — toliau naudojamas paskutinis matavimas (matavimai niekada nepasensta fiksavimo kelyje).

Paskyrus jutiklį: tampa prieinamas eksporto tipas „Atšvaitumas“, tiesioginiai peržiūros vaizdai yra koreguojami pagal apšvietimą, prognozuojamas automatinis ekspozicijos nustatymas gali naudoti spektrą, o kiekvienas atšvaitumo užfiksavimas įrašo DAQ rodmenį, kuris iš tikrųjų buvo panaudotas kaip **`.daq` priedas** šalia vaizdo, kad fiksavimą vėliau būtų galima apdoroti iš naujo.

## `array-connect` CLI parinktys

| Žymė | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automatiškai aptikti visas „LATTICE“ kameras (reikia ≥2) | **Pirmasis serijinis numeris yra MASTER.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinchronizavimo linija. |
| `--target-fps F` | autom. | Pagrindinio įrenginio trigerio aktyvacijos dažnis. |
| `--binning {1,2,4}` | autom. | Aparatinis binningas. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Sinchronizavimo lygio pasirinkimo perrašymas ekspertiniu režimu. |
| `--wire-ceiling-mbps MB_PER_S` | nustatoma automatiškai | Pagrindinio kompiuterio duomenų perdavimo srauto riba MB/s — lauko **Laido pralaidumo limitas** forma CLI. Sumažinkite jį, jei masyvas praneša apie sugadintus rėmus. Išsaugoma su projektu, todėl vėliau prisijungus ji bus atkurta. |
| `--no-recommend` | išjungta | Praleisti tinklo analizės etapą. |
| `--no-ptp` | išjungta | Išjungti PTP (tuomet kamerų laiko žymos nebus palyginamos). |

`lattice array-list`, `array-status` ir `array-disconnect` valdo nuolatinę sesiją. Išsami subkomandų apžvalga, įskaitant suderinimą (`align-calibrate` / `align-apply`) ir tinklo įrankius, pateikta [CLI nuorodoje § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice); o SDK ekvivalentai (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) yra [SDK nuorodoje](../reference/sdk-reference.md). Nuo Python laidų biudžetas yra `connect_array(..., wire_ceiling_mbps=120)`, o veikusių, sugadintų ir niekada nepasiekusių laidų pasiskirstymas yra [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames).
