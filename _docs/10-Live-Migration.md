---
title: "Live Migration"
excerpt: "Configurer la migration dynamique pour concevoir un cluster S2D."
classes: wide
---

```powershell
Set-VMHost -MaximumVirtualMachineMigrations 2
```

Mieux vaut 2 machines qui bougent vite, que 10 qui se trainent.

> 💡 On sera amené à rediscuter le point Live Migration en [fin de tuto]({{ "/doc/>>>/" | relative_url }})

```powershell
If ((Get-NetAdapter -Physical | Group-Object speed | ? Count -eq 2).Name -le 10000000000 ) {
    Set-VMHost –VirtualMachineMigrationPerformanceOption Compression
} Else {
    Set-VMHost –VirtualMachineMigrationPerformanceOption SMB
}
```

> ℹ️ En dessous de 10Gbs, MS suggère la compression pour le live migration.