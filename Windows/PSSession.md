connect to another user using `PSSession`

```powershell
$user = "inlanefreight\svc_sql"  
$Password = ConvertTo-SecureString "lucky7" -AsPlainText -Force  
$credentials = New-Object System.Management.Automation.PSCredential ($user, $Password)

Enter-PSSession -ComputerName "MS01.inlanefreight.local" -Credential $credentials
```