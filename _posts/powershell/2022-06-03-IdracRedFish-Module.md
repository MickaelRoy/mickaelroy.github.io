---
title:  "Dell/Idrac RedFish powershell module"
excerpt: "Un module PowerShell à retrouver sur mon GitHub, pour administrer l'IDRAC sans utiliser Racadm."
category: PowerShell
tags: 
  - PowerShell
  - Module
  - Dell
  - Idrac
  - Redfish
  - Racadm
header:
  teaser: /assets/images/DellLogoCorruption.webp
  image_description: "Dell Logo"
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

```powershell
Register-RacBiosSettings -Ip_Idrac 192.168.0.120 -RacUser root -RacPwd *pass* -Name SysProfile -Value PerfOptimized
Submit-RacPendingBiosSettings -Ip_Idrac 192.168.0.120 -RacUser root -RacPwd *pass* -Restart
```
On voit dans cet exemple que l'authentification se fait deux fois, Ce qui peut être coûteux en temps pour les actions dependants de beaucoups de sous-actions.

### Session

Dans ce cas, on créer une session, on la stock dans une variable pour la réutiliser.

Attention toutefois, les paramètres IDRAC sont configurés pour purger les sessions inactives depuis trop longtemps.

Si ce n'est pas le cas, n'oubliez pas de supprimer la session en fin de traitement. Le nombre de session simultané est limité.

```powershell
$Session = New-RacSession -Ip_Idrac 192.168.0.120 -Credential $Credential
Register-RacBiosSettings -Session $Session -Name SysProfile -Value PerfOptimized
Submit-RacPendingBiosSettings -Session $Session -Restart
Remove-RacSession $Session
```
On voit dans cet example que l'authentification ne se fait qu'une fois.

Ca c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/IdracRedFish){: .btn .btn--info}