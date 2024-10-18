# Sissejuhatus logimisse, monitoorimisse ja jälgitavusse

## Sisukord

1. [Mis on logimine?](#1-mis-on-logimine)
   - [Definitsioon](#definitsioon)
   - [Olulisus IT-süsteemides](#olulisus-it-süsteemides)
2. [Logimise põhikomponendid](#2-logimise-põhikomponendid)
   - [Logiallikad](#logiallikad)
   - [Logivormingud](#logivormingud)
   - [Ajatempli olulisus](#ajatempli-olulisus)
3. [Levinud logimistasemed](#3-levinud-logimistasemed)
4. [Logimise parimad tavad](#4-logimise-parimad-tavad)
5. [Sissejuhatus monitoorimisse](#5-sissejuhatus-monitoorimisse)
6. [Monitoorimise tüübid](#6-monitoorimise-tüübid)
   - [Infrastruktuuri monitoorimine](#61-infrastruktuuri-monitoorimine-süsteemi-vundamendi-jälgimine)
   - [Rakenduse monitoorimine](#62-rakenduse-monitoorimine-tarkvara-tervise-jälgimine)
   - [Võrgu monitoorimine](#63-võrgu-monitoorimine-digitaalse-närvisüsteemi-jälgimine)
7. [Olulised monitoorimise mõõdikud](#7-olulised-monitoorimise-mõõdikud-süsteemi-tervise-indikaatorid)
   - [CPU kasutus](#71-cpu-kasutus-süsteemi-ajutegevuse-mõõtmine)
   - [Mälu kasutus](#72-mälu-kasutus-süsteemi-lühimälu-jälgimine)
   - [Ketta I/O](#73-ketta-io-andmete-liikumise-jälgimine)
   - [Võrguliiklus](#74-võrguliiklus-digitaalse-liikluse-jälgimine)
8. [Monitoorimise tööriistade ülevaade](#8-monitoorimise-tööriistade-ülevaade-võimsad-lahendused-süsteemi-jälgimiseks)
   - [Grafana](#81-grafana-andmete-visualiseerimise-võlur)
   - [ELK Stack](#82-elk-stack-logide-analüüsi-võimas-kolmik)
   - [TICK Stack](#83-tick-stack-ajaandmete-monitoorimise-ekspert)
9. [Mis on jälgitavus (Observability)?](#9-mis-on-jälgitavus-observability-süsteemi-läbipaistvuse-uus-tase)
10. [Jälgitavuse kolm sammast](#10-jälgitavuse-kolm-sammast-süsteemi-tervikliku-mõistmise-alused)
    - [Logid](#101-logid-süsteemi-detailne-päevik)
    - [Mõõdikud](#102-mõõdikud-süsteemi-tervise-numbrilised-näitajad)
    - [Jäljed (Traces)](#103-jäljed-traces-päringu-teekonna-kaardistamine)
11. [Miks jälgitavus on oluline](#11-miks-jälgitavus-on-oluline-süsteemide-efektiivse-haldamise-võti)
12. [Logimine vs. monitoorimine vs. jälgitavus](#12-logimine-vs-monitoorimine-vs-jälgitavus-võrdlev-analüüs)
13. [Reaaleluline stsenaarium](#13-reaaleluline-stsenaarium-e-kaubanduse-veebisaidi-probleemide-lahendamine)
14. [Korrektse logimise, monitoorimise ja jälgitavuse eelised](#14-korrektse-logimise-monitoorimise-ja-jälgitavuse-eelised)
15. [Levinud väljakutsed](#15-levinud-väljakutsed-logimise-monitoorimise-ja-jälgitavuse-rakendamisel)
    - [Andmete üleküllus](#151-andmete-üleküllus-teabe-üleujutuse-haldamine)
    - [Tööriistade keerukus](#152-tööriistade-keerukus-tehniliste-lahenduste-haldamine)
    - [Ressursinõuded](#153-ressursinõuded-arvutus--ja-salvestusressursside-haldamine)
16. [Alustamine](#16-alustamine-logimise-monitoorimise-ja-jälgitavusega)


## 1. Logimine: Süsteemi mälu ja ajalooline perspektiiv

Logimine on nagu süsteemi päevik või mälu. See on protsess, mille käigus rakendused ja süsteemid salvestavad olulist teavet oma toimimise kohta. Kujutage ette, et teie süsteem on keeruline masin - logimine on nagu detailne kasutusjuhend, mis kirjeldab iga toimingut, mida masin teeb.

### Logimise olulisus IT-süsteemides

Logimise tähtsust ei saa alahinnata. See on kui arheoloogiline kaevamine digitaalses maailmas, võimaldades meil rekonstrueerida sündmusi ja mõista, mis täpselt juhtus ja miks.

1. **Vigade tuvastamine ja parandamine**: 
   Kujutage ette olukorda, kus kasutaja teatab, et veebisait lakkas töötamast eile õhtul kell 22:15. Ilma logideta oleksime kui detektiivid ilma ühtegi jäljeta. Logid võimaldavad meil "ajas tagasi minna" ja näha täpselt, mis sel hetkel juhtus - kas server jooksis mälust tühjaks? Kas andmebaas muutus kättesaamatuks? Logid annavad meile konteksti ja suuna probleemi lahendamiseks.

2. **Turvalisuse tagamine**: 
   Logid on nagu turvakaamera salvestised digitaalses maailmas. Kui toimub turvaintsident, näiteks volitamata sisselogimine, on logid need, mis aitavad meil tuvastada, millal ja kuidas rünnak toimus, milliseid ressursse kasutati ja potentsiaalselt isegi ründaja identiteedi.

3. **Jõudluse optimeerimine**: 
   Logid võivad paljastada mustreid, mis muidu jääksid märkamata. Näiteks võime avastada, et teatud andmebaasipäring muutub aeglaseks iga päev kell 14:00. See võib viidata sellele, et meil on vaja optimeerida päringut või suurendada ressursse sel konkreetsel ajal.

4. **Vastavuse tagamine**: 
   Paljudes reguleeritud valdkondades, nagu finants- või tervishoiusektor, on teatud sündmuste logimine seadusega nõutud. Logid on meie tõend, et järgime vajalikke protokolle ja regulatsioone.

## 2. Logimise põhikomponendid: Ehituskivid efektiivseks logimiseks

Efektiivne logimine ei ole lihtsalt suvalistele sündmustele "println" käsu lisamine. See on struktureeritud ja läbimõeldud protsess, mis koosneb mitmest kriitilisest komponendist.

### Logiallikad: Kust info pärineb?

Logiallikad on kui erinevad peatükid meie süsteemi päevikus, igaüks keskendudes erinevatele aspektidele:

1. **Rakendused**: 
   Need on meie süsteemi peamised tegelased. Veebiserverid, nagu Apache või Nginx, salvestavad iga päringu ja vastuse. Andmebaasid, nagu PostgreSQL või MongoDB, logivad päringuid ja indekseerimisoperatsioone. Taustateenused, näiteks tööjärjekorra haldajad, salvestavad teavet töötlemata ja lõpetatud tööde kohta.

2. **Operatsioonisüsteemid**: 
   Need on kui meie süsteemi elukeskkond. Windows Event Log või Linux syslog salvestavad süsteemisündmusi, nagu käivitamised, sulgemised ja riistvara muudatused. Turvateated, nagu ebaõnnestunud sisselogimiskatsed või ebatavalised failisüsteemi muudatused, on kriitilise tähtsusega turvalisuse tagamisel.

3. **Võrguseadmed**: 
   Need on meie süsteemi teed ja sillad. Ruuterid ja lülitid logivad võrguliiklust, mis aitab tuvastada võrguprobleeme või potentsiaalseid turvaohtusid. Tulemüürid salvestavad blokeeritud ja lubatud ühendusi, mis on oluline turvalisuse jälgimiseks.

### Logivormingud: Kuidas infot struktureeritakse?

Logivormingud on kui keeled, milles meie süsteem "räägib". Erinevad vormingud sobivad erinevateks olukordadeks:

1. **Tavaline tekst**: 
   See on kui käsitsi kirjutatud märkmed. Lihtne lugeda inimsilmale, kuid raskesti analüüsitav suurtes kogustes. Näide:
   ```
   2024-03-15 14:30:45 - INFO - Kasutaja 'jaan@näide.ee' logis sisse
   ```

2. **Struktureeritud vormingud (JSON, XML)**: 
   Need on kui hästi organiseeritud tabelid. Lihtsustavad automaatset töötlemist ja analüüsi. JSON näide:
   ```json
   {
     "timestamp": "2024-03-15T14:30:45Z",
     "level": "INFO",
     "message": "Kasutaja logis sisse",
     "user": "jaan@näide.ee"
   }
   ```

3. **Binaarformaadid**: 
   Need on kui krüpteeritud sõnumid. Kompaktsed ja efektiivsed, kuid vajavad spetsiaalseid tööriistu lugemiseks. Kasutatakse tihti olukordades, kus jõudlus on kriitiline.

### Ajatempli olulisus: Sündmuste ajastamine

Ajatempel on kui kuupäev ajalooraamatus - ilma selleta oleks raske mõista sündmuste järjekorda või konteksti.

- **Täpne ajastamine**: Võimaldab meil luua täpse ajalise järjestuse sündmustest. See on eriti oluline, kui uurime intsidente, mis hõlmavad mitut süsteemi või komponenti.
- **Ajavööndid**: UTC (Coordinated Universal Time) kasutamine logides on hea tava, kuna see väldib segadust erinevate ajavööndite vahel, eriti rahvusvahelistes või hajutatud süsteemides.

Näide heast ajatempli kasutamisest:
```
2024-03-15T14:30:45.332Z - INFO - Andmebaasi päring lõpetatud (kestus: 1.23s)
```

## 3. Levinud logimistasemed: Sõnumite olulisuse hierarhia

Logimistasemed on kui häiresignaalid - igaüks näitab erineva tähtsusega infot. Nende mõistmine ja õige kasutamine on kriitilise tähtsusega efektiivse logimissüsteemi loomisel.

1. **DEBUG**: 
   See on kui mikroskoobiga vaatamine. Sisaldab üksikasjalikku teavet, mis on kasulik arendajatele ja süsteemiadministraatoritele süvitsi minevaks analüüsiks. 
   Näide: `DEBUG - Andmebaasi päring: SELECT * FROM users WHERE id = 12345`

2. **INFO**: 
   See on tavaline päevik. Salvestab üldist teavet süsteemi tavapärase toimimise kohta.
   Näide: `INFO - Rakendus käivitatud edukalt, kuulab pordil 8080`

3. **WARN**: 
   See on nagu kollane tuluke armatuurlaual. Näitab potentsiaalseid probleeme, mis ei takista süsteemi toimimist, kuid võivad vajada tähelepanu.
   Näide: `WARN - Vaba ketaruum alla 10%, kaaluge puhastamist`

4. **ERROR**: 
   See on punane tuluke. Näitab tõsiseid probleeme, mis takistavad teatud funktsionaalsust.
   Näide: `ERROR - Andmebaasiga ühenduse loomine ebaõnnestus: Connection timeout`

5. **FATAL**: 
   See on kui häiresireen. Näitab kriitilisi vigu, mis põhjustavad rakenduse või süsteemi täieliku seiskumise.
   Näide: `FATAL - Mälu ammendunud, rakendus sulgub`

Nende tasemete õige kasutamine aitab kiiresti tuvastada ja prioriseerida probleeme. Näiteks, kui näeme FATAL või ERROR taseme logisid, teame, et olukord vajab kohest tähelepanu.

## 4. Logimise parimad tavad: Kunst ja teadus

Efektiivne logimine on nii kunst kui ka teadus. See nõuab tasakaalu piisava info ja liigse müra vahel.

### Mida logida?

1. **Rakenduse oleku muutused**: 
   Logige olulised sündmused nagu rakenduse käivitamine, sulgemine või konfiguratsiooni muutused.
   Näide: `INFO - Rakendus uuendatud versioonile 2.1.3`

2. **Kasutajate tegevused**: 
   Jälgige olulisi kasutajategevusi, eriti neid, mis mõjutavad andmeid või süsteemi seisundit.
   Näide: `INFO - Kasutaja ID 12345 muutis oma profiilipilti`

3. **Vead ja erandid**: 
   Logige kõik vead koos täieliku stacktrace'iga, et lihtsustada vigade silumist.
   Näide:
   ```
   ERROR - Null pointer exception töötlemisel:
   java.lang.NullPointerException
       at com.example.MyClass.processData(MyClass.java:42)
       at com.example.Main.main(Main.java:15)
   ```

4. **Jõudlusega seotud sündmused**: 
   Jälgige aeglasi päringuid, ressursside ammendumist jms.
   Näide: `WARN - Andmebaasi päring võttis aega üle 5 sekundi: SELECT * FROM orders WHERE ...`

### Mida mitte logida?

1. **Tundlikud andmed**: 
   Ärge kunagi logige paroole, krediitkaardi numbreid või muid tundlikke andmeid.
   Halb näide: `INFO - Kasutaja sisselogimine õnnestus, parool: 12345678`
   Hea näide: `INFO - Kasutaja 'jaan@näide.ee' sisselogimine õnnestus`

2. **Isikuandmed**: 
   Olge ettevaatlik isikuandmete logimisega, eriti GDPR ja teiste privaatsusseaduste valguses.
   Halb näide: `INFO - Kasutaja Jaan Tamm (isikukood 38501010000) sisenes süsteemi`
   Hea näide: `INFO - Kasutaja ID 12345 sisenes süsteemi`

3. **Liigne detailsus tavalise töö kohta**: 
   Vältige liiga detailset logimist tavaliste operatsioonide kohta, mis võib põhjustada "logi üleujutust".
   Halb näide: `DEBUG - Andmebaasi ühendus avatud... Andmebaasi ühendus suletud` (iga päringu kohta)
   Hea näide: `DEBUG - Pikaajaline andmebaasi ühendus loodud`

Efektiivne logimine nõuab pidevat tähelepanu ja kohandamist. Regulaarne logide ülevaatus ja analüüs aitab teil mõista, millist infot te tegelikult vajate ja millist mitte.


## 5. Sissejuhatus monitoorimisse: Süsteemi pulsi jälgimine

Monitoorimine on IT-süsteemide haldamise üks alustalasid. Kui logimine on nagu süsteemi päevik, siis monitoorimist võib võrrelda pideva tervisekontrolliga. See on protsess, mille käigus jälgitakse ja mõõdetakse süsteemi või rakenduse jõudlust, kättesaadavust ja terviklikkust reaalajas või peaaegu reaalajas.

### Monitoorimise definitsioon ja olulisus

Monitoorimine on süstemaatiline protsess, mis hõlmab andmete kogumist, analüüsimist ja visualiseerimist, et anda ülevaade süsteemi tervisest ja jõudlusest. See on kui pideva valvsuse seisund, mis võimaldab IT-meeskondadel kiiresti reageerida probleemidele ja optimeerida süsteemi toimimist.

Monitoorimise olulisus seisneb mitmes aspektis:

1. **Ennetav probleemide tuvastamine**: 
   Monitoorimine toimib kui varajase hoiatuse süsteem. See võimaldab tuvastada potentsiaalseid probleeme enne, kui need muutuvad kriitiliseks ja mõjutavad lõppkasutajaid. 
   
   Näide: Kujutage ette e-kaubanduse platvormi, kus monitoorimissüsteem tuvastab, et andmebaasi päringutel kulub järjest rohkem aega. See võib viidata lähenevale jõudlusprobleemile. Tänu varasele tuvastamisele saab IT-meeskond sekkuda, optimeerides andmebaasi või suurendades ressursse, enne kui veebisait muutub kasutajate jaoks aeglaseks.

2. **Jõudluse optimeerimine**: 
   Pidev jõudluse jälgimine aitab tuvastada kitsaskohti ja optimeerimise võimalusi. See võimaldab IT-meeskondadel teha andmepõhiseid otsuseid ressursside jaotamise ja süsteemi arhitektuuri osas.
   
   Näide: Monitoorimisandmed võivad näidata, et teatud mikroteenused tarbivad ebaproportsionaalselt palju ressursse. See info võib viia teenuse ümberkujundamiseni või ressursside ümberjaotamiseni, parandades kogu süsteemi jõudlust.

3. **Trendide tuvastamine**: 
   Pikaajaline monitoorimine võimaldab tuvastada mustreid ja trende süsteemi käitumises. See on eriti väärtuslik ressursside planeerimiseks ja tulevaste vajaduste prognoosimiseks.
   
   Näide: Monitoorimisandmed võivad näidata, et süsteemi koormus kasvab järjepidevalt 5% kuus. See info võimaldab IT-meeskonnal planeerida infrastruktuuri laiendamist õigeaegselt, vältides ootamatuid ülekoormusi.

4. **Intsidentide kiire lahendamine**: 
   Kui probleemid siiski tekivad, aitab monitoorimine neid kiiresti tuvastada ja lokaliseerida, vähendades keskmist aja kulu probleemide lahendamiseks (MTTR - Mean Time To Resolve).
   
   Näide: Kui veebiteenuse monitooring näitab äkilist latentsuse kasvu, saab IT-meeskond koheselt alustada probleemi uurimist, kasutades monitoorimistööriistade pakutavaid üksikasjalikke andmeid, et tuvastada probleemi allikas (näiteks ülekoormatud andmebaas või võrguprobleem).

### Monitoorimise vs. logimise erinevused

Kuigi monitoorimine ja logimine on tihedalt seotud ja sageli üksteist täiendavad, on neil mõned olulised erinevused:

1. **Fookus**:
   - Logimine keskendub üksikutele sündmustele ja nende detailidele.
   - Monitoorimine keskendub süsteemi üldisele seisundile ja jõudlusele aja jooksul.

2. **Ajaline perspektiiv**:
   - Logid on tavaliselt sündmuspõhised ja annavad üksikasjaliku pildi minevikus toimunust.
   - Monitoorimine on pidev protsess, mis annab reaalajas ülevaate süsteemi seisundist.

3. **Andmete tüüp**:
   - Logid sisaldavad tavaliselt tekstipõhist teavet konkreetsete sündmuste kohta.
   - Monitoorimine keskendub enamasti numbrilistele mõõdikutele ja nende muutumisele ajas.

4. **Kasutus**:
   - Logisid kasutatakse sageli sügavamaks analüüsiks ja probleemide uurimiseks pärast nende ilmnemist.
   - Monitoorimist kasutatakse reaalajas jälgimiseks ja kiireks reageerimiseks.

Näide: 
- Logimine: `ERROR - Andmebaasi päring ebaõnnestus: Connection timeout (query: SELECT * FROM users WHERE ...)`
- Monitoorimine: Graafik, mis näitab andmebaasi päringu keskmist latentsust viimase 24 tunni jooksul.

### Monitoorimise komponendid

Efektiivne monitoorimissüsteem koosneb tavaliselt mitmest komponendist:

1. **Andmete kogumine**: 
   Agendid või kollektorid, mis koguvad andmeid erinevatest allikatest (serverid, rakendused, võrguseadmed).

2. **Andmete töötlemine**: 
   Süsteemid, mis töötlevad ja agregeeriuvad kogutud andmeid, muutes need analüüsitavaks.

3. **Andmete salvestamine**: 
   Andmebaasid või muud salvestussüsteemid, mis säilitavad kogutud ja töödeldud andmeid.

4. **Visualiseerimine**: 
   Dashboardid ja graafikud, mis esitavad kogutud andmeid visuaalselt arusaadaval kujul.

5. **Hoiatussüsteem**: 
   Mehhanismid, mis genereerivad teateid või häireid, kui jälgitavad näitajad ületavad eelmääratud piirmäärasid.

6. **Analüüsitööriistad**: 
   Vahendid, mis võimaldavad süvaanalüüsi ja uurimist kogutud andmete põhjal.

Näide monitoorimise töövoost:
1. Agent serveris kogub andmeid CPU kasutuse kohta.
2. Andmed saadetakse kesksetesse süsteemi töötlemiseks.
3. Töödeldud andmed salvestatakse ajalooliseks analüüsiks.
4. Dashboard näitab CPU kasutuse graafikut reaalajas.
5. Kui CPU kasutus ületab 90%, saadetakse hoiatus IT-meeskonnale.
6. IT-meeskond kasutab analüüsitööriistu, et uurida kõrge CPU kasutuse põhjuseid.

Monitoorimine on kriitilise tähtsusega tööriist tänapäeva keerukate IT-süsteemide haldamisel. See võimaldab organisatsioonidel ennetavalt hallata oma infrastruktuuri, optimeerida jõudlust ja tagada teenuste kõrge kvaliteet. Järgmistes osades vaatame lähemalt erinevaid monitoorimise tüüpe ja olulisi mõõdikuid, mida jälgida.

## 6. Monitoorimise tüübid: Süsteemi terviklik jälgimine

Efektiivne monitoorimine hõlmab erinevaid aspekte IT-infrastruktuurist. Igal monitoorimise tüübil on oma spetsiifiline fookus ja eesmärk, mis aitab luua tervikliku pildi süsteemi toimimisest. Vaatame lähemalt kolme peamist monitoorimise tüüpi: infrastruktuuri monitoorimine, rakenduse monitoorimine ja võrgu monitoorimine.

### 6.1 Infrastruktuuri monitoorimine: Süsteemi vundamendi jälgimine

Infrastruktuuri monitoorimine keskendub riistvara ja operatsioonisüsteemi ressursside jälgimisele. See on kui süsteemi füüsilise tervise kontroll.

#### Olulised aspektid:

1. **Riistvara jõudlus**: 
   Jälgitakse füüsiliste või virtuaalsete serverite põhiparameetreid.
   
2. **Ressursside kasutus**: 
   Jälgitakse, kuidas süsteem kasutab saadaolevaid ressursse.

3. **Võimsuse planeerimine**: 
   Aitab ennustada tulevasi ressursivajadusi ja planeerida laienemist.

#### Näited jälgitavatest parameetritest:

- **CPU kasutus**: Protsessori koormuse jälgimine aitab tuvastada ülekoormust või ebaefektiivseid protsesse.
  Näide: Kui CPU kasutus on püsivalt üle 80%, võib see viidata vajadusele suurendada arvutusvõimsust või optimeerida rakendusi.

- **Mälu kasutus**: RAM-i jälgimine aitab tuvastada mälulekkeid või ebapiisavat mälumahtu.
  Näide: Kui vaba mälu hulk langeb alla 10%, võib see põhjustada süsteemi aeglustumist või rakenduste tõrkeid.

- **Kettakasutus**: Kõvaketta ruumi ja I/O operatsioonide jälgimine.
  Näide: Kui ketta I/O on kõrge, kuid CPU kasutus madal, võib see viidata ebaefektiivsetele andmebaasipäringutele.

- **Võrguliiklus**: Sissetuleva ja väljamineva võrguliikluse jälgimine.
  Näide: Ootamatu kõrge võrguliiklus võib viidata DDoS rünnakule või vigasele rakendusele.

#### Infrastruktuuri monitoorimise tähtsus:

1. **Proaktiivne probleemide ennetamine**: 
   Võimaldab tuvastada potentsiaalseid probleeme enne nende eskaleerumist.
   Näide: Kui monitooring näitab, et ketta vaba ruum väheneb kiiresti, saab IT-meeskond rakendada meetmeid (nt. logide puhastamine, lisaruumi eraldamine) enne, kui ketas täis saab ja süsteemid seiskuvad.

2. **Ressursside optimeerimine**: 
   Aitab tuvastada alarakendatud või ülekoormatud ressursse.
   Näide: Kui monitooring näitab, et mõned serverid on püsivalt madala koormusega, võib kaaluda ressursside konsolideerimist või virtualiseerimist.

3. **Jõudluse parandamine**: 
   Võimaldab tuvastada jõudlusprobleemide algpõhjuseid.
   Näide: Kui rakendus muutub aeglaseks ja infrastruktuuri monitooring näitab kõrget CPU kasutust, saab keskenduda koodi optimeerimisele või skaleerimisele.

### 6.2 Rakenduse monitoorimine: Tarkvara tervise jälgimine

Rakenduse monitoorimine keskendub tarkvara toimimise jälgimisele. See annab ülevaate, kuidas rakendus käitub reaalses kasutuskeskkonnas.

#### Olulised aspektid:

1. **Jõudlus**: Kuidas rakendus reageerib erinevate koormuste all.
2. **Kättesaadavus**: Kas rakendus on kasutajatele ligipääsetav.
3. **Funktsionaalsus**: Kas kõik rakenduse osad töötavad ootuspäraselt.
4. **Kasutajakogemus**: Kuidas rakenduse toimimine mõjutab lõppkasutajaid.

#### Näited jälgitavatest parameetritest:

- **Vastamise aeg**: Kui kiiresti rakendus reageerib päringutele.
  Näide: Kui veebilehe laadimine võtab keskmiselt üle 3 sekundi, võib see viidata optimeerimise vajadusele.

- **Läbilaskevõime**: Kui palju päringuid rakendus suudab töödelda ajaühikus.
  Näide: E-poe rakendus peaks suutma toime tulla 1000 samaaegse kasutajaga ilma jõudluse languseta.

- **Veamäär**: Kui sageli esinevad tõrked või vead.
  Näide: Kui rohkem kui 1% API kutsetest ebaõnnestub, võib see viidata tõsisele probleemile.

- **Kasutajate arv**: Aktiivsete kasutajate arv ja nende käitumine.
  Näide: Kui aktiivsete kasutajate arv langeb järsult, võib see viidata probleemile rakenduse funktsionaalsuses.

#### Rakenduse monitoorimise tähtsus:

1. **Kasutajakogemuse parandamine**: 
   Võimaldab tuvastada ja lahendada probleeme, mis mõjutavad otseselt lõppkasutajaid.
   Näide: Kui monitooring näitab, et teatud piirkonna kasutajatel on pikad laadimisajad, võib kaaluda sisu edastusvõrgu (CDN) kasutamist selles piirkonnas.

2. **Äriliste eesmärkide toetamine**: 
   Aitab tagada, et rakendus toetab organisatsiooni ärilisi eesmärke.
   Näide: E-poe puhul võib jälgida ostukorvi hülgamise määra ja selle seost rakenduse jõudlusega.

3. **Arendusprotsessi toetamine**: 
   Annab väärtuslikku tagasisidet arendusmeeskonnale.
   Näide: Kui uue versiooni väljalaskmisel täheldatakse veamäära tõusu, saab kiiresti tuvastada ja parandada probleemse koodi.

### 6.3 Võrgu monitoorimine: Digitaalse närvisüsteemi jälgimine

Võrgu monitoorimine keskendub andmete liikumise ja võrguseadmete toimimise jälgimisele. See on kui organisatsiooni digitaalse närvisüsteemi tervisekontroll.

#### Olulised aspektid:

1. **Võrgu jõudlus**: Kui kiiresti ja efektiivselt andmed liiguvad.
2. **Võrgu kättesaadavus**: Kas kõik võrgu osad on toimivad ja ligipääsetavad.
3. **Võrgu turvalisus**: Kas võrgus esineb kahtlast tegevust.

#### Näited jälgitavatest parameetritest:

- **Latentsus**: Aeg, mis kulub andmepakettide liikumiseks võrgus.
  Näide: Kui latentsus videokonverentsi rakenduses tõuseb üle 150ms, võib see põhjustada katkendlikku heli või pilti.

- **Pakettide kadu**: Kui palju andmepakette läheb võrgus kaduma.
  Näide: Kui pakettide kadu on üle 1%, võib see viidata võrguseadmete probleemidele või ülekoormatud ühendustele.

- **Läbilaskevõime**: Kui palju andmeid saab võrk edastada ajaühikus.
  Näide: Kui failiserveri läbilaskevõime langeb alla 50% tavapärasest, võib see viidata võrguprobleemidele või riistvara tõrgetele.

- **Võrguseadmete olek**: Ruuterite, lülitite ja muude võrguseadmete toimimine.
  Näide: Kui põhiruuter näitab kõrget CPU kasutust, võib see viidata konfiguratsiooniveale või rünnakule.

#### Võrgu monitoorimise tähtsus:

1. **Katkestuste minimeerimine**: 
   Võimaldab kiiresti tuvastada ja lahendada võrguprobleeme.
   Näide: Kui monitooring tuvastab, et teatud võrgusegment on muutunud aeglaseks, saab IT-meeskond kiiresti uurida ja vajadusel ümber konfigureerida liikluse marsruudid.

2. **Turvalisuse tagamine**: 
   Aitab tuvastada võimalikke turvaohte.
   Näide: Ebatavaline võrguliiklus teatud serverist võib viidata pahavara nakatumisele.

3. **Võrgu planeerimine**: 
   Annab infot võrgu laiendamise ja uuendamise vajaduste kohta.
   Näide: Kui teatud võrgulingi läbilaskevõime on püsivalt üle 80%, võib see näidata vajadust suurendada lingi võimsust.

Iga monitoorimise tüüp annab oma unikaalse vaate süsteemi toimimisele. Nende kombineerimine võimaldab luua tervikliku pildi IT-infrastruktuurist, mis omakorda võimaldab ennetavat probleemide lahendamist, paremat ressursside planeerimist ja üldist süsteemi optimeerimist. Järgmises osas vaatame lähemalt konkreetseid mõõdikuid, mida erinevate monitoorimise tüüpide puhul jälgida.

## 7. Olulised monitoorimise mõõdikud: Süsteemi tervise indikaatorid

Monitoorimise mõõdikud on kui süsteemi elutähtsad näitajad. Need annavad meile kiire ülevaate süsteemi tervisest ja jõudlusest. Selles osas keskendume neljale põhilisele mõõdikule: CPU kasutus, mälu kasutus, ketta I/O ja võrguliiklus. Iga mõõdik annab meile unikaalse vaate süsteemi toimimisele ja võimalikele probleemidele.

### 7.1 CPU kasutus: Süsteemi ajutegevuse mõõtmine

CPU (Central Processing Unit) ehk protsessor on arvuti "aju". CPU kasutuse jälgimine on kriitilise tähtsusega süsteemi üldise jõudluse hindamisel.

#### Mõõtmine:
- Mõõdetakse protsentides (0-100%).
- Näitab, kui suure osa ajast on protsessor hõivatud tööülesannete täitmisega.

#### Tõlgendamine:
- **Madal kasutus (0-20%)**: Tavaliselt viitab sellele, et süsteem on alakoormatud või ootel.
- **Keskmine kasutus (20-70%)**: Enamasti optimaalne vahemik paljude süsteemide jaoks.
- **Kõrge kasutus (70-100%)**: Võib viidata ülekoormusele või ressursimahukale protsessile.

#### Näited ja praktilised kaalutlused:
1. **Püsivalt kõrge CPU kasutus (>90%)**:
   - Võimalik põhjus: Ressursimahukas protsess või rakendus.
   - Tegevus: Tuvastage kõrget CPU kasutust põhjustav protsess (näiteks kasutades `top` käsku Linuxis) ja optimeerige või skaleerige ressursse.

2. **Järsk CPU kasutuse tõus**:
   - Võimalik põhjus: Taustal käivitunud uuendused, viirusetõrje skaneerimine või pahavara.
   - Tegevus: Kontrollige hiljuti käivitatud protsesse ja uurige ebatavalisi tegevusi.

3. **CPU kasutuse kõikumine**:
   - Võimalik põhjus: Perioodilised ülesanded või ebaühtlane koormus.
   - Tegevus: Kaaluge ülesannete ajastamist või koormuse ühtlustamist.

#### Parimad tavad:
- Seadke hoiatused kõrge CPU kasutuse jaoks (näiteks >80% üle 5 minuti).
- Jälgige CPU kasutust erinevatel tuumadel mitmetuumaliste protsessorite puhul.
- Analüüsige CPU kasutuse trende pikema aja jooksul ressursside planeerimiseks.

### 7.2 Mälu kasutus: Süsteemi lühimälu jälgimine

Mälu (RAM - Random Access Memory) on süsteemi "lühimälu". See mõjutab otseselt süsteemi võimet kiiresti töödelda andmeid ja hoida töös mitut rakendust korraga.

#### Mõõtmine:
- Mõõdetakse absoluutväärtusena (baitides, megabaitides, gigabaitides) või protsentides.
- Jälgitakse nii füüsilist mälu kui ka virtuaalmälu (swap) kasutust.

#### Tõlgendamine:
- **Madal kasutus (<50%)**: Tavaliselt näitab, et süsteemil on piisavalt vaba mälu.
- **Keskmine kasutus (50-80%)**: Normaalne töörežiim paljude süsteemide jaoks.
- **Kõrge kasutus (>80%)**: Võib viidata mälulekete või ebapiisava mälumahu probleemidele.

#### Näited ja praktilised kaalutlused:
1. **Järk-järguline mälukasutuse suurenemine**:
   - Võimalik põhjus: Mäluleke rakenduses.
   - Tegevus: Tuvastage problemaatiline rakendus ja kaaluge selle taaskäivitamist või koodi optimeerimist.

2. **Kõrge swap-mälu kasutus**:
   - Võimalik põhjus: Ebapiisav füüsiline mälu.
   - Tegevus: Kaaluge mälu lisamist või vähendage samaaegselt töötavate rakenduste arvu.

3. **Äkiline mälukasutuse tõus**:
   - Võimalik põhjus: Uue ressursimahuka protsessi käivitamine.
   - Tegevus: Kontrollige hiljuti käivitatud protsesse ja nende mäluvajadust.

#### Parimad tavad:
- Jälgige nii kasutatavat kui ka vaba mälu.
- Seadke hoiatused kõrge mälukasutuse ja intensiivse swap-kasutuse jaoks.
- Analüüsige mälukasutuse mustreid, et optimeerida rakenduste mälukasutust.

### 7.3 Ketta I/O: Andmete liikumise jälgimine

Ketta I/O (Input/Output) mõõdab, kui kiiresti süsteem suudab lugeda ja kirjutada andmeid kettale. See on kriitilise tähtsusega andmemahukatele rakendustele ja andmebaasidele.

#### Mõõtmine:
- Mõõdetakse tavaliselt operatsioonide arvuna sekundis (IOPS) või andmemahuna sekundis (MB/s).
- Jälgitakse nii lugemis- kui ka kirjutamisoperatsioone.

#### Tõlgendamine:
- **Madal I/O**: Näitab, et süsteem ei ole hetkel intensiivselt ketast kasutamas.
- **Keskmine I/O**: Sõltub süsteemi tavapärasest koormusest.
- **Kõrge I/O**: Võib viidata intensiivsele andmetöötlusele, andmebaasi probleemidele või ebaefektiivsetele päringutele.

#### Näited ja praktilised kaalutlused:
1. **Kõrge lugemise I/O, madal kirjutamise I/O**:
   - Võimalik põhjus: Andmebaasi indeksite puudumine, mis põhjustab täisotsinguid.
   - Tegevus: Optimeerige andmebaasi indekseid ja päringuid.

2. **Kõrge kirjutamise I/O**:
   - Võimalik põhjus: Intensiivne logide kirjutamine või suur hulk andmebaasi uuendusi.
   - Tegevus: Kaaluge logide rotatsiooni strateegiat või optimeerige andmebaasi kirjutamisoperatsioone.

3. **I/O ooteaegade suurenemine**:
   - Võimalik põhjus: Ketta ülekoormatus või riistvara probleemid.
   - Tegevus: Kaaluge SSD-le üleminekut või RAID-konfiguratsiooni kasutamist jõudluse parandamiseks.

#### Parimad tavad:
- Jälgige nii I/O operatsioonide arvu kui ka läbilaskevõimet.
- Seadke hoiatused ebatavaliselt kõrge I/O aktiivsuse jaoks.
- Analüüsige I/O mustreid, et tuvastada kitsaskohti ja optimeerimise võimalusi.

### 7.4 Võrguliiklus: Digitaalse liikluse jälgimine

Võrguliikluse jälgimine annab ülevaate andmete liikumisest süsteemi ja välismaailma vahel. See on oluline nii jõudluse kui ka turvalisuse seisukohast.

#### Mõõtmine:
- Mõõdetakse bittides või baitides sekundis (bps või B/s).
- Jälgitakse nii sissetulevat kui ka väljaminevat liiklust.

#### Tõlgendamine:
- **Madal liiklus**: Tavaliselt näitab vähest võrgukasutust.
- **Keskmine liiklus**: Sõltub süsteemi tavapärasest koormusest ja funktsioonist.
- **Kõrge liiklus**: Võib viidata suurele kasutajate arvule, andmete ülekandmisele või potentsiaalselt kahtlasele tegevusele.

#### Näited ja praktilised kaalutlused:
1. **Järsk võrguliikluse tõus**:
   - Võimalik põhjus: DDoS rünnak või viirusega nakatunud süsteem.
   - Tegevus: Kontrollige liikluse allikaid ja sihtpunkte, vajadusel rakendage võrgu filtreerimist.

2. **Pidev kõrge väljaminev liiklus**:
   - Võimalik põhjus: Andmeleke või pahavara, mis saadab andmeid välja.
   - Tegevus: Tuvastage liikluse allikas ja kontrollige selle legitiimsust.

3. **Ebaühtlane või katkendlik võrguliiklus**:
   - Võimalik põhjus: Võrguseadmete probleemid või ebastabiilne ühendus.
   - Tegevus: Kontrollige võrguseadmeid ja ühendusi, kaaluge võrgu infrastruktuuri uuendamist.

#### Parimad tavad:
- Jälgige nii üldist võrguliiklust kui ka liiklust erinevate protokollide ja portide lõikes.
- Seadke hoiatused ebatavalise võrguliikluse mustrite jaoks.
- Kasutage võrguliikluse visualiseerimise tööriistu anomaaliate kiireks tuvastamiseks.

Nende nelja põhilise mõõdiku - CPU kasutus, mälu kasutus, ketta I/O ja võrguliiklus - jälgimine annab tervikliku ülevaate süsteemi tervisest ja jõudlusest. Nende mõõdikute kombineerimine ja korrelatsioon võimaldab tuvastada keerulisi probleeme ja optimeerida süsteemi toimimist. On oluline meeles pidada, et "normaalsed" väärtused võivad erinevate süsteemide ja rakenduste puhul oluliselt erineda, seega on kriitilise tähtsusega tunda oma süsteemi tavapärast käitumist ja seada asjakohased lävendid hoiatuste jaoks.

## 8. Monitoorimise tööriistade ülevaade: Võimsad lahendused süsteemi jälgimiseks

Monitoorimise maastik on lai ja mitmekesine, pakkudes lahendusi erinevatele vajadustele ja stsenaariumidele. Siin anname ülevaate nii populaarseimatest tööriistadest turul kui ka nendest, mida käsitleme põhjalikumalt meie koolitusel.

### 8.1 Populaarsed monitoorimise tööriistad turul

1. **Datadog**:
   - Pilvepõhine monitoorimisplatvorm
   - Tugev integratsioon paljude tehnoloogiatega
   - Sobib hästi suurte ja keerukate infrastruktuuride jaoks

2. **New Relic**:
   - Terviklik rakenduste jõudluse monitoorimise (APM) lahendus
   - Tugev fookus lõppkasutaja kogemuse jälgimisel
   - Sügav integratsioon paljude programmeerimiskeelte ja raamistikkudega

3. **Dynatrace**:
   - AI-põhine monitoorimislahendus
   - Automaatne probleemide tuvastamine ja analüüs
   - Sobib hästi suurte ettevõtete keerukatele süsteemidele

4. **Splunk**:
   - Võimas platvorm masinate loodud andmete analüüsimiseks
   - Laiad võimalused kohandamiseks ja automatiseerimiseks
   - Populaarne nii IT-operatsioonides kui ka turvasektoris

5. **Nagios**:
   - Üks vanemaid ja laialdaselt kasutatavaid monitoorimissüsteeme
   - Avatud lähtekoodiga, suure kogukonnaga
   - Tugev võrgu- ja serverimonitoorimine

### 8.2 Meie koolituse fookus

Meie neljapaevane koolitus keskendub järgmistele tööriistadele ja teemadele:

#### Päev 1: Sissejuhatus ja põhitööriistad
1. **Sissejuhatus logimisse, monitoorimisse ja jälgitavusse**
2. **Prometheus**: Avatud lähtekoodiga monitoorimissüsteem
3. **Grafana**: Andmete visualiseerimise platvorm

#### Päev 2: Zabbix
1. **Zabbix**: Ettevõtte taseme monitoorimislahendus
   - Teooria ja arhitektuur
   - Monitoorimise põhitõed ja võtmefunktsioonid
   - Integratsioonid ja edasijõudnud teemad

#### Päev 3: ELK Stack
1. **Elasticsearch**: Otsingu- ja analüüsimootor
2. **Logstash**: Andmete kogumise ja töötlemise tööriist
3. **Kibana**: Visualiseerimise ja uurimise tööriist

#### Päev 4: Jälgitavus ja edasijõudnud teemad
1. **Hajutatud jälgimine**: OpenTelemetry, Jaeger ja Zipkin
2. **TICK Stack**: InfluxData'i monitoorimislahendus
3. **Loki ja Tempo**: Grafana ökosüsteemi täiendavad tööriistad
4. **Struktureeritud logimine ja logihalduse parimad tavad**


### 8.3 Tööriistade valiku trendid ja tulevikusuunad

1. **Konteinerite ja mikroteenuste monitoorimine**:
   - Kasvav vajadus dünaamiliste ja lühiajaliste ressursside jälgimiseks
   - Tööriistad nagu Prometheus ja Grafana on siin eriti populaarsed

2. **AI ja masinõppe integratsioon**:
   - Üha enam tööriistu kasutab AI-d anomaaliate tuvastamiseks ja probleemide ennetamiseks
   - Näiteks Dynatrace ja Datadog on selles valdkonnas esirinnas

3. **Hajutatud jälgimine**:
   - Kasvav fookus mikroteenuste arhitektuuride jälgimisele
   - OpenTelemetry standard muutub üha olulisemaks

4. **Integreeritud lahendused**:
   - Trend liikuda eraldiseisvatelt tööriistadelt terviklike platvormide poole
   - Näiteks Elastic Stack (endine ELK Stack) pakub nüüd ka APM ja turvamonitooring

5. **Pilve-native monitoorimine**:
   - Üha enam tööriistu on optimeeritud pilvekeskkondade jaoks
   - Kubernetes ja serverless arhitektuuride monitoorimine muutub üha olulisemaks

Monitoorimise maastik on pidevas arengus, reageerides uutele tehnoloogiatrendidele ja ärivajadustele. Kuigi meie koolitus keskendub teatud spetsiifilistele tööriistadele, on oluline olla kursis ka laiema pildiga ja uute suundumustega selles valdkonnas.

## 9. Mis on jälgitavus?  Süsteemi läbipaistvuse uus tase

Jälgitavus on kontseptsioon, mis on viimastel aastatel muutunud üha olulisemaks, eriti keerukate ja dünaamiliste süsteemide kontekstis. See pakub sügavamat ja paindlikumat lähenemist süsteemide mõistmisele kui traditsiooniline monitoorimine.

### 9.1 Jälgitavuse definitsioon

Jälgitavus on süsteemi omadus, mis võimaldab mõista selle sisemist seisundit väliste väljundite põhjal. Teisisõnu, see on süsteemi võime anda üksikasjalikku teavet oma toimimise kohta, isegi kui me ei tea täpselt, mida otsida.

#### Võtmeaspektid:
1. **Sisemine seisund**: Jälgitavus püüab anda tervikliku pildi süsteemi sisemisest toimimisest.
2. **Välised väljundid**: See põhineb andmetel, mida süsteem ise väljastab.
3. **Paindlikkus**: Võimaldab esitada uusi küsimusi ja uurida probleeme, mida me ei osanud ette näha.

#### Praktiline näide:
Kujutage ette keerukat e-kaubanduse platvormi. Jälgitav süsteem võimaldaks:
- Jälgida üksiku tellimuse teekonda läbi kogu süsteemi, alates kasutaja klikist kuni tellimuse täitmiseni.
- Mõista, kuidas erinevad mikroteenused omavahel suhtlevad ja mõjutavad üksteise jõudlust.
- Tuvastada ootamatuid sõltuvusi või kitsaskohti, mis ilmnevad ainult teatud tingimustes.

### 9.2 Jälgitavuse vs. monitoorimise erinevus

Kuigi jälgitavus ja monitoorimine on omavahel seotud kontseptsioonid, on nende vahel olulised erinevused.

#### Monitoorimine:
- **Fookus**: Keskendub eelnevalt määratletud mõõdikutele ja probleemidele.
- **Lähenemine**: "Meil on probleem X, vaatame mõõdikut Y."
- **Piirangud**: Piiratud võimalused ootamatute probleemide tuvastamiseks.

#### Jälgitavus:
- **Fookus**: Võimaldab uurida ja mõista süsteemi käitumist laiemalt.
- **Lähenemine**: "Midagi on valesti, uurime süsteemi, et mõista, mis toimub."
- **Paindlikkus**: Võimaldab tuvastada ja uurida tundmatuid probleeme.

#### Võrdlev näide:
1. **Monitoorimise stsenaarium**:
   - Probleem: Veebileht laeb aeglaselt.
   - Lähenemine: Kontrollime eelnevalt seadistatud mõõdikuid nagu serveri CPU kasutus, mälu kasutus ja andmebaasi vastamise aeg.
   - Piirang: Kui probleem ei ole otseselt seotud nende mõõdikutega, võib selle põhjuse leidmine olla keeruline.

2. **Jälgitavuse stsenaarium**:
   - Probleem: Veebileht laeb aeglaselt.
   - Lähenemine: Uurime kogu kasutaja päringu teekonda läbi süsteemi, jälgides kõiki mikroteenuseid, andmevoogusid ja sõltuvusi.
   - Eelis: Võime avastada ootamatuid kitsaskohti, näiteks aeglase kolmanda osapoole API või ebaefektiivse päringumustri mikroteenuste vahel.

### 9.3 Jälgitavuse olulisus kaasaegsetes süsteemides

Jälgitavus on muutunud kriitiliselt oluliseks mitmel põhjusel:

1. **Süsteemide keerukus**: 
   - Kaasaegsed süsteemid koosnevad sageli sadadest mikroteenustest ja hajutatud komponentidest.
   - Traditsioonilised monitoorimismeetodid ei ole piisavad sellise keerukuse haldamiseks.

2. **Dünaamiline keskkond**: 
   - Pilveinfrastruktuur ja konteineripõhised rakendused muutuvad pidevalt.
   - Jälgitavus võimaldab mõista süsteemi käitumist ka pidevalt muutuvas keskkonnas.

3. **Kiire tõrkeotsing**: 
   - Jälgitavus võimaldab kiiresti tuvastada ja lahendada probleeme, vähendades keskmist aja kulu probleemide lahendamiseks (MTTR).

4. **Ennetav probleemide tuvastamine**: 
   - Võimaldab tuvastada potentsiaalseid probleeme enne, kui need muutuvad kriitiliseks.

5. **Süsteemi optimeerimise võimalused**: 
   - Annab sügavama arusaama süsteemi toimimisest, võimaldades teha teadlikumaid otsuseid optimeerimise osas.

### 9.4 Jälgitavuse rakendamine praktikas

Jälgitavuse rakendamine hõlmab tavaliselt kolme põhilist komponenti:

1. **Logid**: 
   - Detailsed kirjed sündmustest süsteemis.
   - Näide: Üksikasjalikud logid iga mikroteenuse tegevuse kohta.

2. **Mõõdikud**: 
   - Numbrilised väärtused, mis kirjeldavad süsteemi seisundit.
   - Näide: Päringu latentsus, vigade arv, ressursside kasutus.

3. **Jäljed (Traces)**: 
   - Päringu või toimingu teekond läbi hajussüsteemi.
   - Näide: Kasutaja ostu teekond läbi e-poe erinevate mikroteenuste.

#### Praktiline näide jälgitavuse rakendamisest:

Kujutage ette suurt e-kaubanduse platvormi:

1. **Logid**: 
   - Iga mikroteenuse (nt. kasutajate autentimine, tootekataloog, ostukorv, maksesüsteem) detailsed logid.
   - Logid sisaldavad lisaks sündmuse kirjeldusele ka konteksti (nt. kasutaja ID, sessiooni ID).

2. **Mõõdikud**: 
   - Iga mikroteenuse vastamise aeg.
   - Süsteemi üldine läbilaskevõime (päringud sekundis).
   - Vigade arv ja tüübid.

3. **Jäljed**: 
   - Iga kasutaja ostu teekond alates veebilehe laadimisest kuni ostu kinnitamiseni.
   - Jälg näitab, millised mikroteenused olid kaasatud, kui kaua iga etapp võttis aega ja kus tekkisid viivitused.

Sellise süsteemi puhul saab jälgitavus aidata:
- Tuvastada, miks teatud kasutajate ostud ebaõnnestuvad.
- Mõista, milline mikroteenuse omavaheline suhtlus põhjustab viivitusi.
- Avastada ootamatuid sõltuvusi erinevate süsteemi osade vahel.

### 9.5 Väljakutsed ja parimad tavad

Jälgitavuse rakendamisel on ka oma väljakutsed:

1. **Andmete üleküllus**: Liiga palju andmeid võib muuta analüüsi keeruliseks.
   - Parim tava: Keskenduge olulisele ja kasutage nutikaid filtreerimismeetodeid.

2. **Privaatsus ja turvalisus**: Detailsed logid võivad sisaldada tundlikku infot.
   - Parim tava: Rakendage andmete maskeerimist ja krüpteerimist.

3. **Jõudluse mõju**: Liigne logimine ja jälgimine võib mõjutada süsteemi jõudlust.
   - Parim tava: Kasutage tõhusaid logimismeetodeid ja valige hoolikalt, mida jälgida.

4. **Keerukus**: Jälgitavuse tööriistade seadistamine ja haldamine võib olla keeruline.
   - Parim tava: Alustage lihtsalt ja laiendage järk-järgult. Investeerige meeskonna koolitusse.

Jälgitavus on võimas kontseptsioon, mis võimaldab organisatsioonidel mõista ja hallata oma keerukaid süsteeme paremini kui kunagi varem. See nõuab küll investeeringuid tehnoloogiasse ja oskustesse, kuid pakub väärtuslikku ülevaadet, mis on hädavajalik tänapäeva dünaamilises IT-keskkonnas.

## 10. Jälgitavuse kolm sammast: Süsteemi tervikliku mõistmise alused

Jälgitavus toetub kolmele põhilisele sambale: logid, mõõdikud ja jäljed. Need kolm elementi töötavad koos, et anda terviklik ülevaade süsteemi toimimisest ja tervisest. Vaatame igat sammast lähemalt.

### 10.1 Logid: Süsteemi detailne päevik (journal)

Logid on üksikasjalikud kirjed sündmustest, mis toimuvad süsteemis. Need on kui süsteemi päevik, mis salvestab kõik olulised tegevused ja sündmused.

#### Põhiomadused:
1. **Detailsus**: Logid sisaldavad üksikasjalikku teavet sündmuste kohta.
2. **Ajaline järjestus**: Sündmused on tavaliselt kronoloogilises järjekorras.
3. **Kontekst**: Hea logimine sisaldab ka konteksti (nt kasutaja ID, sessiooni ID).

#### Logide kasutamine praktikas:
- **Probleemide uurimine**: Logid võimaldavad tagantjärele uurida, mis täpselt juhtus.
- **Turvaintsidentide analüüs**: Logid on kriitilise tähtsusega turvaintsidentide uurimisel.
- **Kasutajate käitumise mõistmine**: Võimaldab analüüsida, kuidas kasutajad süsteemiga suhtlevad.

#### Näide:
Kujutage ette veebirakenduse autentimissüsteemi logi:

```
2024-03-15 14:30:45 [INFO] User johnsmith@example.com logged in successfully
2024-03-15 14:31:12 [WARN] Failed login attempt for user maryj@example.com (3rd attempt)
2024-03-15 14:31:30 [ERROR] Authentication service unavailable for 5 seconds
2024-03-15 14:31:35 [INFO] Authentication service restored
```

See logiväljund annab ülevaate autentimissüsteemi toimimisest, võimaldades tuvastada nii edukaid kui ka ebaõnnestunud sisselogimisi ning süsteemi tõrkeid.

#### Parimad tavad logide kasutamisel:
1. **Struktureeritud logimine**: Kasutage ühtset formaati (nt JSON), mis lihtsustab logide töötlemist.
2. **Asjakohane detailsus**: Logige piisavalt infot, kuid vältige liigset logimist, mis võib tekitada müra.
3. **Tsentraalne logikogumine**: Kasutage tsentraalset logisüsteemi, mis koondab logid erinevatest allikatest.
4. **Logide säilitamine**: Määrake sobiv logide säilitamise poliitika, arvestades nii vajadusi kui ka regulatsioone.

### 10.2 Mõõdikud: Süsteemi tervise numbrilised näitajad

Mõõdikud on numbrilised väärtused, mis kirjeldavad süsteemi seisundit teatud aja jooksul. Need on kui süsteemi elulised näitajad, mis annavad kiire ülevaate süsteemi tervisest.

#### Põhiomadused:
1. **Kvantitatiivsus**: Mõõdikud on alati numbrilised väärtused.
2. **Ajaline dimensioon**: Mõõdikuid jälgitakse tavaliselt aja jooksul, et näha trende.
3. **Agregeerimine**: Mõõdikuid saab sageli agregeerida (nt keskmine, summa, protsentiilid).

#### Mõõdikute kasutamine praktikas:
- **Jõudluse jälgimine**: Näiteks serveri CPU kasutuse või rakenduse vastamise aja jälgimine.
- **Äriliste KPI-de jälgimine**: Näiteks tehingute arv minutis või aktiiveste kasutajate arv.
- **Ressursside planeerimine**: Mõõdikud aitavad prognoosida tulevasi ressursivajadusi.

#### Näide:
Kujutage ette veebirakenduse olulisi mõõdikuid:

1. **Päringu latentsus**: 
   - Keskmine: 150ms
   - 95. protsentiil: 300ms
2. **Päringute arv minutis**: 
   - Keskmine: 1000 päringut/min
   - Tippkoormus: 1500 päringut/min
3. **Vigade määr**: 
   - Keskmine: 0.5%
   - Viimase 5 minuti jooksul: 1.2%

Need mõõdikud annavad kiire ülevaate rakenduse jõudlusest ja terviklikust seisundist.

#### Parimad tavad mõõdikute kasutamisel:
1. **Vali õiged mõõdikud**: Keskendu mõõdikutele, mis on tõeliselt olulised süsteemi tervise ja jõudluse seisukohalt.
2. **Määra lävendid**: Sead selged lävendid, millal mõõdikud viitavad probleemidele.
3. **Visualiseeri**: Kasuta graafikuid ja dashboarde mõõdikute visualiseerimiseks.
4. **Kombineeri mõõdikuid**: Vaata erinevaid mõõdikuid koos, et saada terviklik pilt.

### 10.3 Jäljed (Traces): Päringu teekonna kaardistamine

Jäljed järgivad päringu või toimingu teekonda läbi hajussüsteemi. Need on kui GPS-jälg, mis näitab, kuidas päring liigub läbi erinevate süsteemi osade.

#### Põhiomadused:
1. **Terviklik vaade**: Näitab päringu kogu teekonda algusest lõpuni.
2. **Hierarhiline struktuur**: Jälg koosneb tavaliselt mitmest seotud spans'ist (ajavahemikust).
3. **Ajaline info**: Iga span sisaldab infot selle kestuse kohta.

#### Jälgede kasutamine praktikas:
- **Jõudlusprobleemide tuvastamine**: Aitab leida, millises süsteemi osas tekivad viivitused.
- **Süsteemi arhitektuuri mõistmine**: Näitab, kuidas erinevad teenused omavahel suhtlevad.
- **Anomaaliate tuvastamine**: Võimaldab leida ebatavalisi mustreid päringu töötlemisel.

#### Näide:
Kujutage ette e-poe tellimuse töötlemise jälge:

```
Tellimuse töötlemine (1200ms)
├── Kasutaja autentimine (50ms)
├── Ostukorvi valideerimine (100ms)
├── Makse töötlemine (800ms)
│   ├── Krediitkaardi valideerimine (300ms)
│   └── Makse kinnitamine (500ms)
└── Tellimuse kinnituse saatmine (250ms)
    ├── E-maili saatmine (200ms)
    └── SMS-i saatmine (50ms)
```

See jälg näitab tellimuse töötlemise kogu teekonda, võimaldades tuvastada, et makse töötlemine võtab kõige rohkem aega.

#### Parimad tavad jälgede kasutamisel:
1. **Piisav detailsus**: Jälgi piisavalt detailselt, kuid väldi liigset granuleerimist.
2. **Konteksti lisamine**: Lisa jälgedele olulist konteksti (nt kasutaja ID, tellimuse summa).
3. **Jälgede seostamine logide ja mõõdikutega**: Integreeri jäljed teiste jälgitavuse sammastega.
4. **Proovi-põhine jälgimine**: Suure liikluse korral jälgi ainult osa päringuid, et vähendada süsteemi koormust.

### 10.4 Kolme samba integreerimine

Logid, mõõdikud ja jäljed töötavad kõige efektiivsemalt, kui neid kasutatakse koos:

1. **Probleemi tuvastamine mõõdikute abil**: Näiteks märkad, et päringu latentsus on tõusnud.
2. **Probleemi uurimine jälgede abil**: Kasutad jälgi, et tuvastada, millises süsteemi osas viivitus tekib.
3. **Detailne analüüs logide abil**: Uurid konkreetse probleemse komponendi logisid, et mõista täpset põhjust.

#### Näide integreeritud lähenemisest:
1. Mõõdik näitab, et veebirakenduse vastamise aeg on tõusnud üle 500ms.
2. Jäljed näitavad, et viivitus tekib andmebaasipäringute juures.
3. Andmebaasi logidest selgub, et teatud päring võtab ebatavaliselt kaua aega indeksi puudumise tõttu.

Selline integreeritud lähenemine võimaldab kiiresti tuvastada, lokaliseerida ja lahendada probleeme keerukates süsteemides.

Jälgitavuse kolm sammast - logid, mõõdikud ja jäljed - moodustavad võimsa tööriistakomplekti tänapäeva keerukate IT-süsteemide mõistmiseks ja haldamiseks. Nende efektiivne kasutamine ja integreerimine on võtmetähtsusega proaktiivse probleemide lahendamise ja süsteemide optimeerimise jaoks.

## 10. Jälgitavuse kolm sammast: Süsteemi tervikliku mõistmise alused

Jälgitavus toetub kolmele põhilisele sambale: logid, mõõdikud ja jäljed. Need kolm elementi töötavad koos, et anda terviklik ülevaade süsteemi toimimisest ja tervisest. Vaatame igat sammast lähemalt.

### 10.1 Logid: Süsteemi detailne päevik

Logid on üksikasjalikud kirjed sündmustest, mis toimuvad süsteemis. Need on kui süsteemi päevik, mis salvestab kõik olulised tegevused ja sündmused.

#### Põhiomadused:
1. **Detailsus**: Logid sisaldavad üksikasjalikku teavet sündmuste kohta.
2. **Ajaline järjestus**: Sündmused on tavaliselt kronoloogilises järjekorras.
3. **Kontekst**: Hea logimine sisaldab ka konteksti (nt kasutaja ID, sessiooni ID).

#### Logide kasutamine praktikas:
- **Probleemide uurimine**: Logid võimaldavad tagantjärele uurida, mis täpselt juhtus.
- **Turvaintsidentide analüüs**: Logid on kriitilise tähtsusega turvaintsidentide uurimisel.
- **Kasutajate käitumise mõistmine**: Võimaldab analüüsida, kuidas kasutajad süsteemiga suhtlevad.

#### Näide:
Kujutage ette veebirakenduse autentimissüsteemi logi:

```
2024-03-15 14:30:45 [INFO] User johnsmith@example.com logged in successfully
2024-03-15 14:31:12 [WARN] Failed login attempt for user maryj@example.com (3rd attempt)
2024-03-15 14:31:30 [ERROR] Authentication service unavailable for 5 seconds
2024-03-15 14:31:35 [INFO] Authentication service restored
```

See logiväljund annab ülevaate autentimissüsteemi toimimisest, võimaldades tuvastada nii edukaid kui ka ebaõnnestunud sisselogimisi ning süsteemi tõrkeid.

#### Parimad tavad logide kasutamisel:
1. **Struktureeritud logimine**: Kasutage ühtset formaati (nt JSON), mis lihtsustab logide töötlemist.
2. **Asjakohane detailsus**: Logige piisavalt infot, kuid vältige liigset logimist, mis võib tekitada müra.
3. **Tsentraalne logikogumine**: Kasutage tsentraalset logisüsteemi, mis koondab logid erinevatest allikatest.
4. **Logide säilitamine**: Määrake sobiv logide säilitamise poliitika, arvestades nii vajadusi kui ka regulatsioone.

### 10.2 Mõõdikud: Süsteemi tervise numbrilised näitajad

Mõõdikud on numbrilised väärtused, mis kirjeldavad süsteemi seisundit teatud aja jooksul. Need on kui süsteemi elulised näitajad, mis annavad kiire ülevaate süsteemi tervisest.

#### Põhiomadused:
1. **Kvantitatiivsus**: Mõõdikud on alati numbrilised väärtused.
2. **Ajaline dimensioon**: Mõõdikuid jälgitakse tavaliselt aja jooksul, et näha trende.
3. **Agregeerimine**: Mõõdikuid saab sageli agregeerida (nt keskmine, summa, protsentiilid).

#### Mõõdikute kasutamine praktikas:
- **Jõudluse jälgimine**: Näiteks serveri CPU kasutuse või rakenduse vastamise aja jälgimine.
- **Äriliste KPI-de jälgimine**: Näiteks tehingute arv minutis või aktiiveste kasutajate arv.
- **Ressursside planeerimine**: Mõõdikud aitavad prognoosida tulevasi ressursivajadusi.

#### Näide:
Kujutage ette veebirakenduse olulisi mõõdikuid:

1. **Päringu latentsus**: 
   - Keskmine: 150ms
   - 95. protsentiil: 300ms
2. **Päringute arv minutis**: 
   - Keskmine: 1000 päringut/min
   - Tippkoormus: 1500 päringut/min
3. **Vigade määr**: 
   - Keskmine: 0.5%
   - Viimase 5 minuti jooksul: 1.2%

Need mõõdikud annavad kiire ülevaate rakenduse jõudlusest ja terviklikust seisundist.

#### Parimad tavad mõõdikute kasutamisel:
1. **Vali õiged mõõdikud**: Keskendu mõõdikutele, mis on tõeliselt olulised süsteemi tervise ja jõudluse seisukohalt.
2. **Määra lävendid**: Sead selged lävendid, millal mõõdikud viitavad probleemidele.
3. **Visualiseeri**: Kasuta graafikuid ja dashboarde mõõdikute visualiseerimiseks.
4. **Kombineeri mõõdikuid**: Vaata erinevaid mõõdikuid koos, et saada terviklik pilt.

### 10.3 Jäljed (Traces): Päringu teekonna kaardistamine

Jäljed järgivad päringu või toimingu teekonda läbi hajussüsteemi. Need on kui GPS-jälg, mis näitab, kuidas päring liigub läbi erinevate süsteemi osade.

#### Põhiomadused:
1. **Terviklik vaade**: Näitab päringu kogu teekonda algusest lõpuni.
2. **Hierarhiline struktuur**: Jälg koosneb tavaliselt mitmest seotud spans'ist (ajavahemikust).
3. **Ajaline info**: Iga span sisaldab infot selle kestuse kohta.

#### Jälgede kasutamine praktikas:
- **Jõudlusprobleemide tuvastamine**: Aitab leida, millises süsteemi osas tekivad viivitused.
- **Süsteemi arhitektuuri mõistmine**: Näitab, kuidas erinevad teenused omavahel suhtlevad.
- **Anomaaliate tuvastamine**: Võimaldab leida ebatavalisi mustreid päringu töötlemisel.

#### Näide:
Kujutage ette e-poe tellimuse töötlemise jälge:

```
Tellimuse töötlemine (1200ms)
├── Kasutaja autentimine (50ms)
├── Ostukorvi valideerimine (100ms)
├── Makse töötlemine (800ms)
│   ├── Krediitkaardi valideerimine (300ms)
│   └── Makse kinnitamine (500ms)
└── Tellimuse kinnituse saatmine (250ms)
    ├── E-maili saatmine (200ms)
    └── SMS-i saatmine (50ms)
```

See jälg näitab tellimuse töötlemise kogu teekonda, võimaldades tuvastada, et makse töötlemine võtab kõige rohkem aega.

#### Parimad tavad jälgede kasutamisel:
1. **Piisav detailsus**: Jälgi piisavalt detailselt, kuid väldi liigset granuleerimist.
2. **Konteksti lisamine**: Lisa jälgedele olulist konteksti (nt kasutaja ID, tellimuse summa).
3. **Jälgede seostamine logide ja mõõdikutega**: Integreeri jäljed teiste jälgitavuse sammastega.
4. **Proovi-põhine jälgimine**: Suure liikluse korral jälgi ainult osa päringuid, et vähendada süsteemi koormust.

### 10.4 Kolme samba integreerimine

Logid, mõõdikud ja jäljed töötavad kõige efektiivsemalt, kui neid kasutatakse koos:

1. **Probleemi tuvastamine mõõdikute abil**: Näiteks märkad, et päringu latentsus on tõusnud.
2. **Probleemi uurimine jälgede abil**: Kasutad jälgi, et tuvastada, millises süsteemi osas viivitus tekib.
3. **Detailne analüüs logide abil**: Uurid konkreetse probleemse komponendi logisid, et mõista täpset põhjust.

#### Näide integreeritud lähenemisest:
1. Mõõdik näitab, et veebirakenduse vastamise aeg on tõusnud üle 500ms.
2. Jäljed näitavad, et viivitus tekib andmebaasipäringute juures.
3. Andmebaasi logidest selgub, et teatud päring võtab ebatavaliselt kaua aega indeksi puudumise tõttu.

Selline integreeritud lähenemine võimaldab kiiresti tuvastada, lokaliseerida ja lahendada probleeme keerukates süsteemides.

Jälgitavuse kolm sammast - logid, mõõdikud ja jäljed - moodustavad võimsa tööriistakomplekti tänapäeva keerukate IT-süsteemide mõistmiseks ja haldamiseks. Nende efektiivne kasutamine ja integreerimine on võtmetähtsusega proaktiivse probleemide lahendamise ja süsteemide optimeerimise jaoks.

## 12. Logimine vs. monitoorimine vs. jälgitavus: Võrdlev analüüs

Logimine, monitoorimine ja jälgitavus on kolm olulist kontseptsiooni IT-süsteemide haldamises. Kuigi need on omavahel seotud ja tihti kattuvad, on igaühel oma spetsiifiline fookus ja eesmärk. Vaatame neid lähemalt võrdlevas kontekstis.

### 12.1 Ülevaatlik võrdlustabel

| Aspekt | Logimine | Monitoorimine | Jälgitavus |
|--------|----------|---------------|------------|
| Eesmärk | Sündmuste salvestamine | Süsteemi seisundi jälgimine | Süsteemi käitumise mõistmine |
| Fookus | Minevik | Olevik | Minevik, olevik ja tulevik |
| Andmete tüüp | Struktureeritud või struktureerimata tekst | Mõõdikud ja hoiatused | Logid, mõõdikud ja jäljed |
| Kasutus | Veaotsing, auditid | Jõudluse jälgimine, hoiatused | Süsteemi analüüs, optimeerimine |

### 12.2 Detailne võrdlus ja näited

#### Logimine

**Eesmärk**: Salvestada üksikasjalikud andmed süsteemis toimunud sündmuste kohta.

**Fookus**: Keskendub minevikule, salvestades, mis on juba juhtunud.

**Andmete tüüp**: Peamiselt tekstipõhised kirjed, mis võivad olla struktureeritud (nt JSON vormingus) või struktureerimata.

**Kasutus**: 
- Veaotsing: Logid võimaldavad tagantjärele uurida, mis täpselt juhtus.
- Auditid: Logid on olulised turvaintsidentide uurimisel ja vastavuse tagamisel.

**Näide**:
```
2024-03-15 14:30:45 [ERROR] Failed to connect to database: Connection timed out
2024-03-15 14:30:46 [INFO] Retrying database connection (attempt 1 of 3)
2024-03-15 14:30:47 [INFO] Database connection established successfully
```

#### Monitoorimine

**Eesmärk**: Jälgida süsteemi hetkeseisundit ja tuvastada kõrvalekaldeid normaalväärtustest.

**Fookus**: Keskendub olevikule, jälgides reaalajas süsteemi seisundit.

**Andmete tüüp**: Peamiselt numbrilised mõõdikud ja nendega seotud hoiatused.

**Kasutus**: 
- Jõudluse jälgimine: Pidev süsteemi võtmenäitajate jälgimine.
- Hoiatused: Automaatsed teavitused, kui mõõdikud ületavad eelnevalt määratud lävendeid.

**Näide**:
```
CPU Usage: 75% (Warning threshold: 80%)
Memory Usage: 60% (Normal)
Disk I/O: 500 IOPS (High, but within limits)
Active Users: 1000 (Peak hour)
```

#### Jälgitavus

**Eesmärk**: Mõista süsteemi käitumist terviklikult, võimaldades tuvastada ja uurida ka ootamatuid probleeme.

**Fookus**: Hõlmab minevikku, olevikku ja võimaldab prognoosida tulevikku.

**Andmete tüüp**: Kombineerib logisid, mõõdikuid ja jälgi (traces) terviklikuks ülevaateks.

**Kasutus**: 
- Süsteemi analüüs: Võimaldab mõista süsteemi käitumist erinevates tingimustes.
- Optimeerimine: Aitab tuvastada kitsaskohti ja optimeerida süsteemi toimimist.

**Näide**:
```
User Transaction Trace:
1. Web Server (10ms)
   ├─ Authentication Service (50ms)
   ├─ Product Catalog Service (30ms)
   └─ Payment Processing (500ms)
      ├─ Credit Card Validation (200ms)
      └─ Payment Gateway (300ms)

Associated Logs:
14:30:45 [INFO] User 12345 initiated checkout
14:30:46 [WARN] Payment gateway response time high: 300ms

Metrics:
- Transaction Success Rate: 98%
- Avg Response Time: 590ms (SLA: 500ms)
- Payment Gateway Error Rate: 2% (Up from 0.5% last week)
```

### 12.3 Kuidas need kolm lähenemist töötavad koos

Kuigi logimine, monitoorimine ja jälgitavus on eraldiseisvad kontseptsioonid, töötavad need praktikas tihti koos, et anda terviklik ülevaade süsteemi toimimisest.

#### Praktiline näide: E-poe probleemi lahendamine

Kujutage ette olukorda, kus e-poe kliendid teatavad aeglasest ostlemiskogemusest:

1. **Monitoorimine** tuvastab probleemi:
   - Mõõdikud näitavad, et veebilehe laadimisaeg on tõusnud üle 5 sekundi (tavapärane on alla 2 sekundi).
   - Automaatne hoiatus saadetakse IT-meeskonnale.

2. **Jälgitavus** aitab probleemi lokaliseerida:
   - Jäljed näitavad, et viivitus tekib tootekataloogiserveri päringutel.
   - Analüüs paljastab, et 80% aeglastest päringutest on seotud otsingufunktsiooniga.

3. **Logimine** aitab tuvastada juurpõhjuse:
   - Tootekataloogiserveri logid näitavad, et andmebaasi indeks on aegunud, põhjustades aeglasi otsinguid.
   - Logidest selgub, et planeeritud indeksi uuendamine eelmisel ööl ebaõnnestus.

4. **Lahendus ja ennetamine**:
   - IT-meeskond käivitab koheselt indeksi uuendamise.
   - Monitoorimine kinnitab, et pärast indeksi uuendamist veebilehe laadimisajad normaliseeruvad.
   - Jälgitavuse andmete põhjal optimeeritakse otsingualgoritmi, et vähendada koormust andmebaasile.
   - Logimissüsteemi täiendatakse, et tulevikus saada selgemaid hoiatusi indeksi uuendamise ebaõnnestumise korral.

See näide illustreerib, kuidas logimine, monitoorimine ja jälgitavus töötavad koos, et mitte ainult lahendada tekkinud probleeme, vaid ka ennetada neid tulevikus ja pidevalt optimeerida süsteemi toimimist.

### 12.4 Väljakutsed ja parimad tavad

Iga lähenemine toob kaasa oma väljakutsed:

1. **Logimine**:
   - Väljakutse: Liiga palju või liiga vähe logisid.
   - Parim tava: Määratlege selged logimisstandardid ja kasutage dünaamilisi logitasemeid.

2. **Monitoorimine**:
   - Väljakutse: Valede või ebaoluliste mõõdikute jälgimine.
   - Parim tava: Keskenduge äriliselt olulistele mõõdikutele ja vaadake regulaarselt üle monitoorimisstrateegiat.

3. **Jälgitavus**:
   - Väljakutse: Andmete üleküllus ja keerukus.
   - Parim tava: Kasutage nutikaid filtreerimis- ja agregeerimistehnikaid ning investeerige meeskonna koolitusse.

Efektiivne IT-süsteemide haldamine nõuab kõigi kolme lähenemise tasakaalustatud kasutamist. Logimine annab detailse ülevaate minevikusündmustest, monitoorimine hoiab silma peal hetkeseisundil, ja jälgitavus võimaldab mõista süsteemi käitumist terviklikult. Koos moodustavad need võimsa tööriistakomplekti, mis aitab tagada süsteemide stabiilsuse, jõudluse ja pideva optimeerimise.

## 13. Reaaleluline stsenaarium: E-kaubanduse veebisaidi probleemide lahendamine

Kujutage ette suurt e-kaubanduse veebisaiti "TechMart", mis müüb elektroonikaseadmeid ja tarvikuid. TechMart kogeb aeg-ajalt aeglust ja vigu kasutajate tellimuste töötlemisel, eriti tipptundidel ja suurte allahindluskampaaniate ajal. Vaatame, kuidas logimine, monitoorimine ja jälgitavus aitavad tuvastada, analüüsida ja lahendada neid probleeme.

### 13.1 Süsteemi ülevaade

TechMart'i süsteem koosneb järgmistest komponentidest:
- Veebiserver (Nginx)
- Rakendusserver (Node.js)
- Andmebaas (PostgreSQL)
- Toodete otsingu teenus (Elasticsearch)
- Maksete töötlemise teenus (kolmanda osapoole API)
- Tellimuste töötlemise teenus
- Laohalduse süsteem

### 13.2 Probleemi kirjeldus

Kliendid teatavad järgmistest probleemidest:
1. Aeglane veebilehe laadimine
2. Tellimuste töötlemine võtab ebatavaliselt kaua aega
3. Mõned tellimused ebaõnnestuvad maksete töötlemisel
4. Toodete otsing annab aeg-ajalt valesid tulemusi

### 13.3 Logimise rakendamine

TechMart kasutab tsentraalset logimissüsteemi, mis kogub logisid kõigist süsteemi komponentidest.

#### Näited logidest:

1. Veebiserveri logi:
```
2024-03-15 14:30:45 [INFO] 192.168.1.100 - - [15/Mar/2024:14:30:45 +0000] "GET /product/12345 HTTP/1.1" 200 1234 "https://techmart.com/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
2024-03-15 14:30:46 [WARN] High response time: 2.5s for /product/12345
```

2. Rakendusserveri logi:
```
2024-03-15 14:31:00 [INFO] User id=5678 initiated checkout process
2024-03-15 14:31:05 [ERROR] Failed to process payment for order id=9876: Timeout from payment gateway
```

3. Andmebaasi logi:
```
2024-03-15 14:31:10 [WARN] Slow query detected: SELECT * FROM products WHERE category='laptops' ORDER BY price ASC (execution time: 5.2s)
```

#### Logimise kasutamine:
- IT-meeskond analüüsib logisid, et tuvastada mustreid aeglastes päringutes ja vigades.
- Turvameeskond kasutab logisid, et tuvastada võimalikke turvaohte või ebatavalist käitumist.
- Arendusmeeskond kasutab logisid vigade silumiseks ja koodi optimeerimiseks.

### 13.4 Monitoorimise rakendamine

TechMart on seadistanud monitoorimissüsteemi, mis jälgib pidevalt süsteemi võtmenäitajaid.

#### Jälgitavad mõõdikud:

1. Infrastruktuuri mõõdikud:
   - CPU kasutus: 80% (hoiatus: >85%)
   - Mälu kasutus: 70% (hoiatus: >90%)
   - Kettakasutus: 65% (hoiatus: >80%)

2. Rakenduse mõõdikud:
   - Veebilehe laadimisaeg: 3.5s (eesmärk: <2s)
   - Andmebaasi päringute keskmine aeg: 250ms (hoiatus: >500ms)
   - Tellimuste töötlemise aeg: 8s (eesmärk: <5s)

3. Ärilised mõõdikud:
   - Aktiivste kasutajate arv: 5000
   - Tellimuste arv tunnis: 200
   - Ostukorvi hülgamise määr: 25% (eesmärk: <20%)

#### Monitoorimise kasutamine:
- Automaatsed hoiatused saadetakse IT-meeskonnale, kui mõõdikud ületavad seatud lävendi.
- Reaalajas dashboardid annavad ülevaate süsteemi tervisest ja jõudlusest.
- Pikaajalised trendid aitavad planeerida ressursse ja optimeerida süsteemi.

### 13.5 Jälgitavuse rakendamine

TechMart on implementeerinud jälgitavuse lahenduse, mis võimaldab jälgida kasutajate päringuid ja tellimusi läbi kogu süsteemi.

#### Jälgitavuse näide:

Kasutaja tellimuse jälg:
```
Tellimuse ID: 9876 (Kogukestus: 8.2s)
1. Veebiserver (10ms)
2. Rakendusserver (50ms)
   ├─ Kasutaja autentimine (20ms)
   └─ Ostukorvi valideerimine (30ms)
3. Toodete otsingu teenus (1.5s)
   └─ Elasticsearch päring (1.4s)
4. Laohalduse süsteem (200ms)
5. Maksete töötlemise teenus (6s)
   ├─ Krediitkaardi valideerimine (500ms)
   └─ Makse kinnitamine (5.5s) [TIMEOUT]
6. Tellimuste töötlemise teenus (400ms)
```

#### Jälgitavuse kasutamine:
- IT-meeskond saab kiiresti tuvastada, et maksete töötlemise teenus on peamine kitsaskoht.
- Arendusmeeskond näeb, et Elasticsearch päring võtab ebatavaliselt kaua aega ja vajab optimeerimist.
- Ärianalüütikud saavad hinnata, kuidas süsteemi jõudlus mõjutab kasutajate käitumist ja tellimuste lõpuleviimist.

### 13.6 Probleemide lahendamine

Kasutades logimist, monitoorimist ja jälgitavust, TechMart'i meeskond tuvastab ja lahendab järgmised probleemid:

1. **Aeglane veebilehe laadimine**:
   - Jälgitavus näitab, et Elasticsearch päringud on aeglased.
   - Logid paljastavad, et indeksid on optimeerimata.
   - Lahendus: Elasticsearch indeksite optimeerimine ja päringute täiustamine.

2. **Aeglane tellimuste töötlemine**:
   - Monitoorimine näitab kõrget CPU kasutust andmebaasiserveris.
   - Logid paljastavad korduvaid aeglasi SQL päringuid.
   - Lahendus: SQL päringute optimeerimine ja andmebaasi indeksite lisamine.

3. **Ebaõnnestunud maksed**:
   - Jälgitavus näitab, et makseteenuse API vastused on aeglased.
   - Monitoorimine näitab kõrget võrgulatentsust makseteenuse suunas.
   - Lahendus: Makseteenuse timeout'i suurendamine ja alternatiivse makseteenuse lisamine varuplaanina.

4. **Valed otsingutulemused**:
   - Logid näitavad, et Elasticsearch indeks ei ole ajakohane.
   - Lahendus: Elasticsearch indeksi uuendamise protsessi parandamine ja regulaarse kontrolli lisamine.

### 13.7 Tulemused

Pärast nende probleemide lahendamist TechMart näeb järgmisi parandusi:

- Veebilehe keskmine laadimisaeg langeb 3.5 sekundilt 1.8 sekundile.
- Tellimuste töötlemise aeg väheneb 8 sekundilt 4 sekundile.
- Ebaõnnestunud maksete arv langeb 5%-lt 0.5%-le.
- Otsingutulemuste täpsus paraneb 95%-lt 99.5%-le.
- Ostukorvi hülgamise määr langeb 25%-lt 18%-le.

### 13.8 Õppetunnid

1. **Integreeritud lähenemine**: Logimine, monitoorimine ja jälgitavus töötavad kõige paremini koos, andes tervikliku pildi süsteemi toimimisest.
2. **Proaktiivne lähenemine**: Regulaarne logide analüüs ja mõõdikute jälgimine võimaldab tuvastada probleeme enne, kui need muutuvad kriitiliseks.
3. **Pidev optimeerimine**: Jälgitavuse andmed aitavad pidevalt tuvastada optimeerimise võimalusi, isegi kui suuri probleeme ei esine.
4. **Meeskonnadevaheline koostöö**: Efektiivne probleemide lahendamine nõuab tihedat koostööd IT-, arendus- ja ärimeeskondade vahel.

See reaaleluline stsenaarium näitab, kuidas logimine, monitoorimine ja jälgitavus töötavad koos, et aidata organisatsioonidel tuvastada, analüüsida ja lahendada keerukaid probleeme kaasaegsetes IT-süsteemides. Need tööriistad ja lähenemised on hädavajalikud, et tagada kõrge kvaliteediga kasutajakogemus ja efektiivne süsteemihaldus.


## 14. Korrektse logimise, monitoorimise ja jälgitavuse eelised

Korrektne logimine, monitoorimine ja jälgitavus on kriitilise tähtsusega tänapäeva keerukate IT-süsteemide haldamisel. Need praktikad toovad kaasa mitmeid olulisi eeliseid, mis aitavad organisatsioonidel parandada oma süsteemide toimimist, vähendada riske ja optimeerida ressursside kasutamist.

### 14.1 Kiirem probleemide lahendamine

Üks peamisi eeliseid on võime kiiremini tuvastada ja lahendada probleeme.

#### Kuidas see toimib:
1. **Täpne probleemi lokaliseerimine**: Jälgitavus võimaldab kiiresti tuvastada, millises süsteemi osas probleem esineb.
2. **Detailne kontekst**: Logid annavad üksikasjaliku info sündmuste kohta, mis viisid probleemini.
3. **Reaalajas teavitused**: Monitoorimine võimaldab seada hoiatusi, mis teavitavad IT-meeskondi kohe, kui midagi läheb valesti.

#### Praktiline näide:
Kujutage ette e-kaubanduse platvormi, kus kliendid teatavad, et nad ei saa tellimusi lõpule viia:

1. **Monitoorimine** tuvastab, et maksete töötlemise teenuse vastamise aeg on ebatavaliselt pikk.
2. **Jälgitavus** näitab, et viivitus tekib kindla panga API-ga suhtlemisel.
3. **Logid** paljastavad, et panga API tagastab spetsiifilise veakoodi, mis viitab võrguprobleemile.

Tänu sellele infole saab IT-meeskond:
- Kiiresti tuvastada probleemi põhjuse (konkreetse panga API tõrge).
- Rakendada ajutise lahenduse (suunata maksed alternatiivsele pangale).
- Võtta ühendust panga IT-toega spetsiifilise veakoodi infoga.

Ilma nende tööriistadeta võiks probleemi tuvastamine ja lahendamine võtta tunde või isegi päevi. Korrektse logimise, monitoorimise ja jälgitavusega saab seda teha minutitega.

### 14.2 Proaktiivne probleemide ennetamine

Teine oluline eelis on võime ennetada probleeme enne nende tekkimist.

#### Kuidas see toimib:
1. **Trendide analüüs**: Pikaajaliste mõõdikute jälgimine võimaldab tuvastada aeglaselt arenevaid probleeme.
2. **Anomaaliate tuvastamine**: Ebatavaliste mustrite kiire märkamine võib viidata peatselt tekkivatele probleemidele.
3. **Ressursside planeerimise**: Mõõdikud aitavad prognoosida tulevasi ressursivajadusi.

#### Praktiline näide:
Kujutage ette suurt veebirakendust, mis kasutab pilvepõhist infrastruktuuri:

1. **Monitoorimine** näitab, et mälu kasutus on viimase kuu jooksul järk-järgult suurenenud.
2. **Logid** paljastavad, et teatud tüüpi päringud muutuvad aja jooksul aeglasemaks.
3. **Jälgitavus** näitab, et aeglased päringud on seotud ühe konkreetse mikroteenusega.

Selle info põhjal saab IT-meeskond ennetavalt:
- Optimeerida problemaatilise mikroteenuse koodi, et vähendada mälu leket.
- Skaleerida teenust vertikaalselt, suurendades mälu enne, kui see muutub kriitiliseks probleemiks.
- Planeerida põhjalikum refaktoreerimine järgmisesse arendustsüklisse.

Proaktiivne lähenemine võimaldab vältida ootamatuid katkestusi ja säilitada kõrge teenuse kvaliteedi.

### 14.3 Parem otsuste tegemine

Kolmas oluline eelis on võime teha teadlikumaid otsuseid süsteemi arendamise ja haldamise osas.

#### Kuidas see toimib:
1. **Terviklik ülevaade**: Jälgitavus annab põhjaliku arusaama süsteemi toimimisest ja komponentide vahelistest seostest.
2. **Andmepõhised otsused**: Mõõdikud ja trendid võimaldavad teha otsuseid faktide, mitte oletuste põhjal.
3. **Mõju hindamine**: Muudatuste mõju saab kiiresti ja täpselt hinnata.

#### Praktiline näide:
Kujutage ette suurt teenusepõhist arhitektuuri kasutavat ettevõtet, mis kaalub oma infrastruktuuri uuendamist:

1. **Monitoorimine** näitab, et teatud teenused on alakoormatud, samas kui teised töötavad maksimaalse võimsuse piiril.
2. **Jälgitavus** paljastab, et mõned teenused on tihedalt seotud ja nende eraldamine põhjustab suurt võrguliiklust.
3. **Logid** näitavad, et teatud tüüpi päringud on eriti ressursimahukad.

Selle info põhjal saab IT-juhtkond:
- Otsustada konsolideerida alakoormatud teenused, vähendades infrastruktuuri kulusid.
- Planeerida tihedalt seotud teenuste koondamist ühele füüsilisele või virtuaalsele masinale, et vähendada võrguliiklust.
- Prioriseerida ressursimahukate päringute optimeerimist järgmises arendustsüklis.

Sellised andmepõhised otsused võimaldavad optimeerida ressursside kasutust, parandada süsteemi jõudlust ja vähendada kulusid.

### 14.4 Lisaeelised

Lisaks eelmainitud peamistele eelistele toob korrektne logimine, monitoorimine ja jälgitavus kaasa veel mitmeid olulisi hüvesid:

1. **Parem turvalisus**:
   - Ebatavalise käitumise kiire tuvastamine aitab avastada potentsiaalseid turvaohte.
   - Logid võimaldavad põhjalikku auditit turvaintsidentide korral.

2. **Efektiivsem meeskonnatöö**:
   - Ühine arusaam süsteemi toimimisest parandab suhtlust erinevate meeskondade vahel.
   - Vähendab "süüdistamise kultuuri", kuna probleemide põhjused on selgemini nähtavad.

3. **Parem kasutajakogemus**:
   - Võimaldab tuvastada ja lahendada probleeme enne, kui need mõjutavad lõppkasutajaid.
   - Aitab optimeerida süsteemi jõudlust, mis otseselt parandab kasutajakogemust.

4. **Vastavus regulatsioonidele**:
   - Detailsed logid ja auditijäljed aitavad täita erinevaid regulatiivseid nõudeid (nt GDPR, SOX).
   - Võimaldab kiiresti reageerida auditi päringutele.

5. **Innovatsiooni toetamine**:
   - Põhjalik ülevaade süsteemist võimaldab julgemalt katsetada uusi lahendusi.
   - Aitab kiiresti hinnata uute funktsioonide või arhitektuursete muudatuste mõju.

### 14.5 Väljakutsed ja parimad tavad

Kuigi logimine, monitoorimine ja jälgitavus pakuvad suuri eeliseid, toovad need kaasa ka väljakutseid:

1. **Andmete üleküllus**: 
   - Väljakutse: Liiga palju andmeid võib muuta olulise info leidmise keeruliseks.
   - Parim tava: Rakendage nutikaid filtreerimis- ja agregeerimisstrateegiaid. Keskenduge kõige olulisematele mõõdikutele ja sündmustele.

2. **Privaatsus ja turvalisus**: 
   - Väljakutse: Logid ja jäljed võivad sisaldada tundlikku infot.
   - Parim tava: Rakendage andmete maskeerimise ja krüpteerimise tehnikaid. Järgige rangelt andmekaitse regulatsioone.

3. **Ressursside kasutus**: 
   - Väljakutse: Ulatuslik logimine ja monitoorimine võib mõjutada süsteemi jõudlust.
   - Parim tava: Optimeerige logimise ja monitoorimise protsesse. Kaaluge logide ja mõõdikute saatmist eraldi infrastruktuurile.

4. **Meeskonna koolitus**: 
   - Väljakutse: Efektiivne logimise, monitoorimise ja jälgitavuse kasutamine nõuab oskusi.
   - Parim tava: Investeerige meeskonna koolitusse. Looge selged juhised ja parimad tavad.

Kokkuvõttes on korrektne logimine, monitoorimine ja jälgitavus hädavajalikud tööriistad tänapäeva IT-süsteemide efektiivseks haldamiseks. Need võimaldavad organisatsioonidel kiiresti reageerida probleemidele, ennetada tulevasi väljakutseid ja teha teadlikke otsuseid süsteemi arendamisel. Kuigi nende praktikate rakendamine võib esialgu tunduda keeruline, kaaluvad pikaajallised eelised üles algsed investeeringud.


## 15. Levinud väljakutsed logimise, monitoorimise ja jälgitavuse rakendamisel

Kuigi logimine, monitoorimine ja jälgitavus on hädavajalikud kaasaegsete IT-süsteemide haldamisel, toovad need kaasa ka mitmeid väljakutseid. Nende väljakutsete mõistmine ja nendega tegelemine on kriitilise tähtsusega eduka strateegia rakendamiseks.

### 15.1 Andmete üleküllus: Teabe üleujutuse haldamine

Üks peamisi väljakutseid on tohutu andmehulga haldamine, mida põhjalik logimine, monitoorimine ja jälgitavus tekitavad.

#### Probleemi kirjeldus:
- Liiga palju andmeid võib muuta olulise teabe leidmise keeruliseks.
- Suur andmemaht võib aeglustada analüüsiprotsesse.
- Ebaoluline info võib varjutada kriitilised sündmused.

#### Näide:
Kujutage ette suurt e-kaubanduse platvormi, mis logib iga kasutaja tegevuse, iga API päringu ja iga süsteemisündmuse. Tipptundidel võib see tähendada miljoneid logiridu tunnis. Kui nüüd tekib probleem (nt aeglased maksed), võib õige info leidmine sellest andmemassist olla kui nõela otsimine heinakuhjast.

#### Lahendused ja parimad tavad:
1. **Nutikad filtreerimismehhanismid**:
   - Rakendage dünaamilisi logimistasemeid (nt DEBUG, INFO, WARN, ERROR).
   - Kasutage kontekstipõhist logimist, mis suurendab detailsust ainult probleemsetel aladel.

2. **Andmete agregeerimine**:
   - Koondage sarnased sündmused ja esitage kokkuvõtted.
   - Kasutage statistilisi meetodeid anomaaliate tuvastamiseks.

3. **Efektiivsed otsingumehhanismid**:
   - Implementeerige võimsad otsingu- ja päringuvõimalused (nt Elasticsearch).
   - Looge eelseadistatud päringud ja dashboardid levinud stsenaariumide jaoks.

4. **Andmete säilitamise poliitikad**:
   - Määrake selged reeglid, kui kaua erinevat tüüpi andmeid säilitatakse.
   - Rakendage automaatset andmete arhiveerimist ja kustutamist.

5. **Masinõppe kasutamine**:
   - Rakendage masinõppel põhinevaid lahendusi anomaaliate tuvastamiseks ja olulise info esiletõstmiseks.

### 15.2 Tööriistade keerukus: Tehniliste lahenduste haldamine

Paljud jälgitavuse tööriistad on võimsad, kuid keerulised seadistada ja hallata, mis võib tekitada probleeme nende efektiivsel kasutamisel.

#### Probleemi kirjeldus:
- Keerulised seadistusprotsessid võivad põhjustada vigu ja ebaefektiivset kasutamist.
- Tööriistade täieliku potentsiaali ärakasutamine nõuab spetsiifilisi oskusi.
- Erinevate tööriistade integreerimine võib olla keeruline.

#### Näide:
Ettevõte otsustab kasutusele võtta ELK Stack'i (Elasticsearch, Logstash, Kibana) logide haldamiseks ja Prometheus'e monitoorimiseks. Kuigi need on võimsad tööriistad, nõuab nende õige seadistamine ja efektiivne kasutamine põhjalikke teadmisi. Näiteks Elasticsearch'i indeksite optimeerimine või Prometheus'e päringute kirjutamine võib olla keeruline ilma spetsiifilise koolituseta.

#### Lahendused ja parimad tavad:
1. **Investeerimine koolitusse**:
   - Pakkuge meeskonnale põhjalikku koolitust valitud tööriistade kohta.
   - Looge sisemine teadmusbaas ja parimad tavad.

2. **Alustage lihtsalt ja laiendage järk-järgult**:
   - Alustage põhifunktsionaalsusega ja lisage keerukamaid funktsioone aja jooksul.
   - Looge selge tegevuskava tööriistade funktsionaalsuse laiendamiseks.

3. **Standardiseeritud konfiguratsioonid**:
   - Looge eelseadistatud konfiguratsioonid ja mallid.
   - Kasutage infrastruktuuri kui koodi (Infrastructure as Code) põhimõtteid tööriistade seadistamiseks.

4. **Automatiseerimine**:
   - Automatiseerige võimalikult palju seadistus- ja haldusprotsesse.
   - Kasutage orkestreerimisvahendeid nagu Kubernetes tööriistade haldamiseks.

5. **Ekspertide kaasamine**:
   - Kaaluge väliste konsultantide kaasamist keerukamate seadistuste jaoks.
   - Looge suhted tööriistade tootjate või kogukonnaga toe saamiseks.

### 15.3 Ressursinõuded: Arvutus- ja salvestusressursside haldamine

Põhjalik logimine, monitoorimine ja jälgitavus võivad nõuda märkimisväärseid arvutus- ja salvestusressursse, mis võib olla väljakutse nii tehniliselt kui ka finantsiliselt.

#### Probleemi kirjeldus:
- Suur andmemaht nõuab märkimisväärset salvestusruumi.
- Reaalajas andmete töötlemine ja analüüs võib olla arvutusmahukas.
- Ressursside nõudlus võib kasvada koos süsteemi laienemisega.

#### Näide:
Suure kasutajaskonnaga veebirakendus otsustab rakendada detailse jälgitavuse, logides iga kasutaja tegevuse ja iga süsteemisündmuse. Peagi avastab IT-meeskond, et logide salvestamine nõuab terabaitides kettaruumi kuus ja logide töötlemine koormab oluliselt servereid, mõjutades rakenduse jõudlust.

#### Lahendused ja parimad tavad:
1. **Selektiivne logimine ja monitoorimine**:
   - Määrake selgelt, mis andmeid on tõesti vaja koguda.
   - Rakendage dünaamilisi logimistasemeid, et vajadusel suurendada või vähendada logimise detailsust.

2. **Andmete kompressioon**:
   - Kasutage efektiivseid kompressioonimeetodeid logide salvestamisel.
   - Kaaluge binaarformaatide kasutamist tekstipõhiste formaatide asemel, kus võimalik.

3. **Hajutatud arhitektuur**:
   - Jaotage andmete kogumine ja töötlemine mitme serveri vahel.
   - Kaaluge spetsialiseeritud riistvara kasutamist (nt FPGA-d) andmete töötlemiseks.

4. **Pilvelahenduste kasutamine**:
   - Kasutage elastseid pilveteenuseid, mis võimaldavad ressursse dünaamiliselt skaleerida.
   - Rakendage automaatset skaleerimist vastavalt koormusele.

5. **Andmete elutsükli haldamine**:
   - Määrake selged poliitikad andmete säilitamiseks ja arhiveerimiseks.
   - Automatiseerige vanemate andmete arhiveerimine või kustutamine.

6. **Optimeeritud andmebaasid**:
   - Kasutage spetsialiseeritud andmebaase (nt ajapõhised andmebaasid nagu InfluxDB) mõõdikute salvestamiseks.
   - Optimeerige indeksid ja päringud jõudluse parandamiseks.

7. **Koormuse vähendamise tehnikad**:
   - Rakendage proovivõttu (sampling) suure liiklusega süsteemides.
   - Kasutage puhverdamist (buffering) ja pakettide saatmist (batching) andmete edastamisel.

### 15.4 Integreeritud lähenemine väljakutsetele

Kõik need väljakutsed on omavahel seotud ja nõuavad integreeritud lähenemist:

1. **Strateegiline planeerimine**:
   - Looge terviklik strateegia, mis käsitleb nii andmete haldamist, tööriistade valikut kui ka ressursside planeerimist.
   - Kaasake kõik asjakohased osakonnad (IT, arendus, äriosakond) strateegia väljatöötamisse.

2. **Pidev optimeerimine**:
   - Vaadake regulaarselt üle oma logimis-, monitoorimis- ja jälgitavuse praktikad.
   - Koguge tagasisidet meeskondadelt ja kohandage lähenemist vastavalt.

3. **Tasakaalu leidmine**:
   - Leidke õige tasakaal põhjalikkuse ja efektiivsuse vahel.
   - Olge valmis tegema kompromisse, prioriseerides kõige olulisemat teavet.

4. **Kultuuriline muutus**:
   - Julgustage "mõõda kõike, aga mõistlikult" kultuuri.
   - Edendage andmepõhist otsustusprotsessi kogu organisatsioonis.

Kuigi logimine, monitoorimine ja jälgitavus toovad kaasa väljakutseid, on nende ületamine võimalik läbimõeldud strateegia, õigete tööriistade ja parimate tavade rakendamisega. Edukad organisatsioonid näevad neid väljakutseid mitte takistustena, vaid võimalustena optimeerida oma süsteeme ja protsesse, saavutades lõpuks parema ülevaate ja kontrolli oma IT-infrastruktuuri üle.

## 16. Alustamine logimise, monitoorimise ja jälgitavusega

Logimise, monitoorimise ja jälgitavuse rakendamine võib tunduda esmapilgul ülevoolav ülesanne, eriti kui teil on juba töötav süsteem. Siin on sammsammuline juhend, kuidas alustada, koos praktiliste nõuannete ja näidetega.

### 16.1 Alustage lihtsalt: Keskenduge põhilistele logidele ja mõõdikutele

Esimene samm on alustada põhiliste ja kõige olulisemate andmete kogumisega. Ärge proovige kohe kõike jälgida - see võib olla ülekoormav ja ebaefektiivne.

#### Praktilised sammud:
1. **Tuvastage kriitilised komponendid**: 
   - Näide: Veebirakenduse puhul võiksid need olla veebiserver, andmebaas ja peamised äriloogika teenused.

2. **Määrake põhilised sündmused**: 
   - Näide: Rakenduse käivitumine/sulgemine, kasutajate sisselogimine, olulised äritehingud.

3. **Valige esialgsed võtmemõõdikud**: 
   - Näide: CPU kasutus, mälu kasutus, vastamise aeg, vigade arv.

#### Näide alustava logimise strateegia kohta:
```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def process_order(order_id):
    logger.info(f"Alustatud tellimuse {order_id} töötlemine")
    try:
        # Tellimuse töötlemise loogika
        logger.info(f"Tellimus {order_id} edukalt töödeldud")
    except Exception as e:
        logger.error(f"Viga tellimuse {order_id} töötlemisel: {str(e)}")
```

### 16.2 Valige sobivad tööriistad: Kaaluge oma süsteemi vajadusi ja ressursse

Õigete tööriistade valimine on kriitilise tähtsusega. Arvestage oma süsteemi suurust, keerukust ja spetsiifilisi vajadusi.

#### Kaalutlused tööriistade valimisel:
1. **Süsteemi suurus ja keerukus**: 
   - Väiksema süsteemi jaoks võib piisata lihtsast logimislahendusest nagu ELK Stack.
   - Suurema ja keerukama süsteemi jaoks võib olla vajalik spetsialiseeritud lahendus nagu Datadog või New Relic.

2. **Eelarve**: 
   - Avatud lähtekoodiga lahendused vs. kommertslahendused.

3. **Integratsioonivõimalused**: 
   - Kas tööriist ühildub teie olemasoleva infrastruktuuriga?

4. **Skaleeritavus**: 
   - Kas tööriist kasvab koos teie süsteemiga?

5. **Õppimiskõver**: 
   - Kui keeruline on tööriista kasutama õppida ja juurutada?

#### Näide tööriistade valikust:
- **Logimine**: ELK Stack (Elasticsearch, Logstash, Kibana)
- **Monitoorimine**: Prometheus koos Grafanaga
- **Jälgitavus**: Jaeger või Zipkin

### 16.3 Looge logimise strateegia: Otsustage, mida ja kuidas logida

Efektiivne logimine nõuab läbimõeldud strateegiat. Määrake, millised sündmused on olulised ja kuidas neid logida.

#### Logimise strateegia elemendid:
1. **Logimistasemed**: 
   - Määrake, millal kasutada erinevaid tasemeid (DEBUG, INFO, WARN, ERROR).

2. **Logivorming**: 
   - Kasutage struktureeritud logiformaati (nt JSON) lihtsamaks analüüsiks.

3. **Konteksti lisamine**: 
   - Lisage igale logireale piisavalt konteksti (nt transaktsiooni ID, kasutaja ID).

4. **Tundliku info käsitlemine**: 
   - Vältige tundliku info (nt paroolid, krediitkaardi andmed) logimist.

5. **Logide säilitamine**: 
   - Määrake, kui kaua erinevat tüüpi logisid säilitada.

#### Näide logimise strateegiast:
```python
import json
import logging

class StructuredLogger:
    def __init__(self, name):
        self.logger = logging.getLogger(name)
        self.logger.setLevel(logging.INFO)

    def log(self, level, message, **kwargs):
        log_data = {
            "message": message,
            "level": level,
            **kwargs
        }
        self.logger.log(level, json.dumps(log_data))

    def info(self, message, **kwargs):
        self.log(logging.INFO, message, **kwargs)

    def error(self, message, **kwargs):
        self.log(logging.ERROR, message, **kwargs)

# Kasutamine
logger = StructuredLogger("my_app")
logger.info("Kasutaja sisselogitud", user_id="12345", ip_address="192.168.1.1")
```

### 16.4 Seadistage põhilised mõõdikud: Alustage olulisimate süsteemi näitajate jälgimisest

Mõõdikud annavad reaalajas ülevaate teie süsteemi tervisest ja jõudlusest.

#### Olulised mõõdikud alustamiseks:
1. **Ressursikasutus**: 
   - CPU, mälu, ketta kasutus

2. **Rakenduse jõudlus**: 
   - Vastamise aeg, läbilaskevõime

3. **Vigade määr**: 
   - HTTP 500 vigade arv, rakenduse erandid

4. **Ärilised mõõdikud**: 
   - Aktiivste kasutajate arv, tehingute arv

#### Näide Prometheus'e konfiguratsioonist:
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'my_app'
    static_configs:
      - targets: ['localhost:8000']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```

### 16.5 Õppige ja kohandage: Analüüsige kogutud andmeid ja kohandage oma lähenemist

Logimise, monitoorimise ja jälgitavuse rakendamine on iteratiivne protsess. Õppige kogutud andmetest ja kohandage oma lähenemist pidevalt.

#### Sammud pidevaks parandamiseks:
1. **Regulaarne ülevaatus**: 
   - Vaadake kogutud andmed regulaarselt üle (nt iganädalased ülevaatused).

2. **Tagasiside kogumine**: 
   - Küsige meeskonnalt tagasisidet logide ja mõõdikute kasulikkuse kohta.

3. **Trendide analüüs**: 
   - Otsige mustreid ja trende, mis võivad viidata süsteemi probleemidele või optimeerimise võimalustele.

4. **Dashboardide loomine**: 
   - Looge dashboardid, mis annavad kiire ülevaate süsteemi tervisest.

5. **Automaatsed hoiatused**: 
   - Seadistage hoiatused kriitiliste probleemide jaoks.

#### Näide Grafana dashboardist:
```json
{
  "panels": [
    {
      "title": "CPU Kasutus",
      "type": "graph",
      "datasource": "Prometheus",
      "targets": [
        {
          "expr": "100 - (avg by(instance) (irate(node_cpu_seconds_total{mode=\"idle\"}[5m])) * 100)"
        }
      ]
    },
    {
      "title": "Mälu Kasutus",
      "type": "gauge",
      "datasource": "Prometheus",
      "targets": [
        {
          "expr": "100 * (1 - ((node_memory_MemAvailable_bytes) / (node_memory_MemTotal_bytes)))"
        }
      ]
    }
  ]
}
```

### 16.6 Lõplikud soovitused alustamiseks

1. **Alustage väikeselt**: Ärge proovige kohe kõike jälgida. Alustage kõige olulisematest komponentidest ja laiendage järk-järgult.

2. **Kaasake meeskond**: Logimise, monitoorimise ja jälgitavuse rakendamine peaks olema meeskondlik pingutus. Kaasake kõik asjaosalised algusest peale.

3. **Dokumenteerige**: Looge selge dokumentatsioon oma logimise, monitoorimise ja jälgitavuse praktikate kohta.

4. **Planeeri tulevikku**: Mõelge, kuidas teie strateegia skaleerub, kui teie süsteem kasvab.

5. **Jätkuv õppimine**: Hoidke end kursis uute tööriistade ja parimatee tavadega selles valdkonnas.

Alustamine logimise, monitoorimise ja jälgitavusega võib tunduda keeruline, kuid järkjärguline lähenemine ja pidev õppimine aitavad luua efektiivse süsteemi. Alustage lihtsalt, keskenduge olulistele andmetele ja kohandage oma lähenemist vastavalt saadud kogemustele. Aja jooksul areneb teie võime süsteemi mõista ja hallata, võimaldades teil ennetada probleeme ja optimeerida jõudlust.