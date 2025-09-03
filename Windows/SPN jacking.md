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
The first scenario is the simplest one. ServerA is configured for Constrained Delegation to a SPN previously associated with a computer or service account that no longer exists. It could be a standard SPN, such as `cifs/hostname`, associated with a deleted computer/service account or a renamed computed account or the account itself could no longer exist.

In this scenario, the attacker can add the affected SPN to `ServerC` and then run the full S4U attack using `ServerA`’s account to obtain a service ticket for a privileged user to `ServerC`.

The service name of that ticket would not be valid for accessing `ServerC` because the hostname wouldn’t match. However, the important thing is that the ticket is encrypted for `ServiceC`, and the service name is not in the encrypted part of the ticket, so the attacker can change it to a valid one.

the attack works like this:
![[Pasted image 20250903045521.png]]