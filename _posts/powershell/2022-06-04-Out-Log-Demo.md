---
title:  "Out-Log: une cmdlet qui affiche la couleur"
excerpt: |
  Découvrez comment la fonction Out-Log facilite la journalisation dans vos scripts PowerShell en ajoutant des messages datés et catégorisés à des fichiers de log.
  
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - cmdlet
---

# Présentation de la fonction Out-Log - Améliorez la journalisation dans PowerShell

La fonction `Out-Log` est un outil essentiel pour améliorer la journalisation dans vos scripts PowerShell.

En ajoutant des messages datés et catégorisés à des fichiers de log, elle simplifie le suivi et le débogage de vos processus automatisés.

Découvrez comment utiliser cette fonctionnalité puissante pour optimiser votre workflow de développement.

## Utilisation

La fonction `Out-Log` prend plusieurs paramètres :

- **Path** : Le chemin du fichier de journalisation où les messages seront enregistrés.
- **String** : La description du message à enregistrer.
- **Action** : La catégorie du message, comme "ERROR", "WARNING", "ACTION", "INFO", ou "SUCCESS".
- **Encoding** : L'encodage du fichier de journalisation.
- **NoNewLine** : Option pour empêcher l'ajout d'un saut de ligne à la fin du message.
- **Tee** : Option pour écrire le message à la fois sur la sortie standard et dans le fichier de journalisation.

## Exemples

```powershell
"Message d'erreur" | Out-Log ERROR -Path C:\Logs\logfile.txt
```

```powershell
"Message d'action" | Out-Log ACTION -Tee -Path C:\Logs\logfile.txt
```

![First tips image]({{ site.url }}{{ site.baseurl }}/assets/images/2022-06-04-Out-Log-Demo.gif)

## Detournement ?
Petit hack rigolo avant de partir... (il ne fonctionne pas sur ISE)

```powershell
$computerList = 'server01', 'server02', 'server03', 'server04', 'server05', 'server06', 'server07', 'server08', 'server09', 'server10'
$counter = 0
foreach ($computer in $computerList) {
    $counter++
    $percent = $counter / $($computerList.count) * 100
    "Processing computers $percent%" | Out-Log -NoNewLine
    Start-Sleep -Milliseconds 200
}
"Done" | Out-Log SUCCESS
```

Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Out-Log){: .btn .btn--info}
