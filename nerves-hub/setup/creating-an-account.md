# Creating an account

The [nerves-hub.org](https://nerves-hub.org) NervesHub server is currently in limited beta so it does not allow new users to sign up. However, you found these docs and if you can use `mix` and endure some API changes, you can join us.

In addition to the web site, NervesHub provides a command line interface \(CLI\). Some features are only available via the CLI. To enable the CLI in your project, add[nerves\_hub\_cli](https://hex.pm/packages/nerves_hub_cli) to your dependency list:

```elixir
  defp deps do
    [
      {:nerves, "~> 1.3", runtime: false},
      {:nerves_hub_cli, "~> 0.1", runtime: false}
      ...
    ] ++ deps(@target)
  end
```

Run `mix deps.get` to download the `nerves_hub_cli` dependency.

Presumably you do not have an account yet. Create one by running:

```bash
mix nerves_hub.user register
```

If you already have an account, make sure that you have authenticated by running:

```bash
mix nerves_hub.user auth
```

