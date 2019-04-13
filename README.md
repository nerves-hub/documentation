# Introduction

NervesHub is an open-source firmware update server that works well with Nerves-based devices. A managed version is available at [nerves-hub.org](https://nerves-hub.org) and it's possible to host your own.

NervesHub provides many of the features that you'd expect in a firmware update server. Fundamentally, devices connect to the server either by polling at a pre-configured interval or by joining a long-lived Phoenix channel. If a firmware update is available, NervesHub will provide a URL to the device and the device can update immediately or when convenient.

NervesHub does impose some requirements on devices and firmware that may require changes to your Nerves projects:

* Firmware images are cryptographically signed \(both NervesHub and devices

  validate signatures\)

* Devices are identified by a unique serial number
* Each device has its own SSL certificate for authentication with NervesHub

These changes enable NervesHub to provide assurances that the firmware bits that you intend to install on a set of devices make it to those devices unaltered.

