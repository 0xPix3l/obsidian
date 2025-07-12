with ntpdate
```bash
faketime "$(ntpdate -q 10.10.11.76 | cut -d ' ' -f 1,2)" impacket-getTGT VOLEUR.HTB/ryan.naylor:'HollowOct31Nyt'
```

with kinit
```bash
kinit svc_winrm@VOLEUR.HTB # DOMAIN SHOULD BE CAPS
```
