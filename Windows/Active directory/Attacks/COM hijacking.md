If we have enough privileges we can COM hijack DLLs
```powershell
reg query HKEY_CLASSES_ROOT\CLSID /f "7-zip" /s
```
![[Pasted image 20250805191707.png]]
This command searches **COM class registrations**. Each `CLSID` key corresponds to a COM object.

Then we can enumerate if we have write permissions to it:
```powershell
.\accesschk.exe -k -w -u "rustykey.htb\support" "HKEY_CLASSES_ROOT\CLSID\{23170F69-40C1-278A-1000-000100020000}" /accepteula
```
![[Pasted image 20250805191805.png]]
we have RW

we can also use get-acl:
```powershell
Get-Acl "HKLM:\Software\Classes\CLSID\{23170F69-40C1-278A-1000-000100020000}\InprocServer32" | Select -ExpandProperty Access | Where-Object {$_.IdentityReference -like "*Support*"}


RegistryRights    : FullControl
AccessControlType : Allow
IdentityReference : RUSTYKEY\Support
IsInherited       : True
InheritanceFlags  : ContainerInherit
PropagationFlags  : None




```