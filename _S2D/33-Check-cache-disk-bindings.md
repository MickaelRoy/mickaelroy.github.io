---
title: "Vérifier les liaisons des disques de cache"
excerpt: "Verifier les 'bindings' des disques de caches S2D."
classes: wide
---
# Le quorum

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

