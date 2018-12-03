# Host Communications

## Mac OS

KeepKey should work out-of-the-box on Mac OS.

## Windows

On Windows 7 and newer, the operating system uses Microsoft OS Descriptors to
enumerate USB endpoints on devices.  These descriptors are cached in the
registry, and looked up via the device's vendor & product Id's.  In order to
smoothly transition from the HID driver to the WebUSB driver, we've opted to
increment the reported productId of the device. WebUSB devices will report
`vid=0x2b24, pid=0x0001`, whereas HID devices report `vid=0x2b24, pid=0x0002`.

If your device is not recognized when plugged in, you may need to use
[Zadig](https://zadig.akeo.ie/) to select the correct device driver: `libusb0 (v1.2.6.0)`.

## Linux

On linux platforms, you'll need to install UDEV rules in order to allow
non-root users to communicate with the device.

Add the following to `/etc/udev/rules.d/99-keepkey.rules`:

```
# HID Firmware / Bootloader
SUBSYSTEM=="usb", ATTR{idVendor}=="2b24", ATTR{idProduct}=="0001", MODE="0666", GROUP="dialout", SYMLINK+="keepkey%n"
KERNEL=="hidraw*", ATTRS{idVendor}=="2b24", ATTRS{idProduct}=="0001",  MODE="0666", GROUP="dialout"

# WebUSB Firmware / Bootloader
SUBSYSTEM=="usb", ATTR{idVendor}=="2b24", ATTR{idProduct}=="0002", MODE="0666", GROUP="dialout", SYMLINK+="keepkey%n"
KERNEL=="hidraw*", ATTRS{idVendor}=="2b24", ATTRS{idProduct}=="0002",  MODE="0666", GROUP="dialout"
```

Then run `sudo udevadm control --reload-rules`. Unplug & replug your device.
You do not need to restart your machine.