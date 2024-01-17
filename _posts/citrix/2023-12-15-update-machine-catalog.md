---
title: "[Citrix] Mettre à jour un Machine Catalog"
excerpt: "Le studio Citrix ayant ces limitations, il convient pour les plus fainéants d'entres nous d'automatiser le déploiement des mises à jour Citix.
"
category: Citrix
classes: wide
comments: true
tags: 
  - Citrix	
  - Machine Catalog
  - Powershell
---

# Préambule

Cet article décrit comment mettre à jour l'image principale pour les catalogues MCS dédiés et regroupés à l'aide du kit de développement logiciel PowerShell (SDK PowerShell)

# Prérequis

Les PSSnapin (non compatibles avec PowerShell 7) devront être installés sur le poste qui éxecutera ce script.

Un accès WinRM sera également nécessaire.

Je vous invite à adapter ce code pour utilier le WinRM en SSL.

# Sources

[CTX129205](https://support.citrix.com/article/CTX129205)

[hallspalmer_Blog](https://hallspalmer.wordpress.com/2019/10/30/update-citrix-machine-catalog-using-powershell/)

# Dans le détail

Je cite la source de hallspalmer pour la forme, si vous souhaitez vous brûler la cornée sur son script je vous invite à le copier dans un étiteur et de vous armer d'une belle paire de lunettes.

Son script se veut plus compliqué que nécessaire, je pensais m'en servir comme base pour traiter mon besoin, au final je n'ai rien gardé.

En outre, son script pose sans cesse des questions et l'on perd donc l'interêt de l'automatisme.

> Oui je sais, je critique encore.

Mon script (que vous trouverez en fin d'article) ne casse pas trois pattes à un canard de son coté. #ExpressionDeVieux

Le coeur réside en deux actions, la création d'un snapshot et le déclenchement de la publication.

Tout le reste n'est que cosmétique.

Bref ca le fait le taf, rien de plus.

# Le visuel

Voici ce que cela donne d'un point de vu PowerShell.

<figure style="width: 600px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2024-01-12_17h40_31.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2024-01-12_17h40_31.png" alt="Déroulement verbeu."></a>
  <figcaption>Verbosité claire.</figcaption>
</figure>

A la fin du script, vous pourrez consulter l'avancement du déploiement à l'aide de ce petit bout de code.

```powershell
$provTask = Get-ProvTask -AdminAddress $adminAddress -TaskId $PubTask
```


Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Update-CtxMachineCatalog){: .btn .btn--info}