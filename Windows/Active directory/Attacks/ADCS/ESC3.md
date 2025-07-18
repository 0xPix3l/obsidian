# ESC3: Enrollment Agent Certificate Template


Prerequistes:
- For the agent-issuing template (e.g., `EnrollAgent`):
        - `[!] Vulnerabilities ESC3 : Template has Certificate Request Agent EKU set.`
        - `Enrollment Agent : True`
        - `Extended Key Usage` contains `Certificate Request Agent`.
        - `[+] User Enrollable Principals` lists a group the attacker belongs to (e.g., `CORP.LOCAL\Domain Users`), granting them rights to obtain an agent certificate.
- For the target template (e.g., `User`):
        - `[*] Remarks ESC3 Target Template : ... Template has schema version 1.` Schema Version 1 templates are common targets because they typically allow agent-based enrollment without additional restrictions on the agent's certificate.
        - `Client Authentication : True` (or similar authentication EKU).
        - `[+] User Enrollable Principals` for the target template should ideally show that the _target victim_ (e.g., Administrator or Domain Admins group) has enrollment rights.

