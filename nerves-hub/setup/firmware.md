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

With Delta updates, NervesHub can compare two binary files and only update the pieces of the firmware that have changed. This can reduce the size and cost of OTA updates by up to 95%. In order use Delta Updates, you need to do the following:

Be using a fwup >= 1.6.0. If your nerves system is based on nerves_system_br >= 1.11.2 then this will be fine. If not, you may need to upgrade your nerves system.

Be using 	nerves_hub_link >= 0.10.0 or nerves_hub_link_http >= 0.9.0. These versions have not been released yet so the best thing to do for now is to install from the main branch. In your mix.exs file:

```bash
{:nerves_hub_link_http, github: "nerves-hub/nerves_hub_link_http", branch: "main"}
{:nerves_hub_link, github: "nerves-hub/nerves_hub_link", branch: "main"}
```

You must have a nerves system that is compatible with delta updates. You can find instructions here https://github.com/nerves-project/nerves/blob/ec5ae3f2639ededd229ee159d5ec9aa8e23b5470/docs/Experimental%20Features.md for how to make the necessary changes.

You can find instructions in https://hexdocs.pm/nerves/customizing-systems.html on how to generate your own nerves systems. We also have pre-generated examples for the rpi0 and rpi3 which can be added to a mix.exs file like so:

```bash
{:patchable_rpi0,        github: "verypossible/patchable_rpi0",        tag: "v1.12.1",        runtime: false,        targets: :patchable_rpi0}

{:patchable_rpi3,        github: "verypossible/patchable_rpi3",        tag: "v1.12.0",        runtime: false,        targets: :patchable_rpi3}
```
