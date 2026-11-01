# Lab 02 — Segmentation avec une DMZ (pfSense)

## Objectif
Créer une troisième zone, la **DMZ**, pour héberger des serveurs exposés, et appliquer la règle d'or : **la DMZ ne doit jamais pouvoir initier de connexion vers le LAN**.

## Topologie
```
                 NAT GNS3 ─ Switch-WAN ─ em0/WAN
pfSense  em1/LAN  = 192.168.1.1/24  ─ Switch-LAN ─ postes LAN
         em2/DMZ  = 172.16.1.1/24   ─ Switch-DMZ ─ VPCS .10 + serveur web .11
```
L'interface OPT1 (em2) est activée et renommée **DMZ**.

## Matrice de flux

| Source → Destination | Action | Raison |
|----------------------|--------|--------|
| LAN → DMZ | Autorisé | Les utilisateurs accèdent aux serveurs |
| DMZ → Internet | Autorisé | Mises à jour, résolution DNS (durci au lab 03) |
| **DMZ → LAN** | **Bloqué** | Un serveur compromis ne doit pas atteindre le LAN |

## Validation
Trois tests croisés (LAN → DMZ, DMZ → Internet, DMZ → LAN) avec lecture des logs pour le flux bloqué.

## Parallèle FortiGate
Sur FortiGate, l'absence de politique DMZ → LAN suffit : l'**implicit deny** bloque le flux. Voir [FortiGate — fondamentaux](../../fortigate/fondamentaux/).

> Fiche PDF détaillée : à ajouter.
