#trusts 
# Theory
## SID History Primer
The [sidHistory](https://docs.microsoft.com/en-us/windows/win32/adschema/a-sidhistory) attribute is used in migration scenarios. If a user in one domain is migrated to another domain, a new account is created in the second domain. The original user's SID will be added to the new user's SID history attribute, ensuring that the user can still access resources in the original domain.

---

we are creating a Golden Ticket from the compromised child domain to compromise the parent domain. In this case, we will leverage the SIDHistory to grant an account (or non-existent account) Enterprise Admin rights by modifying this attribute to contain the SID for the Enterprise Admins group, which will give us full access to the parent domain without actually being part of the group.

To perform this attack after compromising a child domain, we need the following:
- The `KRBTGT` hash for the child domain (DCSync)
- The SID for the child domain (lookupsid.py)
- The name of a target user in the child domain (does not need to exist!)
- The FQDN of the child domain.
- The SID of the Enterprise Admins group of the root domain. (lookupsid.py)

getting Enterprise admins SID from windows
```powershell
Get-DomainGroup -Domain SEVENKINGDOMS.LOCAL -Identity
"Enterprise Admins"

Get-ADGroup  -Server SEVENKINGDOMS.LOCAL  "Enterprise Admins"
```

Then we can use `tickter.py` to make a golden ticket
