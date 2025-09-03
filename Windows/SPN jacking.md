Its combination of two privileges:
1. unconstrained delegation 
2. write SPN 

ALL IS HERE!!! [ref](https://www.semperis.com/blog/spn-jacking-an-edge-case-in-writespn-abuse/)


This is normal Constrained delegation:
![[Pasted image 20250903021754.png]]
SPN Jacking allowing us to access more that `SQL\DBSRV`. We can access `CIFS\FILESRV` or `HOST\EXCHSRV`.

---
### Scenario

![[Pasted image 20250903043051.png]]
In a case like this we have `NotAdmin` is admin to `ServerA` and have `WriteSPN` on `ServerC (the target)` so what we can do is we can remove `SQL/DeletedAccount` (the ghost SPN) from `ServerA` and configuring it into the target machine.  For example, if we want to compromise `FILESRV` and we have `WriteSPN` or similar rights, we can add the SPN `SQL/DBSRV` to `FILESRV` . After that, if we request a TGS for `SQL/DBSRV` , it will be encrypted for `FILESRV` , allowing us to impersonate any user into the target machine


