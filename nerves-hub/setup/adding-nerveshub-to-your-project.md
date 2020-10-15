# Add NervesHub to your project

The first step is to add [`nerves_hub_link`](https://github.com/nerves-hub/nerves_hub_link) to your target dependencies in your project's `mix.exs`. Since NervesHub uses SSL certificates, the time must be set on the device or certificate validity checks will fail. If you're not already setting the time, add [`nerves_time`](https://hex.pm/packages/nerves_time) to your dependencies. For example:

```elixir
  defp deps(target) do
    [
      {:nerves_runtime, "~> 0.9"},
      {:nerves_hub_link, "~> 0.1"},
      {:nerves_time, "~> 0.2"},
      ...
    ] ++ system(target)
  end
```

Next, update your `config.exs` so that the [`nerves_hub_link`](https://github.com/nerves-hub/nerves_hub_link) library can help provision devices. Do this by adding `provisioning: :nerves_hub_link` to the `:nerves, :firmware` option like this:

```elixir
config :nerves, :firmware,
  provisioning: :nerves_hub_link
```

Adding this library will start a long-lived websocket connection with NervesHub for communicating device updates. If you'd rather use HTTP polling, use [`nerves_hub_link_http`](https://github.com/nerves-hub/nerves_hub_link_http) instead.

## Connection Setup

See the [`NervesKey: NervesHubLink Integration`](../../nerves-key/integration-with-nerveshublink.md#integration-with-your-nerves-project) section for connection setup if you are using NervesKey

If you aren't using NervesKey, you can also provide your own options to use for the NervesHub socket connection via the `:socket`and `:ssl` keys, which are forwarded on to `phoenix_client` when creating the socket connection \(see [`PhoenixClient.Socket`module](https://github.com/mobileoverlord/phoenix_client/blob/master/lib/phoenix_client/socket.ex#L57-L91) for supported options.\)

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

**Note:** The Application config is evaluated at compile time. Extra caution is needed when using file paths in the config as they might be referencing host paths instead of device paths. Another option would be to configure at runtime (see [Runtime Configuration](#runtime-configuration))

### Using your own Signer CA

In some cases you may opt to use your own device signer CA when creating device certificates. If you do, there a few things needed for a successful connection:

1. Your signer CA must be uploaded to NervesHub. You can do this a couple ways
  * From the web UI at `https://www.nerves-hub.org/settings/{YOUR_ORG}/certificates/new`
  * Via CLI with `mix nerves_hub.ca_certificate register /path/to/signer-ca.pem`
2. The signer CA must be included in the connection request alongside the NervesHub intermediate CAs. If you're using [`NervesKey`](https://github.com/nerves-hub/nerves_key), then this is typically handled for you. If managing certificates more manually, it is usually best to read at runtime and appened to the CA list. Here's a basically example of what that step might look like:

```elixir
signer_cert =
  File.read!("/path/to/signer.pem")
  |> NervesHubLink.Certificate.pem_to_der()

cacerts = [signer_cert | NervesHubLink.Certificate.ca_certs()]
```

## Runtime Configuration

Some cases require that connection configuration happens at runtime like selectively choosing which cert/key to use based on device, or reading a file stored on device which isn't available during compilation.

Whatever the reason, `NervesHubLink` also supports runtime configuration via the [`NervesHubLink.Configurator`](https://github.com/nerves-hub/nerves_hub_link/blob/master/lib/nerves_hub_link/configurator.ex) behavior. This is called during application startup to build the configuration that is to be used for the connection. When implementing the behavior, you'll receive the initial default config read in from the application environment and you can modify it however you need.

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

## Client Setup

By default NervesHubLink applies updates as soon as it knows about them from the NervesHubLink server and doesn't give warning before rebooting. Optionally, you can override this behavior if desired by implementing the [`NervesHubLink.Client`](https://hexdocs.pm/nerves_hub_link/NervesHubLink.Client.html) behaviour.

This let's devices hook into the decision making process and monitor the update's progress.

### Example

```elixir
defmodule MyApp.NervesHubLinkClient do
  @behaviour NervesHubLink.Client
  # May return:
  #  * `:apply` - apply the action immediately
  #  * `:ignore` - don't apply the action, don't ask again.
  #  * `{:reschedule, timeout_in_milliseconds}` - call this function again later.
  @impl NervesHubLink.Client
  def update_available(data) do
    if SomeInternalAPI.is_now_a_good_time_to_update?(data) do
      :apply
    else
      {:reschedule, 60_000}
    end
  end
end
```

To have NervesHubLink invoke it, add the following to your `config.exs`:

```elixir
config :nerves_hub_link, client: MyApp.NervesHubLinkClient
```
