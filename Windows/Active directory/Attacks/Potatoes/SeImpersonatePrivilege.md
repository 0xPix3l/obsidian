
for this we can use `printspoofer` or `godpotato`
The tool leverages Windows' **SeImpersonatePrivilege** / impersonation behavior exposed by the Print Spooler to cause a privileged service to hand over a token the attacker can use to create a process as SYSTEM.


user has to have SeImpersonatePrivilege enabled
```powershell
PrintSpoofer64.exe -i -c cmd
```
and we are local nt\system
