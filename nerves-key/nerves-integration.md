# Nerves integration

Nerves can use the serial number programmed into the NervesKey for the hostname. This can come in handy for finding devices on the network since the hostname can be reported via DHCP or broadcast using mDNS.

{% hint style="info" %}
The DHCP client for Nerves reports hostnames to the DHCP server. Some DHCP servers like dnsmasq-based ones register those names with a colocated DNS server so that requests of that name return the IP address of the Nerves-based device. Many DHCP servers do not do this, so mDNS is the only option for easily finding the device.
{% endhint %}

In Nerves, the `boardid` program reads serial numbers off of boards. It has options to support many different kinds of hardware. To have it read the serial number off the NervesKey, add a `rootfs_overlay/etc/boardid.conf` file to your Nerves project. This will override the `boardid.conf` distributed with your Nerves system \(assuming that you're using an official Nerves system\). The contents should be:

```text
# /etc/boardid.conf

-b nerves_key -f /dev/i2c-1
```

If your NervesKey is attached via a different I2C bus, update the `-f` option accordingly.

If this doesn't work, you can test `boardid` at the IEx prompt using the Toolshed `cmd` helper \(or `System.cmd/3` if not using Toolshed\) by running:

```elixir
iex> cmd("boardid -b nerves_key -f /dev/i2c-1 -b force -f failed")
abc123
```

The `-b force -f failed` part ensures that `boardid` doesn't fall back to try to find a serial number based on options in a `/etc/boardid.conf` file. Run `boardid --help` for more information.

