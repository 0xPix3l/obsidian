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

**Step 1: Request the "Any Purpose" certificate for the attacker.** The attacker (`normal@lol.local`) requests a certificate from the `AnyPurposeCert` template.
```bash
    certipy req \
    -u 'normal@lol.local' -p 'normal' \
    -dc-ip '10.10.111.10' -target 'DC01.LOL.LOCAL' \
    -ca 'LOL-CA' -template 'ESC2'
```

This command requests a certificate for the `normal` user from the specified template. The resulting `.pfx` file (`normal.pfx` in this case) contains the certificate that has the "Any Purpose" EKU.

