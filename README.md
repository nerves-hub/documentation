# Introduction

{% hint style="danger" %}
This documentation is being heavily written, edited and reorganized. Please continue to use the documentation in the README.md files in the NervesHub GitHub repositories.
{% endhint %}

NervesHub is an open-source firmware update server that works well with Nerves-based devices. A managed version is available at [nerves-hub.org](https://nerves-hub.org) and it's possible to host your own.

NervesHub provides many of the features that you'd expect in a firmware update server. Fundamentally, devices connect to the server either by polling at a pre-configured interval or by joining a long-lived [Phoenix channel](https://hexdocs.pm/phoenix/channels.html). If a firmware update is available, NervesHub provides a URL to the device and the device can update immediately or when convenient.

NervesHub leverages best practices and libraries from the Elixir community to provide a service that scales from hobbyist use to large scale industrial deployments. Since all components are open-source, there is no lock in. Extensive regression tests enable your development team to extend NervesHub with confidence if necessary. Of course, we hope that you will contribute improvements back.

Security is a primary concern of NervesHub:

* Cryptographic signatures on firmware updates provide end-to-end authentication
* Client-side and server-side SSL provide link authentication and encryption
* Access control within organizations to firmware updates

NervesHub provides the tools for safe remote updates. While it's not possible for the firmware update server alone to ensure that firmware updates are always safe, NervesHub does provide lightweight verification of firmware and detects misbehaving devices.

### High-level architecture

The following diagram shows the primary NervesHub components. 

![](.gitbook/assets/nerveshub-architecture-1.png)

The NervesHub server provides web and API endpoints for most interactions. The actual firmware storage and delivery resides on an object storage service such as AWS S3. 

While NervesHub could operate with many firmware update file formats in principle, it currently expects files using the format provided by [fwup](https://github.com/fhunleth/fwup). The fwup tool provides the low level packaging and application of firmware images. Firmware files are ZIP-formated files with metadata.

User interaction with NervesHub is through the web interface and via a CLI provided by an extension to Elixir's mix build tool. The CLI tools and their underlying User API endpoint are useful for automating common tasks.

Devices connect to NervesHub via a separate API endpoint that exposes only the functionality needed for firmware updates. For Nerves-based devices, the [nerves\_hub](https://github.com/nerves-hub/nerves_hub) library manages the interactions with the NervesHub server and invokes fwup to apply updates.

The final component is the NervesHub Certificate Authority \(CA\). This component issues the client-side SSL certificates for the User API. Devices also use client-side SSL, but those certificates are issued and managed by you, the user of NervesHub. 

### Public key infrastructure

NervesHub uses [asymmetric cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) for authentication and end-to-end validation of firmware images. 

### 











NervesHub does impose some requirements on devices and firmware that may require changes to your Nerves projects:

* Firmware images are cryptographically signed \(both NervesHub and devices

  validate signatures\)

* Devices are identified by a organization-wide unique serial number
* Each device has its own SSL certificate for authentication with NervesHub

These changes enable NervesHub to provide assurances that the firmware that you intend to install on a set of devices make it to those devices unaltered.



