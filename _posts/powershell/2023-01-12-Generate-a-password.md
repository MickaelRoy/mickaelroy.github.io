---
title: "Générez des mots de passe aléatoires."
excerpt: |
  Découvrez une fonction PowerShell amusante et utile pour créer des mots de passe aléatoires de manière personnalisée.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Cmdlet
  - Tips
  - Password
  - Random
header:
  teaser: /assets/images/2023-01-12_23h19_22.webp
  image_description: "Générateur de mots de passe aléatoires."
---

Imaginez-vous en train de créer un compte sur un nouveau site Web et de devoir inventer un mot de passe.

Vous avez envie de quelque chose de sécurisé, mais aussi de mémorable. C'est là que notre fonction entre en jeu !

## Introduction

Ce n'est pas mon premier code qui génère un mot de passe, et encore moins le premier code a le faire tout court. Mais l'exercice merite d'être fait et refait par les debutants et les confirmés.

0x1001 facon de traiter le sujet. En voila une de plus.


## La fonction Get-RandomPassword

Voici une fonction PowerShell élégante pour générer des mots de passe aléatoires.

Avec Get-RandomPassword, vous pouvez spécifier la longueur du mot de passe, le nombre maximum de caractères spéciaux, et même exclure certaines catégories de caractères comme les majuscules, les minuscules, les chiffres, ou les caractères spéciaux.

```powershell
function Get-RandomPassword {
    param (
        [Parameter(Mandatory)]
        [ValidateRange(4,[int]::MaxValue)]
        [int] $Length,
        [int] $MaxSpecial = 1,
        [String] $SpecialPattern = "()_[]-,./?\",
        [Switch] $ShowCharSet,
        [Switch] $NoUpper,
        [Switch] $NoLower,
        [Switch] $NoNumeric,
        [Switch] $NoSpecial

    )

    # les caractères autorisés.
    $CharSet = ""
    $uCharSet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    $lCharSet = "abcdefghijklmnopqrstuvwxyz"
    $nCharSet = "0123456789"
    $sCharSet = $SpecialPattern

    If ($MaxSpecial -gt 0 -and (!$NoSpecial)) { $CharSet += $sCharSet }
    If (!$NoUpper) { $CharSet += $uCharSet }
    If (!$NoLower) { $CharSet += $lCharSet }
    If (!$NoNumeric) { $CharSet += $nCharSet }

    $CharSet = $CharSet.ToCharArray()

    # Pour visualiser les caratères autorisés.
    If ($ShowCharSet) { Return (-join $CharSet) }

    # Creation d'une table d'octet vide de la longueur du mot de passe.
    $bytes = [byte[]]::new($length)

    # Implémente un générateur de nombres aléatoires (RNG) par chiffrement à partir de l'implémentation fournie par le fournisseur de services de chiffrement (CSP)
    # En gros ca rempli $byte de chiffre aléatoire...
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::new()
    $rng.GetBytes($bytes)
 
    # Creation d'une table de caractères de la longeur du mot de passe.
    $result = [char[]]::new($length)
 
    # Represente le nombre de fois qu'un caractère special est inséré dans le mot de passe.
    $schar = 0 

    # Boucle sur chaque caractère du mot de passe.
    For ($i = 0 ; $i -lt $length ; $i++) {
        
        # Utilisation de la fonction mathematique modulo, pour que le choix de caracter aux limites de imposées par le charset.
        # si le charset ne dispose que de 73 caracteres, le modulo (reste de la division) ne peut être qu'inferieur à 73
        $Char = $charSet[$bytes[$i] % $charSet.Length]

        # On incrémente à chaque fois qu'un caractère special passe.
        If ($Char -in $sCharSet.ToCharArray()) { $schar++ ; Write-Verbose "$Char found Special count: $sChar/$MaxSpecial" }

        # Si on a attent le nombre maximum de caractères speciaux, on les retire du charset
        If ($schar -ge $MaxSpecial) { $charSet = $charSet | ? { $_ -notin  $sCharSet.ToCharArray() }; Write-Verbose "MaxSpecial reached"}

        # On place le caractère généré dans la variable result les uns à la suite des autres.
        $result[$i] = $Char
    }

    # pour le fun, on remelange le tout !
    $result = $result | Get-Random -Count $result.Length 
    
    # On colle le tout pour en faire une chaine de caractère.
    $password = (-join $result)

    # Et on retourne le mot de passe
    Return $password
}
```

```powershell
Get-RandomPassword -Length 12 -MaxSpecial 2 -ShowCharSet
```
Exécutez cette commande et voyez par vous-même la magie opérer !


<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-01-12_23h19_22.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-01-12_23h19_22.webp" alt="3 exemples."></a>
  <figcaption>Générez des mots de passes.</figcaption>
</figure>

## Conclusion

Avec Get-RandomPassword, créer des mots de passe n'a jamais été aussi facile ! Vous pouvez l'intégrer dans vos scripts PowerShell pour améliorer la sécurité de vos applications et vous assurer que vos utilisateurs disposent de mots de passe solides et uniques.

Alors, qu'attendez-vous ? Essayez dès maintenant cette fonction et impressionnez vos collègues avec vos mots de passe dignes d'un génie de l'informatique !

Ça, c'est cadeau, à plus !