# COMPTE RENDU TP Hugo

> NOTE : sorry pour le retard , j'ai vraiment galeré sur le TP , j'ai pas spam gpt ni rien tout a été fait dans la galère , j'ai eu plein de problème débile du a de l'inatention notamment ^^

## A. Choix de l'algorithme de chiffrement


### 🌞 Déterminer quel algorithme de chiffrement utiliser pour vos clés


> Je vais use **ED25519** , car les keys sont plus courtes et plus rapides en + vu qu'on va use que des logiciels qui datent d'avant 2014 on aura pas de soucis hehe

[SOURCES](https://www.reddit.com/r/sysadmin/comments/4gktbr/ssh_ed25519_keys_vs_rsa_benefits_and_drawbacks/?tl=fr)

[SOURCES2](https://news.ycombinator.com/item?id=12575358#:~:text=Two%20reasons%3A%201%20they%20are%20a%20lot%20shorter%20for%20the,compromised%20to%20generate%20weak%20keys.)

[SOURCES3](https://cloud.ibm.com/docs/vpc?topic=vpc-ssh-keys&locale=fr&interface=ui)


## B. Génération de votre paire de clés

### 🌞 Générer une paire de clés pour ce TP

  > PS C:\Users\exoli> ssh-keygen -t ed25519 -f C:\Users\exoli\.ssh/cloud_tp
```
Generating public/private ed25519 key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\exoli\.ssh/cloud_tp
Your public key has been saved in C:\Users\exoli\.ssh/cloud_tp.pub
The key fingerprint is:
SHA256:+vZTtAWlfSDlj4x+lHvnpU7jW799bByE5xpHVjwHvcE exoli@n34
The key's randomart image is:
+--[ED25519 256]--+
|            .o*+ |
|            .= E=|
|            ..+ B|
|            .+.@ |
|        S  ..o@ .|
|       .   .oo = |
|      .    .. Oo*|
|       .. .  = *X|
|       ..... .=+*|
+----[SHA256]-----+
```


> PS C:\Users\exoli> ls C:\Users\exoli\.ssh\cloud_tp

```
    Répertoire : C:\Users\exoli\.ssh


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        30/10/2025     02:11            444 cloud_tp
```

### 🌞 Configurer un agent SSH sur votre poste

> ssh-add $env:USERPROFILE\.ssh\cloud_tp
```
Identity added: C:\Users\exoli\.ssh\cloud_tp (exoli@n34)
```



# II. Spawn des VMs

// Btw j'ai été méga débile j'ai oublié de save mon MD dcp j'ai perdu toute cette partie du TP , je remet les commandes que je peux faire mais dcp les VMs etc ont déja été crées il y'a plusieurs jours preuve ici : 

[preuves2madébilité](image.png)

### 🌞 Connectez-vous en SSH à la VM pour preuve

> PS C:\Users\exoli> SSH ne44@40.89.160.61
```
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1041-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
 ```

## 2. az : a programmatic approach

### 🌞 Créez une VM depuis le Azure CLI : azure1.tp1




> PS C:\Users\exoli> az vm create `
>>   --resource-group GambergeLand `
>>   --name azure1.tp1 `
>>   --image Ubuntu2204 `
>>   --admin-username ne44 `
>>   --ssh-key-values "$env:USERPROFILE\.ssh\cloud_tp.pub" `
>>   --authentication-type ssh `
>>   --size Standard_B1s
```
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/f1f8efce-4dfd-40de-8590-a78f3fdb12ca/resourceGroups/GambergeLand/providers/Microsoft.Compute/virtualMachines/azure1.tp1",
  "location": "francecentral",
  "macAddress": "60-45-BD-6D-F5-38",
  "powerState": "VM running",
  "privateIpAddress": "172.16.0.5",
  "publicIpAddress": "40.89.160.61",
  "resourceGroup": "GambergeLand" 
  ```

  ### 🌞 Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique

  >  PS C:\Users\exoli> ssh -i $env:USERPROFILE\.ssh\cloud_tp ne44@40.89.160.61

```
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1041-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
 ```

 ### 🌞 Affichez des infos au sujet de vos deux VMs

 > PS C:\Users\exoli> az vm list -g Gambergeland -d --query "[].{name:name, power:powerState, size:hardwareProfile.vmSize, location:location, admin:osProfile.adminUsername, ipPriv:privateIps, ipPub:publicIps}" -o table

 ```
 Name        Power       Size          Location       Admin    IpPriv      IpPub
----------  ----------  ------------  -------------  -------  ----------  -------------
azure1.tp1  VM running  Standard_B1s  francecentral  ne44     172.16.0.5  40.89.160.61
azure2.tp1  VM running  Standard_B1s  francecentral  ne43     172.16.0.6  20.188.46.15
Hehe        VM running  Standard_B1s  francecentral  wechugo  172.16.0.4  4.251.127.104
```
### 🌞 Configuration SSH client pour les deux machines

> btw ici j'ai perdu deux heures car j'avais pas mis le même user ( ne44 et ne43 ) donc j'arrivais pas a me co ^^ 
```
# Connexion locale
Host localhost
  HostName localhost
  User ne44

# Connexion directe à la VM exposée
Host azure1.tp1
    HostName 40.89.160.61
    User ne44
    IdentityFile ~/.ssh/cloud_tp
    IdentitiesOnly yes
    ForwardAgent yes

# Connexion à la VM interne via rebond sur azure1.tp1
Host azure2.tp1
    HostName 172.16.0.6
    User ne43
    IdentityFile ~/.ssh/cloud_tp
    IdentitiesOnly yes
    ProxyJump azure1.tp1
```
# III. Déployer et configurer un machin

## 1. Machine azure2.tp1

### 🌞 Installer MySQL/MariaDB sur azure2.tp1

> sudo apt install mariadb-server -y

```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  galera-4 libcgi-fast-perl libcgi-pm-perl libclone-perl libconfig-inifiles-perl libdbd-mysql-perl libdbi-perl
  libencode-locale-perl libfcgi-bin libfcgi-perl libfcgi0ldbl libhtml-parser-perl libhtml-tagset-perl
  libhtml-template-perl libhttp-date-perl libhttp-message-perl libio-html-perl liblwp-mediatypes-perl libmariadb3
  libmysqlclient21 libsnappy1v5 libtimedate-perl liburi-perl liburing2 mariadb-client-10.6 mariadb-client-core-10.6
  mariadb-common mariadb-server-10.6 mariadb-server-core-10.6 mysql-common socat
```

### 🌞 Démarrer le service MySQL/MariaDB sur azure2.tp1

> sudo systemctl status mariadb

```
● mariadb.service - MariaDB 10.6.22 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
     Active: active (running)
```

### 🌞 Ajouter un utilisateur dans la base de données pour que mon app puisse s'y connecter

> ne43@azure2:~$ sudo mysql
```
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 31
Server version: 10.6.22-MariaDB-0ubuntu0.22.04.1 Ubuntu 22.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> CREATE USER 'meow'@'%' IDENTIFIED BY 'meow';
Query OK, 0 rows affected (0.006 sec)

MariaDB [(none)]> CREATE DATABASE meow_database;
Query OK, 1 row affected (0.000 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON meow_database.* TO 'meow'@'%';
Query OK, 0 rows affected (0.005 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.001 sec)
```

### 🌞 Ouvrez un port firewall si nécessaire

> ne43@azure2:~$ sudo ss -lnpt

```
State    Recv-Q   Send-Q      Local Address:Port       Peer Address:Port   Process
LISTEN   0        80              127.0.0.1:3306            0.0.0.0:*       users:(("mariadbd",pid=37662,fd=21))
LISTEN   0        128               0.0.0.0:22              0.0.0.0:*       users:(("sshd",pid=892,fd=3))
LISTEN   0        4096        127.0.0.53%lo:53              0.0.0.0:*       users:(("systemd-resolve",pid=521,fd=14))
LISTEN   0        128                  [::]:22                 [::]:*       users:(("sshd",pid=892,fd=4))
```

# 2. Machine azure1.tp1

### 🌞 Récupération de l'application sur la machine

> ne43@azure2:~$  sudo apt update
```
Hit:1 http://azure.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://azure.archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
Get:3 http://azure.archive.ubuntu.com/ubuntu jammy-backports InRelease [127 kB]
Get:4 http://azure.archive.ubuntu.com/ubuntu jammy-security InRelease [129 kB]
Get:5 http://azure.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [3073 kB]
Get:6 http://azure.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1242 kB]
Get:7 http://azure.archive.ubuntu.com/ubuntu jammy-security/main amd64 Packages [2808 kB]
Get:8 http://azure.archive.ubuntu.com/ubuntu jammy-security/main amd64 c-n-f Metadata [13.9 kB]
Get:9 http://azure.archive.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [1008 kB]
Fetched 8529 kB in 2s (3639 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
```

> ne43@azure2:~$ sudo apt install git -y
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
git is already the newest version (1:2.34.1-1ubuntu1.15).
git set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
> ne43@azure2:~$ sudo mkdir -p /opt/meow
> ne43@azure2:~$ sudo git clone https://gitlab.com/it4lik/b2-pano-cloud-2025.git /opt/meow

```
Cloning into '/opt/meow'...
remote: Enumerating objects: 457, done.
remote: Counting objects: 100% (377/377), done.
remote: Compressing objects: 100% (374/374), done.
remote: Total 457 (delta 188), reused 0 (delta 0), pack-reused 80 (from 1)
Receiving objects: 100% (457/457), 14.26 MiB | 33.97 MiB/s, done.
Resolving deltas: 100% (211/211), done.
ne43@azure2:~$
```

### 🌞 Installation des dépendances de l'application

j'ai ragé dcp j'ai tout mit d'un coup cimer gpto

> sudo apt update
sudo apt install python3-pip -y
sudo apt install python3-venv -y  
cd ~
mkdir -p meow_app
cd meow_app
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r /opt/meow/docs/tp/1/app/requirements.txt

### 🌞 Configuration de l'application

```
# Flask Configuration
FLASK_SECRET_KEY=1gpopsoAz1EbqBF9lYOXxZEdL-f8oyJfIpkEgIwczSF_zRH3D1EGdLVqM-_R5NRbWWzqAQsoc8y6JI8vpRG7qg
FLASK_DEBUG=False
FLASK_HOST=0.0.0.0
FLASK_PORT=8000

# Database Configuration
DB_HOST=172.16.0.6
DB_USER=meow
DB_PASS=meow
DB_NAME=meow_database
APP_PORT=8000
DEBUG=True
```

### 🌞 Gestion de users et de droits

> ne43@azure2:/opt/meow/docs/tp/1/app$ sudo useradd --system webapp
ne43@azure2:/opt/meow/docs/tp/1/app$ sudo chown -R webapp:webapp /opt/meow
ne43@azure2:/opt/meow/docs/tp/1/app$ sudo chmod -R 750 /opt/meow

### 🌞 Création d'un service webapp.service pour lancer l'application

> ne43@azure2:/opt/meow/docs/tp/1/app$ sudo nano /etc/systemd/system/webapp.service
ne43@azure2:/opt/meow/docs/tp/1/app$ sudo systemctl daemon-reload
ne43@azure2:/opt/meow/docs/tp/1/app$

### 🌞 Ouverture du port80 dans le(s) firewall(s)

> ne43@azure2:/opt/meow/docs/tp/1/app$ sudo ufw allow 8000/tcp

```
Rules updated
Rules updated (v6)
```
> ne43@azure2:/opt/meow/docs/tp/1/app$  sudo ufw enable
```
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```
> ne43@azure2:/opt/meow/docs/tp/1/app$ sudo ufw status
```
Status: active

To                         Action      From
--                         ------      ----
8000/tcp                   ALLOW       Anywhere
8000/tcp (v6)              ALLOW       Anywhere (v6)
```

# 3. Visitez l'application

### 🌞 L'application devrait être fonctionnelle sans soucis à partir de là

> curl -i http://40.89.160.61:8000

