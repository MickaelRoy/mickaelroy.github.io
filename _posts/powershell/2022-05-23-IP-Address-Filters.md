---
title:  "PowerShell: Manipuler les informations réseau."
excerpt: "Convertion d'IP, masque, en valeur binaire ou decimale et vice et versa."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Réseau
  - Filtre
  - Conversion
---

# Intro

Voici une liste de filtres (ce ne sont pas vraiment des fonctions) qui permettent de manipuler les informations réseaux; addresse IP et masque de sous reseau.

## Le plus utile tout de suite

On ne va pas se mentir, les autres filtres ne m'ont servi qu'une fois en 7 ans.
Je vous donne le plus utile tout de suite, ne perdons pas de temps.

### Convertir une longueur de sous-réseau en masque.

```powershell
Filter ConvertTo-LengthFromIPMask { ([Convert]::ToString(([IPAddress][String]([IPAddress]$_).Address).Address,2) -replace '[\s0]' ).Length }
```

```
PS C:\> "255.255.255.0" | ConvertTo-LengthFromIPMask
24
```

## Travailler avec les decimaux

```
PS C:\> $Interface = [System.Net.NetworkInformation.NetworkInterface]::GetAllNetworkInterfaces()[3]
PS C:\> $Interface.GetIPProperties().UnicastAddresses[1].Address


Address            : 2432805056
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 192.168.1.145
```

### Convertir une IP en decimal
```powershell
Filter ConvertTo-DecimalFromIP { ([IPAddress][String]([IPAddress]$_)).Address }
```

```
PS C:\Users\amena> "192.168.1.145" | ConvertTo-DecimalFromIP
2432805056
```

### Convertir un decimal en IP

```powershell
Filter ConvertTo-IPFromDecimal { ([System.Net.IPAddress]$_).IPAddressToString }
```

La même idée, mais à l'envers.

## Tavailler avec les binaires

Pour ceux d'entres vous qui n'ont pas seché les cours de réseau, vous le savez: la base de calcul en ipv4 est le binaire.
Cela peut donc avoir un interet de convertir une adresse en serie de 1 et de 0.

Nan, à la relecture du sujet, cela n'a pas d'intérêt... mais je trouve l'exercice amusant.

### Convertir une IP en binaire

```powershell
Filter ConvertTo-BinaryFromIP { [Convert]::toString(([IPAddress][String]([IPAddress]$_).Address).Address,2) }
```

```
PS C:\> "192.168.1.145" | ConvertTo-BinaryFromIP
11000000101010000000000110010001
```

### Convertir un binaire en IP

```powershell
Filter ConvertTo-IPFromBinary { ([System.Net.IPAddress]"$([System.Convert]::ToInt64($_,2))").IPAddressToString }
```

```
PS C:\> '11000000101010000000000110010001' | ConvertTo-IPFromBinary
192.168.1.145
```

### Convertir un binaire en une longueur de sous-réseau

```powershell
Filter ConvertTo-BinaryFromLength { ("1" * $_).PadRight(32, "0") }
```

```
PS C:\> "255.255.255.0" | ConvertTo-BinaryFromIP
11111111111111111111111100000000
```

### Convertir une longueur de sous-réseau en binaire.

```powershell
Filter ConvertTo-LengthFromBinary { [Convert]::ToInt32(($_ -replace('[\s0]')).Length) }
```

```
PS C:\>  '11111111111111111111111100000000' | ConvertTo-LengthFromBinary 
24
```

## Bonus

Je félicite celui qui est encore là... il ne sera peut être pas venu pour rien.

### Convertir une longueur de sous-réseau en masque.

Parce que, oui, un masque de sous-réseau a le même format qu'une IP. Incroyable!

```
PS C:\>  24 | ConvertTo-BinaryFromLength | ConvertTo-IPFromBinary 
255.255.255.0
```

Celui là, c'est cadeau, à plus !