---
sticker: emoji//1f5d2-fe0f
---
**On a Domain Controller, Local Admin = Domain Admin**, because **DCs don't have a separate local SAM database** — all privileges are managed by AD.

Only hashes from `ntds.dit` can be used in the PTH attack (can be aquired from DCSync or volume shadow copy from the DC). Any other NTLM has is net-NTLM that is created on the fly (challenge-response) and it can be used for relaying

.NET tools like [SharpCollection](https://github.com/Flangvik/SharpCollection) is so easy to be detected by ADR,AV so in a red team engagement we need to change all the variables so it didn't get detected by them 
