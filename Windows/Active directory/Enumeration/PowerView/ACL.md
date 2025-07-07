

```powershell

# Get ACEs on a certain user: (ITguy)
Get-DomainObjectAcl -Identity "ITguy" -ResolveGUIDs | ForEach-Object { $name = try { ([System.Security.Principal.SecurityIdentifier]$_.SecurityIdentifier).Translate([System.Security.Principal.NTAccount]) } catch { $_.SecurityIdentifier }; [pscustomobject]@{ Identity = $name; Rights = $_.ActiveDirectoryRights; Object = $_.ObjectDN } } | Format-Table -AutoSize
```


![[ACL_Mindmap .png]]
![[DACL.png]]


### 🔐 Common Permission Abbreviations in `icacls`:

|Code|Meaning|
|---|---|
|`F`|Full Control _(includes everything below)_|
|`M`|Modify _(Read, Write, Delete, and Execute)_|
|`RX`|Read and Execute|
|`R`|Read|
|`W`|Write|
|`D`|Delete|
|`L`|List folder contents|
