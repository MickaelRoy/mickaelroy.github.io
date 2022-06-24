---
title: "Renommer les réseaux de cluster"
excerpt: "Pour plus de lisibilité, renommer les réseaux d'un cluster S2D."
classes: wide
---

> Pour plus de lisibilité !

Ici on renomme les réseaux pour plus de clareté dans la console failover-cluster 

```powershell
(Get-ClusterNetwork -Cluster $CNO | ? Role -eq ClusterAndClient).name = "Management"
(Get-ClusterNetwork -Cluster $CNO | ? Role -ne ClusterAndClient).name = "SMB"
```
