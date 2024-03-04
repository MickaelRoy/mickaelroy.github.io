---
title: "Comment configurer votre propre serveur DHCP por remplace celui d'Orange."
excerpt: |
  Je vous décrypte la composition de l'option dhcp nécessaire au fonctionnent du décodeur Orange et à l'éradication du code erreur G03.
  
category: Hors-Series
classes: wide
comments: true
tags: 
  - orange
  - livebox
  - dhcp
---

## Préambule

Dans cet article je vous propose d'apprendre à contourner la restriction Orange qui vise à vous imposer les services dhcp de la Livbox.

## L'anecdote

> passez ce paragraphe si ma vie ne vous interesse pas.

Avant d'aller plus loin dans cet article il faut que je vous comte comment j'en suis arrivé là.

Il se trouve que je suis un abonné Orange depuis de nombreuses années. Free offre un service à la hauteur de mon besoin mais n'offre pas la fibre à mon adresse, SFR est à la rue, et bouygues... Bouygues, je les connais pour la télephonie mobile et c'est bien suffsant.

Bref, dans le cadre de la modification de mon contrat fibre, je me suis vu offrir la LiveBox 6 et son décodeur TV.

### le numéro ONT

En principe je n'avais qu'à brancher la nouvelle box en lieu et place de l'ancienne...

Plus facile à dire qu'à faire, la box est à installer verticalement, il faut donc prévoir sa place. Pour vous donner une idée, elle est plus haute qu'un NAS à deux ou quatre baies.

Une fois que c'est fait, ca ne fonctionne pas. Je ne suis pas un cas isolé, il m'a fallu appeler Orange pour leur communiquer le numero ONT de la box... comme s'ils ne pouvaient pas faire le necessaire en boutique...

> Bon, j'ai Internet, mais pas la télé... code G03

### Le décodeur (G03)

Après deux heures de diagnostique avec Orange, je suis basculé au service Orange PRO. En effet ma ligne migre en ligne PRO donc, les services changent et le support aussi.

Le support aux professionnels m'indique que je dois attendre quinze jours pour que ma ligne soit migrée.

On est pas loin du:

> Wesh frero patiente un peu. 

Après deux semaines à râler pour leur incapacité, toujours rien ! enfin si, toujours le code G03, "Box incompatible avec le décodeur".

Ma femme souhaiterais voir la "maison des maternelles", et elle ne peut pas (ou presque).

Je rappelle donc Orange, je passe au niveau 2 et une conseillère presque compétente m'indique (pour faire court) d'utilsez le dhcp de la box plutôt que le mien. 

> Les nouveaux décodeurs embarquent une sécurité plus aboutie qui impose l'usage du dhcp de la Box.

> Sérieusement? Merde

### Ce qu'ils ne disent pas

Il faut dire que j'étais pour le moins contrarié, mon serveur dhcp n'avait pas remplacé ceux de la Box par hasard.

Mon serveur permet entre autre de fournir les serveurs DNS publiques de mon choix: non filtré sur mes appareils, très restreint sur les appareils de mes enfants.

Cette démarche me permet également d'avoir une résolution de nom plus propre (c'est une autre drôle d'histoire).

Après une nuit de refexion, j'ai fini par douter de l'argument "sécurité de la Livebox". les services reseaux fournis par la box n'ont jamais évoulés. Je doute que le mot _sécurité_ soit approprié. D'autant que le service dhcp est une norme internationnale, je ne vois donc pas Orange ajouter une surcouche de _sécurité_.

> les cons, ils ont ajouté une option !

## Description de l'option 125

La LiveBox 6 doit fournir l'ip au décodeur pour lui fournir dans le même temps une option dhcp (supplémentaire).

Après avoir pas mal chiné, je suis tombé sur [cette page](https://forums.framboise314.fr/viewtopic.php?t=5960).

On y explique quelle est l'option et de quoi elle se compose.

<figure style="width: 640px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2024-02-13_21h17_04.webp" alt="">
  <figcaption>Description de l'option dhcp 125.</figcaption>
</figure> 

### Prérequis

En vous connectant à votre LiveBox depuis "Informations système" → "Internet", notez les informations suivantes:


<span style="color: #F59D56">Le modèle</span>.

<span style="color: #96B656">Le numéro de série</span>.

<span style="color: #527AAE">Les trois premies octets de l'adresse Mac</span>.

Retirez les ":" pour obtenir six caractères, et re-convertissez-les également en hexadécimal. (pourquoi faire simple ?)
{: .notice--warning}

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2024-02-14_22h51_27.webp" alt="">
  <figcaption>Les ingredients.</figcaption>
</figure> 

En suivant ce shema et en vous aidant d'un [converteur hexa en ligne](https://string-functions.com/string-hex.aspx) vous devriez pourvoir composer votre chaine.

### Exemples

#### Livebox 3:

<span style="font-family:consolas; font-size:0.65em;">00:00:0D:E9:2<span style="color: #BA4642">4</span>:04:06:<span style="color: #527AAE">01:23:45:67:89:AB</span>:05:0F:<span style="color: #96B656">41:42:43:44:45:30:31:32:33:34:35:36:37:38:39</span>:06:09:<span style="color: #F59D56">4C:69:76:65:62:6F:78:20:33</span></span>

#### Livebox 4:

<span style="font-family:consolas; font-size:0.65em;">00:00:0D:E9:2<span style="color: #BA4642">4</span>:04:06:<span style="color: #527AAE">01:23:45:67:89:AB</span>:05:0F:<span style="color: #96B656">41:42:43:44:45:30:31:32:33:34:35:36:37:38:39</span>:06:09:<span style="color: #F59D56">4C:69:76:65:62:6F:78:20:34</span></span>

#### Livebox 5 (estampillée Fibre):

<span style="font-family:consolas; font-size:0.65em;">00:00:0D:E9:2<span style="color: #BA4642">8</span>:04:06:<span style="color: #527AAE">01:23:45:67:89:AB</span>:05:0F:<span style="color: #96B656">41:42:43:44:45:30:31:32:33:34:35:36:37:38:39</span>:06:0D:<span style="color: #F59D56">4C:69:76:65:62:6F:78:20:46:69:62:72:65</span></span>

#### Livebox 6

<span style="font-family:consolas; font-size:0.65em;">00:00:0D:E9:2<span style="color: #BA4642">4</span>:04:06:<span style="color: #527AAE">01:23:45:67:89:AB</span>:05:0F:<span style="color: #96B656">41:42:43:44:45:30:31:32:33:34:35:36:37:38:39</span>:06:09<span style="color: #F59D56">:4C:69:76:65:62:6F:78:20:36</span></span>

Profitez de ce [convertisseur Hexa inversé](https://string-functions.com/hex-string.aspx) pour vérifier votre chaine
{: .notice--info}

## Configuration dhcp (windows)

Je suis certains que les linux savent comment configurer leur dhcp. Passons à la suite pour les noob sur windows.

Commencez par déclarer l'option sur le scope, ainsi elle sera disponible pour être utilisée lors de la création de la réservation du bail du décodeur.

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2024-02-11_10h34_11.webp" alt="">
</figure> 

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2024-02-11_10h39_55.webp" alt="">
</figure> 

L'option dhcp créée devrait ressembler à ceci; En moins floue.

<figure style="width: 320px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/2024-02-14_23h53_13.webp" alt="">
</figure> 

> Je me rends compte que j'ai un peu survoler l'aspect administration Windows. clic clic toussa toussa... mais faudrait pas passer pour des idiots face aux linuxiens.

Voilà de quoi résoudre votre code G03, s'il est toujours là c'est que votre chaine binaire est incorrecte.

> Bon Chance !