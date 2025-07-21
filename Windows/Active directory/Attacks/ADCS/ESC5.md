# ESC5: Vulnerable PKI Object Access Control.

### Overview:
ESC5 refers to privilege escalation vulnerabilities stemming from improperly configured ACLs on various PKI-related objects within Active Directory.

>This category is distinct from ESC4 (which focuses on ACLs of individual certificate _template_ objects) and ESC7 (which focuses on permissions directly on the CA _object_ or its services).

### Attack Vectors:

1. **CA Server Object**: `WriteProperty` permission allows configuration changes
2. **Certificate Templates Container**: `GenericWrite` allows template creation
3. **Individual CA Objects**: Various dangerous permissions

### Detection:
`certipy` can't directly detect it as it has  