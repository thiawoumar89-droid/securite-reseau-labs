# Lab 01 — Règles stateful et default deny (pfSense)

## Objectif
Comprendre le fonctionnement **stateful** d'un firewall et le **refus par défaut**, puis les prouver par des tests et la lecture des logs.

## Topologie
```
NAT GNS3 ─ Switch-WAN ─ em0/WAN [pfSense] em1/LAN 192.168.1.1/24 ─ Switch-LAN ─ VPCS + Webterm (192.168.1.x)
```

## Ce qui a été fait
- Lecture des règles LAN par défaut (*Default allow LAN to any*) et du refus implicite sur le WAN.
- Création d'une règle **Block ICMP LAN → any**, placée **au-dessus** de la règle d'autorisation : les règles pfSense sont évaluées de haut en bas, la première qui correspond s'applique.
- Test : le ping depuis le LAN est bloqué, le reste du trafic passe.
- Vérification dans **Status → System Logs → Firewall** (règle cochée « Log packets ») et dans **Diagnostics → States**.

## Notions clés
- **Stateful** : seule l'ouverture de la connexion est évaluée par les règles ; les réponses correspondent à un état (*state*) et reviennent sans règle inverse.
- **Default deny** : tout ce qui n'est pas explicitement autorisé est refusé.
- **Ordre des règles** : première règle qui correspond.

## Parallèle FortiGate
Politiques évaluées de haut en bas, **implicit deny** (politique 0) non journalisé par défaut, table des **sessions** (`diagnose sys session list`). Voir [FortiGate — fondamentaux](../../fortigate/fondamentaux/).

> Fiche PDF détaillée : à ajouter.
