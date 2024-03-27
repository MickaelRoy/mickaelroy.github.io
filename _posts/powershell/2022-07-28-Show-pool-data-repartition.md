---
title: "[BullShit] Répartition des données d'un pool S2D"
excerpt: |
  Découvrez le script de fumiste qui vous permet de gratter du code pour rien et d'avoir une réponse rapide à la question:: les données du pool sont-elles correctement réparties entre les disques ?.
  
category: S2D
classes: wide
comments: true
tags: 
  - BullShit
  - S2D
  - PowerShell
  - Show-PrettyPool
header:
  teaser: /assets/images/2022-07-28-Show-pool-data-repartition.webp
  image_description: "data repartition"
---

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/S2D-Slabs-re-balance.gif"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/S2D-Slabs-re-balance.gif" alt="Data-Rebalance"></a>
  <figcaption>S2D data re-balance.</figcaption>
</figure>


@Cosmos Darwin, please considere all my appologies regarding what I'm about to do.

Ah, le monde merveilleux de PowerShell et des pools S2D ! Dans cet article, je vous présente un script un peu trop sophistiqué qui promet monts et merveilles, mais qui ne fait finalement que brasser de l'air. Mais bon, c'est amusant, je vous assure ! 😄

## A quoi ce script sert-il ?

Parfois, après avoir ajouté de nouveaux disques à votre cluster, vous vous retrouvez avec des disques qui ne sont pas utilisés pour accueillir des données du Storage Pool. 

Parfois même, c'est l'ensemble des disques d'__un__ noeud qui sont sous-utilisés.

Nous allons pouvoir voir la répartition des données d'un pool S2D grâce à cette astuce.

## Mais d'où nous vient ce script trompeur ?

Ce script, je le tiens d'un génie du nom de [Cosmos Darwin](https://twitter.com/CosmosDarwin), un véritable maître dans l'art du PowerShell.

Il a partagé ce petit bijou sur [cette page](https://techcommunity.microsoft.com/t5/storage-at-microsoft/deep-dive-the-storage-pool-in-storage-spaces-direct/ba-p/425959). Merci Cosmos pour cette pépite de code !

## Pourquoi j'en parle ?

Alors, vous vous demandez peut-être pourquoi je parle de ce script ici, sur ce blog. 

Eh bien, c'est simple : c'est un script que j'ai utilisé un jour pour répondre à une question toute bête:
> "les données du pool sont-elles correctement réparties entre les disques ?" 

 Et même si le script est un peu trop enjolivé à mon goût, il fonctionne plutôt bien. Alors pourquoi ne pas en parler ici, hein ?

## Et le "BullShit" dans tout ça ?🤔

Je vous invite fermement à visiter [sa page](https://techcommunity.microsoft.com/t5/storage-at-microsoft/deep-dive-the-storage-pool-in-storage-spaces-direct/ba-p/425959) pour consulter le contenu du script.

A première vue, c'est du lourd, __240 lignes__ pour obtenir un objet PowerShell qui ressemble à ca:

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/Show-PrettyPoolObject.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/Show-PrettyPoolObject.webp" alt="Show-PrettyPool-Result"></a>
  <figcaption>Show-PrettyPool result.</figcaption>
</figure>

Ah, le BullShit... On en vient enfin au cœur du sujet ! Le script original de Cosmos Darwin est un peu... comment dire... chargé en "bullshit".

Je veux dire, 240 lignes de code pour obtenir un objet PowerShell qui ressemble à ça ? C'est un peu exagéré, non ? Et puis, cette histoire de déterminer la racine du nom d'hôte de chaque nœud, sérieusement ? 😅

```
{
"Server-A2.Contoso.Local" -> "A2"
"Server-B4.Contoso.Local" -> "B4"
"Server-C6.Contoso.Local" -> "C6"
}
```

Alors voilà, j'ai pris les choses en main et j'ai réduit le script à l'essentiel, soit 65 lignes de pur bonheur.

J'ai même ajouté un fichier format.ps1xml pour ceux qui ont besoin de valeurs humainement lisibles.

Merci Cosmos pour l'idée de la division _FootPrint/Size_, c'était une vraie brillante idée ! Le reste, eh bien, c'est un peu de la fumisterie, mais bon, on lui pardonne ! 😄

J'ai mis tout ça sur mon repo Git.

C'est cadeau !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Show-PrettyPool){: .btn .btn--info}