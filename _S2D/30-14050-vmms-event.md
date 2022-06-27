---
title: "14050-VMMS Event ID"
excerpt: "Configurer le parefeu pour résoudre l'ID d'évènement 14050 sur un cluster S2D."
classes: wide
---

## AD: Trafic RPC limité à un port specifique ?

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

[Trafic RPC]: <https://docs.microsoft.com/fr-fr/troubleshoot/windows-server/identity/restrict-ad-rpc-traffic-to-specific-port>