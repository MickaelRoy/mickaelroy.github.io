---
title: "Optimisation RSS"
excerpt: "Configurer RSS pour concevoir un cluster S2D."
classes: wide
---

Ce code est adapté dans le cas où vous n'avez qu'une seule carte dual port.

C'est une partie alambiquée qui nécessite une certaine expérience pour comprendre les notions de NUMA et de RSS.

Mieux vaut ne rien faire que de faire des bêtises.

Lisez attentivement la page [Windows Server 2016 Networking – Part 3- Optimizing Network settings] avant de tenter cette config.

> ⚠️ le code suivant n'est là que pour la démo.

```powershell
# Configure RSS  
$CPU = Get-WmiObject -Class Win32_Processor
$MaxProc = ([array]$CPU)[0].NumberOfCores - 1 
$MaxLogProc = ([array]$CPU)[0].NumberOfLogicalProcessors - ([array]$CPU).Count
$MaxLbfoProc = ([array]$CPU).Count * ([array]$CPU)[0].NumberOfLogicalProcessors - ([array]$CPU).Count
Set-NetAdapterAdvancedProperty $Primary1, $Primary2 -DisplayName 'Preferred NUMA Node' -DisplayValue 'Node 0'
Get-NetadapterHardwareInfo $Primary1, $Primary2 | Set-NetAdapterVmq -NumaNode $_.NumaNode

Set-NetAdapterVmq -Name $Primary1 -BaseProcessorNumber 2 -MaxProcessors $MaxProc -MaxProcessorNumber $MaxLogProc
Set-NetAdapterVmq -Name $Primary2 -BaseProcessorNumber ($MaxLogProc + 4) -MaxProcessors $MaxProc -MaxProcessorNumber $MaxLbfoProc

Set-NetAdapterRss -BaseProcessorNumber 2 -Name 'vEthernet (SMB01)'
Set-NetAdapterRss -BaseProcessorNumber 2 -Name 'vEthernet (SMB02)'
Set-NetAdapterRss -BaseProcessorNumber 2 -Name 'vEthernet (Management)'

sleep 1
Restart-NetAdapter 'vEtherne*'
```


[Windows Server 2016 Networking – Part 3- Optimizing Network settings]: <https://www.darrylvanderpeijl.com/windows-server-2016-networking-optimizing-network-settings/>