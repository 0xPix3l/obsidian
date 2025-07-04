```powershell
Enumerate AD group membership

Get-ADPrincipalGroupMembership -Identity studentuser37 | Select Name
# tell u ur group
---------------
Get-ADPrincipalGroupMembership -Identity Managers  
# Shows what groups "Managers" is a member of
---------------------------------
Get-ADGroupMember -Identity "Managers"
# Shows what member in "Managers" 
------------------------------------------

Get-ADPrincipalGroupMembership -Identity ITguy | Where-Object { $_.Name -like "*admin*" }
# tell u if any admin in ur group
```
