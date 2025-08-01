#obstacles
### invoke command
```powershell
$cred = Get-Credential lol\pixel
Invoke-Command -ComputerName normal -Credential $cred -ScriptBlock {
    Invoke-Command -ComputerName normal2 -Credential $Using:cred -ScriptBlock {hostname}
}
```

## PortProxy

Since we have Local Administrator on the intermediate target **bizintel: 10.35.8.17**, you can add a port forwarding rule to send your requests to the final/third server **secdev: 10.35.8.23**.

Can quickly use **netsh** to rip out a one-liner and add the rule.

```
netsh interface portproxy add v4tov4 listenport=5446 listenaddress=10.35.8.17 connectport=5985 connectaddress=10.35.8.23
```
So **bizintel** is listening on port 5446 and will forward requests hitting 5446 off to **secdev** port 5985 (aka WinRM).



all is here: https://posts.slayerlabs.com/double-hop/