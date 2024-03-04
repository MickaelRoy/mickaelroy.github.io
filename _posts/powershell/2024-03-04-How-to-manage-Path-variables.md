---
title: "Ajouter, supprimer des chemins à la variable PATH."
excerpt: |
Deux cmdlets pour industrialiser la modification de la veriable PATH ou PSModulePath.
Cela peut être utilisé dans le cadre de la création de package chocolatey par exemple...

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
---

## Préambule

Ici, je vous livre pas moins de deux cmdlets pour gérer l'ajout et la suppresion de chemin dans les variables d'environnement systèmes PATH ou PSModulePath

Elles ne traitent pas le rechargement des variables dans le contexte d'éxecution, ni la verification de l'existance du chemin lors de l'ajout. Cela fera peut être l'objet d'une évolution ultérieure.

## Dans le vif

### Ajout

```powershell
Function Add-Path {
    Param(
        [Parameter(Mandatory=$false)]
        [ValidateSet("PATH", "PSModulePath")]
        [String]$Target = 'PATH',
        [Parameter(Mandatory=$true)]
        [String]$Path
    )
    $PS = [IO.Path]::PathSeparator
    $Pathvar = [Environment]::GetEnvironmentVariable($Target, "Machine")
    $NewPathvar = ($Pathvar.Split($PS) -ne $Path -join $PS).Trim($PS)
    $NewPathvar = $NewPathvar + $PS + $Path
    [Environment]::SetEnvironmentVariable( $Target, $NewPathvar, "Machine" )
}
```

Cet objet pourra vous aider si vous souhaitez faire évoluer vous-mêmes cette fonction.

```powershell
[System.io.directory]::Exists
```

Ces lignes égelement...

```powershell
$Env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + $PS + [System.Environment]::GetEnvironmentVariable("Path","User")  
```

### Suppression

```powershell
Function Remove-Path {
    Param(
        [Parameter(Mandatory=$false)]
        [ValidateSet("PATH", "PSModulePath")]
        [String]$Target = 'PATH',
        [Parameter(Mandatory=$true)]
        [String]$Path
    )
    $PS = [IO.Path]::PathSeparator
    $Pathvar = [Environment]::GetEnvironmentVariable($Target, "Machine") 
    $NewPathvar = ($Pathvar.Split($PS) -ne $Path -join $PS).Trim($PS)
    [Environment]::SetEnvironmentVariable( $Target, $NewPathvar, "Machine" )
}
```

