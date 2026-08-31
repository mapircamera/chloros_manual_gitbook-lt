# „Index/LUT Sandbox“

„Index/LUT Sandbox“ – tai interaktyvi darbo erdvė, esanti „Chloros Image Viewer“ šoninėje juostoje. Pasirinkite formulę, susiekite su ja savo kameros kanalus, nuspalvinkite ją gradientu ir sureguliuokite verčių diapazoną – vaizdas atsinaujina realiuoju laiku, kol tai darote. Nuo 1.2.0 versijos taip pat galite **išsaugoti tai, ką sukūrėte**, vienam vaizdui arba visam projektui, be pakartotinio apdorojimo.

## Kam skirta „Sandbox“

| „Index/LUT Sandbox“ (interaktyvi)        | Projekto apdorojimas (partija)       |
| -------------------------------------- | -------------------------------- |
| Po vieną vaizdą, greitas grįžtamasis ryšys  | Visas duomenų rinkinys per vieną apdorojimo ciklą     |
| Eksperimentinis ir iteracinis             | Iš anksto sukonfigūruoti nustatymai          |
| Renderiuoja realiuoju laiku; išsaugo tik jūsų prašymu  | Visada įrašo galutinius failus      |
| Puikiai tinka tinkamiems nustatymams rasti | Geriausia naudoti, kai nustatymai jau galutiniai |

{% hint style="success" %}
**Įprastas darbo procesas**: reguliuokite „Sandbox“ tol, kol vizualizacija atitiks jūsų norus, tada eksportuokite tiesiai iš „Sandbox“ arba nukopijuokite tuos pačius indekso ir LUT nustatymus į [Projekto nustatymus](../project-settings/project-settings.md), kad kito apdorojimo ciklo metu jie būtų pritaikyti kiekvienam vaizdui.
{% endhint %}

***

## „Sandbox“ atidarymas

1. Spustelėkite vaizdą lentelėje — jis atsidarys visame ekrane **Vaizdų peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> skirtuke
2. Spustelėkite **Vaizdo peržiūros** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> piktogramą, kad išslystų kairysis šoninis meniu, jei jis dar nėra atidarytas
3. Dešinėje viršuje esančiame sluoksnių išskleidžiamajame meniu pasirinkite daugiabandį sluoksnį — paprastai pasirenkamas **RAW (atspindžio koeficientas)**, nes pagal kalibruotą atspindžio koeficientą apskaičiuotos indeksų reikšmės yra palyginamos tarp vaizdų

Šoninėje juostoje iš viršaus į apačią rodomi:

* vaizdo pavadinimas ir jo fotoaparato modelis
* mygtukas **Eksportuoti/Išsaugoti vaizdus** — pasirodo, kai pažymėta „Index“ arba „LUT“
* žymimieji langeliai **Index**ir**LUT**
* indekso konfigūracijos skydelis
* skydelis **Kursoriaus vertės** su rodmenimis, histogramu ir GSD valdymu

{% hint style="warning" %}
**Negalima naudoti su monochromatinėmis kameromis.** Vienos juostos „LATTICE M3M“ vaizde abu žymimieji langeliai yra išjungti, o įrankio patarime nurodoma: _„Negalima naudoti su monochromatiniais (M3M) jutikliais“_ — vienoje juostoje daugiajuostis indeksas nėra apibrėžtas. Norėdami apskaičiuoti indeksus iš M3M kamerų, sujunkite dvi ar daugiau kamerų į suderintą daugiabandį vaizdų rinkinį ir naudokite „LATTICE“ indekso variklį.
{% endhint %}

***

## Indekso taikymas

1. Pažymėkite langelį **Indeksas** šoninės juostos viršuje
2. Kairėje išskleidžiamajame meniu pasirinkite savo kameros filtrą (`RGN`, `OCN`, `NGB`, `RGB`, `RE`, `NIR`)
3. Dešiniame išskleidžiamajame meniu pasirinkite indekso formulę — 27 įdiegtos formulės ir bet kokios jūsų išsaugotos pasirinktinės formulės
4. Formulė pateikiama kaip žemiau esanti matematinė išraiška, kurioje kiekvienoje juostos vietoje yra tuščias apskritimas. **Pervilkite spalvotą kanalo apskritimą į lizdą**, kad jį susietumėte
5. Kai visi formulėje naudojami lizdai bus susieti, vaizdas atsinaujins ir parodys indekso reikšmes
6. Nukreipkite žymeklį ant vaizdo, kad pamatytumėte reikšmes; skydelyje **„Žymeklio reikšmės“** po žymekliu atsiras indekso eilutė su atitinkama reikšme

Dukart spustelėkite susietą lizdą, kad jį išvalytumėte. Neužbaigta formulė yra įprasta būsena vilkimo metu, o ne klaida — vaizdas tiesiog neatsinaujina, kol formulė nėra užbaigta.

Kanalo apskritimai pažymėti spalvomis: raudona = Red, žalia = Green, mėlyna = Blue, oranžinė = Orange, žydra = Cyan, violetinė = NIR, raudonai violetinė = RE. Tos pačios spalvos naudojamos kanalų taškams ir histogramos kreivėms skydelyje „Cursor Values“ (Kursoriaus reikšmės).

### NDVI pavyzdys

```

Formula: (NIR - Red) / (NIR + Red)

For a Survey3W RGN camera:
  NIR = 850 nm band
  Red = 661 nm band

Result range:          -1.0 to +1.0
Typical vegetation:     0.4 to 0.9
Stressed vegetation:    0.2 to 0.4
Bare soil:              0.0 to 0.2
Water:                 -0.1 to 0.1
```

Išsamią formulių žinyną — visus tris iš anksto nustatytų sąrašų ir informaciją, kur kokie pavadinimai tinka — rasite [Daugiaspektrinių indeksų formulėse](../project-settings/multispectral-index-formulas.md).

### Pažymėjus „Indeksas“, bet nenaudojant LUT

Vaizdas atvaizduojamas **pilkosios skalės** spalvomis, ištemptas tarp dviejų slenksčių verčių. Tai daroma sąmoningai: indekso vaizdas yra skaliariniai duomenys, o pilkosios skalės atvaizdavimas yra tikriausias jo atvaizdavimas. Jei norite spalvų, pridėkite LUT.***

## Darbas su LUT (paieškos lentelėmis)**Paieškos lentelė** susieja indekso vertes su spalvomis: įvesties NDVI 0,65 atveju išvesties spalva yra tam tikra žalia. Ji nekeičia duomenų — ji keičia tai, kaip juos interpretuojate.

### LUT pridėjimas

1. Spustelėkite mygtuką „<img src="../.gitbook/assets/image (1) (1) (1).png" alt="" data-size="line">“ **„+ Add LUT“** po formule
2. Pasirinkite spalvų gradientą
3. Nustatykite apkarpymo minimumą ir maksimumą
4. Pasirinkite apkarpymo režimą
5. Pažymėkite **LUT** langelį šoninėje juostoje, kad jis būtų pritaikytas

LUT langelis lieka neaktyvus, kol LUT nėra faktiškai sukonfigūruotas indekse.

### Spalvų gradiento pasirinkimas

Užveskite pelę ant **gradiento juostos**, kad atidarytumėte išankstinių nustatymų sąrašą — „Chloros“ pateikia**septyni** gradiento išankstinius nustatymus:

| # | Gradientas                            | Forma                                                               |
| - | ----------------------------------- | ------------------------------------------------------------------- |
| 1 | Red → Geltona → Green (**numatytoji**)  | Išsiskirianti — atitinka įprastą supratimą apie augmeniją: žalia = sveika |
| 2 | Violetinė → Geltona → Green             | Skiriasi, su aiškiai išreikštu apatiniu galu                                  |
| 3 | Ruda → Balta → Blue                | Skiriasi aplink šviesų vidurį                                   |
| 4 | Juoda → Violetinė → Rožinė → Šviesiai geltona | Sekcinė, nuo tamsios iki šviesios                                           |
| 5 | Red → Geltona → Blue                 | Skiriasi aplink šviesų vidurį                                   |
| 6 | Violetinė → Blue → Green → Geltona      | Eilės tvarka, nuo tamsios iki šviesios                                           |
| 7 | Orange → Balta → Violetinė             | Skiriasi aplink šviesų vidurį                                   |

**Išsiskiriantis**gradientas lango viduryje pateikia neutralią spalvą, kuri gerai matoma, kai vidurinis taškas reiškia kažką konkretaus (ribą, bazinę datą).**Eilinis** gradientas monotoniškai pereina nuo tamsios iki šviesios spalvos, o tai gerai tinka kiekiui, kurį apibūdina tik „daugiau“ ir „mažiau“.

Kiekvienas iš anksto nustatytas variantas turi septynis spalvų taškus. Spustelėkite iš anksto nustatytą variantą, ir vaizdas bus iškart atnaujintas (jei pažymėta LUT langelis).

### Spalvų taškų redagavimas

Po gradiento juosta yra spalvų pavyzdžių eilė, po vieną kiekvienam taškui:

* **Pakeisti spalvą**: spustelėkite spalvų pavyzdį, kad atidarytumėte spalvų pasirinkimo langą (spalvų ratas, RGB/HSV slankikliai arba šešioliktainis kodas, pvz., `#FF0000`)
* **Pridėti perėjimą**: spustelėkite mygtuką**+** eilutės gale — bus pridėtas baltas perėjimas
* **Pašalinti perėjimą**:**dubliuokite** pavyzdį
* **Išsaugoti redaguotą gradientą**: spustelėkite išsaugojimo piktogramą šalia gradientų juostos, kad redaguotą gradientą įtrauktumėte į iš anksto nustatytų sąrašą ir galėtumėte jį pasirinkti vėl

Gradientas, kurį sukonfigūravote indekse, yra išsaugomas kartu su tuo indeksu projekto nustatymuose, todėl jis išlieka uždarius ir vėl atidarius projektą.

**Mažesnis sustojimų skaičius**sukuria aiškias zonas, kurios atrodo kaip klasifikacija;**didesnis sustojimų skaičius** sukuria sklandžius, beveik fotografinius perėjimus. Nuo trijų iki penkių sustojimų tinka prezentacijų skaidrėms ir klasifikacinėms žemėlapiams; nuo šešių iki dešimties – bendrai analizei; penkiolika ar daugiau – išsamiam tyrimui ir publikacijų iliustracijoms.

### Vertės diapazono nustatymas

Ribos reguliatorius yra **dviejų rankenėlių slankiklis**, kurio diapazonas yra nuo −1 iki +1; abiejuose galuose yra redaguojami tekstiniai laukeliai, skirti tikslioms reikšmėms įvesti, ir mygtukas**AUTO**.

* Patempkite bet kurią rankenėlę arba įveskite skaičių į laukelį ir paspauskite „Enter“
* **AUTO**nustato intervalą pagal**2-ąjį ir 98-ąjį procentilius** pagal vaizdo galiojančias indeksų vertes — tai geras pradinis taškas, kuris ignoruoja išskirtines vertes. Chloros rezultatą apvalina adaptatyviai: iki 4 skaičių po kablelio, jei intervalas labai siauras, iki 3 — jei siauras, o kitais atvejais — iki 2
* Bet koks rankinis nustatymas turi pirmenybę prieš „AUTO“, kol vėl nepaspausite „AUTO“

Pavyzdinis „NDVI“ langas:

| Tikslas                                    | Min.  | Maks. |
| --------------------------------------- | ---- | --- |
| Rodyti viską                         | −1,0 | 1,0 |
| Tik augmenija, išskyrus dirvožemį ir vandenį | 0,2  | 0,9 |
| Tik sveika augmenija                 | 0,5  | 0,9 |
| Pabrėžti stresą                        | 0,2  | 0,5 |

Siaurinant langą padidinamas kontrastas tiriamoje srityje, o visa kita išstumiama už ribų — ten, kur **apkarpymo režimas** nusprendžia, kas su tuo nutiks.***

## Apkarpymo režimai

Kai pikselio indekso vertė nepatenka į minimalios/maksimalios ribos langą, apkarpymo režimas nusprendžia, kaip jis bus atvaizduojamas.

| Išskleidžiamojo meniu pavadinimas                  | Įrašyta reikšmė      | Ribų nepasiekę pikseliai atvaizduojami kaip                                                                                                |
| ------------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Minimalus ir maksimalus** (numatyta) | `clip`            | Artimiausia gradiento galinė spalva — reikšmės, mažesnės už minimalų dydį, priskiriamos pirmajai spalvai, o reikšmės, didesnės už maksimalų dydį, priskiriamos paskutinei |
| **Skaidrus fonas**      | `transparent`     | Visiškai skaidrus (tikrasis alfa)                                                                                                  |
| **Indeksinis fonas**| `indexColor`      | Pilkosios skalės, ištemptas per**visą** vaizdo indeksų diapazoną, todėl už diapazono ribų esanti struktūra vis dar matoma pilka spalva                |
| **Originalus fonas**         | `backgroundColor` | Pats pagrindinis vaizdas, todėl spalvų sluoksnis uždedamas ant tikrosios scenos                                                |

| Režimas                       | Geriausiai tinka                               | Išvaizda                                      |
| -------------------------- | -------------------------------------- | ----------------------------------------- |
| **Minimalus ir maksimalus**      | Visų duomenų rodymas, mokslinė analizė | Kiekvienas pikselis nuspalvintas                      |
| **Skaidrus fonas** | GIS sluoksniai, vertės intervalo izoliavimas   | Spalva lango viduje, už jo – nieko |
| **Indeksinis fonas**       | Akcentavimas išlaikant duomenų kontekstą    | Spalva viduje, pilka už lango               |
| **Originalus fonas**    | Ataskaitos ir pristatymai              | Spalva viduje, nuotrauka už lango         |

{% hint style="info" %}
**Pikseliai be duomenų visada yra skaidrūs, bet kuriame režime.** Pikselis, kurio indeksas nėra baigtinis (dalyba iš 0 į 0) arba yra lygiai −1,0 arba +1,0 (saturacijos indikatoriai, kai vienoje juostoje rodoma nulinė vertė, o kitoje – ne), traktuojamas kaip neturintis duomenų, o ne kaip kraštutinė vertė. Tai leidžia išvengti peršviestų šviesių sričių ir visiškai tamsų sričių patekimo į spalvų skalę, užuot jas vaizdavus kaip ekstremaliausius kadro rodmenis. Ta pati taisyklė apibrėžia, kurie pikseliai naudojami „AUTO“ slenksčiams ir indekso histogramai, todėl visi trys rodikliai sutampa.
{% endhint %}

Skaidrumas išsaugomas, kai eksportas įrašomas kaip PNG. Jis negali būti atvaizduojamas JPG formatu.

***

## Vertės rodymas, kol atliekate nustatymus**„Cursor Values“** skydelis po konfigūracijos skydeliu yra „Sandbox“ matavimo prietaisas:

* Perkelkite žymeklį virš vaizdo ir perskaitykite kiekvieno kanalo šaltinio vertes bei indekso vertę atitinkamoje eilutėje
* Įjunkite mygtuką **INDEX** virš histogramos, kad pamatytumėte indeksų verčių pasiskirstymą kadre: dvi jūsų nustatytos ribos bus pavaizduotos kaip oranžinės punktyrinės linijos, o žymeklio vertė – kaip balta linija. Tai greičiausias būdas pasirinkti langą, kuriame iš tikrųjų yra jūsų duomenys
* Įjunkite **CURSOR**, kad pamatytumėte žymeklio linijas prie verčių po žymekliu
* Padidinkite vaizdą daugiau nei 60× (mažiau, jei nustatytas GSD bloko dydis), kad būtų paryškinti atskiri rodomi pikseliai su kintama verte

Praktinė procedūra:

1. Užsirašykite vertes virš sveikos augmenijos, streso paveiktos augmenijos, pliko dirvožemio ir vandens
2. Pažiūrėkite, kur šie klasteriai yra indekso histogramoje
3. Nustatykite mažiausią ir didžiausią vertes, kad apibrėžtumėte jus dominančią klasterio sritį
4. Pasirinkite apkarpymo režimą — _Original Background_ išlaiko matomą aplinką aplink klasterį

***

## Eksportavimas iš „Sandbox“

Viskas, kas nurodyta aukščiau, yra tiesioginis peržiūros vaizdas, kol jo neišsaugosite. Mygtukas **„Eksportuoti/Išsaugoti vaizdus“** šoninės juostos viršuje atidaro langą, kuris pasislinksta virš šoninės juostos (uždengdamas ne vaizdą, todėl vis dar galite matyti, dėl ko sprendžiate).

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>### Parinktys

| Parinktis                          | Efektas                                                                                                                                            |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Taikyti dabartiniam vaizdui**      | Išsaugo būtent tą vaizdą, kuris rodomas, su šiais nustatymais                                                                                                |
| **Taikyti visiems projekto vaizdams** | Pakartotinai taiko tą pačią konfigūraciją kiekvienam projekto vaizdui. Vaizdai, kuriuose nėra šiam indeksui reikalingų juostų, yra praleidžiami, o ne traktuojami kaip nesėkmės |
| **Indekso/LUT gradiento juosta**      | Taip pat kiekvienam eksportui išsaugo atskirą paaiškinimų paveikslėlį su pažymėtu verčių diapazonu                                                                     |
| **Indekso histograma**             | Taip pat kiekvienam eksportui išsaugo atskirą histogramos paveikslėlį, kuriame rodomos duomenų minimalios ir maksimalios reikšmės bei apribojimo ribos                                               |

Jei vaizdo skirtuke nustatytas **GSD bloko dydis** yra didesnis nei 1, prieš patvirtinant eksportą langelyje bus pateikta atitinkama informacija: eksportuojant bus išsaugotas tai, ką matote, įskaitant blokų vidurkį. Jei norite gauti visą skiriamąją gebą, pirmiausia nustatykite GSD parametrą atgal į 1.

### Kur saugomi failai

Kiekvieną kartą paspaudus **Eksportuoti**sukuriama**nauja, niekada anksčiau nenaudota aplankas**:

```
<project folder>/Sandbox_Exports/<IndexName>_<Index|LUT>_<NNN>/
```

Pavyzdžiai: `Sandbox_Exports/NDVI_LUT_001/`, o kitam vykdymui – `Sandbox_Exports/NDVI_LUT_002/`. Numeracija nustatoma nuskaitant tai, kas jau yra diske, todėl ji išlieka net po perkrovimų ir rankiniu būdu ištrintų aplankų. Nieko niekada neperrašoma — pagrindinė „Sandbox“ paskirtis yra palyginti vieną bandymą su ankstesniuoju.

Aplankėje, kiekvienam vaizdui:

| Failas                                                   | Turinys                                                   |
| ------------------------------------------------------ | ---------------------------------------------------------- |
| `<source name>_<IndexName>_<Index\|LUT>.png`           | Atvaizduotas vaizdas, pikselis po pikselio toks, koks buvo rodomas peržiūroje |
| `<source name>_<IndexName>_<Index\|LUT>_legend.png`    | Gradiento juostos papildomasis failas, jei prašoma                     |
| `<source name>_<IndexName>_<Index\|LUT>_histogram.png` | Indekso histogramos papildomasis failas, jei prašoma                  |

Abu papildomi langeliai visada įrašomi **pilna skiriamąja geba**, net jei pagrindinis vaizdas yra vidurkinamas blokais: bloko dydis atitinka ekrano skiriamąją gebą, o abu papildomi langeliai rodo tikrąsias indekso reikšmes kiekvienam pikseliui. Be to, juose pateikiama daugiau informacijos nei ekrane rodomose versijose — abu nurodo išplėtimo langą _ir_ tikruosius duomenų minimumą bei maksimumą, todėl išsaugota legenda lieka įskaitoma net po kelių mėnesių, net ir neatidarius projekto.

### Vykdymo eiga ir rezultatai

Visos projekto eksportavimas trunka keletą minučių, todėl programa pateikia ataskaitas per tiesioginį vykdymo kanalo srautą, o ne blokuoja sistemą:

* Vykdymo juosta rodo „`current / total`“ ir rašomą failą
* Baigus eksportą, langelyje parodoma, kiek vaizdų buvo eksportuota, kiek praleista, ir išvesties aplanko kelias
* Praleisti vaizdai išvardijami kartu su priežastimi (rodomi iki penkių, po to – eilutė „+N daugiau“). Dažniausiai priežastis yra sluoksnis, kuriame nėra šiam indeksui reikalingų kanalų
* Jei projekte **nė vienas** vaizdas negali naudoti šio indekso, operacija praneša apie nesėkmę, o ne palieka tuščią aplanką

Vienu metu gali vykti tik vienas eksporto procesas „sandbox“ aplinkoje. Bandymas paleisti antrą procesą, kol pirmasis dar vyksta, yra atmestas su aiškiu pranešimu, kad du procesai nesivaržytų dėl to paties projekto failo.

### Tinklelis parenka vykdymą

Kiekvienas užbaigtas vykdymas rodomas kaip atskiras mygtukas [vaizdų tinklelyje](image-grid.md) įrankių juostoje, pažymėtas `<IndexName> <Index|LUT> <NNN>`. Štai kaip galima palyginti eksportavimo operacijas: du kartus eksportuokite naudodami skirtingus gradientus arba slenksčius, tada perjunkite tarp dviejų mygtukų lentelėje.

***

## Pasirinktinės indekso formulės (Chloros+)

{% hint style="info" %}
**Kur jas kurti**: „Sandbox“ šoninėje juostoje arba**projekto nustatymuose** prieš apdorojimą. Abiem atvejais duomenys įrašomi į tą patį projekto lygio sąrašą.
{% endhint %}

1. Atidarykite pasirinktinių formulių skaičiuoklę iš indeksų formulių išskleidžiamojo meniu (reikia prisijungti su atitinkamu Chloros+ abonementu)
2. Įrašykite formulę naudodami **juostų-slotų simbolius** `x`, `y`, `z`, `a`, `b`, `c` — tai nėra juostų pavadinimai
3. Galimi operatoriai: `+`, `-`, `*`, `/`, `^` ir `()`, skirti grupavimui
4. Galimos funkcijos: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
5. Pavadinkite ir išsaugokite jį — jis pasirodys formulės išskleidžiamojo meniu apačioje, o jo lizdus susiesite vilkdami kanalų apskritimus, lygiai taip pat kaip ir su įdiegtomis išankstinėmis nustatymų kombinacijomis

```

Modified NDVI with an offset:   (y-x)/(y+x+0.5)
Simple ratio:                   y/x
Three-band difference:          (y-x)/(y+x-z)
Squared ratio:                  (y/x)^2
```

{% hint style="warning" %}
**Pasirinktinės formulės veikia tik grafinėje vartotojo sąsajoje.** Parinktis „CLI/SDK `--indices`“ išplečia 22 įdiegtų nustatymų sąrašą ir tyliai praleidžia viską kitą, įskaitant jūsų pasirinktas formules. Norėdami apdoroti pasirinktinę formulę partijos režimu, sukonfigūruokite ją projekto nustatymuose ir paleiskite apdorojimą arba naudokite „Sandbox“ eksporto funkciją „Taikyti visiems projekto vaizdams“.
{% endhint %}

***

## Problemų sprendimas

### „Šiame sluoksnyje nėra kanalų, kurių reikia šiam indeksui“

Formulė nuskaito kanalo poziciją, kurios dabartiniame sluoksnyje nėra — pavyzdžiui, trijų lizdų indeksą vieno ar dviejų kanalų faile. Perjunkite į daugiabandį sluoksnį (atspindžio arba be Bayerio filtro) arba pasirinkite indeksą, kuris atitinka jūsų fotoaparato filtrą.

### „Nepavyko prisijungti prie vaizdo apdorojimo užkulisio“

Užkulisio serveris neatsako. Patikrinkite skirtuką „Log“; jei apdorojimo modulis paleidžiamas iš naujo, „Sandbox“ atsistatys savaime, kai tik jis vėl pradės veikti.

### Vaizdas nepasikeitė, kai vilkiau apskritimą

Formulė dar nėra užbaigta. Neužbaigta formulė traktuojama kaip įprasta vilkimo būsena – niekas nerenderuojama ir niekas nepranešama kaip klaida. Užpildykite visus laukelius, kuriuos naudoja formulė.

### Visas vaizdas yra vienos spalvos

Jūsų klipo langas greičiausiai yra gerokai už duomenų ribų. Paspauskite **AUTO**, kad jis prisitaikytų prie 2-ojo/98-ojo procentilio, arba įjunkite**INDEX** histogramą, kad pamatytumėte, kur iš tikrųjų yra duomenys.

### Eksportuotos spalvos nesutampa su tomis, kurias mačiau

Jos turėtų sutapti – eksporto kelias sąmoningai atspindi tiesioginį peržiūros vaizdą, įskaitant alfa kanalą apkarpymo režimu, o blokų vidurkavimas taikomas _po_ spalvinimo, lygiai taip pat, kaip tai daro peržiūros programa. Jei jos skiriasi, patikrinkite, ar GSD bloko dydis nepasikeitė tarp peržiūros ir eksporto.

***

## Tolimesni veiksmai

* [**Vaizdo sluoksniai**](image-layers.md) — kuriam sluoksniui taikyti indeksą ir ką reiškia jo reikšmės
* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) — išsamus kursoriaus rodmenų, histogramos ir GSD valdymo aprašymas
* [**Daugiaspektrinių indeksų formulės**](../project-settings/multispectral-index-formulas.md) — kiekvienas iš anksto nustatytas parametras, kiekviename paviršiuje
* [**Projekto nustatymai**](../project-settings/project-settings.md) — nustatymų, kuriuos radote, įtraukimas į apdorojimo ciklą
