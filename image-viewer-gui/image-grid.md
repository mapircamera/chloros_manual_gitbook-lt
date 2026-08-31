# Vaizdų tinklelis

Į projektą importavus vaizdus, pagrindiniame lange juos matysite išdėstytus tinklelyje. Tinklelyje galite pasirinkti, **kurią kiekvieno vaizdo versiją norite peržiūrėti** — mygtukai virš jo leidžia vienu metu perjungti kiekvieną miniatiūrą tarp šaltinio failų ir kiekvieno apdoroto vaizdo.

## Miniatiūrų dydis

Naudokite dešinėje viršuje esantį mastelio slankiklį, kad nustatytumėte vaizdų miniatiūrų dydį. Slankiklis veikia nuo **64 px iki 1200 px**.

* **Ctrl + pelės ratukas** taip pat keičia miniatiūrų mastelį.
* **Ctrl + `+`**/**Ctrl + `=`**ir**Ctrl + `−`** kiekvienu paspaudimu keičia dydį 4 px žingsniu. Klaviatūros nustatymų diapazonas prasideda nuo 64 px mažiausio dydžio ir baigiasi didžiausiu dydžiu, kuris tiksliai talpina po dvi miniatiūras vienoje eilutėje dabartiniame lange.
* Pasirinktas dydis išsaugomas kartu su projektu (`UI → Grid thumbnail size` į `project.json`, numatytasis `160`), todėl vėl atidarius projektą jis atkuriama.

<figure><img src="../.gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>Miniatiūrų *skiriamoji geba* yra atskiras nustatymas, skirtingas nuo miniatiūrų *dydžio*: žr. **Ekranas → Vaizdo miniatiūrų skiriamoji geba** [Projekto nustatymuose](../project-settings/project-settings.md) (numatyta 512 pikselių ilgiausiuoju kraštu). Dydis rodo, kokio dydžio yra piešiamas langelis; skiriamoji geba rodo, kiek detalių yra paimama jam užpildyti.***

## Tinklelio įrankių juosta

Mygtukų eilė virš tinklelio susideda iš iki trijų grupių, iš kairės į dešinę:

1. **Pagal trigerį / Pagal kamerą** — grupavimo režimas. Rodosi tik projektuose, kuriuose yra „LATTICE“ užfiksuoti vaizdai.
2. **Kameros filtravimo mygtukai** — po vieną kiekvienai „LATTICE“ kamerai. Rodosi tik „Pagal kamerą“ režime.
3. **Eksporto/peržiūros režimo mygtukai** — nurodo, kurį produktą rodo kiekviena miniatiūra.

Kai langas per siauras, kad tilptų visi mygtukai, grupės suskleidžiamos iš dešinės į kairę į išskleidžiamus meniu: pirmiausia suskleidžiami eksporto/peržiūros mygtukai, tada – kamerų mygtukai. Suskleistoje grupėje lieka vienas mygtukas, pažymėtas šiuo metu aktyviu pasirinkimu, o užvedus pelę ant jo, visas rinkinys išslysta žemyn. **„Per Trigger“ / „Per Camera“ niekada nesusilanksto.

<!-- SCREENSHOT-NEEDED: Image grid toolbar of a LATTICE array project at full width, showing all three button groups inline: Per Trigger / Per Camera, three camera filter buttons labelled "LATT-M3M (serial)", and the export/view buttons including TIFF, RAW (Original), RAW (Radiance), RAW (Reflectance). -->

*****

## Eksporto peržiūros mygtukai

Šie mygtukai perjungia tinklelio miniatiūras tarp skirtingų vaizdų tipų. **Mygtukas pasirodo, kai tik atsiranda produktas, kurio pavadinimą jis nurodo** — šaltinių failų atveju tai reiškia iškart po importavimo, o ne po apdorojimo. „Chloros“ pakartotinai nuskaito projekto produktus, kol vyksta apdorojimas, todėl mygtukai atsiranda apdorojimo metu, kai kiekvienas produktas pradedamas įrašyti į diską.

### Pagrindinis mygtukas

Kairiausias eksporto mygtukas pažymėtas pagal **tai, ką iš tikrųjų importavote**:

| Kas importuota | Mygtuko pavadinimas |
| --- | --- |
| Survey3 RAW+JPG | `JPG` |
| „LATTICE“ kadrai su ekrano peržiūra šalia neapdoroto kadro | „`PNG`“ arba „`TIFF`“, priklausomai nuo to, kokios yra peržiūros |
| „LATTICE“ užfiksuoti vaizdai, kur pagrindinis failas **yra** RAW kadras | *nėra mygtuko* — `RAW (Original)` jau rodo tą failą |

Mišriame projekte etiketė atitinka tą plėtinį, kurį naudoja dauguma vaizdų.

### Produkto mygtukai

| Mygtukas | Rodo | Kada pasirodo |
| --- | --- | --- |
| **Tikslai** | Vaizdai su aptiktu kalibravimo tikslu | Po vykdymo, kurio metu buvo aptikti tikslai |
| **Atspindžio koeficientas** | Kalibruoti atspindžio koeficiento vaizdai | Tik Survey3 projektuose — LATTICE projektuose vietoj to naudojamas `RAW (Reflectance)`, todėl tinklelyje niekada nerodomi du atspindžio koeficiento mygtukai |
| **Baltojo balanso** | Vaizdas su nustatytu baltuoju balansu (RGB kameros) | Po apdorojimo |
| **Ištaisyta vinjetė** | Nekalibruotas atsarginis variantas su ištaisyta vinjete | Po ciklo, kurio metu nebuvo galima taikyti atspindžio kalibravimo ir buvo įjungta *vinjetės korekcija* |
| **Jutiklio atsakas** | Nekalibruotas atsarginis variantas su jutiklio atsakais | Tas pats, bet su išjungta *vigneto korekcija* |
| **`RAW (<INDEX> Index)`** | Po vieną mygtuką kiekvienam apskaičiuotam indeksui | Po ciklo su sukonfigūruotais indeksais |
| **`<INDEX> LUT`** | Vienas mygtukas kiekvienam spalviškai susietam indeksui | Po bandymo su sukonfigūruotu LUT |
| **`<Index> <Index\|LUT> <NNN>`** | Po vieną mygtuką už kiekvieną [Indekso/LUT „Sandbox“](index-lut-sandbox.md) eksporto vykdymą | Tuo momentu, kai baigiasi „Sandbox“ eksportas |

### „LATTICE“ lygio mygtukai

Projektai, kuriuose yra „LATTICE“ įrašai, turi šiuos mygtukus, pažymėtus lygio pavadinimu, o ne produkto pavadinimu:

| Mygtukas | Lygis |
| --- | --- |
| **RAW (Originalus)** | Importuotas šaltinis – neapdorotas kadras |
| **RAW (Spinduliavimas)** | „Float32“ spektrinis spinduliavimas, W/m²/sr/nm |
| **RAW (Atspindys)** | „uint16“ atspindys, 32768 = ρ 1,0 |

`RAW (Original)` yra prieinamas nuo pat importavimo momento — jo nereikia apdoroti. Kai LATTICE importui visiškai nėra bazinio mygtuko (kiekvieno įrašo bazinis failas yra jo neapdorotas kadras), tinklelis pats persikelia prie pirmojo prieinamo lygio mygtuko, kad įrankių juostos paryškinimas atitiktų tai, ką matote.

Dviejų lygių Chloros eksportai **neturi savo atskiro tinklelio mygtuko**:

* **Debayered** — `RAW (Original)` vaizdas jau atvaizduojamas be „bayeringo“, todėl antrasis mygtukas ant vizualiai identiško vaizdo būtų nereikalingas. `RAW (Debayered)` produktas vis dar įrašomas į diską ir vis dar pasirenkamas iš viso ekrano sluoksnių išskleidžiamojo meniu.
* **Peržiūra** — RGB kamerose peržiūra registruojama kaip `White Balanced` sluoksnis, kuris turi mygtuką. Daugiaspektrinėse kamerose jis registruojamas kaip „`RAW (Preview)`“ ir pasiekiamas iš visą ekraną užimančio sluoksnių išskleidžiamojo meniu.

{% hint style="info" %}
Šie lygio mygtukai rodomi tik tuose projektuose, kuriuose iš tikrųjų yra „LATTICE“ kadrai. Survey3 projektuose registruojami kai kurie tie patys vidiniai sluoksnių pavadinimai, o mygtukai jiems yra filtruojami, todėl Survey3 tinklelis išlaiko savo įprastą `JPG / Targets / Reflectance` rinkinį.
{% endhint %}

Paspaudus tinklelio miniatiūrą, atidaromas visą ekraną užimantis [Vaizdų peržiūros langas](opening-an-image-full-screen.md) su **tuo pačiu produktu, kurį rodo tinklelis** — jei tinklelis nustatytas kaip „`Targets`“, miniatiūra atidaro eksportuotą tikslinį vaizdą.

<figure><img src="../.gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: This GIF predates the LATTICE level buttons and the toolbar group separators. Reshoot on a LATTICE project cycling base -> RAW (Original) -> RAW (Radiance) -> RAW (Reflectance) -> an index button, so the new button set and the level names are visible. -->

***

## „LATTICE“ projekto grupavimas: pagal trigerį ar pagal kamerą

Masyvo įrašai sukuria kelis to paties momento vaizdus iš skirtingų kamerų modulių. Grupavimas nulemia, kaip tinklelis juos išdėsto. Abu režimai rodo visą plotį užimančias sulankstomas antraščių juostas; **kiekviena grupė iš pradžių yra išskleista**, o Chloros įsimena tas, kurias uždarote. Sulankstymo būsena sekama atskirai pagal kiekvieną režimą, todėl uždarius grupę režime „Pagal kamerą“, režime „Pagal trigerį“ niekas neuždaroma.

### „Pagal kamerą“ (numatyta)

Viena grupė kiekvienam kameros moduliui. Antraštėje rodomas kameros modelis ir serijos numeris (`LATT-M3M — <serial>`) bei nuotraukų skaičius. Grupės viduje esančios nuotraukos išdėstomos chronologine tvarka pagal fotografavimo įvykius.

Šiame režime įrankių juostoje taip pat atsiranda po vieną **kameros filtravimo mygtuką kiekvienai kamerai**, pažymėtą `MODEL (SERIAL)`. Visos kameros iš pradžių yra pažymėtos; paspaudus mygtuką, tos kameros pažymėjimas panaikinamas, o jos grupė pašalinama iš lentelės. Tai greitas būdas peržiūrėti vieną juostą per visą skrydį.

### Pagal suaktyvinimą

Viena grupė vienam fiksavimo įvykiui — visų modulių kadrų rinkinys, nufotografuotas tuo pačiu suaktyvinimu. Antraštėje rodomas fiksavimo laikas, prisidėjusių kamerų skaičius ir žymė pagal kiekvieną grupėje esančią kameros modelį. Grupės viduje esančios plytelės surūšiuotos pagal kamerų serijinius numerius, todėl tas pats juostos kanalas kiekvienam suaktyvinimui yra toje pačioje stulpelyje.

<!-- SCREENSHOT-NEEDED: Image grid in Per Trigger mode for a 3-camera LATTICE array, showing two consecutive trigger groups with their header bars (chevron, capture timestamp, "3 cameras", and the three model badges) and one group collapsed to show the closed state. -->
Mišriame projekte esantys ne „LATTICE“ vaizdai nėra grupuojami — jie rodomi kaip paprastos plytelės po grupių.

***

## Tinklelio miniatiūros atitinka GSD bloko dydį

Jei vaizdo skirtuko šoninėje juostoje nustatėte **GSD (px)** bloko dydį, tinklelio miniatiūros rodomos ta pačia žemės skiriamąja geba — ne tik per visą ekraną. Bloko dydis 8 reiškia, kad kiekvienas rodomas pikselis yra 8 × 8 šaltinio pikselių bloko vidurkis visur programoje, kur rodomas vaizdas.

Kadangi plytelė iš pradžių yra tik keli šimtai pikselių pločio, stambūs bloko dydžiai nustoja daryti matomą skirtumą tinklelyje gerokai anksčiau nei peržiūroje visame ekrane: 4000 px rėmelis, nupieštas 160 px plytelėje, jau sudaro maždaug 25 šaltinio pikselius vienam rodomam pikseliui. Apie patį valdymo elementą skaitykite [Vaizdo atidarymas visame ekrane](opening-an-image-full-screen.md#gsd-block-size).

***

## Susijusios puslapiai

* [**Vaizdo atidarymas visame ekrane**](opening-an-image-full-screen.md) — peržiūros programa visame ekrane, žymeklio reikšmės ir histograma
* [**Vaizdo sluoksniai**](image-layers.md) — sluoksnių išskleidžiamas meniu peržiūros programoje visame ekrane
* [**Indekso/LUT bandymų aplinka**](index-lut-sandbox.md) — indeksų vizualizacijų kūrimas ir eksportavimas
* [**Projekto nustatymai**](../project-settings/project-settings.md) — eksporto perjungikliai, lemiantys, kokie produktai iš viso egzistuoja
