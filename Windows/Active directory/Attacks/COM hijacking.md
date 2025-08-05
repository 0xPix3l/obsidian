If we have enough privileges we can COM hijack DLLs
```powershell
reg query HKEY_CLASSES_ROOT\CLSID /f "7-zip" /s
```
This command searches **COM class registrations**. Each `CLSID` key corresponds to a COM object.