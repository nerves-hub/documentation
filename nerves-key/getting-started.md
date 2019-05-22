# NervesKey for Raspberry Pi

Raspberry Pis are popular boards for prototyping and low volume deployments. Without additional hardware, the only place to store private keys is on the MicroSD card. Since the Raspberry Pi does not have a secure bootloader, the keys either need to be stored in the clear or obfuscated somehow using information in the firmware image or elsewhere on the PI. Neither option is ideal.

The NervesKey for Raspberry Pi is a tiny circuit board with an ATECC608A that can be mounted underneath the Raspberry Pi's GPIO header:

![](../.gitbook/assets/nk_rpi_bottom_mount.jpg)

This location doesn't interfere with Raspberry Pi hats and still fits in many cases. If you would like a less permanent option, you can add a header to the NervesKey and attach it to the top of the Raspberry Pi or even add header pins for insertion in a breadboard. We recommend soldering it to the bottom, though, since NervesKeys are typically associated with one device. The keys can also be removed using solder wick.

### Mounting the NervesKey

If you're using a Raspberry Pi Zero or Zero W that does not have header pins, solder those in first. If you're planning on mounting the NervesKey to the bottom of the board, try to use a little less solder on the right-most pins. Refer to the picture above for where the NervesKey is mounted. By using less solder, you'll be able to mount the NervesKey flatter on the bottom of the board.

{% hint style="info" %}
In an attempt to mount the NervesKey as flat as possible, we used solder wick to remove solder off the header pins on a few Raspberry Pis. We've decided that it wasn't worth it. The NervesKey mounts close enough to flat on the boards we have anyway. If you soldered header pins in and put a particularly large amount of solder on the pins, you may need to remove some to mount the board.
{% endhint %}

The NervesKey connects to the I2C and power pins on the GPIO connector. If you match your work to the picture, you'll be fine.

After you've soldered the NervesKey, go to the [Provisioning](provisioning.md) section for programming it.

### More information and schematics

If you're already designing a custom board that attaches to the Raspberry Pi's GPIO header, it is likely going to be more convenient and less expensive to add an ATECC508A or ATECC608A to your board. See the [schematics](https://github.com/nerves-hub/nerves_key/blob/master/hw/assets/TSW19001_NERVESKEY_X1_SCH.PDF) for the connections.

