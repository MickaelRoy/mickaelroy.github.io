---
title: "Introduction"
excerpt: "Toutes les étapes pour concevoir un cluster S2D aka Storage Spaces Direct."
classes: wide
---

![Volume layers]({{ site.url }}{{ site.baseurl }}/assets/images/converged-full-stack.png)

Aujourd'hui, ce ne sera pas franchement fun. Aujourd'hui je vous donne toutes les étapes clés pour créer un cluster S2D from scratch.

Je vous explique quoi faire et pourquoi.

Ici la configuration est adaptée à RoCE, qui fonctionne en UDP, c'est pourquoi il y a une étape qui concerne la configuration DCB.

Dans le cas de iWarp, que je n'aborderai pas ici, qui fonctionne en TCP (avec ses avantages) vous pouvez zapper la partie DCB/QOS.

