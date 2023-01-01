---
title:  "Out-Log: une cmdlet qui affiche la couleur"
excerpt: "Quand on veut horodater les processus longs, voir en un coup d'oeil les évènements importants."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - cmdlet
---

# Out-Log

## Description

Une cmdlet verbeuse qui permet de journaliser en couleur.

Je m'en sers très souvent dans des scripts qui demandent beaucoup d'actions et de contrôles.

Il pourra remplacer les Write-Host, Write-Warning n'co.

## Moins de bla-bla, plus de demo

Je vous montre tout de suite à quoi ça ressemble:

![First tips image]({{ site.url }}{{ site.baseurl }}/assets/images/2022-06-04-Out-Log-Demo.gif)

## Detournement ?
Petit hack avant de partir... il ne fonctionne pas sur ISE, mais c'est rigolo.

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
