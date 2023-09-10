---
title: "Obtenir la taille d'un dossier."
excerpt: "La facon la plus rapide pour calculer la taille d'un dossier selon moi."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
---

## Préambule

Je vous livre "sur le pouce" 👍🏻 une petite fonction pour calculer la taille un dossier.

Un petit truc qui fonctionne mieux que ce que j'ai pu trouver sur le web, parce que franchement ca, c'est lourd !

```powershell

Get-ChildItem C:\ISO | Measure-Object -Property Length -sum

```

## Entrons dans le vif

```powershell

Function Get-FolderSize {
    Param (
        [system.io.directoryInfo]$Path
    )

    $Fis = $Path.EnumerateFileSystemInfos( "*.*", [system.io.SearchOption]::AllDirectories)

    $Fis.ForEach({ $size += $_.Length })

    Return $size
}

Get-FolderSize C:\Windows

```

> oh non, moi chuis po d'accord, je prefere l'autre, en plus c'est du one-liner. 😒

-> toi tu sors. 👈🏻


Les dessins valent parfois mieux que les longs discourts mais Je ne sais pas dessiner...

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-07-27-Get-FolderSize.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-07-27-Get-FolderSize.webp" alt="Comparons les perfs."></a>
  <figcaption>Les performances parlent d'elles-mêmes.</figcaption>
</figure>

Petit plus de ce script, il utilise la methode ForEach()...

Ça, c'est cadeau, à plus !