---
title: "Vérifier les liaisons des disques de cache"
excerpt: "Verifier les 'bindings' des disques de caches S2D."
classes: wide
---
## Ce que dit Darryl (van der Peijl)

Oui, c'est curieux, comme intro...

> Storage Spaces Direct peut utiliser des disques de cache si vous avez fourni des SSD ou des SSD NVMe dans vos nœuds. Normalement, les disques de capacité sont liés aux disques de cache à tour de rôle, voir la documentation officielle de Microsoft [ici](https://docs.microsoft.com/fr-fr/azure-stack/hci/concepts/cache).

> Si vous soupçonnez ou constatez qu'un nœud n'obtient pas les bons chiffres de performance, vous pouvez vous demander si vos périphériques de cache sont utilisés correctement. La bonne façon de vérifier cela est d'exécuter un Get-Clusterlog sur votre nœud qui fournit un journal de tous les composants du cluster, y compris les périphériques de cache. Dans le fichier, faites défiler jusqu'à la section "[=== SBL Disks ===]"
