# TP1 - Premier pas réseau


En utilisant la ligne de commande (CLI) de votre OS :

**🌞 Affichez les infos des cartes réseau de votre PC**

``` 
ip config /all

Carte réseau sans fil Wi-Fi MAC: DC-F5-05-E1-ED-7D IPv4: 10.33.16.226
```

```
Carte Ethernet Ethernet MAC: 04-D4-C4-73-EB-B0
```

**🌞 Affichez votre gateway**
``` 
ip config /all

Gateway: 10.33.19.254
```

**🌞 Déterminer la MAC de la passerelle**
``` 
ARP -A
MAC: 00-c0-e7-e0-04-4e 
```

**🌞 Trouvez comment afficher les informations sur une carte IP (change selon l'OS)**

```
Pour afficher l'adresse l'IP, la MAC et la gateway il faut ouvrir le panneau de configuration, Réseau et Internet, Connexions réseau,cliqué sur le réseaux wifi Ynov puis ouvrir les détails du réseaux
```


## 2. Modifications des informations

### A. Modification d'adresse IP (part 1)  

🌞 Utilisez l'interface graphique de votre OS pour **changer d'adresse IP** :

```
Ouvrez le Centre de réseau et de partage dans le Panneau de configuration.
Cliquez sur le lien Connexions.
Cliquez sur l’onglet Propriétés dans la fenêtre qui s’ouvre.
Sélectionnez Internet Protocol version 4 (TCP/IP v4).
Sélectionnez Utiliser l’adresse IP suivante et saisissez l’adresse IP.
Sélectionnez Utiliser le serveur DNS suivant et saisissez l’adresse appropriée.
Cochez la case Valider les paramètres à la sortie et cliquez sur OK.
Votre PC exécute automatiquement des diagnostics réseau et vérifie votre connexion.
```

🌞 **Il est possible que vous perdiez l'accès internet.** 
``` 
On perd l'accès a internet car si l'adresse IPv4 est déja utilisé c'est le premier ordinateur connecté à cette adresse qui la garde et donc on est rejeté du réseau
```

# II. Exploration locale en duo

Owkay. Vous savez à ce stade :

- afficher les informations IP de votre machine
- modifier les informations IP de votre machine
- c'est un premier pas vers la maîtrise de votre outil de travail

On va maintenant répéter un peu ces opérations, mais en créant un réseau local de toutes pièces : entre deux PCs connectés avec un câble RJ45.

## 1. Prérequis

- deux PCs avec ports RJ45
- un câble RJ45
- **firewalls désactivés** sur les deux PCs

## 2. Câblage

Ok c'est la partie tendue. Prenez un câble. Branchez-le des deux côtés. **Bap.**

## Création du réseau (oupa)

Cette étape pourrait paraître cruciale. En réalité, elle n'existe pas à proprement parlé. On ne peut pas "créer" un réseau.

**Si une machine possède une carte réseau, et si cette carte réseau porte une adresse IP**, alors cette adresse IP se trouve dans un réseau (l'adresse de réseau). Ainsi, **le réseau existe. De fait.**  

**Donc il suffit juste de définir une adresse IP sur une carte réseau pour que le réseau existe ! Bap.**

## 3. Modification d'adresse IP

🌞 **Modifiez l'IP des deux machines pour qu'elles soient dans le même réseau**
```
Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.225(en double)
Masque de sous-réseau. . . . . . . . . : 255.255.255.0
```
🌞 **Vérifier à l'aide d'une commande que votre IP a bien été changée**
```
ipconfig /all
```

🌞 **Vérifier que les deux machines se joignent**
```
ping 10.10.10.213

Envoi d’une requête 'Ping'  10.10.10.213 avec 32 octets de données :
Réponse de 10.10.10.213 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.213 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.213 : octets=32 temps=3 ms TTL=128
Réponse de 10.10.10.213 : octets=32 temps=1 ms TTL=128
```

🌞 **Déterminer l'adresse MAC de votre correspondant**
```
 arp -a

Interface : 10.33.19.192 --- 0xb
  Adresse Internet      Adresse physique      Type
  10.33.17.98           24-ee-9a-5a-77-60     dynamique
  ```

## 4. Utilisation d'un des deux comme gateway

Ca, ça peut toujours dépann irl. Comme pour donner internet à une tour sans WiFi quand y'a un PC portable à côté, par exemple.

L'idée est la suivante :

- vos PCs ont deux cartes avec des adresses IP actuellement
  - la carte WiFi, elle permet notamment d'aller sur internet, grâce au réseau YNOV
  - la carte Ethernet, qui permet actuellement de joindre votre coéquipier, grâce au réseau que vous avez créé :)
- si on fait un tit schéma tout moche, ça donne ça :

```schema
  Internet           Internet
     |                   |
    WiFi                WiFi
     |                   |
    PC 1 ---Ethernet--- PC 2
    
- internet joignable en direct par le PC 1
- internet joignable en direct par le PC 2
```

- vous allez désactiver Internet sur une des deux machines, et vous servir de l'autre machine pour accéder à internet.

```schema
  Internet           Internet
     X                   |
     X                  WiFi
     |                   |
    PC 1 ---Ethernet--- PC 2
    
- internet joignable en direct par le PC 2
- internet joignable par le PC 1, en passant par le PC 2
```

- pour ce faiiiiiire :
  - désactivez l'interface WiFi sur l'un des deux postes
  - s'assurer de la bonne connectivité entre les deux PCs à travers le câble RJ45
  - **sur le PC qui n'a plus internet**
    - sur la carte Ethernet, définir comme passerelle l'adresse IP de l'autre PC
  - **sur le PC qui a toujours internet**
    - sur Windows, il y a une option faite exprès (google it. "share internet connection windows 10" par exemple)
    - sur GNU/Linux, faites le en ligne de commande ou utilisez [Network Manager](https://help.ubuntu.com/community/Internet/ConnectionSharing) (souvent présent sur tous les GNU/Linux communs)
    - sur MacOS : toute façon vous avez pas de ports RJ, si ? :o (google it sinon)

---

🌞**Tester l'accès internet**

```
Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%)
 ```

🌞 **Prouver que la connexion Internet passe bien par l'autre PC**
```
PS C:\Users\cedri> tracert 1.1.1.1

Détermination de l’itinéraire vers one.one.one.one [1.1.1.1]
avec un maximum de 30 sauts :

  1     1 ms     *        1 ms  LAPTOP-B0TVBCEU.mshome.net [192.168.137.1]
  2     *        *        *     Délai d’attente de la demande dépassé.
  3     7 ms     6 ms     6 ms  10.33.19.254
  4     2 ms     7 ms     8 ms  137.149.196.77.rev.sfr.net [77.196.149.137]
  5    14 ms    13 ms     9 ms  108.97.30.212.rev.sfr.net [212.30.97.108]
  6    22 ms    22 ms    22 ms  222.172.136.77.rev.sfr.net [77.136.172.222]
  7    21 ms    20 ms    28 ms  221.172.136.77.rev.sfr.net [77.136.172.221]
  8    22 ms    23 ms    22 ms  221.10.136.77.rev.sfr.net [77.136.10.221]
  9    24 ms    23 ms    21 ms  221.10.136.77.rev.sfr.net [77.136.10.221]
 10    27 ms    24 ms    49 ms  141.101.67.254
 11    23 ms    23 ms    26 ms  172.71.132.2
 12    24 ms    22 ms    22 ms  one.one.one.one [1.1.1.1]
```
## 5. Petit chat privé

![Netcat](./pics/netcat.jpg)

On va créer un chat extrêmement simpliste à l'aide de `netcat` (abrégé `nc`). Il est souvent considéré comme un bon couteau-suisse quand il s'agit de faire des choses avec le réseau.

Sous GNU/Linux et MacOS vous l'avez sûrement déjà, sinon débrouillez-vous pour l'installer :). Les Windowsien, ça se passe [ici](https://eternallybored.org/misc/netcat/netcat-win32-1.11.zip) (from https://eternallybored.org/misc/netcat/).  

Une fois en possession de `netcat`, vous allez pouvoir l'utiliser en ligne de commande. Comme beaucoup de commandes sous GNU/Linux, Mac et Windows, on peut utiliser l'option `-h` (`h` pour `help`) pour avoir une aide sur comment utiliser la commande.  

Sur un Windows, ça donne un truc comme ça :

```schema
C:\Users\It4\Desktop\netcat-win32-1.11>nc.exe -h
[v1.11 NT www.vulnwatch.org/netcat/]
connect to somewhere:   nc [-options] hostname port[s] [ports] ...
listen for inbound:     nc -l -p port [options] [hostname] [port]
options:
        -d              detach from console, background mode

        -e prog         inbound program to exec [dangerous!!]
        -g gateway      source-routing hop point[s], up to 8
        -G num          source-routing pointer: 4, 8, 12, ...
        -h              this cruft
        -i secs         delay interval for lines sent, ports scanned
        -l              listen mode, for inbound connects
        -L              listen harder, re-listen on socket close
        -n              numeric-only IP addresses, no DNS
        -o file         hex dump of traffic
        -p port         local port number
        -r              randomize local and remote ports
        -s addr         local source address
        -t              answer TELNET negotiation
        -u              UDP mode
        -v              verbose [use twice to be more verbose]
        -w secs         timeout for connects and final net reads
        -z              zero-I/O mode [used for scanning]
port numbers can be individual or ranges: m-n [inclusive]
```

L'idée ici est la suivante :

- l'un de vous jouera le rôle d'un *serveur*
- l'autre sera le *client* qui se connecte au *serveur*

Précisément, on va dire à `netcat` d'*écouter sur un port*. Des ports, y'en a un nombre fixe (65536, on verra ça plus tard), et c'est juste le numéro de la porte à laquelle taper si on veut communiquer avec le serveur.

Si le serveur écoute à la porte 20000, alors le client doit demander une connexion en tapant à la porte numéro 20000, simple non ?  

Here we go :

🌞 **sur le PC *serveur*** 
```
ping 10.10.10.210

        Envoi d’une requête 'Ping'  10.10.10.210 avec 32 octets de données :
        Réponse de 10.10.10.210 : octets=32 temps<1ms TTL=128
        Réponse de 10.10.10.210 : octets=32 temps<1ms TTL=128
        Réponse de 10.10.10.210 : octets=32 temps<1ms TTL=128
        Réponse de 10.10.10.210 : octets=32 temps<1ms TTL=128

        Statistiques Ping pour 10.10.10.210:
        Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
        Durée approximative des boucles en millisecondes :
        Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
        PS C:\Users\pc\Downloads\netcat-1.11> .\nc.exe -l -p 8888
        mec
        ftg
        tg
        bouffon
        fdp
        chu mort ca marche
        c bi1
        
```
🌞 **sur le PC *client*** 

```
ping 10.10.10.213

Envoi d’une requête 'Ping'  10.10.10.213 avec 32 octets de données :
Réponse de 10.10.10.213 : octets=32 temps<1ms TTL=128
Réponse de 10.10.10.213 : octets=32 temps<1ms TTL=128
Réponse de 10.10.10.213 : octets=32 temps<1ms TTL=128
Réponse de 10.10.10.213 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 10.10.10.213:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
PS C:\Users\cedri\Desktop\netcat-1.11> ^C
PS C:\Users\cedri\Desktop\netcat-1.11> .\nc.exe 10.10.10.213 8888
ftg
mec
tg
bouffon
fdp
chu mort ca marche
c bi1
```

🌞 **Visualiser la connexion en cours**
```
netstat -a -n -b
TCP    10.10.10.210:54361     10.10.10.225:8888      ESTABLISHED
 [nc.exe]
 ```
🌞 **Pour aller un peu plus loin**

- si vous faites un `netstat` sur le serveur AVANT que le client `netcat` se connecte, vous devriez observer que votre serveur `netcat` écoute sur toutes vos interfaces
  - c'est à dire qu'on peut s'y connecter depuis la wifi par exemple :D
```
   netstat -a -n -b | select-string 8888

  TCP    0.0.0.0:8888           0.0.0.0:0       LISTENING

```

- il est possible d'indiquer à `netcat` une interface précise sur laquelle écouter
  - par exemple, on écoute sur l'interface Ethernet, mais pas sur la WiFI
```
  .\nc.exe -l -p 8888 -s 10.10.10.225

```

```
netstat -a -n -b | select-string 8888

  TCP    10.10.10.225:8888      0.0.0.0:0              LISTENING
```



## 6. Firewall

Toujours par 2.

Le but est de configurer votre firewall plutôt que de le désactiver

🌞 **Activez et configurez votre firewall**
```
Il faut ouvrir Pare-feu Windows Defender avec fonctions avancés de sécurité puis cliquer sur règles de trafic entrant, séléctionner Partage de fichier et d'imprimantes (Demande d'écho - Trafic entrant ICMPv4) et clique droit dessus et activer la règle
```
  
# III. Manipulations d'autres outils/protocoles côté client

## 1. DHCP

Bon ok vous savez définir des IPs à la main. Mais pour être dans le réseau YNOV, vous l'avez jamais fait.  

C'est le **serveur DHCP** d'YNOV qui vous a donné une IP.

Une fois que le serveur DHCP vous a donné une IP, vous enregistrer un fichier appelé *bail DHCP* qui contient, entre autres :

- l'IP qu'on vous a donné
- le réseau dans lequel cette IP est valable

🌞**Exploration du DHCP, depuis votre PC**
```
PS C:\Users\Guillaume> ipconfig /all

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wi-Fi 6 AX1650i 160MHz Wireless Network Adapter (201NGW)
   Adresse physique . . . . . . . . . . . : 4C-03-4F-E9-73-F7
   DHCP activé. . . . . . . . . . . . . . : Oui <---- 
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::b980:3214:57b0:7a1d%6(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.16.226(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Bail obtenu. . . . . . . . . . . . . . : mercredi 5 octobre 2022 09:05:38 <----------
   Bail expirant. . . . . . . . . . . . . : jeudi 6 octobre 2022 09:05:38 <----------
   Passerelle par défaut. . . . . . . . . : 10.33.19.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.19.254 <----------
   IAID DHCPv6 . . . . . . . . . . . : 88867663
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-29-C3-B8-56-08-8F-C3-51-69-8E
```
🌞** Trouver l'adresse IP du serveur DNS que connaît votre ordinateur**
```
PS C:\Users\cedric> ipconfig /all

   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       8.8.4.4
                                       1.1.1.1
````
🌞 Utiliser, en ligne de commande l'outil `nslookup` (Windows, MacOS) ou `dig` (GNU/Linux, MacOS) pour faire des requêtes DNS à la main
```
PS C:\Users\cedric> nslookup google.com
Serveur :   dns.google
Address:  8.8.8.8  <---- requête à cette adresse

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:819::200e
          142.250.178.142
```
```
PS C:\Users\cedric> nslookup ynov.com
Serveur :   dns.google
Address:  8.8.8.8 <---- requête à cette adresse
Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:ae9
          2606:4700:20::681a:be9
          2606:4700:20::ac43:4ae2
          172.67.74.226
          104.26.11.233
          104.26.10.233
```
Les deux utilises le même DNS, celui de google.

```
PS C:\Users\cedric> nslookup.exe 231.34.113.12
Serveur :   dns.google
Address:  8.8.8.8

*** dns.google ne parvient pas à trouver 231.34.113.12 : Non-existent domain

PS C:\Users\cedric> nslookup.exe 78.34.2.17
Serveur :   dns.google
Address:  8.8.8.8

Nom :    cable-78-34-2-17.nc.de <---- Domaine
Address:  78.34.2.17

```
```
Ici seulement une des deux adresses existes sur la première on voit que le premier n'a pas de domaine, `231.34.113.12 ` n'est donc pas présent dans le DNS de google.
```
# IV. Wireshark

🌞 Utilisez le pour observer les trames qui circulent entre vos deux carte Ethernet. Mettez en évidence :
```
![](https://imgur.com/FcZcH71)
![](https://imgur.com/TjPLyW4)
![](https://i.imgur.com/9v8O7xz.png)

```
🌞 Wireshark it
``` 
Notre PC se connecte à l'adresse 77.136.192.79 et au port 443.
```
