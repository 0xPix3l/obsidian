

## ACL

In their simplest form, ACLs are lists that define:
- who has access to which asset/resource
- the level of access they are provisioned.

The settings themselves in an `ACL` are called Access Control Entries ( `ACEs` ).
Each ACE maps back to a user, group, or process (also known as security principals) and defines the rights granted to that principal.
Every object has an `ACL`, but can have multiple `ACEs` because multiple security principals can access objects in AD. ACLs can also be used for auditing access within AD.


There are two types of ACLs:
1. Discretionary Access Control List ( `DACL` ): defines which security principals are granted or denied access to an object. `DACLs` are made up of `ACEs` that either allow or deny access. **If a DACL does not exist for an object, all who attempt to access the object are granted full rights. If a DACL exists, but does not have any ACE entries specifying specific security settings, the system will deny access to all users, groups, or processes attempting to access it.**
2. System Access Control Lists ( `SACL` ) - allow administrators to log access attempts
made to secured objects. --auditing tab--

![[Pasted image 20250731003757.png]]


## ACEs

Each ACE is made up of the following four components:
1. The security identifier (SID) of the user/group that has access to the object (or principal
name graphically)
2. A flag denoting the type of ACE (access denied, allowed, or system audit ACE)
3. A set of flags that specify whether or not child containers/objects can inherit the given ACE entry from the primary or parent object
4. An access mask which is a 32-bit value that defines the rights granted to an object
![[Pasted image 20250731004101.png]]

---










![[Pasted image 20250722064032.png]]