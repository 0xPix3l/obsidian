## NLA

#### What is NLA?
- **Network Level Authentication** requires the **forces the user to authenticate before a full RDP session is created**.
- It is good because it prevents unnecessarily spawning of processes like `Winlogon.exe` or `Csrss.exe` 

NLA relies on `CredSSP` to present the user's credentials to the server before any session is created. SO, with NLA disabled its's possible to establish  and RDP session without **Prior authentication** so we can at least get a screenshot of the computer login screen to get valid usernames.

nxc has module for that 😀

![[Pasted image 20250708200829.png]]
![[Pasted image 20250708200755.png]]


connect to RDP:
```bash
xfreerdp /u:<username> /p:<password> /v:<host> +clipboard
```
