# NervesKey for Raspberry Pi

The NervesKey is a tiny circuit board with an ATECC608A cryptographic chip from Microchip Technology. The chip is a relatively inexpensive addition to embedded computing platforms that include an I2C bus. The NervesKey is primarily intended to offer a non-disruptive adaptation for the Raspberry Pi, but can also be used in other development applications.

![](../.gitbook/assets/nk_assembled.jpg)

The production NervesKey board contains the tiny 'UDFN' version of the part [ATECC608A-MAHDA](https://www.digikey.com/product-detail/en/microchip-technology/ATECC608A-MAHDA-S/ATECC608A-MAHDA-STR-ND/7928113) and a 0.1uF 0402 size capacitor. These boards are not easy assembled by a user, so they are professionally manufactured in high quantity for the Nerves project and offered for sale in low quantity by Nerves contributor [Troodon Software, LLC](http://www.troodon-software.com/).

## Primary Application

The easiest way to outfit a Raspberry Pi with a NervesKey is to solder it to the 'hat' expansion header on the bottom of the board as shown below. This will connect the appropriate signals and keep the board out of the way of other uses of the hat header. The NervesKey board is made relatively thin to support this application.

![](../.gitbook/assets/nk_rpi_bottom_mount.jpg)

