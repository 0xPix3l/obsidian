# PowerShell core modules
``` powershell
Load a PowerShell script using dot sourcing
. C:\AD\Tools\PowerView.ps1

A module (or a script) can be imported with:
Import-Module C:\AD\Tools\ADModule-
master\ActiveDirectory\ActiveDirectory.psd1

All the commands in a module can be listed with:
Get-Command -Module <modulename>
```


# Bypassing PowerShell Security

```powershell
Using Invisi-Shell  # Bypass the logging
• With admin privileges: RunWithPathAsAdmin.bat 
• With non-admin privileges: RunWithRegistryNonAdmin.bat 
• Type exit from the new PowerShell session to complete the clean-up.
```

---

### ✅ `Get-DomainGroup`

- **Belongs to**: **PowerView**
    
- **Module**: [`PowerView.ps1`](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1) (part of **PowerSploit**)
### ✅ `Get-NetGroup`

- **Belongs to**: **Old/Legacy PowerView**
    
- **Module**: Legacy `PowerView.ps1` before the `Get-Domain*` convention was adopted.