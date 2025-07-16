scp use ssh to copy files.
YOU HAVE TO HAVE A VALID TGT

from attacker machine:
```bash
scp <USER>@<DOMAIN>:<PATH_TO_FILE> <WHERE_TO_SAVE>

# Example:
scp -v f.frizzle@FRIZZ.HTB:Desktop/IE2XMEG.7z .
```