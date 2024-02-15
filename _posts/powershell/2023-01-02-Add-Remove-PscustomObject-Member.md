---
title: "Ajouter et supprimer un membre à un PSCustomObject."
excerpt: |
  Add-Member n'est pas indispensable, et Remove-Member pourrait être écrit.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
header:
  teaser: /assets/images/2023-01-02_13h21_54.webp
  image_description: "Apercu du code."
---

## Préambule

En cherchant toute autre chose, je suis tombé il y a peu sur un collegue américain qui présente un [blog](https://lazywinadmin.com/) semblable a celui-ci.

Dans ce [blog](https://lazywinadmin.com/) il fait part d'une [astuce](https://lazywinadmin.com/2017/06/Remove_PSObject_Property.html) que je ne vous avais pas encore présenté.

Dans ce [blog](https://lazywinadmin.com/) il dit ceci

> Someone at work was recently asking me about the following: How to remove a property from a PowerShell Object ?

En effet, la plupart d'entre vous ont déjà abordé l'ajout d'une propriété a un object [PsOject] ou, [PSCustomObject] notemment avec la cmdlet Add-Member. Si tel est votre cas, cher lecteur, vous avez pu remarquer qu'il n'existe pas de Remove-Member.

## Entrons dans le vif

### Création de l'objet

```powershell
$MyObject = New-Object -Typename PSCustomObject -Property @{
    ComputerName = $env:ComputerName
    MacAddress = '00:11:22:33:44:55'
    Description = 'My Computer'
}
```

l'object $MyObject donne ceci

```
ComputerName    Description MacAddress
------------    ----------- ----------
DESKTOP-HPHHRC5 My Computer 00:11:22:33:44:55
```

Top ! 

Pour retirer une propriété, voici comment:

```powershell
$MyObject.PSObject.properties.remove('Description')
```

Ce qui donne:

```
ComputerName    MacAddress
------------    ----------
DESKTOP-HPHHRC5 00:11:22:33:44:55
```

> Génial Mickael, tu as reussi à copier le contenu d'une page d'un autre blog. Leaker !

### Allons plus loin

Si l'on peut retirer une propriété avec la methode Remove de la methode Properties de la methode PsObject...

Est ce que l'on peut ajouter avec la methode Add de la methode Properties de la methode PsObject...?

YES ! on peut !

```powershell
$MyObject.psobject.properties.Add( [psnoteproperty]::new('IpAddress', '192.168.1.1') )
```

Et voilà !

```
ComputerName MacAddress        IpAddress  
------------ ----------        ---------  
LATITUDE     00:11:22:33:44:55 192.168.1.1
```

Vous noterez que le resultat est identique à celui produit par Add-Member. Add-Member n'a d'utilité que pour faire l'opération sur un objet itérable, tel qu'un [Array] ou [System.Collections.ArrayList]... En clair, si $MyObject contient plus d'un objet.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-01-02_13h21_54.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-01-02_13h21_54.webp" alt="Add-Remove member demo."></a>
  <figcaption>Voici ce que cela donne dans sa globalité.</figcaption>
</figure>

Ça, c'est cadeau, à plus !