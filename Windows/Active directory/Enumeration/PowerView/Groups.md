```powershell
# What groups user administrator is member of
Get-DomainUser -Identity administrator | Select-Object -ExpandProperty memberof

# Get users in a administrators group
Get-DomainGroup -Identity "administrators" | Select-Object -ExpandProperty member

# list domain admins
Get-DomainGroup -Identity "Domain Admins" | Select-Object -ExpandProperty member

```
![[Pasted image 20250703214353.png]]


