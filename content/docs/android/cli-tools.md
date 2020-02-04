---
Title: CLI Tools
---
### heimdall
```
heimdall detect
heimdall print-pit --verbose
heimdall download-pit --output android.pit
heimdall flash --RECOVERY recovery.img
heimdall flash --BOOT boot.img --verbose
```

### adb
```
echo -n 'mtp,adb' > /data/property/persist.sys.usb.config
adb shell screenrecord --output-format=h264 - | vlc --demux h264 --h264-fps=60 --clock-jitter=0 -
```

- Connect to Android with ADB over TCP
```
setprop service.adb.tcp.port 5555
// setprop service.adb.tcp.port -1
stop adbd
start adbd

adb tcpip 5555
adb connect 192.168.1.42:5555
adb disconnect 192.168.1.42:5555
```
