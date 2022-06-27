---
title: "Bienvenu"
layout: splash
permalink: /
hidden: true
header:
  overlay_color: "#1d1f21"
  overlay_filter: "0.2"
  overlay_image: /assets/images/splashposh.png
caption: "A franco-francais website for french baguette eaters, and PowerShell lovers."
intro: 
  - excerpt: "La photo à l'origine de ces trois carreaux a été prise depuis les invalides."
feature_row:
  - image_path: /assets/images/Tour-Eiffel_04b.png
    alt: "S2D"
    title: "S2D"
    excerpt: "Tout au sujet de S2D, Storage Spaces Direct, la solution hyper-convergée on premise de Microsoft."
    url: "/S2D/01-Intro/"
    btn_class: "btn--primary"
    btn_label: "Go !"
  - image_path: /assets/images/Tour-Eiffel_05b.png
    alt: "fully responsive"
    title: "Blog posts"
    excerpt: "Les trucs et astuces de scripteur accumulées au fil du temps."
    url: "/categories/"
    btn_class: "btn--primary"
    btn_label: "Go !"
  - image_path: /assets/images/Tour-Eiffel_06b.png
    alt: "About"
    title: "A propos"
    excerpt: "A propos de ce site et de son auteur, qui est-il et pourquoi il fait tout cela."
    url: "/about/"
    btn_class: "btn--primary"
    btn_label: "Go !"
---

{% include feature_row id="intro" type="center" %}

{% include feature_row %}

<ul class="entries">
  {% for post in paginator.posts %}
  <li>
    <a href="{{ post.url }}">
    <h3>{{ post.title }}</h3>
    <p class="blogdate">{{ post.date | date: "%d %B %Y" }}</p>
    <div>{{ post.content |truncatehtml | truncatewords: 60 }}</div>
    </a>
  </li>
  {% endfor %}
</ul>