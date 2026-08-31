# Kamerų prijungimas

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Skirtukas „Kameros“ prieš prijungiant bet kokį įrenginį</p></figcaption></figure>Chloros automatiškai aptinka LATTICE kameras tinkle — iš GUI skirtuko „Kameros“, iš `chloros-cli lattice` arba iš Python SDK. Kameros modelio eilutė lemia visus tolesnius veiksmus: Chloros nustato jutiklio profilį, juostų išdėstymą ir gamyklinius kalibravimus remdamasis kameros `DeviceUserID` + `DeviceSerialNumber` duomenimis, todėl **nereikia nieko konfigūruoti kiekvienai kamerai atskirai**.

Prieš prijungiant įsitikinkite, kad pagrindinis tinklas yra sukonfigūruotas — nustatytas vietinis adresavimas, „jumbo“ rėmeliai, o masyvų atveju — tinklo plokštės priėmimo buferio parametrai. Tai yra aparatinės įrangos nustatymas, aprašytas „LATTICE“ vadove: [**Tinklo nustatymas**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## Prijungimas per grafinę vartotojo sąsają

Atidarykite skirtuką **„Kameros“**Chloros šoninėje juostoje (aparatinės įrangos skirtukai pasirodo, kai užbaigiamas serverio paleidimas) arba naudokite pagrindinį meniu →**„Prisijungti prie kameros“**. Abiem atvejais atsidaro dialogo langas**„Prisijungti prie kameros (-ų)“**.

### Dialogo langas „Prijungti kamerą (-as)“

Atidarius langą, jis iškart nuskaito tinklą („Nuskaitoma tinklas...“) ir pateikia visų rastų kamerų sąrašą. Kiekvienoje eilutėje nurodytas kameros **modelis**(pvz., `LATT-M3M-L41-F550`),**serijos numeris**ir**IP adresas**.

* **Spustelėkite eilutę, kad ją pažymėtumėte**(žalia spalva). Galite pažymėti**kelias kameras** ir jas prijungti vienu metu — Chloros jas prijungia paeiliui.
* Eilutės su ženklu **„Prijungta“** jau yra prijungtos ir negali būti atrinktos dar kartą.
* Eilutės su ženklu **„Masyve“** priklauso šiuo metu prijungtam kamerų masyvui. Norėdami naudoti tą kamerą atskirai, pirmiausia atjunkite masyvą.
* **Prijungti** — prijungia pažymėtą (-as) kamerą (-as); mygtuke rodomas skaičius, pvz., „Prijungti (3)“, kai pažymėta daugiau nei viena kamera.
* **Iš naujo nuskaityti** — vėl paleidžia paiešką.
* **Uždaryti** — uždaro dialogo langą.
* Jei nuskaitymas baigiasi be rezultatų, dialogo lange rodomas pranešimas **„Tinkle nerasta kamerų“** — žr. [Trikčių šalinimas](connecting.md#troubleshooting) žemiau.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Dialogo langas „Prijungti kamerą (-as)“ — čia parodytas, kai tinkle nėra jokių kamerų</p></figcaption></figure>### Pirmasis prijungimas: kalibravimo paketo atsisiuntimas

**Pirmą kartą**prijungus tam tikrą kamerą prie kompiuterio, Chloros per GigE iš pačios kameros atsisiunčia gamyklinį kameros kalibravimo paketą (\~3,8 MB). Kol vyksta šis procesas, dialogo lange rodomas žalias skydelis**„Atsisiunčiama kalibravimo duomenys iš kameros“**su atskira pažangos juosta kiekvienam serijiniam numeriui – tai trunka maždaug**70 sekundžių** vienai kamerai. Paketas išsaugomas kompiuterio talpykloje, todėl vėliau prijungiant tą pačią kamerą atsisiuntimas visiškai praleidžiamas (ir skydelis nerodomas).

### Sistemos analizė

Dialogo lange esantis mygtukas **„Analyze System“** (Analizuoti sistemą) tikrina pagrindinį kompiuterį ir tinklą (vykdymo metu rodomas užrašas „Analyzing...“ (Analizuojama...)) ir pateikia diagnostikos ataskaitą:

* **Pagrindinis kompiuteris** — procesoriaus branduoliai ir RAM; GPU pavadinimas ir atmintis arba „GPU: nenustatyta“.
* **Tinklo sąsajos** — kiekvienos tinklo plokštės pavadinimas, ryšio greitis, MTU (su žyma „jumbo“, jei aktyvuota), prisijungimo / atjungimo būsena ir informacija, ar ji prijungta prie USB magistralės.
* **Kameros**— serijos numeris, modelis, IP adresas ir**prie kurio tinklo adapterio prijungta kiekviena kamera**.
* **Našumas** — dabartinis ir idealus kadrų per sekundę skaičius (fps) kiekvienai kamerai pagal pikselių formatą; jei idealus rodiklis viršija dabartinį, rodoma žalia eilutė „Potencialas: įmanomas N× pagerinimas“.
* **Įspėjimai ir sunumeruotos rekomendacijos** — arba „Sistema veikia gerai, atsižvelgiant į dabartinį kamerų skaičių“, kai nėra nieko, ką reikėtų taisyti.

Paleiskite šią funkciją, kai kamerų aptikimas ar transliacija veikia netikėtai — ji nustato daugumą su tinklo plokštėmis susijusių problemų (neteisingas MTU, kamera prijungta prie netinkamos sąsajos, USB adapterio apribojimai) neišeinant iš dialogo lango.

### Kamerų masyvo prijungimas

Norėdami prijungti dvi ar daugiau kamerų kaip **sinchronizuotą masyvą**, naudokite masyvo prijungimo vedlį (**„Prijungti kamerų masyvą“**): jis veda per pagrindinės ir pavaldžiosios kamerų atranką (iš anksto užpildytą GPIO jungčių tikrinimo priemonės duomenimis), rodymo režimo pasirinkimą („Atskiri“ arba „Sujungti langeliai“) ir masyvo nustatymų langą su realiu pasiekiamų kadrų per sekundę (fps) ir laidinio pralaidumo rodymu prieš patvirtinant. Vedlys ir masyvo darbo eigos aprašytos šio vadovo skyriuje apie daugiakamerinius masyvus; CLI atitikmuo yra „LATTICE kameros pirmojo prijungimo darbo eiga“ [CLI nuorodoje](../reference/cli-reference.md).

## Prisijungimas iš CLI ir SDK

Norint naudotis CLI ir SDK, reikia turėti mokamą Chloros+ planą ir būti prisijungusiam; tai užtikrinama serverio pusėje (`401 AUTH_REQUIRED`, kai nesate prisijungę, `403 PLAN_UPGRADE_REQUIRED` nemokamoje pakopoje).

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

Išsamūs parašai, parinktys ir duomenų surinkimo darbo srautai: [CLI Nuoroda](../reference/cli-reference.md) § `chloros-cli lattice`, [SDK Nuoroda](../reference/sdk-reference.md) § `connect_camera()` / `connect_array()`.

## Kaip atliekamas kalibravimas prisijungus

Kiekviena „LATTICE“ kamera turi gamyklinį kalibravimo paketą **pačioje kameroje**, o Chloros taip pat patikrina MAPIR debesį, kai kamera prisijungia:

| Situacija   | Ką naudoja „Chloros“                                                                                                                                                                                                          |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Prisijungęs**|**Naujausias kalibravimas, paskelbtas tam serijos numeriui** — debesies kopija turi pirmenybę prieš kameroje esančią kopiją. Todėl kamera, kuri buvo pakalibruota ar atnaujinta naudojant MAPIR, atsinaujina automatiškai; vartotojo veiksmų nereikia. |
| **Neprisijungus**|**Kameroje esantis paketas**, toks, koks yra. Visiškai neprisijungus veikiantys darbo srautai toliau veikia; jie tiesiog neperima naujesnių kalibravimų, kol kamera bent kartą neprisijungs prie interneto (arba nebus atnaujinta gamykliniu būdu).                                                  |

Fiksuojant vaizdą, faktiškai pritaikyti koeficientai yra **įrašomi į kiekvieno vaizdo XMP metaduomenis**. Vėlesnis kalibravimo atnaujinimas niekada tyliai nekeičia jau užfiksuotų vaizdų — perdirbant seną įrašą naudojami koeficientai, įrašyti jo XMP metaduomenyse, o ne tie, kurie šiandien yra naujausi.

## Problemų sprendimas

* **„Tinkle nerasta kamerų“**— patikrinkite vietinio ryšio nustatymus skyriuje [Tinklo nustatymas](https://mapir.gitbook.io/lattice-camera/setup/network-setup): kompiuterio tinklo plokštė su statiniu adresu `169.254.x.x/16`, kameros turi būti tame pačiame tinkle, DHCP ar šliuzo nereikia. Tada naudokite funkciją**„Analyze System“**prisijungimo dialogo lange, kad patikrintumėte, kuriame tinklo adapteryje kiekviena kamera yra (arba nėra) matoma. Po bet kokių kabelių ar tinklo adapterių pakeitimų atlikite**„Rescan“**.
* **Anksčiau veikusi įranga atsisako prisijungti** (masyvo skydų vartai su `FRAMES WILL DROP` / `Reduce ROI to enable`) — tinklo plokštės tvarkyklės atnaujinimas tyliai iš naujo nustatė „receive-ring“ parametrus. Iš naujo pritaikykite juos arba paleiskite komandą „`chloros-cli lattice network --fix`“ iš terminalo su administratoriaus teisėmis; žr. [Tinklo nustatymas](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Kamera rodo „In Array“** — ji priklauso prijungtai masyvo sesijai. Atjunkite masyvą, jei norite naudoti kamerą atskirai.
