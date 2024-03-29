---
title: "Automatiser le téléchargement de fichier via un proxy McAfee."
excerpt: |
    Cette page fournit des informations sur la fonction Get-McAfeeFilteredWebContent, utilisée pour récupérer le contenu web filtré par McAfee Web Gateway.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - McAfee
---

## Description

La fonction Find-StringInGpo permet de rechercher une chaîne spécifique dans les objets de Stratégie de Groupe (GPO) de votre environnement Active Directory. 

Vous pouvez spécifier la chaîne à rechercher ainsi que l'emplacement à partir duquel effectuer la recherche.

## Exemples

```powershell
    Find-StringInGpo -String "Audit"
```

    Recherche la chaîne "Audit" dans tous les GPOs du domaine actuel.

```powershell
    Find-StringInGpo -String "PasswordPolicy" -SearchBase "OU=Security Policies,DC=contoso,DC=com"
```

    Recherche la chaîne "PasswordPolicy" dans les GPOs situés dans l'unité d'organisation "Security Policies" de domaine contoso.com.

```powershell
    Find-StringInGpo -String "Audit" -All
```

    Recherche la chaîne "Audit" dans tous les GPOs du domaine actuel.

Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Get-McAfeeWebContent){: .btn .btn--info}