---
title: "Réinventer Tracert avec un Twist Ludique en PowerShell."
excerpt: |
  Découvrez comment recréer la commande DOS Tracert en PowerShell de manière ludique et efficace avec la fonction Trace-Route.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - cmdlet
  - Tips
  - tracert
  - ludique
---

![Brian disapointed]({{ site.url }}{{ site.baseurl }}/assets/images/hopeless-disappointed.gif)

> Mais qu'est ce qu'il va nous sortir encore...

Dans cet exercice de code PowerShell, plongez-vous dans une aventure ludique tout en recréant la fonctionnalité de la commande DOS Tracert avec la fonction Trace-Route.

Découvrez comment allier plaisir et efficacité tout en explorant les subtilités du tracé de la route vers une destination donnée.

## L'ennui, source de créativité

Eh bien, je dois l'admettre, c'est peut-être moi qui ai trouvé du plaisir dans cette petite aventure, mais le résultat en valait la peine.

Il y a trois ans de cela, je me suis lancé dans un projet qui m'a fait passer pas mal d'heures, sans autre utilité que celle de me vanter un peu...

Animé par la passion des scripts exempts d'exécutables (ceux qui ne retournent que du texte et nécessitent un peu d'analyse), j'ai entrepris de reproduire le comportement de ping.exe et tracert.exe en PowerShell.

Pour ceux qui sont pressés, je vous invite à consulter le lien en bas de page. Pour les autres, continuez votre lecture.

## Une approche plus efficace

En plus d'être ludique, cet exercice vise également à améliorer l'efficacité du tracé de la route en PowerShell. Grâce à une conception intelligente et à des fonctionnalités avancées, la fonction Trace-Route offre une alternative plus flexible et performante à la commande Tracert traditionnelle.

## Description

Dans le code que je partage ici, je souhaite mettre en lumière certains points spécifiques.

Il s'agit de trois fichiers : deux fichiers ps1 et un fichier format.ps1xml.

Le fichier format.ps1xml est particulièrement intéressant pour ceux qui ne sont pas familiers avec cet aspect. Il permet de formater la sortie de la cmdlet, en organisant par exemple la liste des adresses IP de manière à ce qu'elles soient toutes affichées les unes en dessous des autres.

Les deux autres fichiers contiennent les cmdlets proprement dites. Trace-Route utilise Test-Ping pour confirmer que la cible est accessible.

```powershell
If ([console]::KeyAvailable) {
  $key = [system.console]::readkey($true)
  If (($key.modifiers -band [consolemodifiers]"control") -and ($key.key -eq "C")) {
    Write-Verbose -Message "Control-C"
    break
  }
}
```

Par ailleurs, voici une anecdote amusante : j'utilise ce petit bout de code pour pouvoir interrompre le processus de Trace-Route tout en permettant au système d'afficher les résultats déjà obtenus. Cette astuce est également applicable à Test-Ping.

❗Notez que cette astuce ne fonctionne pas avec l'ISE (Integrated Scripting Environment) !

## Demo

### Test-Ping

![Ping Demo]({{ site.url }}{{ site.baseurl }}/assets/images/Test-Ping-Demo.gif)

J'ai poussé le simulacre jusqu'à mettre des sleeps en mode verbeux.😅


### Trace-Route

![Tracert demo]({{ site.url }}{{ site.baseurl }}/assets/images/trace-route-demo.gif)

> Waow, mais c'est bien plus rapide !! Comment ça se fait ?

Pour commencer, je ne tente pas la résolution de l'IP... Ensuite... je ne sais pas vraiment 🤔...

J'ai remarqué le même comportement avec ping.exe... Dans mon code, rien ne m'oblige à attendre 1 seconde entre chaque paquet. 🤷‍♂️

Du coup, c'est beaucoup plus rapide !

## Conclusion

Je dois avouer que cet exercice m'a vraiment emballé. C'est du pur plaisir geek !

Les scripts ne sont presque composés que de classes .NET ; On a presque l'impression de faire du développement à plein temps. 😁

Et quelle surprise de constater que le résultat obtenu dépasse largement nos attentes en termes de vitesse.

Eh bien, voilà pour vous, un petit cadeau ! À la prochaine !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Trace-Route){: .btn .btn--info}
