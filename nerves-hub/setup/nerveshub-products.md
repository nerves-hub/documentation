# Products

A NervesHub product groups devices that run the same kind of firmware. All devices and firmware images have a product. NervesHub provides finer grain mechanisms for grouping devices, but a product is needed to get started.

By default, NervesHub uses the `:app` name in your `mix.exs` for the product name. If you would like it to use a different name, add a `:name` field to your `Mix.Project.config()`. For example, NervesHub would use "My Example" instead of "example" for the following project:

```elixir
  def project do
    [
      app: :example,
      name: "My Example"
    ]
  end
```

For the remainder of this document, though, we will not use the `:name` field and simply use the product name `example`.

Create a new product on NervesHub by running:

```bash
mix nerves_hub.product create
```

