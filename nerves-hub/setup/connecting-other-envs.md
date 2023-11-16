# Connecting to your environment

To configure NervesHubLink to talk to your hosted server, the following configuration must be set:

```elixir
# The Server CA Certificates need to be provided.
ca_certs = "/path/to/my-nerves-hub.org/ca_certs"

config :nerves_hub_link,
  device_api_host: "device.my-nerves-hub.org",
  device_api_sni: 'device.my-nerves-hub.org',
  device_api_port: 443,
  ca_certs: ca_certs

config :nerves_hub_cli, 
  host: "my-nerves-hub.org",
  port: 443,
  server_name_indication: 'my-nerves-hub.org',
  ca_certs: ca_certs
```
