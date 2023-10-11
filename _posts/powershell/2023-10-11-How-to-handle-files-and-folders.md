---
title: "Comment manipuler les fichiers et les dossiers."
excerpt: "Manipuler les données en PowerShell avec classe !"
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
---

## Préambule

Honte à moi j'ai jugé !

Initialement posté sur une page LinkedIn, sur le channel "PowerShell Power User", rien que ca... C'est depuis le fond de mon lit d'hôpital que je suis tombé sur ce vlog 🤮.

D'ordinaire j'adore lire ce channel. On y trouve souvent des gens créatifs, inventifs offrant des enseignements instructifs et constructifs.

Ici ce n'est pas le cas, sinon je ne me serais pas levé après 5 jours d'hospitalisation pour ouvrir mon laptop et compter ces mots.

{% include video id="61E5LB5cXq0" provider="youtube" %}

## Palabrons

Vous me pardonnerez le ton condescendant que j'emploie (ou pas), ma chère épouse vous dirait que c'est un trait de la pathologie dont je souffre.

Que je vous explique tout de même ma frustration. (Ou passez ces quelques lignes si celle-ci vous en touche une sans secouer l'autre comme le dit notre cher E. Macron).

Quand je tombe sur un article sur LinkedIn, en vrai, je ne m'attends plus à rien. Ce réseau social a la médiocrité intellectuelle d'une classe de 6e; les individus qui s'y expriment pensent avoir atteint le niveau de maturité requis pour s'autoriser à pousser des "coups de gueules" et s'attendent à ce que toute l'attenance absorbe leurs mots le visage médusé comme s'ils provenaient du plus illustre des sages. 

On les reconnait facilement, ils commencent leur allocution par ces mots, "Coup de gueule !", suivis de trois retour à la ligne (l'effet teasing sans doute), débutent un petit prologue pour excuser leur attitude (ça n'arrive jamais mais quand c'est trop, c’est trop!) et poursuivent sur un monologue basé sur quelques bloc-paragraphes débutants tous par un emoji comme pour se donner du crédit avec une belle illustration... enfin... tout de même un emoji quoi...

D'un côté leurs compères se féliciterons plus tard d'avoir appuyé ou contredit l'auteur... quant aux autres, ceux qui pourraient prétendre avoir le niveau de maturité, feront mine de ne pas avoir lu, et n'ont de toute façon aucune envie de s'exposer sur un réseau social dit "professionnel" et voir leur carrière sabotée après un commentaire potentiellement mal interprété.

Personnellement quand j'ai du temps à tuer, et en quantité suffisante pour m'_attarder_ (comprenez ce mot au 1er degré) sur LinkedIn, il y a des posts sur lesquels je prends le temps. Ceux qui concernent PowerShell en font partis.

Dans __PowerShell Power User__, j'entends __Power__ user. Pas besoin de d'avoir un diplôme en langues étrangères pour comprendre. Donc, quand je prends le temps sur un post PowerShell j'attends un peu de __Power__ et ne souhaite pas juste m'_attarder_, un peu plus que je ne le suis déjà. (Ca y est t'as compris le jeu de mot ?)

## Entrons dans le vif

(Je ne sais pas si je finirai mon article ce soir, j'ai vraiment mal au crâne.)

(Non, ce n'est pas lié à ma prise de tête et/ou de conscience.)

Reprenons, dans la vidéo relative à ma frustration, on vous explique ce qui est écrit a-peu-prêt dans tous les manuels, y compris [celui-ci]:(https://learn.microsoft.com/en-us/powershell/scripting/samples/working-with-files-and-folders). (Un éditeur inconnu et mal compris)

Sérieusement, Youtube est une source d'information inépuisable, j'ai hâte de voir les remparts de sa prochaine video -how to eat your soup with _the help of_ a spoon- 😒

Bon, vous n'apprendrez rien ce soir avec mon blog, mais restons sur une note constructive ! 😁

Il y a une notion sur laquelle ce youtubeur fou aurait peut-être dû ralentir. En effet, une copie de fichiers récursive ou non, n'impose pas l'usage de Copy-Item. J'en montre l'exemple souvent. Par contre, la notion de copie distante le conseille vivement.

Et oui, même si pour ma part je sais copier de petites quantités de données via un PsSession (c'est déjà chiant), un procédé qui m'était imposé sur les plateformes __PowerShell 4 et inférieur__, copier de données volumineuses m'était alors impossible sans montage d'un partage réseau __puisque les paramètres -toSession et -fromSession n'existaient pas__

Ce qu'il faut retenir, c'est que PowerShell 5 et 5.1 nous on fait atteindre un point culminant en ce qui concerne l'interopérabilité Windows.





> Coup de gueule !

> Vous êtes encore là ?

Bon un petit cadeau avant de partir alors.

C'est un truc tellement bête, mais je le trouve pratique.

```powershell
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

<figure style="width: 372px" class="align-center">
  <a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h36_22.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h36_22.png" alt="ca marche."></a>
  <figcaption>Quand c'est vert c'est bon.</figcaption>
</figure>

Quand ca ne fonctionne pas:

<figure style="width: 387px" class="align-center">
  <a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h37_19.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h37_19.png" alt="ca marche pas."></a>
  <figcaption>Quand c'est rouge c'est pas  bon.</figcaption>
</figure>


