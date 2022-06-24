---
title: "Augentation de la taille des journmaux"
excerpt: "Definir l'emplacement des VM par défaut sur un cluster S2D."
classes: wide
---

> ℹ️ facultatif

Augmenter leur taille permet plus de retention. Par defaut le taux de retention est trop petit et la congestion est fréquente.

```shell
# extend log max size
wevtutil set-log "Microsoft-Windows-SMBServer/Analytic" /enabled:false
wevtutil set-log "Microsoft-Windows-SMBServer/Analytic" /enabled:true /quiet:true /retention:true /maxsize:52428800

wevtutil set-log "Microsoft-Windows-Hyper-V-VmSwitch-Diagnostic" /enabled:false
wevtutil set-log "Microsoft-Windows-Hyper-V-VmSwitch-Diagnostic" /enabled:true /quiet:true /retention:true /maxsize:52428800

wevtutil set-log "Microsoft-Windows-Kernel-IoTrace/Diagnostic" /enabled:false
wevtutil set-log "Microsoft-Windows-Kernel-IoTrace/Diagnostic" /enabled:true /quiet:true /retention:true /maxsize:52428800
```
