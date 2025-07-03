

```powershell
=>  List AD users
1- Get-ADUser -filter * | select SamAccountName 
net user

=>  List AD computers
2- Get-ADComputer -filter * | select -ExcludeProperty Name

=> List Domain Admins members
3- Get-ADGroupMember -Identity 'Domain Admins'

=> List Enterprise Admins
4- Get-ADGroupMember -Identity 'Enterprise Admins' -Server techcorp.local
```
