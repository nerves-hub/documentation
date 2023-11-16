# Devices

## Initializing

In this example we will create a device with a hardware identifier `1234`. The device will also be tagged with `qa` so we can target it in our deployment group. Device certificates are required for a device to establish a connection with the NervesHub server.

```bash
$ mix nerves_hub.device create

NervesHub organization: nerveshub
identifier: 1234
description: test-1234
tags: qa
Local user password:
Device 1234 created
Finished
```

If your device has an ATECCx08A module or NervesKey that has been provisioned by a CA/signer certificate known to NervesHub, the device is ready to go. If not using a hardware module to protect the device's private key, create and register a certificate and key pair manually by running:

{% code overflow="wrap" %}
```
mix nerves_hub.device cert create 1234 --signer-key key.pem --signer-cert cert.pem
```
{% endcode %}

NervesHub certificates and hardware identifiers are persisted to the firmware when the firmware is burned to the SD card. To make this process easier, you can call `nerves_hub.device burn IDENTIFIER`. In this example, we are going to burn the firmware and certificates for device `1234` that we created.

```bash
mix nerves_hub.device burn 1234
```

Your device will now connect to NervesHub when it boots and establishes an network connection.
