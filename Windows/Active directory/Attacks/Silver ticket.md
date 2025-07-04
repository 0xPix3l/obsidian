we can forge a service ticket if we have:
- plaintext password or NTLM hash of a service account
- Service Principal Name (SPN) of that service
- FQDN
- Domain SID (optional for some tools)






> [!NOTE] Title
> this whole tickets forging thing will be useless IF the service did a PAC validation which is rare. Any service that runs inside services.msc will not use PAC validation. 

Getting Domain SID:
```powershell
ldapsearch:
ldapsearch -x -H ldap://<DC_IP> -b "" -s base objectSid

impacket-GetADUsers:
impacket-GetADUsers domain/user:password@<DC_IP> -all
```