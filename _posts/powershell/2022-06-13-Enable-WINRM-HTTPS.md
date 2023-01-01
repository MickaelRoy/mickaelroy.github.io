---
title:  "Activer WINRM/PSRemoting avec chiffrement"
excerpt: "Configurez WINRM et faites du PSRemote en SSL."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Tutoriel
---

# WINRM

Un petit mode operatoire tout simple aujourd'hui.

Activer WINRM c'est facile, mais l'avoir en HTTPS et le configurer en ligne de commande, ca demande une bonne mémoire ou de l'avoir noté quelque part...

Je ne vous explique pas ici comment lancer un Enable-PsRemoting...? je suppose que c'est déjà fait, le port 5985 est donc déjà ouvert.

## Configurer WINRM avec SSL

Comme je suis un chique type, je vous donne l'astuce pour inclure les noms d'hotes alternatifs.

```powershell
# Nom de l'hôte
$subject = "winserv001"

# Noms de l'hôte et alias.
$subjects = @(
    "winserv001"
    "winserv001.home"
)

$Cert = New-SelfSignedCertificate  -DnsName $Subjects -CertStoreLocation cert:\LocalMachine\My -TextExtension '2.5.29.37={text}1.3.6.1.5.5.7.3.1' -Subject $Subject

New-WSManInstance  -ResourceURI winrm/config/Listener -SelectorSet @{Address="*";Transport="HTTPS"} -ValueSet @{Hostname="$Subject";CertificateThumbprint="$($Cert.Thumbprint)"}

```
Ici le paramètre Address est "*", donc pas de restriction. A vous de gérer vos contraintes et d'en changer la valeur si nécessaire.

## Le pare-feu

Il est probable que le port 5986 soit toujours fermé.

Curieux, Microsoft ne semble pas mettre à disposition de règle de pare-feu pour le port securisé.

Tant pis, on va la créer... Il y en a une pour le port standard qui se nomme WINRM-HTTP-In-TCP, créons la deuxième (sur la machine de destination):

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

Après ca, la communication doit pouvoir être établie entre votre client (Source) et le serveur (Destnation)

# Bonus

Je vous ai décrit une base, pour aller plus loin dans l'automation, il y a une cmdlet sur mon GitHub.

La cmdlet repose sur le module powershell Active-Directory pour recuperer les différents SPN et en déduire les noms d'hôtes alternatifs.

Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Enable-WinRMforHTTPS){: .btn .btn--info}
