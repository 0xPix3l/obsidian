
all is here [here](https://angelica.gitbook.io/hacktricks/network-services-pentesting/ipsec-ike-vpn-pentesting)

---
# enumeration

```bash
└─$ nmap -sU -p 500 --script ike-version 10.129.246.248
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-21 20:45 EEST
Nmap scan report for 10.129.246.248
Host is up (0.072s latency).

PORT    STATE SERVICE
500/udp open  isakmp
| ike-version:
|   attributes:
|     XAUTH
|_    Dead Peer Detection v1.0

Nmap done: 1 IP address (1 host up) scanned in 0.56 seconds



└─$ ike-scan 10.129.246.248
Starting ike-scan 1.9.6 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
10.129.246.248  Main Mode Handshake returned HDR=(CKY-R=cd46ff9092daf568) SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800) VID=09002689dfd6b712 (XAUTH) VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0)

Ending ike-scan 1.9.6: 1 hosts scanned in 0.069 seconds (14.52 hosts/sec).  1 returned handshake; 0 returned notify
```

# exploitation

## finding a valid transformation
The IPSec configuration can be prepared only to accept one or a few transformations. A transformation is a combination of values. **Each transform** contains a number of attributes like DES or 3DES as the **encryption algorithm**, SHA or MD5 as the **integrity algorithm**, a pre-shared key as the **authentication type**, Diffie-Hellman 1 or 2 as the key **distribution algorithm** and 28800 seconds as the **lifetime**.

Then, the first thing that you have to do is to **find a valid transformation**, so the server will talk to you. To do so, you can use the tool **ike-scan**. By default, Ike-scan works in main mode, and sends a packet to the gateway with an ISAKMP header and a single proposal with **eight transforms inside it**.

> Think of it (valid transformation) as a **secret handshake** with very specific rules. For two people to successfully complete the handshake, they must both agree on every single step in the same order. If one person expects a fist bump and the other offers a high-five, the handshake fails.

