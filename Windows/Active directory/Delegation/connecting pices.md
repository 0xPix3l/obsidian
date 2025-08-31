## 1. Unconstrained Delegation

- **Definition:** A service (e.g., `web01`) can impersonate **any user** to **any service** on the network once the user authenticates to it.
- **How it works:**  
  - The service automatically receives the user’s TGT when the user logs in.  
  - It can request service tickets to any other service using that TGT.
- **Relation to S4U:**  
  - Unconstrained delegation **doesn’t need S4U2Self / S4U2Proxy** because the service already has the user’s TGT.  
  - The service can directly request service tickets to backend services.

---

## 2. Constrained Delegation

- **Definition:** A service can impersonate users **only to specific backend services** defined in AD.
- **How it works:**  
  1. Front-end service (e.g., `web01`) wants to access a backend service (e.g., `sql01`) **on behalf of a user**.  
  2. It uses **S4U2Self** to get a ticket for the user **to itself**.  
  3. Then it uses **S4U2Proxy** to request a ticket to the **allowed backend service**.
- **Key:** Delegation is **restricted to specific SPNs**.
- **Relation to S4U:**  
  - **S4U2Self:** Get user’s identity without their password.  
  - **S4U2Proxy:** Get service ticket to the allowed backend service on behalf of the user.

---

## 3. Resource-Based Constrained Delegation (RBCD)

- **Definition:** A backend service decides which front-end services can delegate to it. Delegation rights are **stored on the resource** instead of the delegating account.
- **How it works:**  
  - Front-end service uses **S4U2Self / S4U2Proxy** like constrained delegation.  
  - KDC checks the **ACL on the target service object** to see if delegation is allowed.
- **Relation to S4U:**  
  - Exactly like constrained delegation, but **the control is on the resource side**.  
  - S4U2Self → S4U2Proxy is still the flow; only the **authorization check** differs.

---

## 🔹 Summary Table

| Delegation Type | S4U2Self Needed? | S4U2Proxy Needed? | Who controls delegation |
|----------------|----------------|-----------------|------------------------|
| Unconstrained  | No             | No              | Domain admin sets it on front-end service |
| Constrained    | Yes            | Yes             | Front-end service object in AD |
| RBCD           | Yes            | Yes             | Resource (backend) service object in AD |

---

### ✅ Key Takeaway

- **S4U2Self:** Always the first step to “pretend to be a user” without their password.  
- **S4U2Proxy:** Always the second step to get a service ticket to the delegated target.  
- **Unconstrained delegation** doesn’t need S4U because the service already gets the user’s TGT.
