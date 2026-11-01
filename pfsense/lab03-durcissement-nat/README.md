# Lab 03 — Durcissement de la DMZ et NAT entrant (pfSense)

## Objectif
1. Appliquer le **moindre privilège** aux flux sortants de la DMZ.
2. Publier un serveur web de la DMZ sur Internet par **NAT entrant** (port forwarding).

## Durcissement : least privilege
- Alias de ports **`web_ports`** = 80, 443, 53.
- Règle DMZ → any limitée à `web_ports` : le ping (ICMP) depuis la DMZ est désormais bloqué, preuve que seul le nécessaire passe.

## NAT entrant
- **Port forward** : WAN:80 → 172.16.1.11:80, avec la règle de filtrage associée.
- Serveur web minimal sur un Webterm :
  ```sh
  printf 'HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\n\r\nDMZ server OK\n' > /tmp/page.txt
  socat TCP-LISTEN:80,fork,reuseaddr SYSTEM:"cat /tmp/page.txt"
  ```
- Test depuis un client placé côté WAN : la page « DMZ server OK » s'affiche.

## Dépannage réalisé
| Symptôme | Cause | Correction |
|----------|-------|------------|
| Client WAN bloqué | « Block private networks » actif sur le WAN (le lab utilise des adresses privées côté WAN) | Décocher l'option sur le WAN |
| Compteur **States 0/0** sur la règle | La règle n'est jamais atteinte | Vérifier destination et port |
| Page injoignable | Test sur le port externe 8080 au lieu de 80 | Port externe ≠ port interne : tester le bon port |

Outils utilisés : Diagnostics → Test Port, Ping, Packet Capture, System Logs.

## Notion clé
NAT entrant = **DNAT + règle de filtrage**. Sur pfSense, le filtrage s'applique **après** la NAT : la règle vise l'**IP interne**.

## Parallèle FortiGate
Une **VIP** porte la traduction, et la politique vise l'**objet VIP** (NAT de la politique désactivée). Voir [FortiGate — fondamentaux](../../fortigate/fondamentaux/).

> Fiche PDF détaillée : à ajouter.
