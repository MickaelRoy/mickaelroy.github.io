---
title: "Mettre à jour votre enregistrement DNS dynamique"
excerpt: "Voici un script pour mettre en correspondance votre nom de domaine avec votre adresse IP publique."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - DNS
header:
  teaser: /assets/images/2022-07-12-Update-Dynamic-DNS.webp
---

## DNS dynamique Google

Le DNS dynamique met les noms de domaine Internet en correspondance avec les adresses IP et vous permet d'accéder à un ordinateur de votre réseau domestique où que vous soyez dans le monde.

Bien entendu, vous devez disposer:
- d'un nom de domaine Google
- un nom d'utilisateur pour accéder a l'API Google
- et son mot de passe

Le tout est disponible sur votre dashboard Google Domains

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

On a à peu près compris le principe avec Google, j'irai plus vite à l'essentiel avec NoIP.com. 

Notez simplement que NoIP demande un [User Agent] et qu'il doit être formaté comme ceci:

_User-Agent: Company NameOfProgram/OSVersion-ReleaseVersion maintainer-contact@example.com_

Les valeurs sont arbitraires.

[User Agent]:<https://www.noip.com/integrate/add-noip>

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
