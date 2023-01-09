---
title: "NetSh, les commandes qui sauvent."
excerpt: "L'essentiel des commandes NetSh qui peuvent vous sauver quand PowerShell n'est pas là."
category: PowerShell
classes: wide
comments: true
tags: 
  - NetSH
  - Tips
  - WinHTTP
---

## Préambule


Un post un peu singulier puisqu'il ne vous ai pas vraiment destiné, c'est plus un post-it qu'un post.

## Commandes Winhttp proxy


La base, importer les pametres IE.

```
netsh winhttp import proxy source = ie
```


Exemple de configuration manuelle.

```
netsh winhttp set proxy proxy-server = "SRV-PROXY: PORT-PROXY" bypass-list = "192.168.1.*; *.home"
```


Afficher la configuration courante

```
netsh winhttp show proxy
```


Remise à 0.

```
netsh winhttp reset proxy
```


## Commandes interface


Utiles dans un WinPE depourvu de PowerShell.

Pour voir la configuration actuelle.

```
netsh interface ip show config
```


Configuration dhcp.

```
netsh interface ip set address "connection name" source=dhcp
netsh interface ip set dns "connection name" source=dhcp
```


Configuration static. Definition de l'adresse IP, Masque et passerelle.

```
netsh interface ip set address "connection name" static 192.168.0.101 255.255.255.0 192.168.0.1
```



Configuration des DNS

```
netsh interface ipv4 set dns name="connection name" static 4.4.4.4
ou
netsh interface ipv4 add dnsserver "connection name" address=8.8.8.8 index=2
```


Activer ou désactiver une carte réseau.

```
netsh interface set interface "connection name" enable/disable
```


Désactiver les interfaces virtuelles IPv6

```
netsh interface teredo set state disabled
netsh interface ipv6 6to4 set state state=disabled undoonstop=disabled
netsh interface ipv6 isatap set state state=disabled
```


Pour tout peter !

```
netsh winsock reset
netsh winhttp reset tracing
netsh winsock reset catalog
netsh int ip reset all
netsh int ipv4 reset catalog
netsh int ipv6 reset catalog
```

## Commandes advfirewall

Pour tout ouvrir :) (pare-feu)

```
netsh advfirewall set domainprofile state off
netsh advfirewall set privateprofile state off
netsh advfirewall set publicprofile state off
```


Pour afficher tous les profils de pare-feu

```
netsh advfirewall show allprofiles
```


Pour afficher la configuration globale du pare-feu

```
netsh advfirewall monitor show firewall
```

Ça, c'est pour moi, à plus !