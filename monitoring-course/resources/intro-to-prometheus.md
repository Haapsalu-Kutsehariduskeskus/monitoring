# Prometheus: Theory and Architecture

## Sisukord

1. [Introduction to Prometheus](#introduction-to-prometheus)

2. [Core Concepts](#core-concepts)
   - 2.1 [Time Series Data Model](#time-series-data-model)
   - 2.2 [Metric Types](#metric-types)
   - 2.3 [Labels and Dimensions](#labels-and-dimensions)

3. [Architecture Overview](#architecture-overview)
   - 3.1 [High-level Architecture](#high-level-architecture)
   - 3.2 [Main Components](#main-components)
   - 3.3 [Data Flow](#data-flow)

4. [Data Collection](#data-collection)
   - 4.1 [Pull Model vs. Push Model](#pull-model-vs-push-model)
   - 4.2 [Exporters](#exporters)
   - 4.3 [Service Discovery](#service-discovery)

5. [PromQL (Prometheus Query Language)](#promql-prometheus-query-language)
   - 5.1 [Basic Query Syntax](#basic-query-syntax)
   - 5.2 [Common Operators and Functions](#common-operators-and-functions)
   - 5.3 [Example Queries](#example-queries)

6. [Storage](#storage)
   - 6.1 [Local Storage](#local-storage)
   - 6.2 [Long-term Storage Options](#long-term-storage-options)
   - 6.3 [Retention and Compaction](#retention-and-compaction)

7. [Alerting](#alerting)
   - 7.1 [Alertmanager Overview](#alertmanager-overview)
   - 7.2 [Alert Rules Configuration](#alert-rules-configuration)
   - 7.3 [Notification Integrations](#notification-integrations)

8. [Visualization](#visualization)
   - 8.1 [Built-in Expression Browser](#built-in-expression-browser)
   - 8.2 [Integration with Grafana](#integration-with-grafana)
   - 8.3 [Example Dashboards](#example-dashboards)

9. [Best Practices](#best-practices)
   - 9.1 [Naming Conventions](#naming-conventions)
   - 9.2 [Cardinality Considerations](#cardinality-considerations)
   - 9.3 [Performance Optimization Tips](#performance-optimization-tips)

10. [Prometheus Ecosystem](#prometheus-ecosystem)
    - 10.1 [Related Projects](#related-projects)
    - 10.2 [Community and Resources](#community-and-resources)

11. [Comparison with Other Monitoring Solutions](#comparison-with-other-monitoring-solutions)
    - 11.1 [Prometheus vs. Other Tools](#prometheus-vs-other-tools)
    - 11.2 [Use Cases Where Prometheus Excels](#use-cases-where-prometheus-excels)

12. [Conclusion](#conclusion)
    - 12.1 [Key Strengths of Prometheus](#key-strengths-of-prometheus)
    - 12.2 [Prometheus's Role in Modern Observability](#prometheuss-role-in-modern-observability)
    - 12.3 [Future Perspectives](#future-perspectives)
    - 12.4 [Final Thoughts](#final-thoughts)

## 1. Introduction to Prometheus

Prometheus on avatud lähtekoodiga süsteemide jälgimise ja hoiatuste tööriistakomplekt, mis on viimastel aastatel kiiresti populaarsust kogunud. See loodi algselt SoundCloudis 2012. aastal, et lahendada nende kasvava mikroteenuste arhitektuuri jälgimise väljakutseid. Sellest ajast alates on Prometheus arenenud iseseisvaks avatud lähtekoodiga projektiks, mida kasutavad paljud organisatsioonid üle maailma.

Prometheuse peamised omadused, mis eristavad seda teistest jälgimislahendustest, on:

1. **Mitmemõõtmeline andmemudel**: Prometheus salvestab kõik andmed ajaseeriatena, mis on identifitseeritud meetrika nime ja võtme-väärtuse paaridena tuntud siltide abil. See võimaldab väga paindlikku ja võimsat päringute tegemist.

2. **Paindlik päringukeele (PromQL)**: PromQL võimaldab kasutajatel agregeerida ja manipuleerida andmeid keerukatel viisidel, mis teeb sellest võimsa tööriista probleemide tuvastamiseks ja analüüsimiseks.

3. **Autonoomne arhitektuur**: Prometheus ei sõltu hajutatud salvestusest, mis muudab selle paigaldamise ja käitamise lihtsamaks.

4. **Tõmbemudel (pull model) andmete kogumisel**: Prometheus kogub meetrikaid, "tõmmates" neid HTTP kaudu, mis lihtsustab jälgitavate süsteemide seadistamist ja avastamist.

5. **Tõukemudel (push model) toetus**: Kuigi Prometheus kasutab peamiselt tõmbemudelit, toetab see ka andmete tõukamist läbi vahendaja (Pushgateway), mis on kasulik teatud stsenaariumide puhul.

6. **Teenuste avastamine**: Prometheus suudab dünaamiliselt avastada jälgitavaid sihtmärke, mis on eriti kasulik pilvekeskkondades ja konteineripõhistes arhitektuurides.

7. **Visualiseerimine ja dashboardid**: Kuigi Prometheus sisaldab lihtsat graafilist liidest, integreeritakse seda sageli teiste visualiseerimistööriistadega nagu Grafana.

Nende omaduste kombinatsioon teeb Prometheusest võimsa tööriista kaasaegsete, dünaamiliste ja keerukate IT-infrastruktuuride jälgimiseks.

## 2. Core Concepts

Prometheuse mõistmiseks on oluline tunda selle põhimõisteid. Vaatame neid lähemalt.

### 2.1 Time Series Data Model

Prometheuse südames on ajaseeriate andmemudel. See on fundamentaalne kontseptsioon, mis määrab, kuidas Prometheus andmeid kogub, salvestab ja pärib.

- **Ajaseeria definitsioon**: Ajaseeria on andmepunktide jada, kus igal punktil on ajatempel ja väärtus. Näiteks võiks CPU kasutuse ajaseeria näha välja nii:

  ```
  1600000000 50.0
  1600000060 52.3
  1600000120 48.7
  ```

- **Meetrika nimi ja sildid**: Iga ajaseeria on unikaalselt identifitseeritud oma meetrika nime ja siltide (labels) kombinatsiooniga. Näiteks:

  ```
  cpu_usage_percent{host="server01",cpu="0"} 50.0
  ```

  Siin on `cpu_usage_percent` meetrika nimi ja `host="server01"` ning `cpu="0"` on sildid.

Selle mudeli võimsus seisneb selle paindlikkuses. Näiteks saame kergesti pärida kõigi serverite keskmist CPU kasutust või võrrelda erinevate CPUde kasutust samal serveril.

### 2.2 Metric Types

Prometheus defineerib neli põhilist meetrika tüüpi, mis vastavad erinevatele andmete kogumise ja kasutamise stsenaariumidele.

1. **Counter**
   - Loendur on kumulatiivne meetrika, mis saab ainult suureneda või nullile lähtestuda.
   - Näide: `http_requests_total` - HTTP päringute koguarv
   - Kasutus: Loendureid kasutatakse sündmuste koguarvu jälgimiseks või sündmuste toimumise kiiruse arvutamiseks.

2. **Gauge**
   - Mõõdik on meetrika, mis võib suvaliselt tõusta ja langeda.
   - Näide: `cpu_usage_percent` - hetke CPU kasutus protsentides
   - Kasutus: Mõõdikuid kasutatakse hetkeseisude või väärtuste jälgimiseks, mis võivad kõikuda.

3. **Histogram**
   - Histogramm proovib vaatlusi ja loendab neid konfigureeritavatesse ämbritesse (buckets).
   - Näide: `http_request_duration_seconds` - HTTP päringute kestus sekundites
   - Kasutus: Histogramme kasutatakse, kui on vaja arvutada kvantiile või keskmisi väärtusi ajavahemike lõikes.

4. **Summary**
   - Sarnane histogrammile, kuid pakub konfigureeritavaid kvantiile.
   - Näide: `http_request_duration_seconds` koos eelarvutatud kvantiididega
   - Kasutus: Kasutatakse, kui on vaja spetsiifilisi kvantiile, mida arvutab klient.

Nende meetrika tüüpide mõistmine on oluline, et valida õige meetrika tüüp oma rakenduse instrumenteerimiseks ja tõlgendada Prometheuse andmeid õigesti.

### 2.3 Labels and Dimensions

Sildid on Prometheuse mitmemõõtmelise andmemudeli võtmeelement.

- **Siltide struktuur**: Sildid on võtme-väärtuse paarid, mis lisatakse meetrika nimele.
- **Paindlikkus**: Sildid võimaldavad ühe meetrika jaoks salvestada mitmeid seotud ajaseeriaid.
- **Võimas pärimine**: Siltide abil saab teha keerukaid päringuid ja agregatsioone.

Näide:

```
http_requests_total{method="GET", endpoint="/api/users", status="200"} 1234
http_requests_total{method="POST", endpoint="/api/users", status="201"} 56
http_requests_total{method="GET", endpoint="/api/products", status="200"} 789
```

Selle struktuuri abil saame kergesti pärida:

- Kõiki GET päringuid: `http_requests_total{method="GET"}`
- Kõiki edukaid päringuid: `http_requests_total{status=~"2.."}`
- Konkreetse lõpp-punkti päringuid: `http_requests_total{endpoint="/api/users"}`

Sildid võimaldavad meil koguda detailseid andmeid ilma eraldi meetrikaid defineerimata iga võimaliku kombinatsiooni jaoks.

## 3. Architecture Overview

Prometheuse arhitektuuri mõistmine on oluline selle efektiivseks kasutamiseks ja skaleerimiseks. Vaatame lähemalt, kuidas erinevad komponendid omavahel suhtlevad ja milline on andmevoog süsteemis.

### 3.1 High-level Architecture

[Siia tuleks lisada diagramm, mis näitab Prometheuse põhikomponente ja nende omavahelisi seoseid]

Prometheuse arhitektuur koosneb mitmest põhikomponendist, mis töötavad koos, et pakkuda terviklikku jälgimislahendust.

### 3.2 Main Components

1. **Prometheus Server**
   - See on Prometheuse süsteemi tuum.
   - Funktsioonid:
     - Andmete kogumine (scraping)
     - Andmete salvestamine
     - PromQL päringute töötlemine
     - Hoiatuste genereerimine

2. **Client Libraries**
   - Kasutatakse rakenduste instrumenteerimiseks.
   - Saadaval paljudes programmeerimiskeeltes (nt Java, Go, Python).
   - Võimaldavad arendajatel lisada oma rakendustesse kohandatud meetrikaid.

3. **Push Gateway**
   - Võimaldab lühiajaliste tööde meetrikate kogumist.
   - Kasulik stsenaariumides, kus otsene andmete kogumine (scraping) pole võimalik.

4. **Exporters**
   - Koguvad meetrikaid süsteemidest, mida ei saa otseselt instrumenteerida.
   - Näited:
     - Node Exporter (Linux/Unix süsteemi meetrikad)
     - MySQL Exporter (MySQL andmebaasi meetrikad)

5. **Alertmanager**
   - Käsitleb Prometheus serverist tulevaid hoiatusi.
   - Funktsioonid:
     - Hoiatuste grupeerimine
     - Hoiatuste suunamine õigetele vastuvõtjatele
     - Vaigistamine ja inhibeerimine

6. **Data Visualization Tools**
   - Prometheus sisaldab lihtsat väljenduste sirvijat (expression browser).
   - Sageli kasutatakse koos Grafanaga põhjalikumate visualiseeringute jaoks.

### 3.3 Data Flow

Prometheuse andmevoog on disainitud lihtsuse ja efektiivsuse tagamiseks:

1. **Andmete kogumine**: Prometheus server kogub (scrape) regulaarselt meetrikaid instrumenteeritud rakendustelt, eksportijatelt ja Push Gateway'lt.

2. **Andmete salvestamine**: Kogutud näidised salvestatakse lokaalselt Prometheus serveris ajaseriatena.

3. **Reeglite rakendamine**: Prometheus rakendab konfigureeritud reegleid, et agregeerida andmeid ja genereerida uusi ajaseeriaid või hoiatusi.

4. **Hoiatuste käsitlemine**: Genereeritud hoiatused saadetakse Alertmanager'ile, mis hoolitseb nende edastamise eest.

5. **Andmete kuvamine**: Andmeid saab pärida ja visualiseerida kas Prometheuse enda väljenduste sirvija kaudu või väliste tööriistade nagu Grafana abil.

See arhitektuur võimaldab Prometheus'el olla nii võimas kui ka paindlik, kohanedes erinevate jälgimisstsenaariumidega alates väikestest rakendustest kuni suurte, hajutatud süsteemideni.

## 4. Data Collection

Andmete kogumine on Prometheuse üks põhifunktsioone. Mõistmine, kuidas Prometheus andmeid kogub, on oluline selle efektiivseks kasutamiseks ja seadistamiseks.

### 4.1 Pull Model vs. Push Model

Prometheus kasutab peamiselt tõmbemudelit (pull model) andmete kogumiseks, mis eristab seda paljudest teistest jälgimissüsteemidest.

**Tõmbemudel (Pull Model)**:

- Prometheus "tõmbab" (scrape) meetrikaid regulaarselt sihtmärkidelt.
- Eelised:
  - Lihtsam seadistada ja hallata
  - Prometheus saab kontrollida andmete kogumise sagedust
  - Kergem tuvastada, kui sihtmärk on maas
- Näide konfiguratsioonist:

  ```yaml
  scrape_configs:
    - job_name: 'my_app'
      scrape_interval: 15s
      static_configs:
        - targets: ['localhost:8080']
  ```

**Tõukemudel (Push Model)**:

- Toetatud läbi Pushgateway komponendi
- Kasulik stsenaariumid:
  - Lühiajalised tööd (nt. cron jobs)
  - Süsteemid, mis on tulemüüri taga või muul põhjusel otse ligipääsmatud
- Näide Pushgateway kasutamisest:

  ```python
  from prometheus_client import CollectorRegistry, Gauge, push_to_gateway

  registry = CollectorRegistry()
  g = Gauge('job_last_success_unixtime', 'Last time a batch job successfully finished', registry=registry)
  g.set_to_current_time()
  push_to_gateway('localhost:9091', job='batch_job', registry=registry)
  ```

### 4.2 Exporters

Eksportijad on olulised tööriistad Prometheuse ökosüsteemis, võimaldades jälgida süsteeme ja rakendusi, mida ei saa otseselt instrumenteerida.

- **Definitsioon**: Eksportija on programm, mis kogub meetrikaid süsteemist või rakendusest ja esitab need Prometheuse formaadis.

- **Tüübid**:
  1. Otsesed eksportijad (nt. node_exporter)
  2. Iseseisvad eksportijad (nt. MySQL eksportija)

- **Populaarsed eksportijad**:
  - Node Exporter: Kogub süsteemi meetrikaid (CPU, mälu, ketta kasutus jne)
  - Blackbox Exporter: Võimaldab teha väliseid kontrollimisi (nt. HTTP endpoint'ide kontrollimine)
  - MySQL Exporter: Kogub MySQL andmebaasi meetrikaid
  - Elasticsearch Exporter: Jälgib Elasticsearch klastri tervist ja jõudlust

- **Eksportija näide** (lihtsustatud Node Exporter väljund):

  ```
  # HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
  # TYPE node_cpu_seconds_total counter
  node_cpu_seconds_total{cpu="0",mode="idle"} 3.8921744e+06
  node_cpu_seconds_total{cpu="0",mode="user"} 1.2903e+05
  node_cpu_seconds_total{cpu="0",mode="system"} 2.0252e+04

  # HELP node_memory_MemTotal_bytes Memory information field MemTotal_bytes.
  # TYPE node_memory_MemTotal_bytes gauge
  node_memory_MemTotal_bytes 1.6777216e+10
  ```

Eksportijate kasutamine võimaldab Prometheus'el koguda meetrikaid paljudest erinevatest allikatest, laiendades oluliselt selle jälgimisvõimekust.

### 4.3 Service Discovery

Teenuste avastamine on oluline funktsioon dünaamilistes keskkondades, kus sihtmärgid võivad sageli muutuda.

- **Staatiline konfiguratsioon**:
  - Lihtne, kuid vähem paindlik
  - Näide:

    ```yaml
    scrape_configs:
      - job_name: 'static_nodes'
        static_configs:
          - targets: ['server1:9100', 'server2:9100', 'server3:9100']
    ```

- **Dünaamiline teenuste avastamine**:
  - Toetatud platvormid:
    - Kubernetes
    - Consul
    - AWS EC2
    - Azure
    - ja paljud teised
  - Näide Kubernetes'e teenuste avastamisest:

    ```yaml
    scrape_configs:
      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
          - role: pod
        relabel_configs:
          - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
            action: keep
            regex: true
    ```

- **Eelised**:
  - Automaatne kohanemine muutuvate keskkondadega
  - Vähendab käsitsi konfigureerimise vajadust dünaamilistes infrastruktuurides
  - Võimaldab jälgimissüsteemil kasvada ja kahaneda koos infrastruktuuriga

Teenuste avastamine on eriti kasulik pilvekeskkondades ja konteineripõhistes arhitektuurides, kus teenused võivad dünaamiliselt tekkida ja kaduda.

## 5. PromQL (Prometheus Query Language)

PromQL on Prometheus'e võimas päringukeele, mis võimaldab kasutajatel andmeid pärida, agregeerida ja analüüsida. See on üks Prometheus'e peamisi tugevusi võrreldes teiste jälgimissüsteemidega.

### 5.1 Basic Query Syntax

PromQL päringud on disainitud olema intuitiivsed ja võimsad.

- **Meetrika nime valimine**:
  - Lihtsaim päring on meetrika nime kirjutamine
  - Näide: `http_requests_total`

- **Siltide sobitamine**:
  - Kasuta loogelisi sulge `{}` siltide määramiseks
  - Näide: `http_requests_total{status="200", method="GET"}`

- **Ajavahemiku valimine**:
  - Kasuta nurksulge `[]` ajavahemiku määramiseks
  - Näide: `http_requests_total[5m]` - viimase 5 minuti andmed

### 5.2 Common Operators and Functions

PromQL pakub mitmeid operaatoreid ja funktsioone andmete manipuleerimiseks.

- **Aritmeetilised operaatorid**: `+`, `-`, `*`, `/`, `%`, `^`
  - Näide: `(node_memory_MemTotal_bytes - node_memory_MemFree_bytes) / node_memory_MemTotal_bytes`

- **Võrdlusoperaatorid**: `==`, `!=`, `>`, `<`, `>=`, `<=`
  - Näide: `http_requests_total > 100`

- **Loogikaoperaatorid**: `and`, `or`, `unless`
  - Näide: `http_requests_total{status="500"} or http_requests_total{status="503"}`

- **Agregeerimisoperaatorid**: `sum`, `avg`, `min`, `max`, `count`
  - Näide: `sum(http_requests_total) by (status)`

- **Funktsioonid**:
  - `rate()`: Arvutab keskmise muutuse sekundis
  - `increase()`: Arvutab väärtuse suurenemise
  - `histogram_quantile()`: Arvutab histogrammi kvantiili
  - Näide: `rate(http_requests_total[5m])`

### 5.3 Example Queries

Vaatame mõnda praktilist näidet, mis demonstreerivad PromQL'i võimsust:

1. HTTP päringute kiirus viimase 5 minuti jooksul:

   ```
   rate(http_requests_total[5m])
   ```

2. 95. protsentiil päringu kestusest:

   ```
   histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
   ```

3. Top 5 CPU-d kasutavat protsessi:

   ```
   topk(5, sum by (process) (rate(process_cpu_seconds_total[5m])))
   ```

4. Vaba mälu protsent:

   ```
   (node_memory_MemFree_bytes / node_memory_MemTotal_bytes) * 100
   ```

5. Keskmine päringu kiirus minuti kohta, grupeeritud staatuse koodi järgi:

   ```
   sum(rate(http_requests_total[5m])) by (status) * 60
   ```

PromQL'i oskuslik kasutamine võimaldab teil saada väärtuslikke teadmisi oma süsteemide toimimisest ja tuvastada probleeme enne, kui need muutuvad kriitiliseks.

## 6. Storage

Prometheus'e salvestussüsteem on optimeeritud ajaseeria andmete jaoks, pakkudes efektiivset salvestust ja kiireid päringuid.

### 6.1 Local Storage

Prometheus kasutab kohalikku salvestust, mis põhineb kohandatud ajaseeria andmebaasil (TSDB).

- **Salvestusformaat**:
  - Andmed on grupeeritud plokkidesse (tavaliselt 2 tunni andmed)
  - Iga plokk sisaldab:
    - Indeksi faili kiireks otsinguks
    - Ühte või mitut segment faili, mis sisaldavad tegelikke ajaseeria andmeid

- **Efektiivsus**:
  - Optimeeritud kirjutamise ja lugemise jõudlusele
  - Kasutab Delta-of-Delta kodeerimist andmete tihendamiseks

- **Mälu ja ketta kasutus**:
  - Aktiivne andmeplokk hoitakse mälus kiireks kirjutamiseks
  - Vanemad plokid hoitakse kettal

### 6.2 Long-term Storage Options

Kuigi Prometheus on disainitud lühiajaliseks salvestuseks, on mitmeid võimalusi pikaajaliste andmete säilitamiseks.

- **Remote Write/Read API**:
  - Võimaldab Prometheus'el saata ja lugeda andmeid välisest salvestussüsteemist
  - Konfigureerimise näide:

    ```yaml
    remote_write:
      - url: "http://remote-storage-server:9201/write"
    remote_read:
      - url: "http://remote-storage-server:9201/read"
    ```

- **Integratsioon teiste süsteemidega**:
  - Thanos: Võimaldab luua kõrgkäideldava Prometheus'e seadistuse pikaajalise salvestusega
  - Cortex: Horisontaalselt skaleeruv, kõrgkäideldav Prometheus teenusena
  - M3DB: Hajutatud ajaseeria andmebaas ja agregaatorisüsteem

### 6.3 Retention and Compaction

Andmete säilitamine ja tihendamine on olulised Prometheus'e jõudluse ja ressursikasutuse haldamisel.

- **Vaikimisi säilitusaeg**: 15 päeva (konfigureeritav)
  - Muutmiseks kasuta `--storage.tsdb.retention.time` lippu

- **Tihendamisprotsess**:
  - Ühendab väiksemad andmeplokid suuremateks
  - Vähendab kettakasutust ja parandab päringu jõudlust
  - Toimub automaatselt taustal

- **Näide konfiguratsioonist**:

  ```yaml
  storage:
    tsdb:
      retention:
        time: 30d
        size: 50GB
  ```

Prometheus'e salvestussüsteem on disainitud olema võimalikult lihtne ja efektiivne, pakkudes samas paindlikkust pikaajaliste andmete säilitamiseks väliste süsteemide abil.

## 7. Alerting

Hoiatuste genereerimine ja haldamine on üks Prometheus'e põhifunktsioone, võimaldades kasutajatel kiiresti reageerida probleemidele oma süsteemides.

### 7.1 Alertmanager Overview

Alertmanager on Prometheus'e ökosüsteemi osa, mis vastutab hoiatuste käsitlemise eest.

- **Põhifunktsioonid**:
  - Hoiatuste vastuvõtmine Prometheus serverilt
  - Hoiatuste deduplitseerimine
  - Grupeerimine
  - Marsruutimine õigetele vastuvõtjatele
  - Vaigistamine ja inhibeerimine

- **Arhitektuur**:
  - Töötab eraldi teenusena Prometheus serverist
  - Võib olla konfigureeritud kõrgkäideldavasse klastri konfiguratsiooni

### 7.2 Alert Rules Configuration

Hoiatuste reeglid defineeritakse Prometheus'e konfiguratsioonis.

- **Reeglite struktuur**:

  ```yaml
  groups:
    - name: example
      rules:
      - alert: HighRequestLatency
        expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
        for: 10m
        labels:
          severity: page
        annotations:
          summary: High request latency
  ```

- **Komponendid**:
  - `alert`: Hoiatuse nimi
  - `expr`: PromQL väljend, mis määratleb hoiatuse tingimuse
  - `for`: Aeg, kui kaua tingimus peab olema tõene enne hoiatuse käivitamist
  - `labels`: Võimaldavad hoiatusi kategoriseerida ja marsruutida
  - `annotations`: Lisainfo hoiatuse kohta, tavaliselt inimloetav

### 7.3 Notification Integrations

Alertmanager saab saata teavitusi mitmesse erinevasse sihtkohta.

- **Populaarsed integratsioonid**:
  - Email
  - Slack
  - PagerDuty
  - OpsGenie
  - Webhook (võimaldab integratsiooni kohandatud süsteemidega)

- **Näide konfiguratsioonist** (Slack integratsioon):

  ```yaml
  receivers:
  - name: 'slack-notifications'
    slack_configs:
    - api_url: 'https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX'
      channel: '#monitoring-alerts'
      send_resolved: true
  ```

- **Marsruutimise näide**:

  ```yaml
  route:
    receiver: 'slack-notifications'
    group_by: [alertname, datacenter, app]
    group_wait: 30s
    group_interval: 5m
    repeat_interval: 4h
  ```

Efektiivne hoiatuste seadistamine on kriitilise tähtsusega proaktiivse süsteemihalduse jaoks, võimaldades kiiresti tuvastada ja lahendada probleeme enne, kui need muutuvad kriitiliseks.

## 8. Visualization

Kuigi Prometheus pakub lihtsat sisseehitatud graafilist liidest, kasutatakse sageli väliseid visualiseerimisvahendeid põhjalikumate dashboardide loomiseks.

### 8.1 Built-in Expression Browser

Prometheus'e sisseehitatud väljenduste sirvija on kasulik kiireks andmete uurimiseks ja päringute testimiseks.

- **Võimalused**:
  - PromQL päringute käivitamine
  - Tulemuste kuvamine graafikuna või tabelina
  - Ajavahemiku valimine
  - Lihtne graafiku formateerimine

- **Kasutamine**:
  - Juurdepääs tavaliselt aadressil `http://<prometheus-server>:9090/graph`
  - Sobib hästi kiireks probleemide diagnoosimiseks ja päringute koostamiseks

### 8.2 Integration with Grafana

Grafana on de facto standard Prometheus'e andmete visualiseerimiseks, pakkudes rikkalikku graafikute ja dashboardide loomise funktsionaalsust.

- **Peamised eelised**:
  - Rikkalik graafikute valik
  - Dünaamilised ja interaktiivsed dashboardid
  - Mallid ja muutujad
  - Kasutajate autentimine ja autoriseerimine
  - Mitmete andmeallikate toetus (lisaks Prometheus'ele)

- **Prometheus'e andmeallika lisamine Grafanas**:
  1. Mine "Configuration" > "Data Sources"
  2. Vali "Add data source"
  3. Vali "Prometheus"
  4. Sisesta Prometheus'e serveri URL
  5. Salvesta ja testi ühendust

- **Graafiku loomine**:
  4. Vali graafiku tüüp (nt. joondiagramm, tulpdiagramm)
  5. Kohanda graafiku välimust (värve, telgi, legendisid)
  6. Lisa pealkiri ja kirjeldus

- **Dashboardide loomine**:
  - Kombineeri mitu graafikut ühele dashboardile
  - Kasuta ridu ja veerge paigutuse organiseerimiseks
  - Lisa muutujaid dünaamilise sisu jaoks (nt. serveri valimiseks)

- **Grafana mallid**:
  - Võimaldavad luua taaskasutatavaid dashboarde
  - Saab jagada Grafana kogukonnaga
  - Näide: Node Exporter malli importimine annab kohe ülevaatliku serveri jälgimise dashboardi

### 8.3 Example Dashboards

Vaatame mõnda praktilist näidet Prometheus + Grafana dashboardidest:

1. **Süsteemi ülevaate dashboard**:
   - CPU, mälu, ketta ja võrgu kasutus
   - Süsteemi koormus ja protsesside arv
   - Uptime ja põhilised süsteemi meetrikad

2. **Rakenduse jõudluse dashboard**:
   - Päringu latentsus (keskmised ja protsentiilid)
   - Päringute arv sekundis
   - Veamäär
   - Aktiivse kasutajate arv

3. **Veamäära ja latentsuse dashboard**:
   - Veamäär erinevate teenuste lõikes
   - Latentsus erinevate operatsioonide lõikes
   - Aeglaste päringute loend
   - Veateadete loend koos esinemissagedusega

Näide Grafana dashboardi konfiguratsioonist (JSON väljavõte):

```json
{
  "panels": [
    {
      "title": "HTTP Request Rate",
      "type": "graph",
      "datasource": "Prometheus",
      "targets": [
        {
          "expr": "sum(rate(http_requests_total[5m])) by (status)",
          "legendFormat": "{{status}}"
        }
      ]
    },
    {
      "title": "CPU Usage",
      "type": "gauge",
      "datasource": "Prometheus",
      "targets": [
        {
          "expr": "100 - (avg by(instance) (rate(node_cpu_seconds_total{mode=\"idle\"}[5m])) * 100)"
        }
      ]
    }
  ]
}
```

Efektiivne visualiseerimine on kriitilise tähtsusega, et muuta Prometheus'e kogutud andmed kasulikuks infoks. Grafana pakub võimsat ja paindlikku platvormi selle eesmärgi saavutamiseks.

## 9. Best Practices

Prometheus'e efektiivseks kasutamiseks on oluline järgida teatud parimaid praktikaid. Need aitavad tagada, et teie jälgimissüsteem on tõhus, skaleeruv ja lihtsalt hallatav.

### 9.1 Naming Conventions

Hea nimetamise tava aitab muuta meetrikad arusaadavamaks ja lihtsamini hallatavaks.

- **Üldine formaat**: `<namespace>_<name>_<unit>`
  - Näide: `http_requests_total`, `node_cpu_utilization_percentage`

- **Nimede reeglid**:
  - Kasuta ainult tähti, numbreid ja alakriipse
  - Alusta meetrika nime tähega
  - Väldi Prometheus'e reserveeritud eesliiteid (go_, promhttp_)

- **Siltide nimetamine**:
  - Kasuta lühikesi, kirjeldavaid nimesid
  - Väldi liigseid või korduvaid silte

- **Ühikud**:
  - Lisa ühik meetrika nimesse, kui see pole ilmne
  - Kasuta baasmõõtühikuid (sekundid, baidid), mitte tuletatud ühikuid

### 9.2 Cardinality Considerations

Kõrge kardinaliteedi vältimine on oluline Prometheus'e jõudluse säilitamiseks.

- **Mis on kardinaliteet?**
  - Unikaalsete ajaseeriate arv meetrika kohta

- **Kõrge kardinaliteedi ohud**:
  - Suurendab mälukasutust
  - Aeglustab päringute töötlemist
  - Võib põhjustada Prometheus'e jõudluse probleeme

- **Kuidas vältida kõrget kardinaliteeti**:
  - Väldi lõpmatu väärtuste hulgaga silte (nt. kasutaja ID-d, e-posti aadressid)
  - Kasuta agregeerimist kõrge kardinaliteediga meetrikate jaoks
  - Piira siltide arvu meetrika kohta

- **Näide hea vs halva praktika**:
  - Halb: `http_requests_total{user_id="12345", email="user@example.com"}`
  - Hea: `http_requests_total{status="200", endpoint="/api/users"}`

### 9.3 Performance Optimization Tips

Prometheus'e jõudluse optimeerimine aitab tagada süsteemi skaleeruvuse ja töökindluse.

1. **Efektiivsed päringud**:
   - Väldi liiga keerulisi päringuid, eriti dashboardides
   - Kasuta agregeerimist, et vähendada töödeldavate andmete hulka

2. **Recording rules**:
   - Kasuta recording rules'e, et eelarvutada sageli kasutatavad päringud
   - Näide:

     ```yaml
     rules:
       - record: job:http_requests_total:sum
         expr: sum(http_requests_total) by (job)
     ```

3. **Scrape intervallide kohandamine**:
   - Kohandage scrape intervalle vastavalt vajadusele ja ressursside piirangutele
   - Näide konfiguratsioonist:

     ```yaml
     scrape_configs:
       - job_name: 'high_frequency_job'
         scrape_interval: 10s
       - job_name: 'low_frequency_job'
         scrape_interval: 1m
     ```

4. **Federatsioon suuremahuliste juurutuste jaoks**:
   - Kasutage Prometheus'e federatsiooni, et jagada koormust mitme Prometheus'e instantsi vahel
   - Näide federatsiooni konfiguratsioonist:

     ```yaml
     scrape_configs:
       - job_name: 'federate'
         scrape_interval: 15s
         honor_labels: true
         metrics_path: '/federate'
         params:
           'match[]':
             - '{job="prometheus"}'
             - '{__name__=~"job:.*"}'
         static_configs:
           - targets:
             - 'prometheus1:9090'
             - 'prometheus2:9090'
     ```

5. **Mälukasutuse optimeerimine**:
   - Kohandage TSDB ploki suurust ja säilitusaega vastavalt vajadustele
   - Näide konfiguratsioonist:

     ```yaml
     storage:
       tsdb:
         retention.time: 15d
         out_of_order_time_window: 10m
     ```

Nende parimate praktikate järgimine aitab tagada, et teie Prometheus'e juurutus on efektiivne, skaleeruv ja lihtne hallata.

## 10. Prometheus Ecosystem

Prometheus'e ümber on tekkinud lai ökosüsteem täiendavatest tööriistadest ja projektidest, mis laiendavad selle funktsionaalsust ja kasutatavust.

### 10.1 Related Projects

1. **Thanos**:
   - Eesmärk: Kõrgkäideldav Prometheus'e seadistus pikaajalise salvestusega
   - Peamised omadused:
     - Globaalne päring üle mitme Prometheus'e instantsi
     - Piiramatu andmete säilitamine objektisalvestuses (nt. S3)
     - Downsampling ja kompaktimine pikaajaliste andmete jaoks

2. **Cortex**:
   - Eesmärk: Horisontaalselt skaleeruv, kõrgkäideldav Prometheus teenusena
   - Peamised omadused:
     - Mitme rentniku tugi
     - Pikaajaline salvestus
     - Kõrge kättesaadavus

3. **Loki**:
   - Eesmärk: Logide agregeerimine, inspireeritud Prometheus'est
   - Peamised omadused:
     - Sildipõhine logi indekseerimine
     - Integratsioon Grafanaga
     - Efektiivne salvestus ja päring

4. **Alertmanager**:
   - Eesmärk: Hoiatuste haldamine ja marsruutimine
   - Peamised omadused:
     - Hoiatuste grupeerimine
     - Vaigistamine ja inhibeerimine
     - Mitmekülgsed teavituste integratsioonid

5. **Blackbox Exporter**:
   - Eesmärk: Väliste teenuste ja lõpp-punktide testimine
   - Peamised omadused:
     - HTTP, HTTPS, DNS, TCP ja ICMP kontrollid
     - TLS kontrollid
     - Vastuse sisu kontrollimine

### 10.2 Community and Resources

Prometheus'el on aktiivne ja kasvav kogukond, mis pakub palju ressursse ja tuge.

1. **Ametlik dokumentatsioon**:
   - prometheus.io/docs
   - Põhjalik juhend Prometheus'e seadistamiseks ja kasutamiseks

2. **GitHub repositoorium**:
   - github.com/prometheus/prometheus
   - Lähtekoodi haldus ja probleemide jälgimine

3. **Prometheus'e kogukonna foorum**:
   - groups.google.com/forum/#!forum/prometheus-users
   - Koht küsimuste esitamiseks ja aruteludeks

4. **PromCon konverentsid**:
   - Iga-aastane Prometheus'e keskne konverents
   - Ettekanded, töötoad ja võrgustiku loomine

5. **Prometheus'e blogi**:
   - prometheus.io/blog/
   - Uudised, uuendused ja tehnilised ülevaated

6. **Koolitused ja sertifikatsioonid**:
   - Cloud Native Computing Foundation (CNCF) pakub Prometheus'e koolitusi
   - Prometheus Certified Associate (PCA) sertifikaat

7. **Kolmanda osapoole tööriistad ja integratsioonid**:
   - Suur hulk eksportijaid erinevate süsteemide jaoks
   - Integratsioonid pilveteenuste ja orkestreerimisplatvormidega

Prometheus'e ökosüsteem jätkab laienemist, pakkudes üha rohkem võimalusi ja tööriistu moodsate, dünaamiliste infrastruktuuride jälgimiseks ja haldamiseks.

## 11. Comparison with Other Monitoring Solutions

Prometheus on üks paljudest jälgimislahendustest turul. Mõistmaks selle tugevusi ja nõrkusi, on kasulik võrrelda seda teiste populaarsete tööriistadega.

### 11.1 Prometheus vs. Other Tools

1. **Prometheus vs. Graphite**:
   - Sarnasused:
     - Mõlemad on ajaseeria andmebaasid
     - Toetavad visualiseerimist ja hoiatusi
   - Erinevused:
     - Prometheus kasutab tõmbemudelit, Graphite tõukemudelit
     - Prometheus'el on võimsam päringukeele (PromQL)
     - Prometheus'el on sisseehitatud hoiatuste süsteem

2. **Prometheus vs. InfluxDB**:
   - Sarnasused:
     - Mõlemad on optimeeritud ajaseeria andmete jaoks
     - Pakuvad kõrget kirjutamise ja lugemise jõudlust
   - Erinevused:
     - InfluxDB kasutab tõukemudelit, Prometheus tõmbemudelit
     - InfluxDB toetab SQL-sarnast päringukeelt (InfluxQL), Prometheus kasutab PromQL-i
     - InfluxDB pakub kommertsiaalset ettevõtte versiooni lisafunktsioonidega

3. **Prometheus vs. Nagios**:
   - Sarnasused:
     - Mõlemad võimaldavad süsteemide ja teenuste jälgimist
     - Toetavad hoiatuste genereerimist
   - Erinevused:
     - Nagios on rohkem keskendunud kontrollidele, Prometheus mõõdikutele
     - Prometheus pakub paremat skaleeruvust ja dünaamilist avastamist
     - Prometheus'el on võimsam päringu ja visualiseerimise võimekus

4. **Prometheus vs. ELK Stack (Elasticsearch, Logstash, Kibana)**:
   - Sarnasused:
     - Mõlemad võimaldavad andmete kogumist, salvestamist ja visualiseerimist
   - Erinevused:
     - ELK on rohkem keskendunud logide analüüsile, Prometheus mõõdikutele
     - Prometheus on lihtsam seadistada ja hallata
     - ELK pakub võimsamaid tekstiotsingu võimalusi

### 11.2 Use Cases Where Prometheus Excels

Prometheus on eriti sobiv teatud stsenaariumide jaoks:

1. **Konteineripõhised keskkonnad**:
   - Suurepärane integratsioon Kubernetes'ega
   - Dünaamiline teenuste avastamine

2. **Mikroteenuste arhitektuurid**:
   - Võime jälgida suurt hulka dünaamilisi teenuseid
   - Efektiivne mitmemõõtmeline andmemudel

3. **Pilvepõhised infrastruktuurid**:
   - Paindlik ja dünaamiline teenuste avastamine
   - Hea integratsioon paljude pilveteenuste pakkujatega

4. **DevOps ja SRE praktikad**:
   - Toetab "neli kuldset signaali" jälgimist (latentsus, liiklus, vead, küllastus)
   - Võimaldab luua detailseid SLO (Service Level Objective) mõõdikuid

5. **Kõrge skaleeruvusega süsteemid**:
   - Võime käsitleda suurt hulka mõõdikuid
   - Efektiivne salvestus ja kiired päringud

6. **Ajutised või lühiajalised töökoormsed**:
   - Pushgateway võimaldab jälgida ka lühiajalisi töid

7. **Süsteemid, mis nõuavad keerulist andmete analüüsi**:
   - PromQL võimaldab teha keerulisi päringuid ja arvutusi

8. **Multidimensionaalsed andmed**:
   - Sildipõhine mudel võimaldab paindlikku andmete organiseerimist ja päringuid

Prometheus on eriti tõhus keskkondades, kus on vaja jälgida dünaamilisi, kõrge skaleeruvusega süsteeme ja kus on vajadus keerulise andmeanalüüsi järele.

## 12. Conclusion

Prometheus on võimas ja paindlik jälgimislahendus, mis on muutunud de facto standardiks paljudes kaasaegsetes infrastruktuurides, eriti pilve- ja konteineripõhistes keskkondades.

### 12.1 Key Strengths of Prometheus

1. **Võimas andmemudel ja päringukeele**:
   - Mitmemõõtmeline andmemudel siltidega
   - PromQL võimaldab keerulisi päringuid ja analüüse

2. **Disainitud töökindluse ja skaleeruvuse jaoks**:
   - Autonoomne arhitektuur
   - Efektiivne tõmbemudel andmete kogumiseks

3. **Rikas ökosüsteem ja integratsioonid**:
   - Lai valik eksportijaid ja integratsioonivõimalusi
   - Tugev integratsioon Grafana ja teiste visualiseerimisvahendidega

4. **Dünaamiline teenuste avastamine**:
   - Sobib hästi pilvepõhiste ja konteineriseeritud keskkondadega

5. **Sisseehitatud hoiatuste süsteem**:
   - Alertmanager pakub paindlikku hoiatuste haldamist ja marsruutimist

### 12.2 Prometheus'e roll kaasaegses jälgimises

Prometheus on saanud oluliseks osaks kaasaegsest jälgimise stackist, eriti:

- **Konteinerite ja orkestraatorite jälgimisel**: Eriti tugev Kubernetes'e keskkonnas
- **Mikroteenuste arhitektuuride jälgimisel**: Võimaldab efektiivselt jälgida suurt hulka dünaamilisi teenuseid
- **SRE (Site Reliability Engineering) praktikates**: Toetab "neli kuldset signaali" ja SLO jälgimist
- **DevOps kultuuris**: Aitab luua terviklikku vaadet süsteemide toimimisele

### 12.3 Tuleviku perspektiivid

Prometheus jätkab arenemist, keskendudes:

- Veelgi paremale skaleeruvusele
- Täiustatud integratsioonidele pilveteenustega
- PromQL'i võimekuse laiendamisele
- Ökosüsteemi laiendamisele uute tööriistade ja integratsioonidega

### 12.4 Lõppsõna

Prometheus on võimas tööriist, mis pakub väärtuslikku ülevaadet teie süsteemide toimimisest. Selle efektiivne kasutamine nõuab planeerimist ja head arusaamist oma infrastruktuurist, kuid tasub end ära parema ülevaate, kiirema probleemide tuvastamise ja efektiivsema ressursside kasutamise näol.

Jälgimise maastik areneb pidevalt ja Prometheus on selles oluline osa, pakkudes stabiilset alust, millele ehitada kaasaegseid, töökindlaid ja skaleeruvaid süsteeme.
