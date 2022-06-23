---
title: "Jumbo frame"
excerpt: "Activer le jumbo frame pour concevoir un cluster S2D."
classes: wide
---

## Activer le jumbo frame sur les cartes physiques

```powershell
# Enable Jumbo
Set-NetAdapterAdvancedProperty -Name $Primary1 -RegistryKeyword '*JumboPacket'  -RegistryValue 9014
Set-NetAdapterAdvancedProperty -Name $Primary2 -RegistryKeyword '*JumboPacket'  -RegistryValue 9014

```

Un point à ne pas négliger, le jumbo frame apporte vraiment beaucoup dans l’hyper-convergence.