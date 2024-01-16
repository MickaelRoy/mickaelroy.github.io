---
title: "Gérer les heures de connexion autorisées d'un utilisateur Active Directory."
excerpt: "La plupart des propriétés des objets Active Directory sont facilement accessibles en utilisant Get- ou Set- tel que Get-ADUser ou Set-ADUser, mais ce n'est pas si simple en ce qui concerne LogonHours.
Plusieurs étapes et défis sont nécessaires pour créer de la valeur, et dans ce didacticiel, vous apprendrez tous les trucs et astuces nécessaires pour définir les heures de connexion via Windows PowerShell."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Active Directory
---

## Préambule

De toute évidence, la fonctionnalité _Logon Hours_ est un laissé pour compte de Microsoft. Rien ne semble avoir bougé du coté de cet attribut depuis au moins une décennie.

La pluspart des attributs sont modifiable avec des cmdlets dédiées, mais il n'existe pour l'heure absolument rien pour le _Logon Hours_

C'est d'autant plus malheureux que ces attributs me peuvent être défini que sur un compte utilisateur et pas sur un groupe. Automatiser la définition des heures de connexion sur plusieurs utilisateur à la fois semble être indispensable.

Dans cet article je vous présente rapidement les outils que j'ai développés.

## Sources

[farismalaeb](https://www.powershellcenter.com/2021/03/09/manage-ad-user-logon-hours-using-powershell/)

[rlmueller](https://www.rlmueller.net/AllUsersLogonHours.htm)

[francishagyard2](https://content.spiceworksstatic.com/service.community/p/post_attachments/0000184588/5f675101/attached_file/Convert-ADLogonHours.txt)


## Entrons dans le vif

L'article et le code de Faris Malaed est d'un niveau très correct. Je n'ai apporté que tres peu de changement, principalement cosmetiques.

Le changement plus pertinent est le paramètre _LogonPrecedence_, le nom de ce paramètre n'est pas des plus adapté. Comprenez-le comme ceci:

Conjointement à la plage horaire _TimeIn24Format_, _LogonPrecedence_ indique si l'on est en train de spécifer une plage autorisée ou à contrario, interdite. Voyez-le comme un inverseur.

Le second changement est la possibilité de ne pas changer certains jour. Tel que Faris a écrit son code, il y a un manque de granularité: s'il on ne spécifie pas un jour alors, il doit être considéré comme entièrement autorisié ou interdit celon ce qui est précié par _NonSelectedDaysare_.

J'ai donc ajuouté la considération que l'on puisse vouloir simplement ignorer ce jour.

Notez que le paramètre _TimeIn24Format_ attend une list (array), tel que:

```powershell
# 8h à midi, puis 14h à 18h:
-TimeIn24Format @(8,9,10,11,14,15,17)

# pour une plage plus exaustive: 
-TimeIn24Format  @(8,9,10,11,12,13,14,15,16)
# ou mieux: 
-TimeIn24Format  (8..16)
```

En ce qui concerne les jours, chaque jour est un switch, chaque switch indique que le jour sera soumis à la plage _TimeIn24Format_

Un exemple.

```powershell
Set-ADLogonHours -Identity $User -TimeIn24Format (0..5) -Monday -Tuesday -Wednesday -Thursday -Friday -Saturday -LogonPrecedence Deny -NonSelectedDaysare NonWorkingDays

```

<figure style="width: 800px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/2024-01-16_10h52_52.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/2024-01-16_10h52_52.png" alt="AdLogon Output."></a>
  <figcaption>Un output clair.</figcaption>
</figure>

Et là vous allez me dire:

> c'est très graphique comme output...

En effet, j'ai choisi l'option la plus visuelle pour savoir si immediatement si le paramètrage soumis est le bon.

Libre à vous de faire les changements nécessaires si cela ne convient pas. Contrairement au Sel de cuisine, le code est plus facile à retirer qu'à ajouter.

Cela m'amène à la plus grosse partie de mon investissement.

Richard avait de l'idée, Francis aussi. J'en ai encore plus.

En réalité, le code de Francis était très inspirant, je me suis basé dessus, mais l'aspect du code de Richard m'a poussé à aller plus loin.

Pour arriver à obtenir un output qui va au delà de ce qu'a fait Richard, j'ai dû refondre le code de Francis en profondeur.

Pour l'historique voici la boucle de Francis:

```powershell
$ExportArray = For ($Inc = 0; $Inc -lt $User.LogonHours.Count; $Inc += 3) {
        
                    $EndIndex = $Inc + 2

                    Switch ($Inc) {
                        0 {$DayOfWeek = "Sunday"}
                        3 {$DayOfWeek = "Monday"}
                        6 {$DayOfWeek = "Tuesday"}
                        9 {$DayOfWeek = "Wednesday"}
                        12 {$DayOfWeek = "Thursday"}
                        15 {$DayOfWeek = "Friday"}
                        18 {$DayOfWeek = "Saturday"}
                    }

                    $BinaryArray = $User.LogonHours[$Inc..$EndIndex] | ForEach {
                        $TempArray = [Convert]::ToString($_,2).PadLeft(8,'0').ToCharArray()
                        [Array]::Reverse($TempArray)
                        $TempArray
                    }

                    $ExportObj = New-Object PsObject
                    $ExportObj | Add-Member -MemberType NoteProperty -Name "Name" -Value $User.Name
                    $ExportObj | Add-Member -MemberType NoteProperty -Name "SamAccountName" -Value $User.SamAccountName
                    $ExportObj | Add-Member -MemberType NoteProperty -Name "UserPrincipalName" -Value $User.UserPrincipalName
                    $ExportObj | Add-Member -MemberType NoteProperty -Name "DistinguishedName" -Value $User.DistinguishedName
                    $ExportObj | Add-Member -MemberType NoteProperty -Name "DayOfWeek" -Value $DayOfWeek

                    For ($Hour = 0; $Hour -lt 24; $Hour++) {

                        If ($Hour -eq 23) {$EndHour = 0}
                        Else {$EndHour = $Hour + 1}

                        Switch ($BinaryArray[$Hour]) {
                            '1' {$LogonAuth = 'Allowed'}
                            '0' {$LogonAuth = 'Denied'}
                        }
                
                        $ExportObj | Add-Member -MemberType NoteProperty -Name "$Hour-$EndHour" -Value $LogonAuth
                    }
                    $ExportObj
                }
                Return $ExportArray
```

Voici la mienne:

```powershell
                $AllTheWeek = $User.LogonHours.ForEach({
                    $TempArray = [Convert]::ToString($_,2).PadLeft(8,'0').ToCharArray()
                    [Array]::Reverse($TempArray)
                    $TempArray
                })

                $AllTheWeek = -join $AllTheWeek
              # Timezone Check
                If ((Get-TimeZone).BaseUtcOffset.Hours -le 0) {
                    $TimeZoneOffset = $AllTheWeek.Substring(0,((Get-TimeZone).BaseUtcOffset.Hours))
                    $TimeZoneOffset1 = $AllTheWeek.SubString(((Get-TimeZone).BaseUtcOffset.Hours))
                    $FixedTimeZoneOffSet = "$TimeZoneOffset1$TimeZoneOffset"
                }
                If ((Get-TimeZone).BaseUtcOffset.Hours -gt 0) {
                    $TimeZoneOffset = $AllTheWeek.Substring(0,168 - ((Get-TimeZone).BaseUtcOffset.Hours))
                    $TimeZoneOffset1 = $AllTheWeek.SubString(168 - ((Get-TimeZone).BaseUtcOffset.Hours))
                    $FixedTimeZoneOffSet = "$TimeZoneOffset1$TimeZoneOffset"
                }

                $ExportObj = [PSCustomObject]::new()
                $ExportObj.psobject.properties.Add( [psnoteproperty]::new('Name', $User.Name) )
                $ExportObj.psobject.properties.Add( [psnoteproperty]::new('SamAccountName', $User.SamAccountName) )
                $ExportObj.psobject.properties.Add( [psnoteproperty]::new('UserPrincipalName', $User.UserPrincipalName) )
                $ExportObj.psobject.properties.Add( [psnoteproperty]::new('DistinguishedName', $User.DistinguishedName) )

                $BinaryResult = $FixedTimeZoneOffSet -split '(\d{24})' -ne ''

                $Inc = 0
                Foreach ($Result in $BinaryResult) {
                    $Inc++
        
                    Switch ($Inc) {
                        1 { $DayOfWeek = [dayofweek]::Sunday }
                        2 { $DayOfWeek = [dayofweek]::Monday }
                        3 { $DayOfWeek = [dayofweek]::Tuesday }
                        4 { $DayOfWeek = [dayofweek]::Wednesday }
                        5 { $DayOfWeek = [dayofweek]::Thursday }
                        6 { $DayOfWeek = [dayofweek]::Friday }
                        7 { $DayOfWeek = [dayofweek]::Saturday }
                    }
                    $Result = $Result -split '(.)' -ne ''

                    $LogonHours = [ordered]@{}
                    For ($Hour = 0; $Hour -le 23; $Hour++) {
                        $LogonAuth = $Result[$Hour]
                        [Void]$LogonHours.Add(("{0:d2}" -f $Hour), $LogonAuth)
                    }
                    $ExportObj.psobject.Properties.Add( [psnoteproperty]::new($DayOfWeek, $LogonHours) )
                }
```

Sachez que _Set-ADLogonHours_ dépend de _Get-ADLogonHours_. C'est sur ce dernier que se porte le visuel.

Bon, je ne vais pas m'étendre davantage sur le sujet, je pense que vous avez compris l'idée.



Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/ADLogonHours){: .btn .btn--info}