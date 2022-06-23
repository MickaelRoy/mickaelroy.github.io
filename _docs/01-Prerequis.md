---
title: "Deployer un cluster S2D"
excerpt: "Toutes les étapes pour concevoir un cluster S2D aka Storage Spaces Direct."
toc: true
classes: wide
---

# Prérequis

## Prérequis Matériels

- Au moins deux serveurs identique tel que _Dell Powerdge r740 xd_
- Au moins 1 carte dual port 10Gb, 25Gb est fortement conseillé.
- Une carte HBA non-RAID, seul le _JBOD_ est supporté.
- Suivez ce lien pour savoir ce qu'en pense MS: [HardWare Requirement]

> ℹ️ DELL propose des serveurs "S2D Ready node"

## Prérequis Logiciels

- Windows Datacenter 2016 ou plus. Préférez une version CORE.

```powershell
# Convert to Datacenter Edition  
$Edition = Get-WindowsEdition -Online
$RelaseId = Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion' -Name ReleaseID | Select-Object ReleaseID
If ($Edition.Edition -notlike "ServerDatacenter*") {
    If ($Edition.Edition -like "*Cor") {
         If ( $RelaseId.ReleaseId -ge 1809) {
            dism /online /Set-Edition:ServerDatacenterCor /ProductKey:WMDGN-G9PQG-XVVXX-R3X43-63DFG /AcceptEula /Quiet
         } Else {
            dism /online /Set-Edition:ServerDatacenterCor /ProductKey:CB7KF-BWN84-R7R2Y-793K2-8XDDG /AcceptEula /Quiet
         }
    }
    } Else {
     Return "Nothing to do" 
    }
}
```

Ce bout de code permet la conversion d'une version __CORE Standard__ à une version __CORE DataCenter__.