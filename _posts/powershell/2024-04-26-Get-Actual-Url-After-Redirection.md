---
title: "Obtenir l'URL réelle derrière une URL redirigée"
description: "Une fonction PowerShell pour obtenir l'URL réelle d'un raccourci."
excerpt: |
    Une fonction PowerShell qui permet de récupérer l'URL réelle d'un raccourci à partir de son URL courte.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Web
  - Uri
---

La fonction `Get-ActualUrl` permet de récupérer l'URL réelle d'un raccourci à partir de son URL courte, ou après une redirection web.

## Le code

```powershell
Function Get-ActualUrl {
    param(
        [Parameter(Mandatory=$true, ValueFromPipeline=$true,Position=0)]
        [ValidateNotNullOrEmpty()]
        [uri] $Uri
    )

    $WebClientObject = [System.Net.WebClient]::new()
    $WebRequest = [System.Net.WebRequest]::create($Uri)
    $WebResponse = $WebRequest.GetResponse()
    $ActualDownloadURL = $WebResponse.ResponseUri.AbsoluteUri
    $ResultsObject = [PsCustomObject] @{ 
        'Shortened URL' = $Uri;
        'Actual URL' = $ActualDownloadURL
    }
    $WebResponse.Close()
    $ResultsObject.'Actual URL'
}
```

## Qu'est-ce que ça fait ?

Cette fonction prend une URL en entrée et utilise une requête Web pour obtenir l'URL réelle. Elle retourne l'URL réelle sous forme d'objet PowerShell personnalisé.

Utilisez-la lorsque vous avez besoin de résoudre des Url d'URL pour obtenir l'URL complète.


Ca, c'est cadeau, à plus !