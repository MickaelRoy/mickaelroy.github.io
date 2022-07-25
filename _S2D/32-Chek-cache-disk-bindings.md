---
title: "Vérifier les liaisons des disques de cache"
excerpt: "Verifier les "bindings" des disques de caches S2D."
category: S2D
classes: wide
tags: 
  - PowerShell
  - S2D
  - Support
  - Cache
  - Get-StorageBusCacheReport
header:
  teaser: /assets/images/2022-07-25-Chek-cache-disk-bindings.webp
---

## Ce que dit Darryl (van der Peijl)

Oui, c'est curieux, comme intro...

> Storage Spaces Direct peut utiliser des disques de cache si vous avez fourni des SSD ou des SSD NVMe dans vos nœuds. Normalement, les disques de capacité sont liés aux disques de cache à tour de rôle, voir la documentation officielle de Microsoft ici.

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



## A quoi cela ressemble-t-il ?

Pour être concret, la cmdlet va retourner un resultat semblable a celui-ci pour chaqun des noeuds du cluster.

Si des disques sont _unbound_ ou le ration non _even_ il sera alors temps de lancer la cmdlet _Repair-ClusrerS2D -RecoerUnboundDrives_

```
╒═══════════════╕
│ Node: CLUS001 │
╘═══════════════╛

DiskState           DiskId                                 DeviceNumber CacheDeviceNumber HasSeekPenalty PathId BindingAttributes DirtyPages
---------           ------                                 ------------ ----------------- -------------- ------ ----------------- ----------
InitializedAndBound {fe67b448-b652-0693-babe-bca0ebb6faf6}            1 = cache           false          22     0                 0         
InitializedAndBound {86cd8423-8778-3851-3f71-907b9ec71b3e}            2 = cache           false          11     0                 0         
InitializedAndBound {0e148d53-717b-897e-524d-8c58332d954a}            3 1                 false          56     23                1308126   
InitializedAndBound {65583c1a-aa4f-17ab-689c-aa34807b55d5}            4 2                 false          36     23                1391641   
InitializedAndBound {c770b738-32d9-4584-54a0-c1bb836ec257}            5 2                 false          38     23                1213649   
InitializedAndBound {fc594d3d-9505-91aa-1e2d-44d99882fc11}            6 1                 false          64     23                1448332   
InitializedAndBound {297defab-838e-fcfe-090a-322dac4fe0d6}            7 2                 false          46     23                1462796   
InitializedAndBound {f132317c-b4e7-72ac-275a-cd52c1b54274}            8 1                 false          58     23                1374992   
InitializedAndBound {50aa4b2a-9150-463e-7b4c-fa70ff8f9dcf}            9 2                 false          32     23                1489383   
InitializedAndBound {16005fbe-36d6-b567-8e08-f6d31a124a6f}           10 1                 false          52     23                1457775   
InitializedAndBound {a72aacb6-1ea5-90d7-f22f-6590a8548dfc}           11 2                 false          42     23                1479399   
InitializedAndBound {adf1e9f2-2270-8b2b-7def-0c28c27ecf10}           12 1                 false          54     23                1517967   
InitializedAndBound {cba59f55-1d79-86f5-0fac-81dd14c3bdf1}           13 2                 false          34     23                1354746   
InitializedAndBound {e15e7d5a-0928-f55a-3e40-473c553a0f9a}           14 2                 false          40     23                1281865   
InitializedAndBound {5cfd0ce0-cfe1-b909-0838-8b8ecac7e2eb}           15 1                 false          62     23                1382768   
InitializedAndBound {f9177849-762a-1009-fb09-5432820cdc74}           16 1                 false          66     23                1391490   
InitializedAndBound {c9f1d909-ba1c-94fb-1391-78f6c3cb3f3c}           17 1                 false          60     23                1327518   
InitializedAndBound {d33a901e-7e65-44b3-daa8-80fd93906dee}           18 2                 false          30     23                1485054   
InitializedAndBound {257416f5-8b47-cc26-d890-332224fa7c69}           19 1                 false          48     23                1842391   
InitializedAndBound {479eb0f1-8d4c-d03a-8767-7645f9db060c}           20 2                 false          28     23                1859124   
InitializedAndBound {21c8312d-f63f-55b6-7b42-806a46ff892e}           21 2                 false          44     23                1909617   
InitializedAndBound {b8b8e56f-66dd-fbb4-ef7e-ccf39a3f6cc9}           22 1                 false          50     23                1899861   


Device counts: 
	Cache: 2 
	Capacity: 20
Binding ratio is even: 1:10
All disks are in InitializedAndBound
```

## Conclusion

Un script qui ne date pas d'hier mais trop méconnu.

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Get-CacheDiskStatus){: .btn .btn--info}

Ca c'est cadeau !

A plus !