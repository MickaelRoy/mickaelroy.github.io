---
title: "Créer une zone d'entrée graphique personnalisée."
excerpt: "Dans Windows PowerShell 3.0 et versions ultérieures, utilisez les fonctionnalités de création de formulaires de Microsoft .NET Framework pour créer une zone d'entrée graphique personnalisée à l'aide d'un script."
category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Winform
  - Tips
header:
  teaser: /assets/images/Invoke-Inputbox.webp
  image_description: "Boite de dialogue en WInForm."
---

## Préambule

Je ne vous cache pas que ce que je vous montre ici, 90% est expliqué chez nos amis [Microsoft](https://learn.microsoft.com/fr-fr/powershell/scripting/samples/creating-a-custom-input-box?view=powershell-5.1).

Je ne vais donc pas m'attarder sur ce qu'est du Winform, qui est censé être déprécié depuis des années, mais plutôt me concentrer sur les tips qui font que cette petite GUI aura de la gueule... enfin... c'est du Winform, ne vous attendez pas non plus à un truc de fou.

> GUI tu dis ? ouais j'lé dit. GUI et PowerShell ne s'oppose pas forcément, même si cela semble contre-intuitif.

<figure style="width: 303px" class="align-center">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/Invoke-Inputbox.webp" alt="InputBox">
  <figcaption>Illustation de la boite de dialogue.</figcaption>
</figure>

## Astuce n°1

```powershell
[System.Windows.Forms.Application]::EnableVisualStyles()
```

Cette méthode active les styles visuels pour l’application. Les styles visuels sont les couleurs, les polices et d’autres éléments visuels qui forment un thème de système d’exploitation.

Doit être placé dans le code avant toute création d'objet.

## Astuce n°2

```powershell
# This base64 string holds the bytes that make up the red 'L' icon (just an example for a 16x16 pixel image)
$iconBase64 = 'AAABAAEAEBAAAAEAIABoBAAAFgAAACgAAAAQAAAAIAAAAAEAIAAAAAAAQAQAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACQYlPRIKTHQPCj5eDgk6WQ4JPFwMCC9KCQYnPQoG
        KUALBy1JBwYjPQEBBg0AAAAAAAAAAAAAAAAAAAAAAAAAAA0JN1MlGKDkKhyw8igaqvApHLH1Jhme
        4BsQcakaD2ykGhFysB4Tgb4QCkNiAAAAAAAAAAAAAAAAAAAAAAAAAAAIBCM5IhiU0DAgzf8qHLf5
        KBuv9Cwdu/8aEXCsGRFvqxoRb6whFY7LIhaPywUDEx4AAAAAAAAAAAAAAAAAAAAACAQiOSIWkcwy
        IdL/HRR+sw4JOloQC0ZpCQUlPwoGK0UKBilECwcuSBYOWosJBCQ/AAAAAAAAAAAAAAAAAAAAAAgE
        IjkjF5HNMyLX/xcPYIoAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAMMAwIKFgAAAAAAAAAAAAAA
        AAAAAAAIBCI5IxeRzTMi1v8YEGWQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAACAQiOSMXkc0zItb/GBBlkAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAgEIjkjF5HNMyLW/xgQZZAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIBCI5IxeRzTMi1v8YEGWQAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACAQiOSIWkMwzItf/Fw9mkgAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAgEIjkjF5LOMyHW/xYPYYsA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAANCThTKhuu9ykZ
        qecNCDRQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAQEEDQg1
        WBgQZpsNCDZRAQEFCwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AQEBBAMCDBwAAAEIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        AAAAAAAAAAAAAA=='
        $iconBytes = [Convert]::FromBase64String($iconBase64)
        # initialize a Memory stream holding the bytes
        $stream = [System.IO.MemoryStream]::new($iconBytes, 0, $iconBytes.Length)
        $form.Icon = [System.Drawing.Icon]::FromHandle(([System.Drawing.Bitmap]::new($stream).GetHIcon()))
        $form.ShowIcon = $True
```

Ajoutez une belle icone pour votre application. 16x16 pixels suffit, embarquez le dans un string en base64.

## Astuce n°3

```powershell
        $form.Opacity = 0.01
        $timer = New-Object System.Windows.Forms.Timer
        $timer.Interval = 10;  #we'll increase the opacity every 10ms
        $timer.add_Tick({
            If ($form.Opacity -eq 1) {
                $timer.Stop()
                $timer.Dispose()
            }
            Else {
                $form.Opacity += 1*$($form.Opacity/15)
            }
        })
        $timer.Start()
```

Un effet de fondu pour afficher la GUI, en voilà une bonne idée. **1*$($form.Opacity/15)** donnera une sensation d'accélération...

<figure style="width: 303px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/Invoke-InputBox.gif"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/Invoke-InputBox.gif" alt="Fade-In-InputBox"></a>
  <figcaption>Effet fondu.</figcaption>
</figure>


## Astuce n°4

```powershell
        $form.Font = [System.Drawing.SystemFonts]::DefaultFont
```

Forcez le font system par défaut pour une cohérence avec l'OS.
, ou au contraire, utilisez en une autre pour vous démarquer.

## Astuce n°5

```powershell
        $form.Close()
        $form.Dispose()
```

N'oubliez pas le Dispose() après le Close(). Si vous développez sous ISE, cela évitera les freezes de ce dernier.

## Astuce n°6

```powershell
        $form.Add_KeyDown({
            # $Global:Key = $_ # Astuce pour consigner la combinaison de touche dans une variable
            If (($_.Control) -and ($_.KeyCode -eq [System.Windows.Forms.Keys]::C)) {
                $cancelButton.PerformClick()
            }
        })
```

Offrez-vous le luxe d'un ctrl+C

## Conclusion

Voilà, je ne vais pas détailler **tout** le contenu de ce script. Il n'est pas foufou, mais il donne un petit aperçu des possibilités offertes par PowerShell.

D'ailleurs le saviez-vous ? PowerShell ISE, est développé en PowerShell il parait...

Ça, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Invoke-InputBox){: .btn .btn--info}


