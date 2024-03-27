---
title:  "Configuration de WinRM avec un certificat SSL auto-signé en PowerShell"
excerpt: |
  Découvrez comment configurer WinRM avec un certificat SSL auto-signé pour sécuriser les communications à distance en PowerShell.
  
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Tutoriel
  - WinRM
  - SSL
  - Certificat
---

Dans les environnements Windows, la configuration de WinRM (Windows Remote Management) est essentielle pour permettre la gestion à distance des machines. Voici comment configurer WinRM avec un certificat SSL auto-signé pour sécuriser les communications à distance en PowerShell.

Je ne vous explique pas ici comment utiliser Enable-PsRemoting...? je suppose que c'est déjà fait, le port 5985 est donc déjà ouvert.

## Configurer WINRM avec SSL

### Génération du certificat SSL auto-signé

> Comme je suis un chique type, je vous donne l'astuce pour inclure les noms d'hôtes alternatifs.

```powershell
# Nom de l'hôte
$subject = "winserv001"

# Noms de l'hôte et alias.
$subjects = @(
    "winserv001"
    "winserv001.home"
)

$Cert = New-SelfSignedCertificate  -DnsName $Subjects -CertStoreLocation cert:\LocalMachine\My -TextExtension '2.5.29.37={text}1.3.6.1.5.5.7.3.1' -Subject $Subject
```

Ce code génère un certificat SSL auto-signé pour sécuriser les communications avec l'hôte winserv001, avec plusieurs noms d'hôte et alias.

### Configuration de WinRM avec le certificat SSL

```powershell
New-WSManInstance  -ResourceURI winrm/config/Listener -SelectorSet @{Address="*";Transport="HTTPS"} -ValueSet @{Hostname="$Subject";CertificateThumbprint="$($Cert.Thumbprint)"}

```
Ici le paramètre Address est "*", donc pas de restriction. A vous de gérer vos contraintes et d'en changer la valeur si nécessaire.

Ce code configure WinRM pour écouter les connexions HTTPS et utilise le certificat SSL auto-signé généré précédemment.

## Le pare-feu

Il est possible que le port 5986 soit initialement fermé par le pare-feu, ce qui peut empêcher la communication sécurisée avec WinRM. Curieusement, Microsoft ne fournit pas de règle de pare-feu par défaut pour ce port. Cependant, nous pouvons facilement remédier à cela en créant une règle spécifique sur la machine de destination:

```powershell
    New-NetFirewallRule -Name "WINRM-HTTPS-In-TCP" `
        -DisplayName "Windows Remote Management (HTTPS-In)" `
        -Description "Inbound rule for Windows Remote Management via WS-Management. [TCP 5986]" `
        -Group "Windows Remote Management" `
        -Program "System" `
        -Protocol TCP `
        -LocalPort "5986" `
        -Action Allow `
        -Profile Domain,Private

```

Une fois cette règle de pare-feu créée, la communication sécurisée entre votre client (source) et le serveur (destination) devrait pouvoir être établie sans problème.

# Bonus

Je vous ai décrit une base, pour aller plus loin dans l'automation, il y a une cmdlet sur mon GitHub.

La cmdlet repose sur le module powershell Active-Directory pour récupérer les différents SPN et en déduire les noms d'hôtes alternatifs.

Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Enable-WinRMforHTTPS){: .btn .btn--info}
