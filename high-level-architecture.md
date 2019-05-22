# High level architecture

The following diagram shows the primary NervesHub components.

![NervesHub architecture](.gitbook/assets/nerveshub-architecture-1.png)

The NervesHub server provides web and API endpoints for most interactions. The actual firmware storage and delivery resides on an object storage service such as AWS S3.

While NervesHub could operate with many firmware update file formats in principle, it currently expects files using the format provided by [fwup](https://github.com/fhunleth/fwup). The fwup tool provides the low level packaging and application of firmware images. Firmware files are ZIP-formated files with metadata.

User interaction with NervesHub is through the web interface and via a CLI provided by an extension to Elixir's mix build tool. The CLI tools and their underlying User API endpoint are useful for automating common tasks.

Devices connect to NervesHub via a separate API endpoint that exposes only the functionality needed for firmware updates. For Nerves-based devices, the [nerves\_hub](https://github.com/nerves-hub/nerves_hub) library manages the interactions with the NervesHub server and invokes fwup to apply updates.

The final component is the NervesHub Certificate Authority \(CA\). This component issues the client-side SSL certificates for the User API. Devices also use client-side SSL, but those certificates are issued and managed by you, the user of NervesHub.