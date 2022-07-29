---
title: "Déploiement du Switch Embedded Teamin (SET)"
excerpt: "Les prérequis pour concevoir un cluster S2D."
classes: wide
---

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
 