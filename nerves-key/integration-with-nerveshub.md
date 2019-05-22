---
description: >-
  This section walks you through integrating a device with a NervesKey with
  NervesHub.
---

# Integration with NervesHub

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

Make sure that your Nerves project includes the `:nerves_key` dependence in your `mix.exs`:

```text
  defp deps do
    [
      # Dependencies for all targets
      {:nerves, "~> 1.4", runtime: false},
      ... 
      
      # Dependencies for all targets except :host
      {:nerves_hub, "~> 0.2", targets: @all_targets},
      {:nerves_key, "~> 0.3", targets: @all_targets},
      {:nerves_time, "~> 0.2", targets: @all_targets},
    ]
```

Next, update your application supervisor to create the device certificate out of the NervesKey and pass it to the `NervesHub.Supervisor`. Modify the following code if your NervesKey is on a different I2C bus.

```text
def children(_target) do
  {:ok, engine} = NervesKey.PKCS11.load_engine()
  {:ok, i2c} = ATECC508A.Transport.I2C.init([])

  cert =
    NervesKey.device_cert(i2c)
    |> X509.Certificate.to_der()

  signer_cert =
    NervesKey.signer_cert(i2c)
    |> X509.Certificate.to_der()

  key = NervesKey.PKCS11.private_key(engine, i2c: 1)
  cacerts = [signer_cert | NervesHub.Certificate.ca_certs()]

  [
    {NervesHub.Supervisor, [key: key, cert: cert, cacerts: cacerts]}
  ]
end
```

If you have programmed the NervesKey with an auxiliary device certificate and would like to use it, modify the calls to `NervesKey.device_cert/2` and `NervesKey.signer_cert/2` to pass `:aux` as a second parameter.

