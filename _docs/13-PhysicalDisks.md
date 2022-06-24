---
title: "Préparation des disques"
excerpt: "Préparer les disques en vue de les inclure à un cluster S2D."
classes: wide
---

> ❗Ce code est destructif❗

> __Ce que dis Intel:__
The drives intended to be used for S2D must be empty and without partitions or other data, or they will not be included in the S2D system.
Prepare the drives  

```powershell
# Execute on each node the code below
Update-StorageProviderCache
Get-StoragePool | ? IsPrimordial -eq $False | Set-StoragePool -IsReadOnly $False -ErrorAction SilentlyContinue
Get-StoragePool | ? IsPrimordial -eq $False | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False -ErrorAction SilentlyContinue
Get-StoragePool | ? IsPrimordial -eq $False | Get-VirtualDisk | Remove-StoragePool -Confirm:$False -ErrorAction SilentlyContinue
Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
Get-Disk| ? Number -ne $null| ? IsBoot -ne $true |? IsSystem -ne $true |? PartitionStyle -ne RAW | % {
    $_ | Set-Disk -IsOffline $False
    $_ | Set-Disk -IsReadOnly $False
    $_ | Clear-Disk -RemoveData -RemoveOEM -Confirm:$false
    $_ | Set-Disk -IsReadOnly $true
    $_ | Set-Disk -IsOffline $true
}
```

Bref. C’est une RAZ des disques. Ne le faite qu’une fois à la creation du cluster

## Vérification des disques

```powershell
$nodes  = 'nodea', 'nodeb', 'nodec', 'noded'
icm $nodes {
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

Afin de s'assurer que la préparation a fonctionné.