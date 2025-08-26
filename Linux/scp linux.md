### from the attacker to victim
```bash
scp /path/to/local/file username@victim_ip:/path/to/remote/destination


#example
scp exploit.sh bob@10.10.10.20:/tmp/
```


### victim to the attacker
```bash
scp username@victim_ip:/path/to/file /path/to/local/destination


#example:
scp bob@10.10.10.20:/etc/passwd ~/loot/
```