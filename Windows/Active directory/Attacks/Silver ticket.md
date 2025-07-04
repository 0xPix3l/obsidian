we can forge a service ticket if we have:
- plaintext password or NTLM hash of a service account
- Service Principal Name (SPN) of that service
- FQDN
- Domain SID (optional for some tools)






> [!NOTE] Title
> this whole tickets forging thing will be useless IF the service did a PAC validation which is rare. Any service that runs inside services.msc will not use PAC validation. 
