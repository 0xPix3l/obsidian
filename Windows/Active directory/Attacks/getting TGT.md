with ntpdate
```bash
faketime "$(ntpdate -q 10.10.11.76 | cut -d ' ' -f 1,2)" impacket-getTGT VOLEUR.HTB/ryan.naylor:'HollowOct31Nyt'
```

with kinit
```bash
kinit svc_winrm@VOLEUR.HTB # DOMAIN SHOULD BE CAPS
```

```bash
impacket-getTGT -dc-ip 10.10.11.75 RUSTYKEY.HTB/rr.parker:'8#t5HE8L!W3A'
```
