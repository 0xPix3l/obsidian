#evasion 
An application whitelist is a list of approved software applications or executables that are
allowed to be present and run on a system.

Organizations also often focus on blocking the `PowerShell.exe` executable, but forget
about the other PowerShell executable locations such as
`%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe` or
`PowerShell_ISE.exe` .

---
### enum AppLocker
```powershell
PS C:\htb> Get-AppLockerPolicy -Effective | select -ExpandProperty
RuleCollections
```