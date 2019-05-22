# Introduction

A NervesKey is a cryptographic hardware module that is configured to work with NervesHub. Its primary responsibility is to secure the private key used for authentication. It can also store useful information such as device serial numbers, board name, and important settings.

The following are options for adding a NervesKey to your device:

1. If you're making a custom board, add a Microchip [ATECC508A](https://octopart.com/search?q=atecc508a) or [ATECC608A](https://octopart.com/search?q=atecc608a) to an available I2C bus
2. Obtain a [NervesKey](https://www.tindie.com/products/troodonsw/nerveskey/) and solder it to the GPIO connector on a Raspberry Pi
3. Contact us for information about USB NervesKey

The NervesKey configuration of the ATECC508A/ATECC608A is documented in the [nerves\_key project's README.md](https://github.com/nerves-hub/nerves_key#atecc508a-configuration). The cryptographic modules are quite capable devices. If the NervesKey configuration does not meet your needs, refer to the module datasheets and use the [nerves\_key configuration](https://github.com/nerves-hub/nerves_key#atecc508a-configuration) as a helpful example.

{% hint style="danger" %}
Except for a few cases, NervesKeys come unprogrammed. Some fields in the NervesKey can only be programmed once.
{% endhint %}

