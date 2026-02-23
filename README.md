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

* Priorité 150 sur Sw1 (Actif)

* Priorité 100 sur Sw2 (Secours)

* Activation du mode preempt

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
