# Kamerų prijungimas

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Skirtukas „Kameros“, kol dar niekas nėra prijungta</p></figcaption></figure>

Chloros automatiškai aptinka „LATTICE“ kameras tinkle — iš GUI skirtuko „Kameros“, iš „`chloros-cli lattice`“ arba iš Python SDK. Kameros modelio eilutė lemia viską tolesniuose etapuose: Chloros nustato jutiklio profilį, juostų išdėstymą ir gamyklinį kalibravimą pagal kameros „`DeviceUserID`“ + „`DeviceSerialNumber`“, todėl **nėra nieko, ką reikėtų konfigūruoti kiekvienai kamerai atskirai**.

Prieš prijungiant įsitikinkite, kad pagrindinis tinklas yra sukonfigūruotas — nustatytas vietinis adresavimas, „jumbo“ rėmeliai, o masyvų atveju — tinklo plokštės priėmimo buferio parametrai. Tai yra aparatinės įrangos nustatymas, aprašytas „LATTICE“ vadove: [**Tinklo nustatymas**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## Prijungimas per grafinę vartotojo sąsają

Atidarykite skirtuką **„Kameros“** Chloros šoninėje juostoje (aparatinės įrangos skirtukai pasirodo, kai užbaigiamas sistemos paleidimas) arba naudokite pagrindinį meniu → **„Prijungti prie kameros“**. Abiem atvejais atsidaro dialogo langas**„Prijungti kamerą (-as)“**.

### Dialogo langas „Prijungti kamerą (-as)“

Atidarius langą, jis iškart nuskaito tinklą („Nuskaitoma tinklas...“) ir pateikia visų rastų kamerų sąrašą. Kiekvienoje eilutėje rodomas kameros **modelis**(pvz., `LATT-M3M-L41-F550`),**serijos numeris**ir**IP adresas**.

* **Spustelėkite eilutę, kad ją pažymėtumėte**(žalia spalva). Galite pasirinkti**kelias kameras** ir jas prijungti vienu kartu — Chloros jas prijungia paeiliui.
* Eilutės su ženklu **„Prijungta“** jau yra prijungtos ir negali būti pasirinktos dar kartą.
* Eilutės su ženklu **„Masyve“** priklauso šiuo metu prijungtam kamerų masyvui. Norėdami naudoti tą kamerą atskirai, pirmiausia atjunkite grupę.
* **Prijungti** — prijungia pasirinktą (-as) kamerą (-as); jei pasirinkta daugiau nei viena kamera, mygtuke rodomas skaičius, pvz., „Prijungti (3)“.
* **Iš naujo nuskaityti** — vėl paleidžia paiešką.
* **Uždaryti** — uždaro dialogo langą.
* Jei paieška baigiasi be rezultatų, dialogo lange rodomas pranešimas **„Tinkle nerasta kamerų“** — žr. [Trikčių šalinimas](connecting.md#troubleshooting) žemiau.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Dialogo langas „Prijungti kamerą (-as)“ — čia rodomas, kai tinkle nėra kamerų</p></figcaption></figure>

### Pirmasis prijungimas: kalibravimo paketo atsisiuntimas

**Pirmą kartą** prijungus tam tikrą kamerą prie kompiuterio, Chloros per GigE iš pačios kameros atsisiunčia gamyklinį kameros kalibravimo paketą (\~3,8 MB). Kol vyksta šis procesas, dialogo lange rodomas žalias skydelis **„Atsisiunčiama kalibravimo duomenys iš kameros“**su atskira pažangos juosta kiekvienam serijiniam numeriui — tai užtrunka maždaug**70 sekundžių** vienai kamerai. Jei kamerų yra daugiau nei šešios, pažangos eilutės slenka skydelio viduje. Paketas išsaugomas kompiuterio talpykloje, todėl vėliau prijungiant tą pačią kamerą atsisiuntimas visiškai praleidžiamas (ir langelis nerodomas).

### Sistemos analizė

Dialogo lange esantis mygtukas **„Analizuoti sistemą“** tikrina pagrindinį kompiuterį ir tinklą (vykdymo metu rodomas užrašas „Analizuojama...“) ir pateikia diagnostikos ataskaitą:

* **Pagrindinis kompiuteris** — procesoriaus branduoliai ir RAM; GPU pavadinimas ir atmintis arba „GPU: nenustatyta“.
* **Tinklo sąsajos** — kiekvienos tinklo plokštės pavadinimas, ryšio greitis, MTU (su žyma „jumbo“, jei aktyvuota), prisijungimo/atsijungimo būsena ir informacija, ar ji prijungta prie USB magistralės.
* **Kameros**— serijos numeris, modelis, IP adresas ir**prie kurio tinklo adapterio prijungta kiekviena kamera**.
* **Našumas** — dabartinis ir idealus kadrų per sekundę skaičius (fps) kiekvienai kamerai pagal pikselių formatą; jei idealus rodiklis viršija dabartinį, rodoma žalia eilutė „Potencialas: įmanomas N× pagerėjimas“.
* **Įspėjimai ir sunumeruotos rekomendacijos** — arba „Sistema atrodo gerai, atsižvelgiant į dabartinį kamerų skaičių“, kai nėra ko taisyti.

Paleiskite šią funkciją, kai aptikimas ar transliacija veikia netikėtai — ji nustato daugumą su tinklo plokštėmis susijusių problemų (netinkamas MTU, kamera prijungta prie netinkamos sąsajos, USB adapterio apribojimai) neišeinant iš dialogo lango.

### Kamerų masyvo prijungimas

Norėdami prijungti dvi ar daugiau kamerų kaip **sinchronizuotą masyvą**, vietoj to naudokite masyvo prijungimo vedlį (**Prijungti kamerų masyvą**): jis padės atlikti pagrindinės ir pavaldžiosios kamerų parinkimą (iš anksto užpildytą sinchronizavimo kabelio patikrinimo metu — bet kuri prijungta kamera gali būti pagrindinė, o RGB `-FRGB` kamera yra iš anksto pasirinkta, kai ji prijungta kabeliu), ekrano režimo pasirinkimą (Atskiri vs. Sujungti langeliai) ir masyvo nustatymų sceną su tiesioginiu pasiekiamų kadrų per sekundę (fps) ir laidinio pralaidumo rodymu prieš patvirtinant. Vedlys ir kamerų masyvo darbo eigos aprašytos šio vadovo skyriuje apie daugiakamerinius masyvus; CLI ekvivalentas yra „LATTICE kameros pirmojo prisijungimo darbo eiga“ [CLI nuorodoje](../reference/cli-reference.md).

## Prijungimas per CLI ir SDK
Norint naudotis

CLI ir SDK, būtina turėti mokamą „Chloros+“ planą ir būti prisijungusiam; tai užtikrinama serverio pusėje (`401 AUTH_REQUIRED`, kai nesate prisijungę, `403 PLAN_UPGRADE_REQUIRED` nemokamame plane).

```bash
# List cameras on the network (vendor, model, serial, IP, MAC)
chloros-cli lattice info

# Single-camera smoke test: capture one frame (saves every applicable export type)
chloros-cli lattice capture -o output/

# Connect a synchronized array — same smart-prep flow as the GUI
chloros-cli lattice array-connect --serials 213800234,214000533
```

```python
import chloros_sdk

# Persistent live-camera session through the backend
with chloros_sdk.connect_camera("213800234") as cam:
    ...

# Array session (smart-prep: network probe, tier auto-pick, PTP, AE seeding, trigger config)
with chloros_sdk.connect_array(["213800234", "214000533"]) as array:
    ...
```

Išsamūs parašai, parinktys ir duomenų surinkimo darbo eigos: [CLI žinynas](../reference/cli-reference.md) § `chloros-cli lattice`, [SDK žinynas](../reference/sdk-reference.md) § `connect_camera()` / `connect_array()`.

## Kaip atliekamas kalibravimas prisijungus

Kiekviena „LATTICE“ kamera turi gamyklinį kalibravimo paketą **pačioje kameroje**, o Chloros taip pat patikrina „MAPIR “ debesį, kai kamera prisijungia:

| Situacija   | Ką naudoja Chloros                                                                                                                                                                                                           |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Prisijungus**|**Naujausias kalibravimas, paskelbtas tam serijos numeriui** — debesų kopija turi pirmenybę prieš kameroje esančią kopiją. Todėl kamera, kuri buvo pakalibruota arba atnaujinta MAPIR, atsinaujina automatiškai; vartotojo veiksmų nereikia. |
| **Neprisijungus**|**Kameroje esantis paketas**, toks, koks yra. Visiškai neprisijungus veikiantys darbo srautai toliau veikia; jie tiesiog neperima naujesnių kalibravimų, kol kamera bent kartą neprisijungs prie interneto (arba nebus atnaujinta gamykliniu būdu).                                                  |

Fiksuojant vaizdą, faktiškai taikomi koeficientai yra **įrašomi į kiekvieno vaizdo XMP metaduomenis**. Vėlesnis kalibravimo atnaujinimas niekada tyliai nepakeičia jau užfiksuotų vaizdų — perdirbant seną įrašą naudojami koeficientai, įrašyti jo XMP metaduomenyse, o ne tie, kurie šiandien yra naujausi.

## Problemų sprendimas

* **„Tinkle nerasta kamerų“**— patikrinkite vietinio ryšio nustatymus skyriuje [Tinklo nustatymas](https://mapir.gitbook.io/lattice-camera/setup/network-setup): stacionarus kompiuterio tinklo adapteris `169.254.x.x/16`, kameros tame pačiame tinkle, DHCP/vartų nereikia. Tada naudokite funkciją**„Analyze System“**prisijungimo dialogo lange, kad patikrintumėte, per kurį tinklo adapterį kiekviena kamera yra (arba nėra) matoma. Po bet kokių kabelių sujungimo ar tinklo adapterio pakeitimų atlikite**„Rescan“**.
* **Anksčiau veikusi įranga neprisijungia** (masyvo skydų vartai su `FRAMES WILL DROP` / `Reduce ROI to enable`) — tinklo plokštės tvarkyklės atnaujinimas tyliai iš naujo nustatė priėmimo žiedo parametrus. Pradėkite juos iš naujo arba paleiskite komandą „`chloros-cli lattice network --fix`“ iš terminalo su administratoriaus teisėmis; žr. [Tinklo nustatymas](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Kamera rodo „In Array“** — ji priklauso prijungtai masyvo sesijai. Atjunkite masyvą, jei norite naudoti kamerą atskirai.
