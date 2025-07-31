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
https://github.com/Ridter/noPac