eunm:
```bash
sudo nmap -sCV --script "rtsp-*" -p 554 10.0.52.5
```

```bash
ffmpeg -rtsp_transport tcp -i rtsp://10.0.52.5/mpeg4 -c copy test.mp4
```

