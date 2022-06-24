---
title: "Joindre les noeuds au cluster"
excerpt: "Joindre tous les noeuds afin de créer un cluster S2D."
classes: wide
---

## Activer l'hyper-convergence

```powershell
Enable-ClusterS2D -Cim $CNO
```

Facile... ?

Dans un cas simple, ou les disques de cache sont by design plus rapide que ceux de stockage, c'est nickel.

> Oui mais, mois j'ai de la thune, et je suis en full flash NVME...


Microsoft l'explique très bien, il faut forcer le nodel des disques que vous souhaitez réserver au cache.

```PowerShell
Get-PhysicalDisk | Group Model -NoElement

Count Name
----- ----
    8 FABRIKAM NVME-1710
   16 CONTOSO NVME-1520
```

Then enter the following command, specifying the cache device model:

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

Vous pouvez vérifier que les lecteurs souhaités sont utilisés pour la mise en cache en exécutant Get-PhysicalDisk dans PowerShell et en vérifiant que leur propriété *Usage* indique *Journal*