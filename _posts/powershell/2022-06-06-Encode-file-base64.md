---
title:  "Encoder, décoder un fichier"
seo_title: "Encoder ou décoder un fichier dans une variable."
seo_description: "Une astuce pour transformer n'importe quel fichier en chaine de charactères, la stocker dans une variable, l'envoyer par mail, faites-en ce que vous voulez."
excerpt: |
  Petite astuce pour consigner un fichier dans une variable par exemple.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Base64
---

## Description

Voici une petite astuce pour encoder n'importe quel fichier en chaine de caractère, et bien entendu, décoder cette chaine en fichier.

Ce n'est en aucun cas du chiffrement, soyons bien clair, c'est pourquoi j'utilise les termes encoder/décoder

s PowerShell, il est souvent nécessaire de déplacer des fichiers entre des machines distantes de manière efficace. Voici comment vous pouvez accomplir cette tâche rapidement en utilisant la commande Invoke-Command.

(Un petit fichier... moins de 1Mo... n'allez pas essayer avec un ISO.)

## Encoder
 
```powershell
$FilePath = "c:\setup\foo.exe"
$File = [System.IO.File]::ReadAllBytes($FilePath)
$Base64String = [System.Convert]::ToBase64String($File)
```
Ce code lit le contenu du fichier foo.exe en tant qu'octets, puis le convertit en une chaîne base64.

Il peut donc être décodé sur un server distant via un Invoke-command.


## Decoder

```powershell
# Si vous utilisez invoke-command, $Using ne vous est pas inconnu.
# $Base64String = $Using:Base64String
[Byte[]] $bytes = [System.Convert]::FromBase64String($Base64String);
[IO.File]::WriteAllBytes("c:\setup\foo.exe",$bytes)

Ce code reprend la chaîne base64 précédemment générée, la convertit en octets, puis écrit ces octets dans un nouveau fichier foo.exe.

```

## Déplacement du fichier via Invoke-Command

```powershell
# Si vous utilisez invoke-command, $Using ne vous est pas inconnu.
# $Base64String = $Using:Base64String
[Byte[]] $bytes = [System.Convert]::FromBase64String($Base64String)

Invoke-Command -ComputerName RemoteComputer -ScriptBlock {
    param($bytes)
    [IO.File]::WriteAllBytes("c:\setup\foo.exe", $bytes)
} -ArgumentList $bytes
```
Ce code utilise Invoke-Command pour exécuter une opération sur un ordinateur distant. Il transmet les octets du fichier foo.exe à l'ordinateur distant où ils sont ensuite écrits dans un nouveau fichier.

Cette méthode offre une solution efficace pour déplacer des fichiers entre des machines distantes en utilisant PowerShell, ce qui peut être particulièrement utile dans les environnements de gestion de serveurs ou d'automatisation.

## Note

Vous noterez que dans le code que je vous donne, on utilise que des classes dot net : _System.IO.File_, _System.Text.Encoding_ ou encore _System.Convert_.

Ca claque ou pas ?



Ca, c'est cadeau, à plus !