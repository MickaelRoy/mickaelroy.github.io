---
title:  "Encoder, décoder un fichier"
excerpt: "Petite astuce pour consigner un fichier dans une variable par exemple."
category: PowerShell
tags: 
  - PowerShell
  - Tips
  - Base64
---

# Get-WsusUpdates

## Description

Voici une petite astuce pour convertir n'importe quel fichier en chaine de caractère.

Ce n'est en aucun cas du chiffrement, soyons bien clair.

Je m'en sers parfois pour exporter un fichier entre deux serveurs ou seul winrm est ouvert.

Un petit fichier... moins de 1Mo... n'allez essayer avec un ISO.

## Encode
 
```powershell
$FilePath = "c:\setup\foo.exe"
$File = [System.IO.File]::ReadAllBytes($FilePath)
$Base64String = [System.Convert]::ToBase64String($File)
```
Le contenu du fichier se trouve codé dans la variable Base64String, il peut donc être décodé sur un server distant via un invoke-command.

## Decode

```powershell
# Si vous utilisez invoke-command, $Using ne vous est pas inconnu.
# $Base64String = $Using:Base64String
[Byte[]] $bytes = [System.Convert]::FromBase64String($Base64String);
[IO.File]::WriteAllBytes("c:\setup\foo.exe",$bytes)
```
## Note

Vous noterez que dans le code que je vous donne, on utilise que des classes dot net : _System.IO.File_, _System.Text.Encoding_ ou encore _System.Convert_.

Ca claque ou pas ?



Ca, c'est cadeau, à plus !