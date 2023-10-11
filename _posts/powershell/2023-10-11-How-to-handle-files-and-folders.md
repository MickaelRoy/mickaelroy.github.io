---
title: "Comment manipuler les fichiers et les dossiers."
excerpt: "Manipuler les données en PowerShell, mais avec classe!"
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
---

## Préambule

Honte à moi j'ai jugé !

Initialement posté sur une page LinkedIn, sur le channel "PowerShell Power User", rien que ca... C'est depuis le fond de mon lit d'hopital que je suis tombé sur ce vlog 🤮.

D'ordinaire j'ardore lire ce channel. On y trouve souvent des gens créatifs, inventifs offrant des enseignements instructifs et constructifs.

Ici ce n'est pas le cas, sinon je ne me serais pas levé après 5 jours d'hospitalisation pour ouvrir mon laptop et compter ces mots.

{% include video id="uxoYSVLIhXbjnnXv" provider="youtube" %}

## Palabrons

Vous me pardonnerez le ton condescendant que j'emploie (ou pas), ma chère épouse vous dirait que c'est un trait de la pathologie dont je souffre.

Que je vous explique tout de même ma frustration. (ou passez ces quelques lignes si celle-ci vous en touche une sans secouer l'autre comme le dit notre cher E. Macron).

Quand je tombe sur un article sur LinkedIn, en vrai, je ne m'attends plus à rien. Ce réseau social a la mediocrité intellectuelle d'une classe de 6e; les individus qui s'y expriment pensent avoir atteint le niveau de maturité requis pour s'autoriser à pousser des "coups de gueules" et s'attendent a ce que toute l'attendance absorbe leurs mots le visage médusé comme s'ils provenaient du plus illustre des sages. 

On les reconnait facilement, ils commencent leur allocution par ces mots, "Coup de gueule !", suivis de trois retour à la ligne (l'effet teasing sans doute), débutent un petit prologue pour excuser leur attitude (ca n'arrive jamais mais quand c'est trop,c'est trop!) et poursuivent sur un monologue basé sur quelques bloc-paragraphes debutant tous par un emoji comme pour se donner du crédit avec une belle illustration... enfin... tout de même un emoji quoi...

d'un coté leurs compaires se féliciterons plus tard d'avoir appuyé ou contre-dit l'auteur... quand aux autres, ceux qui pourraient prétendre avoir le niveau de maturité quant a eux, feront mine de ne pas avoir lu, et n'ont de toute facon aucune envie de s'exposer sur un réseau social dit "proffesionnel" et voir leur carrière sabotée après un commentaire potentiellement mal interpreté.

Personnellement quand j'ai du temps à tuer, et en quantité suffisante pour m'_attarder_ (comprenez ce mot au 1er degré) sur LinkedIn, il y a des posts sur les quels je prends le temps. Ceux qui concernent PowerShell en font partis.

Dans __PowerShell Power User__, j'entends __Power__ user. Pas besoin de d'avoir un diplôme en langues étrangères pour comprendre. Donc, quand je prends le temps sur un post PowerShell j'attends un peu de __Power__ et ne souhaite pas juste m'_attarder_, un peu plus que je ne le suis déjà. (ca y est t'as compris le jeu de mot ?)


## Entrons dans le vif

(je ne sais pas si je finirai mon article ce soir, j'ai vraiment mal au crâne.)
(non, ce n'est pas lié à ma prise de tête et/ou de conscience.)

Reprenons, dans la video relative à ma frustration, on vous explique ce qui est écrit a-peu-pret dans tous les manuels, y compris [celui-ci:https://learn.microsoft.com/en-us/powershell/scripting/samples/working-with-files-and-folders]. (un éditeur inconnu et mal compris)

Serieusement, Youtube est une source d'information inépuisable, j'ai hâte de voir les ramparts de sa prochaine video -how to eat your soup with _the help of_ a spoon- 😒

Bon, vous n'apprendrez rien ce soir avec mon blog, mais restons sur une note constructive ! 😁

Il y a une notion sur laquelle ce youtubeur fou aurait peut être dû ralentir. En effet, une copie de fichiers recurcive ou non, n'impose pas l'usage Copy-Item. J'en montre l'exemple souvent. Par contre, la notion de copie distante le conseille vivement.

Et oui, même si pour ma part je sais copier de petites quantités de données via un PsSession (c'est déjà chiant), un procédé qui m'était imposé sur les plate-formes PowerShell 4 et inférieur, copier de données volumineuses m'était alors impossible sans montage de partage réseaux.

Ce qu'il faut retenir, c'est que PowerShell 5 et 5.1 nous on fait atteindre un point culiminant en ce qui concerne l'inter-opérabilité Windows.



> Coup de gueule !

Vous êtes encore là ?

bon un petit cadeau avant de partir alors.

C'est un truc tellement bête, mais je le trouve pratique

```poweershell
Try {
    Write-host -NoNewline "Execution du sleep 5 sec: "
    Start-Sleep 5
    Write-Host -ForegroundColor Green "OK"
} Catch {
    Write-Host -ForegroundColor Red "NOK"
}
```
Et voilà ce que ca donne:

Quand ca fonctionne:

<figure class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h36_22.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h36_22.png" alt="ca marche."></a>
  <figcaption>Les performances parlent d'elles-mêmes.</figcaption>
</figure>


Quand ca ne fonctionne pas:

<figure class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h37_19.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h37_19.png" alt="ca marche pas."></a>
  <figcaption>Les performances parlent d'elles-mêmes.</figcaption>
</figure>

