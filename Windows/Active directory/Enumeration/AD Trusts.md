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

---
When establishing a trust, certain elements can be modified depending on the business case. 

Trusts can be transitive or non-transitive.



![[Pasted image 20250801034105.png]]