# Grafana: Basics

## Sisukord

1. [Sissejuhatus Grafanasse](#1-sissejuhatus-grafanasse)
   - [Mis on Grafana?](#mis-on-grafana)
   - [Grafana ajalugu ja areng](#grafana-ajalugu-ja-areng)
   - [Grafana roll kaasaegses IT-infrastruktuuris](#grafana-roll-kaasaegses-it-infrastruktuuris)

2. [Grafana paigaldamine ja seadistamine](#2-grafana-paigaldamine-ja-seadistamine)
   - [Süsteeminõuded](#süsteeminõuded)
   - [Grafana paigaldamine Linuxi masinas](#grafana-paigaldamine-linuxi-masinas)
   - [Esmane konfigureerimine](#esmane-konfigureerimine)
   - [Grafana uuendamine](#grafana-uuendamine)

3. [Grafana kasutajaliidese ülevaade](#3-grafana-kasutajaliidese-ülevaade)
   - [Põhikomponendid ja navigeerimine](#põhikomponendid-ja-navigeerimine)
   - [Kasutajate ja meeskondade haldamine](#kasutajate-ja-meeskondade-haldamine)
   - [Üldised seaded ja eelistused](#üldised-seaded-ja-eelistused)

4. [Andmeallikate integreerimine](#4-andmeallikate-integreerimine)
   - [Toetatud andmeallikad](#toetatud-andmeallikad)
   - [Andmeallika lisamine ja konfigureerimine](#andmeallika-lisamine-ja-konfigureerimine)
   - [Päringute koostamine erinevatele andmeallikatele](#päringute-koostamine-erinevatele-andmeallikatele)
   - [Andmeallikate turvalisus ja jõudlus](#andmeallikate-turvalisus-ja-jõudlus)

5. [Töölaudade loomine ja haldamine](#5-töölaudade-loomine-ja-haldamine)
   - [Töölauda põhimõtted ja parimad praktikad](#töölauda-põhimõtted-ja-parimad-praktikad)
   - [Paneelide lisamine ja konfigureerimine](#paneelide-lisamine-ja-konfigureerimine)
   - [Visualiseeringute tüübid ja nende kasutamine](#visualiseeringute-tüübid-ja-nende-kasutamine)
   - [Muutujate ja mallide kasutamine töölaudadel](#muutujate-ja-mallide-kasutamine-töölaudadel)

6. [Grafana hoiatuste süsteem](#6-grafana-hoiatuste-süsteem)
   - [Hoiatuste loomine ja seadistamine](#hoiatuste-loomine-ja-seadistamine)
   - [Hoiatuste kanalid ja teavitused](#hoiatuste-kanalid-ja-teavitused)
   - [Hoiatuste haldamine ja probleemide lahendamine](#hoiatuste-haldamine-ja-probleemide-lahendamine)

7. [Logide analüüs Grafanas](#7-logide-analüüs-grafanas)
   - [Loki integreerimine](#loki-integreerimine)
   - [Logipäringute koostamine](#logipäringute-koostamine)
   - [Logide visualiseerimine ja analüüs](#logide-visualiseerimine-ja-analüüs)

8. [Grafana jõudluse optimeerimine](#8-grafana-jõudluse-optimeerimine)
   - [Ressursside kasutuse jälgimine](#ressursside-kasutuse-jälgimine)
   - [Päringute optimeerimine](#päringute-optimeerimine)
   - [Skaleerimise strateegiad](#skaleerimise-strateegiad)

9. [Grafana turvalisus](#9-grafana-turvalisus)
   - [Autentimise ja autoriseerimise seadistamine](#autentimise-ja-autoriseerimise-seadistamine)
   - [SSL/TLS konfigureerimine](#ssltls-konfigureerimine)
   - [Andmete krüpteerimine ja turvalisuse parimad praktikad](#andmete-krüpteerimine-ja-turvalisuse-parimad-praktikad)

10. [Grafana laiendamine ja kohandamine](#10-grafana-laiendamine-ja-kohandamine)
    - [Pluginate paigaldamine ja kasutamine](#pluginate-paigaldamine-ja-kasutamine)
    - [Kohandatud paneelide ja visualiseeringute loomine](#kohandatud-paneelide-ja-visualiseeringute-loomine)
    - [Grafana API kasutamine automatiseerimiseks](#grafana-api-kasutamine-automatiseerimiseks)

11. [Grafana ja DevOps praktikad](#11-grafana-ja-devops-praktikad)
    - [Grafana integreerimine CI/CD protsessidega](#grafana-integreerimine-cicd-protsessidega)
    - [Infrastruktuuri kui koodi (IaC) põhimõtted Grafanaga](#infrastruktuuri-kui-koodi-iac-põhimõtted-grafanaga)
    - [Grafana kasutamine mikroteenuste arhitektuuris](#grafana-kasutamine-mikroteenuste-arhitektuuris)

12. [Juhtumiuuringud ja parimad praktikad](#12-juhtumiuuringud-ja-parimad-praktikad)
    - [Reaalsed näited Grafana kasutamisest ettevõtetes](#reaalsed-näited-grafana-kasutamisest-ettevõtetes)
    - [Levinud probleemid ja nende lahendused](#levinud-probleemid-ja-nende-lahendused)
    - [Tulevikutrendid ja Grafana arengusuunad](#tulevikutrendid-ja-grafana-arengusuunad)

13. [Lisad](#13-lisad)
    - [Grafana käsurea tööriistad](#grafana-käsurea-tööriistad)
    - [Kasulikud ressursid ja kogukonna tugi](#kasulikud-ressursid-ja-kogukonna-tugi)
    - [Sõnastik](#sõnastik)

# 1. Sissejuhatus Grafanasse

## 1.1 Mis on Grafana?

Grafana on avatud lähtekoodiga platvorm andmete visualiseerimiseks ja analüüsiks. See võimaldab kasutajatel luua interaktiivseid töölaudu erinevatest andmeallikatest.

**Põhiomadused:**
- Mitmekülgsed andmeallikad (nt. Prometheus, InfluxDB, MySQL)
- Dünaamilised ja kohandatavad töölauad
- Hoiatuste süsteem
- Kasutajate haldus ja juurdepääsukontroll

**Näide: Lihtne Grafana töölaud**
```
+----------------------------------+
|  Serveri jõudlus                 |
|  +------------+  +------------+  |
|  | CPU        |  | Mälu       |  |
|  | kasutus    |  | kasutus    |  |
|  +------------+  +------------+  |
|  +------------+  +------------+  |
|  | Võrgu      |  | Ketta      |  |
|  | liiklus    |  | kasutus    |  |
|  +------------+  +------------+  |
+----------------------------------+
```

## 1.2 Grafana ajalugu ja areng

- **2014**: Grafana 1.0 väljalase
- **2016**: Grafana Labs asutamine
- **2019**: Loki (logihaldussüsteem) tutvustamine
- **2021**: Tempo (hajusjälgimissüsteem) väljalase

**Võtmesündmus:** Grafana 7.0 (2020) tõi kaasa uue hoiatuste süsteemi ja täiustatud andmeallikate toe.

## 1.3 Grafana roll kaasaegses IT-infrastruktuuris

Grafana on keskne tööriist paljudes IT-operatsioonides:

1. **Infrastruktuuri monitooring**
   - Näide: Serverite CPU ja mälu kasutuse jälgimine
   ```promql
   100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
   ```

2. **Rakenduste jõudluse analüüs**
   - Näide: Veebirakenduse vastamisaja jälgimine
   ```sql
   SELECT mean("response_time") FROM "http_requests" WHERE $timeFilter GROUP BY time($__interval)
   ```

3. **Logide analüüs**
   - Näide: Vigade sageduse jälgimine logides
   ```logql
   count_over_time({job="app"} |= "error" [5m])
   ```

4. **Ärimõõdikute visualiseerimine**
   - Näide: Igapäevaste müükide jälgimine
   ```sql
   SELECT date, SUM(amount) FROM sales WHERE $timeFilter GROUP BY date
   ```

# 2. Grafana paigaldamine ja seadistamine

## 2.1 Süsteeminõuded

Grafana töötab enamikel Linuxi distributsioonidel. Minimaalsed nõuded:
- 1 CPU tuum
- 2 GB RAM
- 10 GB vaba kettaruumi

**Toetatud operatsioonisüsteemid:**
- Ubuntu / Debian
- CentOS / RedHat
- macOS (arenduseks)

## 2.2 Grafana paigaldamine Linuxi masinas

Näide Ubuntu/Debian süsteemis:

1. Vajalike pakettide paigaldamine:
   ```bash
   sudo apt-get install -y apt-transport-https software-properties-common wget
   ```

2. Grafana GPG võtme lisamine:
   ```bash
   wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
   ```

3. Grafana repositooriumi lisamine:
   ```bash
   echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
   ```

4. Pakettide nimekirja uuendamine ja Grafana paigaldamine:
   ```bash
   sudo apt-get update
   sudo apt-get install grafana
   ```

5. Grafana teenuse käivitamine:
   ```bash
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server
   ```

## 2.3 Esmane konfigureerimine

1. Avage veebibrauser ja minge `http://localhost:3000`
   (või asendage 'localhost' serveri IP-aadressiga)

2. Sisselogimiseks kasutage vaikimisi kasutajanime ja parooli:
   - Kasutajanimi: `admin`
   - Parool: `admin`

3. Muutke koheselt vaikimisi parool

4. Põhilised konfiguratsioonifailid:
   - `/etc/grafana/grafana.ini`: Peamine konfiguratsioonifail
   - `/etc/grafana/provisioning/`: Automaatse seadistamise failid

Näide: SMTP seadistamine e-posti teavituste jaoks (`grafana.ini`):
```ini
[smtp]
enabled = true
host = smtp.gmail.com:587
user = your_email@gmail.com
password = your_password
```

## 2.4 Grafana uuendamine

Uuendamise protsess Ubuntu/Debian süsteemis:

1. Varundage olemasolev konfiguratsioon:
   ```bash
   sudo cp /etc/grafana/grafana.ini /etc/grafana/grafana.ini.bak
   ```

2. Uuendage paketid:
   ```bash
   sudo apt-get update
   sudo apt-get upgrade grafana
   ```

3. Taaskäivitage Grafana teenus:
   ```bash
   sudo systemctl restart grafana-server
   ```

**Oluline:** Enne uuendamist kontrollige alati ametlikku dokumentatsiooni võimalike muudatuste või probleemide osas.

# 3. Grafana kasutajaliidese ülevaade

## 3.1 Põhikomponendid ja navigeerimine

Grafana kasutajaliides koosneb mitmest põhikomponendist:

1. **Peamenüü** (vasakul):
   - Töölauad (Dashboards)
   - Uurimine (Explore)
   - Hoiatused (Alerting)
   - Konfiguratsioon (Configuration)

2. **Ülemine riba**:
   - Otsing
   - Uue töölauda loomine
   - Ajavahemiku valik
   - Kasutaja seaded

3. **Peamine tööala**:
   - Töölaudade ja paneelide kuvamine
   - Seadistuste muutmine

Näide navigeerimisstruktuurist:
```
Peamenüü
├── Töölauad
│   ├── Uus töölaud
│   ├── Impordi
│   └── Kaustad
├── Uurimine
├── Hoiatused
│   ├── Hoiatusreeglid
│   ├── Kontaktpunktid
│   └── Teavituste ajalugu
└── Konfiguratsioon
    ├── Andmeallikad
    ├── Kasutajad
    ├── Meeskonnad
    └── Pluginad
```

## 3.2 Kasutajate ja meeskondade haldamine

Grafanas on mitmeastmeline kasutajate haldamise süsteem:

1. **Kasutajate rollid**:
   - Admin: Täielik juurdepääs
   - Editor: Saab luua ja muuta töölaudu
   - Viewer: Saab ainult vaadata

2. **Meeskondade loomine**:
   ```
   Konfiguratsioon -> Meeskonnad -> Uus meeskond
   ```

3. **Õiguste määramine**:
   - Töölaua tasemel
   - Kausta tasemel
   - Organisatsiooni tasemel

Näide meeskonna loomisest:
1. Minge "Configuration" -> "Teams"
2. Klõpsake "New team"
3. Sisestage meeskonna nimi (nt "DevOps")
4. Lisage liikmed e-posti aadresside kaudu
5. Määrake meeskonnale õigused konkreetsetele töölaudadele

## 3.3 Üldised seaded ja eelistused

Grafana pakub mitmeid kohandamisvõimalusi:

1. **Organisatsiooni seaded**:
   ```
   Konfiguratsioon -> Preferences
   ```
   - Vaikimisi teema
   - Kodukausta seadistamine
   - Ajavööndi valik

2. **Kasutaja eelistused**:
   ```
   Kasutaja ikoon -> Preferences
   ```
   - Keele valik
   - Teema (hele/tume)
   - Töölauda eelistused

3. **Töölaua seaded**:
   ```
   Töölaud -> Settings (hammasratta ikoon)
   ```
   - Töölauda nimi ja kirjeldus
   - Märksõnad
   - Ajavahemiku seaded

Näide kohandatud teema loomisest:
1. Minge "Configuration" -> "Preferences"
2. Valige "New custom theme"
3. Kohandage värve (nt taustavärv: #1a1a1a, tekstivärv: #ffffff)
4. Salvestage ja rakendage uus teema

# 4. Andmeallikate integreerimine

## 4.1 Toetatud andmeallikad

Grafana toetab paljusid erinevaid andmeallikaid, sealhulgas:

1. Aegridade andmebaasid:
   - Prometheus
   - InfluxDB
   - Graphite

2. Relatsioonilised andmebaasid:
   - MySQL
   - PostgreSQL
   - Microsoft SQL Server

3. Logide andmebaasid:
   - Loki
   - Elasticsearch

4. Pilveteenused:
   - AWS CloudWatch
   - Google Cloud Monitoring
   - Azure Monitor

## 4.2 Andmeallika lisamine ja konfigureerimine

Üldine protsess andmeallika lisamiseks:

1. Minge: Configuration -> Data Sources
2. Klõpsake "Add data source"
3. Valige andmeallika tüüp
4. Sisestage ühenduse andmed
5. Testige ühendust
6. Salvestage ja lõpetage

Näide: Prometheus andmeallika lisamine

```
Nimi: Minu Prometheus
URL: http://prometheus:9090
Juurdepääs: Server (vaikimisi)
```

Põhilised seaded:
- Scrape interval: 15s
- Query timeout: 60s
- HTTP Method: GET

## 4.3 Päringute koostamine erinevatele andmeallikatele

Iga andmeallikas kasutab oma päringukeelt. Siin on mõned näited:

1. **Prometheus (PromQL)**:
   CPU kasutuse päring:
   ```promql
   100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
   ```

2. **InfluxDB (InfluxQL)**:
   Mälu kasutuse päring:
   ```sql
   SELECT mean("used_percent") FROM "mem" WHERE $timeFilter GROUP BY time($__interval) fill(null)
   ```

3. **MySQL**:
   Viimase 24 tunni tellimuste arv:
   ```sql
   SELECT 
     $__timeGroup(created_at,'1h') as time,
     count(*) as 'orders'
   FROM orders
   WHERE $__timeFilter(created_at)
   GROUP BY 1
   ORDER BY 1
   ```

## 4.4 Andmeallikate turvalisus ja jõudlus

Turvalisuse tagamiseks:

1. Kasutage HTTPS-i kõikide väliste ühenduste jaoks
2. Looge eraldi kasutajakontod Grafana jaoks minimaalse vajaliku õiguste hulgaga
3. Kasutage võrgu segmenteerimist, et eraldada Grafana ja andmeallikad

Jõudluse optimeerimiseks:

1. Seadistage sobiv päringu ajalimiit (Query Timeout)
2. Kasutage vahemälu (Caching), kui andmeallikas seda toetab
3. Optimeerige päringuid, vältides liiga suuri andmehulki

Näide: Prometheus vahemälu seadistamine
```yaml
# Grafana.ini
[unified_alerting.prometheus]
# Ajaline vahemik, mille jooksul andmeid puhverdatakse
query_cache_time = 60s
```

# 5. Töölaudade loomine ja haldamine

## 5.1 Töölauda põhimõtted ja parimad praktikad

Töölauad on Grafana keskne element, mis koondavad visualiseeringud ja mõõdikud.

Parimad praktikad:
1. Järgige loogilist struktuuri
2. Kasutage järjepidevat värviskeemi
3. Grupeerige seotud paneelid
4. Lisage selgitavad märkused

Näide töölauda struktuurist:
```
+----------------------------------+
|  Rakenduse jõudlus               |
|  +------------+  +------------+  |
|  | Päringute  |  | Vastuse    |  |
|  | arv        |  | aeg        |  |
|  +------------+  +------------+  |
|  +------------+  +------------+  |
|  | Vead       |  | Aktiivs-   |  |
|  |            |  | ed seansid |  |
|  +------------+  +------------+  |
+----------------------------------+
```

## 5.2 Paneelide lisamine ja konfigureerimine

Paneeli lisamise protsess:
1. Klõpsake "Add panel"
2. Valige visualiseerimise tüüp
3. Konfigureerige andmeallikas ja päring
4. Kohandage visualiseeringut
5. Salvestage paneel

Näide: CPU kasutuse paneeli lisamine
```yaml
Andmeallikas: Prometheus
Päring: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
Visualiseering: Graafik
Pealkiri: CPU kasutus
Ühik: Percent (0-100)
```

## 5.3 Visualiseeringute tüübid ja nende kasutamine

Grafana pakub mitmeid visualiseerimistüüpe:

1. **Graafikud**: Ajaliste andmete kuvamiseks
   ```yaml
   Tüüp: Time series
   Stiil: Lines
   Fill opacity: 20
   Point size: 5
   ```

2. **Mõõdikud**: Üksikute väärtuste kuvamiseks
   ```yaml
   Tüüp: Gauge
   Min: 0
   Max: 100
   Thresholds: 
     - 0-60: Green
     - 60-80: Yellow
     - 80-100: Red
   ```

3. **Tabelid**: Detailsete andmete esitamiseks
   ```yaml
   Tüüp: Table
   Columns:
     - Instance
     - Value
     - Time
   Sorting: Value (descending)
   ```

4. **Stat**: Üksiku statistilise väärtuse kuvamiseks
   ```yaml
   Tüüp: Stat
   Calculation: Last
   Color mode: Value
   Thresholds: Similar to Gauge
   ```

## 5.4 Muutujate ja mallide kasutamine töölaudadel

Muutujad võimaldavad dünaamiliselt filtreerida ja kohandada töölaudu.

Näide muutuja loomisest:
1. Minge töölauda seadetesse
2. Valige "Variables"
3. Klõpsake "New"
4. Valige muutuja tüüp (nt. Query)

```yaml
Nimi: server
Tüüp: Query
Andmeallikas: Prometheus
Päring: label_values(node_cpu_seconds_total, instance)
```

Muutuja kasutamine päringus:
```promql
node_memory_MemTotal_bytes{instance="$server"}
```

# 6. Grafana hoiatuste süsteem

## 6.1 Hoiatuste loomine ja seadistamine

Grafana hoiatuste süsteem võimaldab automaatselt tuvastada ja reageerida ebanormaalsetele olukordadele teie süsteemides.

Hoiatuse loomise põhisammud:
1. Minge: Alerting -> Alert rules
2. Klõpsake "New alert rule"
3. Määrake tingimused
4. Seadistage hoiatuse käitumine
5. Määrake teavituskanalid

Näide: CPU kasutuse hoiatus
```yaml
Nimi: Kõrge CPU kasutus
Päring: 
  A: avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
Tingimus: when A is below 10
For: 5m
Raskusaste: High
```

## 6.2 Hoiatuste kanalid ja teavitused

Grafana toetab mitmeid teavituskanaleid:
- E-post
- Slack
- PagerDuty
- Webhook

Näide: Slack teavituskanali seadistamine
1. Minge: Alerting -> Contact points
2. Klõpsake "New contact point"
3. Valige tüübiks "Slack"
4. Sisestage Slacki webhook URL
```yaml
Nimi: Slack-Ops
Tüüp: Slack
Webhook URL: https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
Kanal: #ops-alerts
```

## 6.3 Hoiatuste haldamine ja probleemide lahendamine

Hoiatuste haldamise parimad praktikad:
1. Kasutage selgeid ja kirjeldavaid nimesid
2. Grupeerige seotud hoiatused
3. Kasutage erinevaid raskusastmeid
4. Regulaarselt vaadake üle ja uuendage hoiatusi

Hoiatuste vaigistamine:
```yaml
Minge: Alerting -> Silences
Klõpsake: New Silence
Määrake:
  - Algusaeg
  - Lõppaeg
  - Vaigistamise põhjus
  - Hoiatuste filtrid
```

Näide: Hoiatuste grupeerimine
```yaml
Grupp: Serveri jõudlus
Reeglid:
  - Kõrge CPU kasutus
  - Madal vaba mälu
  - Kõrge ketta kasutus
```
# 7. Logide analüüs Grafanas

## 7.1 Loki integreerimine

Loki on Grafana Labsi poolt loodud logide agregeerimise süsteem, mis on tihedalt integreeritud Grafanaga.

Loki seadistamine:
1. Paigaldage Loki (nt Docker konteinerina)
2. Lisage Loki andmeallikas Grafanas:
   ```yaml
   Nimi: Minu Loki
   URL: http://loki:3100
   ```

Promtail seadistamine (logide saatmiseks Lokisse):
```yaml
server:
  http_listen_port: 9080

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: system
    static_configs:
    - targets:
        - localhost
      labels:
        job: varlogs
        __path__: /var/log/*log
```

## 7.2 Logipäringute koostamine

Loki kasutab LogQL päringukeelt, mis on sarnane PromQL-ile.

Põhilised päringutüübid:
1. Logiridade otsing
2. Logi kirjete loendamine

Näited:
1. Kõik "error" sisaldavad logiread:
   ```logql
   {job="varlogs"} |= "error"
   ```

2. HTTP 500 vigade loendamine 5-minutiliste intervallidena:
   ```logql
   sum(rate({job="nginx"} |= "HTTP/1.1\" 500" [5m])) by (status)
   ```

## 7.3 Logide visualiseerimine ja analüüs

Grafana pakub mitmeid võimalusi logide visualiseerimiseks:

1. Logs paneel:
   - Kuvab logiridu kronoloogilises järjekorras
   - Võimaldab otsingut ja filtreerimist

2. Logi muster (Log pattern):
   - Tuvastab automaatselt korduvaid mustreid logides

3. Logide statistika:
   - Loob kokkuvõtteid ja graafikuid logidest

Näide: Logide visualiseerimine
1. Looge uus paneel
2. Valige visualiseerimise tüübiks "Logs"
3. Kasutage järgmist päringut:
   ```logql
   {job="varlogs"}
   | json
   | line_format "{{.level}} - {{.message}}"
   ```

# 8. Grafana jõudluse optimeerimine

## 8.1 Ressursside kasutuse jälgimine

Grafana jõudluse optimeerimiseks on oluline jälgida süsteemi ressursside kasutust.

Peamised jälgitavad mõõdikud:
1. CPU kasutus
2. Mälu kasutus
3. Ketta I/O
4. Võrgu läbilaskevõime

Grafana enda jõudluse jälgimine:
1. Kasutage Grafana sisseehitatud mõõdikuid:
   ```
   http://your-grafana-instance/metrics
   ```
2. Looge töölaud nende mõõdikute visualiseerimiseks:

Näide päringutest:
```promql
# Grafana HTTP päringute arv
sum(rate(grafana_http_request_duration_seconds_count[5m])) by (handler)

# Grafana mälu kasutus
process_resident_memory_bytes{job="grafana"}

# Grafana CPU kasutus
rate(process_cpu_seconds_total{job="grafana"}[5m])
```

## 8.2 Päringute optimeerimine

Efektiivsed päringud on kriitilise tähtsusega Grafana jõudluse tagamisel.

Päringute optimeerimise strateegiad:
1. Kasutage sobivaid ajavahemikke
2. Vältige liiga sagedast andmete pärimist
3. Kasutage agregeerimisfunktsioone

Näide optimeeritud vs optimeerimata päring:

Optimeerimata:
```promql
http_requests_total
```

Optimeeritud:
```promql
sum(rate(http_requests_total[5m])) by (status_code)
```

## 8.3 Skaleerimise strateegiad

Grafana skaleerimiseks suuremate koormuste jaoks:

1. Vertikaalne skaleerimine:
   - Suurendage serveri ressursse (CPU, RAM)

2. Horisontaalne skaleerimine:
   - Kasutage mitut Grafana instantsi koos koormuse tasakaalustajaga

3. Andmebaasi optimeerimine:
   - Eraldage Grafana andmebaas eraldi serverisse
   - Kasutage andmebaasi replikatsiooni

4. Vahemälu kasutamine:
   - Seadistage Redis vahemälu kiire andmete juurdepääsu jaoks

Näide Redis vahemälu konfiguratsioonist (`grafana.ini`):
```ini
[caching]
enabled = true
backend = redis
redis_url = redis://localhost:6379/0
```

# 9. Grafana turvalisus

## 9.1 Autentimise ja autoriseerimise seadistamine

Grafana pakub mitmeid autentimise võimalusi:

1. Sisemine autentimine
2. LDAP/Active Directory
3. OAuth (Google, GitHub, jne)
4. SAML

Näide LDAP konfiguratsiooni seadistamisest (`grafana.ini`):

```ini
[auth.ldap]
enabled = true
config_file = /etc/grafana/ldap.toml

[server]
root_url = https://grafana.yourdomain.com
```

LDAP konfiguratsioonifail (`ldap.toml`):

```toml
[[servers]]
host = "ldap.yourdomain.com"
port = 389
use_ssl = false
start_tls = true
bind_dn = "cn=admin,dc=yourdomain,dc=com"
bind_password = 'grafana'
search_filter = "(sAMAccountName=%s)"
search_base_dns = ["dc=yourdomain,dc=com"]

[servers.attributes]
name = "givenName"
surname = "sn"
username = "sAMAccountName"
member_of = "memberOf"
email =  "email"

[[servers.group_mappings]]
group_dn = "cn=admins,dc=yourdomain,dc=com"
org_role = "Admin"
```

## 9.2 SSL/TLS konfigureerimine

Grafana turvaliseks ühenduseks on oluline kasutada HTTPS-i.

SSL/TLS seadistamine:

1. Hangi SSL sertifikaat (nt Let's Encrypt)
2. Konfigureeri Grafana kasutama HTTPS-i:

```ini
[server]
protocol = https
cert_file = /path/to/cert.pem
cert_key = /path/to/cert.key
```

## 9.3 Andmete krüpteerimine ja turvalisuse parimad praktikad

Andmete krüpteerimine:

1. Andmebaasi ühenduse krüpteerimine:

```ini
[database]
type = postgres
host = localhost:5432
name = grafana
user = grafanauser
password = ${__env:GF_DATABASE_PASSWORD}
ssl_mode = require
```

2. Tundlike andmete krüpteerimine:

```ini
[security]
encryption_provider = aes
secret_key = <32-byte-key>
```

Turvalisuse parimad praktikad:

1. Kasuta tugevaid paroole
2. Piira võrgujuurdepääsu
3. Uuenda Grafanat regulaarselt
4. Jälgi ja logi turvalisuse sündmusi
5. Rakenda vähimate õiguste põhimõtet

# 10. Grafana laiendamine ja kohandamine

## 10.1 Pluginate paigaldamine ja kasutamine

Grafana pluginad võimaldavad laiendada funktsionaalsust, lisades uusi andmeallikaid, paneele või rakendusi.

Plugina paigaldamine käsurea kaudu:
```bash
grafana-cli plugins install <plugin-id>
```

Näide: Worldmap Paneli paigaldamine:
```bash
grafana-cli plugins install grafana-worldmap-panel
```

Pluginate haldamine Grafana kasutajaliideses:
1. Minge: Configuration -> Plugins
2. Sirvige saadaolevaid pluginaid
3. Klõpsake "Install" soovitud plugina juures

## 10.2 Kohandatud paneelide ja visualiseeringute loomine

Grafana võimaldab luua kohandatud paneele React ja TypeScript abil.

Kohandatud paneeli loomise põhisammud:

1. Seadistage arenduskeskkond:
```bash
npx @grafana/toolkit plugin:create my-custom-panel
cd my-custom-panel
yarn install
yarn dev
```

2. Muutke `src/module.ts` faili:
```typescript
import { PanelPlugin } from '@grafana/data';
import { SimplePanel } from './SimplePanel';

export const plugin = new PanelPlugin<SimpleOptions>(SimplePanel).setPanelOptions(builder => {
  return builder
    .addTextInput({
      path: 'text',
      name: 'Simple text option',
      description: 'Description of panel option',
      defaultValue: 'Default value of text input option',
    });
});
```

3. Muutke `src/SimplePanel.tsx` faili vastavalt oma vajadustele.

4. Ehitage plugin:
```bash
yarn build
```

5. Kopeerige plugin Grafana pluginate kausta.

## 10.3 Grafana API kasutamine automatiseerimiseks

Grafana API võimaldab automatiseerida mitmeid toiminguid, nagu töölaudade loomine, kasutajate haldamine jne.

Näide: Töölauda loomine API kaudu:

```bash
curl -X POST -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  http://your-grafana-instance/api/dashboards/db \
  -d @dashboard.json
```

Kus `dashboard.json` on fail järgmise sisuga:

```json
{
  "dashboard": {
    "id": null,
    "title": "API Loodud Töölaud",
    "tags": [ "automated" ],
    "timezone": "browser",
    "panels": [
      {
        "type": "graph",
        "title": "API Loodud Paneel",
        "gridPos": { "x": 0, "y": 0, "w": 12, "h": 8 }
      }
    ]
  },
  "folderId": 0,
  "overwrite": false
}
```

# 11. Grafana ja DevOps praktikad

## 11.1 Grafana integreerimine CI/CD protsessidega

Grafana saab efektiivselt integreerida pideva integratsiooni ja pideva tarnimise (CI/CD) protsessidesse, võimaldades automaatset monitoorimise ja visualiseerimise haldust.

Näide: Grafana töölaudade automaatne uuendamine GitLab CI/CD abil:

`.gitlab-ci.yml` fail:
```yaml
stages:
  - deploy

deploy_dashboards:
  stage: deploy
  image: curlimages/curl:latest
  script:
    - |
      for file in dashboards/*.json; do
        curl -X POST -H "Content-Type: application/json" \
             -H "Authorization: Bearer ${GRAFANA_API_KEY}" \
             http://your-grafana-instance/api/dashboards/db \
             -d @$file
      done
  only:
    - main
```

## 11.2 Infrastruktuuri kui koodi (IaC) põhimõtted Grafanaga

Infrastruktuuri kui koodi põhimõtete rakendamine Grafana seadistamisel võimaldab versioonihaldust ja reprodutseeritavust.

Näide: Grafana seadistamine Terraformi abil:

```hcl
resource "grafana_dashboard" "metrics" {
  config_json = file("dashboards/metrics.json")
}

resource "grafana_data_source" "prometheus" {
  type = "prometheus"
  name = "Prometheus"
  url  = "http://prometheus:9090"
}

resource "grafana_alert_notification" "slack" {
  name = "Slack"
  type = "slack"
  settings = jsonencode({
    url = var.slack_webhook_url
  })
}
```

## 11.3 Grafana kasutamine mikroteenuste arhitektuuris

Mikroteenuste arhitektuuris on Grafana võtmetähtsusega tööriist teenuste jõudluse ja tervise jälgimiseks.

Näide: Mikroteenuste jälgimise töölaud:

1. Looge töölaud iga mikroteenuse jaoks
2. Kasutage muutujaid teenuste valimiseks
3. Lisage järgmised paneelid:
   - Teenuse vastamisaeg
   - Päringute arv sekundis
   - Vigade määr
   - Ressursside kasutus (CPU, mälu)

Näide päringu mikroteenuse vastamisaja jälgimiseks:
```promql
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket{service="$service"}[5m])) by (le))
```

# 12. Juhtumiuuringud ja parimad praktikad

## 12.1 Reaalsed näited Grafana kasutamisest ettevõtetes

### Juhtum 1: E-kaubanduse platvorm

Ettevõte: MegaShop (hüpoteetiline nimi)
Väljakutse: Veebilehe jõudluse ja kasutajakogemuse jälgimine

Lahendus:
1. Loodud töölauad:
   - Üldine jõudlus (veebilehe laadimisaeg, serveri vastamisaeg)
   - Kasutajate käitumine (ostude arv, ostukorvi hülgamise määr)
   - Serveri ressursid (CPU, mälu, võrgu läbilaskevõime)

2. Seadistatud hoiatused:
   - Veebilehe laadimisaeg > 3 sekundit
   - Serveri CPU kasutus > 80%
   - Ostude arv < tavapärane 30%

Tulemus: 20% paranemine veebilehe jõudluses ja 15% kasv müügis

### Juhtum 2: SaaS pakkuja

Ettevõte: CloudSoft (hüpoteetiline nimi)
Väljakutse: Mikroteenuste jälgimine ja probleemide kiire tuvastamine

Lahendus:
1. Loodud hierarhiline töölaudade struktuur:
   - Üldvaade kõigist teenustest
   - Detailsed töölauad iga mikroteenuse jaoks

2. Implementeeritud RED meetod (Rate, Errors, Duration):
   - Päringute arv sekundis
   - Vigade määr
   - Päringu kestus

3. Kasutatud hajusjälgimine (distributed tracing) Jaeger integratsiooniga

Tulemus: Keskmine probleemide lahendamise aeg vähenes 60%

## 12.2 Levinud probleemid ja nende lahendused

### Probleem 1: Liiga palju andmeid / aeglased päringud

Lahendus:
- Kasutage agregeerimisfunktsioone (`avg`, `sum`, `rate`)
- Optimeerige ajavahemikke
- Rakendage andmete eelarvutamist (pre-computation)

Näide optimeeritud päringu:
```promql
avg_over_time(rate(http_requests_total[5m])[1h:5m])
```

### Probleem 2: Informatsiooni üleküllus töölaudadel

Lahendus:
- Järgige hierarhilist struktuuri (üldvaade -> detailid)
- Kasutage muutujaid filtreerimiseks
- Rakendage järjepidevat värvikoodide süsteemi

### Probleem 3: Ebatõhusad hoiatused

Lahendus:
- Seadistage mitmetasemelised hoiatused (hoiatus -> kriitiline)
- Kasutage "for" klauslit lühiajaliste kõikumiste vältimiseks
- Grupeerige seotud hoiatused

Näide tõhusast hoiatusest:
```yaml
- alert: HighCPUUsage
  expr: avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) by (instance) < 0.2
  for: 15m
  labels:
    severity: warning
  annotations:
    summary: "Kõrge CPU kasutus"
    description: "CPU kasutus on olnud üle 80% viimased 15 minutit serveril {{ $labels.instance }}"
```

## 12.3 Tulevikutrendid ja Grafana arengusuunad

1. Masinõppe integratsioon:
   - Anomaaliate automaatne tuvastamine
   - Ennustav analüütika

2. Laiendatud jälgimise võimalused:
   - Sügavam integratsioon logide ja jälgimisega (Loki, Tempo)
   - Täiustatud hajusjälgimine

3. Täiustatud visualiseeringud:
   - Interaktiivsed 3D visualiseeringud
   - Laiendatud reaalsuse (AR) integratsioon

4. Suurem automatiseerimine:
   - Automaatne töölauda genereerimine
   - Intelligentsed hoiatused ja soovitused

5. Pilvepõhised ja servervabad lahendused:
   - Grafana Cloud laiendamine
   - Serverless Grafana instantsid

# 13. Lisad

## A. Grafana käsurea tööriistad

Grafana pakub mitmeid käsurea tööriistu, mis aitavad administraatoritel ja arendajatel Grafanat hallata ja laiendada.

### grafana-cli

`grafana-cli` on peamine tööriist Grafana haldamiseks käsureal.

Põhilised käsud:

1. Pluginate haldamine:
   ```bash
   grafana-cli plugins list
   grafana-cli plugins install <plugin-id>
   grafana-cli plugins update-all
   ```

2. Administreerimine:
   ```bash
   grafana-cli admin reset-admin-password <new-password>
   grafana-cli db migrate
   ```

3. Serveri haldamine:
   ```bash
   grafana-cli server start
   grafana-cli server restart
   ```

### grafana-server

`grafana-server` on Grafana põhirakendus. Selle käivitamiseks kasutatakse tavaliselt süsteemi teenuste haldamise tööriistu (nt. systemd), kuid seda saab käivitada ka otse:

```bash
grafana-server --config=/path/to/grafana.ini
```

## B. Kasulikud ressursid ja kogukonna tugi

1. Ametlik dokumentatsioon: [Grafana Docs](https://grafana.com/docs/)
2. Grafana Labs blogi: [Grafana Blog](https://grafana.com/blog/)
3. GitHub repositoorium: [Grafana GitHub](https://github.com/grafana/grafana)
4. Kogukonna foorum: [Grafana Community](https://community.grafana.com/)
5. Grafana konverentsid ja üritused: [Grafana Events](https://grafana.com/about/events/)

## C. Sõnastik

- **Dashboard**: Töölaud, mis koondab mitmeid visualiseeringuid ja paneele.
- **Panel**: Üksik visualiseering töölaual.
- **Data Source**: Andmeallikas, millest Grafana pärib andmeid.
- **Query**: Päring, mida kasutatakse andmete pärimiseks andmeallikast.
- **Alert**: Hoiatus, mis käivitub teatud tingimuste täitumisel.
- **Annotation**: Märge graafikul, mis tähistab olulist sündmust.
- **Template Variable**: Muutuja, mida saab kasutada dünaamilise sisu loomiseks töölaudadel.
- **Plugin**: Laiendus, mis lisab Grafanale uusi funktsioone või andmeallikaid.
- **Time Series**: Aegrida, andmete jada, mis on indekseeritud aja järgi.
- **Gauge**: Mõõdik, visualiseerimistüüp, mis näitab üksikut väärtust skaalal.
- **Heatmap**: Soojuskaart, visualiseering, mis näitab andmete tihedust värvide abil.
- **Provisioning**: Protsess, mille käigus seadistatakse Grafana ressursse automaatselt.
