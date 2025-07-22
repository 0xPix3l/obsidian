# ESC5: Vulnerable PKI Object Access Control. (Golden Certificate)

### Overview:
ESC5 refers to privilege escalation vulnerabilities stemming from improperly configured ACLs on various PKI-related objects within Active Directory.

>This category is distinct from ESC4 (which focuses on ACLs of individual certificate _template_ objects) and ESC7 (which focuses on permissions directly on the CA _object_ or its services).

### Attack Vectors:

1. **CA Server Object**: `WriteProperty` permission allows configuration changes
2. **Certificate Templates Container**: `GenericWrite` allows template creation
3. **Individual CA Objects**: Various dangerous permissions

### Detection:
`certipy` can't directly detect it, you have to get the private key of the CA that signs all the certificate in order to make it.

you can enumerate it using `certutil` which **lists all certificates** in the **"My" certificate store** for the current user or machine.
```bash
certutil -store My
```

typically it looks like this:
```powershell
================ Certificate 2 ================
Serial Number: 75b2f4bbf31f108945147b466131bdca
Issuer: CN=Certificate-LTD-CA, DC=certificate, DC=htb
NotBefore: 11/3/2024 3:55 PM
NotAfter: 11/3/2034 4:05 PM
Subject: CN=Certificate-LTD-CA, DC=certificate, DC=htb
Certificate Template Name (Certificate Type): CA
CA Version: V0.0         # Big indicator that is a root CA
Signature matches Public Key
Root Certificate: Subject matches Issuer
Template: CA, Root Certification Authority
Cert Hash(sha1): 2f02901dcff083ed3dbb6cb0a15bbfee6002b1a8
Key Container = Certificate-LTD-CA
Unique container name: 26b68cbdfcd6f5e467996e3f3810f3ca_7989b711-2e3f-4107-9aae-fb8df2e3b958
Provider = Microsoft Software Key Storage Provider
Signature test passed
CertUtil: -store command completed successfully.

```
and it has:
- `Root Certificate: Subject matches Issuer ✅`: confirms it's a **Root CA**
- `Key Usage: Cert Sign, CRL Sign ✅`:This is required to **sign other certs** (i.e., forge TGTs)
- `Certificate Template Name: CA ✅`
- `Provider = Microsoft Software Key Storage Provider` This means **exportable key** is available 
- `Key Container = Certificate-LTD-CA` means the **private key is present and accessible**

> if you see `CA Version: V0.0` it high likely a root CA


***
### How the CA Key Is Stolen:
