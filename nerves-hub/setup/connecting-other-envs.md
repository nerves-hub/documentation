# Connecting to other environments

Some cases may require you to target other instances of NervesHub, such as testing a staging environment or directing devices to a self-hosted NervesHub. In these cases, you'll need to specify a few additional details in your application config:

```elixir
host = "my-nerves-hub.org"
port = 443
# With a separate instance, the Server CA Certificates will be different
# and need to be provided.
ca_certs = "/path/to/my-nerves-hub.org/ca_certs"

config :nerves_hub_link,
  device_api_host: "device.#{host}",
  device_api_sni: 'device.#{host}',
  device_api_port: port,
  ca_certs: ca_certs

config :nerves_hub_user_api, 
  host: "api.#{host}",
  port: port,
  server_name_indication: "api.#{host}",
  ca_certs: ca_certs
```
