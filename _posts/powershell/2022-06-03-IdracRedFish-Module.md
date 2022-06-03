---
title:  "Dell/Idrac RedFish powershell module"
excerpt: "Un module PowerShell à retrouver sur mon GitHub, pour administrer l'IDRAC sans utiliser Racadm."
category: Powershell
tags: 
  - Powershell
  - Module
  - Dell
  - Idrac
  - Redfish
  - Racadm
link: https://github.com/MickaelRoy/IdracRedFish
---

# Idrac RedFish Powershell Module


## Description
Basé sur un travail de DELL, voici quelques cmdlets simplifiées pour des operations administrative courantes.

- Ajouter/Supprimer un utilisateur IDRAC.
- Modifier des paramètres BIOS.
- Allumer/Eteindre le serveur.

## Deux methodes d'authentification
### utilisateur/mot de passe
Dans le cas present, on utilise un nom d'utilisateur et un mot de passe pour une action simple.

```Powershell
Register-RacBiosSettings -Ip_Idrac 192.168.0.120 -RacUser root -RacPwd *pass* -Name SysProfile -Value PerfOptimized
Submit-RacPendingBiosSettings -Ip_Idrac 192.168.0.120 -RacUser root -RacPwd *pass* -Restart
```
On voit dans cet exemple que l'authentification se fait deux fois, Ce qui peut être coûteux en temps pour les actions dependants de beaucoups de sous-actions.

### Session
Dans ce cas, on creer une session, on la stock dans une variable pour la reutiliser.
Attention toutefois, les paramètres IDRAC sont configurés pour purger les sessions inactives depuis trop longtemps.
Si ce n'est pas le cas, n'oubliez pas de supprimer la session en fin de traitement. Le nombre de session simultané est limité.

```Powershell
$Session = New-RacSession -Ip_Idrac 192.168.0.120 -Credential $Credential
Register-RacBiosSettings -Session $Session -Name SysProfile -Value PerfOptimized
Submit-RacPendingBiosSettings -Session $Session -Restart
Remove-RacSession $Session
```
On voit dans cet example que l'authentification ne se fait qu'une fois.

Ca c'est cadeau, a plus !