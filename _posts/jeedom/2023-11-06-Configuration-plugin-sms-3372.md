---
title: "[Jeedom] Utiliser la clé huawei 3372"
excerpt: "Il est pour moi indispensable de pouvoir faire communiquer jeedom autrement que par les cannaux classique. Les sms est une excelente alternative si votre box internet ne fonctionne plus."
category: Jeedom
classes: wide
comments: true
tags: 
  - Jeedom
  - sms
  - huawei
---

## Préambule

Dans cet article je vais reprendre les notes d'akenad concernant l'usage de clé Huawei 3372 (la mienne)

Je vous renvois également sur l'article [Youdom](https://youdom.net/flashez-sa-cle-3g-huawei-e3372s-153/). Contrairement ce qui est écrit j'en suis l'auteur...

## Ce que dit Akenad

__"Flash"__ Plugin SMS et clé Huawei E3372 ou E3531 :

/!\ ATTENTION : utilisez une version d’origine et non pas fourni via opérateur ou en marque blanche, car le firmware a été probablement modifié et.ou bridé.

Je (Akenad) recommande de ne pas modifier le firmware.

Configuration du système pour envoyer des SMS avec le plugin SMS:

pour la JeedomSmart (en Stretch) :
Je préconise l’utilisation de la E3372 (s-153 ou h-153), la clé est reconnue par défaut.
(/!\ ATTENTION : je n’ai pas connaissance que le modèle E3372h-320 et E3372h-607 soient reconnues par défaut).

Dans d’autres cas comme la Smart en Buster, Odroid-C2 Armbian, Pi3 Raspbian ou Pi4 PiOS-64, VM Debian amd64,
en root, vérifier que le paquet usb-modeswitch est installé, sinon l’installer et :

Pour la E3372 (s-153 ou h-153, mais pas h-320 ou h-607):

Créez le fichier /etc/usb_modeswitch.d/12d1:1f01 avec comme contenu :

```
# Huawei E3372s-153 ou E3372h-153 switch mode vers 12d1:1442 (mode GSM modem) :
TargetVendor=0x12d1
Targetproduct=0x1f01
MessageContent="55534243000000000000000000000011060000000000000000000000000000"
```

Pour la E3531 :

Créez le fichier /etc/usb_modeswitch.d/12d1:1f01 avec comme contenu :

```
# Huawei E3531i-2 ou E3531s-2 switch mode vers 12d1:1001 (mode GSM modem, 3 ports)
TargetVendor=0x12d1
Targetproduct=0x1f01
MessageContent="55534243123456780000000000000011062000000100000000000000000000"
```

Puis débranchez et rebranchez la clé.

Le switch de 12d1:1f01 vers le mode GSM modem est visible avec la commande :

```
dmesg | grep USB
```

Le résultat du switch est visible par la commande :

```
lsusb
```

L’affectation des ttyUSBx pour la clé HUAWEI est visible par la commande :

```
ls -l /dev/serial/by-id
```

Utilisez le premier port.

La documentation Jeedom officielle du #plugin-sms est [ici](https://doc.jeedom.com/fr_FR/plugins/communication/sms/)

## Ce que moi je dis.

Je n'ai pas reussi à faire fonctionner la version 320 de la clé. 

J'ai dû flasher le firmware de la clé 153 tel que je l'indique dans l'article Youdom. C'est une douleur.

L'ajout du fichier /etc/usb_modeswitch.d/12d1:1f01 est indispendable et fonctionne très bien même sur Bullseye fourni par Jeedom. Ne prétez pas attention à l'Id de la clé, chez moi elle est reconnue à la base sous l'id 12d1:1d46.

A titre indicatif, ce contenu fonctionne très bien pour la 3372.
```
DefaultVendor= 0x12d1
DefaultProduct=0x1f01

# Huawei E353 (3.se) and others
# Switch from default mass storage device mode 12d1:1f01 to ...
TargetVendor=0x12d1

# WWAN mode 12d1:155e
#TargetProduct=0x155e
#MessageContent="55534243123456780000000000000011063000000100010000000000000000"

# Broadband modem mode 12d1:1442
TargetProduct=0x1442
MessageContent="55534243000000000000000000000011060000000000000000000000000000"

# "ethernet" mode
#TargetProductList="14db,14dc"
#HuaweiNewMode=1
```

Enfin... et là Akenad a oublié de nous le dire dans ses tutos... Il se peut qu'il manque les fichiers rules, notemment si vous avez un Debian officiel non customisé par Jeedom.

Dans mon cas, avec le Debien Bullseye fourni par Jeedom, le fichier existe et contient une ligne "fourre-tout".

```
# Generic entry for most Huawei devices, excluding Android phones
ATTRS{idVendor}=="12d1", ATTRS{manufacturer}!="Android", ATTR{bInterfaceNumber}=="00", ATTR{bInterfaceClass}=="08", RUN>
```

Si ce n'est pas le cas chez vous, créez, ou éditez le fichier /lib/udev/rules.d/40-usb_modeswitch.rules pour qu'il contienne:

```
# Huawei 3531-i2
ATTR{idVendor}=="12d1", ATTR{idProduct}=="1f01", RUN+="/usr/sbin/usb_modeswitch -c /etc/usb_modeswitch.d/12d1:1f01"
```

et à la fin du fichier, en derniere ligne:

```
LABEL="modeswitch_rules_end"
```

> bon chance !