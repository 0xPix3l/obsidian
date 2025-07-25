If Point and Print is misconfigured, any domain user can abuse the `RpcAddPrinterDriver` API to install a malicious driver the system. Since the Print Spooler runs as `SYSTEM`, the payload executes with `SYSTEM` privileges, giving local privilege escalation. And value of `0` means non-admin users can install drivers. It’s the old classic **PrintNightmare** exploit

There are 2 CVEs for this vulnerability:

- `CVE-2021–1675` which goal is RCE
- `CVE-2021–34527` for PrivEsc

---
### CVE-2021–34527 - PrivEsc

script [here](https://github.com/JohnHammond/CVE-2021-34527) if you have RDP

---
