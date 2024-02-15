---
title:  "Get-WsusUpdates et un peu plus"
excerpt: |
  Get-WsusUpdates: Ou telecharger les patches directement depuis le wsus.
  
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - cmdlet
  - wsus
---

# Get-WsusUpdates

## Description

Dans des structures de grande ampleur, il est parfois utile de dialoguer avec le wsus pour connaitre l'état d'un client.

Plus utile encore: télécharger les cabs pour les installer hors-ligne et ainsi maitriser l'ordonnancement.

Cette cmdlet fera les deux selon le besoin.

## Plus de bla-bla, moins de demo

Ici, pas de demo. Mon lab n'est pas suffisant pour avoir un wsus.

Vous allez devoir me faire confiance.

L'idée de cette cmdlet est d'interroger le wsus (et non pas "le serveur wsus" puisque le dernier 's' veut dire server ndlr).

Celui retournera les mises à jours disponibles pour le client ainsi que le chemin http pour le télécharger.

La cmdlet utilisera alors **_BITS_** pour transférer les fichiers si cela lui est demandé.

Enfin, le cab pourra être installé avec DISM (ou son homologue powershell Add-Packages) ou wusa.exe.

Cas d'usage:
------------

Télécharger les packages pour chaque noeud d'un cluster une seule fois sur un partage commun: C:\ClusterStorage\Volume1.

Puis, les installer sur chaque noeud un à un pour éviter la perte de parité.

## Précautions à prendre.

La cmdlet est dessinée pour chiffrer les communications.

le port 8531 est celui utilisé par default pour communiquer avec wsus. Il faut donc que votre wsus dispose d'un certificat valide et soit configuré sur ce port, sinon c'est à vous de coder :)

## Et un peu plus ??

je vous laisse le loisir de découvrir Start-WUScan.

C'est une cmdlet qui déclenche une synchro entre le client et le wsus, un peu comme wauclt /reportnow, mais qui retourne les mises a jours correspondantes au critère de recherche.

Attention toutefois, la synchro n'est pas immediate, cela peut prendre un quart d'heure. Ce n'est donc pas parfait.



Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Windows-Update){: .btn .btn--info}
