---
description: >-
  This section walks you through integrating a device with a NervesKey with
  NervesHub.
---

# NervesHubLink integration

## Device registration

Devices must be registered with NervesHub via the commandline tools, web user interface or User API first. You will need the device's manufacturing serial number for this step. If you don't have it, the Quickstart firmware or `NervesKey.manufacturing_sn/1` can retrieve it. From the CLI, run `mix nerves_hub.device create`. Fill in the manufacturing serial number when it asks for an identifier. Fill in the `description` and `tags` fields as you'd like:

```text
$ mix nerves_hub.device create
NervesHub server: api.nerves-hub.org:443
NervesHub organization: your_organization
identifier: 123456789ABCDE
description: My favorite device
tags: qa
Local NervesHub user password:
Device 123456789ABCDE created
```

{% hint style="info" %}
When registering devices in bulk, we've found that there's usually a convenient hook in the manufacturing process to call `mix nerves_hub.device create` with commandline parameters. A future enhancement to NervesHub will allows you to have NervesHub automatically create devices that present properly signed device certificates and firmware metadata.
{% endhint %}

### Integration with your Nerves project

You can tell `nerves_hub_link` to read those certificates and key from the chip and assign the SSL options for you by enabling add it as a dependency in your `mix.exs`:

```elixir
def deps() do
  [
    {:nerves_hub_link, "~> 0.7"},
    {:nerves_key, "~> 0.5"}
  ]
end
```

NervesKey will default to using i2c bus 1 and `:primary` cerificate pair. However, you can cusomtize these options as well to use a different bus and certificate pair:

```elixir
config :nerves_hub_link, :nerves_key,
  certificate_pair: :aux,
  i2c_bus: 0
```

