# 📡 Architecture Réseau d’Entreprise – OSPF, HSRP, VLAN & Supervision Zabbix
<img width="938" height="866" alt="Screenshot_20260223_235022" src="https://github.com/user-attachments/assets/76fafdbb-a83b-4040-b876-3e42a4dab2f9" />

# 📌 Présentation du Projet

Ce projet consiste à concevoir et implémenter une architecture réseau d’entreprise hautement disponible intégrant :

* 🔁 OSPF (routage dynamique – Area 0)

* 🛡 HSRP (redondance de passerelle)

* 🏷 Segmentation VLAN

* 📊 Supervision via Zabbix (SNMP)

# L’objectif est de mettre en place une couche de distribution redondante capable d’assurer :

* La continuité de service

* La convergence rapide du routage

* La segmentation logique des départements

* La supervision en temps réel des équipements réseau

# 🏗 Architecture Réseau
🔹 Couche Distribution

Deux commutateurs Layer 3 :

* Sw1 → HSRP Actif (Priority 150)

* Sw2 → HSRP Standby (Priority 100)

🔹 Liaisons redondantes inter-distribution :

10.10.11.0/30

10.10.12.0/30

Injection de la route par défaut vers le cœur du réseau.

# 🏷 Plan d’Adressage & VLAN
| VLAN | Département | Sous-réseau     | Passerelle virtuelle |
| ---- | ----------- | --------------- | -------------------- |
| 10   | IT          | 192.168.10.0/24 | 192.168.10.1         |
| 20   | DAF         | 192.168.20.0/24 | 192.168.20.1         |
| 30   | DRH         | 192.168.30.0/24 | 192.168.30.1         |
| 40   | FIN         | 192.168.40.0/24 | 192.168.40.1         |


 Le routage inter-VLAN est assuré au niveau de la couche Distribution.

# 🔁 Haute Disponibilité – HSRP

HSRP est configuré pour chaque VLAN avec :

* Adresse IP virtuelle (.1)

* Activation du mode preempt

* Priorité 150 sur Sw1 (Actif)
```bash
interface Vlan10
 ip address 192.168.10.2 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 150
 standby 10 preempt
 standby 10 name VLAN10-GW
no  ip helper-address 192.168.10.4
no sh

interface Vlan20
 ip address 192.168.20.2 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 150
 standby 20 preempt
 standby 20 name VLAN20-GW
 ip helper-address 192.168.10.4
no sh

interface Vlan30
 ip address 192.168.30.2 255.255.255.0
 standby 30 ip 192.168.30.1
 standby 30 priority 150
 standby 30 preempt
 standby 30 name VLAN30-GW
 ip helper-address 192.168.10.4
no sh

interface Vlan40
 ip address 192.168.40.2 255.255.255.0
 standby 40 ip 192.168.40.1
 standby 40 priority 150
 standby 40 preempt
 standby 40 name VLAN40-GW
 ip helper-address 192.168.10.4 
no sh
```
* Priorité 100 sur Sw2 (Secours)
```bash  
interface Vlan10
 ip address 192.168.10.3 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 100
 standby 10 preempt
 standby 10 name VLAN10-GW
 ip helper-address 192.168.10.4
 no shutdown

interface Vlan20
 ip address 192.168.20.3 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 100
 standby 20 preempt
 standby 20 name VLAN20-GW
 ip helper-address 192.168.10.4
 no shutdown

interface Vlan30
 ip address 192.168.30.3 255.255.255.0
 standby 30 ip 192.168.30.1
 standby 30 priority 100
 standby 30 preempt
 standby 30 name VLAN30-GW
 ip helper-address 192.168.10.4
 no shutdown

interface Vlan40
 ip address 192.168.40.3 255.255.255.0
 standby 40 ip 192.168.40.1
 standby 40 priority 100
 standby 40 preempt
 standby 40 name VLAN40-GW
 ip helper-address 192.168.10.4
 no shutdown
```
🔹 Scénario de Test

Simulation effectuée :

* Arrêt du switch actif (Sw1)

* Basculement automatique vers Sw2

#  🌐 Routage Dynamique – OSPF

Configuration :

Process ID : 1

Area : 0 (Backbone)

Interfaces VLAN en passive-interface

Liaisons point-à-point redondantes

Injection de la route par défaut :
```bash
router ospf 1
 router-id 2.2.2.2
 network 192.168.10.0 0.0.0.255 area 0
 network 192.168.20.0 0.0.0.255 area 0
 network 192.168.30.0 0.0.0.255 area 0
 network 192.168.40.0 0.0.0.255 area 0
 passive-interface Vlan10
 passive-interface Vlan20
 passive-interface Vlan30
 passive-interface Vlan40
 network 10.10.12.0 0.0.0.3 area 0   
 network 10.10.11.0 0.0.0.3 area 0  
 network 1.1.3.0 0.0.0.3 area 0
default-information originate always
```
🔹 Objectifs

* Convergence rapide

* Architecture évolutive

* Standard entreprise

* Maintien de la passerelle

* Perte de paquets minimale

# 📊 Supervision Réseau – Zabbix

<img width="1022" height="732" alt="dash" src="https://github.com/user-attachments/assets/32c8b59a-1c53-451e-b0f6-fc4e3c7bde27" /> 

Supervision via SNMPv2 configuré sur les deux équipements Distribution.

🔎 Paramètres supervisés :

État des interfaces

Charge CPU

État des voisins OSPF

État HSRP (Active/Standby)

Événements Link Up / Link Down

🔔 Traps activés :

SNMP

OSPF state-change

HSRP

Link down/up

Zabbix génère des alertes en cas de basculement ou d’anomalie réseau.
