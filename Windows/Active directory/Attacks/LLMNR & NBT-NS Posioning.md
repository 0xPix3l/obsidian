#lateral_movement 

Link-Local Multicast Name Resolution (`LLMNR`) and `NetBIOS` Name Service (`NBT-NS`) are
Microsoft Windows components ***that serve as alternate methods of host identification that
can be used when DNS fails.*** 

when `LLMNR`/`NBT-NS` are used for name resolution, ANY host on the
network can reply. This is where we come in with `Responder` to poison these requests.

### Quick Example
1. A host attempts to connect to the print server at \\print01.inlanefreight.local, but
accidentally types in \\printer01.inlanefreight.local.
2. The DNS server responds, stating that this host is unknown.
3. The host then broadcasts out to the entire local network asking if anyone knows the
location of \\printer01.inlanefreight.local.
4. The attacker (us with Responder running) responds to the host stating that it is the
\\printer01.inlanefreight.local that the host is looking for.
5. The host believes this reply and sends an ***authentication request to the attacker with a
username and NTLMv2 password hash.***
6. This hash can then be cracked offline or used in an SMB Relay attack if the right
conditions exist.

---

`Responder.conf` config file, typically located in `/usr/share/responder` 

### Remediation
We can disable `LLMNR` in Group Policy by going to: Computer Configuration -->
Administrative Templates --> Network --> DNS Client and enabling "Turn OFF Multicast Name Resolution."![[Pasted image 20250730055300.png]]
