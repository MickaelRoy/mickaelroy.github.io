---
title: "Mettez à jour automatiquement votre IP avec style !"
excerpt: |
  Découvrez comment mettre à jour automatiquement votre adresse IP avec une touche de style grâce à PowerShell.
  
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - DNS
  - Domains
  - IP
header:
  teaser: /assets/images/2022-07-12-Update-Dynamic-DNS.webp
---

Ah, la magie de PowerShell combinée à la puissance de Google Domains ! Dans cet article, vous allez découvrir comment mettre à jour automatiquement votre adresse IP avec une touche de style.

## Le Scénario

Imaginez-vous, dans un monde où votre adresse IP change plus souvent que les saisons.

Pas de panique ! Avec quelques lignes de code et une pincée de bonne humeur, vous pouvez automatiser cette tâche fastidieuse.

## Pourquoi est-ce utile ?

Un DNS dynamique est un service qui permet de mettre à jour automatiquement l'adresse IP associée à un nom de domaine. C'est particulièrement utile lorsque vous avez un site Web ou un service hébergé sur votre réseau domestique ou sur un serveur dont l'adresse IP est susceptible de changer.

En utilisant le code PowerShell présenté ici, vous pouvez automatiser le processus de mise à jour de votre adresse IP sur Google Domains (ou NoIP). Cela signifie que votre site Web ou service reste accessible même si votre adresse IP change régulièrement.



## DNS dynamique Google

### Les Prérequis

Avant de plonger dans le code, assurez-vous d'avoir les éléments suivants :

- **Un Nom de Domaine Google :** Vous aurez besoin d'un nom de domaine enregistré auprès de Google Domains. Si vous n'en avez pas encore, vous pouvez en acheter un sur leur site web.

- **Un Nom d'Utilisateur Google :** Vous devrez disposer d'un nom d'utilisateur pour accéder à l'API Google Domains. Assurez-vous de le noter quelque part en lieu sûr !

- **Votre Mot de Passe Google :** Évidemment, vous aurez également besoin de votre mot de passe Google pour vous connecter à l'API. Veillez à choisir un mot de passe sécurisé et à ne le partager avec personne.

Le tout est disponible sur votre dashboard Google Domains

### Le code

```powershell
$hostname = "subdomain.domain.xx"
$user = "Ch@ineUserCheul0u"
$pass = "M0t2p@ssCheulou"
$pair = "$($user):$($pass)"

#Get a page with your current IP
$MyIpPage = Invoke-WebRequest "https://domains.google.com/checkip"

If ($MyIpPage.RawContent -match "(?:[0-9]{1,3}.){3}[0-9]{1,3}")
{
    #encode the username and password for the header
    $bytes = [System.Text.Encoding]::ASCII.GetBytes($pair)
    $base64 = [System.Convert]::ToBase64String($bytes)
    $basicAuthValue = "Basic $base64"
    $headers = @{ Authorization =  $basicAuthValue }

    #Build up the URL
    $url = "https://domains.google.com/nic/update?hostname={0}&myip={1}" -f $hostname, $MyIpPage

    #Invoke the URL
    $resp = Invoke-WebRequest -Uri $url -Headers $headers
    $resp.Content 
}
```

Les réponses attendues dans $resp.Content sont disponibles sur le [Support Google]

[Support Google]: <https://support.google.com/domains/answer/6147083?hl=fr#zippy=%2Cutiliser-lapi-pour-mettre-%C3%A0-jour-votre-enregistrement-dns-dynamique>

## DNS dynamique NoIP

On a à peu près compris le principe avec Google, mais que diriez-vous d'aller encore plus vite à l'essentiel avec NoIP.com ? 

NoIP demande un [User Agent] spécifique qui doit être formaté comme suit :

_User-Agent: Company NameOfProgram/OSVersion-ReleaseVersion maintainer-contact@example.com_

Les valeurs sont arbitraires mais assurez-vous de suivre ce format lors de l'intégration.

[User Agent]:<https://www.noip.com/integrate/add-noip>

Voici un exemple de code pour mettre à jour automatiquement votre adresse IP avec NoIP.com :

```powershell
$myhost ="subdomain.domain.xx"
$user = "ChaineUser"
$pass = ConvertTo-SecureString "MotDePasse" -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential($user,$pwd)

#Get a page with your current IP
$MyIpPage = Invoke-WebRequest "https://domains.google.com/checkip"
$myexternalIP = $MyIpPage.Content

# User agent string to send to DynDNS.
$UserAgent = "domain.xx/0.1 toto@onthe.net"

#Invoke the URL
$NoIpResp = Invoke-WebRequest -Credential $Cred -Uri "https://dynupdate.no-ip.com/nic/update?hostname=$myhost&myip=$myexternalIP" -UserAgent $UserAgent
$NoIpResp.Content

```
