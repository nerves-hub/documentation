# Devices

## Initializing

In this example we will create a device with a hardware identifier `1234`. The device will also be tagged with `qa` so we can target it in our deployment group. We will select `y` when asked if we would like to generate device certificates. Device certificates are required for a device to establish a connection with the NervesHub server.

```bash
$ mix nerves_hub.device create

NervesHub organization: nerveshub
identifier: 1234
description: test-1234
tags: qa
Local user password:
Device 1234 created
Would you like to generate certificates? [Yn] y
Creating certificate for 1234
Finished
```

It is important to note that device certificate private keys are generated and stay on your host computer. A certificate signing request is sent to the server, and a signed public key is passed back. Generated certificates will be placed in a folder titled `nerves-hub` in the current working directory. You can specify a different location by passing `--path /path/to/certs` to NervesHubCLI mix commands.

NervesHub certificates and hardware identifiers are persisted to the firmware when the firmware is burned to the SD card. To make this process easier, you can call `nerves_hub.device burn IDENTIFIER`. In this example, we are going to burn the firmware and certificates for device `1234` that we created.

```bash
mix nerves_hub.device burn 1234
```

Your device will now connect to NervesHub when it boots and establishes an network connection.

