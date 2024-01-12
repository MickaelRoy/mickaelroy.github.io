---
title: "[Citrix] UPM Configuration Edge"
excerpt: "Microsoft a annoncé le retrait d'Internet Explorer à l'été 2022, MS Edge a été déployé sur les serveurs Citrix XenApp avec des bureaux publiés.

Lorsque les utilisateurs se déconnectent et se reconnectent, MS Edge présente des invites de dialogue « Ou of Box ». Les favoris MS Edge, la page d'accueil, l'historique, etc. ne sont pas conservés.

Cet article fournit les paramètres recommandés pour éviter une éventuelle surcharge de profil associée à la post-configuration de Microsoft Edge."
category: Citrix
classes: wide
comments: true
tags: 
  - Citrix	
  - UPM
  - Edge
---

# Préambule

Une fois n'est pas coutûme, je rédige un petit article qui concerne la configuration du gestionaire de profile Citrix (UPM) et en particulier celle de Edge.

A cette fin, j'ai configuré une GPO dédiée a ce contexte. A titre informatif, la version de Xitrix XenApp utilisée est en 2203.

# La configuration

Je suis vraiment navré je vous la confie sous forme de capture, mais je manque de temps.

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-12-15_18h02_34.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-12-15_18h02_34.png" alt="Assistant Esxi Etape 2"></a>
  <figcaption>Sélectionnez un nom et un système d'exploitation.</figcaption>
</figure>

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-12-15_18h02_46.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-12-15_18h02_46.png" alt="Assistant Esxi Etape 2"></a>
  <figcaption>Sélectionnez un nom et un système d'exploitation.</figcaption>
</figure>

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-12-15_18h03_06.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-12-15_18h03_06.png" alt="Assistant Esxi Etape 2"></a>
  <figcaption>Sélectionnez un nom et un système d'exploitation.</figcaption>
</figure>

# Sources

[CTX336809](https://support.citrix.com/article/CTX336809)

[CTX235698](https://support.citrix.com/article/CTX235698)

[La base Chrome](https://docs.citrix.com/en-us/profile-management/2203-ltsr/integrate/google-chrome.html)

A suivre...