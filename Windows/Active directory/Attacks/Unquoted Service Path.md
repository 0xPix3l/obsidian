---
tags:
  - PrivEsc
---

## getting service with Unquoted Service Path

### 1- using cmd:

```cmd
wmic service get name,pathname,displayname,startmode | findstr /i "auto" | findstr /i /v "C:\Windows\\" | findstr /i /v '""'
```

### 2 - `privesc.ps1`

```powershell
Get-UnquotedService -verbose
```


## Idea:
When a **service** is created whose **executable path** contains **_spaces_** and isn’t enclosed within **_quotes_**, leads to a vulnerability known as Unquoted Service Path which allows a user to gain **SYSTEM** privileges (only if the vulnerable service is running with SYSTEM privilege level which most of the time it is).

In Windows, if the service is not enclosed within quotes and is having spaces, it would handle the space as a break and pass the rest of the service path as an argument.

For example, consider we have the following executable path.

`C:\Program Files\A Subfolder\B Subfolder\C Subfolder\SomeExecutable.exe`

In order to run **SomeExecutable.exe**, the system will interpret this path in the following order from 1 to 5.

1. C:\Program.exe
2. C:\Program Files\A.exe
3. C:\Program Files\A Subfolder\B.exe
4. C:\Program Files\A Subfolder\B Subfolder\C.exe
5. C:\Program Files\A Subfolder\B Subfolder\C Subfolder\SomeExecutable.exe

If **C:\Program.exe** is not found, then **C:\Program Files\A.exe** would be executed. If **C:\Program Files\A.exe** is not found, then **C:\Program Files\A Subfolder\B.exe** would be executed and so on.
[more info](https://medium.com/@SumitVerma101/windows-privilege-escalation-part-1-unquoted-service-path-c7a011a8d8ae)
