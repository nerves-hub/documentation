# Add NervesHub to your project

The first step is to add [`nerves_hub_link`](https://github.com/nerves-hub/nerves\_hub\_link) to your target dependencies in your project's `mix.exs`. Since NervesHub uses SSL certificates, the time must be set on the device or certificate validity checks will fail. If you're not already setting the time, add [`nerves_time`](https://hex.pm/packages/nerves\_time) to your dependencies. For example:

```elixir
  defp deps(target) do
    [
      {:nerves_runtime, "~> 0.9"},
      {:nerves_hub_link, "~> 2.0"},
      {:nerves_hub_cli, "2.0.0-dev"},
      {:nerves_time, "~> 0.2"},
      ...
    ] ++ system(target)
  end
```

Next, update your `config.exs` so that the [`nerves_hub_link`](https://github.com/nerves-hub/nerves\_hub\_link) library can help provision devices. Do this by adding `provisioning: :nerves_hub_link` to the `:nerves, :firmware` option like this:

```elixir
config :nerves, :firmware,
  provisioning: :nerves_hub_link
```

Adding this library will start a long-lived websocket connection with NervesHub for communicating device updates.

