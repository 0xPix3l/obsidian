
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