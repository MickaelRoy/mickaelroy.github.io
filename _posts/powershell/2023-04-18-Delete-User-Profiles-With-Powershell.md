---
title: "Supprimer un profil utilisateur."
excerpt: "Supprimer un profil utilisateur residuel proprement."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - profiles
---

## Préambule

Lorsqu'une personne démissionne, son profil Windows reste dans le système.

Habituellement, tout va bien jusqu'à ce que des années plus tard, lorsque les mises à jour de sécurité soient mises à jour, certains des anciens fichiers peuvent devenir malveillants.

Il existe 2 façons de supprimer les anciens profils Windows.

## Entrons dans le vif

```powershell
Get-CimInstance win32_userprofile | Where-Object {(!$_.Special) -and ($_.LastUseTime -lt  (Get-Date).AddDays(-30)) } | Remove-CimInstance
```

Cela supprimera les anciens profils de plus de 30 jours. Il ignorera les comptes "spéciaux" tels que LocalService, NetworkService, etc.

```powershell
Get-CimInstance win32_userprofile | Where-Object {(!$_.Special) -and $_.LocalPath -match "adm_*" } | Remove-CimInstance
```

Cela supprimera les profils dont le chemin contient "adm_". Il ignorera les comptes "spéciaux" tels que LocalService, NetworkService, etc.


Sinon, Voici un exemple plus complet qui vient d'un autre [blog](https://paullimblog.wordpress.com/2019/12/31/delete-user-profiles-with-powershell/).

```powershell
# Powershell script to remove user profiles

# Update the $array the list of user profiles to remove

$array = @("localuser1","localuser2")

$array | ForEach-Object {

    Write-Host 'Removing: ' $_

    $localPath = $_

    $result = Get-CimInstance Win32_UserProfile | Where-Object { $_.LocalPath.split('\')[-1] -eq $localPath }

    if ($result -eq $null) {
        Write-Host 'Profile does not exist: ' $localPath
        return
    }

    # remove user profile

    Get-CimInstance Win32_UserProfile | Where-Object { $_.LocalPath.split('\')[-1] -eq $localPath } | Remove-CimInstance `

    # check if profile has been removed

    $result = Get-CimInstance Win32_UserProfile | Where-Object { $_.LocalPath.split('\')[-1] -eq $localPath }

    if ($result -eq $null) {
        Write-Host 'Profile is removed: ' $localPath
    } else {
        Write-Host 'Error removing profile: ' $localPath
    }
}
```

J'espere que les explications en commentaire suffiront...

<figure style="width: 400px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2023-01-12_23h19_22.webp"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2023-01-12_23h19_22.webp" alt="3 exemples."></a>
  <figcaption>Générez des mots de passes.</figcaption>
</figure>

## Conclusion

Bien entendu, il est perfectible, vous constatez qu'il y a un argument MaxSpecial, mais pas de MinSpecial..
je vous laisse me chambrer en commentaire 😁

Ça, c'est cadeau, à plus !