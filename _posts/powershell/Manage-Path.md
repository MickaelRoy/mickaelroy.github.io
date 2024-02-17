
```powershell
Function Add-Path {
    Param(
        [Parameter(Mandatory=$false)]
        [ValidateSet("PATH", "PSModulePath")]
        [String]$Target = 'PATH',
        [Parameter(Mandatory=$true)]
        [String]$Path
    )
    $Pathvar = [Environment]::GetEnvironmentVariable($Target, "Machine")
    $NewPathvar = ($Pathvar.Split(";") -ne $Path -join [IO.Path]::PathSeparator).Trim(";")
    $NewPathvar = $NewPathvar + [IO.Path]::PathSeparator + $Path
    [Environment]::SetEnvironmentVariable( $Target, $NewPathvar, "Machine" )
}
```
```powershell
Function Remove-Path {
    Param(
        [Parameter(Mandatory=$false)]
        [ValidateSet("PATH", "PSModulePath")]
        [String]$Target = 'PATH',
        [Parameter(Mandatory=$true)]
        [String]$Path
    )
    $Pathvar = [Environment]::GetEnvironmentVariable($Target, "Machine") 
    $NewPathvar = ($Pathvar.Split(";") -ne $Path -join ";").Trim(";")
    [Environment]::SetEnvironmentVariable( $Target, $NewPathvar, "Machine" )
}
```