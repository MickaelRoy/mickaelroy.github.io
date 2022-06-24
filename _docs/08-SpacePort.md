---
title: "Time-Out SpacePort"
excerpt: "Optimiser le Timeout du service SpacePort pour concevoir un cluster S2D."
classes: wide
---

## Changement du Timeout pour le service SpacePort

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\spaceport\Parameters -Name HwTimeout -Value 0x00002710
```

Pour optimiser les performances et la fiabilité, mettez à jour la configuration du délai d'expiration du matériel pour le service SpacePort. (Dell Pr;econise 0x00002710 ou 10sec)

Cela donne une plus grande résilience à une éventuelle latence de stockage VHD / VHDX / VMDK dans les clusters invités, augmente la valeur du délai d'attente d'E/S des espaces de stockage (MS Preconise 0x00007530 ou 30sec)

> ℹ️ La valeur par défaut est 0x1770 ou 6sec