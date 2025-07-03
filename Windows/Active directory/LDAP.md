
### Anonymous Bind
```shell
ldapsearch -x -H ldap://10.10.111.10 -b "dc=lol,dc=local"

-x: Use simple (anonymous) authentication
-H: URL of the LDAP server
-b: Base DN (Distinguished Name) to start the search from
```

### Authenticated Bind
```shell
ldapsearch -x -D "CN=pixel,CN=Users,DC=lol,DC=local" -w 'face' -H ldap://10.10.111.10 -b "DC=lol,DC=local"

-D: Bind DN
-w: Password
```

### search for all users
```shell
ldapsearch -x -H ldap://10.10.111.10 -b "dc=lol,dc=local" "(objectClass=user)" sAMAccountName
```

### specified user
```shell
ldapsearch -x -H ldap://10.10.111.10 -b "dc=lol,dc=local" "(sAMAccountName=pixel)"
```

### all groups
```shell
ldapsearch -x -H ldap://10.10.111.10 -b "dc=lol,dc=local" "(objectClass=group)" cn
```

### all computers
```shell
ldapsearch -x -H ldap://10.10.111.10 -b "dc=lol,dc=local" "(objectClass=computer)" name
```
