---
title: "[BullShit] Répartition des données d'un pool S2D"
excerpt: "La cmdlet de fumiste, gratter du code pour rien."
category: S2D
classes: wide
tags: 
  - BullShit
  - S2D
  - PowerShell
  - Show-PrettyPool
header:
  teaser: /assets/images/2022-07-28-Show-pool-data-repartition.webp
---

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/S2D-Slabs-re-balance.gif" alt="Data-Rebalance">
  <figcaption>S2D data re-balance.</figcaption>
</figure>

@Cosmos Darwin, please considere all my appologies regarding what i'm about to do.

## A quoi ce script sert-il ?

Il arrive parfois, notemment après l'insertion de nouveaux disques au sein d'un cluster, que des disques ne soient pas utilisés pour acceuillir des donnés du StoragepPool à la meme echelle que les autres.

Parfois même, c'est l'ensemble des disques d'un noeud qui sont sous-utilisés.

Nous allons pouvoir voir la répartition des données d'un pool S2D grâce à cette astuce.

## D'où nous vient ce script ?

Sans plus attendre, j'entre dans le vif du sujet.

L'origine de ce script, je le tiens de [Cosmos Darwin](https://twitter.com/CosmosDarwin), un ingénieur confirmé, plus précisément de [cette page](https://techcommunity.microsoft.com/t5/storage-at-microsoft/deep-dive-the-storage-pool-in-storage-spaces-direct/ba-p/425959).

## Pourquoi j'en parle ?

D'ordinaire, je poste les sujets autour d'S2D directement dans la collection prévue à cet effet. 

> Mais alors pourquoi j'en parle ici ?

J'y viens... c'est un script qu'il met arrivé d'exécuter comme ça, à brule-pourpoint, pour avoir une réponse rapide à une question simple: -"les données du pool sont-elles correctement réparties entre les disques ?"

Le script en soit est fonctionnel, et a son intérêt. Je voulais donc en parler sur un blog en français. J'ai donc entrepris de le nettoyer, et l'améliorer pour pouvoir être exécuté à distance.

## Et le "BullShit" dans tout ça ?🤔

Je vous invite fermement à visiter [sa page](https://techcommunity.microsoft.com/t5/storage-at-microsoft/deep-dive-the-storage-pool-in-storage-spaces-direct/ba-p/425959) pour consulter le contenu du script.

A première vue, c'est du lourd, __240 lignes__ pour obtenir un objet PowerShell qui ressemble a ca:

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Show-PrettyPoolObject.webp" alt="Show-PrettyPool-Result">
  <figcaption>Show-PrettyPool result.</figcaption>
</figure>

> ...

Et c'est là que vient le bullshit, son script comprends quasiment 200 lignes pour formater les valeurs, et surtout déterminer la racine du nom d'hôte de chaque nœud comme suit:

```
{
"Server-A2.Contoso.Local" -> "A2"
"Server-B4.Contoso.Local" -> "B4"
"Server-C6.Contoso.Local" -> "C6"
}
```

Non seulement c'est une information peu pertinente, mais c'est ça prend un temps fou à calculer.
Enfin, le cœur même du sujet réside en une boucle qui récupère une poignée de propriétés à un simple _Get-PhysicalDisk_.

## Conclusion

En conclusion, j'ai réduit le script à 65 lignes pour avoir l'essentiel, et ajouté un fichier _format.ps1xml_ à côté pour ceux qui ont besoin d'avoir les valeurs humainement lisibles.

Cosmos Darwin a eu l'intelligence de faire l'execice de la division _FootPrint/Size_. On l'en remercie. Celon moi, le reste c'est de la fumisterie et je le dis avec toute mon affection 😅.

J'ai mis tout ça sur mon repo Git.

C'est cadeau !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Show-PrettyPool){: .btn .btn--info}