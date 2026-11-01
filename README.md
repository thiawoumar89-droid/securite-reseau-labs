# Sécurité réseau en lab — pfSense & FortiGate

Parcours pratique en sécurité réseau, entièrement réalisé dans **GNS3** : du firewall stateful jusqu'au **VPN IPsec** et au **SD-WAN**, d'abord sur **pfSense** (libre), puis sur **FortiGate** (le firewall le plus répandu en entreprise). Chaque lab est documenté : objectif, topologie, configuration commentée, tests de validation, pannes réellement rencontrées et parallèle entre les deux constructeurs.

*Hands-on network security labs on GNS3 (pfSense & FortiGate): stateful firewall, DMZ, NAT, IPsec VPN, SD-WAN — with commented configurations, validation tests and real troubleshooting.*

## Sommaire

| # | Lab | Plateforme | Notions | Statut |
|---|-----|------------|---------|--------|
| 00 | [Installer pfSense dans GNS3](pfsense/00-installation/) | pfSense CE 2.9.0 | Installateur Netgate, interfaces, pièges d'installation | ✅ |
| 01 | [Règles stateful et default deny](pfsense/lab01-stateful/) | pfSense | Stateful, state table, ordre des règles, logs | ✅ |
| 02 | [Segmentation avec une DMZ](pfsense/lab02-dmz/) | pfSense | 3 zones, DMZ → LAN bloqué | ✅ |
| 03 | [Durcissement et NAT entrant](pfsense/lab03-durcissement-nat/) | pfSense | Least privilege, alias, port forwarding | ✅ |
| 04 | [VPN IPsec site-à-site](pfsense/lab04-ipsec-site-a-site/) | pfSense ↔ pfSense | IKEv2, phases 1/2, SA, PFS, dépannage | ✅ |
| — | [Installer FortiGate VM dans GNS3](fortigate/00-installation/) | FortiOS 7.4.12 | Image KVM, licence d'essai permanente | ✅ |
| — | [FortiGate — fondamentaux](fortigate/fondamentaux/) | FortiGate | Implicit deny, sessions, DMZ, VIP | ✅ |
| 05 | [SD-WAN sur FortiGate](fortigate/lab05-sdwan/) | FortiGate + Cisco 7200 | Membres, SLA, failover, règles | ✅ |
| 06 | IPsec FortiGate ↔ FortiGate | FortiGate | Route-based, phase1-interface | 🔜 |
| 07 | IPsec hybride pfSense ↔ AWS | pfSense + AWS | Site-to-Site VPN, NAT-T | 🔜 |

## Environnement

- **GNS3** + **GNS3 VM** sous VMware Workstation
- **pfSense CE 2.9.0** (Netgate Installer)
- **FortiGate VM 7.4.12** (KVM), licence d'essai permanente
- **Cisco 7200** (Dynamips) pour simuler un second opérateur
- **VPCS** et **Webterm** comme postes de test

### Plan d'adressage commun

| Zone | Réseau | Passerelle |
|------|--------|------------|
| WAN (NAT GNS3) | 192.168.122.0/24 | 192.168.122.1 |
| LAN site A | 192.168.1.0/24 | 192.168.1.1 |
| DMZ | 172.16.1.0/24 | 172.16.1.1 |
| LAN site B (IPsec) | 10.10.10.0/24 | 10.10.10.1 |
| WAN2 / opérateur 2 (CGNAT) | 100.64.2.0/30 | 100.64.2.2 |

## Contraintes de la licence d'essai FortiGate

Depuis FortiOS 7.2.1, la licence d'essai est **permanente**, mais limitée : 1 vCPU, 2 Go de RAM, **3 interfaces, 3 politiques, 3 routes**, chiffrement faible (DES) et pas de services FortiGuard. Les labs FortiGate sont conçus pour tenir dans ce budget. Conséquence notable : un tunnel IPsec entre la FortiGate d'essai et pfSense (ou AWS) est impossible, faute d'algorithme commun.

## Méthode

Chaque lab suit la même démarche : comprendre le *pourquoi*, configurer, **prouver par des tests** (pings, captures, logs, tables de session et de routage), puis provoquer des pannes pour apprendre à les diagnostiquer.

## Sécurité du dépôt

Aucune donnée sensible n'est publiée : pas de clé pré-partagée, de mot de passe, de numéro de série ni de fichier de configuration complet. Les configurations sont des extraits nettoyés ; les secrets sont remplacés par des `<espaces-réservés>`.

## Auteur

**Oumar Thiaw** — ingénieur réseaux & télécoms. Série vidéo associée en préparation.
