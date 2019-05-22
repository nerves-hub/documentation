# General NervesKey storage

NervesKeys provide a small amount of non-key storage. This includes

* Manufacturer serial number assignment
* Board name and revision
* Device settings

The manufacturer serial number is used to identify devices associated with the NervesKey to NervesHub. This is the serial number that might be printed on a label on the outside of the device. Manufacturer serial numbers are written to one-time-programmable memory on the NervesKey and can never be changed.

{% hint style="warning" %}
NervesHub requires all devices within an organization to have unique serial numbers. If you do not want to maintain a list of serial numbers, we recommend that you use the default provided by the NervesKey provisioning software. It is guaranteed to be globally unique.
{% endhint %}

Storage for a board name and hardware revision is also available. Like the manufacturer serial number, it is written to one-time-programmable memory. Setting these fields is only available programmatically via the `nerves_key` library. The board name is set to "NervesKey" by default. Most users don't need to set this field and its not displayed or used internally by any NervesHub components.

Finally, each NervesKey provides 524 bytes of user storage for settings. This can be handy for storing important information settings that you don't want to lose if someone replaces the MicroSD card on your device. This could hold WiFi settings, for example. Another use is to store calibration data that's determined at manufacturing time. See [`NervesKey.put_settings/2`](https://hexdocs.pm/nerves_key/NervesKey.html#put_settings/2) and [`NervesKey.get_settings/1`](https://hexdocs.pm/nerves_key/NervesKey.html#get_settings/1) for more information.

See the NervesKey [ATECC508a Configuration](https://github.com/nerves-hub/nerves_key#atecc508a-configuration) for the low level storage of all of the above.

