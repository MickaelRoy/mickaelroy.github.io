---
title: "Validation préliminaire du cluster"
excerpt: "Préparer les disque en vue de les inclure à un cluster S2D."
classes: wide
---

## Validation préliminaire du cluster

Dès lors que tous les nœuds héritent de la même configuration, on peut passer a la jonction du cluster en tant que tel.

Bien sur, vous vous êtes assurés que chaque vNic de chaque nœud peut joindre chaque vNic de tous les autres nœuds ?

Si ce n'est pas le cas, le Cmdlet Test-Cluster le fera pour vous.

Ouvrez une session sur l'un des nœuds, 

```powershell
$Nodes = 'nodea', 'nodeb', 'nodec', 'noded'
Test-Cluster -Node $nodes –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
```

Apres quelques minutes, un rapport sera généré et reflètera les déviances et autres anomalies.

Vous pouvez ignorer les warning (⚠️), mais pas les échecs (❌). 

Donc si c'est jaune, vous corrigerez plus tard, si c'est rouge, c'est mort.