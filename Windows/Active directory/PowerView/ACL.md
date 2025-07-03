

```powershell

# Get ACEs on a certain user: (ITguy)
Get-DomainObjectAcl -Identity "ITguy" -ResolveGUIDs | ForEach-Object { $name = try { ([System.Security.Principal.SecurityIdentifier]$_.SecurityIdentifier).Translate([System.Security.Principal.NTAccount]) } catch { $_.SecurityIdentifier }; [pscustomobject]@{ Identity = $name; Rights = $_.ActiveDirectoryRights; Object = $_.ObjectDN } } | Format-Table -AutoSize
```


![[ACL_Mindmap .png]]
![[DACL.png]]
