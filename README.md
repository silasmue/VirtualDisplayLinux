# VirtualDisplayLinux
Short Guide on how to use empty ports on the iGPU or GPU to create virtual displays. Credit to https://discuss.kde.org/t/how-to-create-a-virtual-monitor-display/2725/5 vyacheslavl

## Identify video-cards and ports
```
$ ls /sys/class/drm/card*
```
By using cat on different outputs you can see the status and loaded edid if a edid is loaded the following example is for `card1` output `HDMI-A-1`:
```
$ cat /sys/class/drm/card1-HDMI-A-1/status
$ cat /sys/class/drm/card1-HDMI-A-1/edid
```
Checking this is pretty useful while debugging.

# Create EDID folder
Create a folder, if it is not existing in `/usr/lib/firmware/edid`
```
$ sudo mkdir /usr/lib/firmware/edid
```

# Create EDID files
The EDID files can be downloaded or extracted from the monitor(s) in use.
Extract EDID file:
```
$ sudo get-edid > /usr/lib/firmware/custom_edid.bin
```
Or [download/generate](https://github.com/akatrevorjay/edid-generator) a EDID file.

# Edid kernel arguments to load EDID files at boot
On Ubuntu edit `/etc/default/grub`:
```
...
GRUB_CMDLINE_LINUX_DEFAULT="... drm.edid_firmware=HDMI-A-1:edid/1920x1080.bin,HDMI-A-2:edid/2560x1440.bin video=HDMI-A-1:1920x1080@60e video=HDMI-A-2:2560x1440@60e"
...
```
Be aware if you want to add new EDID-files you need to add entries to the comma seperated list `drm.edid_firmware` but to add display settings you need to add another `video` argument. The example above adds two virtual display edid it to add more or only one display. Use the previousely identified outputs to load the EDID and setup the display.

After that (for Ubuntu):
```
$ sudo update-grub
$ sudo reboot
```

For debugging checking `dmesg` might be helpful:
```
$ sudo dmesg
$ sudo dmesg | grep drm
```

