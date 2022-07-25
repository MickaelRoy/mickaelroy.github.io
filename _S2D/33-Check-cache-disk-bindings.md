---
title: "Vérifier les liaisons des disques de cache"
excerpt: "Verifier les 'bindings' des disques de caches S2D."
classes: wide
---
## Ce que dit Darryl (van der Peijl)

Oui, c'est curieux, comme intro...

> Storage Spaces Direct peut utiliser des disques de cache si vous avez fourni des SSD ou des SSD NVMe dans vos nœuds. Normalement, les disques de capacité sont liés aux disques de cache à tour de rôle, voir la documentation officielle de Microsoft [ici](https://docs.microsoft.com/fr-fr/azure-stack/hci/concepts/cache).

> Si vous soupçonnez ou constatez qu'un nœud n'obtient pas les bons chiffres de performance, vous pouvez vous demander si vos périphériques de cache sont utilisés correctement. La bonne façon de vérifier cela est d'exécuter un Get-Clusterlog sur votre nœud qui fournit un journal de tous les composants du cluster, y compris les périphériques de cache. Dans le fichier, faites défiler jusqu'à la section "[=== SBL Disks ===]"

## Retour à la source

Darryl le dit lui même, il n'a rien inventé dans ce script, il a juste apporté sa touche personnelle et c'est très bien. c'est d'ailleurs mon tour d'y apporter des améliorations. 

[La source](https://github.com/PowerShell/PrivateCloud.DiagnosticInfo/blob/master/PrivateCloud.DiagnosticInfo/PrivateCloud.DiagnosticInfo.psm1) se trouve chez Microsoft rien que ça. Chez eux, la cmdlet se nomme "Get-StorageBusCacheReport"

## C'est très bien mais ca sert à quoi ?

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/dynamic-binding.gif" alt="">
  <figcaption>Les liaisons de lecteurs.</figcaption>
</figure>

Comme le montre cette figure (Merci MS), dans l'ideal et a moins que vous n'ayez désactivé le cache S2D, les disques de capacité sont liés à des disques de cache.

Il doit y avoir un ratio, entre le nombre de disques de capacité et le nombre de disques de cache.

> Exemple, 2 de cache, 8 de capacité donc un ratio de 1:4

De plus il faut s'assurer que les disques sont bien liés, il arrive parfois (souvent) que certains disques perdent cette liaison.