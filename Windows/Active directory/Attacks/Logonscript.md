#Persistence 
# Abuse ScriptPath
Throughout engagements, abusing logon scripts via rights on a user's scriptPath attribute depends on whether we can write or read it and other factors.

Windows stores logon scripts in the scripts folder within the `SYSVOL` network share (which, on a domain controller, resides at the physical location `%systemroot%\SYSVOL\sysvol` )
## Write scriptPath
