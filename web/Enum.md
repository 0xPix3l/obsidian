directories:
```bash
ffuf -u http://monitorsfour.htb/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -mc 200
```

subdomains:
```bash
ffuf -u http://monitorsfour.htb -H "Host: FUZZ.monitorsfour.htb" -ac -w /usr/share/seclists_custom/big.txt
```