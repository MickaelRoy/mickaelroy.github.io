---
title: "Supprimer les fichiers récalcitrants"
excerpt: |
Il est parfois compliqué de supprimer des fichiers. Le système le verrouille pour ces besoins et redemarrer -certains- services ne fonctionne pas toujours. Un probleme souvent rencontré lors de mise à jour Windows. Heureusement MS-Windows dispose d'un mécanisme pour contourner cette contrainte.

category: PowerShell
classes: wide
comments: true
tags: 
  - PowerShell
  - Tips
---

## Préambule

Ici je vous livre un cmdlet qui permet la suppression d'un fichier au redémarrage suivant du systeme d'exploitation.

Dans le cadre de la mise à jour de l'agent zabbix il m'est arrivé de ne pas pouvoir supprimer l'executable de l'agent et ce, même après la suppréssion du service.

Le plus étonnant, c'est que le fichier peut être supprimé via l'explorateur de fichier et, il est toujours possible de la déplacer ou le renommer. Pourtant, la suppression, quelque soit la méthode utilisée (remove-item, method .net, method WMI...) nous claque un accès refusé.

En fin de compte, j'ai fini par abdiquer. Mon script de mise à jour renomme le fichier et j'ordonne sa suppression au prochain démarrage.


## Sources

[heyscriptingguy](https://blogs.technet.com/b/heyscriptingguy/archive/2013/10/19/weekend-scripter-use-powershell-and-pinvoke-to-remove-stubborn-files.aspx)

[leeholmes](http://www.leeholmes.com/blog/2009/02/17/moving-and-deleting-really-locked-files-in-powershell/)

[Microsoft](https://learn.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-movefileexa)

## Substance

Je suis bien incapable de vous expliquer le contenu entre @''@, tout ce que je peux vous dire c'est que l'on s'en sert pour créer une classe dont on se sert pour utiliser la fonction système MoveFileEx.

```powershell
Function Remove-ItemAnyway {
    [CmdletBinding()]
    Param(
        [parameter(Mandatory=$true)]
	    [string] $Path
    )

Add-Type @'
    using System;
    using System.Text;
    using System.Runtime.InteropServices;
       
    public class Posh
    {
        public enum MoveFileFlags
        {
            MOVEFILE_DELAY_UNTIL_REBOOT = 0x00000004
        }
 
        [DllImport("kernel32.dll", SetLastError = true, CharSet = CharSet.Unicode)]
        static extern bool MoveFileEx(string lpExistingFileName, string lpNewFileName, MoveFileFlags dwFlags);
        
        public static bool MarkFileDelete (string sourcefile)
        {
            return MoveFileEx(sourcefile, null, MoveFileFlags.MOVEFILE_DELAY_UNTIL_REBOOT);         
        }
    }
'@

    $Path = [System.IO.Path]::GetFullPath([System.IO.Path]::Combine($PWD.Path, $Path))

    Try {
        Remove-Item $Path -ErrorAction Stop
    } Catch {
        $deleteResult = [Posh]::MarkFileDelete($Path)
        If ($deleteResult) {
            Write-Warning -Message "$Path has been marked for deletion"
        }
    }
}
```

En relisant ce code, on peut se rendre compte que l'on pourrait étendre ses capacités en utilisant les autres methodes proposées par la fonction.

```powershell
public enum MoveFileFlags
{
MOVEFILE_REPLACE_EXISTING = 0x00000001,
MOVEFILE_COPY_ALLOWED = 0x00000002,
MOVEFILE_DELAY_UNTIL_REBOOT = 0x00000004,
MOVEFILE_WRITE_THROUGH = 0x00000008,
MOVEFILE_CREATE_HARDLINK = 0x00000010,
MOVEFILE_FAIL_IF_NOT_TRACKABLE = 0x00000020
}
```

> Je n'en vois pas l'utilité.

