---
description: >-
  This section walks you through integrating a device with a NervesKey with
  NervesHub.
---

# NervesHubLink and NervesKey

## Device registration

Devices must be registered with NervesHub via the commandline tools, web user interface or via an API integration first to authenticate with a NervesKey. The only exception is if you are using the JITP feature in which case registration is automated.

The command-line tools are the easiest for this. Follow [these instructions](/nerves-hub/cli-tools.md) to get them installed.

You will need the device's manufacturing serial number for this step. If you don't have it `NervesKey.manufacturer_sn/1` can retrieve it. From the CLI, run `nh device create`. Fill in the manufacturing serial number when it asks for an identifier. Fill in the `description` and `tags` fields as you'd like:

```text
$ nh device create
NervesHub server: api.nerves-hub.org:443
NervesHub organization: your_organization
identifier: 123456789ABCDE
description: My favorite device
tags: qa
Local NervesHub user password:
Device 123456789ABCDE created
```

{% hint style="info" %}
When registering devices in bulk, we've found that there's usually a convenient hook in the manufacturing process to call `nh device create` with commandline parameters.
{% endhint %}

If you are not using the Signer CA approach you also want to upload a certificate per device. This can be done using:

```text
nh device cert import 123456789ABCDE device-cert-file-path-goes-here
```

## Integration with your Nerves project

You can tell `nerves_hub_link` to read those certificates and key from the chip and assign the SSL options for you by enabling add it as a dependency in your `mix.exs`:

```elixir
def deps() do
  [
    {:nerves_hub_link, "~> 2.7"},
    {:nerves_key, "~> 1.2"}
  ]
end
```

NervesKey will default to using i2c bus 1 and `:primary` cerificate pair.

NervesHubLink will use the NervesKey library by default if it is available. It still needs to be configured a bit:

```elixir
config :nerves_hub_link,
  # point this at the device endpoint of your instance
  host: "devices.nervescloud.com",
  # if you want the remote console
  remote_iex: true,
```

