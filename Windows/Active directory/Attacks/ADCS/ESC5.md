# ESC5: Vulnerable PKI Object Access Control.

### Overview:
ESC5 refers to privilege escalation vulnerabilities stemming from improperly configured ACLs on various PKI-related objects within Active Directory.

>This category is distinct from ESC4 (which focuses on ACLs of individual certificate _template_ objects) and ESC7 (which focuses on permissions directly on the CA _object_ or its services).

The critical PKI objects involved in ESC5 are typically located in the Configuration Naming Context of Active Directory (e.g., under `CN=Public Key Services,CN=Services,CN=Configuration,DC=...`) and play crucial roles in the PKI's overall operation and trust model.