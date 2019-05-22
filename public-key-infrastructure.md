# Public key infrastructure

NervesHub uses [asymmetric cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) for authenticating both sides of the connections to NervesHub and for end-to-end validation of firmware images. As with any use of asymmetric cryptography, a public key infrastructure \(PKI\) is required for establishing trust. The PKI for NervesHub is mostly managed by organizations using the service. Choices in NervesHub are intentionally compatible with other IoT services such as AWS IoT so that devices need only be provisioned with one private key rather than having a private key per service. There is no requirement that preventing devices from being issued many private keys, though.

## Client-side SSL for devices

Before use, each device must be provisioned with a signed X.509 certificate and its associated private key. When devices connect to NervesHub they'll validate NervesHub's certificate. Likewise, NervesHub validates these client certificates.

Device certificates are signed by a Device CA certificate owned by you. You will register one or more Device CA certificates with NervesHub. The private keys associated with these certificates are never seen by NervesHub. If necessary, you can secure them in hardware security modules \(HSMs\).

When a device connects for the first time, the device must present both its public certificate and the Device CA Certificate that signed it. Assuming that NervesHub knows the Device CA Certificate and that the Device Certificate has been signed properly, it will allow the device to connect. The Device Certificate is recorded on NervesHub to avoid verifying the chain of trust in the future. This also enables scenarios where Device CA Certificates have short validity time periods or are deleted once all devices signed by it have all connected.

The NervesHub CLI tools provide convenience functions for creating certificates. It's also possible to use openssl or the Elixir [x509 library](https://hex.pm/packages/x509) directly.

Care should be taken when handling the Device CA and device private keys. Compromised private keys can be used to access firmware updates and likely cause other mischief. Consider integrating a hardware storage chip into your device if it does not natively support secure storage. See [NervesKey](nerves-key/getting-started.md) for one solution.

## Firmware update signatures

NervesHub requires cryptographic signatures on all firmware files. As opposed to the X.509 certificates used earlier, this functionality is provided by [libsodium's public key support](https://download.libsodium.org/doc/public-key_cryptography/public-key_signatures). The fwup commandline utility invokes libsodium to create key pairs, sign firmware update files, and verify signatures.

The public keys used to sign firmware update files must be uploaded to NervesHub and accessible to the device's firmware. By default, the NervesHub tooling embedded public keys in the device firmware. Both NervesHub and devices validate firmware signatures. The device validation is the important step since it ensures that the firmware that you generated is identical to the firmware being installed.

NervesHub requires users to register firmware public keys with it. This is used to catch mistakes that would otherwise waste time and bandwidth with deploying firmware that could never install correctly.

Users should protect firmware signing keys. It is possible to register multiple firmware public keys on NervesHub and on devices. This allows more convenient setups where development devices can accept both "production" and "debug" firmware. Devices in the field, though, could have only the "production" public keys and therefore reject accidental deploys of "debug" firmware. Of course, NervesHub supports more mechanisms to group devices so relying on firmware signing keys can be considered a last resort.

