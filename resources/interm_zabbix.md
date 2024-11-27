# Zabbix'i Intermedium

See juhend on mõeldud neile, kes on juba Zabbix'i põhitõed selgeks saanud ja soovivad liikuda edasi keerukamate konfiguratsioonide ja parimate praktikate juurde.

## Zabbix'i Skaleerimine ja Jõudluse Optimeerimine

Suuremate süsteemide jälgimine nõuab tõhusat ressursside haldust ja arhitektuuri optimeerimist. Järgnevalt vaatleme, kuidas optimeerida Zabbix'i jõudlust ja kuidas tagada skaleerimine suuremate keskkondade puhul.

### Serveri Optimeerimine
Zabbix serveri jõudluse suurendamiseks tuleb tähelepanu pöörata erinevatele konfiguratsioonidele ja süsteemiressursside haldamisele.

#### Pollerite ja Muude Protsesside Konfiguratsioon

Zabbix kasutab mitut tüüpi protsesse, nagu pollerid, pingerid ja trappers, mis kõik vajavad süsteemiressursse. Allolevad soovitused aitavad tagada optimaalse töö:

```ini
# Server optimaalne seadistus suurele koormusele
StartPollers=200            # Pollerite arv, mis küsivad agendi poolt kogutud andmeid.
StartDiscoverers=20         # Discovererite arv, mis otsivad uusi seadmeid ja elemente.
StartPingers=25             # Pingerite arv, mis kontrollivad hostide vastavust võrgu kaudu.
StartTrappers=20            # Trapperite arv, mis töötlevad agentidelt saadetud andmeid (nt aktiivsed ühendused).
StartPreprocessors=50       # Preprocessorite arv, mis töötlevad kogutud andmeid enne salvestamist.
CacheSize=4G                # Cache suurus, mida kasutatakse Zabbix serveri mälu jaoks.
HistoryCacheSize=2G         # Cache suurus ajalooliste andmete hoidmiseks (ajaloolised mõõdikud).
TrendCacheSize=1G           # Cache suurus trendiandmete hoidmiseks (vähem detailne ajalooline info, mida hoitakse pika aja jooksul).

```

#### Andmebaasi Optimeerimine

Andmebaas on Zabbix'i süsteemi kriitiline komponent, kuna see salvestab kogu jälgimise ajaloo ja konfiguratsiooni. Andmebaasi optimeerimine aitab suurendada üldist jõudlust:

```sql
-- MySQL-i optimeerimine
innodb_buffer_pool_size = 50G
innodb_log_file_size = 4G
innodb_flush_method = O_DIRECT
innodb_io_capacity = 2000
innodb_flush_log_at_trx_commit = 2
```

Samuti tuleks regulaarselt puhastada vanu andmeid ja optimeerida tabeleid:

```sql
-- Tabelite optimeerimine
OPTIMIZE TABLE history;
OPTIMIZE TABLE trends;
```

### Proxy Kasutamine Skaleerimiseks

Zabbix Proxy võimaldab hajutada andmete kogumist ja vähendada Zabbix serveri koormust, eriti kui tegemist on suure keskkonnaga.

#### Proxy Tüübid ja Kasutusjuhtumid
- **Aktiivne Proxy** – Proxy algatab ühenduse serveriga ja saadab andmed.
- **Passiivne Proxy** – Server algatab ühenduse ja küsib andmeid Proxy-lt.

Suuremates keskkondades on soovitatav kasutada mitut Proxy't, et jagada koormust ja pakkuda täiendavat töökindlust.

```ini
# Proxy seadistus
ProxyMode=0  # 0 - aktiivne, 1 - passiivne
Server=zabbix.company.com
Hostname=proxy01
DBName=zabbix_proxy
ConfigFrequency=3600
DataSenderFrequency=1
```

## Täiustatud Jälgimisseaded

Järgnevalt käsitleme mõningaid täiustatud jälgimisvõimalusi ja seadistusi, mis võimaldavad teil Zabbix'ist rohkem kasu saada.

### Kohandatud Kontrollid ja Kasutajaparameetrid
Zabbix agendile saab lisada kohandatud kontrolli käske, mis võimaldavad jälgida spetsiifilisi süsteemi olekuid või rakendusi.

```ini
# Kohandatud kasutajaparameetrid
UserParameter=mysql.connections,/usr/local/bin/check_mysql_connections.sh
UserParameter=nginx.active_connections,/usr/local/bin/check_nginx_connections.sh
```

Need käsud saavad olla kasulikud näiteks kohandatud rakenduste jälgimiseks, mida Zabbix vaikimisi ei toeta.

### LLD (Low-Level Discovery)

LLD on võimas vahend dünaamiliste elementide, nagu failisüsteemid, võrguliidesed ja protsessid, automaatseks avastamiseks ja jälgimiseks.

#### Näide: Failisüsteemide Avastus

```json
{
    "data": [
        {
            "{#FSNAME}": "/",
            "{#FSTYPE}": "ext4"
        },
        {
            "{#FSNAME}": "/data",
            "{#FSTYPE}": "xfs"
        }
    ]
}
```

LLD aitab oluliselt vähendada käsitsi seadistamise koormust, võimaldades süsteemidel ise uusi komponente avastada ja jälgida.

### Web Scenario Jälgimine

Web scenario võimaldab monitoorida veebilehtede ja -rakenduste tervist, simuleerides kasutaja interaktsioone.

#### Näide: Veebilehe Test

```yaml
scenario:
  name: "Website Health Check"
  steps:
    - name: "Open Home Page"
      url: "https://example.com"
      status_codes: "200"
    - name: "Login Page"
      url: "https://example.com/login"
      status_codes: "200"
```

Web scenario'd on kasulikud näiteks kasutajate autentimise, makselehtede ja muude oluliste veebilehtede jälgimisel.

## Probleemide Lahendamine ja Parimad Praktikad

### Tavapärased Probleemid ja Lahendused

#### 1. Agent Ei Vasta
Kui agent ei vasta, võib see olla tingitud võrguühenduse probleemidest või valedest konfiguratsioonidest.

```bash
# Kontrolli, kas agent töötab
systemctl status zabbix-agent

# Kontrolli, kas agent saab serveriga ühendust
zabbix_get -s agent_ip -k agent.ping
```

#### 2. Andmebaasi Ülekoormus
Kui andmebaas on ülekoormatud, tuleks kontrollida ajastatud päringuid ja optimeerida suurte tabelite käsitsemist.

```sql
-- Kontrolli aeglasi päringuid
SELECT * FROM information_schema.processlist WHERE time > 10;
```

### Parimad Praktikad

- **Jõudluse Optimeerimine** – Kasutage CacheSize ja HistoryCacheSize parameetreid piisavate väärtustega, et vähendada andmebaasi koormust.
- **Turvalisus** – Seadistage SSL-ühendused ja kasutage tugevaid paroole.
- **Regulaarne Varundamine** – Varundage regulaarselt Zabbix serveri konfiguratsioon ja andmebaas, et vältida andmekadu.

## Täiendavad Ressursid

- **Zabbix Documentation**: [zabbix.com/documentation](https://www.zabbix.com/documentation)
- **Zabbix Share**: [share.zabbix.com](https://share.zabbix.com)
- **Community Forums**: [zabbix.com/forum](https://www.zabbix.com/forum)

Edukat jälgimist ja optimeerimist!

