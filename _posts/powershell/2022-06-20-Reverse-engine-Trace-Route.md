---
title: "Trace-Route, Test-Ping et du fun"
excerpt: "Un projet rigolo, reproduire le comportement de tracert.exe et de ping.exe"
description: "Une demonstration de la force de powershell tout en s'amusant. Deux commandes Dos reproduite en Posh et une astuce en prime."
category: PowerShell
tags: 
  - PowerShell
  - cmdlet
  - Tips
---

![Brian disapointed]({{ site.url }}{{ site.baseurl }}/assets/images/hopeless-disappointed.gif)

> Mais qu'est ce qu'il va nous sortir encore...

## Le mec qui s'ennuit

Bon j'avoue, je suis probablement le seul a m'être éclaté à ce petit jeu, mais le resultat est formateur.

C'est un petit projet que j'ai entrepris il y a 3 ans, il m'a cramé pas mal d'heures, et ca n'a d'utilité que pour crâner...

Obsédé par les scripts totalement exempt d'executables (qui ne retournent que du texte et qu'il faut parser), j'ai tenté de reproduire le comportement de ping.exe et tracert.exe en PowerShell.

Pour ceux qui sont pressés, allez sur le lien en bas de page. Les autres peuvent poursuivre la lecture.

## Description

Dans le code que je livre ici, il y a des points sur lesquels j'aimerais faire un focus.

Il s'agit de 3 fichiers, deux ps1, et un format.ps1xml.

Le fichier format.ps1xml est interessant pour ceux qui ne maitrisent pas cet aspect. Il permet la mise en forme de la sortie de la cmdlet, plus précisement, pour que la liste des IP soient toutes affichées les unes en dessous des autres.

Les deux autres fichiers contiennent les cmdlets en tant que tel. Trace-Route se sert de Test-Ping pour valider que la cible est joignable.

```powershell
If ([console]::KeyAvailable) {
  $key = [system.console]::readkey($true)
  If (($key.modifiers -band [consolemodifiers]"control") -and ($key.key -eq "C")) {
    Write-Verbose -Message "Control-C"
    break
  }
}
```

Fun fact, j'utilise ce petit bout de code pour pouvoir stopper le processus de Trace-Route, mais en permettant au systeme de m'afficher ce qu'il a déjà trouvé.
Test-Ping dispose aussi de ce tips.

❗Cette astuce ne fonctionne pas avec ISE !


## Demo

### Test-Ping

![Ping Demo]({{ site.url }}{{ site.baseurl }}/assets/images/Test-Ping-Demo.gif)

J'ai poussé le simulacre jusqu'a mettre des sleep en mode verbeux.😅


### Trace-Route

![Tracert demo]({{ site.url }}{{ site.baseurl }}/assets/images/trace-route-demo.gif)

> Waow, mais c'est bien plus rapide !! Comment ca se fait ?

Pour commencer je ne tente pas la resolution de l'ip... après... je ne sais pas 🤔...

j'ai le meme comportement avec ping.exe... dans mon code, rien ne me force a attendre 1 seconde entre chaque paquet. 🤷‍♂️

Du coup, c'est beaucoup plus rapide !

## Conclusion

J'ai trouvé l'exercice très exaltant. Parole de geek !

Les scripts ne contiennent quasiment que des classes dot net; Cela donne l'illusion que l'on flirt avec du developpement.😁

Il s'avère que le resultat obtenu est bien plus rapide qu'attendu. 


Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Trace-Route){: .btn .btn--info}
