```bash
# getting all ACEs over pixel
python3 pywerview.py get-objectacl --name 'pixel' -w lol.local -t 10.10.111.10 -u 'administrator' -p 'P@ssw0rd' --resolve-sids --resolve-guids




# getting ACEs for a specific user
python3 pywerview.py get-objectacl --name 'pixel' -w lol.local -t 10.10.111.10 -u 'administrator' -p 'P@ssw0rd' --resolve-sids --resolve-guids --json | jq '.results | map(select(.securityidentifier | contains("Administrator")))'
```