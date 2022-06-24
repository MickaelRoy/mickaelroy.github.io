---
title: "Introduction"
excerpt: "Toutes les étapes pour concevoir un cluster S2D aka Storage Spaces Direct."
classes: wide
---
<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/storage-spaces-hyper-converged.png" alt="">
  <figcaption>Storage Spaces Direct illustration</figcaption>
</figure> 


Aujourd'hui je vous donne toutes les étapes clés pour créer un cluster S2D from scratch.

Je vous explique quoi faire et pourquoi.

Ici la configuration est adaptée à RoCE, qui fonctionne en UDP, c'est pourquoi il y a une étape qui concerne la configuration DCB.

Dans le cas de iWarp, que je n'aborderai pas ici, qui fonctionne en TCP (avec ses avantages) vous pouvez zapper la partie DCB/QOS.

