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

command for low latency:
```bash
xfreerdp /u:Helen /p:'RedRiot88' /d:inlanefreight.local /v:10.129.229.244 /dynamic-resolution /drive:.,linux /bpp:8 /compression -themes -wallpaper /clipboard /audio-mode:0 /auto-reconnect -glyph-cache
```
/bpp:8 : Reduces the color depth to 8 bits per pixel, decreasing the amount of data
transmitted.
/compression : Enables compression to reduce the amount of data sent over the
network.
-themes : Disables desktop themes to reduce graphical data.
-wallpaper : Disables the desktop wallpaper to further reduce graphical data.
/clipboard : Enables clipboard sharing between the local and remote machines.
/audio-mode:0 : Disables audio redirection to save bandwidth.
/auto-reconnect : Automatically reconnects if the connection drops, improving
session stability.
-glyph-cache : Enables caching of glyphs (text characters) to reduce the amount of
data sent for text rendering.