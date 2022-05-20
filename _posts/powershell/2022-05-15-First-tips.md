---
title:  "Bienvenu sur mon blog!"
category: Powershell
tags: 
  - Powershell
  - Tips
---

# Welcome

**Bonjour tout le monde !**, Ceci est mon premier post avec un vrai contenu.

```powershell
If ($null -ne "String") {
  $result = $True
} 
ELse { $result = $false }

```

C'est une syntaxe que j'ai lu dans le code d'un collegue...
Ma foi, voila qui est un peu lourd...

Essayons ceci:

```powershell
  $result = ($null -ne "String")

```
