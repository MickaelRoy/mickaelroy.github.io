---
title: "[Humour] Plongée dans le monde absurde de PowerShell"
excerpt: |
  Venez découvrir une plongée navrante dans le monde étrange des tutoriels PowerShell sur YouTube.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Humour
---

Honte à moi, chers lecteurs, mais je ne peux plus me taire ! En ce jour fatidique, alors que je languis dans mon lit d'hôpital, un vlog des plus insipides a retenu mon attention... Oh, la joie ! 🙄

Initialement posté sur le saint Graal de la médiocrité intellectuelle, j'ai nommé LinkedIn, sur le canal "PowerShell Power User", je vous assure, rien que ça... Et me voici, tiré de ma torpeur hospitalière, pour vous livrer mes observations.

{% include video id="61E5LB5cXq0" provider="youtube" %}

## Une descente dans l'absurde

Vous me pardonnerez le ton condescendant que j'emploie (ou pas), ma chère épouse vous dirait que c'est un trait de la pathologie dont je souffre.

Mais laissez-moi vous exposer ma frustration (Ou passez ces quelques lignes si celle-ci vous en touche une sans secouer l'autre comme le dit notre cher président Macron).

Quand je parcours les articles sur LinkedIn, je ne m'attends plus à grand-chose. Ce réseau social semble parfois manquer cruellement de profondeur intellectuelle, évoquant plutôt l'ambiance d'une salle de classe de sixième. Les utilisateurs qui s'y expriment donnent souvent l'impression d'avoir atteint un niveau de maturité tel qu'ils se permettent de lancer des "coups de gueule" avec une désinvolture déconcertante. Ils semblent penser que leurs paroles seront absorbées par l'assemblée, comme si elles émanaient des plus grands sages.

Ces individus sont facilement reconnaissables : ils amorcent leurs diatribes par un retentissant "Coup de gueule !", suivi de trois retours à la ligne (probablement pour créer un effet de suspense), puis entament un bref prologue pour justifier leur attitude (comprendre: cela n'arrive jamais, mais bon, quand c'est trop, c'est trop !). Ils continuent ensuite avec un monologue parsemé de quelques blocs de texte, chacun agrémenté d'un emoji, comme s'ils cherchaient à crédibiliser leur propos avec une touche artistique...

D'un côté, leurs semblables se féliciteront plus tard d'avoir soutenu ou contredit l'auteur de façon si éloquente... Quant aux autres, ceux qui pourraient (peut être)revendiquer un niveau de maturité certain, ils préfèreront feindre l'ignorance, n'ayant aucune envie de s'exposer sur un réseau social prétendument "professionnel" et risquer de compromettre leur carrière à la suite d'un commentaire potentiellement mal interprété.

Personnellement quand j'ai du temps à tuer, et en quantité suffisante pour m'attarder (au sens littéral du terme) sur un post LinkedIn, certains articles retiennent mon attention. Ceux qui concernent PowerShell en font partis.

Dans __PowerShell Power User__, le terme __Power__ user prend tout son sens. Pas besoin d'être un expert en linguistique pour le comprendre. Alors, quand je m'_attarde_ sur un post PowerShell j'attends un peu de __Power__ et ne souhaite pas juste devenir un peu plus '_attardé_, que je ne le suis déjà. (Ca y est t'as compris le jeu de mot ?)

## Entrons dans le vif

(Je ne sais pas si je vais terminer cet article ce soir, j'ai vraiment mal à la tête.)

(Non, ce n'est pas lié à ma prise de conscience soudaine.)

Revenons-en à nos moutons. Dans cette vidéo frustrante, on nous explique ce qui est écrit dans tous les manuels, y compris [celui-ci](https://learn.microsoft.com/en-us/powershell/scripting/samples/working-with-files-and-folders). (Un éditeur inconnu et incompris)

Franchement, YouTube est une mine d'informations sans fin. J'ai hâte de découvrir les remparts de la prochaine vidéo de ce _créateur de contenu_: "how to eat your soup with _the help of_ a spoon" 😒

Bon, vous n'apprendrez rien de nouveau ce soir avec mon blog, mais restons constructifs ! 😁

Il y a une notion sur laquelle ce YouTubeur aurait peut-être dû s'attarder. En effet, une copie de fichiers, récursive ou non, n'impose pas nécessairement l'utilisation de Copy-Item. Mais bon, il faut parfois laisser les enfants jouer avec leurs jouets préférés. Par contre, la notion de copie distante le conseille vivement.

Et oui, même si pour ma part, je peux copier de petites quantités de données via un PsSession (c'est déjà embêtant), un procédé qui était obligatoire sur les anciennes versions de __PowerShell__, copier de grandes quantités de données était alors impossible sans montages de partages réseau, __car les paramètres -toSession et -fromSession n'existaient pas.__

Ce qu'il faut retenir, c'est que PowerShell 5 et 5.1 ont atteint un sommet en matière d'interopérabilité Windows. Quelle époque formidable !

<br/>

<br/>

<br/>

<br/>

> Coup de gueule !

> Vous êtes encore là ?

Bon un petit cadeau avant de partir.

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

Quand ça fonctionne:

![image-left]({{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h36_22.png){: .align-left}

<br/>

Quand ça ne fonctionne pas:

![image-left]({{ site.url }}{{ site.baseurl }}/assets/images/2023-10-11_21h37_19.png){: .align-left}
