---
title: "Invoke-Parallel : Exécution Simultanée de Scripts PowerShell"
excerpt: |
    Une fonction légère et utile pour vous aider à chercher une chaîne spécifique dans les objets de Stratégie de Groupe de votre environnement Active Directory.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - GPO
---

Vous avez des tâches à exécuter sur plusieurs machines et vous voulez gagner du temps ? Ne cherchez pas plus loin ! La fonction Invoke-Parallel est là pour vous.

Cette petite pépite de PowerShell vous permet d'exécuter vos scripts sur plusieurs ordinateurs en même temps, tout en gardant le contrôle.

## Qu'est-ce que ça fait ?

La fonction `Invoke-Parallel` est votre ticket pour le monde du parallélisme dans PowerShell. Imaginez pouvoir exécuter plusieurs scripts en même temps, sur plusieurs machines, sans vous arracher les cheveux. Eh bien, c'est exactement ce que fait cette petite merveille !

## Comment ça marche ?

C'est simple ! Vous spécifiez une liste d'ordinateurs, vous écrivez votre script, et `Invoke-Parallel` se charge du reste. Il divise automatiquement le travail entre les machines et vous rend les résultats plus rapidement que vous ne pouvez dire "PowerShell" !

## Pourquoi c'est cool ?

- **Rapidité Maximale** : Vos scripts s'exécutent en un clin d'œil, même sur de grandes quantités de machines.
- **Zéro Stress** : Pas besoin de jongler avec les threads ou les processus, `Invoke-Parallel` s'occupe de tout.
- **Optimisation Totale** : Vous pouvez maintenant siroter votre café en regardant vos scripts se dérouler sans tracas.


## Fonctionnalités

- **Traitement Parallèle** : Exécutez des scripts simultanément sur plusieurs ordinateurs, réduisant ainsi le temps d'exécution.
- **Contrôle de Débit** : Contrôlez le nombre maximal d'exécutions de scripts simultanées pour éviter l'épuisement des ressources.
- **Scripting Dynamique** : Transmettez des paramètres et des variables dynamiques pour personnaliser le comportement du script pour chaque exécution.
- **Importation de Modules** : Importez des modules PowerShell requis de manière transparente pour une fonctionnalité de script améliorée.
- **Suivi de Progression** : Suivez la progression de l'exécution du script sur plusieurs ordinateurs pour une meilleure gestion des tâches.
- **Support du Pipeline** : Prend en charge l'entrée de pipeline, permettant une intégration transparente avec d'autres cmdlets et scripts PowerShell.

## Exemple d'Utilisation

```powershell
Invoke-Parallel -Computername "Serveur01", "Serveur02", "Serveur03" -ScriptBlock {
    param(
        `$ComputerName
    )
    # Protéger la variable $ComputerName
    Echo `$ComputerName >> C:\temp\log.txt
    Get-Service -Name WinRM -ComputerName `$ComputerName
}
```

<aside class="tip" markdown="1">
💡 **Conseil** : Assurez-vous de protéger vos variables dans le script block en utilisant le signe backtick (`) pour éviter qu'elle ne soit évaluée localement
</aside>

## Conclusion

Avec Invoke-Parallel, vous pouvez exploiter la puissance du traitement parallèle pour exécuter des tâches efficacement dans votre environnement. Que vous gériez des serveurs, automatisiez des tâches administratives ou traitiez des données, cette fonction offre une solution polyvalente pour optimiser l'exécution des scripts PowerShell.

Explorez davantage sur Invoke-Parallel et débloquez de nouvelles possibilités pour améliorer votre flux de travail PowerShell.

C'était tout pour ce tour ! Restez connecté pour plus d'aventures PowerShell !

À bientôt sur la ligne de commande ! 😉


Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Invoke-Parallel){: .btn .btn--info}