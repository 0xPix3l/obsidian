```powershell
Get-DomainGPO
Get-DomainOU

# Tells you which GPOs are linked to the "IT" OU
(Get-DomainOU -Identity "IT").gplink

# tells you what is the GPO of this OU
Get-DomainGPO -identity "{6AC1786C-016F-11D2-945F-00C04fB984F9}" 
```

GPO abuse tools
SharpGPOabuse and pyGPOabuse
- pyGPOabuse
- GPOwned
- New-GPOImmediateTask
- sharpgpoabuse