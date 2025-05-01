TODO:
[ ] Write Documentation
[x] Get basic /dev/hailo0 device and kernel module working
[ ] try socket approach for multiprocess inference
    - try symlink /tmp/socket into a shared volume for other containers to make use of.
    - with a tmpfs volume
[ ] Try reduce priviledge of container down to SYS_ADMIN only
[ ] Try install only hailofw/stable,now 4.20.0-1  and hailort/stable,now 4.20.0-1



## Configuration

In order to make use of Gen 3 PCI, set device overlays to:
```
"vc4-kms-v3d,cma-320","dwc2,dr_mode=host","dwc2,dr_mode=host,pciex1_gen=3"
```

## Testing:

Check that hailo_service is running:
```
root@e72577fb98d2:~# supervisorctl status
hailort_service                  RUNNING   pid 26, uptime 0:03:15
```
Test that multi-process processing works:

```
root@e72577fb98d2:~/models# hailortcli run2 --multi-process-service set-net yolov8s-hailo8l.hef set-net scrfd_2.5g-hailo8l.hef
[===================>] 100% 00:00:00
yolov8s:    fps: 21.52
scrfd_2_5g: fps: 23.23
```