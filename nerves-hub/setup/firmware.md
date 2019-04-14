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

