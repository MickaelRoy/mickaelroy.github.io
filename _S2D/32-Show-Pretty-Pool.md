---
title: "Répartition des données d'un pool S2D"
excerpt: "S'assurer que les données sont distribuées entre les disques de façon équitable."
classes: wide
---

## Le quorum

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/S2D-Slabs-re-balance.gif" alt="Data-Rebalance">
  <figcaption>S2D data re-balance.</figcaption>
</figure>

@Cosmos Darwin, please considere all my appologies regarding what i'm about to do.

## De quoi s'agit-il ?

C'est un script qu'il met arrivé d'exécuter comme ça, à brule-pourpoint, pour avoir une réponse rapide à une question simple: -"les données du pool sont-elles correctement réparties entre les disques ?"

Il arrive parfois, notemment après l'insertion de nouveaux disques au sein d'un cluster, que des disques ne soient pas utilisés pour acceuillir des donnés du StoragepPool à la meme echelle que les autres.

Parfois même, c'est l'ensemble des disques d'un noeud qui sont sous-utilisés; Après une mise en maintenance par exemple.

## D'où nous vient ce script ?

L'origine de ce script, je le tiens de [Cosmos Darwin](https://twitter.com/CosmosDarwin), un ingénieur confirmé, plus précisément de [cette page](https://techcommunity.microsoft.com/t5/storage-at-microsoft/deep-dive-the-storage-pool-in-storage-spaces-direct/ba-p/425959).


## Conclusion

En conclusion, j'ai réduit le script d'origine à 65 lignes pour avoir l'essentiel, et ajouté un fichier _format.ps1xml_ à côté pour ceux qui ont besoin d'avoir les valeurs humainement lisibles.

Le resultat de la commande est pas loin de ça

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Show-PrettyPoolObject.webp" alt="Show-PrettyPool-Result">
  <figcaption>Show-PrettyPool result.</figcaption>
</figure>

J'ai mis tout ça sur mon repo Git... C'est cadeau !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Show-PrettyPool){: .btn .btn--info}