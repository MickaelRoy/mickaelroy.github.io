﻿---
title:  "Bienvenue sur mon blog!"
excerpt: |
  Optimisation de code, test et booleens.
  
category: PowerShell
comments: true
tags: 
  - PowerShell
  - Tips
---

# Tips de bienvenue

**Bonjour tout le monde !** ceci est mon premier post avec un vrai contenu.

```powershell
If ($Null -ne "String") {
  $result = $True
} Else { 
  $result = $False
}
```

C'est une syntaxe que j'ai lu dans le code d'un collegue...

Ma foi, voila qui est un peu lourd...

Essayons ceci:

```powershell
  $result = ($Null -ne "String")
```

On vient de diviser par 4 le nombre de ligne de code.

De quoi alléger la lecture, et épater les copains !

Ca, c'est cadeau, à plus !

![First tips image]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-15-First-tips.gif)