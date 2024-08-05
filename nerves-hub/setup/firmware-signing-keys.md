# Firmware signing keys

NervesHub requires cryptographic signatures on all managed firmware. Devices receiving firmware from NervesHub validate signatures. Since firmware is signed before uploading to NervesHub, NervesHub or any service NervesHub uses cannot modify it.

Firmware authentication uses [Ed25519 digital signatures](https://en.wikipedia.org/wiki/EdDSA#Ed25519). You need to create at least one public/private key pair and copy the public key part to NervesHub and to devices. NervesHub tooling helps with both. A typical setup has multiple signing keys to support key rotation and "development" keys that are not as protected.

Start by creating a `devkey` firmware signing key pair:

```bash
mix nerves_hub.key create devkey
```

On success, you'll see the public key. You can confirm using the NervesHub web interface that the public key exists. Private keys are never sent to the NervesHub server. NervesHub requires valid signatures from known keys on all firmware it distributes.

The next step is to make sure that the public key is embedded into the firmware image. This is important. The device uses this key to verify the firmware it receives from a NervesHub server before applying the update. This protects the device against anyone tampering with the firmware image between when it was signed by you and when it is installed.

All firmware signing public keys need to be added to your `config.exs`.

```elixir
config :nerves_hub_link,
  fwup_public_keys: [
    # devkey
    "bM/O9+ykZhCWx8uZVgx0sU3f0JJX7mqnAVU9VGeuHr4="
  ]
```

The `nerves_hub_link` dependency converts key names to public keys at compile time. If you haven't compiled your project yet, run `mix firmware` now. If you have compiled it, `mix` won't know to recompile `nerves_hub_link` due to the configuration change. Force it to recompile by running:

```bash
mix deps.compile nerves_hub_link --force
mix firmware
```

While not shown here, you can export keys for safe storage. Additionally, key creation and firmware signing can be done outside of the `mix` tooling. The only part that is required is that the firmware signing public keys be added to your `config.exs` and to the NervesHub server.

