---
title: "Créer le volume de stockage"
excerpt: "Ajouter un volume, son disque virtuel pour aboutir à un CSV de cluster S2D."
classes: wide
---


```powershell
# Example: With 2 or 3 servers
# If your deployment has only two servers, Storage Spaces Direct will automatically use two-way mirroring
New-Volume -FriendlyName "Volume1" -AllocationUnitSize 65536 -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size xTB
# Example: With 4+ servers
# 3-Way Mirroring
New-Volume -FriendlyName "Volume1" -AllocationUnitSize 65536 -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size xTB -ResiliencySettingName Mirror
# 3-Way Parity
New-Volume -FriendlyName "Volume1" -AllocationUnitSize 65536 -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size xTB -ResiliencySettingName Parity
```

> ⚠️ Notez que la taille d'unité d'allocation a 64KB est un best practice MS pour les besoins Hyper-V et particulièrement si vous renoncez au ReFS au profit du NTFS (avec une taille d'unité à 4KB le volume se limiterait à 16TB )

> ℹ️ **A mon sens**:
Utilisez une typologie de type Mirror pour de la production, réservez le Parity pour du stockage pure (Franchement ça rame !).
Personnellement, je n'aime pas le tiering parce qu'il est difficile d'apréhender la ratio Perf/Capa.

## Renommer le Virtual disk

A titre purement instructif, renommez les divers composants du CSV.

```powershell
Get-VirtualDisk Volume1 | Set-VirtualDisk -NewFriendlyName CSV01
(Get-ClusterSharedVolume -Name *Volume1*)[0].Name = 'Cluster Virtual Disk (CSV01)'
Set-Volume -FileSystemLabel Volume1 -NewFileSystemLabel CSV01
```

Avec ceci, vous allez obtenir:
- Un point de montage dans C:\ÇlusterShardVolume nommé Volume1
- Un disque virtuel nommé 'CSV01'
- Un Volume Cluster Partagé (CSV) nommé 'Cluster Virtual Disk (CSV01)'
- Un Volume "classique" dont le label est CSV01

![404 image]({{ site.url }}{{ site.baseurl }}/assets/images/volumes-in-smapi.png)