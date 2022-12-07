# Mise en place d'un réseau d'entreprise
**Requirements:**
- Debian

3 machines:
pub, priv, router
pub & priv have 2 interfaces
router have 3 intefaces : 
1 


machine | @IP | @IP_res

e-pub | 100.7.2.2 | 100.7.2.0/25

e-priv| 192.168.1.2 | 192.168.1.0/25

e-router| 192.168.1.1 & 100.7.2.1 | both


## Credentials :
eric:eric

root:root

## Partie 1 - Mise en place du réseau sur les VMs
### Q1.1 Faut-il mettre en place du NAT pour la communication entre la DMZ et le réseau privé ? Pourquoi ?
Il est utile de mettre en place le NAT afin de cacher les adresses IP du réseau privé. De la sorte il est plus difficile d'identifier les différentes machines du réseau privé.

### Q1.2
1/ ARP, TCP connexion, HTTP 
2/ Non sécurisé
3/ Connexion TCP
4/ il manque protocole SSL/TLS (vérification de certificat), résolution de nom 

### Q1.3
1/ machine privé 2 route :
- default -> gateway
- réseau privé

2/ DMZ : 
- default -> gateway
- reseau public

3/ routeur
- reseau pub
- reseau priv


## Configuration files for networks purposes
### Pub's /etc/network/interfaces
```bash
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
iface enp0s8 inet static
    address 100.7.2.2/24
    gateway 100.7.2.1
```

`sudo apt-get install apache2`

`sudo systemctl start apache2`

### Priv's config
```bash
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
iface enp0s8 inet static
    address 192.168.1.2/24
    gateway 192.168.1.1/24

```
### Router's config
```bash
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
iface enp0s3 inet static
    address 192.168.1.1/24

iface enp0s8 inet static
    address 100.7.2.2/24

```

activation du mode router en décommentant la ligne `net.ipv4.ip_forward=1 ` du fichier `/etc/sysctl.conf`.

reload le systectl `sysctl -p /etc/sysctl.conf`
ajout d'une regle de forwarding `iptables -P FORWARD ACCEPT`

## Partie 2 - Interconnexion avec le monde 

### 2.1 
Pas besoin de route
### 2.2 
Le VM priv car possède une adresse privée + règle de firewall ?


## Partie 3 - Communication du réseau privé avec les serveurs web
**SUR LE ROUTEUR**

- Activer le NAT sur le routeur `iptables -t nat -A POSTROUTING -o enp0s9 -j MASQUERADE` (interface de sortie vers internet)

- redirection des requetes http vers la DMZ `iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to 100.7.2.2:80`

- Sauvegarde des règles `iptables-save > /etc/iptables-rules.save` 

- Persistance après redémarrage `post-up iptables-restore < /etc/iptables-rules.save`

### 3.1
Les paquets sortants du LAN privé passeront par le routeur où le NATING s'effectuera. Le routeur NAT translatera l'adresse privé source par une adresse publique en sortie d'interface. 
### 3.2 

## Partie 4 - Mise en place de la sécurité
### 4.1
N'ouvrir que les ports TCP/UDP + http/https + arp + icmp entrant sur la DMZ
sortie -> osef ?

Les paquets entrant sur le routeur seront reject pour plus de lisibilité ?

## Partie 5 - DHCP
indiquer le DNS (ici on utilisera GOOGLE puis on remplacera par un serveur BIND sur la meme machine)