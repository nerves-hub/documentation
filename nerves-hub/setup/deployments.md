# Deployments

## Creating

Deployments associate firmware images to devices. NervesHub won't send firmware to a device until you create a deployment. First find the UUID of the firmware. You can list the firmware on NervesHub by calling:

```bash
mix nerves_hub.firmware list

Firmwares:
------------
  product:      example
  version:      0.3.0
  platform:     rpi3
  architecture: arm
  uuid:         1cbecdbb-aa7d-5aee-4ba2-864d518417df
```

In this example we will create a new deployment for our test group using firmware `1cbecdbb-aa7d-5aee-4ba2-864d518417df`.

```bash
mix nerves_hub.deployment create

NervesHub organization: nerveshub
Deployment name: qa_deployment
firmware uuid: 1cbecdbb-aa7d-5aee-4ba2-864d518417df
version condition: < 0.3.0
tags: qa
Local user password:
Deployment test created
```

Here we create a new deployment called `qa_deployment`. In the conditions of this deployment, we specify "< 0.3.0" and the `tags` set to only `qa`. This means that in order for a device to qualify for an update, it needs to have at least the tags `[qa]` and the device must not already have been updated to this version.

At this point we can try to update the connected device.

Start by bumping the application version number from `0.1.0` to `0.1.1`. Then, create new firmware:

```bash
mix firmware
```

We can publish, sign, and deploy firmware in a single command now.

```bash
mix nerves_hub.firmware publish --key devkey --deploy qa_deployment
```

## Conditionally applying updates

It's not always appropriate to apply a firmware update immediately. Custom logic can be added to the device by implementing the `NervesHub.Client` behaviour and telling the NervesHub OTP application about it.

Here's an example implementation:

```elixir
defmodule MyApp.NervesHubClient do
   @behaviour NervesHub.Client

   # May return:
   #  * `:apply` - apply the action immediately
   #  * `:ignore` - don't apply the action, don't ask again.
   #  * `{:reschedule, timeout_in_milliseconds}` - call this function again later.

   @impl NervesHub.Client
   def update_available(data) do
    if SomeInternalAPI.is_now_a_good_time_to_update?(data) do
      :apply
    else
      {:reschedule, 60_000}
    end
   end
end
```

To have NervesHub invoke it, update your `config.exs` as follows:

```elixir
config :nerves_hub, client: MyApp.NervesHubClient
```

