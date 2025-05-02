IoT and embedded devices come in all sorts. Your requirements for updating
firmware on a thermostat, a boat or a running industrial production line are
quite different. NervesHubLink is highly configurable to let you solve for the
various scenarios you might need.

## Client Setup

By default NervesHubLink applies updates as soon as it knows about them from the NervesHubLink server and doesn't give warning before rebooting. Optionally, you can override this behavior if desired by implementing the [`NervesHubLink.Client`](https://hexdocs.pm/nerves\_hub\_link/NervesHubLink.Client.html) behaviour.

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
