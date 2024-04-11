---
title: Configuration de la police de la console Windows par défaut
excerpt: Découvrez comment personnaliser la police de la console Windows avec ces commandes de registre.

description: Apprenez à configurer la police par défaut de la console Windows en utilisant des commandes de registre pour personnaliser l'apparence selon vos préférences.

category: Hors-Series
classes: wide
comments: true
tags: 
  - Registry
  - Font
  - Console
---

Cette astuce présente une série de commandes de registre pour configurer la police par défaut de la console Windows. Ces commandes peuvent être utiles pour personnaliser l'apparence de la console système selon vos préférences.

## Instructions

1. Charger le profil par défaut de l'utilisateur à partir du fichier `ntuser.dat` :

   ```bash
   reg load HKLM\DEFAULT c:\users\default\ntuser.dat
   ```

2. Définir la famille de polices de la console :

   ```bash
    reg add "HKLM\DEFAULT\Console" /v FontFamily /t REG_DWORD /d 0x36 /f
   ```

3. Définir la taille de la police de la console :

   ```bash
    reg add "HKLM\DEFAULT\Console" /v FontSize /t REG_DWORD /d 0xe0000 /f
   ```

4. Définir le poids de la police de la console :

   ```bash
    reg add "HKLM\DEFAULT\Console" /v FontWeight /t REG_DWORD /d 0x190 /f
   ```

5. Désactiver l'interception de copier-coller dans la console :

   ```bash
    reg add "HKLM\DEFAULT\Console" /v InterceptCopyPaste /t REG_DWORD /d 0 /f
   ```

6. Définir la police pour PowerShell :
  Pour la version 32 bits de PowerShell :

   ```bash
    reg add "HKLM\DEFAULT\Console\%SystemRoot%_System32_WindowsPowerShell_v1.0_powershell.exe" /v FaceName /t REG_SZ /d Consolas /f
   ```

  Pour la version 64 bits de PowerShell :

   ```bash
    reg add "HKLM\DEFAULT\Console\%SystemRoot%_SysWOW64_WindowsPowerShell_v1.0_powershell.exe" /v FaceName /t REG_SZ /d Consolas /f
   ```

7. Décharger le profil par défaut de l'utilisateur :

   ```bash
    reg unload HKLM\DEFAULT
   ```

Ces commandes doivent être exécutées avec des privilèges administratifs et peuvent nécessiter un redémarrage pour prendre effet.