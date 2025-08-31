#creds

![[Pasted image 20250724094841.png]]

>This attack involves requesting a Kerberos service ticket(s) (TGS) for the Service Principal Name (SPN) of the target service account (Step #3 above). This request uses a valid domain user’s authentication ticket (TGT) to request one or several service tickets for a target service running on a server.

>The Domain Controller looks up the SPN in Active Directory and encrypts the ticket using the **service account password** associated with the SPN in order for the service to validate user access. 

***

So any domain user can request (TGS) for services registered with an SPN.

Requirements:
- any valid domain joined user creds
```shell
impacket-GetUserSPNs <domain>/<user>:<password> -dc-ip <DC_IP> -request
```


we can also [[Kerberoasting via AS-REP Roasting]]

---
from windows we can get the hash of a service with `PowerView`
```powershell
Get-DomainUser -Identity svc_sql | Get-DomainSPNTicket -Format Hashcat # get user hash (kerberoast)
```