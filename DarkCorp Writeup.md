1. CVE and explain `cve and explain /?_task=mail&_framed=1&_uid=2&_mbox=INBOX&_framed=1&_action=headers ` (1)

2. got email with username (2)

3. running exploit against the target and add to `/etc/hosts`  the new subdomain (3)

4. getting access denied (4)

5. back to login we see reset password function and putting our target again (5)

6. back to stealing emails (6)

7. logged into the dashboard (7)

8. more usernames (8)


9. tried `'` just out of curiosity if there is a SQL injection and KABOOM into my face (9) 


10. Enumerated the version to see if it supports `pg_execute_server_program` according to https://www.offsec.com/blog/postgresql-exploit/ (10)

11. used `'';SELECT usesuper FROM pg_user WHERE usename = current_user;` to see if our user can perform commands like ``pg_execute_server_program`` and it is true (11)

12.   `pg_execute_server_program` didnt work so I tried another function


