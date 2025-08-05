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

---
### HKCC (HKEY_CURRENT_CONFIG)
contain information that are gathered in the runtime, never get stored on the disk

---
