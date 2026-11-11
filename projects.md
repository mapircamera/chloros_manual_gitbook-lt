# Grafinė vartotojo sąsaja: Projektai

Chloros leidžia kurti projektus, kuriuos ateityje galima vėl atidaryti. Projektas yra paprastas aplankas (esantis jūsų projektų aplanke), kuriame yra:

* `project.json` — projekto nustatymai, failų sąrašas ir rodymo parametrai
* `cameras.json` — kameros ir matuoklių masyvai, prijungti, kai projektas buvo atidarytas, su jų nustatymais
* `sensors.json` — DAQ šviesos jutikliai, prijungti, kol projektas buvo atidarytas, bei kamerų ir jutiklių susiejimai
* jūsų užfiksuoti vaizdai, `.daq` įrašai ir apdorotų duomenų išvesties aplankai

Nėra jokio uždaros struktūros projekto failo formato — projektą sudaro aplankas ir jame esantys JSON failai, todėl projektus lengva kopijuoti, archyvuoti ir valdyti per [CLI](CLI.md) arba [Python SDK](api-python-sdk.md).

Jei to nepakeisite [Projekto nustatymuose](project-settings/project-settings.md), projekto aplankas bus `Documents\MAPIR\Chloros Projects` jūsų „Dokumentai“ aplanke (`~/Documents/MAPIR/Chloros Projects` Linux sistemoje).

{% hint style="warning" %}
**Windows: naudokite trumpus projekto aplankų pavadinimus, nebent būtų įjungti ilgi keliai.**Windows sistemoje ilgi keliai yra išjungti, todėl joks failo kelias negali būti ilgesnis nei 260 simbolių. Apdoroti produktai saugomi maždaug 130 simbolių žemiau projekto aplanko, todėl nuo 1.3.0 versijos Chloros atmeta pernelyg giliai įdėtą projekto aplanką – kai jį kuriate ar atidarote, arba kai pradedate apdorojimą – vietoj to, kad paleistų programą ir nieko neįrašytų. Pranešime nurodomas ilgiausias tinkamas projekto aplanko pavadinimas. Naudokite trumpesnį projekto aplanką arba įjunkite Windows ilgus kelius (diegimo programos parinktis**Įjungti Windows ilgus kelius**, pažymėta pagal numatytuosius nustatymus) ir iš naujo paleiskite Chloros. Žr. [Apdorojimo pradžia](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).
{% endhint %}

## Naujas projektas

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>Pagrindiniame meniu pasirinkite „Naujas projektas“ ir įveskite unikalų savo projekto pavadinimą.

Jei esate išsaugoję kokių nors projekto šablonų, po pavadinimo laukeliu pasirodys išskleidžiamas meniu **Pasirinkti šabloną** — pasirinkus vieną iš jų, naujas projektas bus pradėtas pagal to šablono nustatymus. Šablonai išsaugomi per [Projekto nustatymus](project-settings/project-settings.md): įveskite pavadinimą laukelyje „Išsaugoti projekto šablono pavadinimą“ ir spustelėkite išsaugojimo piktogramą.

## Atidaryti projektą

<figure><img src=".gitbook/assets/v120-open-project.jpg" alt=""><figcaption><p>Skiltyje „Atidaryti projektą“ pateikiamas visų jūsų projektų aplanke esančių projektų sąrašas, o apačioje yra nuoroda <strong>Atidaryti projektų aplanką</strong></p></figcaption></figure>

Pasirinkite „Atidaryti projektą“, kad pamatytumėte esamų projektų sąrašą projekto aplanke. Jei projektų nėra, antrinis šoninis meniu neatsidarys. Viršutiniame paveikslėlyje matomi keli GUI sukurti projektai (t1, t2, t3). Projektai „DATE\_TIME“ buvo sukurti naudojant CLI pagal numatytąją projektų pavadinimų schemą. Spustelėjus bet kurį projekto pavadinimą, jis bus atidarytas.

Spustelėjus mygtuką „Atidaryti projekto aplanką“ atsidarys jūsų kompiuterio failų naršyklė, nukreipta į projekto kelią. Projekto kelią galite pakeisti [Projekto nustatymuose](project-settings/project-settings.md).

Jei nuo paskutinio projekto atidarymo buvo perkelti arba ištrinti kokie nors projekto šaltinio vaizdo failai, Chloros vietoj tuščio tinklelio parodo dialogo langą, kuriame tiksliai nurodoma, kokių failų trūksta.

## Projekto kopijavimas

Ši funkcija prieinama, kai projektas yra atidarytas. Pasirinkite „Kopijuoti projektą“, kad dabartinį projektą nukopijuotumėte su nauju pavadinimu — Chloros pasiūlys kitą laisvą pavadinimą (pvz., „ManoProjektas (2)“) — ir kopija bus atidaryta iš karto.

## Pridėti failus

Atidarius projektą, pagrindiniame meniu pasirinkite „Pridėti failus“, kad į dabartinį projektą įtrauktumėte atskirus vaizdo failus. Ši funkcija atitinka failų naršyklės pridėjimo funkciją, tačiau patogumo dėlei yra prieinama tiesiogiai iš pagrindinio meniu.

## Pridėti aplanką

Atidarius projektą, pagrindiniame meniu pasirinkite „Pridėti aplanką“, kad į esamą projektą pridėtumėte vaizdų aplankus. Vienu kartu galite pasirinkti kelis aplankus. Pakartotiniai failai ignoruojami.

## Pradėti / Sustabdyti apdorojimą

Pridėjus failus į projektą, pagrindiniame meniu tampa prieinama parinktis „Pradėti apdorojimą“. Tai tas pats veiksmas, kaip ir paspaudimas mygtuko „Paleisti/Pradėti“ viršutiniame antrašte. Apdorojimo metu meniu punktas pasikeičia į „Sustabdyti apdorojimą“, kad galėtumėte sustabdyti apdorojimo procesą.

## Prijungti prie kameros / Prijungti prie šviesos jutiklio

Pagrindinio meniu apačioje yra du aparatinės įrangos sparčiųjų klavišų, prieinami tiek atidarius projektą, tiek jo neatidarius:

* **Prijungti prie kameros** — atidaro [„Kameros“ skirtuką](lattice/), skirtą prijungti „LATTICE“ kamerą arba kamerų masyvą.
* **Prijungti prie šviesos jutiklio** — atidaro [„Šviesos jutikliai“ skirtuką](daq/), skirtą prijungti DAQ šviesos jutiklį.

Prijungus įrangą, kai projektas yra atidarytas, ji išsaugoma projekte (žr. toliau). Jei projektas neatidarytas, jungtys galioja tik sesijos metu.

{% hint style="info" %}
Meniu punktai „Pridėti failus“, „Pridėti aplanką“ ir „Pradėti/Sustabdyti apdorojimą“ matomi arba įjungiami tik tada, kai projektas atidarytas ir failai jau pridėti. Jie suteikia greitą prieigą prie veiksmų, kurie taip pat prieinami per failų naršyklės šoninę juostą ir antraštės mygtukus.
{% endhint %}

## Projektai įsimena jūsų įrangą

Nauja 1.2.0 versijoje: projektas išsaugo įrangą, kurią prijungiate, kol jis yra atidarytas. Kameros ir matricos (kartu su kiekvienos kameros nustatymais, pavadinimais, spalvomis ir tinklelio išdėstymu) yra įrašomos į „`cameras.json`“, o šviesos jutikliai (kartu su pavadinimais, spalvomis ir kamerų priskyrimais) – į „`sensors.json`“ — automatiškai, jums dirbant.

Kai **vėl atidarote** projektą, Chloros iš karto neprisijungia prie jokios įrangos. Kiekviena pusė vėl prisijungia, kai pirmą kartą atidarote jai priklausančią kortelę:

* Atidarius kortelę **Kameros**, vėl prisijungiamos išsaugotos kameros ir matricos bei vėl pritaikomi jų išsaugoti nustatymai.
* Atidarius skirtuką **Šviesos jutikliai**, iš naujo prijungiami išsaugoti DAQ jutikliai ir vėl pritaikomi jų išsaugoti nustatymai, įskaitant dangtelį. Jutiklis, kurio „Cap“ meniu nustatytas į**„None“ (nuogas jutiklis)**, lieka nuogas. Ankstesnėje Chloros versijoje išsaugotame projekte nuogas jutiklis buvo užregistruotas kaip vienintelis pasirinkimas, todėl jis vėl veikia su saulės spindulių kosinusine korekcija; dar kartą nustatykite jį į „None“ ir išsaugokite projektą.

Tokiu būdu, atidarius projektą tik vaizdams peržiūrėti ar eksportuoti, kameros niekada nebus įjungtos transliacijai. Jei atidarius skirtuką nepavyksta rasti išsaugoto įrenginio, dialogo lange bus nurodyta, kurie įrenginiai yra neprieinami, kad galėtumėte juos vėl prijungti arba pašalinti.

## DAQ įrašai ir .daq failai projekte

* `.daq` įrašai, padaryti atidarius projektą (iš skirtuko „Šviesos jutikliai“ arba fiksavimo metu), **automatiškai pridedami prie projekto**.
* Importuoti `.daq` failai ir visi projekto įrašai yra išvardyti [Projekto nustatymų](project-settings/project-settings.md) skyriuje **„DAQ šviesos jutiklis“**, kiekvienas su savo apšvietimo korekcijos profiliu.
* Apdorojimo metu projekto `.daq` failai teikia žemyn nukreiptą apšvietimą atspindžio produktams — žr. [Išvesties vaizdo formatai](output-image-formats.md).

## Išsaugoto projekto vykdymas be grafinės sąsajos

Išsaugotą projektą galima vykdyti be grafinės sąsajos:

* **CLI**: „`chloros-cli project open / connect / capture / sensor / align / run`“ veikia pagal projekto aplanko kelią — žr. [CLI žinyną](reference/cli-reference.md).
* **SDK**: „`chloros_sdk.open_project(path)`“ grąžina projekto identifikatorių; `connect_all()` įjungia visas išsaugotas kameras, matricas ir šviesos jutiklius su jų išsaugotais nustatymais (šviesos jutikliai per veikiantį Chloros užkurtį) — žr. [SDK žinyną](reference/sdk-reference.md).
