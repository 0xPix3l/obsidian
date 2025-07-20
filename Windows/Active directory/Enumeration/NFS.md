list available shares:

```bash
showmount -e $IP
```

mount nfs:
```bash
sudo mkdir /mnt/nfs
sudo mount -t nfs $IP:/$SHARE_NAME /mnt/nfs
```

`-t` : for target's type which in this case `NFS`


we can force a specific version on our side in case the client forces a certain dialect:
```bash
sudo mount -t nfs -o vers=3 $IP:/$SHARE /mnt/nfs

```

Unmount:
```bash
sudo umount /mnt/nfs

# -l for force
```
