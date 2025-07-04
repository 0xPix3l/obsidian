```powershell
rpcclient -U Administrator%'P@ssw0rd' 10.10.111.10
rpcclient -U "" 10.211.11.10 -N

-U: Used to specify the username, in our case, we are using an empty string for anonymous login.

-N: Tells RPC not to prompt us for a password.
```
