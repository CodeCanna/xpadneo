## Debugging
### Enable Debug Output
If you are asked to send debug info or want to fix bugs, enable debugging
first in the driver and upon request send the xpadneo related part:

```
echo 3 | sudo tee /sys/module/hid_xpadneo/parameters/debug_level
dmesg | grep xpadneo > ~/xpadneo_log
```

where `3` is the most verbose debug level. Disable debugging by setting the
value back to `0`.

You may want to set the debug level at load time of the driver. You can do
this by applying the setting to modprobe:

```
echo "options hid_xpadneo debug_level=3" | sudo tee /etc/modprobe.d/xpadneo.conf
```

Now, the driver will be initialized with debug level 3 during modprobe.

Useful information can now be aquired with the commands:

* `dmesg`: I advise you to run `dmesg -wH` in a terminal while you connect your controller from a second terminal to get hardware information in realtime.
* `modinfo hid_xpadneo`: get information on xpadneo as a kernel module.
* When your gamepad is connected, run
  ```console
  sudo find "/sys/kernel/debug/hid/" -name "0005:045E:*" -exec sh -c 'echo "{}" && head -1 "{}/rdesc" | tee /dev/tty | cksum && echo' \;
  ```
  to get the rdesc identifier of your gamepad.

### Generated Events
If you are asked to supply the events generated by xpadneo, please run the following command

```
perl -0777 -l -ne 'print "/dev/input/$1\n" if /Name="Xbox Wireless Controller".*Handlers.*(event[0-9]+)/s' /proc/bus/input/devices | xargs evtest
```

Do whatever you think does not behave correctly (e.g. move the sticks from left to right if you think the range is wrong)
and upload the output.

### HID device descriptor (including checksum)

If we ask you to supply the device descriptor, please post the output of the following command

```
sudo find "/sys/kernel/debug/hid/" -name "0005:045E:*" -exec sh -c 'echo "{}" && head -1 "{}/rdesc" | tee /dev/tty | cksum && echo' \;
```
