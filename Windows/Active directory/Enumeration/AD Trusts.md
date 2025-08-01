A trust is used to establish forest-forest or domain-domain (intra-domain) authentication,
which allows users to access resources in (or perform administrative tasks) another domain,
outside of the main domain where their account resides.
An organization can create various types of trusts:

- Parent-child : Two or more domains within the same forest. The child domain has a
two-way transitive trust with the parent domain, meaning that users in the child domain
`corp.inlanefreight.local` could authenticate into the parent domain
`inlanefreight.local` , and vice-versa.
- Cross-link : A trust between child domains to speed up authentication.
- External : A non-transitive trust between two separate domains in separate forests
which are not already joined by a forest trust. This type of trust utilizes SID filtering or
filters out authentication requests (by SID) not from the trusted domain.
- Forest : A transitive trust between two forest root domains.
- Tree-root : A two-way transitive trust between a forest root domain and a new tree
root domain.

Below is a graphical representation of the various trust types.
![[Pasted image 20250801040333.png]]

---
When establishing a trust, certain elements can be modified depending on the business case. 

Trusts can be transitive or non-transitive.

- A transitive trust means that trust is extended to objects that the child domain trusts.
	For example, let's say we have three domains. In a transitive relationship, if Domain A has a trust with Domain B , and Domain B has a transitive trust with Domain C ,then Domain A will automatically trust Domain C .
- In a non-transitive trust , the child domain itself is the only one trusted.

![[Pasted image 20250801034105.png]]
> *An easy comparison to make can be package delivery to your house. For a transitive trust, you have extended the permission to anyone in your household (forest) to accept a package on your behalf. For a non-transitive trust, you have given strict orders with the package that no one other than the delivery service and you can handle the package, and only you can sign for it.*

| Trust Type     | Transitive                                             | Non-Transitive                           |
| -------------- | ------------------------------------------------------ | ---------------------------------------- |
| Shared Trust   | One to many                                            | Direct trust                             |
| Trust Scope    | The trust is shared with anyone in the forest          | Not extended to next level child domains |
| Trust Examples | Forest, tree-root, parent-child, and cross-link trusts | Typical for external trusts              |

---
Trusts can be set up in two directions: one-way or two-way (bidirectional):
- One-way trust : Users in a trusted domain can access resources in a trusting
domain, not vice-versa.
- Bidirectional trust : Users from both trusting domains can access resources in the other domain. For example, in a bidirectional trust between `INLANEFREIGHT.LOCAL` and `FREIGHTLOGISTICS.LOCAL` , users in `INLANEFREIGHT.LOCAL` would be able to access resources in `FREIGHTLOGISTICS.LOCAL` , and vice-versa.

---
## Enumeration

### using Powershell 

```bash
PS C:\htb> Import-Module activedirectory
PS C:\htb> Get-ADTrust -Filter *
```
