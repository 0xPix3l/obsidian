| **Group**                  | **Description**                                                                                                                                                      |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Administrators**        | Domain Admins and Enterprise Admins – "super" groups.                                                                                                               |
| **Server Operators**      | Can modify services, access SMB shares, and back up files.                                                                                                          |
| **Backup Operators**      | Can log onto DCs locally. Considered equivalent to Domain Admins. Can make shadow copies of SAM/NTDS, read registry remotely, and access file system via SMB.     |
| **Print Operators**       | Can log onto DCs locally and exploit driver loading to execute malicious code.                                                                                      |
| **Hyper-V Administrators**| If DCs are virtualized, members can be considered Domain Admins due to control over virtual machines.                                                              |
| **Account Operators**     | Can modify non-protected user and group objects in the domain.                                                                                                      |
| **Remote Desktop Users**  | No special privileges by default, but often granted RDP access and lateral movement capabilities.                                                                  |
| **Remote Management Users**| Can log into DCs using PowerShell Remoting (PSRemoting); sometimes added to the local remote management group on non-DCs.                                         |
| **Group Policy Creator Owners** | Can create new Group Policy Objects (GPOs), but require delegated rights to link them to domains or OUs.                                                  |
| **Schema Admins**         | Can modify the AD schema and inject backdoors into new objects by manipulating default ACLs.                                                                        |
| **DNS Admins**            | Can load malicious DLLs into DNS service (though unreliable). Better exploited by creating a WPAD record for credential capture or persistence.                    |

>  DC only allows rc4 encryption type alogrithm, but members of protected users can only login with aes 256 or 128, without removing the group you would always receive enc nosupp error (like when trying to update its password `aes` keys for the user won't update and since user in protected users) so, according to [Microsoft](https://learn.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/protected-users-security-group) protects against unconstrained delegation

Protected User accounts that authenticate to a domain running Windows Server are unable to do the following:
- Authenticate with NTLM authentication.
- Use DES or RC4 encryption types in Kerberos preauthentication.
- Delegate with unconstrained or constrained delegation.    
- Renew Kerberos TGTs beyond their initial four-hour lifetime.










