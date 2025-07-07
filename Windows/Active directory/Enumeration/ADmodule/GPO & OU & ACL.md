```powershell

# Lists domain Group Policy Objects
 Get-GPO -All

# Lists Organizational Units in domain
Get-ADOrganizationalUnit -filter *

# tell u what is the GPO of this OU
Get-ADOrganizationalUnit -Identity "OU=IT,DC=lol,DC=local" -Properties gplink | Select-Object -ExpandProperty gplink

# Tells you which GPOs are linked to the "IT" OU
Get-GPO -Id "{5DD3AD61-5EEA-4990-ADF7-14952DB140FB}"


```

```powershell
# ACLs
get-acl C:\ | Select -ExpandProperty Access
icacls <file_name>
```

### 🔐 Common Permission Abbreviations in `icacls`:

| Code | Meaning                                     |
| ---- | ------------------------------------------- |
| `F`  | Full Control _(includes everything below)_  |
| `M`  | Modify _(Read, Write, Delete, and Execute)_ |
| `RX` | Read and Execute                            |
| `R`  | Read                                        |
| `W`  | Write                                       |
| `D`  | Delete                                      |
| `L`  | List folder contents                        |