# Private keys and certificates

Each NervesKey holds one ECC P256 private key. The provisioning process creates the private key and it never leaves the module. The ATECC508A and ATECC608A modules used on the NervesKeys have countermeasures to make extracting the private keys impractical. The associated public key can always be retrieved from the NervesKey.

The X.509 standards define an encoding for keys and their associated metadata. A key and its metadata are referred to as a certificate. Certificates can then be cryptographically signed by other keys to establish a chain of trust. X.509 certificates have two common encodings, PEM and DER. DER is a binary encoding and PEM is a text encoding.

The NervesKey holds information for up to four certificates:

1. A primary device certificate
2. The certificate that signed the primary device certificate
3. An auxiliary device certificate
4. The certificate that signed the auxiliary device certificate

The provisioning procedure requires a signing certificate and creates the primary device certificate. Both certificates are recorded to the NervesKey. The auxiliary certificates are similar in function, but can be filled in later and rewritten.

The X.509 certificate that signs the device certificate is also called a Device Certificate Authority certificate \(Device CA certificate\) and must be uploaded to NervesHub for the device to be allowed to connect over TLS.

All certificates have validity periods. Validity periods establish the dates where a device can be used. It is crucial to have these set appropriately or TLS connections will not work. Deciding how long certificates are valid requires some thought. Device CA certificates are maintained outside of the NervesKey and may need to be kept in less than ideal conditions. NervesHub only requires a valid Device CA certificate the first time that a device connects \(it saves trusted device certificates\).

{% hint style="warning" %}
Nerves devices often start with their clocks set to January 1st, 1970. X.509 certificates will fail validation unless the clock is updated. The [`nerves_time`](https://hex.pm/packages/nerves_time) library is one option for synchronize the clock.
{% endhint %}

Since the NervesKey protects the private key, device certificates typically have long validity periods. The Device CA certificates have much shorter validity periods and can be removed from NervesHub once all devices have connected once.

Since the NervesKey has limited storage, the contents of the certificates must be kept small. Additional compromises were made in the granularity of validity dates and other fields. The NervesKey software follows the [ATECC Compressed Certificate Specification](http://ww1.microchip.com/downloads/en/AppNotes/Atmel-8974-CryptoAuth-ATECC-Compressed-Certificate-Definition-ApplicationNote.pdf) for encoding and storing certificates. Unfortunately, the compressed certificate constraints make it difficult to generate certificates with programs like `openssl`. The `nerves_key` library has helper functions that create certificates using the `x509` library and Erlang's `public_key` application. Note that the X.509 certificates are still completely valid and usable by services that require them. If a third party service cannot be made to work with the constraints imposed by the NervesKey, you can still create an X.509 certificate for their use with the public key from the NervesKey. However, you cannot store that X.509 certificate in the NervesKey.

