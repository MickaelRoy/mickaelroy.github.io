---
title: "Trouver une chaîne dans les objets de Stratégie de Groupe avec PowerShell."
excerpt: |
    Une fonction légère et utile pour vous aider à chercher une chaîne spécifique dans les objets de Stratégie de Groupe de votre environnement Active Directory.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - GPO
---

## Préambule

La fonction `Get-McAfeeWebContent` est une commande PowerShell utilisée pour récupérer le contenu web filtré par McAfee Web Gateway. 

Cette fonction fournit des vérifications supplémentaires et des solutions de contournement pour garantir que les fichiers peuvent être téléchargés une fois que l'analyse antivirus de McAfee est terminée.

## Utilisation

```powershell
Get-McAfeeFilteredWebContent -Uri "https://example.com" -OutFile "fichier_telecharge.txt"
```

```powershell
Function Get-McAfeeWebContent {
    [CmdletBinding(HelpUri='http://go.microsoft.com/fwlink/?LinkID=217035')]
    param(
        [Parameter(Mandatory=$true, ValueFromPipeline=$true,Position=0)]
        [ValidateNotNullOrEmpty()]
        [uri] ${Uri},

        [Parameter(Mandatory=$true)]
        [string] ${OutFile},

        [switch] ${UseBasicParsing},

        [switch] ${DisableKeepAlive}

    )

    process {
        try {
            $response = Invoke-WebRequest @PSBoundParameters -PassThru

            if ($response.Headers.ContainsKey('Via') -and $response.Headers.Via.Contains('McAfee Web Gateway')) {
                $unixEpochStart = [DateTime]::new(1970,1,1,0,0,0,([DateTimeKind]::Utc))
                $content = $response.Content
                if ($content -match '<meta id="progresspageid" content="(.*?)">') {
					    $RequestID = $matches[1]
                        Write-Verbose "Detected request ID of '$RequestID'"
                }
                if ($content -match '/mwg-internal/(.*?)/files/') {
                    $URLPart1 = $matches[1]
                    Write-Verbose "Detected internal URL with folder '$URLPart1'"
                }
                $requestDomain = $Uri.ToString().Split('/')[0..2] -join '/'
                $statusComplete = $false

                While (!$statusComplete) {
                    Start-Sleep -Seconds 3
					$statusUri = "$requestDomain/mwg-internal/$URLPart1/progress?id=$RequestID&a=1&$([Int64]([DateTime]::UtcNow - $unixEpochStart).TotalMilliseconds)"
                    Write-Verbose "Requesting status URI: $statusUri"
                    $PSBoundParameters['Uri'] = $statusUri
					$statusResponse = Invoke-WebRequest @PSBoundParameters -PassThru
                    if ($statusResponse.StatusCode -eq 200) {
                        $Global:colStat = $statusResponse.Content.Split(';')
                        if ($colStat[3] -eq 1 -or $colStat[3] -eq $null) {
                            Write-Verbose "Detected completion status. Attempting request of original content"
                            $statusComplete = $true
							$PSBoundParameters['Uri'] = "$requestDomain/mwg-internal/$URLPart1/progress?id=$RequestID&dl"
							$result = Invoke-WebRequest @PSBoundParameters
                        } elseif ($colStat[4] -eq 0) {
                            Write-Verbose "Downloaded $($colStat[0]) of $($colStat[1])"
                        } else {
                            Write-Verbose "Scanning in progress ($($colStat[4])s)"
                        }
                    }
                }
            }
        } catch {
            throw
        }
    }
}
```

## Paramètres

* __Uri__: Paramètre obligatoire spécifiant l'URI du contenu web à récupérer.
* __OutFile__: Paramètre obligatoire spécifiant le chemin où le contenu téléchargé sera enregistré.
* __UseBasicParsing__: Commutateur facultatif indiquant s'il faut utiliser une analyse de base pour la requête web.
* __DisableKeepAlive__: Commutateur facultatif indiquant s'il faut désactiver le maintien de connexion HTTP pour la requête web.

## Exemple

```powershell
Get-McAfeeFilteredWebContent -Uri "https://example.com" -OutFile "fichier_telecharge.txt"
```

Cette commande récupère le contenu web depuis "https://example.com" et le sauvegarde dans un fichier nommé "fichier_telecharge.txt".

## Remarques

* Cette fonction est conçue pour fonctionner spécifiquement avec McAfee Web Gateway afin de garantir le téléchargement réussi des fichiers après la numérisation antivirus.
* Des paramètres et des commutateurs supplémentaires sont disponibles pour la personnalisation et l'optimisation en fonction des besoins spécifiques.

Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Find-StringInGPO){: .btn .btn--info}