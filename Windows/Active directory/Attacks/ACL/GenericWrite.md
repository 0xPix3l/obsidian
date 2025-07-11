adding user to a group:
```bash
bloodyAD --host puppy.htb -u levi.james -p 'KingofAkron2025!' add groupMember DEVELOPERS levi.james
```

check user in group:

```bash
ldapsearch -x -H ldap://10.10.11.72 \
  -D 'alfred@tombwatcher.htb' -w 'basketball' \
  -b "CN=Infrastructure,CN=Users,DC=tombwatcher,DC=htb" \
  member

```