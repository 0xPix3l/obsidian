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

---

## NFS SUID
```bash
# Analysis to find the root file handle
nfs_analyze 127.0.0.1 /srv/web.fries.htb
```

This will output the root file handle needed for mounting.

#### Mounting with fuse_nfs:

```bash
mkdir -p /tmp/mount

fuse_nfs /tmp/mount/ 127.0.0.1 - fake-uid - allow-write - manual-fh [FILE_HANDLE]
```
