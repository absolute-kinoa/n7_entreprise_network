# Mise en place d'un réseau d'entreprise
**Requirements:**
- Ubuntu 22.04 server

3 machines:
e-pub, e-priv, e-router



machine | @IP | @IP_res

e-pub | 100.7.2.2 | 100.7.2.0/25

e-priv| 100.7.2.130 | 100.7.2.128/25

e-router| 100.7.2.129 & 100.7.2.1 | both

mdp:changemepls!
## Partie 1 Mise en place du réseau sur les VMs
### Q1.1 Faut-il mettre en place du NAT pour la communication entre la DMZ et le réseau privé ? Pourquoi ?
Il est utile de mettre en place le NAT afin de cacher les adresses IP du réseau privé. De la sorte il est plus difficile d'identifier les différentes machines du réseau privé.

### Q1.2
1/ ARP, TCP connexion, HTTP 
2/ Non sécurisé
3/ Connexion TCP
4/ il manque protocole SSL/TLS (vérification de certificat), résolution de nom 

### Q1.3
1/ machine privé 2 route :
default -> gateway
réseau privé

2/ DMZ : 
default -> gateway
reseau public

3/ routeur
reseau pub
reseau priv
default ?


### Q1.2 
```bash
# e-pub
sudo ip a a dev enp0s8 100.7.2.2/25

sudo apt-get update
sudo apt-get install apache2
sudo systemctl start apache2

# e-priv
iface <interface> inet4 static
    address 100.7.2.130/25
    gateway 100.7.2.129

# e-router
iface <interface> inet4 static
    address 100.7.2.129/25
    gateway 100.7.2.129

iface <interface> inet4 static
    address 100.7.2.1/25
    gateway 100.7.2.129
 ```