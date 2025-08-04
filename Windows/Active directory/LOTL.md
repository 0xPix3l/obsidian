#lateral_movement 
## wmic :
```powershell
Host Enumeration:

--- OS Specifics ---
wmic os LIST Full (* To obtain the OS Name, use the "caption" property)

wmic computersystem LIST full

--- Anti-Virus ---

wmic /namespace:\\root\securitycenter2 path antivirusproduct

--- Peripherals ---
wmic path Win32_PnPdevice 

--- Installed Updates ---
wmic qfe list brief

--- Directory Listing and File Search ---

wmic DATAFILE where "path='\\Users\\test\\Documents\\'" GET Name,readable,size

wmic DATAFILE where "drive='C:' AND Name like '%password%'" GET Name,readable,size /VALUE

--- Local User Accounts ---

wmic USERACCOUNT Get Domain,Name,Sid

Domain Enumeration:

--- Domain and DC Info ---

wmic NTDOMAIN GET DomainControllerAddress,DomainName,Roles /VALUE

--- Domain User Info ---

wmic /NAMESPACE:\\root\directory\ldap PATH ds_user where "ds_samaccountname='testAccount'" GET 


--- List All Users ---

wmic /NAMESPACE:\\root\directory\ldap PATH ds_user GET ds_samaccountname

--- List All Groups ---

wmic /NAMESPACE:\\root\directory\ldap PATH ds_group GET ds_samaccountname

--- Members of A Group ---

wmic /NAMESPACE:\\root\directory\ldap PATH ds_group where "ds_samaccountname='Domain Admins'" Get ds_member /Value
wmic path win32_groupuser where (groupcomponent="win32_group.name="domain admins",domain="YOURDOMAINHERE"")

--- List All Computers ---

wmic /NAMESPACE:\\root\directory\ldap PATH ds_computer GET ds_samaccountname

OR

wmic /NAMESPACE:\\root\directory\ldap PATH ds_computer GET ds_dnshostname

Misc:

--- Execute Remote Command ---

wmic process call create "cmd.exe /c calc.exe"

--- Enable Remote Desktop ---

wmic rdtoggle where AllowTSConnections="0" call SetAllowTSConnections "1"

OR

wmic /node:remotehost path Win32_TerminalServiceSetting where AllowTSConnections="0" call SetAllowTSConnections "1"
```

---

### Net commands:
| Command                                         | Description                                                      |
| ----------------------------------------------- | ---------------------------------------------------------------- |
| `net accounts`                                  | Information about password requirements                          |
| `net accounts /domain`                          | Password and lockout policy                                      |
| `net group /domain`                             | Information about domain groups                                  |
| `net group "Domain Admins" /domain`             | List users with domain admin privileges                          |
| `net group "domain computers" /domain`          | List of PCs connected to the domain                              |
| `net group "Domain Controllers" /domain`        | List PC accounts of domain controllers                           |
| `net group <domain_group_name> /domain`         | Users that belong to the specified group                         |
| `net groups /domain`                            | List of domain groups                                            |
| `net localgroup`                                | All available groups                                             |
| `net localgroup administrators /domain`         | List users that belong to the administrators group in the domain |
| `net localgroup Administrators`                 | Information about a group (Admins)                               |
| `net localgroup administrators [username] /add` | Add user to administrators group                                 |
| `net share`                                     | Check current shares                                             |
| `net user <ACCOUNT_NAME> /domain`               | Get information about a user within the domain                   |
| `net user /domain`                              | List all users of the domain                                     |
| `net user %username%`                           | Information about the current user                               |
| `net use x: \\computer\share`                   | Mount the share locally                                          |
| `net view`                                      | Get a list of computers                                          |
| `net view /all /domain[:domainname]`            | Shares on the domains                                            |
| `net view \\computer /ALL`                      | List shares of a computer                                        |
| `net view /domain`                              | List of PCs of the domain                                        |

>NOTE: If you believe the network defenders are actively logging/looking for any commands out of
the normal, you can try this workaround to using net commands. Typing `net1` instead of
net will execute the same functions without the potential trigger from the net string.

---

### Dsquery

```powershell
dsquery user
dsquery group
dsquery computers
dsquery * "CN=Users,DC=LOL,DC=LOCAL" # Wildcard

# search domain controllers
dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -limit 5 -attr sAMAccountName
```
> List of flags of LDAP bits can be [here](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/useraccountcontrol-manipulate-account-properties#list-of-property-flags)

>  search filter syntax are [here](https://learn.microsoft.com/en-us/windows/win32/adsi/search-filter-syntax)