
```powershell
powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck -Extended -Report PrivescCheck_$($env:COMPUTERNAME) -Format TXT,HTML"
```
[commands here](https://github.com/itm4n/PrivescCheck?tab=readme-ov-file)
