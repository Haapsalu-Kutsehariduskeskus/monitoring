
- **Main Lecture is here:** [Prom material](https://docs.google.com/document/d/1NMjHC8qi0RvVOfqVcUhHdsTeAOQCCoa6FQQJv3Tsz74/edit?usp=sharing).

## Kokkuvõtte

## Mis on Prometheus?

Prometheus on avatud lähtekoodiga jälgimissüsteem ja aegridade andmebaas, mis on spetsiaalselt loodud mikroteenuste ja konteineriseeritud keskkondade jälgimiseks. See on üks populaarsemaid tööriistu kaasaegsetes DevOps ja pilverakendustes.

## Prometheus'e Põhiomadused

### 1. Kraapimismeetod (Scrape Method)
- Prometheus kasutab **pull-based** lähenemist metrikate kogumiseks
- Süsteem "kraabib" (scrapes) andmeid HTTP lõpp-punktidest regulaarsete intervallidega
- Rakendused peavad pakkuma spetsiaalset metrikate lõpp-punkti (tavaliselt `/metrics`)

### 2. Aegridade Andmed
- Salvestab kõik andmed **aegridade andmebaasi**
- Iga mõõtmispunkt sisaldab:
  - Ajatemplit
  - Metrika nime
  - Silte (labels)
  - Väärtust

### 3. Võimas Päringukeel (PromQL)
- Võimaldab keerukaid päringuid ja agregatsioone
- Toetab matemaatilisi operatsioone
- Võimaldab andmete visualiseerimist ja hoiatuste seadistamist

## Prometheus'e Arhitektuur

### Põhikomponendid:
1. **Prometheus Server**
   - Metrikate kogumine
   - Andmete salvestamine
   - Päringute töötlemine

2. **Exporters**
   - Spetsiaalsed agendid, mis koguvad metrikaid erinevatest süsteemidest
   - Näited:
     - Node Exporter (süsteemi metrikad)
     - MySQL Exporter (andmebaasi metrikad)
     - Blackbox Exporter (väliste teenuste jälgimine)

3. **Alert Manager**
   - Hoiatuste haldamine ja edastamine
   - Hoiatuste grupeerimine ja marsruutimine

4. **Visualiseerimistööriistad**
   - Grafana (kõige populaarsem)
   - Prometheus'e sisseehitatud UI

## Metrikate Kogumise Protsess

1. **Seadistamine**
   - Määratakse sihtmärgid `prometheus.yml` failis
   - Määratakse kraapimise intervall
   - Seadistatakse vajalikud sildid

2. **Kraapimine**
```yaml
scrape_configs:
  - job_name: 'minu-rakendus'
    scrape_interval: 15s
    static_configs:
      - targets: ['localhost:8080']
```

3. **Andmete Salvestamine**
   - Metrikad salvestatakse lokaalses andmebaasis
   - Vaikimisi säilitusaeg on 15 päeva

## Peamised Metrika Tüübid

1. **Counter**
   - Ainult kasvav väärtus
   - Näide: HTTP päringute koguarv

2. **Gauge**
   - Võib tõusta ja langeda
   - Näide: Mälu kasutus

3. **Histogram**
   - Mõõdab väärtuste jaotust
   - Näide: Päringu vastuse aeg

4. **Summary**
   - Sarnane histogrammile
   - Arvutab kvantiile serveripoolel

## Prometheus vs Teised Jälgimissüsteemid

### Eelised:
- Lihtne seadistada ja kasutada
- Skaalauvus ja töökindlus
- Tugev kogukond ja integratsioonid
- Pull-based mudel lihtsustab turvalisust

### Väljakutsed:
- Piiratud pikaajaline andmete säilitamine
- Ei sobi kõrgeks kättesaadavuseks ilma lisatööriistadeta
- Pull-mudel võib olla keeruline mõnedes võrgukonfiguratsioonides

## Parimad Praktikad

1. **Metrikate Nimetamine**
   - Kasuta selgeid, kirjeldavaid nimesid
   - Järgi standardset nimekonventsiooni
   - Lisa asjakohased sildid

2. **Hoiatuste Seadistamine**
   - Määra selged läved
   - Väldi valepositiivseid hoiatusi
   - Grupeeri seotud hoiatused

3. **Andmete Säilitamine**
   - Planeeri andmemaht
   - Seadista sobiv säilitusaeg
   - Kasuta vajadusel välist salvestust

## Kokkuvõte

Prometheus on võimas ja paindlik jälgimissüsteem, mis sobib eriti hästi mikroteenuste ja konteineriseeritud keskkondade jälgimiseks. Selle peamised tugevused on:
- Lihtne seadistamine ja kasutamine
- Skaalauvus ja töökindlus
- Võimas päringukeel
- Tugev kogukondlik tugi

Õige seadistamise ja kasutamisega aitab Prometheus tagada süsteemide töökindluse ja probleemide kiire tuvastamise.