# Firmware

## Publishing

Uploading firmware to NervesHub is called publishing. To publish firmware start by calling:

```bash
mix firmware
```

Firmware can only be published if has been signed. You can sign the firmware by running.

```bash
mix nerves_hub.firmware sign --key devkey
```

Firmware can also be signed while publishing:

```bash
mix nerves_hub.firmware publish --key devkey
```

## Delta Updates

With Delta updates, NervesHub can compare two binary files and only update the pieces of the firmware that have changed. This can reduce the size and cost of OTA updates by up to 95%. There are a few requirements to use Delta Updates:

* You will need to enable delta updates, on a per product basis, on the product settings page in your [nerves-hub.org](https://nerves-hub.org) account.
* `fwup >= 1.6.0` - If your nerves system is based on `nerves_system_br >= 1.11.2` then this condition is met. If not, you may need to upgrade your nerves system.
* `nerves_hub_link >= 0.9.1`
* Delta update compatible nerves system
  * If needed, you can find instructions in [Nerves: Experimental Features](https://hexdocs.pm/nerves/experimental-features.html#firmware-patches) for how to make the necessary changes in your system to support firmware patches.
  * see [Nerves: Customizing Systems](https://hexdocs.pm/nerves/customizing-systems.html) for instructions on creating your own Nerves system if needed.
