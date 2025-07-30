Link-Local Multicast Name Resolution (`LLMNR`) and `NetBIOS` Name Service (`NBT-NS`) are
Microsoft Windows components ***that serve as alternate methods of host identification that
can be used when DNS fails.*** 

when `LLMNR`/`NBT-NS` are used for name resolution, ANY host on the
network can reply. This is where we come in with `Responder` to poison these requests.