---
title: "Fonctionnalités"
excerpt: "Les prérequis pour concevoir un cluster S2D."
classes: wide
---

## Ajout des fonctionnalités Windows

```powershell
Install Windows Features  
 Install-WindowsFeature -Name 'File-Services'
 Install-WindowsFeature -Name 'Data-Center-Bridging'
 Install-WindowsFeature -Name 'FS-SMBBW'
 Install-WindowsFeature -Name 'Failover-Clustering' -IncludeManagementTools
 Install-WindowsFeature -Name 'Hyper-V' -IncludeManagementTools -Restart
```

FS-SMBBW n'est pas obligatoire, mais je le conseille.

En l’état il vous manquera une fonctionnalité si vous souhaitez faire du réplicat de stockage.