
# Hailo AI on balena Raspberry Pi 5

This project is a demonstration of how to install and use the hailo8 firmware on a raspberry pi 5. It also install and sets up the `hailort_service` which is neccessary for running multi-process inference.

## Usage:
Clone this project and then run the following from the root of the project, where `myFleet` is the name of you fleet on balenaCloud:
```
balena push myFleet
```

## Configuration

In order to make use of Gen 3 PCI, ensure that your device overlay setting on `https://dashboard.balena-cloud.com/devices/<DEVICE_UUID>/config` is set to the following:
```
"vc4-kms-v3d,cma-320","dwc2,dr_mode=host","dwc2,dr_mode=host,pciex1_gen=3"
```

## Testing:

Check that hailo_service is running:
```
root@e72577fb98d2:~# supervisorctl status
hailort_service                  RUNNING   pid 26, uptime 0:03:15
```
Test that multi-process processing works, open a terminal session to hailo-service and run:
```
hailortcli run2 --multi-process-service set-net /root/models/yolov8s-hailo8l.hef set-net /root/models/scrfd_2.5g-hailo8l.hef
```
You should see an output like this
```
root@e72577fb98d2:~/models# hailortcli run2 --multi-process-service set-net /root/models/yolov8s-hailo8l.hef set-net /root/models/scrfd_2.5g-hailo8l.hef
[===================>] 100% 00:00:00
yolov8s:    fps: 21.52
scrfd_2_5g: fps: 23.23
```

## TODO:
[ ] Write Documentation
[x] Get basic /dev/hailo0 device and kernel module working
[x] try socket approach for multiprocess inference
    - ~~try symlink /tmp/socket into a shared volume for other containers to make use of.~~ This didn't work :(
    - It might be possible if we could configure the path of the socket, have asked about this on the hailo forum: https://community.hailo.ai/t/is-it-possible-to-change-the-default-tmp-hailort-uds-sock-location-for-hailort-service/14340 

[ ] Try reduce priviledge of container down to SYS_ADMIN only
[ ] Try install only hailofw/stable,now 4.20.0-1  and hailort/stable,now 4.20.0-1