---
title: "Interdir le réseau de management au live migration"
excerpt: "Reserver le trafic de migration dynamique sur le réseau adéquat d'un cluster S2D."
classes: wide
---

Le trafic du live migration n'a pas vocation à transiter par la carte de management, en particulier si vous avez réduit ses possibilités RSS.

```powershell 
Get-ClusterResourceType -Cluster $CNO -Name "Virtual Machine" | Set-ClusterParameter -Cluster $CNO -Name MigrationExcludeNetworks -Value ([String]::Join(";",(Get-ClusterNetwork -Cluster $CNO | Where-Object {$_.Name -eq "Management"}).ID))
```

Ce bout de code permet de selectionner tous les reseaux possible sauf le Management.

Bien entendu, il faut avoir [renommé les réseaux]({{ "/docs/23-Rename-Cluster-Network/" | relative_url }}) avant.

