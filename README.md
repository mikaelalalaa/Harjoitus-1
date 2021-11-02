# Harjoitus-1



# a) Linux ja Salt versio
Sain katsottua mitä linux ja salt versiota pyöritän komennolla: 
```
 sudo salt-call --local grains.item osfinger saltversion
```
Käytössäni on OS ubuntu-20.04 ja Salt versiona on 3004

# b) Salt koodi


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
