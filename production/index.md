# Production setup with NervesKey

This tutorial will get a thorough setup with the easy to use but reasonably
secure NervesKey hardware (Microchip ATECC608-series) to provide mTLS using
device certificates for authentication against your NervesHub instance. This
is not the simplest or fastest way to try NervesHub, for that, try the
[quickstart](/quickstart/index.md). It is not a massive undertaking either, so
don't worry.

This guide is largely also applicable if using the LocalCert authentication
method which is simply less secure but relevant if your hardware lacks a secure
peripheral. It can also be applied for other HSM (Hardware Security Module)
type devices but the tooling and support will vary. The NervesKey gets to be
the example, but anything that offers an OpenSSL PKCS11 engine implementation
should be possible to use.

A fair number of devices ship with an ATECC608 on them already but if you need
a breakout for your prototyping you can
[get one from Adafruit](https://www.adafruit.com/product/4314) and possibly a
few other vendors.

We will:

- Choose an authentication method
- Create a Nerves project
- Set up a NervesHub Product for our chosen method
- Deploy a device
- Create and sign firmware
- Deploy a firmware update
- Relax!

## Selecting the authentication method

There are a variety of ways to use NervesHub's device authentication process.
This guide focuses on the device certificate-based approaches. The
[quickstart](/quickstart/index.md) covers the Shared Secret method.

This is the most explanation that will need to happen during this tutorial as
you need to make a choice about your needs.

### Device Certificate method

This is the recommended approach. It is secure, explicit and controlled.

This relies on knowing the individual device's public key/certificate and
uploading those to NervesHub as part of manufacture or provisioning of the
devices. This works well with the Microchip TrustNGo parts as well that come
pre-provisioned. You create the device on NervesHub before it comes online
and you add the device certificate information to the device in NervesHub. As
part of the mTLS exchange we look up that the device exists and has a matching
certificate in NervesHub. You essentially create an exact allow-list.

We still recommend uploading the Signer Certificate (aka. public key) as a
Certificate Authority in NervesHub since that allows correlating which devices
run certificates signed by which CA. Because the Device Certificate is uploaded
before the device ever connects the CA is not strictly needed and this method
would also work in a situation where you for some reason do not control the
signing key.

### JITP method (Just-in-Time Provisioning)

This is not the recommended approach but it has special use-cases.

JITP will not provision your NervesKey. It refers to provisioning the device
onto NervesHub. A JITP setup requires the Signer Certificate to be uploaded as
a Certificate Authority in NervesHub. It also requires enabling that key to be
used specifically for JITP on a particular product.

When devices that hold a device certificate matching that CA connect we will
trust the information they provide to provision them onto the platform. This
approach is not or primary recommendation as the Device Certificate method is
more explicit and gives you as a manufacturer of the device more control. The
JITP approach has worse consequences if the Signer private key gets out.

JITP can work in unusual situations where the Device Certificate method will
not work at all, so it is an option. This mostly applies where complex key
infrastructure is in place.

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

```
export NERVES_HUB_ORG="my-org"
export NERVES_HUB_PRODUCT="my_project"
```

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

## Add new dependencies

In your Nerves project find `mix.exs` and in the function called `deps` add:

```elixir
{:nerves_hub_link, "~> 2.7"},
{:nerves_key, "~> 1.2"}
```

Now run:

```sh
mix deps.get
```

## Configuration

Your `config/target.exs` is for configuration that applies to your target
device. Since we added the `nerves_key` library NervesHubLink will attempt
to use a NervesKey with the `:primary` key slot and default I2C bus.

```elixir
config :nerves_hub_link,
  # Replace this with your instance device endpoint if hosting your own
  host: "devices.nervescloud.com",
  # Enable the very nice remote console
  remote_iex: true,
  # If you want to adjust config or be explicit you can uncomment these
  # configurator: NervesHubLink.Configurator.NervesKey
  # certificate_pair: :primary,
  # certificate_pair: :aux,
  # i2c_bus: 0
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

```sh
ssh nerves.local
```

You need to ensure you have a way of SSH:ing into the device for the next step.

## Provision NervesKey

Ensure the device you have has a NervesKey/ATECC608 peripheral attached on I2C.
There are a lot of things you might want to know about the NervesKey and [the
docs](https://hexdocs.pm/nerves_key/readme.html) provide a lot of detail. Here
we focus on using it. First we have to provision it, this means adding the
necessary information, locking in the config of the device and generating a
private key inside the device that will never see the light of day.

### Managing the Signer Certificate and private key

We will be generating important cryptographic secrets during this tutorial. The
Signer Certificate private key is what lets you create hardware devices that
you can verify cryptographically as yours. **Put it in a secure place intended
for secret management.** Limit how many people have access to it. The devices
you test this with will be **permanently linked to that key**. No take-backs.

Exactly how you manage this key during manufacturing and production of devices
is a real challenge that is hard to give a single answer for. Put some thought
into it, handle it with care.

You generate your Signer certificate and private key using a mix task that
makes sure it matches the ATECC Compressed Certificate Definition:

```sh
mix nerves_key.signer create my_board_prod_signer_1
```

This produces two files:

- `my_board_prod_signer_1.cert` - You can be sloppy with this one.
- `my_board_prod_signer_1.key` - This is the secret one to be careful with.

### Your serial number

The NervesKey will store a serial number of your own design as a manufacturer
serial number. You are a manufacturer now, enjoy it. It is up to you to ensure
uniqueness and have a satisfying and useful scheme for your product.

### The board name

You also get to name the board, aka. the product. This is nice and
informational. It is not used by NervesHub.

### Performing the provisioning

We upload the cert and key to the device using sftp. It may work over `scp` but
the Erlang SSH subsystem and `nerves_ssh` can be a bit particular so be mindful
of that if you experiment. It really doesn't like Cyberduck for some reason.

```sh
$ sftp nerves.local
Connected to nerves.local.
sftp> cd /tmp
sftp> put my_board_prod_signer_1.*
Uploading my_board_prod_signer_1.cert to /tmp/my_board_prod_signer_1.cert
my_board_prod_signer_1.cert                                              100%  636    78.3KB/s   00:00
Uploading my_board_prod_signer_1.key to /tmp/my_board_prod_signer_1.key
my_board_prod_signer_1.key                                               100%  228    78.3KB/s   00:00
sftp> exit
```

Now we get to the fun part. Burning permanent unchangeable information into the
hardware. If you are building a production device. Have multiple ATECC chips to
work with during experimentation. **You can screw up the chip if you make a
mistake here.**

Next we `ssh nerves.local` to get the IEx prompt:

```elixir
cert_name="my_board_prod_signer_1"
manufacturer_sn = "MB000001"
board_name = "my_board"

signer_cert = File.read!("/tmp/#{cert_name}.cert") |> X509.Certificate.from_pem!;true
signer_key = File.read!("/tmp/#{cert_name}.key") |> X509.PrivateKey.from_pem!();true

{:ok, i2c} = ATECC508A.Transport.I2C.init([])
provision_info = %NervesKey.ProvisioningInfo{manufacturer_sn: manufacturer_sn, board_name: board_name}

# Double-check what you typed above before running this
NervesKey.provision(i2c, provision_info, signer_cert, signer_key)
```

To verify that you NervesKey is provisioned you can run the following and get
your public key/device certificate:

```elixir
{:ok, i2c} = ATECC508A.Transport.I2C.init([])
true = NervesKey.provisioned?(i2c)
cert = NervesKey.device_cert(i2c)
X509.Certificate.to_pem(cert) |> IO.puts()
```

Grab that and put it in `MB000001.cert` on your local machine. This is a
public key and so not particularly sensitive.

## Set up your NervesHub product

Now log in to the web UI of your NervesHub instances. For NervesCloud this is
[manage.nervescloud.com](https://manage.nervescloud.com).

You should already have an organization in your name. Or you can create a
separate one. Selecting the org should take you to the Products view. Hit the
button for creating a new Product. We can use the defaults but we want to add a
name. There is a convenience in using the same name for this as your Nerves
project so let's use `"my_project"`.

## Add Signer CA cert

On the Organisation view you will find a section called Certificates. You can
add your CA cert there. This is mandatory for the JITP method and strongly
recommended for the Device Certificate method. Easiest is to upload using the
CLI:

```sh
nh cacert register my_board_prod_signer_1.cert
```

**JITP method:** If using JITP there is a checkbox for enabling Just In Time
Provisioning. The important part is to then select your Product from the
dropdown menu.

## Register device on NervesHub

If using JITP you can skip this step as that is what JITP will do for you.

Otherwise you want to create the device on NervesHub and attach the certificate
so it can be allow-listed for connecting later on.

```sh
nh device create
```

It will prompt you for additional details. I will assume you enter `MB000001`
for the serial number. There are flags for automatically providing the serial
and so on if you want to script it later.

With that created, we can then import the certificate from before:

```sh
nh device cert import MB000001 MB000001.cert
```

This will upload your certificate and associate it with your device on
NervesHub. For a manufactured batch you'd typically do this based on a CSV
file of provisioned devices or something to that effect.

## Device shows up in NervesHub

Given a bit of time and if it has Ethernet or Wi-Fi it should reach NervesHub
and show up in the Devices list of your product.

If it fails to show up, SSH to the device and run `RingLogger.next` to see why
it fails. You can also use `NervesHubLink.reconnect()` to trigger a reconnect.

Assuming your device shows up at this point we move on. If you can't get it
working, consult [the Nerves section](https://elixirforum.com/c/nerves-forum)
of the Elixir Forum and feel free to ask for help.

If you used the JITP method the device won't have existed before and should
have been automatically created. From then on it will have a device certificate
and be allow-listed just like under the Device Certificate method.

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

This is **the key that allows people to put new firmware on your device** and
it should be treated with a lot of care. Put it in the same type of secret
management you use for the Signer CA key we created earlier

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

Congratulations! Your Nerves device can now enjoy the splendor of NervesHub.

Check in on your device and see if it is reporting Health data, maybe a
geo-location and try the console.

