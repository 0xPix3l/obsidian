
```powershell
Get-WmiObject Win32_Service -Filter "Name='WayneService'" | Select-Object Name, StartName

```
