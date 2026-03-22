# Chloros+ prisijungimas

## Chloros ir Chloros (naršyklė) prisijungimas

Vartotojo <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> šoniniame meniu galite prisijungti prie savo Chloros+ paskyros ir atrakinti papildomas funkcijas.

Prisijungus bus rodomi jūsų paskyros duomenys:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>## CLI Prisijungimas

Prisijunkite naudodami savo Chloros+ prisijungimo duomenis, kad įgalintumėte CLI apdorojimą. Linux (be GUI) versijoje tai yra vienintelis būdas aktyvuoti savo licenciją.

**Sintaksė:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK vartotojai**: Python SDK taip pat teikia programinį `logout()` metodą, skirtą išvalyti talpyklos prisijungimo duomenis. Išsamią informaciją rasite [Python SDK dokumentacijoje](api-python-sdk.md#logout).
{% endhint %}

**Pavyzdys:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Specialieji simboliai**: Slaptažodžius, kuriuose yra simbolių, pvz., `$`, `!`, arba tarpų, apgaubkite viengubomis kabutėmis.
{% endhint %}

**Rezultatas:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>### Prisijungimo duomenų saugykla

Į cache įrašyti prisijungimo duomenys saugomi platformai būdingoje vietoje:

| Platforma | Prisijungimo duomenų cache kelias |
| --- | --- |
| **Windows** | `%APPDATA%\Chloros\cache\` |
| **Linux** | `~/.cache/chloros/` |

### Plano galiojimo pabaiga

GUI rodomas plano galiojimo pabaigos laikas nurodo, kada jūsų licencija taps negaliojanti. Pasikartojančių mėnesinių prenumeratų atveju galiojimo pabaiga yra mėnesio pabaiga. Metinių prenumeratų atveju tai yra metai po to, kai pradėjote prenumeratą. Licencijos patikrinimui reikalingas kasmėnesinis interneto ryšys, o atidėjimo laikotarpis yra 30 dienų.

### Įrenginių skaičiaus riba

Kiekvienas Chloros+ planas siūlo skirtingą registruotų įrenginių skaičių. Kiekvienas įrenginys, į kurį prisijungiate naudodami Chloros+ paskyrą, bus įskaičiuotas į jūsų registruotų įrenginių skaičių. Įrenginį galite pervardyti ir pašalinti savo MAPIR Cloud paskyros puslapyje.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ planas</th><th align="center">VARIS</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">AUKSO</th></tr></thead><tbody><tr><td align="right">Palaikomi įrenginiai</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>
