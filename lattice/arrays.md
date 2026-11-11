# Daugiakameriniai masyvai

LATTICE **masyvas**– tai dvi ar daugiau LATTICE kamerų, sujungtų į vieną sinchronizuotą vienetą. Viena kamera yra**pagrindinė**: ji siunčia aparatinį GPIO trigerio impulsą bendrai sinchronizavimo linijai (pagal numatytuosius nustatymus –**Line2**), todėl kiekviena sistema eksponuoja tą pačią akimirką. Chloros

prideda PTP laiko sinchronizavimą, tiesioginį peržiūros vaizdą (atskiri kiekvienos kameros vaizdo fragmentai arba vienas suderintas daugiabandis kompozitas) ir sinchronizuotą fiksavimą — kiekvienas fiksavimo ciklas sukuria vieną **kadrų grupę**, kurioje visos kameros turi tą patį laiko žymą ir kadro ID (fiksavimo išvestyje nurodomas kaip `fid:N`).

Matricos yra būdas, kuriuo monochromatinės (M3M) kameros generuoja augmenijos indeksus – viena kamera prisideda viena juosta, o matrica jas suderina į daugiajuostinį rinkinį. Žr. [Mono kameros ir augmenijos indeksai](mono-indices.md).

Yra trys lygiaverčiai būdai, kaip prijungti masyvą, ir visi jie naudoja tą patį „smart-prep“ srautą:

| Paviršius | Įėjimo taškas |
| --- | --- |
| GUI | Skirtukas „Kameros“ → **Prijungti masyvą** (mėlynas mygtukas) |
| CLI

| `chloros-cli lattice array-connect --serials SN1,SN2,…` (pirmasis serijos numeris = pagrindinis) |
| Python

SDK

| `connect_array(serials=[…])` → `ArraySession` (pirmasis serijos numeris = pagrindinis) |

„Smart-prep“ atlieka: tinklo galimybių patikrinimą (ICMP DF ping + GVSP patikrinimas), sinchronizavimo lygio pasirinkimą, kadrų dažnio prognozavimą pagal prašomą kadro dydį, sprendimą dėl suspaustų 12 bitų duomenų perdavimo (žr. [Suspaustas 12 bitų perdavimas](#packed-12-bit-transport)), PTP įjungimą, automatinį pikselių formato pasirinkimą kiekvienai kamerai, automatinį ekspozicijos nustatymą pagal kiekvienos kameros išsaugotą būseną ir GPIO trigerio konfigūraciją ant „Line2“.

**Pagal numatytuosius nustatymus naudojamas visas jutiklis (nauja 1.3.0 versijoje).** „Connect“ išlaiko jūsų nurodytą kadrų dydį — visą 2048×1536 jutiklį, be pikselių sujungimo ir be apkarpymo, nebent pasirinksite kitaip — ir leidžia kadrų dažniui sumažėti iki to, ką užtikrina pagrindinis kompiuteris ir tinklas. Programa niekada pati neatlieka pikselių sujungimo ar apkarpymo (versijoje 1.2.0 pikselių sujungimas automatiškai įjungiamas, kai dažnis nukrenta žemiau 1,5 fps). Pikselių sujungimas arba mažesnis ROI – jūsų sprendimas: „Connect“ dialogo lange pasirinkite vieną iš variantų, o „Projected Outcome“ parodys atitinkamą kadrų dažnį; „`array-connect`“ išspausdina greitesnius variantus su jų prognozuojamais dažniais.

{% hint style="info" %}
Kad visa tai veiktų, kameros turi būti pasiekiamos per ryšį — žr. [Kamerų prijungimas](connecting.md) dėl kamerų aptikimo, adresavimo ir kalibravimo duomenų atsisiuntimo pirmojo prisijungimo metu. Naudojant kelių kamerų sistemas, pagrindinio kompiuterio tinklo plokštės (NIC) priėmimo žiedo nustatymai yra tokie pat svarbūs kaip ir ryšio greitis; išsami simptomų ir sprendimų lentelė pateikta skyriuje [CLI

nuorodos § Pagrindinio kompiuterio NIC nustatymas ir optimizavimas](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays).
{% endhint %}

## Dialogo langas „Array Connect“

Skirtuke „Kameros“ → **„Prijungti masyvą“**atidaromas trijų žingsnių vedlys:**Pasirinkimas → Rodymo režimas → Nustatymai**.

### 1 žingsnis — Pagrindinės ir pavaldžiosios kameros pasirinkimas



<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered (an FRGB among them). Table showing Camera / Serial / IP / Master radio / Slave checkbox columns in camera order (M3C first, FRGB at the top), with the FRGB pre-selected as master and the green "Sync cable found on N cameras. Any of them can be master; the RGB camera (else the first) is pre-selected." probe banner visible above the table. -->

Dialogo langas, vos tik atsidarius, nuskaito tinklą („Tinklo nuskaitymas...“), tada tikrina sinchronizavimo kabelius („Sinchronizavimo kabelių tikrinimas...“), kad nustatytų, kurios kameros yra prijungtos prie M8 sinchronizavimo linijos. Norint sukurti kamerų grupę, reikia mažiausiai **2 kamerų**.

M8 sinchronizavimo kabeliai susilieja vienoje jungtyje, todėl **bet kuri prijungta kamera gali būti pagrindinė**. Patikrinimas iš anksto priskiria vaidmenis — RGB

(`-FRGB`) kamera tampa pagrindine, jei ji prijungta kabeliu, kitais atvejais — pirmoji prijungta kamera, o kitos prijungtos kameros tampa pavaldžiomis — ir rodo vieną iš trijų pranešimų:

| Pranešimas | Reikšmė |
| --- | --- |
| „Sinchronizavimo kabelis rastas {n} kamerose. Bet kuri iš jų gali būti pagrindinė; iš anksto pasirinkta RGB

kamera (kitu atveju – pirmoji).“ (žalia) | Zondas aptiko kameras sinchronizavimo linijoje; žymės langeliai „pagrindinė“ ir „pavaldžioji“ jau pažymėti. |
| „Pagrindinė kamera neaptikta – patikrinkite GPIO kabelį“ (oranžinė) | Nė viena kamera neaptiko paleidimo impulso; patikrinkite sinchronizavimo laidus. Vaidmenys vis dar yra iš anksto užpildyti (RGB

kamera, kitaip – pirmoji kamera sąraše, kaip pagrindinė), ir juos galite pakeisti. |
| „Nėra sinchronizavimo kabelio: {serialai}“ (oranžinė) | Prie išvardytų kamerų nėra prijungtas sinchronizavimo kabelis. |

Kameros lentelėje yra stulpeliai **„Kamera“ / „Serijos numeris“ / „IP“ / „Pagrindinė (radijas)“ / „Pavaldžioji (varnelė)“**. Eilutės išdėstytos**kamerų tvarka** — M3C (Bayer) kameros prieš M3M (mono); tarp M3C, FRGB, FRGN, FOCN, tada FNGB; M3M kameros – pagal filtro bangos ilgį, pradedant nuo mažiausio (F450 prieš F808). Šoninės juostos kamerų sąraše ir lentelės numatytame išdėstyme naudojama ta pati tvarka.

* Pasirinkite tiksliai **vieną pagrindinę**ir**vieną ar daugiau pavaldžiųjų**. Dar kartą spustelėjus esamos pagrindinės kameros radijo mygtuką, pasirinkimas ištrinamas. Kitą pagrindinę kamerą galima pasirinkti vienu spustelėjimu: naujoji pagrindinė kamera palieka pavaldžiųjų sąrašą, o ankstesnė pagrindinė kamera tampa pavaldžiąja (nebent ji neturi sinchronizavimo kabelio).
* Kamera, pažymėta kaip **„Be sinchronizavimo kabelio“**, niekada negali būti pasirinkta kaip pavaldžioji — pavaldžioji kamera be paleidimo laidų amžinai lauktų sinchronizavimo linijos signalo ir perduotų neveikiančią vaizdo transliaciją. Vietoj to prijunkite tą kamerą kaip autonominę kamerą.
* Kameros, kurios jau yra prijungtos kaip savarankiškos, *nėra* išjungiamos: prijungus prie masyvo, savarankiško veikimo sesija nutraukiama, o kamera vėl įtraukiama į masyvą.

**Toliau: Ekrano režimas →**įjungiamas, kai pasirenkamas pagrindinis įrenginys ir bent viena pavaldžioji kamera.**Pakartotinis nuskaitymas** iš naujo paleidžia paiešką ir laidų tikrinimą.

{% hint style="warning" %}
**Atšaukti** yra išjungta, kol vyksta nuskaitymas ar laidų tikrinimas — atšaukimas tikrinimo metu gali sugadinti kameros SDK

, veikiančią su „LATTICE“ kameros programine įranga. Palaukite, kol pasibaigs sukimosi indikatorius.
{% endhint %}

### 2 žingsnis — Rodymo režimas

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->



| Režimas | Ką gaunate |
| --- | --- |
| **Atskiros kameros** | Po vieną tiesioginio vaizdo plytelių kiekvienai kamerai, visos suaktyvinamos kartu, kad kadrai liktų sinchronizuoti. Kiekviena kamera išlaiko savo spalvą ir nustatymus. |
| **Sujungtos kameros** *(numatyta)* | Vienas langelis, kuriame rodomas suderintas daugiabandis NDVI

/indekso kompozitas. Kameros dalijasi masyvo spalva. |

Rodymo režimas keičia tik tiesioginio peržiūros vaizdą — vaizdo fiksavimo veikimas abiejuose režimuose yra toks pat.

### 3 žingsnis — Masyvo nustatymai ir numatomas rezultatas

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Trigger Rate / Wire Budget controls (1.3.0 has no "Pin resolution" checkbox), right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->



Patekęs į šią sceną, Chloros

paprašo serverio pateikti **rekomendaciją**ir ją taiko:**visas jutiklis — be kadrų sujungimo, be ROI apkarpymo — kai tik kadrų dažnis tai leidžia**, nepriklausomai nuo to, kokį dažnį užtikrina serveris ir ryšys. Tik tada, kai joks dažnis negali užtikrinti viso jutiklio, rekomendacija sumažina kadrą, ir tuomet pirmenybė teikiama pikselių sujungimui, o ne ROI apkarpymui, nes sujungimas išsaugo visą matymo lauką. Nuo šio momento jūs keičiate skiriamąją gebą į kadrų dažnį. Kiekvienas jūsų atliktas pakeitimas iš naujo paleidžia analizę realiuoju laiku ir atnaujina dešinėje esantį**Prognozuojamas rezultatas** skydelį.

Kairysis stulpelis — nustatymai:

| Valdymas | Pasirinkimai | Numatytasis | Pastabos |
| --- | --- | --- | --- |
| **ROI (regėjimo laukas)** | Visas (2048×1536) / Pusė (1024×768) / Ketvirtis (512×384) | Visas | Jutiklio apkarpymas: pusės ar ketvirčio apkarpymas į mažesnę sritį išlaikant natūralų pikselių žingsnį. |
| **Pikselių sujungimas** | 1× / 2× (suma 2×2) / 4× (suma 4×4) | 1× | Aparatinis pikselių sujungimas: 2×2 = visas matymo laukas už ketvirtadalį duomenų perdavimo sąnaudų; 4×4 = visas matymo laukas už 1/16. Paslėpta, jei kameros nepalaiko pikselių sujungimo. |
| **Vaizdas, perduodamas laidais** (nuskaitymas) | — | — | Po pikselių sujungimo plotis × aukštis, faktiškai perduodamas laidais, suapvalintas iki 16 kartotinių (mažiausiai 64). |
| **Sukėlimo dažnis** | 0,5–60 kadrų per sekundę, žingsnis 0,1 | tuščia = autom. | Pagrindinio įrenginio sukelimo dažnis. Palikite tuščią, kad Chloros

jį apskaičiuotų. |
| **Duomenų perdavimo srauto riba**| 20–2000 MB/s, žingsnis 10 | tuščia = autom. | Kiek pagrindinis kompiuteris iš tikrųjų gali priimti, išreikšta MB/s —**vienintelis skaičius, nuo kurio priklauso visas masyvo paskirstymas.** Automatiškai nustatoma pagal tinklo adapterį. Sumažinkite jį, jei masyvas praneša apie sugadintus rėmus: nustatyta vertė pervertina USB adapterių ir bendrų komutatorių pajėgumą. Pakeitus šį parametrą, prognozė vėl apskaičiuojama realiuoju laiku. Lauke iš pradžių rodomas pralaidumo limitas, su kuriuo buvo prijungtas paskutinis masyvas (išsaugotas su projektu), taigi kartą pakoreguotas limitas išlieka. |

Nuo 1.2.0 versijos **„Pin resolution“** žymės langelis pašalintas: „connect“ visada išlaiko jūsų pasirinktą kadrų dydį ir vietoj to sumažina kadrų dažnį.

Dešinioji kolona — **Prognozuojamas rezultatas**:

* **Sinchronizacijos lygis** — „Vienalaikis įrašymas“ (žalia), „Vienalaikis įrašymas (FTD – pakopinis siuntimas)“ (žalia), „Pakopinis įrašymas (100 ms nuokrypis)“ (gintarinė) arba „Konfigūracija per didelė“ (raudona). Chloros

pasirenka lygį: vienalaikį fiksavimą, kai laidą galima dalytis tokiu greičiu, kuriuo kameros gali dirbti (kadrų dažnis mažėja, kai mažėja kiekvienos kameros dalis), o pakopinį fiksavimą — tik tada, kai to padaryti neįmanoma; taigi masyvas prisijungia prie bet kurio tinklo tuo kadrų dažniu, kurį leidžia tas tinklas.
* **Kadrų dažnio prognozė** — kadrų dažnis, kuriuo tikimasi, kad veiks kamerų masyvas. Vienalaikio fiksavimo atveju tai yra vienas skaičius: dažnis nustatomas pirmiausia, o automatinė ekspozicija užpildo jam skirtą laikotarpį, tada padidina stiprinimą, kad prastai apšviesta scena jo nesumažintų (žr. [Kaip automatinė ekspozicija paskirsto ekspozicijos laiką ir stiprinimą](camera-settings.md)). Naudojant pakopinį fiksavimą, jis rodomas kaip intervalas („tamsus → šviesus“), nes kiekvienos kameros ekspozicija prailgina pakopą tamsioje scenoje.
* **NIC linija** — ryšio greitis ir nuolatinis pralaidumas („NIC {mbps} Mbps · nuolatinis {N} MB/s“).
* **Simuliacinio išsiuntimo srauto patikrinimas** — ar pagrindinio kompiuterio NIC žiedo pralaidumas gali priimti vieną vienalaikį srautą iš visų kamerų („Simuliacinio išsiuntimo srautas: X MB · NIC žiedo pralaidumas: Y MB ✓/✗“).
* **Laido pralaidumo patikrinimas** — pastovios būsenos bendras poreikis palyginti su susidūrimų saugiu laido riba („Laido pralaidumas: {poreikis} MB/s, kurį reikalauja {n} kamerų · riba {ceiling} MB/s ✓/✗ viršytas“).
* **„Maksimalus kamerų skaičius šiame tinkle: {n} — nustatomas pagal minimalų pralaidumą vienai kamerai, todėl grupavimas jo nepadidina.“** — rodomas, kai artėjate prie (arba viršijate) kamerų skaičiaus ribą.
* **„SU ŠIAIS NUSTATYMAIS KADRAI BUS PRARANDAMI.“**— raudonas įspėjimas su serverio nurodyta priežastimi, taip pat trukdžių sąrašas ir mėlyni**pataisymo pasiūlymai** („Kad šis masyvas tilptų tinkle“ / „Kad būtų įjungtas vienalaikis įrašymas“).**„Taikyti ir prisijungti“** mygtukas yra užblokuotas, kol nėra prognozės, o jo etiketė nurodo, kodėl jis atmetamas:

| Mygtuko užrašas | Reikšmė | Kas iš tikrųjų padeda |
| --- | --- | --- |
| „Analizuojama...“ | Analizė vis dar vyksta. | Palaukite. |
| **„„Per daug kamerų šiam tinklui“**| Tinklas negali perduoti masyvo net po vieną kamerą (bendras patikrinimas nepavyko): kamerų skaičius gerokai viršija tinklo pralaidumą arba jame jau transliuojamos kitos kameros. | Mažiau kamerų, „jumbo“ rėmeliai nuo vieno galo iki kito arba greitesnis tinklo adapteris.**Mažesnis ROI NEpadės** — žr. toliau. |
| **„Sumažinkite ROI, kad būtų galima įjungti“** | Su šiais nustatymais kadrai būtų prarandami (neišlaikytas burst/ring patikrinimas). | Sumažinkite ROI, padidinkite binningą arba sutvarkykite tinklo plokštės priėmimo žiedą. |

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->



Prisijungiant gali pasirodyti žalias **kalibravimo atsisiuntimo langas** su pažangos juosta pagal kiekvieną serijinį numerį: pirmą kartą prijungus kamerą prie kompiuterio, Chloros

per GigE iš kameros atsisiunčia ~3,8 MB gamyklinį kalibravimo paketą (maždaug 70 sekundžių vienai kamerai). Kameros, kurių duomenys yra išsaugoti talpykloje, šio skydelio nerodo; jei prijungta daugiau nei šešios kameros, jo eilutės slenka. Žr. [Kamerų prijungimas](connecting.md).

### Suspaustas 12 bitų perdavimas

12 bitų formatai (`BayerRG12`, `Mono12`) paprastai perduodami kaip 16 bitų žodžiai. **Suspaustas**perdavimas siunčia tuos pačius 12 bitų 1,5 baitų vienam pikseliui ir išpakuoja juos pagrindiniame kompiuteryje, todėl kiekvieno kadro siuntimas užtrunka 25 % mažiau laiko — esant pilnam jutiklio našumui, per sekundę perduodama maždaug trečdaliu daugiau kadrų.**Išsaugoti duomenys yra identiški.** „Array connect“ nusprendžia tai automatiškai (`auto`): suspaudžia tik tada, kai tai naudinga ir pagrindinis kompiuteris turi laisvų procesoriaus išteklių išpakavimui. Grafinėje vartotojo sąsajoje (GUI) nėra atitinkamo jungiklio; iš komandinės eilutės (CLI

) naudokite `array-connect --packed {auto,on,off}` (numatyta reikšmė – `auto`). 8 bitų ir 16 bitų formatai niekada nesuspaudžiami.

## Pralaidumas: kiek kamerų telpa

Tai, kiek kamerų gali perduoti masyvas, priklauso nuo ryšio linijos, o ne nuo Chloros

, todėl planavimo duomenys pateikiami aparatinės įrangos vadove: **[Masyvo pralaidumo planavimas](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Kaip Chloros

juos naudoja: prisijungimo dialogo lange paleidžiamas tinklo testas, prognozuojamas pasiekiamas kadrų dažnis ir pasirenkamas tinkamas lygis. Masyvas prisijungia prie bet kurio tinklo: lėtu ryšiu kiekvienai kamerai skiriama jai tenkanti laidinio ryšio dalis, o kadrų dažnis sumažėja, o ne atmetamas masyvas ar sumažinami jo kadrai. Atsisakoma tik to masyvo, kurio ryšio linija negali perduoti net vienos kameros vienu metu (**Per daug kamerų šiam tinklui**), o ne tyliai atmesti paketus — žr. aukščiau aprašytą numatomų rezultatų skydelį.

## Kai prarandami kadrai

Kamera gali nebūti paskelbtoje grupėje dėl dviejų visiškai skirtingų priežasčių,
ir joms reikia priešingų sprendimų. Chloros

skaičiuoja jas atskirai, o ne pateikia vieną
„nepilną“ skaičių, kuris nenurodo nė vienos iš jų:

| Kas įvyko | Ką tai reiškia | Kur ieškoti |
| --- | --- | --- |
| **Sugadintas**— kadras pasiekė tikslą, bet buvo struktūriškai sugadintas | GVSP paketų praradimas tinklo kelyje |**Laido pralaidumo ribos**, tinklo plokštės priėmimo žiedas, „jumbo“ kadrai, komutatorius |
| **Niekada nepasiekė**— rėmelis visai neatėjo | Kamera nesuveikė arba iš jos nieko neišėjo |**M8 sinchronizavimo kabelis**, sinchronizavimo linija, ar visi nariai yra įjungti |

Suskirstymas pervertinamas kas 10 sekundžių, kol masyvas transliuoja. Viršijus 5 %, tai
užregistruojama nurodant abu skaičius, o apie kiekvieną sugadintą buferį pranešama pirmą kartą, kai tai
įvyksta kiekvienai kamerai, tada duomenys apibendrinami kartą per minutę, kad ilga sesija išliktų įskaitoma.

**Sugadinti kadrai su nuliniu „niekada nepasiekė“ reiškia, kad suveikimas ir kabelio sinchronizacija veikia puikiai**ir kiekvienas prarastas kadras yra tinklo kelyje. Sprendimas – sumažinti**Wire Budget** ir
prisijungti iš naujo.

{% hint style="warning" %}
**Sukeltų kadrų dažnio sumažinimas nepadeda išspręsti sugadintų kadrų problemos.** Kameros paketų
perdavimo tempas nustatomas vieną kartą – prisijungimo metu. Sukeltų kadrų dažnio sumažinimas keičia, kaip dažnai įvyksta duomenų srautas,
o ne tai, kaip greitai pats srautas patenka į laidą. Išmatuotoje 4 kamerų sistemoje
5 kartus sumažinus suaktyvinimo dažnį niekas nepasikeitė, o sumažinus „Wire Budget“ nuo 240 iki
200 MB/s sumažino sugadintų kadrų skaičių toje pačioje sistemoje nuo 10,4 % iki nulio.
{% endhint %}

Veikiantis masyvas negali pats persiplanuoti — atjunkite ir vėl prijunkite, kad prisijungimo laiko
pasirinkimo funkcija galėtų veikti pagal naują pralaidumo limitą.

### USB tinklo adapteriai yra ribojami

USB Ethernet adapteris nurodo savo *Ethernet* ryšio greitį, tačiau tai, ką jis iš tikrųjų
gali išlaikyti, riboja USB magistralė ir jos tvarkyklė. USB 10GbE raktui anksčiau buvo priskiriamas
maždaug 1000 MB/s pralaidumas — skaičius, kurio niekas niekada nebuvo išmatavęs — o keturių kamerų veikimas
pagal tą fantominį rezervą sugadino 6–18 % kadrų, nors masyvas
vis dar rodė tinkamą tikslų kadrų dažnį. USB jungiamų adapterių pralaidumas dabar ribojamas iki
**200 MB/s** Windows

ir „NVIDIA Jetson“ sistemose bei **400 MB/s** Linux

x86_64 sistemose. Ši riba yra absoliuti, o ne procentinė, nes ribojantis veiksnys yra
magistralė: USB 1 GbE adapteris pasiekia apie 80 MB/s ir jam tai neturi įtakos.

Jei jūsų pagrindinis kompiuteris iš tiesų yra greitesnis nei nustatyta riba, padidinkite **Wire Budget**, kad tai atspindėtų.

## PTP laiko sinchronizavimas

Kadrų *sinchronizavimas* vyksta per aparatinės įrangos trigerį; **PTP** (IEEE 1588 PTPv2) užtikrina vienodus *laiko žymes* visuose įrenginiuose. Ji įjungiama pagal numatytuosius nustatymus prijungus masyvą:

* **Chloros

pagrindinio kompiuterio užkulisiai valdo PTP „grandmaster“**. „LATTICE“ kameros ir „DAQ-E“ šviesos jutikliai veikia kaip jo pavaldiniai 0 domene, todėl vaizdų laiko žymos ir DAQ spektrai sutampa su vienu laikrodžiu (~1 ms).
* `--no-ptp` (CLI

) išjungia šią funkciją, kai dirbama ant bandymų stalo — tuomet skirtingų kamerų laiko žymos **nėra** suderinamos.
* Patikrinkite sinchronizacijos būklę naudodami CLI

:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity, health (OK, or why its packets are not reaching the network)
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Pačiame skirtuke „Kameros“ nėra PTP indikatoriaus; ten pateikiami kiekvienos kameros sinchronizavimo parametrai: tik skaitymo režimu veikiantys **Vaidmuo**(Pagrindinė/Pavaldžioji),**Sinchronizavimo linija** ir masyvo Galimybių lygis. DAQ-E PTP būsena rodoma skirtuke „Šviesos jutikliai“ esančiuose jutiklio duomenyse.

## Masyvo peržiūra

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows in camera order (M3C before M3M, monos by wavelength), and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->

realiuoju laiku Pagrindinėje srauto srityje siūlomi du išdėstymo variantai (perjungiama viršutinėje juostoje): **tinklelinis vaizdas**(kiekviena plytelių dalis yra langelis; perkelkite, kad pakeistumėte tvarką, kai tinklelio spynelė yra atrakinta) ir**sąrašo vaizdas**(visos eilutės užima visą plotį:**sujungtas**masyvas sudaro vieną eilutę — jo sudėtinę plytelių grupę — o**atskiras**masyvas suteikia**kiekvienam nariui savo eilutę**su atskiru tiesioginiu vaizdu ir savo kalibravimo taikinio lentele; po masyvais – viena atskira kamera – aktyvioji). Slankiklis**„Feed Zoom“** keičia plytelių dydį; kai langelio plotis mažesnis nei 200 pikselių, pavadinimo ir kadrų per sekundę (fps) antraštės paslepiamos automatiškai. Kameros, sudarančios masyvą, rodomos šoninėje juostoje ir tinklelio numatytame išdėstyme kamerų tvarka (M3C prieš M3M, pirmiausia FRGB, monokromatinės pagal bangos ilgį); atrakindami tinklelį ir vilkdami vis tiek galite pakeisti tinklelio tvarką.

<!-- SCREENSHOT-NEEDED: list view of a connected SEPARATE array (1.3.0): one full-width row per member camera, each with its live feed and its own Calibration Target table below it. -->



**Atskirasis režimas** rodo po vieną plytelių kiekvienai kamerai. Kiekvienoje plytelių rodomi:

* kameros pavadinimą (viršuje kairėje),
* **kadrų per sekundę rodmenį** (viršuje dešinėje) — tai yra kameros *tikrasis kadrų ėmimo dažnis*, kurį praneša serveris, o ne peržiūros atnaujinimo dažnis (tiesioginė peržiūra apribota iki 30 kadrų per sekundę, nepriklausomai nuo kadrų ėmimo dažnio),
* būsenos taškas — žalias (transliacija) / gintarinis (įkėlimas) / raudonas (klaida),
* **pasenusio kadro sukimosi indikatorius**, kai 2 sekundes negaunamas naujas kadras — tai normalu maždaug 5 sekundes po bet kokio prisijungimo ar atjungimo, kol serveris iš naujo paskirsto duomenų srauto išteklius tarp kamerų.**Kombinuotasis režimas**rodo vieną sudėtinį langelį: serveris atlieka „debayeringą“, mastelio keitimą, suderinimą, triukšmo pašalinimą, konvertavimą į spinduliavimą pagal juostas (plius DLS atspindžio koeficientą, kai prijungtas šviesos jutiklis), įvertina masyvo indekso išraišką, taiko LUT ir transliuoja rezultatą MJPEG formatu. Kol nėra atvaizduojamas pirmasis suderintas kadras, plytelių langelis rodo savo būseną: „Rengiamas masyvas…“, „Kalibruojamas suderinimas…“, „Laukiama pirmojo kadro…“ arba — jei pasibaigia plytelių automatinio suderinimo bandymų skaičius (maždaug po minutės) — „Reikalingas suderinimas“ su mygtuku**Kalibruoti suderinimą**.

Matricos plytelių kadrų per sekundę rodmuo taip pat informuoja apie srauto būseną:**„Taikomi nustatymai…“**, kai nustatymų pakeitimas (formatas, binningas, ROI) iš naujo paleidžia kameras,**„Sustojo“**, kai matrica veikia, bet nustojo perduoti kadrų grupes, ir**„Sustabdytas“**, kai jis nustojo transliuoti — tokiu atveju iš naujo prijunkite masyvą. Kai spektrinis išskyrimas turėjo apriboti kompozicijoje rodomą juostą didelėje jos pikselių dalyje (25 % ar daugiau), plytelėje rodomas pranešimas**„Mažo patikimumo juostos duomenys · N %“**: pagal tą juostą apskaičiuotos indeksų reikšmės yra abejotinos (paprastai viena juosta gavo per mažai šviesos).

Naudingi faktai apie kombinuotąjį režimą:

* Kompozicija yra susieta su **pagrindinės**kameros kadru. AE-ROI taikymas ir taškinis matavimas kompozicijoje yra tikslūs pagrindinei kamerai ir apytiksliai – pavaldžioms kameroms; naudokite**Split View** (matricos nustatymai → „Rodyti sudėtines kameras“), kad gautumėte pikselio tikslumo plyteles pagal kiekvieną kamerą, neatidarydami papildomų kamerų jungčių.
* **„Display Layers“**(masyvo nustatymai; pagal numatytuosius išjungta) leidžia pasirinkti priešplanio ir fono sluoksnį — bet kurią kamerą arba**indeksą**. Kai priešplanas = indeksas, pikseliai už LUT min./maks. ribų rodo fono sluoksnį.
* **Renderio skiriamoji geba** (numatyta 720p) nustato tiesioginės transliacijos aukštį *ir* išsaugoto kompozicijos eksporto dydį. Kiekvienos kameros vaizdai visada eksportuojami pilna skiriamąja geba.
* Suderinimas apskaičiuojamas kiekvienos sesijos metu ir niekada neišsaugomas. Chloros

**automatiškai suderina kiekvieną masyvą po prisijungimo abiejuose rodymo režimuose**, kai tik narių automatinė ekspozicija stabilizuojasi (bandoma iki 5 minučių). Masyvo nustatymų skydelio skyriuje**„Suderinti“**rodomi RMS nuokrypiai ir mygtukas**„Pakalibruoti suderinimą“** — taip pat ir atskiriems masyvams, kurie neturi sudėtinio plytelių vaizdo.

## Įrašymas: stebėjimas ir analizė

Matricos fiksavimo paviršiai aiškiai skirstomi į **stebėjimo lygio**(įrašoma tai, ką matote) ir**analizės lygio** (įrašomi neapdoroti duomenys, kalibruojama vėliau):

| Darbo eiga | Lygis | Kas išsaugoma | GUI | CLI

|
| --- | --- | --- | --- | --- |
| **Fiksuoti**(nuotraukos) | Analizės | Viena sinchronizuota kadrų grupė per vieną praėjimą; failai pagal kamerą kiekviename pasirinktame eksporto lygyje (neapdoroti/be debayeringo/spinduliavimo/atspindžio/peržiūros/indekso) + `.daq` „sidecar“ |**„Capture All“** mygtukas + „Capture Settings“ | `lattice array-capture` |
| **Įrašyti indekso vaizdo įrašą** | Stebėjimas | Rodomas tiesioginis sujungto indekso kompozitas — 8 bitų, peržiūros skiriamoji geba, įtrauktas LUT. Iš CLI

, kai GUI langas neuždarytas, užkulisinė sistema atvaizduoja masyvo NDVI

(arba `--expr`), kai tik masyvas suderinamas; įrašymas, kurio metu nerašomas joks kadras, nepavyksta | ● Įrašyti indeksinį vaizdo įrašą (kombinuoti masyvai) | `lattice array-record` |
| **Neapdoroti serijiniai kadrai → vaizdo įrašo sukūrimas**| Analizė | Neapdoroti jutiklio kadrai esant maksimaliam įrašymo dažniui + manifestas + `.daq`, po to atliekama neprisijungus rekonstrukcija į kalibruotą spinduliavimo / atspindžio / indekso vaizdo įrašą, laiko atžvilgiu suderintą su DAQ rodmenimis | ⦿ Įrašyti neapdorotus serijinius kadrus →**Sukurti vaizdo įrašą** | `lattice array-burst` → `lattice array-build-video` |

Praktinė taisyklė: jei pikseliai bus naudojami *matavimams*, naudokite fiksavimą arba seriją (analizės kokybės); jei tiesiog reikia *peržiūrėti ar pademonstruoti*, ką užfiksavo matrica, įrašykite indeksinį vaizdo įrašą (stebėjimo kokybės).

### Įrašymo nustatymai (GUI)

<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->



Ratelis šalia **„Capture All“** atidaro langą „Capture Settings“ (reikia atidaryto projekto — įrašai į jį išsaugomi):

* **Įrašymo režimas**:**Vienkartinis**(vienas praėjimas) /**Nuolatinis**(vienas po kito; ribojamas įrašų skaičiumi, numatytasis – 1, arba trukme, numatytasis – 10 s) /**Intervalas** (laiko tarpo fotografavimas: N kadrų kas X intervalą, iš viso Y; numatyta – 1 kas 5 s per 1 minutę).
* **Eksporto tipai pagal kamerą**: „Raw“, „Debayered“, „Radiance“, „Reflectance“, „Preview“, „Index“ – numatyta, kad įjungti visi taikytini tipai. „Radiance“/„Reflectance“ yra paslėpti kameroms su RGB

filtru; **„Reflectance“ rodomas tik tada, kai kamera turi DAQ šviesos jutiklį** (savo arba paveldėtą iš masyvo); „Index“ reikalauja sukonfigūruoto indekso išraiškos.
* **Suderinta**(kiekvienam masyvui, numatyta**įjungta**): iškraipo eksportuojamus elementus pagal masyvo suderinimo profilį, kad eksportai būtų suderinti pagal pikselius. Neapdoroti duomenys visada lieka neiškreipti, tačiau transformacija įrašoma metaduomenyse. Masyvo narių failai išsaugomi pagal kiekvienos kameros natūralią orientaciją (bet kokie „Veidrodžio atspindžio“ / „Pasukimo“ nustatymai įrašomi į failą, bet netaikomi).
* **„Fastest Capture“** (perjungimas): tik „raw“ + priskirtas DAQ rodmuo + nemokamas kombinuoto indekso kompozitas, praleidžiant kalibravimo skaičiavimus fiksavimo metu, siekiant maksimalaus greičio — spinduliavimą/atspindį/indeksą vėliau atkurkite iš išsaugoto `.daq`.
* Pasirinkimai išlieka projekte. Paslėptos arba sustabdytos kameros praleidžiamos.
* **Nuolatinis** režimas masyvuose veikia Chloros

užkulisiuose (nauja funkcija versijoje 1.3.0): kai kiekviena sąraše esanti kamera priklauso masyvui, užkulisiuose pats vykdomas fiksavimo ciklas, rašant vieną grupę, kol pasirenkama kita, be atskiro užklausimo kiekvienam fiksavimui. Pilnas diskas sustabdo veikimą („Sustabdyta: diskas pilnas. Išsaugota N, F nepavyko“). Žr. [Įrašymo nustatymai ir režimai](capture.md#capture-modes), kur pateikta „Microsoft Defender“ pastaba apie didelius įrašymo dažnius.

Atitinkama CLI

(tas pats užkulisio galinis taškas, ta pati semantika):

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

TIFF

suspaudimas užfiksuotoms nuotraukoms yra `deflate` (be nuostolių, numatytasis) arba `none` — išsamios žymių lentelės, užfiksuotų nuotraukų aplanko struktūra ir pakartotinio apdorojimo taisyklės pateiktos [CLI

žinyne](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).

## DAQ šviesos jutiklio suporavimas

Atspindžio ir apšvietimo koreguotiems peržiūros vaizdams reikalingi žemyn nukreiptos šviesos duomenys iš DAQ jutiklio (prijungto skirtuke **„Šviesos jutikliai“**):

* Šoninėje juostoje esančioje **matricos eilutėje**rodomas**„DAQ · įjungta/išjungta“ mygtukas** — *įjungtas*, kai nustatytas matricos lygio šviesos jutiklis **arba** bet kuri kameros narė turi savo jutiklį; jo paaiškinime nurodoma, kuris jutiklis tiekia duomenis kuriai kamerai.
* Nustatykite visam masyvui masyvo nustatymuose → **Aplinkos šviesos jutiklis**→ išskleidžiamajame meniu**Šviesos jutiklis**. Šis pasirinkimas išlieka projekte, perduodamas kiekvienai masyvo kamerai, o atskiros kameros vis tiek gali jį pakeisti savo jutikliu.
* Po juo esanti būsenos eilutė rodo dabartinę būseną: **Išjungta**→ „Laukiama pirmojo spektro…“ →**„Veikia — visų masyvo kamerų apšvietimas pakoreguotas“** → arba, jei per paskutines 3 sekundes nebuvo gautas naujas spektras, pasirodo pranešimas apie pasenusius duomenis — toliau naudojamas paskutinis rodmuo (rodmenys niekada nepasensta įrašymo kelyje).

Paskyrus jutiklį: tampa prieinamas eksporto tipas „Atšvaitumas“, tiesioginiai peržiūros vaizdai yra koreguojami pagal apšvietimą, prognozuojamas automatinis ekspozicijos nustatymas gali naudoti spektrą, o kiekvienas atšvaitumo užfiksavimas įrašo faktiškai naudotą DAQ rodmenį kaip **`.daq` failą** į užfiksavimų aplanką `daq/` aplanke, kad fiksavimą vėliau būtų galima apdoroti iš naujo. Šalia jo esantis failas „`.daq.cameras.json`“ nurodo kameras, kurių kadrus apibūdina tas rodmuo; pakartotinai apdorodamas Chloros

suderina rodmenį tik su tų kamerų vaizdais.

## „`array-connect`“ CLI

parametrai

| Parametras | Numatytasis | Aprašymas |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automatiškai aptikti visas „LATTICE“ kameras (reikia ≥2) | **Pirmasis serijos numeris yra MASTER.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinchronizavimo linija. |
| `--target-fps F` | auto | Pagrindinio įrenginio trigerio aktyvacijos dažnis. |
| `--binning {1,2,4}` | auto | Aparatinis binningas. „Auto“ reiškia visą jutiklio duomenų srautą (be binningo), kai tik bet koks kadrų dažnis tai leidžia; „connect never“ nenaudoja binningo, kad išlaikytų aukštą dažnį. |
| `--packed {auto,on,off}` | `auto` | [Suspaustas 12 bitų perdavimas](#packed-12-bit-transport): „`auto`“ pakuoja tik tada, kai tai padeda, ir jei pagrindinis kompiuteris gali sau leisti išpakuoti. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Ekspertinis sinchronizacijos lygio pasirinkimo perrašymas. |
| `--wire-ceiling-mbps MB_PER_S` | nustatoma automatiškai | Priimančiojo kompiuterio laidinio ryšio pralaidumo riba MB/s — **Wire Budget** lauko CLI

-forma. Sumažinkite ją, jei masyvas praneša apie sugadintus rėmus. Išsaugoma kartu su projektu, todėl vėliau prisijungus ji atkuriamas. |
| `--no-recommend` | išjungta | Praleisti tinklo analizės etapą. |
| `--no-ptp` | išjungta | Išjungti PTP (tuomet skirtingų kamerų laiko žymos nebus palyginamos). |

`lattice array-list`, `array-status` ir `array-disconnect` valdo nuolatinę sesiją. Išsami subkomandų apžvalga, įskaitant suderinimą (`align-calibrate` / `align-apply`) ir tinklo įrankius, pateikta [CLI

apžvalgoje § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice); atitinkami SDK

ekvivalentai (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) pateikiami [SDK

žinyne](../reference/sdk-reference.md). Python

kalboje duomenų perdavimo biudžetas yra `connect_array(..., wire_ceiling_mbps=120)`, o gyvų duomenų sugadinimo ir niekada nepasiekusių duomenų atskyrimas pateiktas [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames).
