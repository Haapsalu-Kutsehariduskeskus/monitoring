# **Juhend: Kuidas luua tasuta proovikonto DigitalOceanis ja seadistada Ubuntu droplet**

## **1. samm: Loo tasuta proovikonto DigitalOceanis**

1. **Ava DigitalOceani veebisait:**
   - Mine [DigitalOceani kodulehele](https://www.digitalocean.com).
   
2. **Vajuta "Get Started" või "Sign Up":**
   - Kodulehel klõpsa nuppu “Get Started” või “Sign Up”.

3. **Sisesta oma andmed:**
   - Sul palutakse sisestada e-posti aadress ja parool. Alternatiivina võid kasutada ka **Google** või **GitHub** kontot.

4. **Tasuta krediidid:**
   - Pärast registreerimist saad automaatselt $200 väärtuses krediiti, mida saad kasutada 60 päeva jooksul.

5. **Kinnita oma e-posti aadress:**
   - Kontrolli oma postkasti ja klõpsa kinnituse lingil, mille DigitalOcean sulle saadab.

6. **Makseteave:**
   - Konto aktiveerimiseks pead lisama maksemeetodi. Võid kasutada krediitkaarti või PayPali, kuid **sind ei võeta maksma** enne, kui ületad tasuta krediidid.

---

## **2. samm: Ubuntu dropleti loomine**

1. **Logi sisse DigitalOceani:**
   - Mine [DigitalOceani sisselogimise lehele](https://cloud.digitalocean.com/login) ja logi sisse oma e-posti või Google/GitHub kontoga.

2. **Loo Droplet:**
   - Kui oled juhtpaneelil, klõpsa üleval paremas nurgas **"Create"** nuppu ja vali rippmenüüst **"Droplets"**.

3. **Vali Image:**
   - "Choose an image" jaotises vali operatsioonisüsteemiks **Ubuntu**.
   - Soovitatav on valida uusim LTS versioon, näiteks **Ubuntu 24.04 (LTS)**.

4. **Vali Dropleti plaan:**
   - Tasuta prooviperioodi ajal vali odavam variant, näiteks **$4/kuus**, mis annab 1GB RAM, 1 vCPU, 25GB SSD ja 1TB andmemahtu.

5. **Vali andmekeskuse asukoht:**
   - Vali andmekeskus, mis asub sinule või sinu kasutajatele kõige lähemal. Populaarsed valikud on **New York**, **Amsterdam** ja **Singapur**.

6. **Autentimine:**
   - Sul on kaks võimalust autentimiseks: **SSH võtmega** või **parooliga**:
     - **SSH võti**: Kui soovid kasutada SSH võtit, laadi oma avalik SSH võti üles.
     - **Parool**: Alternatiivselt saad määrata dropletile root kasutaja parooli.

7. **Dropleti seadete lõpuleviimine:**
   - Soovi korral võid lisada varukoopiad, monitooringu ja plokksalvestuse, kuid need on valikulised.
   - Lisa märksõnad või projekti nimed organisatsioonilistel eesmärkidel.

8. **Loo Droplet:**
   - Kui kõik on seadistatud, klõpsa **"Create Droplet"** nuppu.
   - Loomeprotsess võtab mõne hetke ja seejärel on droplet valmis.

9. **Ligipääs Dropletile:**
   - Kui droplet on loodud, annab DigitalOcean sulle dropleti IP-aadressi. Logi sisse serverisse terminalist järgmise käsuga:
     ```bash
     ssh root@your_droplet_ip
     ```
   - Kui valisid autentimiseks parooli, siis palutakse sul sisestada varem loodud parool.

---

## **3. samm: Ubuntu dropleti esmane seadistamine**

1. **Paketiloendite uuendamine:**
   - Pärast sisselogimist uuenda esimesena dropleti paketiloendid:
     ```bash
     apt update
     ```

2. **Paigaldatud pakettide uuendamine:**
   - Uuenda ka kõik aegunud paketid:
     ```bash
     apt upgrade
     ```

---

## **4. samm: Kasuta oma dropletit**

Nüüd on sul täielikult funktsionaalne Ubuntu Droplet, mida saad kasutada veebisaitide majutamiseks, rakenduste käivitamiseks või teenuste haldamiseks. Sa saad hallata ja jälgida oma dropletit DigitalOceani juhtpaneeli kaudu.

---

## **Järgmised sammud:**
Sa saad avastada täiendavaid võimalusi DigitalOceani juhtpaneelil, näiteks:
- **One-click installatsioonid** rakendustele nagu WordPress, LAMP või Docker.
- **Võrgustiku võimalused** nagu floating IP-d, privaatvõrgud j