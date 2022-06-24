---
title: "Créez deux cartes pour la réplication SMB"
excerpt: "Les deux cartes SMB pour concevoir un cluster S2D."
classes: wide
---

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

Beaucoup de choses dans ce bout de code.

Prenez le temps de regarder la partie Ipv6, que je désactive de force, de façon un peu sale.

L’expérience montre qu’en entreprise, il nous arrive de devoir faire face a des GPO récalcitrantes, d’où l’interet de ce procédé.

> ℹ️ Si vous avez le choix, optez toujours pour une désactivation de l’Ipv6 via la clé __DisabledComponents__. La seule methode supportée par MS.

> ℹ️ Chaque vNic devrait être sur un VLAN séparé. Ici je ne sépare que la management des deux autres.