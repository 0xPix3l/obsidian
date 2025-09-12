![[Pasted image 20250805183927.png]]
This attack allows an attacker on the network to request a hashed & salted version of **any** computer account NT hash in the domain **without** the need for authentication.

So how to detect if actually timeroasting attack will success?

- our user can RID brute for computer accounts (which we has because he can read `IPC$` share)
- port 123/udp is open for `ntp` service
- `servicePrincipalName` attributes for computers (we did this using LDAPsearch)
- requested TGS (like normal kerberoasting attack but for computers)
- cracked it offline using `hashcat` module `31300`


This process is designed to prevent a client from synchronizing with a malicious time server (by responding with a Message Authentication Code (MAC) in the NTP packet. This MAC is a cryptographic digest derived from the password hash of the computer account that sent the request.)