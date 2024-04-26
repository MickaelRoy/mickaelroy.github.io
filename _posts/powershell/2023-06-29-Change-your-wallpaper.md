---
title: "Changer le papier peint."
excerpt: |
    Changer votre fond d'écran Windows par script.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
  - Wallpaper
  - Background
  - Cmdlet
---

## Préambule

Ici je vous présente de quoi modifier le fond d'écran windows, autrement dit le papier peint.

Ce n'est pas du powershell a proprement parler puisque l'astuce consiste en l'instanciation d'une API bas-niveau Windows. Je ne vais pas rentrer dans les details, [d'autres l'on fait pour moi](https://fuzzysecurity.com/tutorials/24.html).

## Entrons dans le vif

```powershell
#Modify Path to the picture accordingly to reflect your infrastructure
$imgPath="\\Domain.lab\netlogon\Wallpaper.png"
$code = @' 
using System.Runtime.InteropServices; 
namespace Win32{ 
    
     public class Wallpaper{ 
        [DllImport("user32.dll", CharSet=CharSet.Auto)] 
         static extern int SystemParametersInfo (int uAction , int uParam , string lpvParam , int fuWinIni) ; 
         
         public static void SetWallpaper(string thePath){ 
            SystemParametersInfo(20,0,thePath,3); 
         }
    }
 } 
'@

add-type $code 

#Apply the Change on the system 
[Win32.Wallpaper]::SetWallpaper($imgPath)
```

Ci-dessus, la version simplifiée, ce code permet de changer le fond d'écran, mais pas de choisir son format.


```powershell
<#PSScriptInfo
    .VERSION 1.0.0.0
    .GUID cfc2e719-67d8-4722-b594-3d198a1206c7
    .FILENAME Set-DesktopWallpaper.ps1
#>
function Set-DesktopWallpaper {
    <#
    .DESCRIPTION
        Sets a desktop background image
    .PARAMETER PicturePath
        Defines the path to the picture to use for background
    .PARAMETER Style
        Defines the style of the wallpaper. Valid values are, Tiled, Centered, Stretched, Fill, Fit, Span
    .EXAMPLE
        Set-DesktopWallpaper -PicturePath "C:\pictures\picture1.jpg" -Style Fill
    .EXAMPLE
        Set-DesktopWallpaper -PicturePath "C:\pictures\picture2.png" -Style Centered
    .NOTES
        Supports jpg, png and bmp files.
    #>

    [CmdletBinding()]
    param(
        [Parameter(Mandatory)][String]$PicturePath,
        [ValidateSet('Tiled', 'Centered', 'Stretched', 'Fill', 'Fit', 'Span')]$Style = 'Fill'
    )


    BEGIN {
        $Definition = @"
[DllImport("user32.dll", EntryPoint = "SystemParametersInfo")]
public static extern int SystemParametersInfo(int uAction, int uParam, string lpvParam, int fuWinIni);
"@

        Add-Type -MemberDefinition $Definition -Name Win32SystemParametersInfo -Namespace Win32Functions
        $Action_SetDeskWallpaper = [int]20
        $Action_UpdateIniFile = [int]0x01
        $Action_SendWinIniChangeEvent = [int]0x02

        $HT_WallPaperStyle = @{
            'Tiles'     = 0
            'Centered'  = 0
            'Stretched' = 2
            'Fill'      = 10
            'Fit'       = 6
            'Span'      = 22
        }

        $HT_TileWallPaper = @{
            'Tiles'     = 1
            'Centered'  = 0
            'Stretched' = 0
            'Fill'      = 0
            'Fit'       = 0
            'Span'      = 0
        }

    }


    PROCESS {
        Set-ItemProperty -Path 'HKCU:\Control Panel\Desktop' -Name wallpaperstyle -Value $HT_WallPaperStyle[$Style]
        Set-ItemProperty -Path 'HKCU:\Control Panel\Desktop' -Name tilewallpaper -Value $HT_TileWallPaper[$Style]
        $null = [Win32Functions.Win32SystemParametersInfo]::SystemParametersInfo($Action_SetDeskWallpaper, 0, $PicturePath, ($Action_UpdateIniFile -bor $Action_SendWinIniChangeEvent))
    }
}
```

Ce script n'est pas de moi, je ne l'ai même pas optimisé;

Si cela avait été le cas je n'aurai pas fait usage de bloc BEGIN/PROCESS qui est une notion completement inutile si le cmdlet n'est pas destiné à recevoir des objets en entrée.

Ça, c'est cadeau, à plus !