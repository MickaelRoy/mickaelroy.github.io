---
title: "[Jeedom] Virtualiser sa box domotique sur Debian 10"
excerpt: |
  Installez Jeedom sous Esxi à l'aide de l'image officielle basée sur Debian 10 Buster.
  
category: Jeedom
classes: wide
comments: true
tags: 
  - VMWare	
  - Esxi
  - Jeedom
  - Debian
header:
  teaser: /assets/images/2022-10-26-Esxi-Jeedom-Installation.webp
  image_description: "Jeedom sur Esxi"
---

## Le système d’exploitation pour Jeedom

Je pars du principe que votre serveur Esxi est déjà prêt. Ici nous allons survoler l'installation de Debian Buster 10 préconfiguré par Jeedom SAS.

J'ai personnellement opté pour leur ISO plutôt que l'officielle Debian, ainsi je suis certain que Jeedom ne souffrira pas d'une mauvaise configuration de l'OS.

En plus, le sujet est déjà couvert chez [DomoBlog](https://www.domo-blog.fr/virtualisation-installer-jeedom-debian-9-esxi-vm/) et personnellement, vu la quantité d'étapes... c'est chiant. Je n'ai pas le temps. 😅

Commencez donc par télécharger l’ISO sur le [repo de Jeedom](https://images.jeedom.com/x86-64/) et copiez la sur le datastore de votre serveur Esxi.

## Créez la nouvelle machine virtuelle

Cliquez sur le bouton "Créer une machine virtuelle", puis sélectionnez l'option du même nom dans l'assistant.

> Jusque-là vous savez faire...

Ensuite, donnez un nom à votre VM. Ce sera également son nom d'hôte.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_21h39_48.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_21h39_48.webp" alt="Assistant Esxi Etape 2"></a>
  <figcaption>Sélectionnez un nom et un système d'exploitation.</figcaption>
</figure>

Sélectionnez le datastore qui hébergera la VM, il se peut que vous en ayez qu'un... je ne peux pas le savoir pour vous 😁

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_21h41_44.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_21h41_44.webp" alt="Assistant Esxi Etape 3"></a>
  <figcaption>Sélectionnez un stockage.</figcaption>
</figure>

> ❗Prenez le temps de lire cette partie.

Si votre mini pc (barebone) le permet, choisissez 2 vCPU. Moi je frime avec mon nuc, je l'ai choisi volontairement avec 4 cœurs afin de multiplier les contextes d'exécutions.

2 Go de RAM c'est bien.

12 Go de disque est le minimum. Sorti de boite, l'installation de Jeedom occupe 7 Go. Prévoyez assez parce que redimensionner un volume Linux, n'est pas simple quand on ne maitrise pas cet OS.

Enfin, profitez-en pour connecter le lecteur CD-ROM à l'ISO précédemment télé-versé sur votre datastore. N'omettez pas de cocher la case "Connecter lors de la mise sous tension"

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_21h44_27.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_21h44_27.webp" alt="Assistant Esxi Etape 4"></a>
  <figcaption>Personnalisez les paramètres.</figcaption>
</figure>

Terminez l'assistant et démarrez la VM pour passer à l'installation de l'OS

## Installation du système d'exploitation

Sélectionnez la seule option disponible.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h22_52.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h22_52.webp" alt="Install on disk"></a>
  <figcaption>Installation sur disque.</figcaption>
</figure>

Validez.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h23_15.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h23_15.webp" alt="Install Jeedom"></a>
  <figcaption>Installer Jeedom ? Yes !</figcaption>
</figure>

Patientez jusqu'a obtenir la mire de connexion.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h27_28.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2022-10-23_22h27_28.webp" alt="Install Installed"></a>
  <figcaption>Mire de connexion Jeedom.</figcaption>
</figure>

## Affinez l'installation

### Changer le layout du clavier

Pour la plupart d'entre vous, le clavier azerty c'est top.

Mais pour ceux qui sont habitués à un autre layout...

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

Bref. à l'aide de la console VMWare, connectez-vous en root avec pour mot de passe: Mjeedom96

Enfin exécutez cette commande pour autoriser le compte jeedom a utiliser sudo

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

## Conclusion

Voilà, on a abordé la plupart des sujets pour mettre en production votre box domotique.
J'espère que c'était bref mais clair.

je vous dis à bientôt !
