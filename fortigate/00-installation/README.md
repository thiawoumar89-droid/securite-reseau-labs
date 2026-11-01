# Installer FortiGate VM dans GNS3 (licence d'essai permanente)

## Résultat
FortiGate VM **7.4.12 (build 2902, Mature)** dans GNS3, licence d'essai permanente **Valid**, interface graphique accessible depuis le PC.

## La licence d'essai permanente
Depuis FortiOS 7.2.1 : licence **sans expiration**, liée à un compte FortiCloud, **une seule par compte**. Limites : 1 vCPU, 2 Go de RAM, 3 interfaces / 3 politiques / 3 routes, chiffrement faible, pas de FortiGuard. Une licence invalide déjà chargée empêche de demander la licence d'essai : partir d'une image neuve.

## Étapes
1. **Compte** sur support.fortinet.com.
2. **Image** : Support → VM Images → FortiGate → **KVM** → 7.4.12 → fichier `FGT_VM64_KVM-v7.4.12-...-FORTINET.out.kvm.zip` (pas le `.out` seul). Dézipper, **renommer** `fortios.qcow2` en `fortios-7.4.12-kvm.qcow2`.
3. **GNS3** : New template → appliance FortiGate → installée sur la **GNS3 VM** → *Create a new version* → disque 1 = l'image, disque 2 = `empty30G.qcow2`.
4. **Template** : 2048 Mo, 1 vCPU, console telnet.
5. **Premier démarrage** : Ethernet0 (= port1) relié au NAT ; login `admin`, mot de passe vide, puis nouveau mot de passe. L'invite doit afficher `FortiGate-VM64-KVM`.
6. **Internet puis licence** :
   ```
   config system interface
       edit port1
           set mode dhcp
           set allowaccess ping http https ssh
       next
   end
   execute ping 8.8.8.8
   execute ping www.fortinet.com
   execute vm-license-options account-id <email-forticloud>
   execute vm-license-options account-password "<mot-de-passe>"
   execute vm-license
   get system status          # License Status: Valid
   ```
7. **GUI** : port2 en LAN (192.168.1.1/24) ; assistant : mises à jour automatiques **désactivées**, tableau de bord **Complet**.

### Accès à la GUI depuis le PC Windows
Nœud **Cloud** sur l'interface host-only de la GNS3 VM (ici `eth1`, à vérifier sur chaque machine), relié au switch LAN ; adresse secondaire 192.168.1.10/24 **sans passerelle** sur la carte VMnet1 du PC.

## Pièges rencontrés

| Symptôme | Cause | Correction |
|----------|-------|------------|
| Invite `FortiGate-VM64-ALI` et `Login incorrect` | Image **Alibaba Cloud** au lieu de KVM | Retélécharger en plateforme KVM |
| Ancienne image réutilisée | Même nom `fortios.qcow2` sur la GNS3 VM | Renommer chaque image |
| PC sans accès à la GUI | Cloud branché sur la mauvaise interface | Utiliser l'interface host-only |
| « Failed to download VM license » | Pas d'Internet, ou CPU | Vérifier les pings ; option `-cpu host` |
| Perte de connexion GUI | Session navigateur | Vérifier uptime/mémoire en console, rafraîchir |

🎬 Script vidéo de cet épisode : [videos/Episode9_Script_Installer_FortiGate_GNS3.pdf](../../videos/Episode9_Script_Installer_FortiGate_GNS3.pdf)
