Kerberos Delegation allows a service (like a web server) to act on behalf of a user and request access to other services (like a file share or SQL server) **as if it were the user** — using Kerberos tickets.
User impersonation is the goal of delegation.
#### the domain controller will add a copy of the user's TGT to the TGS ticket .

This is typically useful in multi-tier service or applications where
Kerberos Double Hop is required. For example, users authenticates to a
web server (first hop) and web server makes requests to a database
server (second hop).
![[Pasted image 20250709000354.png]]

---

There are 4 types of Kerberos Delegation:
###  General/Basic or [[Windows/Active directory/Delegation/Unconstrained Delegation.md|Unconstrained Delegation]] - Allows the first hop
(web server in our example) to request access to any service on any
computer in the domain.
![[Pasted image 20250709022957.png]]

---


### Constrained Delegation - Allows the first hop to request access only
to specified services on specified computers. If Kerberos
authentication is not used to authenticate to the first hop, Protocol
Transition is used to transition the request to Kerberos.
![[Pasted image 20250901011802.png]]
While for unconstrained delegation a copy of the user's TGT gets sent to the service account, this is not the case for constrained delegation. If the service account, here `WEBSRV` ,wishes to authenticate to a resource ( `SQL/DBSRV` ) on behalf of the user, it must make a special TGS request to the domain controller. Two fields will be modified compared to a classic TGS request.
- The additional tickets field will contain a copy of the TGS ticket or Service Ticket the user sent to the service.
- The `cname-in-addl-tkt` flag will be set to indicate to the Domain Controller that it should not use the server information but the ticket information in additional tickets , i.e., the user's information the server wants to impersonate
---
### Resource-Based Constrained Delegation
Until now, delegation management was done at the level of the service that wanted to impersonate a user to access a resource. Resource-based constrained delegation reverses the responsibilities and shifts delegation management to the final resource. It is no longer at the service level that we list the resources to which we can delegate, but at the resource level, a trust list is established. Any account on this trusted list has the right to delegate authentication to access the resource.
![[Pasted image 20250901013206.png]]
Unlike the other two types of delegation, the resource has the right to modify its own trusted list. Thus, any service account has the right to modify its trusted list to allow one or more accounts to delegate authentication to themselves.
>If a service account adds one or more accounts to its trusted list, it updates its `msDS-AllowedToActOnBehalfOfOtherIdentity `attribute in the directory.

We can add with the following command and the attribute will be updated
![[Pasted image 20250901013614.png]]

---

### `S4U2Proxy` & `S4U2Self`
`S4U2Proxy` ( Service for User to Proxy) and `S4U2Self` ( Service for User to Self) . they are two Active Directory extensions that allow delegation.
#### `S4U2Proxy`
