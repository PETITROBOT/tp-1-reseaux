I. Setup IP
Le lab, il vous faut deux machines :

les deux machines doivent être connectées physiquement
vous devez choisir vous-mêmes les IPs à attribuer sur les interfaces réseau, les contraintes :

IPs privées (évidemment n_n)
dans un réseau qui peut contenir au moins 1000 adresses IP (il faut donc choisir un masque adapté)
oui c'est random, on s'exerce c'est tout, p'tit jog en se levant c:
le masque choisi doit être le plus grand possible (le plus proche de 32 possible) afin que le réseau soit le plus petit possible



🌞 Mettez en place une configuration réseau fonctionnelle entre les deux machines

```
PS C:\WINDOWS\system32> netsh interface ipv4 set address name="Ethernet" static 10.10.10.210 255.255.252.0

On est allé sur le site sipcalc

Network address		- 10.10.8.0
Network mask		- 255.255.252.0
Broadcast address	- 10.10.11.255
```
On est allé sur le site sipcalc
🌞 Prouvez que la connexion est fonctionnelle entre les deux machines
```
PS C:\Users\cedri> ping 10.10.10.200

Envoi d’une requête 'Ping'  10.10.10.200 avec 32 octets de données :
Réponse de 10.10.10.200 : octets=32 temps<1ms TTL=128
Réponse de 10.10.10.200 : octets=32 temps<1ms TTL=128
Réponse de 10.10.10.200 : octets=32 temps<1ms TTL=128
Réponse de 10.10.10.200 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 10.10.10.200:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```
🌞 Wireshark it

[](tp-reseaux2/ICMP.pcapng)

Le type de paquet ICMP:

    Type: 8 (Echo (ping) request)
    
🌞 Check the ARP table
```
La commande pour afficher la table ARP est
PS C:\Users\cedri> arp -a
```
L'adresse MAC de mon binome est:
```
 10.10.11.255
```
L'adresse MAC de la gateway de mon réseau est :
```
10.33.19.255
```

🌞 Manipuler la table ARP
```
Pour vider votre table ARP:

 netsh interface IP delete arpcache

 arp -d
```
```
 Pour verifier que mon cache arp a été vider: 
 arp -a
```
```
Ré-effectuez des pings, et constatez la ré-apparition des données dans la table ARP:

ping 10.10.10.200

arp -a
```


🌞 Wireshark it
```
 Interface : 10.10.10.210 --- 0x10
  Adresse Internet      Adresse physique      Type
  10.10.10.200          50-eb-f6-e6-0c-e3     dynamique
  10.10.11.255          ff-ff-ff-ff-ff-ff     statique 
```
```
  Interface : 10.10.10.210 --- 0x10
  Adresse Internet      Adresse physique      Type
  10.10.11.255          ff-ff-ff-ff-ff-ff     statique
```
[Trame ARP](tp-reseaux2/ARP.pcapng)


🌞 Wireshark it
```
Découverte de serveur --->  Alors que l’adresse de destination de découverte d’envoi est diffusée 255.255.255.255 et l’adresse source est 0.0.0.0.

Offre de location IP ---> l contient un paramètre de configuration réseau pour le client comme une adresse IP offerte au client 10.1.1.1.

Demande de bail IP --->Cela signifie accepter l’offre du serveur DHCP avec IP 10.1.1.1. ce message a été envoyé par le client avec l’adresse de destination 255.255.255.255 et l’adresse source est 10.1.1.1.

Reconnaissance de bail DE PI ---> Ce message indique clairement au client que vous pouvez maintenant commencer à utiliser le réseau.

1. une IP à utiliser : Offre de location IP
2. l'adresse IP de la passerelle du réseau : Découverte de serveur
3. l'adresse d'un serveur DNS joignable depuis ce réseau : Demande de bail IP
```

🦈 PCAP qui contient l'échange DORA

[DHCP](tp-reseaux2/DHCP.pcapng)