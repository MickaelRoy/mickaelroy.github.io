---
title: "Créer une zone d'entrée graphique personnalisée."
excerpt: |
  Apprenez à créer une zone d'entrée graphique personnalisée en utilisant PowerShell et WinForms.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Winform
  - GUI
  - Tips
  - Cmdlet
header:
  teaser: /assets/images/Invoke-Inputbox.webp
  image_description: "Boite de dialogue en WInForm."
---

Je vous avoue que la majeure partie de ce que je vous présente ici est expliqué en détail sur le site de [Microsoft](https://learn.microsoft.com/fr-fr/powershell/scripting/samples/creating-a-custom-input-box?view=powershell-5.1).

Plutôt que de m'attarder sur les bases de Winform, qui sont censées être dépassées depuis un bon moment déjà, je vais plutôt me concentrer sur les astuces qui vont donner du style à cette petite GUI... Bon, soyons honnêtes, on parle de Winform, donc ne vous attendez pas non plus à des miracles.

> GUI, ça vous dit quelque chose ? Eh oui, même si cela peut sembler étrange, GUI et PowerShell ne sont pas forcément incompatibles.

<figure style="width: 303px" class="align-center">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/Invoke-Inputbox.webp" alt="InputBox">
  <figcaption>Illustation de la boite de dialogue.</figcaption>
</figure>

## Astuce n°1

```powershell
[System.Windows.Forms.Application]::EnableVisualStyles()
```

Cette ligne active les styles visuels pour votre application, ce qui signifie qu'elle bénéficiera des couleurs, des polices et d'autres éléments visuels du thème de votre système d'exploitation.

Astuce: Assurez-vous de placer cette ligne avant toute création d'objet dans votre code. C'est un peu comme installer les décorations avant de commencer à meubler une pièce.

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

Dans cet exemple, nous créons une nouvelle fenêtre Windows Forms avec une icône personnalisée (16x16 pixels suffit) et une zone de texte.

Vous pouvez modifier la taille, l'emplacement et d'autres propriétés de la zone d'entrée graphique selon vos besoins.

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

Ajouter un effet de fondu pour afficher la GUI est une excellente idée ! En utilisant **1*$($form.Opacity/15)**  vous obtiendrez une sensation d'accélération, ce qui rendra l'apparition de la fenêtre plus fluide et agréable pour l'utilisateur.

<figure style="width: 303px" class="align-center">
	<a href="{{ site.url }}{{ site.baseurl }}/assets/images/Invoke-InputBox.gif"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/Invoke-InputBox.gif" alt="Fade-In-InputBox"></a>
  <figcaption>Effet fondu.</figcaption>
</figure>


## Astuce n°4

```powershell
        $form.Font = [System.Drawing.SystemFonts]::DefaultFont
```

Avec celle-co vous forcez votre formulaire à adopter la police par défaut du système. Cela garantit une cohérence avec l'apparence générale de l'interface de votre système d'exploitation.

Maintenant, si vous voulez sortir du lot et ajouter une touche de personnalité, n'hésitez pas à choisir une autre police qui vous démarquera. Après tout, il n'y a pas de mal à se démarquer un peu, n'est-ce pas ?

## Astuce n°5

```powershell
        $form.Close()
        $form.Dispose()
```

Lorsque vous fermez votre formulaire avec __$form.Close()__, n'oubliez pas d'appeler __$form.Dispose()__ juste après. Cette méthode est importante, surtout si vous développez sous ISE. Elle permet d'éviter les éventuels freezes de l'ISE, assurant ainsi une meilleure stabilité dans votre travail.

## Astuce n°6

Offrez-vous le luxe d'un raccourci clavier Ctrl+C pour annuler une action. Ajoutez simplement cette fonctionnalité à votre formulaire avec le code suivant :

```powershell
        $form.Add_KeyDown({
            # $Global:Key = $_ # Astuce pour consigner la combinaison de touche dans une variable
            If (($_.Control) -and ($_.KeyCode -eq [System.Windows.Forms.Keys]::C)) {
                $cancelButton.PerformClick()
            }
        })
```

OMaintenant, chaque fois que vous appuierez sur __Ctrl+C__, cela déclenchera automatiquement l'action associée au bouton d'annulation. Pratique, n'est-ce pas ?

## Conclusion

Et voilà, nous arrivons à la fin de ce script !

Je pourrais passer des heures à détailler chaque petite ligne, mais soyons honnêtes, ce n'est pas non plus le script du siècle. Néanmoins, il offre un petit aperçu des nombreuses possibilités offertes par PowerShell.

D'ailleurs, avez-vous déjà entendu parler du fait que PowerShell ISE est développé en... PowerShell ?

Incroyable, n'est-ce pas ? Cela montre à quel point ce langage est puissant et polyvalent.

Qui aurait cru qu'on puisse créer un outil de développement avec le langage même qu'il utilise ? Fascinant !

Ca, c'est cadeau, à plus !

[Lien Direct](https://github.com/MickaelRoy/Cmdlets/tree/main/Invoke-InputBox){: .btn .btn--info}


