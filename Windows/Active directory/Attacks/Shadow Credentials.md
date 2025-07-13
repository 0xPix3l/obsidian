Prerequisites
1. The target Domain Functional Level must be **Windows Server 2016** or above.
2. The target domain must have at least one Domain Controller running Windows Server 2016 or above.
3. The Domain Controller to use during the attack must have its own certificate and keys (this means either the organization must have AD CS, or a PKI, a CA or something alike).
4. The attacker must have control over an account able to write the `msDs-KeyCredentialLink` attribute of the target user or computer account.

---

basically it allow us to modify the `msDS-KeyCredentialLink` to add shadow credentials
```bash
pywhisker.py -d "<DOMAIN" -u "<USER>" -p "<PASSWORD>" --target "winrm_svc" --action "add"

```
