# Quickstart

This is the fastest and simplest way to get started trying NervesHub.

## Start the Project

Start a Nerves project (or use your existing one):

```
mix nerves.new sample
```

Add `nerves_hub_link` to the `deps` function:

```
{:nerves_hub_link, "~> 2.5"}
```

Then run `mix deps.get`.

## Set up Product

Inside of your NervesHub account under the Org of your choice. Create a Product.

Then go to settings. Hit the button to "Add a Shared Secret".

Copy the Key and copy the Secret. If your browser does not seem to grab the secret
when you hit "Copy Secret" you can inspect the button and steal the secret anyway.

## Configuration

In `config/target.exs`:

```
config :nerves_hub_link,
  host: "devices.nervescloud.com", # or whatever your instance is
  shared_secret: [
    product_key: "<YOUR KEY>",
    product_secret: "<YOUR_SECRET>"
  ]
```

If you need WiFi find the part about `:vintage_net` and `"wlan0"`. Replace the blank wlan interface configuration with:

```
{  "wlan0",
      %{
        type: VintageNetWiFi,
        vintage_net_wifi: %{
          networks: [
            %{
              key_mgmt: :wpa_psk,
              ssid: "<MY_NETWORK_NAME>",
              psk: "<MY_WIFI_PASSWORD>",
            }
          ]
        },
        ipv4: %{method: :dhcp},
      }
    }
```

To build for a Raspberry Pi Zero:

```
export MIX_TARGET=rpi0
mix deps.get # fetches the system as well
mix firmware
```

Then deploy to SD card via `mix burn` or to an existing working Nerves device via `mix upload`.

Power it up and it should join your device list.

Click into it, try the Console and have some fun. For installations with more serious security and device identification needs. See the full Setup guide and how to use NervesKey.
