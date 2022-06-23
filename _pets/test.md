---
title: "Test"
excerpt: "Toutes les étapes pour concevoir un cluster S2D aka Storage Spaces Direct."
---

# Intro

Aujourd'hui, ce ne sera pas franchement fun. Aujourd'hui je vous donne toutes les étapes clés pour créer un cluster S2D from scratch.

Je vous explique quoi faire et pourquoi.

Ici la configuration est adaptée à RoCE, qui fonctionne en UDP, c'est pourquoi il y a une étape qui concerne la configuration DCB.

Dans le cas de iWarp, que je n'aborderai pas ici, qui fonctionne en TCP (avec ses avantages) vous pouvez zapper la partie DCB/QOS.

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


# Procédure pour chaque nœud

## Ajout des fonctionnalités Windows

```powershell
Install Windows Features  
 Install-WindowsFeature -Name 'File-Services'
 Install-WindowsFeature -Name 'Data-Center-Bridging'
 Install-WindowsFeature -Name 'FS-SMBBW'
 Install-WindowsFeature -Name 'Failover-Clustering' -IncludeManagementTools
 Install-WindowsFeature -Name 'Hyper-V' -IncludeManagementTools -Restart
```

FS-SMBBW n'est pas obligatoire, mais je le conseille.

En l’état il vous manquera une fonctionnalité si vous souhaitez faire du réplicat de stockage.

## Création du VmSwitch SET.

```powershell
Function Invoke-NetConfig {
 
    $All = [System.Net.NetworkInformation.NetworkInterface]::GetAllNetworkInterfaces().Where({ $_.OperationalStatus -eq 'Up' })
    Foreach ( $Item in $All ) {
        $Props = $Item.GetIPProperties()
        If ($null -ne $Props) {
            Try {  $GWMgmt = $Props.GatewayAddresses[0].Address.ToString()
             
                If ( -not [String]::IsNullOrEmpty($GWMgmt)) {
                    $UnicastAddresses = $Props.UnicastAddresses.Where({ $_.Address.AddressFamily -eq [System.Net.Sockets.AddressFamily]::InterNetwork })
                    $IPMgmt = $UnicastAddresses.Address.ToString()
                    $PLMgmt = $UnicastAddresses.PrefixLength
                    $DNSServer = $Props.DnsAddresses -join ","
                    $Speed = $Item.Speed
                    Break
                }
 
            } Catch { Write-Warning -Message "oupsie... trying next" }
        }
    }

    # Get vlan ID for management vNic
    [Int]$VlanID = (Get-NetAdapterAdvancedProperty -DisplayName 'VLAN ID' | Sort-Object Name | Select-Object -First 1).DisplayValue

    $SwitchName = 'SW-' + ($Speed/ 1000000000) + 'G'
    $AdapterGroup = Get-NetAdapter -Physical | Where-Object Speed -GE 10000000000 | Group-Object DriverDescription | Where-Object Count -EQ 2
    New-VMSwitch -Name $SwitchName -EnableEmbeddedTeaming $True -NetAdapterName $AdapterGroup.Group.Name -AllowManagementOS $True -MinimumBandwidthMode Weight
    Set-VMSwitchTeam -Name $SwitchName -LoadBalancingAlgorithm HyperVPort
 
    $MgmtNic = Get-VMNetworkAdapter $SwitchName -ErrorAction SilentlyContinue -ManagementOS
    If ( $null -eq $MgmtNic ) { # should never happens if -AllowManagementOS parameter is $True on New-VMSwitch execution
        # Create Management vNic
        Add-VMNetworkAdapter -Name Management -SwitchName $SwitchName -ManagementOs
    } Else {
        # Rename Management vNic
        Rename-VMNetworkAdapter -Name $SwitchName -NewName Management -ManagementOs
    }
 
    $CurrentIP = (Get-NetIPAddress -InterfaceAlias 'vEthernet (Management)' -AddressFamily IPv4 -ErrorAction SilentlyContinue).IPAddress
    If ($CurrentIP -ne $IPMgmt) {
        New-NetIPAddress -InterfaceAlias 'vEthernet (Management)' -IPAddress $IPMgmt -PrefixLength $PLMgmt -Type Unicast -DefaultGateway $GWMgmt
        Set-DnsClientServerAddress -InterfaceAlias 'vEthernet (Management)' -ServerAddresses $DNSServer
    }   
    
    If ($null -ne $VlanID) {
        Set-VMNetworkAdapterVlan -ManagementOS -VMNetworkAdapterName Management -Access -VlanId $VlanID
    }

    Restart-NetAdapter 'vEthernet (Management)'
}
 
Invoke-NetConfig
```

Ce bout de code attrape les paramètres réseaux de la carte qui dispose d'une passerelle pour créer et configurer le Switch SET ainsi que la carte de management.

> ⚠️ Je précise que le LACP n’est pas supporté par MS dns un contexte HCI.
 
## Configuration RoCE selon les best practices DELL

> ℹ️ Zappez la partie QOS si vous avez des cartes iWarp.

```powershell
$AdapterGroup = Get-NetAdapter -Physical | Where-Object Speed -GE 10000000000 | Sort-Object Name| Group-Object DriverDescription | Where-Object Count -EQ 2
 
$Primary1 = $AdapterGroup.Group[0].Name
$Primary2 = $AdapterGroup.Group[1].Name
 
Remove-NetQosTrafficClass
Remove-NetQosPolicy -Confirm:$False
 
# Configure QoS policies for SMB-Direct (RoCE), Cluster Heartbeat and Default (all other) traffic
New-NetQosPolicy -Name "Cluster" -Cluster -PriorityValue8021Action 7
New-NetQosPolicy -Name "SMB" -NetDirectPortMatchCondition 445 -PriorityValue8021Action 3
New-NetQosPolicy -Name "Default" -Default -PriorityValue8021Action 0
New-NetQosPolicy -Name "TCP" -IPProtocolMatchCondition TCP -PriorityValue8021Action 1
New-NetQosPolicy -Name "UDP" -IPProtocolMatchCondition UDP -PriorityValue8021Action 1
 
# Set minimum bandwidth - 50% for SMB-Direct
New-NetQosTrafficClass "SMB" -Priority 3 -BandwidthPercentage 50 -Algorithm ETS

# This traffic class ensures that there's enough bandwidth reserved for cluster heartbeats:
If ((Get-NetAdapter -Physical | Group-Object speed | ? Count -eq 2).Name -le 10000000000 ) {
    New-NetQosTrafficClass "Cluster" -Priority 7 -BandwidthPercentage 2 -Algorithm ETS
} Else {
    New-NetQosTrafficClass "Cluster" -Priority 7 -BandwidthPercentage 1 -Algorithm ETS
}
 
# Disable flow control for all other traffic
Disable-NetQosFlowControl -Priority 0,1,2,4,5,6,7
 
# Enable flow control for SMB-Direct (RoCE)
Enable-NetQosFlowControl -Priority 3
 
# Apply Quality of Service (QoS) policy to the target adapters
Enable-NetAdapterQos -Name $Primary1, $Primary2
 
# Block DCBX protocol between switches and nodes for all interfaces
Set-NetQosDcbxSetting -Willing $false -Confirm:$false
  
# Set Mellanox dcbxmode to host in charge; requires OOB Mellanox driver
Set-NetAdapterAdvancedProperty -Name $Primary1 -DisplayName 'Dcbxmode' -DisplayValue 'Host in charge'
Set-NetAdapterAdvancedProperty -Name $Primary2 -DisplayName 'Dcbxmode' -DisplayValue 'Host in charge'

# force la carte en mode RoCE (paramètre Mellanox)
Set-NetAdapterAdvancedProperty -Name $Primary1 -RegistryKeyword '*NetworkDirectTechnology' -RegistryValue 4
Set-NetAdapterAdvancedProperty -Name $Primary2 -RegistryKeyword '*NetworkDirectTechnology' -RegistryValue 4

#  SET Switch need the -IeeePriorityTag to add the "Cluster" PriorityTag of 7 on vNIC/MGMT
Set-VMNetworkAdapter -Name Management -ManagementOS -MinimumBandwidthWeight 10
Set-VMNetworkAdapter -ManagementOS -Name Management -IeeePriorityTag on
```

## Activer le jumbo frame sur les cartes physiques

```powershell
# Enable Jumbo
Set-NetAdapterAdvancedProperty -Name $Primary1 -RegistryKeyword '*JumboPacket'  -RegistryValue 9014
Set-NetAdapterAdvancedProperty -Name $Primary2 -RegistryKeyword '*JumboPacket'  -RegistryValue 9014

```

Un point à ne pas négliger, le jumbo frame apporte vraiment beaucoup dans l’hyper-convergence.

## Créez deux cartes pour la réplication SMB

```powershell
# n'oubliez pas de renseigner ces variables !!
$VlanSMB =
$IPSMB01 =
$IPSMB02 =
$PLSMB = 

$SwitchName = Get-VMSwitch | select -ExpandProperty Name

# Add Vnic Storage attached to vSwitch
Add-VMNetworkAdapter -Name SMB01 -SwitchName $SwitchName -ManagementOS
Add-VMNetworkAdapter -Name SMB02 -SwitchName $SwitchName -ManagementOS
 
# Enable RDMA
Enable-NetAdapterRDMA -Name *SMB*
  
# Set Vlan of storage vNic
Set-VMNetworkAdapterVlan -ManagementOS -VMNetworkAdapterName SMB01 -Access -VlanId $VlanSMB
Set-VMNetworkAdapterVlan -ManagementOS -VMNetworkAdapterName SMB02 -Access -VlanId $VlanSMB
  
# Restart net adapters to ensure all is effective
Restart-NetAdapter 'vEthernet (SMB01)'
Restart-NetAdapter 'vEthernet (SMB02)'
  
Start-Sleep 5
 
# Map each storage vNic on a dedicated physical port
if (-not (Get-NetLbfoTeamNic)) {
    Set-VMNetworkAdapterTeamMapping -ManagementOS -VMNetworkAdapterName SMB01 -SwitchName $SwitchName -PhysicalNetAdapterName $Primary1
    Set-VMNetworkAdapterTeamMapping -ManagementOS -VMNetworkAdapterName SMB02 -SwitchName $SwitchName -PhysicalNetAdapterName $Primary2
}

# Configure DNS on each interface, but do not register Storage interfaces
Set-DnsClient -InterfaceAlias "vEthernet (SMB0?)" -RegisterThisConnectionsAddress $false
 
# Disable ipv6, because sometime DisabledComponents is forced to 0x20 by GPO :( - It's a non supported way to disable ipv6.
$Reg = Get-ItemProperty "hklm:\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" -Name DisabledComponents
If ($Reg.DisabledComponents -ne 10 -and $Reg.DisabledComponents -ne 11 -and $Reg.DisabledComponents -ne 255) {
    Get-NetAdapter *SMB* | Disable-NetAdapterBinding -ComponentID ms_tcpip6
    Set-Net6to4configuration -state disabled
    Set-Netisatapconfiguration -state disabled
    Set-NetTeredoConfiguration -type disabled
}
  
# Set IP mellanox card (not needed if configured as IPv6 Local Link)
New-NetIPAddress -InterfaceAlias "vEthernet (SMB01)" -IPAddress $IPSMB01 -PrefixLength $PLSMB -Type Unicast
New-NetIPAddress -InterfaceAlias "vEthernet (SMB02)" -IPAddress $IPSMB02 -PrefixLength $PLSMB -Type Unicast

# Enable Jumbo frame.
# The valid keyword values are 1514, 4088, 9014
if (Get-NetLbfoTeamNic) {
    Set-NetAdapterAdvancedProperty -Name 'vEthernet (SMB01)' -RegistryKeyword '*JumboPacket'  -RegistryValue 9014
    Set-NetAdapterAdvancedProperty -Name 'vEthernet (SMB02)' -RegistryKeyword '*JumboPacket'  -RegistryValue 9014
}

Set-VMNetworkAdapter -ManagementOS -Name SMB01 -IeeePriorityTag on
Set-VMNetworkAdapter -ManagementOS -Name SMB02 -IeeePriorityTag on
```

Beaucoup de chose dans ce bout de code.

Prenez le temps de regarder la partie Ipv6, que je désactive de force, de façon un peu sale.

L’expérience montre qu’en entreprise, il nous arrive de devoir faire face a des GPO récalcitrantes, d’où l’interet de ce procédé.

> ℹ️ Si vous avez le choix, optez toujours pour une désactivation de l’Ipv6 via la clé __DisabledComponents__. La seule methode supportée par MS.

> ℹ️ Chaque vNic devrait être sur un VLAN séparé. Ici je ne sépare que la management des deux autres.

## Changement du Timeout pour le service SpacePort

```powershell
# For performance optimization and reliability, update the hardware timeout configuration for the Spaces port. (Dell Preco 0x00002710 or 10sec)
# Gives greater resiliency to possible VHD / VHDX / VMDK storage latency in guest clusters, increase the Storage Spaces I/O timeout value (MS Preco 0x00007530 or 30sec)
# Default value is 0x1770 or 6sec
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\spaceport\Parameters -Name HwTimeout -Value 0x00002710
```

## AD: Traffic RPC limité à un port specifique ?

> ℹ️ Si votre configuration AD n'est pas concernée, passez cette étape.

```powershell
# Create the rule to avoid 14050 vmms event 
$policy = New-Object -ComObject HNetCfg.FwPolicy2
$rule = $policy.ServiceRestriction.Rules | Where-Object { $_.RemotePorts -eq 5191}
If ([String]::IsNullorEmpty($rule)) {
    $rule = New-Object -com HNetCfg.FWRule
    $rule.Name = "Allow outbound traffic from service to TCP 5191"
    $rule.Direction = 2
    $rule.Protocol = 6
    $rule.Enabled = $true
    $rule.RemotePorts = "5191"
    $rule.ApplicationName = "$($env:systemdrive)\WINDOWS\system32\vmms.exe"
    $rule.ServiceName = "vmms"
    $policy.ServiceRestriction.Rules.Add($rule)
}
```

Pour plus d'info, consultez l'article MS concernant le [Trafic RPC]

## Configuration RSS

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

## Live Migration

```powershell
Set-VMHost -MaximumVirtualMachineMigrations 2
```

Mieux vaut 2 machines qui bougent vite, que 10 qui se trainent.

> 💡 On sera amené à rediscuter le point Live Migration en fin de tuto.

```powershell
If ((Get-NetAdapter -Physical | Group-Object speed | ? Count -eq 2).Name -le 10000000000 ) {
    Set-VMHost –VirtualMachineMigrationPerformanceOption Compression
} Else {
    Set-VMHost –VirtualMachineMigrationPerformanceOption SMB
}
```

> ℹ️ En dessous de 10Gbs, MS suggère la compression pour le live migration.

## Emplacement par défaut des VMs.

```powershell
Set-VMHost -VirtualMachinePath 'C:\ClusterStorage\Volume1'
```

C'est purement cosmetic quand on y pense.

## Augmentation de la taille des journaux (facultatif)

```powershell
# extend log max size
wevtutil set-log "Microsoft-Windows-SMBServer/Analytic" /enabled:false
wevtutil set-log "Microsoft-Windows-SMBServer/Analytic" /enabled:true /quiet:true /retention:true /maxsize:52428800

wevtutil set-log "Microsoft-Windows-Hyper-V-VmSwitch-Diagnostic" /enabled:false
wevtutil set-log "Microsoft-Windows-Hyper-V-VmSwitch-Diagnostic" /enabled:true /quiet:true /retention:true /maxsize:52428800

wevtutil set-log "Microsoft-Windows-Kernel-IoTrace/Diagnostic" /enabled:false
wevtutil set-log "Microsoft-Windows-Kernel-IoTrace/Diagnostic" /enabled:true /quiet:true /retention:true /maxsize:
```

## Préparation des disques.
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

Bref. C’est une RAZ des disques. Ne le faite qu’une fois à la creation du cluster.

## Vérification des disques

```powershell
$nodes  = 'nodea', 'nodeb', 'nodec', 'noded'
icm $nodes {
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

Afin de s'assurer que la préparation a fonctionné.

# Création du cluster

## Validation préliminaire du cluster

Dès lors que tous les nœuds héritent de la même configuration, on peut passer a la jonction du cluster en tant que tel.

Bien sur, vous vous êtes assurés que chaque vNic de chaque nœud peut joindre chaque vNic de tous les autres nœuds ?

Si ce n'est pas le cas, le Cmdlet Test-Cluster le fera pour vous.

Ouvrez une session sur l'un des nœuds, 

```powershell
$Nodes = 'nodea', 'nodeb', 'nodec', 'noded'
Test-Cluster -Node $nodes
```

Apres quelques minutes, un rapport sera généré et reflètera les déviances et autres anomalies.

Vous pouvez ignorer les warning (⚠️), mais pas les échecs (❌). 

Donc si c'est jaune, vous corrigerez plus tard, si c'est rouge, c'est mort.

## Joindre les noeuds au cluster

Le Cmdlet nécessite que vous ayez les droits de création d'objet Computer sur l'AD.
Si vous êtes dans un contexte restreint, vous pouvez le faire pré-provisionner.

```powershell
#Tell to powershell to clusterize all nodes for you
New-Cluster -Name $CNO -Node $nodes -NoStorage -staticAddress [CNOIPAddress]
```
> ℹ️ L'ip du CNO doit être dans le même VLAN que la vNic de management.

## Le quorum

Il existe plusieurs facon de gérer le Quorum. Ici je présente le File-Share Witness. (hébergé sur un File-Share Windows qui plus est...)

```powershell
# don't forget to feed the variables below:
$CNO = [ClusterName]
$FsServer = [FileShareServerName]
$Path = [FileShareLocation]

$FsW = icm -Computername $FsServer -scriptblock {
    $Path = $Using:Path
    $dir = New-Item -Path $Path -Name $Using:CNO -ItemType Directory
    New-SmbShare -Name ($Using:CNO + '$') -Path $dir.FullName -FullAccess ($Using:CNO + '$') -ContinuouslyAvailable:$false -Description "File Share Witness Quorum for $Using:CNO"
    (Get-SmbShare –Name ($Using:CNO + '$')).PresetPathAcl | Set-Acl
}
FailoverClusters\Set-ClusterQuorum -NodeAndFileShareMajority "\\$($FsW.ScopeName)\$($FsW.Name)" -Cluster $CNO
```

## Activer l'hyper-convergence

```powershell
Enable-ClusterS2D -Cim $CNO
```

Facile...

## Renommer les réseaux de cluster

```powershell
(Get-ClusterNetwork -Cluster $CNO | ? Role -eq ClusterAndClient).name = "Management"
(Get-ClusterNetwork -Cluster $CNO | ? Role -ne ClusterAndClient).name = "SMB"

```

Pour plus de lisibilité !

## Augmenter la taille de block de cache du cluster

D'expérience, cela améliore grandement les performances d'écritures sur le CSV.
La valeur est purement arbitraire, je vous invite à faire des benches pour connaitre la valeur la plus adéquat.

```powershell
Configuring CSV Cache
(Get-Cluster).BlockCacheSize = 8192
```

## Exclure la vNic de management du live migration

Le trafic du live migration n'a pas vocation à transiter par la carte de management, en particulier si vous avez réduit ses possibilités RSS.

```powershell 
Get-ClusterResourceType -Cluster $CNO -Name "Virtual Machine" | Set-ClusterParameter -Cluster $CNO -Name MigrationExcludeNetworks -Value ([String]::Join(";",(Get-ClusterNetwork -Cluster $CNO | Where-Object {$_.Name -eq "Management"}).ID))
```

## Limiter la bande passante du live migration

Afin d'éviter une congestion réseau lors d'une migration dynamique, je préconise une limitation à deux VMs en mouvement simultané d'une part, mais également de limiter la bande passante utilisée par ce service.

Ceci garantit un déplacement invisible de la VM, en particulier si un Storage Job est en cours après un reboot par exemple.

Imaginez le scenario où le cluster est en cours de patching... La reconstruction du CSV et le déplacement des VMs sur leur nœud d'origine peuvent s'effectuer parallèlement ; Selon la quantité de RAM alloué à chaque VM, c'est une vrai tempête qui déferle sur les cartes réseaux.

```powershell
Set-SmbBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
```

## Créer le volume

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

```powershell
Get-VirtualDisk Volume1 | Set-VirtualDisk -NewFriendlyName CSV01
(Get-ClusterSharedVolume -Name *Volume1*)[0].Name = 'Cluster Virtual Disk (CSV01)'
Set-Volume -FileSystemLabel Volume1 -NewFileSystemLabel CSV01
```

Pour de la lisibilité.

# Conclusion

Voici pour l'essentiel. Il y a pleins d'autres aspects à prendre en considération. J'en parlerai dans d'autres posts, promis !

Si ça c'est pas un putain de cadeau ?!

A plus !

[HardWare Requirement]: <https://docs.microsoft.com/fr-fr/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements>
[Trafic RPC]: <https://docs.microsoft.com/fr-fr/troubleshoot/windows-server/identity/restrict-ad-rpc-traffic-to-specific-port>
[Windows Server 2016 Networking – Part 3- Optimizing Network settings]: <https://www.darrylvanderpeijl.com/windows-server-2016-networking-optimizing-network-settings/>
