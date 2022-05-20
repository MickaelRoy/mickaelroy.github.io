---
title:  "Bienvenu sur mon blog!"
category: Powershell
tags: 
  - Powershell
  - Tips
---

# Tips de bienvenue

**Bonjour tout le monde !** ceci est mon premier post avec un vrai contenu.

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

On vient de diviser par 4 le nombre de ligne de code.

De quoi alléger la lecture, et épater les copains !

Ca, c'est cadeau, a plus !