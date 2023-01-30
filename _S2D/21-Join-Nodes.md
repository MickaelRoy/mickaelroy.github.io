---
title: "Joindre les noeuds au cluster"
excerpt: "Joindre tous les noeuds afin de créer un cluster S2D."
classes: wide
---

Le Cmdlet nécessite que vous ayez les droits de création d'objet Computer sur l'AD.
Si vous êtes dans un contexte restreint, vous pouvez/devez le faire [pré-provisionner][MsPreStage].

```powershell
#Tell to powershell to clusterize all nodes for you
New-Cluster -Name $CNO -Node $nodes -NoStorage -staticAddress [CNOIPAddress]
```

> ℹ️ L'ip du CNO doit être dans le même VLAN que la vNic de management.

[MsPreStage]: https://learn.microsoft.com/windows-server/failover-clustering/prestage-cluster-adds