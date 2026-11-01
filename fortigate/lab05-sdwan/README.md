# Lab 05 — SD-WAN sur FortiGate

Deux opérateurs, un **SLA de performance**, un **failover** automatique et des **règles d'aiguillage**, dans le budget de la licence d'essai (3 interfaces, 1 politique, 1 route).

📄 Fiche complète : [docs/Lab_Securite_5_SDWAN_FortiGate.pdf](docs/Lab_Securite_5_SDWAN_FortiGate.pdf) · ⚙️ [configs/fortigate-sdwan.txt](configs/fortigate-sdwan.txt) · [configs/isp2-cisco7200.txt](configs/isp2-cisco7200.txt)

## Topologie
```
                     NAT GNS3 (192.168.122.1)
                         |
                      SW-WAN ---------------- ISP2 (Cisco 7200) Fa0/0 = 192.168.122.13
                         |                     ISP2 Fa1/0 = 100.64.2.2/30
   port1 WAN1 = 192.168.122.21/24                    |
                   [ FGT-1 ] ------------------------+ port3 WAN2 = 100.64.2.1/30
   port2 LAN  = 192.168.1.1/24 - SW-LAN - postes
```
Le second opérateur est simulé par un routeur Cisco 7200 qui fait du PAT ; le lien utilise la plage **CGNAT 100.64.0.0/10**, comme une box derrière le NAT d'un FAI.

## Briques SD-WAN

| Brique | Rôle | Équivalent pfSense |
|--------|------|--------------------|
| Membres | port1 (gw 192.168.122.1), port3 (gw 100.64.2.2) | Passerelles |
| Zone `virtual-wan-link` | Une seule « interface » pour la route et la politique | Groupe de passerelles |
| SLA `SLA-Internet` | Ping 8.8.8.8 / 500 ms ; mort après 3 échecs ; seuils 150 ms, 50 ms, 5 % | dpinger |
| Règles | 1.1.1.1 → WAN2 (manuel) ; reste → WAN1 tant qu'il respecte le SLA | Policy routing |

## Tests validés

| Test | Résultat |
|------|----------|
| Failover : câble WAN1 suspendu (interface toujours *up*) | 2-3 pings perdus, route retirée, trafic sur WAN2 |
| Retour du lien | *alive* après 3 sondes réussies (anti-flapping) |
| Trafic vers 1.1.1.1 | Toujours par WAN2 (source 100.64.2.1) |
| Trafic vers 9.9.9.9 | Par WAN1 (source 192.168.122.21) |
| Délai de 200 ms sur WAN1 | WAN1 vivant mais hors SLA → bascule sur WAN2 |

## Pièges
- Une interface ne devient membre SD-WAN que si **rien ne la référence** (politique, VIP, objet d'adresse auto-créé) : `diagnose sys cmdb refcnt show system.interface.name port3`.
- En statique, configurer le DNS (`config system dns`).
- Tester vers une autre cible que le serveur du SLA, sinon sondes et trafic se mélangent.
- Le lien est choisi à la création de la session : relancer le ping après un changement.
- Cisco 7200 Dynamips : régler l'**Idle-PC**.

## Aide-mémoire
```
diagnose sys sdwan member
diagnose sys sdwan health-check
diagnose sys sdwan service
get router info routing-table all
diagnose sniffer packet any 'icmp and host 1.1.1.1' 4
```
