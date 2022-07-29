---
title: "Emplacement par defaut des VM"
excerpt: "Definir l'emplacement des VM par défaut sur un cluster S2D."
classes: wide
---
Cette commande permet de modifier l'emplacement par defaut lors de la création de machines virtuelles.

```powershell
Set-VMHost -VirtualMachinePath 'C:\ClusterStorage\Volume1'
```

C'est purement cosmetic quand on y pense... 🤔
