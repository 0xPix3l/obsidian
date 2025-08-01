#trusts 
# Theory
## SID History Primer
The [sidHistory](https://docs.microsoft.com/en-us/windows/win32/adschema/a-sidhistory) attribute is used in migration scenarios. If a user in one domain is migrated to another domain, a new account is created in the second domain. The original user's SID will be added to the new user's SID history attribute, ensuring that the user can still access resources in the original domain.

> **`SidHistory` Attribiute Contains previous SIDs used for the object if the object was moved from another domain. Whenever an object is moved from one domain to another, a new SID is created and that new SID becomes the objectSID. The previous SID is added to the sIDHistory property.**

