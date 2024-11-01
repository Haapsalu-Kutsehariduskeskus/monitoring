- **Main Lecture is here:**: [Grafana material](https://docs.google.com/document/d/1gviMHQwUG4Fl29KddkzFz_gqnAAQgzwfc3JlecZv_So/edit?usp=sharing).

# Kokkuvõte

Grafana on juhtiv avatud lähtekoodiga platvorm monitooringu ja analüütika visualiseerimiseks. See võimaldab organisatsioonidel:
- Visualiseerida süsteemide ja rakenduste jõudlust
- Seadistada hoiatusi kriitiliste sündmuste jaoks
- Ühendada erinevaid andmeallikaid ühtseks vaateks
- Jagada andmeid meeskonna või organisatsiooni sees

## Grafana Põhikontseptsioonid

### 1. Dashboardid
Dashboardid on Grafana südameks. Need on kohandatavad vaated, mis koosnevad erinevatest paneelidest. Igal paneelil võib olla:
- Graafik
- Statistika
- Tabel
- Hoiatuste ülevaade

### 2. Andmeallikad (Data Sources)
Grafana toetab paljusid populaarseid andmeallikaid:
- **Aegridade andmebaasid:** Prometheus, InfluxDB
- **Logide andmebaasid:** Elasticsearch, Loki
- **Klassikalised andmebaasid:** PostgreSQL, MySQL
- **Pilveplatvormid:** AWS CloudWatch, Azure Monitor

### 3. Päringud (Queries)
Grafana võimaldab pärida andmeid erinevatel viisidel:
- Graafiline päringuehitaja
- SQL päringud
- PromQL (Prometheuse päringukeel)
- Muud spetsiifilised päringuvormingud

## Praktiline Näide

### Veebirakenduse Monitooring
Kujutage ette tüüpilist veebirakendust. Grafanaga saame jälgida:

**1. Infrastruktuuri Tervist**
- Serveri CPU kasutus
- Mälu kasutus
- Kettaruumi olek
- Võrgu läbilaskevõime

**2. Rakenduse Jõudlust**
- Vastuse aeg (Response time)
- Vigade arv
- Aktiivsed kasutajad
- API päringute maht

**3. Äriaspekte**
- Kasutajate registreerumine
- Tehingute arv
- Käive
- Kasutajate geograafiline jaotus

## Paigaldusvõimalused

### 1. Grafana Cloud
- Kiire alustamine
- Hallatav teenus
- Skaleeritav
- Sobib väiksematele ja keskmistele organisatsioonidele

### 2. Kohapealne Paigaldus (On-Premises)
- Täielik kontroll
- Andmed jäävad organisatsiooni sisse
- Kohandatav seadistus
- Sobib suurematele organisatsioonidele ja rangete turvanõuetega keskkondadele

## Praktilised Soovitused

### 1. Alustamiseks
- Alustage Grafana Cloud'iga proovimiseks
- Kasutage valmis dashboarde (Grafana marketplace)
- Õppige tundma põhilisi visualiseerimise võimalusi

### 2. Dashboardide Loomine
- Järgige ühtset disainikeelt
- Grupeerige seotud mõõdikud
- Kasutage templatesid korduvateks elementideks
- Lisage dokumentatsioon ja selgitused

### 3. Jõudluse Optimeerimine
- Optimeerige päringuid
- Seadke sobivad värskendusintervallid
- Kasutage vahemälu (caching) võimalusi

## Kokkuvõte

Grafana on võimas tööriist, mis:
- Muudab keerulised andmed arusaadavaks
- Võimaldab kiiresti tuvastada probleeme
- Aitab teha andmepõhiseid otsuseid
- Sobib nii väikestele kui suurtele organisatsioonidele

### Kodus, kui tahad rohkem õppida

1. Registreeruge Grafana Cloud'i
2. Tutvuge dokumentatsiooniga
3. Proovige mõnda valmis dashboardi
4. Looge oma esimene dashboard
