Kerberos Delegation allows a service (like a web server) to act on behalf of a user and request access to other services (like a file share or SQL server) **as if it were the user** — using Kerberos tickets.
User impersonation is the goal of delegation.

This is typically useful in multi-tier service or applications where
Kerberos Double Hop is required. For example, users authenticates to a
web server (first hop) and web server makes requests to a database
server (second hop).
![[Pasted image 20250709000354.png]]

There are two types of Kerberos Delegation:
1. General/Basic or [[Windows/Active directory/Delegation/Unconstrained Delegation.md|Unconstrained Delegation]] - Allows the first hop
(web server in our example) to request access to any service on any
computer in the domain.
![[Pasted image 20250709022957.png]]


2. Constrained Delegation - Allows the first hop to request access only
to specified services on specified computers. If Kerberos
authentication is not used to authenticate to the first hop, Protocol
Transition is used to transition the request to Kerberos.