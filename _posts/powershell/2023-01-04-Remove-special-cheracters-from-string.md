---
title: "Retirer les caractères speciaux d'une chaine."
excerpt: "Trois approche pour nettoyer une chaine de caratères."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - RegEx
header:
  teaser: /assets/images/2023-01-06_21h57_35.webp
  image_description: "Apercu du code."
---

## Préambule

En cherchant toute autre chose, sachez que je ne suis pas l'auteur de cette page, l'originale se trouve sur ce [blog](https://lazywinadmin.com/2015/08/powershell-remove-special-characters.html).

Une astuce fort pratique pour manipuler des chaines de caractères tout en abordant differents aspects et notamment les *RegEx* (Prononcez Reguex, puisque le terme est la contraction de *Regular Expression*)


## Entrons dans le vif

### Une approche RegEx

```powershell
$String = "François-Xavier!?!#@$%^&*()_+\|}{○<>??/ €$¥£¢ \^$.|?*+()[{ 0123456789"
```

#### Meta-caractère \W

```powershell
# Regular Expression - Using the \W (opposite of \w)
PS> $String -replace '[\W]', ''
FrançoisXavier_0123456789
```
Dans une RegEx `\w` signifie un mot, une chaine composée de caractères alphanumérique `a-z`, `A-Z`, `0-9` incluant le `_`. `\W` est sont exacte opposé.

> celui qui a pensé "Le tiret du 8", il peut sortir.

On note que le `ç` a été concervé. En effet, nous avons remplacé tout ce qui n'était pas un caratère alphanumérique mais, le `C cedille` est un caractère alphanumerique *accentué* *si je puis dire.


### La plage [^a-zA-Z0-9]

```powershell
# Regular Expression - Using characters from a-z, A-Z, 0-9
PS> $String -replace '[^a-zA-Z0-9]', ''
FranoisXavier0123456789
```
> ℹ️ `^` dans une expression entre crochets inverse sa signification, un peu comme un `-not` en PowerShell.

Cela fonctionne bien, et le `C cédille` a été supprimé.


### ASCII Ranges

```powershell
# Regular Expression - Using ASCII
#  See http://www.asciitable.com/
PS> $String -replace '[^\x30-\x39\x41-\x5A\x61-\x7A]+', ''
FranoisXavier0123456789
```

Même resultat que précédement. On utilise les plages de caractère ASCII pour la frime.

### UNICODE Specific Code Point

```powershell
# Regular Expression - Unicode - Matching Specific Code Points
# See http://unicode-table.com/en/
PS> $String -replace '[^\u0030-\u0039\u0041-\u005A\u0061-\u007A]+', ''
FranoisXavier0123456789
```

Bon... 

### Categories UNICODE (Ce que l'auteur utilise dans son code)

```powershell
# Regular Expression - Unicode - Unicode Categories
PS> $String -replace '[^\p{L}\p{Nd}]', ''
FrançoisXavier0123456789
```

Chaque caractère Unicode appartient à une certaine catégorie. Vous pouvez faire correspondre un seul caractère appartenant à la catégorie "lettre" avec `\p{L}`. Idem pour les nombres, vous pouvez utiliser `\p{Nd}` pour les décimales.

D'autres exemples sympas tels que `\p{N}` pour tout type de nombres, `\p{Nl}` pour un nombre qui ressemble à une lettre, comme un chiffre romain et enfin `\p{No}` pour un chiffre en exposant ou en indice, ou un nombre qui n'est pas un chiffre "0-9".

Je trouve très intelligente la methode, surtout pour un générateur de mot de passe.

Ça, c'est cadeau, à plus !