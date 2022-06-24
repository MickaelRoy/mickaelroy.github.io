---
title: "Live Migration"
excerpt: "Configurer la migration dynamique pour concevoir un cluster S2D."
classes: wide
---

```powershell
Set-VMHost -MaximumVirtualMachineMigrations 2
```

Mieux vaut 2 machines qui bougent vite, que 10 qui se trainent.

> 💡 On sera amené à rediscuter le point Live Migration [Dans le chapitre Cluster]({{ "/docs/25-Live-Migration/" | relative_url }})

```powershell
If ((Get-NetAdapter -Physical | Group-Object speed | ? Count -eq 2).Name -le 10000000000 ) {
    Set-VMHost –VirtualMachineMigrationPerformanceOption Compression
} Else {
    Set-VMHost –VirtualMachineMigrationPerformanceOption SMB
}
```

> ℹ️ En dessous de 10Gbs, MS suggère la compression pour le live migration.


## Limiter la bande passante du live migration

Afin d'éviter une congestion réseau lors d'une migration dynamique, je préconise une limitation à deux VMs en mouvement simultané d'une part, mais également de limiter la bande passante utilisée par ce service.

Ceci garantit un déplacement invisible de la VM, en particulier si un Storage Job est en cours après un reboot par exemple.

Imaginez le scenario où le cluster est en cours de patching... La reconstruction du CSV et le déplacement des VMs sur leur nœud d'origine peuvent s'effectuer parallèlement ; Selon la quantité de RAM alloué à chaque VM, c'est une vrai tempête qui déferle sur les cartes réseaux.

```powershell
Set-SmbBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
```