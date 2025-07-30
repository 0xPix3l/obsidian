 **gMSA (Group Managed Service Account)** is a **special type of Active Directory account** designed for **running services securely** on domain-joined machines — **without needing to manually manage passwords**.

---

## 🔐 Why gMSAs Exist

Traditionally, Windows services (e.g., SQL Server, IIS app pools, scheduled tasks) are often run as:

- A domain user account
    
- With a manually set password
    
- That never expires (bad security)
    
- Shared across multiple systems (worse security)
    

👉 **gMSAs solve all of that.**

| Feature                               | Explanation                                                                                       |
| ------------------------------------- | ------------------------------------------------------------------------------------------------- |
| 🛡️ **Automatic password management** | AD auto-generates and rotates the password every 30 days by default. No human ever sees it.       |
| 🔐 **High security**                  | Long, complex passwords stored in LSASS — resistant to offline brute force.                       |
| 👥 **Group-based access**             | You can allow a **group of machines** to use the gMSA — not just one.                             |
| 🎯 **Service identity**               | Perfect for assigning **unique identity** to a service — for delegation, auditing, Kerberos, etc. |
| 📈 **Scales well**                    | No password vaults or manual rotation needed across large environments.                           |