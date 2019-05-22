# Add NervesHub to your project

The first step is to add `nerves_hub` to your target dependencies in your project's `mix.exs`. Since NervesHub uses SSL certificates, the time must be set on the device or certificate validity checks will fail. If you're not already setting the time, add [`nerves_time`](https://hex.pm/packages/nerves_time) to your dependencies. For example:

```elixir
  defp deps(target) do
    [
      {:nerves_runtime, "~> 0.9"},
      {:nerves_hub, "~> 0.1"},
      {:nerves_time, "~> 0.2"},
      ...
    ] ++ system(target)
  end
```

Next, update your `config.exs` so that the `nerves_hub` library can help provision devices. Do this by adding `provisioning: :nerves_hub` to the `:nerves, :firmware` option like this:

```elixir
config :nerves, :firmware,
  provisioning: :nerves_hub
```

The library won't connect to [nerves-hub.org](https://nerves-hub.org) unless requested. The easiest way is to add `NervesHub.Supervisor` to your main application supervisor:

```elixir
  defmodule Example.Application do
    use Application

    def start(_type, _args) do

      opts = [strategy: :one_for_one, name: Example.Supervisor]
      children = [
        NervesHub.Supervisor
      ] ++ children(@target)
      Supervisor.start_link(children, opts)
    end
  end
```

SSL options can be configured by passing them into the NervesHub supervisor. This is useful for applications that store their ssl credentials in different places, such as [NervesKey](https://github.com/nerves-hub/nerves_key).

```elixir
  defmodule Example.Application do
    use Application

    def start(_type, _args) do
      {:ok, engine} = NervesKey.PKCS11.load_engine()
      {:ok, i2c} = ATECC508A.Transport.I2C.init([])
      nerves_key_socket_opts = [
        key: NervesKey.PKCS11.private_key(engine, {:i2c, 1}),
        cert: X509.Certificate.to_der(NervesKey.device_cert(i2c)),
        cacerts: [X509.Certificate.to_der(NervesKey.signer_cert(i2c)) | NervesHub.Certificate.ca_certs()],
      ]

      opts = [strategy: :one_for_one, name: Example.Supervisor]
      children = [
        {NervesHub.Supervisor, nerves_key_socket_opts}
      ] ++ children(@target)
      Supervisor.start_link(children, opts)
    end
  end
```

