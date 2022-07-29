---
title: "Augmenter la taille de block de cache du cluster"
excerpt: "Pour plus de lisibilité, renommer les réseaux d'un cluster S2D."
classes: wide
---

D'expérience, cela améliore grandement les performances d'écritures sur le CSV.
La valeur est purement arbitraire, je vous invite à faire des benches pour connaitre la valeur la plus adéquat.

```powershell
#Configuring CSV Cache
(Get-Cluster).BlockCacheSize = 8192
```

Si je ne dis pas de bétise, il est désactivé sur 2016, et à 1024 sur 2019.