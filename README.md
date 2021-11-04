# Harjoitus-1

Tässä harjoituksessa tutustutaan komentoon grains.items/grains.item ja miten sitä filteroida ja tiivistelmiä. Miten Idempotenssi voi varmistaa/tunnistaa ja omia mausteita komentoihin pkg, file, service, user. Myös koitettu yrittää suorittaa koodia infra ja init.sls tiedostoa.  

 
# Tiivistelmät 

[Run Salt Command Locally](https://terokarvinen.com/2021/salt-run-command-locally/)
 
``` $ sudo salt-call --local -l info state.single pkg.installed (asennus tiedosto)``` komennolla voit asentaa haluamasi ohjelman 
```$ sudo salt-call --local -l info state.single pkg.removed (asennus tiedosto)``` komennolla voi poistaa haluamais ohjelman 

```$ sudo salt-call --local -l info state.single file.managed (hakemisto) ``` komennolla voit luoda haluamaasi hakemistoon tiedoston
```contents="teksti" ```lisäämällä tämän äskeiseen komennon loppuun, voi luoda sisällön tiedostolle
```$ sudo salt-call --local -l info state.single file.absent (hakemisto)``` Komennolla poistat 

```$ sudo salt-call --local -l info state.single service.running (palvelu) enable=True``` komennolla voit käynnistää jonkun palvelun esimerkiksi SSH
```$ sudo salt-call --local -l info state.single service.dead (palvelu) enable=False``` komennolla voit lopettaa jonkun palvelun esimerkiksi SSH

```$ sudo salt-call --local -l info state.single user.present (käyttäjä nimi)``` komennolla luot käyttäjän
```$ sudo salt-call --local -l info state.single user.absent (käyttäjä nimi)``` komennolla poistat käyttäjän

```$ sudo salt-call --local -l info state.single cmd.run 'touch hakemisto' creates="hekemiston" ```komento luo uuden hakemiston

[SaltStack Configuration Management](https://docs.saltproject.io/en/getstarted/config/index.html)

SaltStack konfiguraatio hallintajärjestelmän avulla voit määrittää sovelluksia, tiedostoja ja muita asetuksia. 

[Install Apache Web Server on Ubuntu](https://terokarvinen.com/2008/05/02/install-apache-web-server-on-ubuntu-4/)

 Kertoo miten voi asentee apache2 ja ottaa netti sivu käyttöön

[SaltStack Configuration Management Manage Files](https://docs.saltproject.io/en/getstarted/config/files.html)
File.Managed funktiolla voit aikallista tiedostoa määrittämällä lähdetiedoston Salt master -ohjelmassa.
File.Recurse funktiolla kopioi koko hakemiston 

[Command Line Basics Revisited](https://terokarvinen.com/2020/command-line-basics-revisited/) 

sivustolla kerrotaan erillaisia komentoja.
miten voi liikkua ja katsoa ympäriin
Miten voi luoda tai siirtää tiedostoja/hakemistoja, tai poistaa.
Kerrotaan SSH etäkäytöstä
Miten saa apua ja historian 


[SaltStack Configuration Management Functions](https://docs.saltproject.io/en/getstarted/config/functions.html)

Sivustolla kerrotaan "call salt state funktioita", kuten miten lataa jonkun ohjelman tai luoda hakemiston tai lisää käyttäjän

# a) Linux ja Salt versio
Sain katsottua mitä linux ja salt versiota pyöritän komennolla: 
```
 sudo salt-call --local grains.item osfinger saltversion
```
Käytössäni on OS ubuntu-20.04 ja Salt versiona on 3004

# b) Salt koodi
Tämäkään ei nytten onnistunut ollenkaan.
Koitin koitin komennoilla:
```
 sudo salt-call --local -l info state.single file.managed /bin/salt/hellomikaela/init.sls contens="Hei Saltin maailma"
```
Virhe tuloksena tuli
![image](https://user-images.githubusercontent.com/93308960/139927251-ffa0e573-fced-497b-912b-4d5f04a84dc1.png)

Kommenttina oli Parent directory not present katsoin googlesta mitä se mahdollisestin tarkottaa
Vastaukseksi löysin "Viittaa toisen hakemiston yläpuolella olevaan hakemistoon. Jokainen hakemisto, lukuun ottamatta juurihakemistoa, sijaitsee toisen hakemiston alla. Ylempää hakemistoa kutsutaan päähakemistoksi ja alempaa hakemistoa kutsutaan alihakemistoksi."
Lähde: https://www.webopedia.com/definitions/parent-directory/

Toinen komento jota testasin oli: 
```
sudo salt-call --local -l info state.single file.managed bin/salt/hellomikaela/init.sls contens="Hei Saltin maailma"
```
Virhe tuloksena tuli 
![image](https://user-images.githubusercontent.com/93308960/139939761-e2815d92-bca1-4fe9-acc3-30b670a771b0.png)
itse virhe "is not an absolute path" tarkoittaa täydelliset tiedot, joita tarvitaan tiedoston tai kansion löytämiseen.

epäilen kahta asiaa että joko tiedosto polku ei ole oikeuín tai komento "file.managed" ei ole oikein. Olen yrittänyt erillaisia komentoja kuten 
```
sudo salt-call --local -l info state.single cmd.run bin/salt/hellomikaela/init.sls contens="Hei Saltin maailma"
```
![image](https://user-images.githubusercontent.com/93308960/139941066-37daa86c-b5b5-464a-a95b-a4b54b80e973.png)
Tässä epälen että "cmd.run" komento kohta ei ole oikein. Myös virhe koodi "failed with return code: 127" tämä tarkoittaa että annettua komento ei löydy PATH-järjestelmämuuttujasta

En osannut enkä löytänyt tarpeellisia apu keinoja.

# c) Koneen tiedot 
Keräsin saltilla käyttöjärjestelmän, muistin ja bios tiedot komennolla: 
```
sudo salt-call --local grains.item osfinger biosversion mem_total
```
Tuloksena tuli käyttöjärjestelmä on ubunti, biosversio on VirtualBox ja muistia on 3639.

# d) Kultajyvät 
Halusin kerää kernel, ipv4 ja host tiedot esille käyttämällä salt ja grains.item komentoa. 
Komentona toimi
```
sudo salt-call --local grains.item kernel ipv4 host
```
Tuloksena tuli host nimi roott, oma ip-osoitteeni ja kernel Linux.

# e) infraa koodina

En valitettavastin osannut/muistanut miten tämä tehtiin. Yiritin googlailla kovastin mutta en löytänyt apuja.

# f) Idempotenssi
Komento jota käytin on:
```
sudo salt-call --local -l info state.single file.managed /tmp/hellomikaela contents="hai"
```
Ensimmäisellä keralla kun ajoin komennon tuloksena tuli 
```
[INFO    ] Loading fresh modules for state activity
[INFO    ] Running state [/tmp/hellomikaela] at time 14:59:21.980094
[INFO    ] Executing state file.managed for [/tmp/hellomikaela]
[INFO    ] File changed:
---
+++
@@ -0,0 +1 @@
+hai

[INFO    ] Completed state [/tmp/hellomikaela] at time 14:59:22.036174 (duration_in_ms=56.08)
local:
----------
          ID: /tmp/hellomikaela
    Function: file.managed
      Result: True
     Comment: File /tmp/hellomikaela updated
     Started: 14:59:21.980094
    Duration: 56.08 ms
     Changes:
              ----------
              diff:
                  ---
                  +++
                  @@ -0,0 +1 @@
                  +hai

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1
Total run time:  56.080 ms
```
Tästä näen kolmesta kohtaan että muutokset ovat onnistuneet. Ensimäinen kohta on "info file changed:" toinen on "changes" kohta molemmissa kohdissa näkyy mitä muutoksia tapahtui. Viimeinen kohta on "succeeded: 1 (changed=1)" joka kertoo että muutokest onnistui ja muutoksia oli yksi. 

Tämän jälkeen ajoin komennon noin neljä kertaa varmistaakseen että mitään uusia muutoksia ei tapahdu. Tuloksena tuli: 
```
[INFO    ] Loading fresh modules for state activity
[INFO    ] Running state [/tmp/hellomikaela] at time 15:42:19.777111
[INFO    ] Executing state file.managed for [/tmp/hellomikaela]
[INFO    ] File /tmp/hellomikaela is in the correct state
[INFO    ] Completed state [/tmp/hellomikaela] at time 15:42:19.827038 (duration_in_ms=49.927)
local:
----------
          ID: /tmp/hellomikaela
    Function: file.managed
      Result: True
     Comment: File /tmp/hellomikaela is in the correct state
     Started: 15:42:19.777111
    Duration: 49.927 ms
     Changes:

Summary for local
------------
Succeeded: 1
Failed:    0
------------
Total states run:     1
Total run time:  49.927 ms
```
Tässä huomaan kahdesta kohtaa että mitään uusia muutoksia ei ole tapahtunut. Ensimmäinen kohta on "Changes:" siinä ei ole mitään tietoa että muutoksia on tapahtunut verrattuna aikasempaa missä luki mitä muutoksia on tapahtunut. Toinen kohta on "Succeeded: 1" se kertoo että ajettu komento on onnistunut ja muutoksia ei ole tehty kun taas verrattuna aikaisempaan "Succeeded: 1 (changed=1)" ajettu komento on onnistutu ja muutoksia tehtiin. 

# g) omat mausteet
Asensin net-tools paketin saltin pkg avulla. 
Komento mitä käytin on:
```
sudo salt-call --local -l info state.single pkg.installed net-tools
```
Tulokseksi sain tämän joka kertoo että asennus on onnistunut.
```
     Changes:
              ----------
              net-tools:
                  ----------
                  new:
                      1.60+git20180626.aebd88e-1ubuntu1
                  old:

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1
Total run time:   8.318 s
```
Seuraavaksi tein uuden käyttäjän käyttämällä user.add.
Komentona toimi: 
```
sudo salt-call --local -l info user.add mikaela
```
Tulokseksi tuli, jonka jälkeen katsoin komennolla "tree home/" että käyttäjä oli luotu.
```
local:
    True
```
Käyttäjän luomisen jälkeen, loin sinne uuden tiedoston käyttämälläm file.managed.
Komentona toimi: 
```
sudo salt-call --local -l info state.single file.managed /home/mikaela/hellotest contents="testi hellou"
```
Tulokseksi tuli että luominen onnistui. 
```
     Changes:
              ----------
              diff:
                  New file

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1
Total run time:   7.445 ms
```
Joten kävin vielä tarkistaa että onnistui komennoilla 
```
tree home/mikaela/
cat home/mikaela/hellotest
```
![image](https://user-images.githubusercontent.com/93308960/139920775-f9626e19-4d96-4051-a69f-f017a29623de.png)

Käynnistin SSH palvelimen käyttämällä komentoa
```
sudo salt-call --local -l info state.single service.running ssh enable=True 
```
