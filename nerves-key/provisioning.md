---
description: >-
  NervesKeys must be provisioned before they're used. The NervesKey Quickstart
  firmware is the easiest way of doing this and it will prepare you for
  automating the provisioning steps in the future.
---

# Quickstart

## Prerequisites

To work through this tutorial, you'll need any Raspberry Pi or BeagleBone
device. The NervesKey Quickstart firmware configures Raspberry Pi Zero, Zero W, 3
Model A+, and any BeagleBone-compatible device in what's called gadget mode. In
gadget mode, the device uses a USB cable for power, serial console, and
networking. We recommend using these devices if they're available. The other
devices require a HDMI monitor and keyboard or a wired Ethernet connection.

## Downloading the Firmware

Find the appropriate firmware or zip file
[here](https://github.com/nerves-hub/nerves_key_quickstart/releases). If
you're using `fwup` to write images to MicroSD cards, download the `.fw`
extension and if you're using `etcher`, get the `zip` file. Releases are named
by the boards they support:

* `bbb` - BeagleBone Black, BeagleBone Green, PocketBeagle, etc.
* `rpi0` - Raspberry Pi Zero or Zero W
* `rpi` - The original Raspberry Pi Model B
* `rpi2` Raspberry Pi 2 Model B
* `rpi3` - Raspberry Pi 3 Model B and Model B+
* `rpi3a` - Raspberry Pi 3 Model A+

Once that's done, you're ready to burn the firmware to the MicroSD card.

## Burning the Firmware

Navigate to the directory where you downloaded the firmware. We'll go through
both `fwup` and `etcher` methods.

To be clear, this formats your SD card, and you will *lose all data on the SD
card*. Make sure you're OK with that.

### `fwup`

```console
$ fwup nerves_key_quickstart_rpi0.fw
Use 15.84 GB memory card found at /dev/rdisk2? [y/N] y
```

Depending on your OS, you'll likely be asked to authenticate this action. Go
ahead and do so.

```console
|====================================| 100% (31.81 / 31.81) MB
Success!
Elapsed time: 3.595 s
```

It's quite fast. Now you have Nerves ready to run on your device.  Skip ahead to
the next section.

### `etcher`

Start `etcher`, point it to the zip file, and follow the prompts:

![etcher screenshot](assets/etcher.png)

## Testing the Firmware

Eject the SD card and insert it into the device that you're using. Power up and
connect the device with a USB cable. In the case of the `rpi0`, the micro USB
does both.

Once the device boots, you can now connect to it.
