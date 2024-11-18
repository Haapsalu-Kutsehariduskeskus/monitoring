# Linux Logimine ja Haldamine: Põhitõed

## Sisukord
1. [Sissejuhatus Linux logimisse](#1-sissejuhatus-linux-logimisse)
   - [Mis on logimine?](#mis-on-logimine)
   - [Logimise tähtsus Linux süsteemides](#logimise-tähtsus-linux-süsteemides)
   - [Logimise ajalugu Linuxis](#logimise-ajalugu-linuxis)

2. [Linux logimise põhikomponendid](#2-linux-logimise-põhikomponendid)
   - [syslog ja rsyslog](#syslog-ja-rsyslog)
   - [journald](#journald)
   - [logrotate](#logrotate)

3. [Logifailide haldamine](#3-logifailide-haldamine)
   - [Logide vaatamine](#logide-vaatamine)
   - [Logides otsimine](#logides-otsimine)
   - [Logide pööramine](#logide-pööramine)

4. [Keskne logiserver](#4-keskne-logiserver)
   - [Keskse logiserveri eelised](#keskse-logiserveri-eelised)
   - [Keskse logiserveri seadistamine](#keskse-logiserveri-seadistamine)

5. [Logide indekseerimine ja analüüs](#5-logide-indekseerimine-ja-analüüs)
   - [Indekseerimise põhimõtted](#indekseerimise-põhimõtted)
    - [Indeksi struktuur Linuxis](#indeksi-struktuur-linuxis)

6. [Parimad praktikad Linux logimises](#6-parimad-praktikad-linux-logimises)
   - [Turvalisus](#turvalisus)
   - [Jõudlus](#jõudlus)
   - [Säilitamine ja arhiveerimine](#säilitamine-ja-arhiveerimine)

# 1. Sissejuhatus Linux logimisse

## Mis on logimine?
Logimine on protsess, mille käigus salvestatakse süsteemi või rakenduse sündmused, tegevused ja teated. See on oluline osa igast operatsioonisüsteemist, sealhulgas Linuxist.

**Logimise põhiomadused:**
- Sündmuste kronoloogiline salvestamine
- Erinevate sündmuste kategooriatesse jagamine
- Automaatne ja pidev protsess

**Logisõnumi anatoomia:**
Tüüpiline logisõnum sisaldab järgmisi elemente:
1. Ajatempel: Millal sündmus toimus
2. Allikas: Milline süsteemi osa või rakendus logi genereeris
3. Sõnumi tüüp või tase: Näiteks INFO, WARNING, ERROR
4. Sõnumi sisu: Detailne kirjeldus sündmusest

Näide logisõnumist:
```
May 7 10:23:45 myserver sshd[12345]: Failed password for invalid user test from 192.168.1.100 port 54321 ssh2
```

## Logimise tähtsus Linux süsteemides
Logimine on kriitilise tähtsusega mitmel põhjusel:

1. **Vigade tuvastamine ja lahendamine**
2. **Turvalisuse tagamine**
3. **Süsteemi jõudluse jälgimine**
4. **Vastavus regulatsioonidele ja auditeerimise võimaldamine**

```

    /var/log/syslog or /var/log/messages — global system log
    /var/log/auth.log or /var/log/secure — user authentication information
    /var/log/dmesg — hardware and device driver logs
    /var/log/anaconda.log — system installation log
    /var/log/audit — logs from the auditd daemon
    /var/log/boot.log — system boot log
    /var/log/cron — cron daemon logs
    /var/log/mysql — MySQL database logs
    /var/log/apache2 — Apache web server logs
    /var/log/nginx — NGINX web server logs

```

## Logimise ajalugu Linuxis

1. **Algne Unix syslog protokoll**
   - Loodud 1980. aastatel Eric Allman'i poolt
   - Põhiidee: standardiseeritud viis sõnumite saatmiseks ja töötlemiseks

2. **Linux-spetsiifilised täiendused**
   - klogd: kerneli logimisdeemon
   - syslogd: süsteemi logimisdeemon

3. **Kaasaegsed lahendused**
   - rsyslog: laiendatud võimalustega syslog
   - systemd journal: struktureeritud logimine

**Ajajoone näide:**
```
1980 - Unix syslog
  |
  v
1991 - Linux tuuma esmane versioon
  |
  v
2004 - rsyslog arendus algab
  |
  v
2010 - systemd (koos journald'iga) tutvustamine
```

# 2. Linux logimise põhikomponendid

## 2.1 rsyslog

rsyslog (Rocket-fast System for Log processing) on mitmekülgne ja võimas logimise lahendus Linuxi süsteemides.

**Põhiomadused:**
- Mitmelõimeline töötlus
- Toetab TCP, SSL, TLS ja RELP protokolle
- Võimalus salvestada logisid andmebaasidesse (nt MySQL)
- Paindlik filtreerimine ja vormindamine

**Konfiguratsioon:**
rsyslog'i peamine konfiguratsioonifail asub tavaliselt `/etc/rsyslog.conf`. Lisaks võivad olla täiendavad konfiguratsioonifailid kaustas `/etc/rsyslog.d/`.

Näide konfiguratsioonist:
```
# /etc/rsyslog.conf näide
authpriv.*                 /var/log/secure
mail.*                     -/var/log/maillog
cron.*                     /var/log/cron
*.emerg                    :omusrmsg:*
uucp,news.crit             /var/log/spooler
```

**rsyslog'i moodulid:**
- Sisendmoodulid (im): koguvad infot erinevatest allikatest
- Väljundmoodulid (om): saadavad sõnumeid sihtkohtadesse
- Filtreerimismoodulid (fm): filtreerivad sõnumeid
- Parsimismoodulid (pm): analüüsivad sõnumeid
- Sõnumite modifitseerimise moodulid (mm): muudavad sõnumite sisu
- Stringi genereerimise moodulid (sm): genereerivad sõnumeid

## 2.2 logrotate

logrotate on tööriist logifailide automaatseks haldamiseks, pööramiseks ja arhiveerimiseks.

**Põhiomadused:**
- Automaatne logifailide pööramine
- Vanade logide komprimeerimine
- Võimalus seadistada erinevaid reegleid eri tüüpi logidele

**Peamised konfiguratsioonivõimalused:**
- `daily`, `weekly`, `monthly`, `yearly`: pööramise sagedus
- `rotate`: säilitatavate vanade logifailide arv
- `compress`: logifailide komprimeerimine
- `delaycompress`: kompressimise edasilükkamine järgmise tsüklini
- `missingok`: ignoreerib puuduvaid logifaile

Näide konfiguratsioonist:
```
/var/log/nginx/*.log {
    daily
    missingok
    rotate 52
    compress
    delaycompress
    notifempty
    create 0640 www-data adm
    sharedscripts
    postrotate
        [ -f /var/run/nginx.pid ] && kill -USR1 `cat /var/run/nginx.pid`
    endscript
}
```

## 2.3 journald

journald on osa systemd süsteemist ja pakub struktureeritud logimist.

**Põhiomadused:**
- Binaarformaat logidele (kaitse muutmise vastu, efektiivne salvestamine)
- Struktureeritud andmed
- Indekseeritud andmed keskses hoidlas

**Konfiguratsioon:**
Peamine konfiguratsioonifail: `/etc/systemd/journald.conf`

**Logide vaatamine:**
```bash
# Kõigi logide vaatamine
journalctl

# Konkreetse teenuse logide vaatamine
journalctl -u nginx.service

# Viimase tunni logid
journalctl --since "1 hour ago"
```

## 2.4 auditd

auditd on Linuxi tuuma auditisubsüsteem, mis jälgib ja logib olulisi süsteemisündmusi turvalisuse seisukohalt.

**Põhiomadused:**
- Jälgib süsteemikutseid ja failisüsteemi muudatusi
- Võimaldab seadistada kohandatud auditreegleid
- Pakub tööriistu logide analüüsimiseks (ausearch, aureport)

**Konfiguratsioon:**
Peamine konfiguratsioonifail: `/etc/audit/auditd.conf`
Auditreeglid: `/etc/audit/audit.rules`

**Näited auditreeglite lisamisest:**
```bash
# Jälgi kõiki süsteemikutseid konkreetselt PID-ilt
auditctl -a exit,always -S all -F pid=1234

# Jälgi kõiki ebaõnnestunud failide avamise katseid
auditctl -a exit,always -S open -F success=0

# Jälgi konkreetse kataloogi muudatusi
auditctl -w /etc/important_dir/ -p wa -k important_changes
```

**Logide vaatamine:**
```bash
# Otsi sündmusi konkreetse kasutaja kohta
ausearch -ua root

# Vaata sündmusi kindlas ajavahemikus
ausearch -ts today -te now -i
```

## 2.5 abrtd

abrtd (Automatic Bug Reporting Tool) on tööriist, mis automaatselt tuvastab, kogub ja raporteerib rakenduste kokkujooksmisi.

**Põhiomadused:**
- Automaatne rakenduste krahhide tuvastamine
- Kogub diagnostilist informatsiooni analüüsiks
- Võimaldab saata vearaporteid arendajatele

**Paigaldamine ja käivitamine:**
```bash
# Paigaldamine
yum install abrt-cli abrt-desktop

# Teenuste käivitamine
systemctl start abrtd
systemctl start abrt-ccpp
```

**Vearaportite vaatamine:**
```bash
# Näita kõiki vearaporteid
abrt-cli list

# Vaata konkreetse vearaporti detaile
abrt-cli info [REPORT_ID]
```

## 2.6 kdump

kdump on Linuxi tuuma krahhide puhul mälutõmmiste tegemise mehhanism, mis võimaldab hiljem analüüsida süsteemi seisundit krahhi hetkel.

**Põhiomadused:**
- Loob mälutõmmise (core dump) tuuma krahhi korral
- Kasutab teist, väiksemat tuuma mälutõmmise tegemiseks
- Võimaldab põhjalikku analüüsi crash utiliidiga

**Seadistamine:**
1. Veenduge, et kdump on paigaldatud:
   ```bash
   yum install kexec-tools
   ```

2. Seadistage GRUB, et eraldada mälu kdump jaoks:
   Lisage kerneli käsureale `crashkernel=auto`

3. Käivitage kdump teenus:
   ```bash
   systemctl enable kdump
   systemctl start kdump
   ```

**Mälutõmmise analüüsimine:**
Kasutage `crash` utiliiti mälutõmmise analüüsimiseks:
```bash
crash /usr/lib/debug/lib/modules/$(uname -r)/vmlinux /var/crash/127.0.0.1-2019-03-15-11:32:23/vmcore
```

## 2.7 Kokkuvõte

Nende tööriistade kombinatsioon - rsyslog, logrotate, journald, auditd, abrtd ja kdump - moodustab tervikliku logimise ja süsteemi monitoorimise lahenduse Linuxis. Igal tööriistal on oma spetsiifiline roll:

- rsyslog tegeleb üldise logimisega
- logrotate haldab logifailide suurust ja arhiveerimist
- journald pakub struktureeritud ja indekseeritud logimist
- auditd jälgib spetsiifilisi turvalisusega seotud sündmusi
- abrtd aitab tuvastada ja raporteerida rakenduste vigu
- kdump võimaldab analüüsida süsteemi seisundit kriitiliste vigade korral

Nende tööriistade efektiivne kasutamine aitab tagada süsteemi stabiilsuse, turvalisuse ja probleemide kiire tuvastamise.


# 3. Logifailide haldamine

## 3.1 Logide vaatamine

Linuxis on mitmeid tööriistu logide vaatamiseks:

- `cat`: Kuvab kogu logifaili sisu
  ```bash
  cat /var/log/syslog
  ```

- `less`: Võimaldab logifaili sirvida
  ```bash
  less /var/log/syslog
  ```

- `tail`: Näitab logifaili viimaseid ridu, kasulik reaalajas jälgimiseks
  ```bash
  tail -f /var/log/syslog
  ```

- `journalctl`: systemd-põhiste süsteemide jaoks
  ```bash
  journalctl -xe
  ```

## 3.2 Logides otsimine

Efektiivseks logide analüüsiks on vajalik oskus neis otsida:

- `grep`: Otsib kindlaid mustreid
  ```bash
  grep "error" /var/log/syslog
  ```

- `awk`: Võimaldab keerukamaid otsinguid ja andmete töötlust
  ```bash
  awk '/error/ {print $1, $2, $3}' /var/log/syslog
  ```
### Keerukamad otsingud ja analüüsid

Linuxi käsurea tööriistad võimaldavad teha ka keerukamaid otsinguid ja analüüse, kombineerides erinevaid käske. Näiteks:

```bash
grep "GET" /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c | sort -nr
```

See käsk teeb järgmist:

1. `grep "GET"`: Filtreerib välja kõik read, mis sisaldavad "GET" (HTTP GET päringud).
2. `awk '{print $1}'`: Prindib välja iga rea esimese välja (tavaliselt IP-aadress).
3. `sort`: Sorteerib tulemused.
4. `uniq -c`: Loendab korduvad read ja eemaldab duplikaadid.
5. `sort -nr`: Sorteerib tulemused numbriliselt (`n`) ja pööratud järjekorras (`r`).

Tulemuseks on nimekiri IP-aadressidest, mis on teinud GET päringuid, sorteeritud päringute arvu järgi kahanevas järjekorras.

Seda käsku saab kasutada näiteks:
- Veebiserverite logide analüüsimiseks
- Populaarseimate külastajate IP-aadresside tuvastamiseks
- Potentsiaalselt kahtlase tegevuse tuvastamiseks (nt DDoS rünnakud)


## 3.3 Logide roteerumine

Logide roteerumine on oluline, et vältida kettaruumi täitumist. 
Vaatame lähemalt tüüpilist Nginx logrotate konfiguratsiooni ja selgitame iga rea tähendust:

```bash
/var/log/nginx/*.log {
    daily
    missingok
    rotate 52
    compress
    delaycompress
    notifempty
    create 0640 www-data adm
    sharedscripts
    prerotate
        if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
            run-parts /etc/logrotate.d/httpd-prerotate; \
        fi \
    endscript
    postrotate
        [ -s /run/nginx.pid ] && kill -USR1 `cat /run/nginx.pid`
    endscript
}
```
Selgitus:

1. `/var/log/nginx/*.log`: See rida määrab, milliseid logifaile roteeritakse (kõik .log laiendiga failid Nginx logikaustas).

2. `daily`: Logifaile roteeritakse iga päev.

3. `missingok`: Kui logifail puudub, jätkab logrotate ilma veateadet genereerimata.

4. `rotate 52`: Säilitatakse 52 roteeritud logifaili, mis tähendab umbes aasta jagu logisid (kui roteeritakse iga päev).

5. `compress`: Roteeritud logifailid kompresseeritakse.

6. `delaycompress`: Kompressimine lükatakse edasi järgmise rotatsioonitsüklini. See on kasulik, kui rakendus võib veel kirjutada eelmisesse logifaili.

7. `notifempty`: Tühje logifaile ei roteerita.

8. `create 0640 www-data adm`: Pärast rotatsiooni luuakse uus tühi logifail õigustega 0640, omanikuks www-data ja grupiks adm.

9. `sharedscripts`: Käivitab pre- ja post-rotatsiooniskriptid ainult üks kord, isegi kui roteeritakse mitu logifaili.

10. `prerotate` ... `endscript`: Skript, mis käivitatakse enne logide roteerimist. Siin kontrollitakse, kas eksisteerib teatud kaust ja käivitatakse seal olevad skriptid.

11. `postrotate` ... `endscript`: Skript, mis käivitatakse pärast logide roteerimist. Siin saadetakse Nginx protsessile signaal USR1, mis põhjustab logifailide uuesti avamise.

See konfiguratsioon tagab, et Nginx logid roteeritakse regulaarselt, säilitades piisava ajaloo, hoides kettaruumi kontrolli all ja tagades, et Nginx jätkab korrektselt logimist pärast rotatsiooni.

# 4. Keskne logiserver

## 4.1 Keskse logiserveri eelised

1. Parem turvalisus: logid on eraldatud potentsiaalselt ohustatud süsteemidest
2. Lihtsam analüüs: võimaldab sündmuste korrelatsiooni mitme süsteemi vahel
3. Lihtsustatud varundamine ja säilitamine
4. Ühtne vaade kõikide süsteemide logidele

## 4.2 Keskse logiserveri seadistamine

1. rsyslog konfigureerimine klientmasinas:
   ```bash
   *.* @@keskne-logiserver:514
   ```
2. rsyslog konfigureerimine serveris:
   ```bash
   $ModLoad imtcp
   $InputTCPServerRun 514
   ```
3. Tulemüüri seadistamine, et lubada logiliiklus

# 5. Logide indekseerimine ja analüüs

## 5.1 Indekseerimise põhimõtted

- Kiire otsing suurtes logikogumites
- Struktureeritud andmete salvestamine
- Reaalajas analüüsi võimaldamine

## 5.2 Indeksi struktuur Linuxis

Linuxis võib logide indekseerimine varieeruda sõltuvalt kasutatavast tarkvarast, kuid üldiselt järgitakse teatud põhimõtteid. Vaatame, kuidas indeks võib välja näha, kasutades näitena Elasticsearch'i, mis on populaarne logide indekseerimise ja otsingu lahendus.

### Elasticsearch'i indeksi struktuur:

1. **Indeks**: Elasticsearch'is on indeks sarnane relatsioonilise andmebaasi tabeliga. Näiteks võib olla indeks nimega "syslog-2023.05.24" päeva logide jaoks.

2. **Dokument**: Iga logikirje on dokument indeksis. Dokument on JSON-vormingus ja võib välja näha nii:

   ```json
   {
     "@timestamp": "2023-05-24T10:15:30.123Z",
     "host": "webserver1",
     "level": "ERROR",
     "message": "Connection refused",
     "service": "nginx",
     "client_ip": "192.168.1.100"
   }
   ```

3. **Väljad**: Iga dokumendi atribuut (nagu "host", "level", "message") on väli, mida saab indekseerida ja mille järgi saab otsida.

4. **Mappingud**: Määravad, kuidas dokumendi välju tuleks indekseerida. Näiteks:

   ```json
   {
     "properties": {
       "@timestamp": { "type": "date" },
       "host": { "type": "keyword" },
       "level": { "type": "keyword" },
       "message": { "type": "text" },
       "service": { "type": "keyword" },
       "client_ip": { "type": "ip" }
     }
   }
   ```

5. **Shardid ja replikatsioonid**: Indeks on jagatud shardideks jõudluse parandamiseks ja replitseeritud kõrgema saadavuse tagamiseks.

### Indeksi kasutamine:

1. **Logide lisamine**:
   ```bash
   curl -X POST "localhost:9200/syslog-2023.05.24/_doc" -H "Content-Type: application/json" -d'
   {
     "@timestamp": "2023-05-24T10:15:30.123Z",
     "host": "webserver1",
     "level": "ERROR",
     "message": "Connection refused",
     "service": "nginx",
     "client_ip": "192.168.1.100"
   }'
   ```

2. **Otsing indeksist**:
   ```bash
   curl -X GET "localhost:9200/syslog-2023.05.24/_search?q=level:ERROR"
   ```

3. **Agregeeritud päringud**:
   ```bash
   curl -X GET "localhost:9200/syslog-2023.05.24/_search" -H "Content-Type: application/json" -d'
   {
     "aggs": {
       "error_count": {
         "terms": {
           "field": "host",
           "size": 10
         }
       }
     }
   }'
   ```

### Indeksi eelised:

1. **Kiirus**: Indekseeritud andmetes on otsing oluliselt kiirem kui tavalistes logifailides.
2. **Struktureeritus**: Logid on struktureeritud kujul, mis lihtsustab analüüsi.
3. **Skaleerimine**: Indekseerimislahendused nagu Elasticsearch on disainitud skaleeruma suurte andmemahtudega.
4. **Reaalajas analüüs**: Võimaldab teha reaalajas päringuid ja analüüsi üle suure hulga logide.

Indekseerimine muudab logide haldamise ja analüüsi Linuxis oluliselt efektiivsemaks, võimaldades kiiresti leida vajalikku informatsiooni ja tuvastada mustreid suures hulgas logiandmetes.

# 6. Parimad praktikad Linux logimises

## 6.1 Turvalisus

- Krüpteeri logid tundliku info kaitseks
- Piira ligipääsu logifailidele
- Jälgi logisid regulaarselt kahtlase tegevuse suhtes
- Kasuta turvalist protokolli (nagu TLS) logide edastamiseks võrgus

## 6.2 Jõudlus

- Optimeeri logimise taset, et vältida liigset infot
- Kasuta efektiivseid logimismeetodeid (nt. journald)
- Monitoori logimise mõju süsteemi ressurssidele
- Kasuta logide pööramist, et vältida suuri logifaile

## 6.3 Säilitamine ja arhiveerimine

- Määra selge logide säilitamise poliitika
- Arhiveeri vanad logid pikaajalise säilitamise jaoks
- Järgi regulatiivseid nõudeid logide säilitamisel
- Kasuta automaatseid tööriistu (nagu logrotate) logide arhiveerimiseks