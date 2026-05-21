# Quickstart

This tutorial will get you up and running with a device on your NervesHub
instance with minimal fuss. It uses the simplest approach. We offer
[a separate guide](/production/index.md) for production deployments with security hardware in
place.

We will:

- Create a Nerves project
- Set up a NervesHub product for Shared Secret auth
- Deploy a device
- Create and sign firmware
- Deploy a firmware update
- Rejoice!

## Create a Nerves project (or use an existing one)

Assuming you've [installed Nerves](https://hexdocs.pm/nerves/installation.html)
you should be able to run the following command:

```sh
mix nerves.new my_project
```

This gets you a Nerves project including a bunch of the [supported Nerves
systems](https://hexdocs.pm/nerves/supported-targets.html#supported-targets-and-systems)
by default. We will pretend that you are using a Raspberry Pi 4 for this guide.

```sh
export MIX_TARGET=rpi4
```

After running that your Nerves-related `mix` commands will know which system
you are targeting.

## Set up your NervesHub product

Now log in to the web UI of your NervesHub instances. For NervesCloud this is
[manage.nervescloud.com](https://manage.nervescloud.com).

You should already have an organization in your name. Selecting it should take
you to the Products view. Hit the button for creating a new Product. We can use
the defaults but we want to add a name. There is a convenience in using the
same name for this as your Nerves project so let's use `"my_project"`.

Select your Product, go to the Settings of the Product and find the section for
Shared Secret authentication. Hit the button for creating a new Shared Secret.

## Add new dependencies

In your Nerves project find `mix.exs` and in the function called `deps` add:

```elixir
{:nerves_hub_link, "~> 2.12"},
```

Now run:

```sh
mix deps.get
```

## Configuration

Your `config/target.exs` is for configuration that applies to your target device. Here you can grab those Shared Secret credentials you added to your product and add them:

```elixir
config :nerves_hub_link,
  # Replace this with your instance device endpoint if hosting your own
  host: "devices.nervescloud.com",
  # Enable the very nice remote console
  remote_iex: true,
  shared_secret: [
    product_key: "YourProductKey",
    product_secret: "YourProductSecret",
  ]
```

You can add this to your `config/dev.exs` and `config/test.exs` to stop
NervesHubLink from connecting in development or test runs:

```elixir
config :nerves_hub_link, connect: false
```

Some systems will be able to do networking using USB gadget mode which is great
works. It doesn't always work and is sometimes not practical. It also won't
provide Internet access. Which we need for NervesHub.
Ethernet will work right out of the box if you have it. Otherwise Wi-Fi becomes
your best option. Again, in `config/target.exs` you can set up your `wlan0`:

```
config :vintage_net,
  config: [
    {"usb0", %{type: VintageNetDirect}},
    {"eth0",
     %{
       type: VintageNetEthernet,
       ipv4: %{method: :dhcp}
     }},
    # Typically you only need to change the wlan0 config
    {"wlan0",
      %{
        type: VintageNetWiFi,
        vintage_net_wifi: %{
          networks: [
            %{
              key_mgmt: :wpa_psk,
              ssid: "your-network-name",
              psk: "your-network-password",
            }
          ]
        },
        ipv4: %{method: :dhcp},
      }
    }
  ]
```

## Build and deploy

Remember to ensure you have `MIX_TARGET` set to the appropriate target and
that you have run `mix deps.get` for the target. You can now build the
firmware:

```sh
mix firmware
```

Typically then you'll use and SD card reader to burn the image onto an SD card.
When working with Compute Modules and other non-SD devices there is some other
process to get flashing done. This tutorial doesn't cover that, consult your
system documentation:

```sh
mix burn
```

Then insert the SD card into the device and power it up.

It should transmit the `nerves.local` mDNS hostname that you can then SSH into.

## Device shows up in NervesHub

Given a bit of time and if it has Ethernet or Wi-Fi it should reach NervesHub
and show up in the Devices list of your product.

If it fails to show up, SSH to the device and run `RingLogger.next` to see why
it fails. You can also use `NervesHubLink.reconnect()` to trigger a reconnect.

Sometimes it can take a while before the device clock is updated and picked up
by the Erlang runtime. In that case you may see connection errors as the
Shared Secret cryptography requires the clock to be something reasonable.

Assuming your device shows up at this point we move on. If you can't get it
working, consult [the Nerves section](https://elixirforum.com/c/nerves-forum)
of the Elixir Forum and feel free to ask for help.

## Install the CLI

The easiest way to install is via [Homebrew](https://brew.sh/).

```
brew install nerves-hub/tap/nh
```

The second easiest is via curl:ing a shell script.

```
curl --proto '=https' --tlsv1.2 -fsSL https://raw.githubusercontent.com/nerves-hub/nerves_hub_cli/master/install.sh | sh
```

More details and alternative installation methods are available in [the repo](https://github.com/nerves-hub/nerves_hub_cli).

To set your NervesHub instance to use with the CLI, use this command, replacing the specific URL with your instance:

```sh
nh config set uri "https://manage.nervescloud.com/"
```

You need to be authorized with the NervesHub instance, you get that via:

```sh
nh user auth
```

You can test it out with:

```sh
nh device list --org my-org --product my_project
```

Then to avoid setting those org and product flags all the time you can set env
vars. This means a tool like `direnv` can be helpful to manage per-project env
vars:

```sh
export NERVES_HUB_ORG="my-org"
export NERVES_HUB_PRODUCT="my_project"
```

## Firmware signing

To update devices with new firmware the firmware must be cryptographically
signed. We create a signing key like this:

```sh
nh key create my-key
```

It will ask you for a password and then produce a public key while saving the
password-protected key in a special directory. It will also upload the public
key to NervesHub as a firmware signing key attached to your organization. Keys
can also be added on your Organization in the web UI by visiting Signing Keys.

## Create a firmware update

Let's modify the project. We don't have to do real work on it, we can just grab
`mix.exs` and bump the version number. Then we build it:

```sh
mix firmware
```

Then you can run this to sign it:

```sh
nh firmware sign "./_build/${MIX_TARGET}_dev/nerves/images/my_project.fw" --key my-key
```

It will prompt you for the password then your .fw file should be fully signed.

## Upload firmware

Again we use the CLI:

```sh
nh firmware publish "./_build/${MIX_TARGET}_dev/nerves/images/my_project.fw"
```

It should ask for confirmation and then show a progress bar for the upload.
You can also perform an upload by going to your Product in the web UI and
visiting the Firmwares section.

## Create a deployment

Your firmware has a UUID that is occasionally useful. We can get it via `fwup`
which is the tool that does all the interesting stuff with firmware:

```sh
fwup -i "./_build/${MIX_TARGET}_dev/nerves/images/my_project.fw" -m --metadata-key meta-uuid
```

Then we can use this UUID for setting up a deployment, or again, we can do this
from the web UI.

```sh
nh deployment create --name "My deployment" --firmware "UUID_GOES_HERE" --version "" --tag "main"
```

The deployment is not active by default. To turn it on run:

```sh
nh deployment update "My deployment" state on
```

## Confirm that the device updates

The device should be automatically added to the Deployment we just created
since it wasn't already associated to one. And it should then be selected for
receiving the new update.

If the update doesn't happen or you don't want to wait, you can hit the
Reconnect button to force the device to reconnect to NervesHub or you can
manually add the Deployment or even manually send the firmware. All from the
web UI in the Device detail view.

## Sending more updates

Future versions do not need as many commands:

```sh
mix firmware
export FW_PATH="./_build/${MIX_TARGET}_dev/nerves/images/my_project.fw"
nh firmware sign $FW_PATH --key my-key
nh firmware publish $FW_PATH --deploy "My deployment"
```

And of course you can build out your own script around this if you like.

Congratulations! Your Nerves device can now enjoy the delights of NervesHub.

Check in on your device and see if it is reporting Health data, maybe a
geo-location and try the console.

