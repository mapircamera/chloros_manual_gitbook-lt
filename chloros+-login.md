# Chloros+ Prisijungimas

## Prisijungimas per grafinę vartotojo sąsają

Naudotojo „<img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">“ šoniniame meniu galite prisijungti prie savo „Chloros+“ paskyros ir atrakinti papildomas funkcijas.

**Kiekviename kompiuteryje prisijungti reikia tik vieną kartą.** Grafinė vartotojo sąsaja, „CLI“, „Python“ ir „SDK“ naudoja tą pačią sesijos talpyklą — prisijungus per darbalaukio GUI, toje mašinoje taip pat aktyvuojami „CLI“ ir „SDK“ (ir atvirkščiai – per „`chloros-cli login`“).

Prisijungus bus rodomi jūsų paskyros duomenys:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the logged-in user account panel in Chloros 1.2.0 — plan name display and the registered-device list UI may have changed; must show plan name, expiration, and device list. -->
## Planų lygiai

| Planas | `plan_id` | Tipas |
| --- | --- | --- |
| „Iron“ | `0` | Nemokamas |
| „Copper“ | `1` | Mokamas (Chloros+) |
| „Bronze“ | `2` | Mokamas (Chloros+) |
| Sidabras | `3` | Mokamas (Chloros+) |
| Auksas | `4` | Mokamas (Chloros+) |

Ką apima kiekvienas mokamas lygis, žr. [planus ir kainas](https://cloud.mapir.camera/pricing).

### Norint naudotis CLI / SDK, reikalingas mokamas planas

Prieigai prie CLI ir Python bei SDK reikalingas **bet koks mokamas Chloros+ planas („Copper“ ar aukštesnis)**. Tai užtikrinama**serverio pusėje** — kiekvienas CLI/SDK užklausimas turi turėti tiek aktyvią sesiją, tiek mokamą planą:

| HTTP būsena | `error_code` | Reikšmė | Sprendimas |
| --- | --- | --- | --- |
| `401` | `AUTH_REQUIRED` | Neprisijungta šiame kompiuteryje | `chloros-cli login <email> <password>` |
| `403` | `PLAN_UPGRADE_REQUIRED` | Prisijungta, bet plano lygis per žemas (nemokamas „Iron“ lygis) | Atnaujinkite į bet kurį mokamą „Chloros+“ planą |

`chloros-cli status` lieka pasiekiamas nemokamo lygio sąlygomis, todėl visada galite pamatyti savo dabartinį planą ir priežastį, dėl kurios prieiga atsisakyta.

### Prijungtos įrangos apribojimai pagal planą

Kiekvienas planas apriboja, kiek „LATTICE“ kamerų ir DAQ šviesos jutiklių galima vienu metu prijungti tiesiogiai:

| Planas | „LATTICE“ kameros | DAQ šviesos jutikliai |
| --- | --- | --- |
| „Iron“ (nemokamas / neprisijungęs) | 4 | 2 |
| „Copper“ / „Bronze“ | 6 | 3 |
| „Silver“ | 10 | 6 |
| „Gold“ | 20 | 12 |

## CLI prisijungimas

Prisijunkite naudodami savo Chloros+ prisijungimo duomenis, kad įgalintumėte CLI apdorojimą. Linux (be grafinės vartotojo sąsajos) sistemoje tai yra vienintelis būdas aktyvuoti licenciją.

**Sintaksė:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK vartotojai**: Python ir SDK taip pat siūlo programinį `logout()` metodą, skirtą išvalyti talpyklos duomenis. Išsamią informaciją rasite [SDK žinyne](reference/sdk-reference.md).
{% endhint %}

**Pavyzdys:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Specialieji simboliai**: Slaptažodžius, kuriuose yra simbolių, pvz., `$`, `!`, arba tarpų, apgaubkite viengubomis kabutėmis.
{% endhint %}

**Rezultatas:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the CLI login output — the banner now prints "Chloros CLI 1.2.0"; capture a successful login with the current output format. -->
### Autentifikavimo duomenų saugojimas

Išsaugoti autentifikavimo duomenys ir konfigūracija **visose platformose** saugomi jūsų vartotojo namų kataloge esančiame aplanke „`.chloros`“:

| Platforma | Autentifikavimo duomenų talpyklos kelias |
| --- | --- |
| **Windows** | `%USERPROFILE%\.chloros\` |
| **Linux** | `~/.chloros/` |

### Plano galiojimo pabaiga ir atidėjimo laikotarpis

GUI rodomas plano galiojimo pabaigos laikas, kai jūsų licencija taps negaliojanti. Pasikartojančių mėnesinių prenumeratų atveju galiojimas baigiasi mėnesio pabaigoje; metinių prenumeratų atveju – praėjus metams nuo prenumeratos pradžios.

Chloros patvirtina jūsų licenciją prisijungus prie interneto, tačiau darbas neprisijungus prie interneto yra palaikomas per atidėjimo laikotarpį:

* Sėkmingi serverio patvirtinimai išsaugomi kešyje **5 minutes**, todėl įprastinio naudojimo metu licencijos patvirtinimų užklausų skaičius yra labai mažas.
* Pasirašyta, prie kompiuterio pririšta licencijos talpykla užtikrina ilgesnį veikimą neprisijungus prie interneto: **30 dienų mėnesiniams planams**ir**iki jūsų prenumeratos galiojimo pabaigos datos (ne daugiau kaip 365 dienos) metiniams planams**.
* Pasibaigus atidėjimo laikotarpiui, planas perjungiamas į nemokamą „Iron“ lygį, kol kompiuteris bent kartą prisijungs prie licencijų serverio; prieiga atnaujinama po kito sėkmingo patikrinimo.

### Įrenginių skaičiaus riba

Kiekvienas Chloros+ planas siūlo skirtingą registruotų įrenginių skaičių. Kiekvienas įrenginys, prie kurio prisijungiate naudodami „Chloros+“ paskyrą, įskaičiuojamas į jūsų registruotų įrenginių skaičių. Savo „MAPIR Cloud“ paskyros puslapyje galite pervardyti ir pašalinti įrenginį.

<table><thead><tr><th width="168.5999755859375" align="right">„Chloros+“ planas</th><th align="center">VARIS</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">AUKSO</th></tr></thead><tbody><tr><td align="right">Palaikomi įrenginiai</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>Tiksli jūsų paskyros įrenginių kvota nurodyta jūsų „MAPIR Cloud“ paskyros puslapyje. Atsijungus iš įrenginio, jo vieta patikimai atlaisvinama, o jau užregistruotas įrenginys visada gali vėl prisijungti, net jei paskyra pasiekė įrenginių limitą.
