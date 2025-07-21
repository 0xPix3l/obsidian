create a protected share:
```bash
mkdir -p /tmp/share
sudo impacket-smbserver share /tmp/share -smb2support -username kali -password kali

```

in powershell or cmd, mount it:
```powershell
             #IP
net use \\10.10.16.39\share /user:kali kali
```
