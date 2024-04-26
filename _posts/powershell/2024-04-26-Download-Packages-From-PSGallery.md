---
title: "Télécharger des packages depuis PowerShell Gallery."
description: "Une fonction PowerShell pour télécharger des packages depuis PowerShell Gallery avec leurs dépendances."
excerpt: |
    Une fonction PowerShell pour télécharger des packages depuis PowerShell Gallery avec leurs dépendances.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Web
  - PSGallery
  - PowershellGet
  - Téléchargement
  - Cmdlet
---

La fonction `Get-PSGalleryPackages` permet de télécharger des packages depuis PowerShell Gallery avec leurs dépendances.


## Le code

```powershell
Function Get-PSGalleryPackages {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true, ValueFromPipeline=$true,Position=0)]
        [ValidateNotNullOrEmpty()]
        [String] $packageName,
        [ValidateNotNullOrEmpty()]
        [String] $Destination
    )
    Function Get-ActualUrl {
        param(
            [Parameter(Mandatory=$true, Position=0)]
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

    $BaseMopdule = Find-Module $packageName

    $Uri = Get-ActualUrl "https://www.powershellgallery.com/api/v2/package/$packageName"
    Start-BitsTransfer $Uri -HttpMethod Get -Destination $Destination

    $BaseMopdule.Dependencies | ForEach-Object {
        $Uri = Get-ActualUrl "https://www.powershellgallery.com/api/v2/package/$($_.Name)"
        Start-BitsTransfer $Uri -HttpMethod Get -Destination $Destination
    }
}
```

## Qu'est-ce que ça fait ?

Cette fonction prend en entrée le nom d'un package PowerShell Gallery et un emplacement de destination.

Elle télécharge le package spécifié ainsi que toutes ses dépendances depuis PowerShell Gallery en utilisant l'API et BitsTransfer.

Cette cmdlet est particulièrement utile lorsque l'on dispose d'un référentiel PowershellGet local. 

Utilisez-la lorsque vous avez besoin de télécharger des packages et leurs dépendances à partir de PowerShell Gallery.

Ca, c'est cadeau, à plus !

[--> En savoir plus sur la fonction Get-ActualUrl]({% post_url 2024-04-26-Get-Actual-Url-After-Redirection %})