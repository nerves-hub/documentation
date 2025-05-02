IoT and embedded devices come in all sorts. Your requirements for updating
firmware on a thermostat, a boat or a running industrial production line are
quite different. NervesHubLink is highly configurable to let you solve for the
various scenarios you might need.

## Connection Setup, SSL and Socket configuration

You can provide your own options to use for the NervesHub socket connection via the `:socket`and `:ssl` keys, which are forwarded on to `slipstream` when creating the socket connection. This can be especially useful if you aren't using a NervesKey and need to adapt the setup in some way.

Any [valid Erlang ssl socket option](http://erlang.org/doc/man/ssl.html#TLS/DTLS%20OPTION%20DESCRIPTIONS%20-%20COMMON%20for%20SERVER%20and%20CLIENT) can go in the `:ssl` key.

```elixir
config :nerves_hub_link,
  socket: [
    json_library: Jason,
    heartbeat_interval: 45_000
  ],
  ssl: [
    cert: "some_cert_der",
    keyfile: "path/to/keyfile"
  ]
```

**Note:** The Application config is evaluated at compile time. Extra caution is needed when using file paths in the config as they might be referencing host paths instead of device paths. Another option would be to configure at runtime (see [Runtime Configuration](adding-nerveshub-to-your-project.md#runtime-configuration))

## Runtime Configuration

Some cases require that connection configuration happens at runtime like selectively choosing which cert/key to use based on device, or reading a file stored on device which isn't available during compilation.

Whatever the reason, `NervesHubLink` also supports runtime configuration via the [`NervesHubLink.Configurator`](https://github.com/nerves-hub/nerves\_hub\_link/blob/master/lib/nerves\_hub\_link/configurator.ex) behavior. This is called during application startup to build the configuration that is to be used for the connection. When implementing the behavior, you'll receive the initial default config read in from the application environment and you can modify it however you need.

For example:

```elixir
defmodule MyApp.Configurator do
  @behaviour NervesHubLink.Configurator

  @impl true
  def build(config) do
    ssl = [certfile: "/root/ssl/cert.pem", keyfile: "/root/ssl/key.pem"]
    %{config | ssl: ssl}
  end
end
```

Then you specify which configurator `NervesHubLink` should use in `config.exs`:

```elixir
config :nerves_hub_link, configurator: MyApp.Configurator
```
