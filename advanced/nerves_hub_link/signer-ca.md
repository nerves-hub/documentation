IoT and embedded devices come in all sorts. Your requirements for updating
firmware on a thermostat, a boat or a running industrial production line are
quite different. NervesHubLink is highly configurable to let you solve for the
various scenarios you might need.

## Using your own Signer CA

In some cases you may opt to use your own device signer CA when creating device certificates. If you do, there a few things needed for a successful connection:

1. Your signer CA must be uploaded to NervesHub. You can do this a couple ways

* From the web UI at `https://www.nerves-hub.org/settings/{YOUR_ORG}/certificates/new`
* Via CLI with `mix nerves_hub.ca_certificate register /path/to/signer-ca.pem`

2. The signer CA must be included in the connection request alongside the NervesHub intermediate CAs. If you're using [`NervesKey`](https://github.com/nerves-hub/nerves\_key), then this is typically handled for you. If managing certificates more manually, it is usually best to read at runtime and appened to the CA list. Here's a basically example of what that step might look like:

```elixir
signer_cert =
  File.read!("/path/to/signer.pem")
  |> NervesHubLink.Certificate.pem_to_der()

cacerts = [signer_cert | NervesHubLink.Certificate.ca_certs()]
```
