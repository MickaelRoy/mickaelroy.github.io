---
title: "Configuration RoCE"
excerpt: "La configuration RoCE pour concevoir un cluster S2D."
classes: wide
---
 
> Prononcez "Roky"

## Configuration RoCE selon les best practices DELL

> ℹ️ Zappez la partie QOS si vous avez des cartes iWarp.

```powershell
$AdapterGroup = Get-NetAdapter -Physical | Where-Object Speed -GE 10000000000 | Sort-Object Name | Group-Object DriverDescription | Where-Object Count -EQ 2
 
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