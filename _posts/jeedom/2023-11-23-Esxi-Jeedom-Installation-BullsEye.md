---
title: "[Jeedom] Virtualiser sa box domotique sur Debian 11"
excerpt: "Installer Jeedom sous Esxi VM a l'aide de l'image officielle."
category: Jeedom
classes: wide
comments: true
tags: 
  - VMWare	
  - Esxi
  - Jeedom
header:
  teaser: /assets/images/2022-10-26-Esxi-Jeedom-Installation.webp
  image_description: "Jeedom sur Esxi"
---

Après un an de bon et loyaux service, Debian 10 {Buster) laisse sa place à Debian 11(BullsEye).

Le précédent article mérite un coup de frais, je reprends donc son contenu et vous le mets à jour.

## Le système d’exploitation pour Jeedom

Je pars du principe que votre serveur Esxi est déjà prêt. Ici nous allons survoler l'installation de Debian 11 préconfiguré par Jeedom SAS.

J'ai personnellement opté pour leur ISO plutôt que l'officielle Debian, ainsi je suis certain que Jeedom ne souffrira pas d'une mauvaise configuration de l'OS et que tous les prérequis seront présents.

Commencez par télécharger l’ISO sur le [repo de Jeedom](https://images.jeedom.com/x86-64/) et copiez-la sur le datastore de votre serveur Esxi.

## Créez la nouvelle machine virtuelle

Cliquez sur le bouton "Créer une machine virtuelle", puis sélectionnez l'option du même nom dans l'assistant.

> Jusque-là vous savez faire...

Ensuite, donnez un nom à votre VM. Ce sera également son nom d'hôte.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h30_33.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h30_33.webp" alt="Assistant Esxi Etape 2"></a>
  <figcaption>Sélectionnez un nom et un système d'exploitation.</figcaption>
</figure>

Sélectionnez le datastore qui hébergera la VM, il se peut que vous en ayez qu'un... je ne peux pas le savoir pour vous 😁

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h31_16.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h31_16.webp" alt="Assistant Esxi Etape 3"></a>
  <figcaption>Sélectionnez un stockage.</figcaption>
</figure>

> ❗Prenez le temps de lire cette partie.

Si votre serveur le permet, choisissez 2 vCPU. Moi je frime avec mon nuc, je l'ai choisi volontairement avec 4 cœurs afin de multiplier les contextes d'exécutions.
2 Go de RAM c'est bien.
Je recommande 16 Go de disque. Prévoyez assez parce que redimensionner un volume Linux, n'est pas simple quand on ne maitrise pas cet OS.
Enfin, profitez-en pour connecter le lecteur CD-ROM à l'ISO précédemment télé-versé sur votre datastore. N'omettez pas de cocher la case "Connecter lors de la mise sous tension"

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h33_44.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h33_44.webp" alt="Assistant Esxi Etape 4"></a>
  <figcaption>Personnalisez les paramètres.</figcaption>
</figure>

Terminez l'assistant et démarrez la VM pour passer à l'installation de l'OS

## Installation du système d'exploitation

Sélectionnez la seule option disponible.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h34_41.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h34_41.webp" alt="Install on disk"></a>
  <figcaption>Installation sur disque.</figcaption>
</figure>

Validez.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h35_00.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h35_00.webp" alt="Install Jeedom"></a>
  <figcaption>Installer Jeedom ? Yes !</figcaption>
</figure>

Il se peut que l'assistant d'installation vous signale cette erreur. Dans ce cas, validez.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h38_12.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_00h38_12.webp" alt="Install Jeedom"></a>
  <figcaption>Bam! une erreur !</figcaption>
</figure>

Patientez jusqu'a obtenir la mire de connexion.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h27_28.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h27_28.webp" alt="Install Installed"></a>
  <figcaption>Mire de connexion Jeedom.</figcaption>
</figure>

## Affinez l'installation

### Changer le layout du clavier

Pour la plupart d'entre vous, le clavier azerty francais c'est top, vous pouvez passer cette étape, pour les autres voici comment congigurer le clavier.

```bash
su -
dpkg-reconfigure keyboard-configuration
```

> ℹ️ dpg-reconfiure n'est accessible qu'en tant que root.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h42_03.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h42_03.webp" alt="keyboad layout"></a>
  <figcaption>Adaptez votre clavier</figcaption>
</figure>

### sudo

Partout vous verrez les commandes exécutées sur Linux commencer par "sudo". Cela permet d'héritez des privilèges root sans être root.

> En principe et pour des raisons de sécurité, le mot de passe root doit différer de celui du compte jeedom.

Bref, à l'aide de la console VMWare, connectez-vous en root avec pour mot de passe: Mjeedom96

Enfin exécutez cette commande pour autoriser le compte jeedom a utiliser sudo.

```bash
usermod -aG sudo mon_user
```

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_23h02_50.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_23h02_50.webp" alt="Enable sudo"></a>
  <figcaption>Déléguez les super-pouvoirs.</figcaption>
</figure>

### Mise à jour de debian

Récupérez la liste des mises à jour disponibles

```bash
sudo apt update
```

<figure style="width: 450px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_23h06_22.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_23h06_22.webp" alt="apt update"></a>
  <figcaption>sudo apt update.</figcaption>
</figure>

Puis lancez la mise à jour

```bash
sudo apt-get upgrade
```

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_23h09_42.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_23h09_42.webp" alt="apt-get upgrade"></a>
  <figcaption>sudo apt-get upgrade.</figcaption>
</figure>

### Installation du certicat

Pour l'accès externe il est nécessaire que l'url du site web de votre box domotique soit certifiée.

C'est une sécurité indispensable pour chiffrer le dialogue entre les appareils que vous utilisez pour vous connectez depuis l'extérieur et votre box.

Je ne vais pas tout reprendre, [Jeedomiser.fr](https://jeedomiser.fr/article/acceder-a-jeedom-depuis-lexterieur-et-en-https-avec-lets-encrypts-sur-son-raspberry-pi/) l'a déjà fait, mais voici les commandes de base.

Installez snap

```bash
sudo apt install snapd
sudo snap install core
sudo snap refresh core
sudo snap install --classic certbot
```

puis après avoir créer un lien symbolique pour appeler certbot, appelez certbot

```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --apache
```

Ne reste plus qu'a répondre aux questions.

<figure style="width: 450px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-24_22h55_15.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-24_22h55_15.webp" alt="Certbot"></a>
  <figcaption>Générez le certificat.</figcaption>
</figure>

### Installez les vmtools

Le matériel de votre machine virtuelle, bien que virtuel, a besoin de pilotes.

```bash
sudo apt-get install open-vm-tools
```

### Configurez la box en IP ststique

Fixer l'adresse IP permet d'éviter bien des déboires quand on a plus de dhcp...

Editez le fichier de configuration.

```bash
sudo nano /etc/network/interfaces
```

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-27_00h48_24.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-27_00h48_24.webp" alt="Ip static"></a>
  <figcaption>Fixez l'adresse IP.</figcaption>
</figure>

> Ne mettez pas les mêmes valeurs que moi ! mettez les vôtres ! copieurs !

## Compatibilité des plugin.

Le passage a cette nouvelle distro Debian ne sera pas sans conséquence.

En effet, Python 2 n'est plus et cela créer des adherences avec le module développé sur ce langage.

### JeeOrangeTV

Ce plugin ne fonctionnera plus du tout. Et il n'est plus maintenu.

Heureusement, une version beta était en cours de developpement et fonctionne avec Python 3.

### Google Cast

#### SocketClient' object has no attribute 'isAlive

>. Il suffit de remplacer isAlive() par is_alive() dans le fichier googlecast/resources/pychromecast/pychromecast/init.py ligne 444.

Après cela, votre Google Home pourra à nouveau parler.

#### protobuf

A ne pas confondre avec GCast de Jeedom.

C'était deja le cas avec Buster, le package protobuf a besoin d'etre rétrogradé pour être compatible avec Google Cast.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_01h23_26.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_01h23_26.webp" alt="Ip static"></a>
  <figcaption>Fixez l'adresse IP.</figcaption>
</figure>

Executez cette commande en ssh.

```bash
sudo pip install protobuf==3.20.*
```
2023-11-23_01h23_26.webp


<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_01h24_38.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-11-23_01h24_38.webp" alt="Ip static"></a>
  <figcaption>Fixez l'adresse IP.</figcaption>
</figure>


je vous dis à bientôt !
