---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# DUK

<details>

<summary>Ar galiu apdoroti vaizdus iš kamerų, kurios nėra MAPIR prekės ženklo, naudodamas Chloros?</summary>

Ne, Chloros palaiko tik MAPIR kamerų vaizdų apdorojimą. Daugiau informacijos rasite [palaikomų kamerų modelių sąraše](supported-cameras.md). Mes siūlome kitų kamerų vaizdų apdorojimą „MAPIR Cloud“ platformoje, pilną sąrašą rasite [čia](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Ar galiu kalibruoti vaizdus pagal atspindžio koeficientą be kalibravimo taikinio?</summary>

Ne. Jei kalibravimo taikinio vaizdas nebuvo užfiksuotas tuo pačiu metu, kai buvo užfiksuoti vaizdai be taikinio, negalėsite susieti vaizdo pikselių verčių su žinomu atspindžio procentu. Jei taip pat neįtrauksite MAPIR šviesos jutiklio žurnalo, aplinkos šviesos spektras nebus išmatuotas, o atspindžio rezultatai nebus tikslūs.

</details>

<details>

<summary>Ar galiu redaguoti vaizdus prieš apdorojimą Chloros?</summary>

Ne. Chloros daro prielaidą, kad įvestiniai duomenys nebuvo pakeisti. Nepakeiskite failų pavadinimų.

</details>

<details>

<summary>Ar galiu nustatyti savo MAPIR ir Survey3 kameras į automatinę ekspoziciją ir apdoroti vaizdus Chloros?</summary>

Ne. Survey3 vaizdų duomenų rinkiniai turi turėti fiksuotą/užfiksuotą ekspoziciją, taigi negalima naudoti automatinio užrakto greičio ar automatinio ISO. Visi to paties kameros modelio vaizdai turi turėti identišką užrakto greitį ir ISO (ekspoziciją).

</details>

<details>

<summary>Ar Chloros gali apdoroti ar analizuoti ortomozaikos vaizdus?</summary>

Ne. Palaikomi tik atskiri MAPIR fotoaparato vaizdai, o ne sujungti vaizdai, pvz., ortomozaikinis žemėlapis.

</details>

<details>

<summary>Kaip galima pagreitinti Chloros tikslo aptikimo etapą?</summary>

Failų naršyklės lentelėje iš anksto pasirinkus tikslinius vaizdus dešinėje skiltyje, Chloros bus nurodyta ieškoti kalibravimo tikslų tik tuose vaizduose, o tai žymiai pagreitins apdorojimą.

</details>

<details>

<summary>Jei ketinu įkelti savo vaizdus į <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">„MAPIR Cloud“,</a> ar turėčiau juos apdoroti „Chloros“ prieš įkeliant?</summary>

Jei planuojate įkelti į mūsų internetinę apdorojimo platformą [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), prieš įkeliant nuotraukų neredaguokite. „Cloud“ atliks visą tą patį apdorojimą ir dar daugiau.

</details>

<details>

<summary>Ar MAPIR kada nors palaikys X funkciją? Labai norėčiau, kad MAPIR siūlytų X.</summary>

Mes visada džiaugiamės gavę atsiliepimus apie mūsų produktus. Jei pastebėjote problemą su mūsų produktais arba turite pasiūlymų, kaip galėtume juos patobulinti, prašome [SUSISIEKTI SU MUMIS](https://www.mapir.camera/community/contact) ir pasidalinti savo mintimis. Didžioji dalis mūsų mokslinių tyrimų ir plėtros veiklos yra orientuota į klientų poreikių išklausymą.

</details>

<details>

<summary>Ar Chloros yra prieinamas Linux?</summary>

Taip! Chloros 1.1.0 palaiko Linux amd64 (x86_64) ir arm64 (NVIDIA Jetson JetPack 6) per `.deb` paketus. CLI ir Python SDK yra visiškai palaikomi Linux. Linux neturi GUI — visa sąveika vyksta per [CLI](CLI.md) arba [Python SDK](api-python-sdk.md). Daugiau informacijos rasite [Linux apžvalgoje](linux/linux-overview.md).

</details>

<details>

<summary>Ar galiu paleisti Chloros „NVIDIA Jetson“?</summary>

Taip! Chloros 1.1.0 palaiko „NVIDIA Jetson“ platformas, įskaitant „Jetson Nano“, „Orin Nano“, „Orin NX“ ir „AGX Orin“, kuriose veikia „JetPack 6“. Chloros automatiškai aptinka jūsų „Jetson“ modelį ir optimizuoja jo apdorojimo strategiją. Įrengimo ir diegimo instrukcijas rasite [„NVIDIA Jetson“ vadove](linux/nvidia-jetson-guide.md).

</details>

<details>

<summary>Ar Chloros automatiškai optimizuoja mano aparatinę įrangą?</summary>

Taip! Chloros 1.1.0 versijoje yra [dinaminis skaičiavimo pritaikymas](processing-architecture/dynamic-compute-adaptation.md), kuris automatiškai aptinka jūsų CPU, GPU, RAM ir (Jetson įrenginiuose) temperatūros jutiklius. Tada ji pasirenka optimalų apdorojimo būdą – nuo `GPU_PARALLEL` sistemose su didele atmintimi iki `GPU_SINGLE` ribotų išteklių įrenginiuose ir `CPU_PARALLEL` sistemose be NVIDIA GPU. Rankinis konfigūravimas nereikalingas.

</details>

<details>

<summary>Kas yra 4 sriegių apdorojimo vamzdynas?</summary>

„Chloros 1.1.0“ naudoja 4 sriegių konvejerinę architektūrą „Chloros+“ vartotojams: 1-asis srautas (aptikimas) įkelia vaizdus ir aptinka kalibravimo taškus, 2-asis srautas (kalibravimas) apskaičiuoja atspindžio kalibravimą, 3-asis srautas (apdorojimas) atlieka GPU pagreitintą debayeringą ir indeksų skaičiavimą, o 4-asis srautas (eksportavimas) įrašo išvesties failus. Siekiant maksimalaus našumo, keli vaizdai gali būti apdorojami skirtinguose srautuose vienu metu. Daugiau informacijos rasite skyriuje [Apdorojimo srautas](processing-architecture/processing-pipeline.md).

</details>

<details>

<summary>Kaip atlikti Chloros įdiegimo diagnostiką?</summary>

Naudokite komandą `selftest`, kad paleistumėte 7 sistemos diagnostikos testus, įskaitant versijos patikrinimą, prievadų prieinamumą, užpakalinės dalies paleidimą, API ryšį, sistemos informaciją, triukšmo šalinimo modelius ir CUDA prieinamumą:

```bash
chloros-cli selftest
```

Tai ypač naudinga Linux/Jetson sistemose, siekiant patikrinti GPU ir CUDA nustatymus.

</details>
