---
title: "[Jeedom] migration Aeotec z-stick 700"
excerpt: |
  Aeotec z-stick 700, une migration compliquée.

category: Jeedom
classes: wide
comments: true
tags: 
  - VMWare  
  - Esxi
  - Jeedom
  - zwave
  - aetoec
---

## Préambule

Dans cet article je vais m'efforcer de vous décrire le cheminement dont j'ai fait l'objet pour réussir la migration de ma clé Aeotec z-stick gen5+ ver la nouvelle z-stick 700 de la marque.

## Théorie

Je ne vais pas décrire en détail les étapes théoriques, mais voici le principe de base qui fonctionne parfaitement pour 90% des utilisateurs.

Situation initiale: vous utilisez une clé gen5+

A l'aide du Dashboard zwavejsui, vous faites un backup du NVM de la clé.

Toujours à l'aide de zwavejsui, vous restaurez ce backup sur la nouvelle

Situation finale: vous utilisez la nouvelle clé.

## Pratique

Situation initiale: vous utilisez une clé gen5+

A l'aide du Dashboard zwavejsui, vous tentez un backup du NVM de la clé. Vous obtenez un message d'erreur : _Timeout while waiting for a response from the controller (ZW0200)_

> Pas de backup -> pas de migration

## Tentatives

De facon sommaire voici les essais infructueux, autres que debrancher/rebrancher la clé, essayer un autre port usb, etc...

### Premier essai

Utilisation de l'outil de backup fourni par Aeotec pour faire un backup.

Factory reset de la clé.

Restauration de la clé avec le même outil

-> __Echec__

### Deuxième essai

Migration de Jeedom vers Debian Bullseye a l'aide de l'ISO fourni par Jeedom

### Troisième essai

Création d'une VM __from scratch__ à l'aide de l'ISO Debian.

Installation des composants zwavejs après vois ajouté cette ligne dans /etc/apt/sources.list

_deb http://deb.debian.org/debian bullseye main contrib non-free_

```sh
sudo apt-get update
sudo apt-get install snapd
sudo snap install core
sudo snap install zwave-js-ui
sudo snap connect zwave-js-ui:raw-usb
sudo snap connect zwave-js-ui:hardware-observe
sudo zwave-js-ui.enable
```

(J’ai également configuré le port ainsi que les divers Id s0, s2...)

-> __Echec__

### Quatrieme essai

Backup de la clé avec l'outil Aeotec

Restauration avec zwavejsui

Cela semblait hasardeux, mais pas tant que ça. Zwavejsui a des processus de validation de compatibilité.

Il a donc fait une conversion (bon départ), puis la restauration.

Mais aucun nœud n’a été restauré.

-> __Echec__

## Résolution

Avant d'aller plus loin, il faut que je vous décrive un peu mon setup.

### Contexte

Mon Jeedom fonctionne sur une VM depuis près de 2 ans.

C'est une VM créé depuis l'ISO fourni par l'équipe Jeedom SAS sur un ESXi 7.0.0 en licence gratuite lequel est installé sur un NUC NUC8i5BEH/NUC8i5BEK (l'un des rares avec 4 coeurs)

Coté matériel virtualisé, le contrôleur USB est en 2.0, le 3.1 étant indiqué par VMware comme non prise en charge par le SE invité.

La clé Gen5+ dispose du dernier firmware en date: 1.2

Notez que je n'ai noté aucun dysfonctionnement sur mon installation hormis que les versions récentes de zwavejs m'imposent de désactiver le soft reset de la clé sans quoi j'ai un message d'erreur _Driver non initialisé_.

### Mise à jour d'ESXi

La VM Jeedom étant à jour, le firmware de la clé également, il ne reste que la partie VMware.

Il faut savoir que j'ai toujours reculé l'échéance pour mettre à jour VMware. En effet, la carte réseau orienté bureautique n'était plus prise en charge par VMware dans les versions plus récentes.

[virten.net](https://www.virten.net/2021/03/esxi-7-0-update-2-on-intel-nuc/) indiquait alors une procédure pour customiser un ISO, mais pour des raisons qui m'échappent, il y a un an je n'y arrivais pas.

D'ailleurs je l'ai fait hier soir et cela fonctionne très bien. Je vous donne le lien vers flings encore disponible sur [Archive.org](https://archive.org/download/flings.vmware.com/Flings/Community%20Networking%20Driver%20for%20ESXi/_) car il a été retiré il a deux semaines des plateformes VMware...

Et vous devrez suivre également [cet article]{https://www.virten.net/2022/12/how-to-install-powercli-13-with-python-3-7-on-windows-required-for-imagebuilder/}), c'est assez long mais obligatoire.

Bref, revenons-en aux faits. En parcourant la toile à la recherche d'insipration je suis tombé sur [cette page](https://williamlam.com/2022/09/homelab-considerations-for-vsphere-8.html), ou il est stipulé que ESXi 8.0 est installable sur un NUC de 8e génération (et pas seulement). Rien n'indique qu'il y a une anomalie à gérer concernant la carte réseau.

Ainsi, cette nuit, j'ai entrepris la __grande migration__.

A l'aide de rufus, et après avoir téléchargé l'ISO ESXi 8.0u2, j'ai créé la clé USB d'installation ESXi.

Au préalable j'ai exporté les VM en production. (Pour rien en fin de compte).

A l'amorçage de la clé, quand le menu vous le propose, appuyez sur les touches shift+O et ajoutez "allowLegacyCPU=true" (sont concernés les NUC de génération 5 à 8).

Ensuite, le système vous demande si vous souhaitez migrer, installez en préservant le vmfs de stockage, ou tout péter.

J'ai choisi la première option.

Une fois toutes les étapes franchies, ESXi était disponible et en ligne sur le réseau !

A ce stade, j'ai désactivé le TPM qui est incorrectement reconnu par vmware et démarré les VM.

J'ai tenté le backup via zwavejsui.

-> __Echec__

> oooooh ce n’est pas vrai ! tout ça, pour ça ??

Attendez ne partez pas.

Quitte à avoir un système tout neuf capable de prendre en charge Windows et Debian dans leur version 12 (Ce qui n'est pas inutile), j'ai pris le temps de mettre à jour la compatibilité des VM pour les faire adhérer au nouveau système de virtualisation.

Puis, j'ai ajouté un contrôleur USB 3.1 (en plus du 2.0 déjà présent), ce qui n'était pas possible tant que la VM était dans un mode de compatibilité 7.0.

> Démarrage de la VM... tentative de backup du NVM et boom ! Backup Réussi !

Dès lors que le backup est fait, la suite est facile. Je remplace la clé par la nouvelle (en configurant le port), je mets à jour son firmware (tant qu'à faire) et je restaure le backup.

Les nœuds sont immédiatement remontés dans l'interface.

## Bonus

En migrant sur ESXi 8.0u2 j'ai également gagné en performance

Si j'en crois les performances indiqué dans la page santé de Jeedom, les latences ont été divisées par deux.

|Nom|Temps|
| :-------------------- |---------------------:|
|cache_write_5000|0.20650196075439|
|cache_read_5000|0.05158805847168|
|database_write_delete_1000|0.13969802856445|
|database_update_1000|0.079020977020264|
|database_replace_1000|0.071662187576294|
|database_read_50000|0.010976076126099|
|subprocess_200|0.097031116485596|
|total|0.65647840499878|

# Conclusion

J'ignore si c'est l'ajout du contrôleur USB3.1, sachant que la clé est USB 2.0.

J'ignore si c'est la migration ESXi 8.0u2

J'ignore si c'est un coup de chance, après avoir fait 30 essais.

Voici ce que je retiens, les NUC sont très bien pris en charge par ESXi 8 dans des conditions de Lab.

Les performances sont accrues et le dialogue USB amélioré.

Non seulement cela règle les problèmes de compatibilités entre les versions récentes de zwavejs et la gen5+ mais cela permet la migration vers la nouvelle Gen7.

C'est tout pour aujourd'ui j'épère que cet article en aidera plus d'un.
