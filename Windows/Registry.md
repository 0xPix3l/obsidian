## What is registry?
It is a hierarchical database that stores and configures settings and options.
- It is designed to be so fast, small in size (15 -> 20 MB)
- It is always loaded into RAM
- It contains two basic elements:
	-  Registry keys: container objects similar to folders
	-  Registry values: non-container objects similar to files

> If we got heat map for windows, Registry would be *RED HOT*
---

Confusing part:

| Component   | Stores...                    | Needed for...                           |
| ----------- | ---------------------------- | --------------------------------------- |
| `ntds.dit`  | Domain users & hashes        | AD credential dumping                   |
| `SAM`       | Local user hashes            | Local hash extraction                   |
| `SYSTEM`    | Boot config, decryption keys | Decrypt SAM/NTDS hashes, LSA secrets    |
| `SECURITY`  | LSA Secrets                  | Cached creds, service accounts, etc.    |
| `lsass.exe` | Runs LSA service in memory   | Can be dumped from memory with mimikatz |

---
## The 5 root keys of registry:

### HKLM (HKEY_LOCAL_MACHINE)
Most critical registry key of all
This contains 7 subkeys:
- SAM
- Security
- SYSTEM (for cached credentials)
- Software (any installed software writes into this)
- Hardware
- Components
- BCD.dat (for boot)


NOTE:
`HKCR` (short for **HKEY_CLASSES_ROOT**) is a **merged view** of:
- `HKLM\Software\Classes`  (machine-wide classes) 
- `HKCU\Software\Classes`  (user-specific overrides)

so for example:

`HKCR\CLSID\{23170F69-40C1-278A-1000-000100020000}\InprocServer32`
is the same as:
`HKLM\Software\Classes\CLSID\{23170F69-40C1-278A-1000-000100020000}\InprocServer32`


### HKCC (HKEY_CURRENT_CONFIG)
contain information that are gathered in the runtime, never get stored on the disk


### HKCR (HKEY_CLASSES_ROOT)
contains info about registered applications


### HKCU (HKEY_CURRENT_USER)
Stores settings that are specific to the current user

### HKU (HKEY_USERS)
contains subkeys corresponding to HKEY_CURRENT_USER for each profile