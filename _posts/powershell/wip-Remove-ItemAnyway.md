

Function Remove-ItemAnyway {
    [CmdletBinding()]
    Param(
        [parameter(Mandatory=$true)]
	    [string] $Path
    )
    # the code below has been used from
    #    https://blogs.technet.com/b/heyscriptingguy/archive/2013/10/19/weekend-scripter-use-powershell-and-pinvoke-to-remove-stubborn-files.aspx
    # with inspiration from
    #    http://www.leeholmes.com/blog/2009/02/17/moving-and-deleting-really-locked-files-in-powershell/
    # and error handling from
    #    https://blogs.technet.com/b/heyscriptingguy/archive/2013/06/25/use-powershell-to-interact-with-the-windows-api-part-1.aspx

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
