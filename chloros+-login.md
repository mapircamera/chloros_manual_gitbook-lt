# Prisijungimas prie „Chloros+“

## Prisijungimas per grafinę vartotojo sąsają (GUI)

Naudotojo šoninio meniu <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> pagalba galite prisijungti prie savo „Chloros+“ paskyros ir atrakinti papildomas funkcijas.

**Kiekviename kompiuteryje prisijungti reikia tik vieną kartą.** GUI, CLI ir Python SDK naudoja tą pačią sesijos talpyklą — prisijungus per darbalaukio GUI, taip pat aktyvuojami CLI ir SDK tame kompiuteryje (ir atvirkščiai per `chloros-cli login`).

Prisijungus bus rodomi jūsų paskyros duomenys:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the logged-in user account panel in Chloros 1.3.0 — plan name display and the registered-device list UI may have changed; must show plan name, expiration, and device list. -->

## Planų lygiai

| Planas | `plan_id` | Tipas |
| --- | --- | --- |
| „Iron“ | `0` | Nemokamas |
| „Copper“ | `1` | Mokamas („Chloros

+“) |
| „Bronze“ | `2` | Mokamas („Chloros

+“) |
| „Silver“ | `3` | Mokamas („Chloros

+“) |
| Auksas | `4` | Mokamas („Chloros

+“) |

Kiekvieno mokamo lygio paslaugų sąrašą rasite [planuose ir kainoraštyje](https://cloud.mapir.camera/pricing).

### Norint naudotis CLI

/ SDK

, reikalingas mokamas lygis

Prieigai prie CLI

ir Python

SDK

reikalingas **bet kuris mokamas „Chloros

+“ lygis („Copper“ ar aukštesnis)**. Tai užtikrinama**serverio pusėje** — kiekvienas CLI

/SDK

užklausimas turi turėti tiek aktyvią sesiją, tiek mokamą planą:

| HTTP

būsena | `error_code` | Reikšmė | Sprendimas |
| --- | --- | --- | --- |
| `401` | `AUTH_REQUIRED` | Neprisijungta šiame kompiuteryje | `chloros-cli login <email> <password>` |
| `403` | `PLAN_UPGRADE_REQUIRED` | Prisijungta, bet plano lygis per žemas (nemokamas „Iron“ lygis) | Atnaujinkite į bet kurį mokamą „Chloros

+“ planą |

`chloros-cli status` lieka pasiekiamas nemokamoje pakopoje, todėl visada galite pamatyti savo dabartinį planą ir priežastį, dėl kurios prieiga atsisakyta.

### Prijungtos įrangos apribojimai pagal planą

Kiekviename plane nustatytas ribinis skaičius, kiek „LATTICE“ kamerų ir DAQ šviesos jutiklių galima vienu metu prijungti tiesiogiai:

| Planas | „LATTICE“ kameros | DAQ šviesos jutikliai |
| --- | --- | --- |
| „Iron“ (nemokamas / neprisijungęs) | 4 | 2 |
| „Copper“ / „Bronze“ | 6 | 3 |
| „Silver“ | 10 | 6 |
| „Gold“ | 20 | 12 |

## Prisijungimas prie CLI



Prisijunkite naudodami savo „Chloros

+“ prisijungimo duomenis, kad įgalintumėte CLI

apdorojimą. Linux

sistemoje (be grafinės sąsajos) tai yra vienintelis būdas aktyvuoti licenciją.

**Sintaksė:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK

vartotojai**: Python

SDK

taip pat teikia programinį metodą `logout()`, skirtą išvalyti talpyklos duomenis. Daugiau informacijos rasite [SDK

žinyne](reference/sdk-reference.md).
{% endhint %}

**Pavyzdys:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Specialieji simboliai**: Slaptažodžius, kuriuose yra simbolių, pvz., `$`, `!`, arba tarpų, įrašykite tarp viengubų kabučių.
{% endhint %}

**Rezultatas:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the CLI login output — the banner now prints "Chloros CLI 1.3.0"; capture a successful login with the current output format. -->

### Autentifikavimo duomenų saugojimas

Išsaugoti autentifikavimo duomenys ir konfigūracija **visose platformose** saugomi jūsų vartotojo namų katalogo aplanke „`.chloros`“:

| Platforma | Autentifikavimo duomenų talpyklos kelias |
| --- | --- |
| **Windows** | `%USERPROFILE%\.chloros\` |
| **Linux** | `~/.chloros/` |

Įdiegus naujesnę versiją vietoj esamos, jūsų prisijungimo duomenys išlieka Windows

(įdiegimo programoje) ir Linux

(kataloge „`.deb`“, įskaitant „`chloros-cli update`“). Pašalinus Chloros

— arba pašalinus Linux

paketą — išvaloma prisijungimo informacija iš talpyklos, todėl po naujos įdiegos turėsite prisijungti iš naujo.

### Plano galiojimo pabaiga ir atidėjimo laikotarpis

GUI rodomas plano galiojimo pabaigos laikas, kai jūsų licencija taps negaliojanti. Kiekvieną mėnesį atnaujinamų prenumeratų galiojimas baigiasi mėnesio pabaigoje; metinių prenumeratų – praėjus metams nuo prenumeratos pradžios.

Chloros

tikrina jūsų licenciją internete, tačiau darbas neprisijungus prie interneto palaikomas per atidėjimo laikotarpį:

* Sėkmingi serverio patikrinimai išsaugomi talpykloje **5 minutes**, todėl įprastinio naudojimo metu licencijos patikrinimų atliekama labai nedaug.
* Pasirašyta, prie kompiuterio pririšta licencijos talpykla užtikrina ilgesnį veikimą neprisijungus: **30 dienų mėnesiniams planams**ir**iki jūsų prenumeratos galiojimo pabaigos datos (ne daugiau kaip 365 dienos) metiniams planams**.
* Pasibaigus atidėjimo laikotarpiui, planas perjungiamas į nemokamą „Iron“ lygį, kol kompiuteris bent kartą prisijungs prie licencijų serverio; prieiga atnaujinama po kito sėkmingo patikrinimo.

### Įrenginių skaičiaus riba

Kiekvienas „Chloros

+“ planas siūlo skirtingą registruotų įrenginių skaičių. Kiekvienas įrenginys, prie kurio prisijungiate naudodami „Chloros

+“ paskyrą, įskaičiuojamas į jūsų registruotų įrenginių skaičių. Savo „MAPIR

Cloud“ paskyros puslapyje galite pervardyti ir pašalinti įrenginį.

<table><thead><tr><th width="168.5999755859375" align="right">„Chloros+“ planas</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">Palaikomi įrenginiai</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>

Tiksli jūsų paskyros įrenginių kvota nurodyta „MAPIR Cloud“ paskyros puslapyje. Atsijungus iš įrenginio, jo vieta patikimai atlaisvinama, o jau užregistruotas įrenginys visada gali vėl prisijungti, net jei paskyra pasiekė įrenginių limitą.
